# Edge-case library — Yordle drivable area

Các card này là tài liệu nội bộ. Rule cần cho peer đã được chép vào `02_guideline.md`; không gửi file này trong blind
pack.

---

CASE ID: EC01
Sample: BDD02
Scene: City intersection ban ngày có crosswalk và đường giao cắt
Observation: Vạch crosswalk phủ lên asphalt; side street nối trực tiếp với carriageway ego; sidewalk/curb nằm sát junction
Decision: LABEL + IGNORE
Expected: Polygon `drivable_area` trên corridor ưu tiên có `area_type=direct`; side street/lane có thể đi vào hợp lệ có `area_type=alternative`; sidewalk/curb không có polygon
Rationale: Downstream cần continuity của mặt đường nhưng không được học sidewalk như hành lang xe
Common mistake: Cắt crosswalk khỏi mask hoặc tô toàn bộ intersection cùng class direct
Diversity: ambiguity / conflict

---

CASE ID: EC02
Sample: BDD04
Scene: Residential/city street hẹp có nhiều xe đỗ sát curb
Observation: Xe đỗ che phần lớn road edge nhưng curb còn xuất hiện trước và sau xe
Decision: LABEL
Expected: Các phần mặt đường nhìn thấy có `area_type=direct`; loại footprint xe đỗ và đi biên quanh xe hoặc tách thành nhiều polygon; `needs_review=false` nếu biên còn đủ bằng chứng
Rationale: Output giữ topology của phần drivable nhìn thấy nhưng không gán nhãn lên pixel đang bị xe chiếm dụng
Common mistake: Tô xuyên qua thân xe hoặc đổi phần mặt đường còn nhìn thấy sang class khác
Diversity: occlusion

---

CASE ID: EC03
Sample: BDD05
Scene: Highway có edge line và dải asphalt/lề phía ngoài
Observation: Bề mặt ngoài vạch liền có hình thức giống carriageway nhưng là shoulder
Decision: IGNORE
Expected: Lane ego có `area_type=direct`; lane liền kề hợp lệ có `area_type=alternative`; shoulder ngoài vạch liền không label
Rationale: False positive shoulder-as-direct có thể tạo hành lang downstream sai
Common mistake: Gọi mọi asphalt là direct hoặc alternative
Diversity: conflict / critical

---

CASE ID: EC04
Sample: BDD11
Scene: City intersection có crosswalk, lane marking và curb
Observation: Sơn trắng chiếm nhiều diện tích nhưng nằm trên cùng road plane
Decision: LABEL
Expected: Crosswalk/lane marking nằm trong polygon direct; curb/sidewalk bị loại; branch nối rõ được tách alternative
Rationale: Vật liệu sơn không làm thay đổi semantics của mặt đường
Common mistake: Tạo khe rỗng theo từng stripe crosswalk
Diversity: ambiguity / conflict

---

CASE ID: EC05
Sample: BDD13
Scene: City street đông xe, xe tải và xe con che road edge
Observation: Một số đoạn curb/mép đường không nhìn thấy; vùng bên phải có thể bị nhầm với parking strip
Decision: UNKNOWN
Expected: Vẽ polygon với `area_type` hợp lý nhất theo continuity; bật `needs_review=true` nếu lựa chọn làm đổi hơn 10% vùng gần
Rationale: Giữ best-effort mask nhưng không biến suy đoán thành certainty
Common mistake: Kéo polygon đến curb tưởng tượng hoặc bỏ toàn bộ phần bị che
Diversity: occlusion / ambiguity / escalation

---

CASE ID: EC06
Sample: BDD16
Scene: Khu vực dưới cầu có nhánh và vạch biên cong
Observation: Asphalt liên tục nhưng vạch/gore cho thấy lối di chuyển khác nhau
Decision: LABEL + IGNORE
Expected: Corridor ego có quyền ưu tiên là direct; lane/nhánh có thể đi vào hợp lệ là alternative; painted gore có đường bao liên tục không label
Rationale: Downstream cần phân biệt topology thay vì xem mọi bề mặt liên tục là cùng hành lang
Common mistake: Tô gore hoặc nhánh tách thành direct
Diversity: conflict / ambiguity

