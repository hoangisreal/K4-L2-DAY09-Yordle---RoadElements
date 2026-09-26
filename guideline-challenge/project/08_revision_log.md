# Revision log

Guideline v1 = bản nháp đầu; v2 = sau calibration nội bộ; v3 = sau blind handoff. Mỗi lần tăng `Version` trong
`02_guideline.md`, thêm một hoặc nhiều dòng vào bảng: đổi gì và vì sao, kèm bằng chứng (sample_id, dòng
calibration report, câu hỏi trong clarification log, feedback của peer).

Cột Version ghi dạng `v1`, `v2`, `v3` — `make status` tìm dòng bảng có `v2` và dòng có `v3`.

| Version | Đổi gì | Vì sao | Bằng chứng |
|---|---|---|---|
| v1 | Chốt scope ego-centric drivable area, direct/alternative, geometry, UNKNOWN và ESCALATE | Bản nháp đầu trước CVAT/calibration | Review 26 ảnh BDD01–BDD26 và downstream contract trong `01_problem_statement.md` |
| v2 | Đồng bộ schema thật thành `drivable_area` + `area_type`; cấm export còn undefined; loại footprint vật cản; thêm rule tách polygon và escalation cảnh đêm | Hai export đầu bỏ trống area_type; BDD17–BDD18 có bất đồng lớn về số vùng và semantics; gold yêu cầu loại xe đỗ | `06_calibration_measure.csv`; các dòng BDD04 BDD17 BDD18 trong `06_calibration_report.csv`; BDD10 BDD24 BDD26 trong `04_edge_cases/gold_decisions.csv`; export final `dai_v3final.zip` |
