# Báo cáo thực hành Day 3 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên [LMS](https://aithucchien.vinuni.edu.vn). Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](../../reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202603299 (MSSV)
- Ngày / CVAT local: 11/11/2026 / CVAT local
- Công cụ đã dùng: CVAT local (Brush, Polygon). Không dùng SAM hay gợi ý tự động ở Medium. Sau khi export, tôi nhờ Claude (trợ lý AI) đọc file ZIP và đối chiếu với ảnh để tìm lỗi. Việc sửa mask và export lại do tôi làm trong CVAT.

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh [LMS](https://aithucchien.vinuni.edu.vn) đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

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

Cả 9 ZIP đều có trong `submissions/` và qua bước kiểm cấu trúc (`scripts/inspect_submissions.py`). Cấu trúc đạt không có nghĩa nhãn đã đúng. Các hạn chế tôi đã biết sau khi kiểm lại:

- **medium_instance:** nhiều mask gộp nhiều vật, chưa tách. Ví dụ ảnh `000000181542`: mask `person` (id 1) gộp nhiều người, mask `motorcycle` (id 2) gộp ba xe máy. Ảnh `000000373353` và `000000458325`: các hàng xe đỗ và nhóm người đi bộ cũng bị gộp.
- **hard_panoptic:** ảnh `000000460147`, mask id 33 (xe trắng giữa làn) đang gán `building`, đúng ra là `car`. Mask `truck` id 39 gộp cả đoàn xe nối đuôi.
- **cp6_coverage:** chưa có pixel `sky`, `person`, `vegetation`. Xe buýt và xe tải đang tô `car`.
- **cp1_holes:** ba mask `bus` (caravan, mái che vàng, xe van) chưa chắc đúng lớp.
- **cp3_thin:** mất một mảng trời nhỏ (khoảng 9.400 px) bên phải biển tròn ở góc trên.

Không có lỗi export cần báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ảnh `000000181542`, object đầu tiên trong ảnh (id 1 trong file export, lớp `person`).
- Class và quy tắc tôi dùng để chọn biên: `person`; mask bám sát phần nhìn thấy của vật, không lấy nền hay vật che bên cạnh.
- Nếu dùng gợi ý sau đó: không áp dụng, tôi không dùng gợi ý tự động.
- Nếu không dùng gợi ý: không dùng. Toàn bộ vẽ tay bằng Brush/Polygon.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: `cp5_occlusion`, ảnh `000000336232`, góc dưới phải và giữa phải, cùng hàng xe bên trái.
- Lỗi thuộc loại: sai lớp và gộp-tách.
- Bằng chứng tôi nhìn thấy: khi đối chiếu ZIP với ảnh, mask `bus` thứ 3 phủ cả hai taxi vàng ở góc dưới phải; mask `bus` thứ 4 là chiếc xe con xanh ở giữa phải; một mask `car` có hai mảnh rời nằm ở hai xe khác nhau (mép trái và gần xe buýt). Bản đầu có 17 mask.
- Quy tắc và hành động sửa: mỗi vật một mask, đúng lớp, chỉ vẽ phần nhìn thấy. Tôi tách hai taxi thành hai mask `car`, đổi mask xe xanh thành `car`, tách hai xe bị gộp ở bên trái.
- Sau sửa đã Save và export lại chưa? Rồi. Bản mới có 19 mask, chỉ còn 2 mask `bus` là hai xe buýt thật, các mask `car` không còn gộp hai xe. Tôi cũng sửa theo cách tương tự ở `cp3_thin` (cột từ `traffic sign` sang `pole`, bỏ thân xe khỏi `road`, phủ nốt làn phải, biển tròn thành `traffic sign`) và `cp1_holes` (tách hai người bị gộp).

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): chưa có điểm. Scorecard ba tier tối đa **82**, không phải điểm cuối trên 100. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| cp6_coverage, xe buýt giữa ảnh và xe tải bên trái | Để trống vì schema không có `bus`/`truck`, hoặc tô `car` để phủ vùng | Schema chỉ có 7 lớp; ghi chú starter nói “label every pixel”, còn đề nói không tô ép vùng chưa chắc | Tôi đang tô `car`. Hỏi coach: nên để trống hay tô `car`? |
| cp1_holes, caravan (góc trên trái), mái che vàng, xe van xanh | Gán lớp gần nhất (`truck`/`car`) hoặc để trống | Không vật nào là xe buýt rõ ràng; mái che vàng không phải xe | Hiện đang gán `bus` cho cả ba. Hỏi coach lớp đúng cho caravan và xe van, và có bỏ mái che không |
| medium_instance, ảnh `000000181542`, xe bán tải xanh giữa ảnh | `truck` hoặc `car` | Schema Medium có cả `car` và `truck`; xe có thùng hở phía sau | Tôi gán `truck` (id 3). Hỏi coach xe bán tải thuộc lớp nào |
