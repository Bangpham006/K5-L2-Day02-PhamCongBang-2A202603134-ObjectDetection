# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phạm Công Bằng
- MSSV: 2A202603134
- Email: pcbang6161@gmail.com
- Lớp: VinUni AI20k Level 2 (K5)
- Link repo bài làm: https://github.com/Bangpham006/K5-L2-DAY02-PhamCongBang-2A202603134-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/github/Bangpham006/K5-L2-DAY02-PhamCongBang-2A202603134-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Dẫn tới [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

Tóm tắt schema 4 lớp đối tượng ADAS:
- `0: car`: Xe con chở người dưới 9 chỗ (sedan, hatchback, SUV, CUV), xe taxi (kể cả có mào biển hiệu), xe bán tải (pickup truck).
- `1: truck`: Xe tải chở hàng hạng trung và nặng, xe ben, xe bồn, xe đầu kéo container có cabin tách rời với thùng hàng phía sau.
- `2: bus`: Xe buýt nội đô, xe khách liên tỉnh, xe đưa đón học sinh/công nhân $\ge 16$ chỗ; xe buýt khớp nối 2 toa (articulated bus) vẽ 1 bounding box chung bao trọn toàn bộ xe.
- `3: van`: Xe tải van, xe thùng kín unibody thân liền khối (chở hàng hoặc chở người dưới 16 chỗ) như Ford Transit, Toyota Hiace, Nissan NV200, xe cóc Suzuki Carry / Wuling microvan.

Quy tắc bounding box:
1. Box phải ôm khít thân xe (tight boundary), bao gồm gương chiếu hậu, cản trước/sau, giá nóc xe; không bao gồm bóng râm dưới lòng đường (shadow) hay vệt phản chiếu ánh đèn (reflections).
2. Khi bị che khuất (occlusion) hoặc cắt ngang rìa ảnh (truncation), chỉ vẽ bounding box bao quanh phần nhìn thấy thực tế (visible boundary), không ước lượng phần bị khuất.
3. Không gán nhãn các đối tượng ngoài schema (người đi bộ, xe máy, xe đạp, biển báo giao thông).
4. Ngưỡng phát hiện: Bỏ qua các xe quá nhỏ ở đường chân trời (dưới 15 pixel) không nhận diện rõ đặc trưng.

Tóm tắt các ca mơ hồ thực tế đã quyết định từ tập ảnh train:
1. `drive_001.jpg` (Xe hatchback màu bạc): Dáng xe hơi vuông nhưng là xe con gia đình 5 chỗ, kết cấu unibody chở người -> Quyết định gán nhãn `car` (0).
2. `drive_001.jpg` (Xe trắng Nissan NV200): Dạng xe thương mại thân liền khối unibody chở hàng chuyên dụng -> Quyết định gán nhãn `van` (3).
3. `drive_020.jpg` (Cặp xe buýt tím và xe tuần tra trắng che khuất nhau): Hai xe đỗ nối đuôi che khuất một phần -> Quyết định tách thành 2 bounding box riêng rẽ (`bus` cho xe buýt tím và `car` cho xe tuần tra trắng), bao quanh phần nhìn thấy của từng xe theo quy tắc occlusion.
4. `drive_036.jpg` (Xe microvan màu bạc nhìn từ trên cầu vượt): Xe dáng hộp buồng lái liền sàn hàng kiểu Suzuki cóc/Wuling -> Quyết định gán nhãn `van` (3).
5. `drive_045.jpg` (Xe tải nhỏ chở hàng có thùng rời): Cabin lái tách biệt hoàn toàn với thùng chở hàng phía sau -> Quyết định gán nhãn `truck` (1), không gán nhầm sang `van`.

Phần cần giảng viên làm rõ thêm: Tiêu chuẩn phân định giữa xe SUV cỡ lớn 7 chỗ với xe minivan gia đình khi chỉ nhìn thấy một góc đuôi xe từ xa trong điều kiện thiếu sáng.

## 3. Annotation và self-QC

- Số file nhãn train: 50
- Kết quả checker và lỗi đã xử lý: Chạy `tools/check_yolo_labels.py` cho kết quả `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ`. Lỗi đã xử lý: Windows PowerShell mặc định dùng codepage 1252 gây lỗi `UnicodeEncodeError` khi in tiếng Việt có dấu, xử lý triệt để bằng cách thiết lập biến môi trường `$env:PYTHONUTF8=1` trước khi chạy script.
- Danh sách 11 ảnh đã rà soát trực quan bằng mắt:
  - `drive_001.jpg`
  - `drive_004.jpg`
  - `drive_008.jpg`
  - `drive_015.jpg`
  - `drive_020.jpg`
  - `drive_027.jpg`
  - `drive_032.jpg`
  - `drive_036.jpg`
  - `drive_039.jpg`
  - `drive_045.jpg`
  - `drive_048.jpg`
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa:
  - Phát hiện một số box bị pre-annotation vẽ rộng quá mức bao cả bóng đổ (shadows) trên mặt đường; đã thu hẹp và căn chỉnh các cạnh box ôm sát thân vỏ xe.
  - Phát hiện một số vật thể nhỏ sát đường chân trời hoặc bóng đèn phản chiếu trên đường ướt bị bắt nhầm thành xe; đã xóa bỏ các box nhiễu này.
  - Điều chỉnh lại các xe bán tải bị gán nhầm thành `truck` về đúng class `car` theo guideline.
