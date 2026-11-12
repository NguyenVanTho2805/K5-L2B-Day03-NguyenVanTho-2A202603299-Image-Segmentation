# Báo cáo thực hành Day 3

- Mã học viên: 2A202603299
- Ngày / công cụ: 11/11/2026, CVAT local
- Công cụ đã dùng: CVAT local (Brush và Polygon). Ở Medium tôi không dùng SAM hay gợi ý tự động. Export xong tôi có nhờ Claude đọc file ZIP rồi so với ảnh để tìm chỗ sai. Phần sửa mask và export lại tôi làm trong CVAT.

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

Cả 9 ZIP đã nằm trong `submissions/` và qua script kiểm cấu trúc. Nhưng qua được script không có nghĩa là nhãn đúng hết. Chỗ nào còn sai tôi ghi lại ở đây:

- Medium: nhiều mask đang gộp nhiều vật làm một. Rõ nhất là ảnh `000000181542`: một mask `person` chứa cả nhiều người, một mask `motorcycle` chứa ba xe máy. Hai ảnh còn lại thì hàng xe đỗ và nhóm người đi bộ cũng bị gộp.
- Hard: ảnh `000000460147` có chiếc xe trắng giữa đường (mask số 33) tôi để là `building`, đúng ra phải là `car`. Mask `truck` số 39 thì gộp cả hàng xe nối đuôi nhau.
- cp6: chưa có vùng `sky`, `person`, `vegetation`. Xe buýt và xe tải tôi tô là `car`.
- cp1: ba mask `bus` (caravan, mái che vàng, xe van) chưa chắc đúng lớp.
- cp3: bên phải biển tròn ở góc trên bị mất một mảng trời nhỏ.

Export không bị lỗi gì cần báo coach.

## 2. Một quyết định trước khi dùng gợi ý

- Object Medium đầu tiên tôi tự vẽ: ảnh `000000181542`, object đầu tiên của ảnh (id 1 trong file export, lớp `person`).
- Quy tắc biên: mask đi sát phần nhìn thấy của vật, không lấy nền hay vật che bên cạnh.
- Gợi ý tự động: tôi không dùng, nên không có gì để giữ hay sửa. Cả Medium tôi vẽ tay bằng Brush và Polygon.

## 3. Một lỗi tôi tìm thấy và sửa

- Task, ảnh, vùng: `cp5_occlusion`, ảnh `000000336232`. Chỗ sai ở góc dưới phải, giữa bên phải và hàng xe bên trái.
- Loại lỗi: sai lớp và gộp xe.
- Tôi thấy gì: bản export đầu có 17 mask. Mask `bus` số 3 phủ cả hai taxi vàng ở góc dưới phải, tức là hai xe dính làm một mà lớp lại sai. Mask `bus` số 4 thật ra là chiếc xe con xanh ở giữa phải. Ngoài ra có một mask `car` gồm hai mảnh rời, nằm ở hai xe khác nhau (một ở mép trái, một gần xe buýt).
- Quy tắc và cách sửa: mỗi xe một mask và đúng lớp. Tôi tách hai taxi thành hai mask `car`, đổi xe xanh thành `car`, và tách hai xe bên trái ra.
- Sau khi sửa: tôi Save và export lại, được 19 mask. Chỉ còn hai mask `bus`, là hai xe buýt thật. Các mask `car` không còn gộp hai xe.

Tôi cũng sửa kiểu tương tự ở `cp3_thin` (cột đang là `traffic sign` thì đổi sang `pole`, bỏ thân xe khỏi `road`, phủ nốt làn đường bên phải, biển tròn thành `traffic sign`) và ở `cp1_holes` (tách hai người bị gộp).

Tôi chưa xem điểm tự đánh giá trên GitHub Actions nên không ghi số liệu nào.

## 4. Ba ca chưa chắc

| Ảnh / vị trí | Hai cách hiểu | Căn cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp6_coverage, xe buýt giữa ảnh và xe tải bên trái | Để trống vì schema không có `bus`/`truck`, hoặc tô `car` cho đủ vùng | Schema chỉ có 7 lớp. Ghi chú của starter nói "label every pixel", còn đề nói đừng tô ép vùng chưa chắc | Tôi đang tô `car`. Xin hỏi coach nên để trống hay tô `car` |
| cp1_holes, caravan ở góc trên trái, mái che vàng, xe van xanh | Gán lớp gần nhất (`truck` hoặc `car`), hoặc để trống | Không vật nào là xe buýt rõ ràng, mái che vàng không phải xe | Hiện cả ba đang là `bus`. Xin hỏi coach lớp đúng cho caravan và xe van, và có nên bỏ mái che không |
| medium_instance, ảnh `000000181542`, xe bán tải xanh giữa ảnh | `truck` hoặc `car` | Schema Medium có cả hai lớp, xe có thùng hở phía sau | Tôi gán `truck` (id 3). Xin hỏi coach xe bán tải thuộc lớp nào |
