# Annotation guideline — Ego-centric drivable area trên BDD100K

**Version:** v1

## 1. Objective + scope

Mục tiêu là tạo polygon mặt đường phục vụ mô hình phân đoạn/topology nhìn từ xe ego. Đây là **road-surface map**,
không phải free-space map: xe, người đi bộ và vật cản tạm thời không làm mặt đường bên dưới đổi class.

Label mặt đường dành cho xe cơ giới thông thường khi nó thuộc carriageway chứa ego hoặc là một nhánh/lối kết nối có
thể nhận biết từ ảnh. Không label vỉa hè, curb, dải phân cách, đảo giao thông, grass/dirt verge, ray tàu, vùng sau
barrier/guardrail, lề khẩn cấp ngăn bằng vạch liền, curbside parking strip, vùng hoàn toàn khuất và nắp capo xe ego.

## 2. Annotation unit

- Task ảnh tĩnh; dùng **Shape**, không dùng Track.
- Một annotation là một polygon cho một vùng mặt đường liên thông có cùng class.
- Hai phần cùng class bị xe/người/vật cản tạm thời che ngắn vẫn là một vùng: nội suy biên qua vật cản nếu thấy bằng
  chứng ở hai phía. Không tạo lỗ quanh xe vì downstream không phải free-space.
- Tách thành hai polygon khi vùng bị ngăn bởi curb, median, barrier, grass, khoảng ngoài khung hoặc khi class đổi.
- Lane marking, crosswalk, mũi tên, manhole và patch asphalt nằm trên mặt đường được giữ bên trong polygon.

## 3. Geometry rule

1. Dùng **polygon**, đặt điểm tại mọi chỗ biên đổi hướng; không dùng rectangle/polyline.
2. Biên gần bám mép tiếp xúc giữa mặt đường và curb/sidewalk/grass/barrier. Khi có vạch edge line, chỉ dùng vạch làm
   biên nếu phía ngoài là shoulder ngoài scope; bản thân vạch thuộc phía mặt đường được label.
3. Cạnh dưới kết thúc ở mép trên của nắp capo/dashboard; không label phần xe ego.
4. Cạnh xa kết thúc tại điểm còn bằng chứng thị giác. Có thể nội suy đoạn ngắn qua xe đỗ/xe chạy, nhưng không kéo
   polygon qua một tòa nhà, barrier hoặc vùng khuất lớn để đoán đường phía sau.
5. Ở intersection, vẽ polygon qua phần paved junction nối với carriageway; tách side street/turn branch thành
   `drivable_alternative` tại đường phân chia hợp lý nhất theo hướng tiếp tục của carriageway ego.
6. Tolerance: biên rõ ở tiền cảnh ≤ 5 px; biên xa/mờ/mưa/tuyết/đêm ≤ 10 px. Nếu không thể đáp ứng vì thiếu bằng
   chứng, dùng `boundary_confidence=unknown`, không tự bịa biên.

## 4. Taxonomy

| Label / attribute | Ý nghĩa | Giá trị |
|---|---|---|
| `drivable_direct` | Vùng xe ego đang đi trên đó và có quyền ưu tiên/tiếp tục đi | polygon |
| `drivable_alternative` | Vùng ego chưa đi trên đó nhưng có thể vào bằng chuyển làn/chuyển hướng hợp lệ và phải thận trọng | polygon |
| `boundary_confidence` | Mức chắc chắn của biên polygon | `clear`, `unknown` |
| `needs_review` | Class/biên còn tranh chấp cần spec owner xem | `false`, `true` |
| `image_escalate` | Không thể ra quyết định đáng tin ở cấp ảnh | tag |

`__undefined__` là trạng thái chưa thao tác, **không phải kết quả hợp lệ**. Trước khi lưu, annotator phải đổi
`boundary_confidence` thành `clear` hoặc `unknown` trên mọi polygon.

### Cách phân biệt hai class

- `drivable_direct`: vùng chứa điểm chiếu của xe ego ở cạnh dưới ảnh và corridor mà ego đang có quyền ưu tiên để tiếp
  tục đi. Không tự động gộp tất cả lane trên cùng carriageway vào direct.
- `drivable_alternative`: lane liền kề hoặc paved branch nối với direct mà ego có thể đi vào bằng một chuyển làn,
  turn, exit hoặc merge hợp lệ nhưng hiện không có cùng trạng thái ưu tiên. Opposing lane/carriageway mà ego không
  được phép đi vào không phải alternative và không label.
- Lề cứng, bike lane, curbside parking strip và vùng chỉ dành cho dừng khẩn cấp không phải alternative.

## 5. Inclusion / exclusion

**Bắt buộc label** khi có đủ bằng chứng:

- asphalt/concrete corridor chứa ego và nơi ego có quyền ưu tiên;
- lane liền kề có thể đi vào hợp lệ — gán alternative, không gộp mặc định vào direct;
- phần junction/crosswalk nằm trên carriageway;
- side street, turn pocket, on/off-ramp hoặc driveway công cộng nối trực tiếp và nhìn rõ là dành cho xe cơ giới.

**IGNORE, không vẽ polygon:**

- sidewalk, curb, median, traffic island, painted island/gore có vạch bao liên tục;
- grass, soil, snowbank, puddle nằm ngoài mép đường;
- shoulder ngoài edge line liên tục, parking strip dọc curb, bike lane;
- khu vực sau guardrail/barrier hoặc opposing carriageway tách vật lý;
- bãi đỗ/driveway tư nhân không đủ bằng chứng kết nối công cộng;
- reflection, shadow hoặc khoảng tối không chứng minh được là mặt đường.

