# QA plan + quality gates

## Flow

Guideline → Calibration → Production → Self-QC → Review → Rework → Quality Gate.

- **Ai review, review bao nhiêu:** mỗi annotator self-QC 100% ảnh vừa làm. QA owner Đặng Đức Cường review 100% ảnh
  có `needs_review`, tag `critical` hoặc `low_visibility`; review ngẫu nhiên tối thiểu
  30% số ảnh còn lại của từng annotator. Không ai là reviewer cuối cho chính ảnh mình label.
- **Chọn sample:** ưu tiên toàn bộ risk-tagged trước; phần 30% còn lại chọn bằng random seed được ghi trong issue
  log, stratify theo annotator và scene highway/city/residential. Mọi annotator phải có ít nhất 2 ảnh được review.
- **Issue được ghi ở đâu, đóng thế nào:** calibration ghi tại `06_calibration_report.csv`; production ghi tại
  `qa_issue_log.csv`; blind ghi tại `transfer_score.csv` và `peer_feedback.md`. Issue log có sample_id,
  polygon/class, severity, rule, người sửa, reviewer và trạng thái. Chỉ reviewer đóng sau khi kiểm export/CVAT đã sửa.
- **Guideline gap:** dừng các ảnh chịu ảnh hưởng, spec owner viết rule + ví dụ, tăng version, ghi
  `08_revision_log.md`, dán lại Guide và re-review 100% ảnh bị ảnh hưởng. Không sửa gold/sample pack sau freeze.

## Defect severity

| Severity | Định nghĩa cho project này | Ví dụ | Action mặc định |
|---|---|---|---|
| Critical | False positive/false negative có thể đổi hành lang ego hoặc vi phạm vùng ngăn vật lý | Gán sidewalk snowbank median shoulder sau vạch liền thành `area_type=direct`; bỏ phần lớn direct gần xe | Dừng bàn giao; sửa 100% ảnh cùng pattern; root-cause và spec owner duyệt lại |
| Major | Sai class hoặc thiếu vùng đáng kể nhưng không tạo hành lang xuyên vật cản | Side street đáng ra alternative bị gán direct; quên polygon alternative rõ; thiếu `needs_review` cho ambiguity lớn | Rework ảnh; review thêm 100% ảnh cùng scene/rule của annotator |
| Minor | Sai geometry cục bộ ngoài tolerance hoặc metadata không ảnh hưởng class | Biên curb lệch 6–10 px ở vùng rõ; thừa điểm; `needs_review` sai ở đoạn nhỏ | Sửa trước gate; theo dõi xu hướng theo annotator |
| Question | Chưa đủ bằng chứng để kết luận defect hoặc guideline chưa cover | Không rõ bề mặt tối là road hay parking access | Không tự sửa; chọn `area_type` best-effort, đặt `needs_review=true` và chuyển spec owner |

## Metrics

| Metric | Cách tính | Vì sao phù hợp với bài toán |
|---|---|---|
| Class decision accuracy | Số decision LABEL/IGNORE/class đúng ÷ tổng decision được review | Bắt nhầm direct/alternative và false positive ngoài scope |
| Critical defect escape rate | Số critical defect lọt qua self-QC nhưng reviewer phát hiện ÷ tổng ảnh được review | Đo đúng rủi ro downstream lớn nhất |
| Geometry compliance | Số polygon đạt tolerance ÷ tổng polygon được kiểm geometry | Bảo đảm mask bám curb/edge line thay vì chỉ đúng class |
| Attribute completeness | Polygon có `area_type=direct|alternative` và giá trị `needs_review` hợp lệ ÷ tổng polygon | Bắt thao tác CVAT thiếu và semantic decision vô hình |
| Escalation precision | Escalation có bằng chứng theo mục 7 ÷ tổng escalation | Ngăn lạm dụng escalation để né quyết định |
| Calibration agreement | Số item class/count/attribute/tag đồng thuận ÷ tổng item do `make calib` sinh | Chỉ ra rule chưa transferable trước production |

Metric high-risk tách riêng: **critical defect escape rate**, báo cả tử số/mẫu số; không gộp critical vào accuracy
trung bình vì một lỗi sidewalk-as-direct quan trọng hơn nhiều lỗi điểm polygon nhỏ.

## Quality gate

```text
PASS if:
  critical defect escape rate = 0%;
  class decision accuracy >= 95%;
  geometry compliance >= 90%;
  attribute completeness = 100%;
  và mọi needs_review=true đã có disposition của spec owner.
REWORK if:
  không có critical escape nhưng class accuracy 85–<95%, geometry 80–<90%,
  hoặc còn Major/Minor chưa đóng.
REJECT / ESCALATE if:
  có >= 1 critical escape; class accuracy < 85%; geometry < 80%;
  hoặc guideline gap ảnh hưởng từ 2 ảnh trở lên mà chưa version/update Guide.
```

**Trade-off:** review 100% risk-tagged làm tăng chi phí nhưng bộ chỉ có 26 ảnh và hậu quả false-positive non-road
cao. Ngưỡng geometry thấp hơn class accuracy vì biên mưa/tuyết/đêm có uncertainty hợp lệ; attribute completeness phải
100% vì `undefined` hoặc `__undefined__` là lỗi thao tác có thể kiểm tự động.

## Current blind-transfer disposition (2026-09-26)

Nhóm 99 đạt GTS 68.0 nhưng có 3 critical escapes; theo ngưỡng phía trên, disposition là **REJECT / ESCALATE**, không
phải quality pass. Owner đã cập nhật guideline lên v3 và thêm overlay tham chiếu; cần một blind revalidation độc lập
trước khi kết luận rule mới đã khắc phục các lỗi. `make check` chỉ xác nhận độ đầy đủ hồ sơ, không thay thế quality gate.

Nguyễn Ngọc Nguyên/Nhóm 99 xác nhận có 0 câu hỏi domain rule trong blind window; vì vậy clarification log chỉ có
header và I=100 phản ánh đúng số câu hỏi được peer báo cáo.
