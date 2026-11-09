# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

Họ tên: Phạm Công Bằng
MSSV: 2A202603134
Email: pcbang6161@gmail.com
Lớp: VinUni AI20k Level 2
Link repo bài làm: https://github.com/Bangpham006/K5-L2-Day02-PhamCongBang-2A202603134-ObjectDetection
Link Colab: https://colab.research.google.com/github/Bangpham006/K5-L2-Day02-PhamCongBang-2A202603134-ObjectDetection/blob/main/notebooks/day2_annotation_to_yolo.ipynb

## 2. Schema và mini guideline

Tham chiếu chi tiết tại GUIDELINE_MINI.md.

Quy ước 4 lớp xe:
1. car: xe con 4 đến 7 chỗ gồm sedan, hatchback, SUV, CUV, xe taxi, xe bán tải
2. truck: xe tải chở hàng, xe ben, đầu kéo container có cabin tách rời thùng
3. bus: xe buýt, xe khách, xe buýt nối toa 2 khúc vẽ 1 box chung
4. van: xe thùng kín thân liền khối như Transit, Hiace, xe cóc Suzuki Carry

Quy tắc vẽ box:
1. Khung vẽ sát mép ngoài vỏ xe, không lấy bóng dưới đường và không lấy vệt đèn phản chiếu
2. Xe bị che khuất chỉ vẽ phần nhìn thấy được
3. Xe bị cắt ở mép ảnh thì vẽ chạm viền ảnh rồi dừng lại
4. Bỏ qua xe quá nhỏ ở xa dưới 15 pixel, không gán người, xe máy, xe đạp, biển báo

5 ca thực tế xử lý trên ảnh:
1. drive_001.jpg: xe hatchback bạc gán car vì trần thấp, là xe con gia đình
2. drive_001.jpg: xe Nissan NV200 trắng gán van vì có rãnh cửa trượt và trần cao dạng hộp
3. drive_020.jpg: box gộp xe buýt tím và xe tuần tra trắng được tách làm 2 box riêng gồm bus cho xe buýt và car cho xe tuần tra
4. drive_036.jpg: xe microvan bạc dưới cầu vượt gán van do thân liền khối dáng hộp vuông
5. drive_045.jpg: xe tải nhỏ có thùng chở hàng gán truck vì đầu cabin tách rời với sàn thùng

Vấn đề cần làm rõ thêm: cách phân biệt xe SUV 7 chỗ cỡ lớn và xe van khi chỉ thấy góc đuôi xe vào ban đêm.

## 3. Annotation và self-QC

Số file nhãn train: 50 file từ drive_001.txt đến drive_050.txt
Chạy checker: script tools/check_yolo_labels.py đạt cấu trúc 50 trên 50 file hợp lệ. Lỗi đã xử lý: PowerShell Windows bị lỗi mã hóa font tiếng Việt cp1252, khắc phục bằng biến môi trường env:PYTHONUTF8=1
Danh sách 11 ảnh đã rà soát bằng mắt: drive_001.jpg, drive_004.jpg, drive_008.jpg, drive_015.jpg, drive_020.jpg, drive_027.jpg, drive_032.jpg, drive_036.jpg, drive_039.jpg, drive_045.jpg, drive_048.jpg
Lỗi phát hiện và đã sửa khi rà mắt:
1. Chỉnh lại các cạnh box bám sát vỏ xe, bỏ phần bóng đổ dưới đường
2. Xóa các box bắt nhầm vệt đèn pha loang trên đường ướt ban đêm
3. Đổi xe bán tải bị gán nhầm sang truck về lại car
Commit khóa nhãn ban đầu: efcd137aa14ff218ed051f92b0fc307d31d87baf

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

File kết quả: outputs/annotation_evaluation.json.

Quá trình kiểm tra và sửa nhãn:
Lần chạy đầu so với gold label, điểm F1 đạt 0.5526 do lệch nhiều ở FP (366) và FN (264), độ khít box đạt 0.8437.
Nguyên nhân và cách sửa:
1. Lệch class do bộ gán tự động không có nhãn van: có 122 đối tượng sai lớp, chủ yếu xe van bị gán thành bus hoặc truck. Đối chiếu lại guideline để đổi xe thân hộp liền khối về van và xe bán tải về car.
2. Xóa box nhiễu và vệt nước: ở các ảnh chụp đêm hoặc trời mưa như drive_001, drive_002, drive_018, drive_049, tool tự động bắt nhầm vệt đèn trên đường và xe quá nhỏ dưới 15 pixel, đã xóa các box này giúp giảm FP từ 366 xuống 83.
3. Bổ sung xe trong bóng tối: thêm các xe đỗ sát lề bị khuất ở drive_005, drive_011, drive_019, drive_026, drive_041, giảm FN từ 264 xuống 90.
Sau sửa đạt F1 = 0.8668 và mean IoU = 0.8733, đạt điều kiện qua cổng.

