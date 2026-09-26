# Ontology + CVAT setup

## Ontology table

| Name | Geometry | Type (class / attribute) | Allowed values | Default | Mutable? | Rationale |
|---|---|---|---|---|---|---|
| `drivable_area` | polygon | class | — | — | — | Một class geometry giữ thao tác CVAT gọn; semantics direct/alternative nằm ở attribute |
| `area_type` | — | attribute của `drivable_area` | `undefined`, `direct`, `alternative` | `undefined` | false | Downstream cần phân biệt vùng ego có ưu tiên và vùng có thể đi vào; undefined giúp phát hiện polygon chưa hoàn tất |
| `needs_review` | — | attribute của `drivable_area` | `false`, `true` | `false` | false | Escalation ở cấp polygon và nhìn thấy trong export |

## Class hay attribute

`drivable_area` là class vì mọi vùng dùng cùng polygon/geometry rule. `area_type` là attribute vì direct và
alternative là quan hệ của cùng loại mặt đường với ego; cách này khớp schema trong bản CVAT final. `needs_review` là
attribute vì nó chỉ biểu diễn trạng thái escalation. Default `undefined` không được coi là nhãn hợp lệ: trước export
phải đổi thành `direct` hoặc `alternative`.

## CVAT

- **Phiên bản CVAT:** 2.75.1
- **Task/export:** Cường — task 33 `day09-lab-growntruth`; Hoàng — task 19 `challenge bdd100k`; Đại — job 27, export final `dai_v3final.zip`
- **Guide:** Guide chuẩn để handoff là `02_guideline.md` v2 hiện tại. Hậu tố v1/v2/v3 trong tên export chỉ ba lượt
  annotation/schema khác nhau, không chứng minh ba phiên bản Guide đã được dán trong CVAT.
- **Nhóm dùng Track hay Shape:** Shape; dữ liệu là ảnh tĩnh độc lập.

## Setup test

Không có setup test độc lập được ghi nhận tại thời điểm tạo task. Phần dưới là **tái dựng từ artifact**, không phải
biên bản quan sát một thành viên mới:

- Label: `drivable_area`.
- Tool: Shape → Polygon.
- Attribute bắt buộc: `area_type=direct|alternative`; `needs_review=true` khi cần escalation.
- Điểm vấp thể hiện trong export: v1 và v2 để toàn bộ `area_type` ở `__undefined__/undefined`; v3 đã sửa thành
  direct/alternative. Vì vậy guideline v2 bổ sung checklist cấm export khi còn undefined.
