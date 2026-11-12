# Báo cáo thực hành Day 3

- Mã học viên: 2A202603299
- Ngày / công cụ: 11–12/11/2026, CVAT local
- Công cụ đã dùng: CVAT local (Brush và Polygon). Ở Medium tôi không dùng SAM hay gợi ý tự động. Tôi vẽ và sửa mask, rồi export lại trong CVAT.
- Dùng Claude và đáp án: sau khi tải đáp án ba tier do coach phát (`tiers_gt.zip`, tải về lúc 08:26 ngày 12/11), tôi nhờ Claude chạy scorer của repo, liệt kê vật thiếu/thừa và dựng ảnh đối chiếu bài của tôi với đáp án. Claude không sửa file ZIP. Các lần sửa sau thời điểm đó là sửa sau khi đã xem đáp án.

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Cả 9 ZIP đã nằm trong `submissions/` và qua script kiểm cấu trúc. Qua được script không có nghĩa là nhãn đúng hết.

### Điểm tự đánh giá của ba tier (scorer của repo, tối đa 82)

| Task | Bản export có sẵn lúc 08:06, trước khi tải đáp án | Bản nộp hiện tại (sau khi xem đáp án) |
| --- | ---: | ---: |
| easy_semantic | 18.3 | 18.3 |
| medium_instance | 0.0 | 13.2 |
| hard_panoptic | 11.8 | 18.8 |
| **Cộng** | **30.1** | **50.3** |

Sáu checkpoint không có đáp án nên tôi chưa chấm được. Điểm bản nộp là điểm sau khi tôi xem đáp án, không phải bản tự làm độc lập. Đây là điểm tự đánh giá, không thay điểm chính thức của coach.

### Chỗ còn sai trong bản nộp

- Medium: còn 15 trong 71 vật chưa khớp, chủ yếu là người nhỏ hoặc bị che và 4 xe ở ảnh `000000373353`. IoU trung bình của vật đã khớp là 0.74.
- Hard, ảnh `000000460147`: mask `truck` lớn (x315-438, y168-369) vẫn gộp xe tải với cả hàng xe phía sau; khoảng 9.900 px cây đang tô `building`.
- Hard, ảnh `000000350023`: `sidewalk` chưa vượt ngưỡng khớp; các vật nhỏ 30–40 px (người, xe đạp) chưa khớp.
- Easy: tô thừa lên xe, người, cột (khoảng 69.000 px) và còn lẫn `building`/`vegetation` ở hai ảnh sau.
- cp6: chưa có vùng `sky`, `person`, `vegetation`. Xe buýt và xe tải tôi tô là `car`.
- cp1: ba mask `bus` (caravan, mái che vàng, xe van) chưa chắc đúng lớp.
- cp3: bên phải biển tròn ở góc trên bị mất một mảng trời nhỏ.

Export không bị lỗi gì cần báo coach.

## 2. Một quyết định trước khi dùng gợi ý

- Object Medium đầu tiên tôi tự vẽ: ảnh `000000181542`, lớp `person`, là object id 1 trong lần export đầu (vùng x34–640, y115–465). Lần đó mask này gộp nhiều người thành một. Sau khi so với đáp án tôi tách thành từng người; ảnh này giờ khớp 20/20 vật, và vì vậy id trong ZIP hiện tại đã đổi thứ tự (id 1 bây giờ là một xe máy).
- Quy tắc biên: mask đi sát phần nhìn thấy của vật, không lấy nền hay vật che bên cạnh. Quy tắc instance là mỗi người một mask; lần đầu tôi chưa áp dụng đúng chỗ này.
- Gợi ý tự động: tôi không dùng, nên không có gì để giữ hay sửa. Cả Medium tôi vẽ tay bằng Brush và Polygon.

## 3. Một lỗi tôi tìm thấy và sửa