## 5. Kết quả YOLO

Môi trường chạy: máy cá nhân CPU Intel Core i7-12700H, PyTorch 2.15.0+cpu, Ultralytics 8.4.208
Tham số: 50 epoch, imgsz 640, seed 20260909, chỉnh batch=8 do chạy trên CPU
Checkpoint tốt nhất: outputs/yolo26_runs/vehicle_annotation/weights/best.pt

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4724 |
| Recall | 0.5832 |
| mAP50 | 0.5123 |
| mAP50–95 | 0.3314 |

Đường dẫn ảnh và log:
Ảnh dự đoán: assets/eval_predictions/visuals
Biểu đồ và chỉ số: assets/yolo26_runs/vehicle_eval

Trả lời 4 câu hỏi:
1. Lớp recall thấp nhất là truck (0.400) và bus (0.500) do số lượng xe tải và xe buýt trong tập train quá ít so với xe car. Minh họa trên ảnh drive_055.jpg, xe tải ở xa không được nhận diện do thiếu dữ liệu học.
2. Khi mAP50 cao nhưng mAP50-95 thấp, bounding box chưa khít, bị vẽ rộng thừa đầu đuôi xe hoặc dính bóng đường, làm box trượt ở các ngưỡng IoU cao từ 0.70 trở lên.
3. Lỗi nhầm giữa car và van ở dòng xe 7 chỗ hoặc car và truck ở xe bán tải do ngoại hình giống nhau, guideline cần ghi rõ tiêu chí khung gầm và khoang chở hàng.
4. Không dùng 10 ảnh eval để train nhằm tránh rò rỉ dữ liệu và học vẹt. Tập eval dùng làm validation độc lập để theo dõi quá trình học, chọn best checkpoint và đo độ tổng quát hóa trên ảnh mới.

## 6. Khó khăn và cách xử lý

| Bước thực hiện | Lỗi gặp phải | Cách xử lý | Kết quả |
|---|---|---|---|
| Chạy checker trên PowerShell | Lỗi UnicodeEncodeError do console cp1252 | Thêm env:PYTHONUTF8=1 trước lệnh | Chạy bình thường, in đủ thông báo đạt |
| Chạy CVAT offline | Thiếu container AI Nuclio trong Docker | Dùng script Python chạy YOLO local nạp nhãn sơ bộ vào CVAT | Nạp được box lên 50 ảnh để rà tay |
| Phân biệt xe lai MPV và xe con | Xe 7 chỗ khó tách giữa car và van | Tra cứu kiểu khung thân unibody đưa vào guideline | Thống nhất cách gán nhãn cho cả tập |
| Ảnh đêm dính vệt đèn phản chiếu | Box bắt nhầm vệt sáng trên đường ướt | Xóa box phản chiếu, chỉ giữ box bám vỏ xe | Box gọn gàng, mean IoU đạt 0.87 |

## 7. Tự đánh giá theo rubric

1. Checkpoint 1 guideline: đạt, đủ 4 lớp xe, 4 quy tắc box và 5 ca mơ hồ thực tế từ ảnh
2. Checkpoint 2 gán nhãn: đạt, đủ 50 file nhãn train đúng chuẩn
3. Checkpoint 3 self-QC: đạt, checker pass 50 trên 50 file, đã rà mắt 11 ảnh
4. Checkpoint 4 khóa nhãn: đạt, có commit efcd137 khóa nhãn ban đầu
5. Checkpoint 5 gold QC: đạt, phân tích nguyên nhân lỗi và sửa nhãn đạt F1 0.8668, mean IoU 0.8733
6. Checkpoint 6 huấn luyện: đạt, chạy đủ 50 epoch, mAP50 đạt 0.5123, có đủ ảnh dự đoán và biểu đồ trong assets

## 8. Công cụ và tài liệu tham khảo

1. Công cụ: CVAT để vẽ và kiểm tra box, Ultralytics để huấn luyện mô hình, Google tra cứu lỗi UTF-8 trên PowerShell
2. Tài liệu: đề bài lab VinUni, tài liệu Ultralytics YOLO
3. Việc gán nhãn, chọn ca mơ hồ và sửa lỗi do học viên tự thực hiện