Không dùng hình dáng xe đang chạy làm bằng chứng duy nhất rằng một vùng ngoài scope là drivable.

## 6. Visibility / occlusion

- **Xe/người che một phần:** nội suy biên qua vật cản khi cùng biên xuất hiện ở cả hai phía; class không đổi.
- **Cắt mép ảnh:** polygon kết thúc đúng trên mép ảnh; không co vào bên trong để tạo biên giả.
- **Nhỏ/xa:** label nếu vẫn xác định được kết nối và class; nếu chỉ là vài pixel không đủ phân biệt road/sidewalk,
  bỏ vùng đó thay vì đoán.
- **Mưa, loá, phản chiếu, bóng tối hoặc tuyết:** dùng cấu trúc curb, edge line, xe, barrier và continuity làm bằng
  chứng kết hợp. Nếu class rõ nhưng biên không rõ, dùng `boundary_confidence=unknown`.
- **Occlusion lớn:** không kéo polygon qua vùng hoàn toàn khuất nếu không thấy lại biên; đặt `needs_review=true` khi
  quyết định có thể thay đổi đáng kể diện tích hoặc class.

## 7. Ambiguity / escalation

Áp dụng theo thứ tự:

1. **LABEL:** class và biên đủ bằng chứng → vẽ polygon; `boundary_confidence=clear`, `needs_review=false`.
2. **UNKNOWN:** class có thể chốt nhưng một đoạn biên không đủ bằng chứng → vẽ best-effort,
   `boundary_confidence=unknown`; `needs_review=false` nếu sai số chỉ cục bộ.
3. **ESCALATE object:** direct/alternative còn tranh chấp, hoặc biên không chắc có thể làm thay đổi >10% vùng gần
   xe → chọn class hợp lý nhất, `boundary_confidence=unknown`, `needs_review=true`.
4. **ESCALATE image:** không xác định được carriageway chứa ego, hoặc cả hai biên gần đều mất trên hơn khoảng 1/3
   chiều sâu ảnh → thêm tag `image_escalate` và vẫn vẽ best-effort nếu có vùng đủ bằng chứng.
5. **IGNORE:** bằng chứng cho thấy vùng ngoài scope → không vẽ. Không dùng `unknown` để hợp thức hóa sidewalk,
   shoulder hay snowbank.

Mọi polygon còn `__undefined__` là lỗi chưa hoàn thành. Reviewer không được tự đổi UNKNOWN/ESCALATE thành nhãn chắc
chắn nếu không ghi bằng chứng.

## 8. Temporal rule

Không áp dụng — task ảnh tĩnh. Mỗi ảnh độc lập; không suy đoán từ ảnh BDD khác.

## 9. Examples

| sample_id | Thấy gì | Expected output | Rule áp dụng |
|---|---|---|---|
| BDD01 | Highway ban ngày, lane hiện tại và lane liền kề rõ | Lane ego là `drivable_direct`; lane liền kề có thể chuyển vào là `drivable_alternative`; giữ lane marking trong polygon tương ứng; không label vùng ngoài barrier/grass | §2, §3, §4 |
| BDD02 | City intersection có crosswalk và đường giao cắt | Direct đi qua junction; crosswalk nằm trong direct; nhánh nối rõ là `drivable_alternative`; sidewalk/traffic island bị loại | §3.5, §4, §5 |
| BDD16 | Khu vực dưới cầu có tách/nhập lối và vạch biên cong | Carriageway ego là direct; nhánh khác là alternative chỉ khi kết nối dành cho xe rõ; painted gore không label | §4, §5 |
| BDD23 | Đường residential có tuyết, xe đỗ che curb | Vẽ mặt đường qua occlusion ngắn; loại sidewalk/snowbank; đoạn biên mất dùng `boundary_confidence=unknown`, bật review nếu diện tích bị ảnh hưởng lớn | §6, §7 |

## 10. Common mistakes

- Vẽ toàn bộ carriageway thành direct thay vì tách current/right-of-way corridor với alternative lane.
- Gọi shoulder, parking strip, bike lane hay painted gore là alternative.
- Cắt lỗ quanh xe vì nhầm road-surface với free-space.
- Loại crosswalk/lane marking khỏi polygon dù chúng nằm trên mặt đường.
- Kéo polygon đến horizon qua vùng hoàn toàn khuất mà không có bằng chứng.
- Để `boundary_confidence=__undefined__` hoặc quên `needs_review` ở case đã escalate.
- Dùng `image_escalate` cho mọi ảnh khó thay vì chỉ khi ambiguity ảnh hưởng cấp cảnh.
- Vẽ đè lên nắp capo xe ego hoặc vượt quá tolerance ở curb/median rõ.

## Nguồn định nghĩa

Hai class direct/alternative bám theo semantics của BDD100K: direct là vùng ego đang đi/có quyền ưu tiên;
alternative là vùng ego chưa đi trên đó nhưng có thể đi vào bằng thao tác hợp lệ. Quy tắc geometry, uncertainty,
escalation và việc nội suy ngắn qua vật cản là contract riêng của project Yordle.

- Yu et al., *BDD100K: A Diverse Driving Dataset for Heterogeneous Multitask Learning*, CVPR 2020:
  https://openaccess.thecvf.com/content_CVPR_2020/papers/Yu_BDD100K_A_Diverse_Driving_Dataset_for_Heterogeneous_Multitask_Learning_CVPR_2020_paper.pdf
- BDD100K format documentation: https://github.com/bdd100k/bdd100k/blob/master/doc/source/format.rst