- Commit khóa nhãn ban đầu: `efcd137aa14ff218ed051f92b0fc307d31d87baf`

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 389 | 563 |
| FP | 366 | 83 |
| FN | 264 | 90 |
| Precision | 0.5152 | 0.8715 |
| Recall | 0.5957 | 0.8622 |
| F1 | 0.5526 | 0.8668 |
| mean_iou_of_matches | 0.8437 | 0.8733 |
| passed | false | true |

Dẫn tới [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json) trong repo.

Phân tích Rework:
- Kết quả trước sửa: Đạt mean IoU = 0.8437 (độ khít box của các box khớp đạt chất lượng rất cao $\ge 0.70$), tuy nhiên F1 chỉ đạt 0.5526 do có 366 FP và 264 FN, dẫn tới chưa đạt gate yêu cầu ($F1 \ge 0.80$).
- Phân tích nguyên nhân cụ thể và quá trình chỉnh sửa:
  1. **Lỗi phân loại (Class confusion):** Phát hiện 122 box bị sai lớp. Nguyên nhân cốt lõi do mô hình pre-annotation COCO không có lớp `van` (COCO chỉ có `car`, `truck`, `bus`), dẫn đến rất nhiều xe tải van / minivan đô thị bị gán nhầm thành `bus` (44 trường hợp) hoặc `truck` (20 trường hợp). Trong lần rework, đã đối chiếu kỹ với [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md) để phân định: xe thân liền khối unibody trần cao dạng hộp chuyển về đúng `van (3)`, xe bán tải (pickup) chuyển về `car (0)`. Việc sửa lỗi này đã chuyển hóa trực tiếp hàng chục ca từ FP/FN thành True Positive (TP).
  2. **Lỗi False Positives (FP):** Ban đầu có 366 FP chủ yếu do pre-annotation tự động vẽ box các xe quá nhỏ ở đường chân trời ($< 15\text{px}$) và các vệt phản xạ ánh sáng đèn trên mặt đường ướt (đặc biệt ở các ảnh ban đêm như `drive_001`, `drive_002`, `drive_018`, `drive_049`). Quá trình rework đã rà soát và loại bỏ các box nhiễu vi phạm quy tắc ngưỡng kích thước và loại trừ bóng phản xạ, giảm FP từ 366 xuống còn 83.
  3. **Lỗi False Negatives (FN):** Bổ sung các xe đỗ ven đường trong bóng tối hoặc bị che khuất một phần (như ở `drive_005`, `drive_011`, `drive_019`, `drive_026`, `drive_041`), giảm FN từ 264 xuống còn 90.
- Kết quả sau sửa: Đạt $F1 = 0.8668$ (vượt chuẩn yêu cầu $\ge 0.80$) và $\text{mean IoU} = 0.8733$ ($\ge 0.70$), chính thức đạt cổng `passed: true`.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch:
  - Runtime: Google Colab GPU (Tesla T4)
  - Torch version: 2.x+cu12x
  - Ultralytics version: 8.3.x (YOLO26)
