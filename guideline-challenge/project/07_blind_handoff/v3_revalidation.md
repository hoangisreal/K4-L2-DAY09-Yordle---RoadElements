# Guideline v3 desk revalidation

Đây là kiểm tra coverage do owner thực hiện sau blind handoff. Nó xác nhận mỗi lỗi peer đã có rule, ví dụ hoặc
escalation tương ứng trong v3; **không phải** một lượt blind test độc lập mới và không thay đổi GTS của bản v2.

| Blind failure | Thay đổi v3 | Bằng chứng kiểm tra | Kết quả desk check |
|---|---|---|---|
| BDD10/d1 — thiếu 3 polygon | Checklist đếm vùng và tách polygon khi vùng/`area_type` đổi | `02_guideline.md` §2 và overlay BDD10 | Covered |
| BDD10/d2 — thiếu 3 alternative | Ví dụ chốt 1 direct + 3 alternative | `02_guideline.md` §9 và overlay BDD10 | Covered |
| BDD10/d3 — polygon lấn hood/không bám curb | Cấm kéo polygon xuống nắp capo; nhắc exclusion boundary | `02_guideline.md` §3 và overlay BDD10 | Covered |
| BDD24/d3 — polygon chồng snowbank | Nêu rõ snowbank luôn IGNORE dù curb bị che | `02_guideline.md` §5 và overlay BDD24 | Covered |
| BDD24/d4 — biên đi vào snowbank | Dừng tại wet-road còn bằng chứng; escalation nếu biên ảnh hưởng đáng kể | `02_guideline.md` §6–7 và overlay BDD24 | Covered |
| BDD26/d3 — tô vùng tối ngoài roadway | Dừng ở ranh roadway cuối cùng có bằng chứng; darkness không chứng minh road | `02_guideline.md` §5–7 và overlay BDD26 | Covered |

## Kết luận

- 6/6 failure mode đã có treatment nhìn thấy trong guideline v3.
- Gold, sample pack và điểm blind v2 giữ nguyên theo `gold-freeze`.
- Quality disposition vẫn là REJECT/ESCALATE cho kết quả blind v2. Chỉ một export mới do peer label theo v3 mới
  có thể chứng minh transferability đã cải thiện.