---

CASE ID: EC07
Sample: BDD17
Scene: City street trời mưa, mặt đường phản chiếu
Observation: Reflection và nước làm road edge kém rõ nhưng curb/xe/lane marking vẫn cho bằng chứng kết hợp
Decision: UNKNOWN
Expected: Polygon direct theo continuity; đặt `needs_review=true` nếu biên mờ vượt tolerance; không label vùng sáng chỉ vì reflection
Rationale: Cần usable mask và biểu diễn uncertainty thay vì bỏ cả ảnh mưa
Common mistake: Dùng đường biên của vùng phản chiếu làm road edge
Diversity: low_visibility / ambiguity

---

CASE ID: EC08
Sample: BDD18
Scene: City street ban đêm rất tối
Observation: Biên gần hoặc class có thể mất trên diện rộng; nguồn sáng không đủ chứng minh bề mặt
Decision: ESCALATE
Expected: Vẽ best-effort polygon nơi đủ bằng chứng, chọn `area_type` hợp lý nhất và đặt `needs_review=true`; vùng hoàn toàn không có bằng chứng thì ignore
Rationale: Không ép annotator tạo certainty giả trong cảnh rủi ro cao
Common mistake: Tô toàn bộ vùng tối hoặc escalate dù một biên và carriageway vẫn rõ
Diversity: low_visibility / critical / escalation

---

CASE ID: EC09
Sample: BDD23
Scene: Residential street có tuyết và xe đỗ
Observation: Snowbank che curb, xe đỗ che biên nhưng surface của carriageway còn liên tục
Decision: UNKNOWN + IGNORE
Expected: Road surface có `area_type=direct`; snowbank/sidewalk không label; dùng `needs_review=true` nếu biên bị tuyết che ảnh hưởng lớn
Rationale: Snowbank-as-road là critical false positive; vẫn cần giữ phần direct quan sát được
Common mistake: Mở rộng polygon qua toàn bộ tuyết vì không thấy curb
Diversity: low_visibility / occlusion / critical

---

CASE ID: EC10
Sample: BDD10
Scene: City street có curbside parking strip và nhiều xe đỗ
Observation: Vạch trắng tách phần xe chạy với dải sát curb; asphalt ngoài vạch vẫn nhìn thấy từng đoạn
Decision: LABEL + IGNORE
Expected: Lane/corridor ego có `area_type=direct`; lane hợp lệ khác nếu có là alternative; curbside parking strip ngoài vạch và dưới xe đỗ không label
Rationale: Hệ thống topology không được học parking strip như nhánh di chuyển bình thường
Common mistake: Tô từ centerline đến curb hoặc gán parking strip là alternative
Diversity: conflict / occlusion

---

CASE ID: EC11
Sample: BDD24
Scene: City street mùa đông, snowbank và xe đỗ thu hẹp lòng đường
Observation: Mép asphalt bị tuyết che; mặt đường ướt phản chiếu; xe đỗ nằm sát snowbank
Decision: LABEL + IGNORE
Expected: Hai polygon `drivable_area`: một direct và một alternative; snowbank/sidewalk/xe đỗ không label; `needs_review=false` vì continuity đủ để chốt trong bản final
Rationale: Đây là critical-risk false positive ở biên gần
Common mistake: Dùng mép xe đỗ hoặc rìa vùng sáng làm road boundary chắc chắn
Diversity: low_visibility / occlusion / critical

---

CASE ID: EC12
Sample: BDD26
Scene: City street ban đêm với xe đỗ, nguồn sáng và biên trái tối
Observation: Carriageway còn nhận biết theo lane/xe nhưng một phần biên bị mất trong bóng tối
Decision: LABEL + IGNORE
Expected: Hai polygon `drivable_area`: một direct và một alternative; `needs_review=false`; không tạo polygon trên vùng tối hoặc xe đỗ không có bằng chứng mặt đường
Rationale: Bảo toàn thông tin class; trong ảnh này lane và continuity còn đủ để chốt nên không cần escalation
Common mistake: Không vẽ gì cả hoặc tô toàn bộ khoảng tối đến mép ảnh
Diversity: low_visibility / ambiguity / critical

---