- Task, ảnh, vùng: `cp5_occlusion`, ảnh `000000336232`. Chỗ sai ở góc dưới phải, giữa bên phải và hàng xe bên trái.
- Loại lỗi: sai lớp và gộp xe.
- Tôi thấy gì: bản export đầu có 17 mask. Mask `bus` số 3 phủ cả hai taxi vàng ở góc dưới phải, tức là hai xe dính làm một mà lớp lại sai. Mask `bus` số 4 thật ra là chiếc xe con xanh ở giữa phải. Ngoài ra có một mask `car` gồm hai mảnh rời, nằm ở hai xe khác nhau (một ở mép trái, một gần xe buýt).
- Quy tắc và cách sửa: mỗi xe một mask và đúng lớp. Tôi tách hai taxi thành hai mask `car`, đổi xe xanh thành `car`, và tách hai xe bên trái ra.
- Sau khi sửa: tôi Save và export lại, được 19 mask. Chỉ còn hai mask `bus`, là hai xe buýt thật. Các mask `car` không còn gộp hai xe.

Tôi cũng sửa kiểu tương tự ở `cp3_thin` (cột đang là `traffic sign` thì đổi sang `pole`, bỏ thân xe khỏi `road`, phủ nốt làn đường bên phải, biển tròn thành `traffic sign`) và ở `cp1_holes` (tách hai người bị gộp).

**Lỗi tìm ra nhờ so với đáp án (sau khi xem đáp án):** Medium, ảnh `000000181542`. Bản export trước khớp 13/20 vật; nhóm người nhỏ ngồi trên thùng xe bán tải (x272-372, y114-148) bị vẽ gộp hoặc lệch, xe máy ở mép trái (x0-71, y254-352) vẽ lệch. Sau khi sửa trong CVAT, Save và export lại (ZIP lúc 10:20), ảnh này khớp 20/20 vật.

## 4. Các ca chưa chắc

| Ảnh / vị trí | Hai cách hiểu | Căn cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| hard_panoptic, ảnh `000000460147`, mặt đường chính | `road` (mặt đường xe chạy) hoặc `sidewalk` (theo đáp án) | Ảnh chụp đường cao tốc có xe chạy. Đáp án gán gần hết mặt đường là `pavement-merged` (63.310 px), nên được tính là `sidewalk`; `road` chỉ có 11.824 px | Tôi giữ `road`. Xin coach xem lại nhãn đáp án của ảnh này |
| Medium và Hard, các vật rất nhỏ hoặc bị che | Vẽ cho đủ số vật, hoặc bỏ qua | Một số vật trong đáp án chỉ rộng 3–7 px, hoặc chỉ thấy đầu người ở xa, hoặc người ngồi sau kính xe. Lệch 1 px là mất khớp | Tôi không vẽ khoảng 11 vật ở Hard và khoảng 10 vật ở Medium. Xin coach cho biết có nên bỏ qua loại vật này không |
| cp6_coverage, xe buýt giữa ảnh và xe tải bên trái | Để trống vì schema không có `bus`/`truck`, hoặc tô `car` cho đủ vùng | Schema chỉ có 7 lớp. Ghi chú của starter nói "label every pixel", còn đề nói đừng tô ép vùng chưa chắc | Tôi đang tô `car`. Xin hỏi coach nên để trống hay tô `car` |
| cp1_holes, caravan ở góc trên trái, mái che vàng, xe van xanh | Gán lớp gần nhất (`truck` hoặc `car`), hoặc để trống | Không vật nào là xe buýt rõ ràng, mái che vàng không phải xe | Hiện cả ba đang là `bus`. Xin hỏi coach lớp đúng cho caravan và xe van, và có nên bỏ mái che không |
| medium_instance, ảnh `000000181542`, xe bán tải xanh giữa ảnh | `truck` hoặc `car` | Schema Medium có cả hai lớp, xe có thùng hở phía sau | Tôi gán `truck`. Sau khi xem đáp án, xe này cũng là `truck` và mask khớp (IoU 0.92), nên giữ nguyên |
