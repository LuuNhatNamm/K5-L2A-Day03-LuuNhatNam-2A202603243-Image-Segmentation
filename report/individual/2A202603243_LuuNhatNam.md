# Báo cáo thực hành Day 3

- Mã học viên theo lớp: 2A202603243
- Ngày / CVAT local: 11/11/2026 (buổi lab), CVAT local
- Công cụ đã dùng gồm Polygon trong CVAT và gợi ý polygon tự động để đối chiếu đường biên.
- Tài liệu tham khảo: [Phiếu quy tắc gán nhãn Day 3](../../guideline-mini-sheet.md).

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | `easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

## 2. Một quyết định trước khi dùng gợi ý

- Vật đầu tiên tôi tự vẽ ở Medium là xe buýt số 61, chiếc xe buýt hai tầng màu đỏ ở giữa ảnh `000000373353.jpg`, phía sau taxi vàng.
- Tôi chọn class `bus`. Đối chiếu với polygon do mô hình tạo, đường biên đi theo mép ngoài của nóc và thân xe, bao gồm cả kính thuộc thân xe. Ở phần dưới, mask dừng tại mép người đi bộ và taxi vàng đang che xe, chỉ bao phần xe buýt nhìn thấy.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: `cp1_holes`, ảnh `000000144300.jpg`, xe gần mép phải tại khoảng `(575, 142)`.
- Lỗi thuộc loại: Sai kiểu hình gán nhãn. Còn một bounding box nhãn `car` trong bài instance segmentation.
- Bằng chứng từ ZIP trước khi sửa: 22 polygon và một annotation `car` có trường `segmentation` rỗng.
- Quy tắc và hành động sửa: Mask của mỗi vật cần bám theo phần nhìn thấy. Sau khi sửa trên CVAT, bản xuất còn 22 polygon.
- Sau sửa đã Save và export lại chưa? Đã Save và xuất lại theo COCO 1.0. ZIP mới qua kiểm tra cấu trúc của bài lab.

Kết quả tự đánh giá trên máy với ground truth của ba tier là **47,2 / 82 điểm**. Easy đạt 13,7 / 20 (mIoU 0,708), Medium đạt 19,6 / 32 (mean matched IoU × recall@0,5 là 0,675), Hard đạt 13,9 / 30 (PQ 0,409). Gói ground truth này chỉ có ba tier, nên kết quả tự đánh giá chưa bao gồm `cp1_holes` và các checkpoint khác.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp1_holes`, `000000144300.jpg`, kính chắn gió trong suốt phía trên đầu xe Honda đỏ | Giữ phần kính trong mask xe hoặc để trống vùng nhìn xuyên qua kính. | Mép kính nối với đầu xe. Phiếu quy tắc yêu cầu phân biệt chi tiết trong suốt thuộc vật với khe hở thật. | Coach có thể xác nhận mask nên bám theo đường viền ngoài của tấm kính ở vùng này không? |
| `cp4_curb`, `7d83710e-4697c3b2.jpg`, lối bê tông dưới và ngay trước xe trắng bên phải | Xem phần bê tông nối liền là `sidewalk` hoặc coi phần phục vụ xe vào chỗ đỗ là vùng riêng. | Dải người đi bộ chạy dọc hàng cây và tiếp giáp phần bê tông dẫn vào chỗ đỗ. Quy tắc dựa vào chức năng và bó vỉa, kể cả khi hai vùng cùng màu. | Ở chỗ lối xe cắt ngang vỉa hè, nên giữ nhãn `sidewalk` đến đâu? |
| `cp5_occlusion`, `000000336232.jpg`, xe máy bên trái tiền cảnh, quanh chân người lái | Gán những phần xe nhìn thấy quanh chân người lái vào cùng một instance hoặc tách chúng vì đường viền bị ngắt. | Có thể nhận ra bánh trước và bánh sau thuộc cùng chiếc xe. Theo phiếu quy tắc, phần xe nhìn thấy dùng chung ID và dừng mask tại biên người che. | Với vùng này, nên biểu diễn các mảng xe rời nhau bằng cách nào trong CVAT để khi xuất COCO vẫn giữ cùng một instance? |