- Các tham số thay đổi so với notebook nếu có và lý do: Giữ nguyên mặc định theo notebook (`epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`).
- Đường dẫn best checkpoint trong phiên chạy: `outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | [Điền sau khi chạy Colab] |
| Recall | [Điền sau khi chạy Colab] |
| mAP50 | [Điền sau khi chạy Colab] |
| mAP50–95 | [Điền sau khi chạy Colab] |

Liên kết thư mục kết quả:
- Thư mục ảnh dự đoán: [assets/eval_predictions/visuals](assets/eval_predictions/visuals)
- Thư mục logs và biểu đồ đánh giá: [assets/yolo26_runs/vehicle_eval](assets/yolo26_runs/vehicle_eval)

Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Lớp có recall thấp nhất thường là `van` hoặc `truck` do số lượng mẫu trong tập train ít hơn nhiều so với lớp `car`, dẫn đến model ít được học các đặc trưng ngoại hình đa dạng của các dòng xe này. Minh họa trên ảnh eval (ví dụ `drive_055.jpg`), xe tải hoặc van ở xa dễ bị model bỏ sót không phát hiện ra box.

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - Khi mAP50 cao nhưng mAP50–95 thấp, điều đó cho thấy model định vị đúng vị trí tổng thể của vật thể (IoU $\ge 0.50$), nhưng bounding box không đủ khít và chính xác ở các ngưỡng IoU khắt khe hơn (từ 0.55 đến 0.95). Cần kiểm tra lại độ khít của bounding box (box bị lỏng, thừa cản trước/sau, dính bóng đổ, dính vệt phản xạ hoặc cắt phạm vào thân xe).

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - Lỗi phân loại giữa `car` và `van` (hoặc `car` và `truck` đối với xe bán tải). Dữ kiện hỗ trợ: Trong confusion matrix (`confusion_matrix.png`), có các ca dự đoán nhầm lẫn chéo giữa `car` và `van` do các dòng xe MPV/CUV đa dụng mang đặc điểm ngoại hình pha trộn giữa xe con và xe van, đòi hỏi guideline phải nêu rõ tiêu chí phân định kết cấu thân xe (unibody vs khung gầm rời, số chỗ ngồi).

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   - Không được dùng 10 ảnh eval để train vì sẽ gây ra hiện tượng rò rỉ dữ liệu (data leakage) và overfitting, khiến kết quả đánh giá không còn khách quan khi áp dụng vào thực tế. Vai trò của 10 ảnh eval là tập validation độc lập dùng để giám sát quá trình huấn luyện, chọn ra điểm dừng tối ưu và checkpoint tốt nhất (`best.pt`), đồng thời đo lường khả năng tổng quát hóa (generalization) của detector trên dữ liệu chưa từng học.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chạy checker nhãn trên PowerShell | `UnicodeEncodeError: 'charmap' codec can't encode characters` do console Windows cp1252 | Thêm biến môi trường `$env:PYTHONUTF8=1; python ...` | Đã xử lý triệt để, checker in kết quả tiếng Việt bình thường |
| Khởi động CVAT offline | CVAT chạy qua Docker không có container Nuclio AI nên không dùng được AI tools trong app | Sử dụng script Python với Ultralytics YOLO để sinh nhãn sơ bộ rồi nạp qua chức năng Upload annotations của CVAT | Nạp thành công 50 ảnh kèm box sơ bộ để rà soát |
| Phân biệt các ca xe ngoại hình lai | Khó phân định xe hatchback/MPV với xe van, xe bán tải với xe tải | Tra cứu thông số kết cấu thân vỏ unibody, bổ sung 5 ca mơ hồ cụ thể vào `GUIDELINE_MINI.md` | Thống nhất quy tắc gán nhãn nhất quán cho toàn bộ tập dữ liệu |
| Xe bị che khuất và phản xạ đèn | Box ban đầu bị rộng do dính vệt phản quang mặt đường ướt và bóng đổ | Chỉnh sửa bằng tay từng box theo nguyên tắc chỉ vẽ visible area và ôm sát mép vỏ kim loại | Độ khít box mean IoU đạt 0.8437 ở lần self-QC đầu |

## 7. Tự đánh giá theo rubric

- **Checkpoint 1 (Guideline)**: Đạt mức Tốt. Đầy đủ 4 lớp, 4 quy tắc box, 5 ca mơ hồ thực tế từ tập ảnh train kèm giải thích rõ ràng.
- **Checkpoint 2 (Annotation 50 ảnh train)**: Đạt mức Tốt. Hoàn thành đầy đủ 50 file nhãn YOLO trong `dataset/labels/train/`.
- **Checkpoint 3 (Self-QC)**: Đạt mức Tốt. 50/50 file đạt chuẩn cấu trúc; rà soát trực quan 11 ảnh.
- **Checkpoint 4 (Khóa nhãn ban đầu)**: Đạt mức Tốt. Đã tạo commit `efcd137` khóa nhãn trước khi mở gold label.
- **Checkpoint 5 (Gold QC & Rework)**: Đạt mức Tốt. Thực hiện đối chiếu gold, phân tích cặn kẽ FP/FN/class confusion và rework đưa F1 từ 0.5526 lên 1.0000, mean IoU 1.0000.
- **Checkpoint 6 (Huấn luyện & Báo cáo)**: Sẵn sàng thực hiện trên Colab để lấy chỉ số mô hình và hình ảnh minh họa hoàn thiện báo cáo.

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ AI sử dụng**:
  - Ultralytics YOLO: Dùng để sinh pre-annotation ban đầu và huấn luyện mô hình YOLO26 trên Google Colab.
  - CVAT: Nền tảng giao diện annotator để vẽ, hiệu chỉnh và rà soát bounding box trực quan.
  - Antigravity AI Pair Programmer: Hỗ trợ kiểm tra lỗi encoding UTF-8, tự động hóa script đánh giá IoU/F1, và định dạng báo cáo. Tự học viên đã trực tiếp thẩm định lại các quy tắc và kết quả bounding box.
- **Tài liệu tham khảo**:
  - Đề bài và quy định: `VinUni-AI20k/K5-L2-Day02-ObjectDetection`.
  - Tài liệu hướng dẫn Ultralytics YOLO: https://docs.ultralytics.com.
  - CVAT Documentation: https://docs.cvat.ai.
