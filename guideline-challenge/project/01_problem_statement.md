# Problem statement + downstream contract

## Bài toán

Gán polygon cho **drivable area nhìn từ camera xe ego** trong ảnh tĩnh BDD100K, tập trung vào ranh giới dễ nhầm giữa
lòng đường với lề, làn đỗ xe, vỉa hè, đảo giao thông, nhánh rẽ và vùng bị che hoặc khó nhìn.

## Downstream contract

1. **Downstream task / model / user:** huấn luyện mô hình phân đoạn vùng mặt đường đang khả dụng nhìn từ xe ego để
   hỗ trợ hệ thống nhận thức. Output giữ topology của các corridor hợp lệ nhưng loại footprint nhìn thấy của xe,
   người và vật cản; nó không thay thế mô hình phát hiện vật cản.
2. **Output cần thiết:** polygon `drivable_area`; attribute `area_type=direct` cho vùng ego đang đi/có quyền ưu tiên,
   `area_type=alternative` cho vùng ego có thể đi vào bằng một chuyển làn hoặc chuyển hướng hợp lệ nhưng phải thận
   trọng; `needs_review=true` khi bằng chứng không đủ chắc chắn.
3. **Failure hậu quả lớn nhất:** false positive biến vỉa hè, dải phân cách, đảo giao thông, lề cứng hoặc đống tuyết
   thành `area_type=direct`, khiến mô hình học hành lang chạy xe không hợp lệ.
4. **Escalation path:** annotator chọn `area_type` hợp lý nhất và đặt `needs_review=true`; spec owner của Yordle chốt
   theo guideline và cập nhật version nếu phát hiện guideline gap.

## Scope

- **Trong scope:** mặt đường dành cho xe cơ giới thông thường; lane ego đang dùng/có quyền ưu tiên; lane liền kề,
  nhánh rẽ, on/off-ramp, side street hoặc driveway công cộng mà ego có thể đi vào hợp lệ và có đủ bằng chứng.
- **Ngoài scope:** vỉa hè, curb/median/traffic island, grass/dirt verge, ray tàu, vùng sau barrier/guardrail, lề khẩn
  cấp ngăn bằng vạch liền, curbside parking strip, opposing lane không được phép đi vào, footprint nhìn thấy của
  xe/người/vật cản, vùng hoàn toàn khuất và nắp capo xe ego.
- **Geometry tolerance:** biên rõ ở tiền cảnh lệch không quá 5 px; biên xa, mờ, mưa/tuyết/ban đêm không quá 10 px.
  Polygon không được cắt vào vùng ngoài scope quá 10 px liên tục.

## Output chấm được

- **LABEL:** polygon `drivable_area` với `area_type=direct|alternative`.
- **IGNORE:** không tạo polygon trên vùng ngoài scope.
- **UNKNOWN / ESCALATE:** vẫn chọn `area_type` hợp lý nhất và đặt `needs_review=true`.

Giá trị `undefined` hoặc `__undefined__` chỉ là trạng thái chưa hoàn thành, không phải output hợp lệ. Blind test chấm
số polygon, `area_type`, `needs_review`, inclusion/exclusion và geometry.

## Dữ liệu và giới hạn

Chỉ dùng 26 ảnh tĩnh `BDD01`–`BDD26` trong `data/bdd100k/` (1280×720; highway, city street và residential; gồm
ngày, đêm, mưa, tuyết và chạng vạng). Bộ nhỏ không đại diện đầy đủ cho đường đất, công trường, giao lộ phức tạp hoặc
khác biệt luật giao thông; không suy rộng guideline thành chuẩn toàn bộ BDD100K.
