# Problem statement + downstream contract

## Bài toán

Gán polygon cho **drivable area nhìn từ camera xe ego** trong ảnh tĩnh BDD100K, tập trung vào các ranh giới dễ nhầm
giữa lòng đường với lề, làn đỗ xe, vỉa hè, đảo giao thông, nhánh rẽ và vùng bị che hoặc khó nhìn.

## Downstream contract

1. **Downstream task / model / user:** huấn luyện mô hình phân đoạn mặt đường và topology mức cảnh để hỗ trợ hệ thống
   nhận thức của xe; output mô tả mặt đường có thể dùng cho di chuyển thông thường, **không phải bản đồ free-space tức
   thời** và không thay thế mô hình phát hiện vật cản.
2. **Output cần thiết:** polygon `drivable_direct` cho vùng xe ego đang đi và có quyền ưu tiên; polygon
   `drivable_alternative` cho lane/vùng ego chưa đi trên đó nhưng có thể vào bằng một chuyển làn hoặc chuyển hướng hợp
   lệ và phải thận trọng; mỗi polygon có độ tin cậy ranh giới và cờ cần review. Ảnh không thể quyết định an toàn có
   tag escalation.
3. **Failure hậu quả lớn nhất:** false positive biến vỉa hè, dải phân cách, đảo giao thông, lề cứng hoặc đống tuyết
   thành `drivable_direct`, vì mô hình downstream có thể học một hành lang chạy xe không hợp lệ.
4. **Escalation path:** annotator đặt `needs_review=true` trên polygon còn tranh chấp; nếu sự mơ hồ ảnh hưởng toàn
   cảnh hoặc không thể xác định carriageway chứa ego thì thêm tag `image_escalate`. Spec owner của Yordle chốt theo
   guideline, cập nhật version khi phát hiện guideline gap.

## Scope

- **Trong scope:** mặt đường dành cho xe cơ giới thông thường, nhìn thấy hoặc có thể nội suy ngắn qua vật cản tạm
  thời; vùng/lane xe ego đang dùng và có quyền ưu tiên; lane liền kề, nhánh rẽ, on/off-ramp, side street hoặc driveway
  công cộng mà ego có thể đi vào hợp lệ và có đủ bằng chứng hình ảnh.
- **Ngoài scope:** vỉa hè, curb/median/traffic island, grass/dirt verge, ray tàu, vùng sau barrier/guardrail, lề khẩn
  cấp ngăn bằng vạch liền, curbside parking strip, bãi đỗ tư nhân không rõ kết nối, vùng hoàn toàn khuất và phần nắp
  capo xe ego.
- **Geometry tolerance:** với biên rõ ở tiền cảnh, cạnh polygon lệch không quá 5 px; biên xa, mờ, mưa/tuyết/ban đêm
  không quá 10 px. Polygon không được cắt vào vùng ngoài scope quá 10 px liên tục.

## Output chấm được

- **LABEL:** polygon `drivable_direct` hoặc `drivable_alternative` với đủ attribute.
- **IGNORE:** không tạo polygon trên vùng ngoài scope.
- **UNKNOWN:** polygon vẫn có class hợp lý nhất, `boundary_confidence=unknown`.
- **ESCALATE:** `needs_review=true` cho polygon; thêm tag `image_escalate` nếu vấn đề ở cấp ảnh.

Blind test chấm class, sự hiện diện/vắng mặt của polygon, attribute, tag và tuân thủ geometry tolerance.

## Dữ liệu và giới hạn

Chỉ dùng 26 ảnh tĩnh `BDD01`–`BDD26` trong `data/bdd100k/` (1280×720; highway, city street và residential; gồm
ngày, đêm, mưa, tuyết và chạng vạng). Bộ nhỏ nên không đại diện đầy đủ cho đường đất, công trường, giao lộ phức tạp
hoặc khác biệt luật giao thông; không suy rộng guideline thành chuẩn toàn bộ BDD100K.
