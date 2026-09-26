# Peer feedback + owner response

Phần 1 do **nhóm peer** trả lời (gửi kèm file export). Phần 2 do **nhóm owner** điền. Export và 5 câu feedback của
Nhóm 99 đã được ghi nhận; export được chấm trong `transfer_score.csv`.

- **Nhóm peer:** Nhóm 99 (theo thông tin owner cung cấp cùng `nhom99.zip`)
- **Người label blind:** Chưa được peer xác nhận

## 1. Peer trả lời

1. **Quy tắc rõ nhất và giúp quyết định nhanh nhất:** Các thuộc tính đang khá nhất quán. Cả 10 polygon đều có `area_type` và `needs_review`; đặc biệt BDD03 và BDD21 khớp hoàn toàn với gold nên có thể dùng làm mẫu tham chiếu.
2. **Quy tắc còn mơ hồ hoặc phải tự suy đoán:** Khó nhất là cách tách vùng và chọn `direct/alternative` ở BDD10. Ranh wet-road/snowbank ở BDD24 và ranh đường trong vùng tối ở BDD26 cũng chưa đủ rõ để người mới quyết định mà không phải đoán.
3. **Ảnh khó áp dụng guideline nhất:** BDD10, vì export chỉ có 1 polygon `direct` trong khi gold yêu cầu 4 polygon; điều này cho thấy quy tắc tách vùng hoặc cách xác định loại vùng chưa đủ rõ.
4. **CVAT attribute/default dễ gây thao tác sai:** Trong export hiện tại không có thuộc tính nào bị bỏ trống. Chỉ dựa trên file này chưa thể kết luận các giá trị mặc định của CVAT có khiến annotator chọn nhầm hoặc bỏ sót hay không.
5. **Thay đổi cụ thể được đề xuất:** Thêm ảnh overlay ví dụ cho BDD10, BDD24 và BDD26, cùng checklist trước export để kiểm tra số vùng, `area_type`, `needs_review` và các ranh giới cần loại trừ.

## 2. Owner phân loại

Owner không tranh luận để bảo vệ guideline. Mỗi feedback và mỗi decision peer làm sai được xếp vào một hướng xử lý.

