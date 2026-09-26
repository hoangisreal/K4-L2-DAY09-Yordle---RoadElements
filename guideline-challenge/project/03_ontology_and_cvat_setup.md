# Ontology + CVAT setup

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `drivable_direct` | polygon | class | — | — | — | Vùng ego đang đi và có quyền ưu tiên; class riêng vì là output downstream chính và lỗi false positive có rủi ro cao |
| `boundary_confidence` | — | attribute của `drivable_direct` | `__undefined__`, `clear`, `unknown` | `__undefined__` | false | Buộc annotator xác nhận độ chắc chắn; unknown vẫn giữ được polygon best-effort |
| `needs_review` | — | attribute của `drivable_direct` | `false`, `true` | `false` | false | Escalation ở cấp polygon, nhìn thấy trong export |
| `drivable_alternative` | polygon | class | — | — | — | Lane/vùng ego có thể đi vào hợp lệ qua lane change/turn nhưng hiện không đi trên đó; class riêng để model học topology trực tiếp |
| `boundary_confidence` | — | attribute của `drivable_alternative` | `__undefined__`, `clear`, `unknown` | `__undefined__` | false | Cùng semantics với direct, tránh default `clear` tạo sự chắc chắn giả |
| `needs_review` | — | attribute của `drivable_alternative` | `false`, `true` | `false` | false | Escalation ở cấp polygon |
| `image_escalate` | tag | class | — | — | — | Ghi ambiguity ở cấp toàn ảnh khi không xác định được carriageway ego hoặc cả hai biên gần |

## Class hay attribute

`drivable_direct` và `drivable_alternative` là class vì downstream cần hai mask/topology riêng và lỗi nhầm class có
action QA khác nhau. `boundary_confidence` và `needs_review` là attribute vì chúng mô tả trạng thái của cùng một vùng,
không thay đổi bản chất semantic. `image_escalate` là tag vì vấn đề có thể áp dụng cho toàn cảnh, không có geometry
riêng.

Không đặt default `clear`: annotator quên thao tác sẽ tạo certainty giả. `__undefined__` cố ý là default để reviewer
bắt lỗi chưa gán. Default `needs_review=false` chấp nhận được vì escalation là ngoại lệ, nhưng quality gate vẫn kiểm
toàn bộ vùng `unknown` và toàn bộ ảnh low-visibility.

## CVAT

- **Phiên bản CVAT:** 2.75.1
- **Tên task calibration:** dự kiến `Yordle-calib-v1-hoang`, `Yordle-calib-v1-dai`, `Yordle-calib-v1-cuong`; chưa tạo
- **Guide của task đã dán `02_guideline.md`?** chưa — task chưa tạo
- **Nhóm dùng Track hay Shape, vì sao:** Shape; dữ liệu là 26 ảnh tĩnh độc lập, không có sequence temporal.

## Setup test

Chưa thực hiện vì task calibration chưa được tạo. Sau khi Nguyễn Đình Đại setup, một trong hai thành viên còn lại
phải mở task mà không được hướng dẫn miệng và trả lời đúng: vẽ polygon; chọn direct/alternative; gán
`boundary_confidence`; bật `needs_review` hoặc `image_escalate` theo mục 7. Ghi tên người test, câu trả lời và điểm
vấp vào đây trước gate G2.