| Feedback / decision sai | Nguyên nhân (guideline gap / data ambiguity / execution error) | Xử lý | Bằng chứng |
|---|---|---|---|
| Feedback 1: các thuộc tính đều được điền; BDD03/BDD21 là mẫu tham chiếu tốt | Không thấy guideline gap ở attribute completeness; đây là phản hồi tích cực | Giữ checklist bắt buộc `area_type`/`needs_review`; dùng BDD03/BDD21 làm tham chiếu | `peer_feedback.md` câu 1; các dòng tương ứng trong `transfer_score.csv` |
| Feedback 2: split/direct-alternative ở BDD10 còn mơ hồ | Guideline gap; cách tách vùng và ví dụ semantic chưa đủ cụ thể | Accept + revise: đã thêm ví dụ BDD10 và overlay tham chiếu; kiểm tra số polygon, nhãn từng vùng | `peer_feedback.md` câu 2; BDD10 trong `gold_decisions.csv`; `02_guideline.md` §2–5; `04_edge_cases/overlays/bdd10_reference_overlay.png` |
| Feedback 2: ranh wet-road/snowbank BDD24 và vùng tối BDD26 khó chốt | Data ambiguity + guideline gap về bằng chứng biên và escalation trong low visibility | Accept + revise: thêm overlay riêng và nhắc chỉ vẽ phần có bằng chứng; `needs_review` không hợp thức hóa vùng ngoài scope | `peer_feedback.md` câu 2; `02_guideline.md` §5–7; overlays BDD24/BDD26 |
| Feedback 3: BDD10 chỉ có 1 direct so với gold 4 polygon | Guideline gap; peer nêu rõ cách đếm/split và semantic chưa đủ dễ áp dụng | Accept + revise: đã ghi target 1 direct + 3 alternative và checklist đếm vùng trước export | `peer_feedback.md` câu 3; BDD10/d1–d2 trong `transfer_score.csv` và `gold_decisions.csv` |
| Feedback 4: export không có attribute trống; chưa xác định CVAT default có gây nhầm không | Không có defect CVAT được chứng minh từ XML; câu trả lời giữ nguyên giới hạn bằng chứng | Không đổi default chỉ dựa trên phỏng đoán; giữ cảnh báo `undefined` không hợp lệ và xác nhận thao tác CVAT ở lần review kế tiếp | `peer_feedback.md` câu 4; `03_cvat_labels.json`; `02_guideline.md` §4 |
| Feedback 5: đề xuất overlay BDD10/24/26 và checklist trước export | Đề xuất cải tiến usability, phù hợp với các lỗi và điểm mơ hồ quan sát được | Accept + revise: đã thêm 3 overlay vào guideline và checklist đếm vùng, rà attributes, exclusion boundary | `peer_feedback.md` câu 5; `02_guideline.md` §2, §9 và mục Annotated reference overlays |
| BDD10/d1: peer vẽ 1 polygon, gold yêu cầu 4 | guideline gap có khả năng góp phần; quy tắc tách đã có nhưng chưa có ví dụ minh họa đúng chính sample này | accept + revise: thêm ví dụ đếm vùng và checklist polygon riêng theo vùng liên thông/area_type | `transfer_score.csv`; `04_edge_cases/gold_decisions.csv`; quy tắc tách ở `02_guideline.md` §3–4 |
| BDD10/d2: chỉ có direct, thiếu 3 alternative | guideline gap có khả năng góp phần; ví dụ city street chưa làm rõ các vùng/làn nhìn thấy nào được tính là alternative | accept + revise: thêm hình chú giải direct x1 + alternative x3 cho BDD10, nhắc kiểm tra đủ lane/nhánh hợp lệ | `transfer_score.csv`; gold BDD10/d2; `02_guideline.md` §4–5 |
| BDD10/d3: biên polygon không khớp gold, lấn xuống phần hood ở đáy ảnh | execution error có khả năng cao; quy tắc loại vật cản/ngoài mặt đường đã có, nhưng nguyên nhân cần peer xác nhận | revise: bổ sung hình overlay biên đúng/sai; xác nhận nguyên nhân với peer trước khi kết luận | hình BDD10 và geometry trong `peer_output/nhom99.zip`; gold BDD10/d3; `02_guideline.md` §3–4 |
| BDD24/d3: polygon alternative chồng lên snowbank | Guideline gap/data ambiguity; peer xác nhận ranh wet-road/snowbank khó thấy trong hướng dẫn | Accept + revise: thêm overlay BDD24; snowbank luôn IGNORE dù curb bị tuyết che | hình BDD24 và geometry trong `peer_output/nhom99.zip`; gold BDD24/d3; feedback câu 2; `02_guideline.md` §5–7 |
| BDD24/d4: ranh polygon đi vào snowbank thay vì theo mép wet-road/snowbank | Data ambiguity + guideline gap về ranh khi curb bị tuyết che | Add escalation rule: chỉ vẽ phần mặt đường còn bằng chứng; nếu phần bị che làm thay đổi đáng kể vùng gần xe thì `needs_review=true` | hình BDD24 và geometry trong `peer_output/nhom99.zip`; gold BDD24/d4; feedback câu 2; `02_guideline.md` §6 |
| BDD26/d3: polygon kéo lên vùng tối ngoài mặt đường có bằng chứng | Data ambiguity/low visibility; peer xác nhận cách nhận biết ranh road trong vùng tối chưa rõ | Accept + revise: thêm overlay BDD26 và quy tắc dừng ở ranh roadway cuối cùng có bằng chứng | hình BDD26 và geometry trong `peer_output/nhom99.zip`; gold BDD26/d3; feedback câu 2; `02_guideline.md` §6 |

**Disposition chất lượng:** GTS 68,0 với 3 critical escapes; theo `05_qa_plan.md`, trạng thái là REJECT/ESCALATE. Guideline đã được sửa lên v3, nhưng chưa có blind revalidation sau sửa nên chưa tuyên bố đạt quality gate.

**Clarification count:** `clarification_log.csv` hiện có 0 câu hỏi được ghi; GTS đang tính I=100 theo log. Năm feedback của Nhóm 99 không xác nhận riêng số câu hỏi, nên trước khi báo cáo độc lập như một sự kiện cần xác minh rằng log đầy đủ/peer không hỏi câu nào.

Các nguyên nhân trên là phân loại owner dựa trên export, gold đã freeze và feedback của Nhóm 99; nguyên nhân thao tác ở từng polygon vẫn có thể được xác nhận thêm trong debrief.
