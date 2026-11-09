# Guideline gán nhãn thu nhỏ

Quy tắc thống nhất khi gán nhãn tập dữ liệu xe cộ.

## Định nghĩa các lớp

| Lớp | Định nghĩa | Điểm phân biệt |
|---|---|---|
| car | xe con chở người 4 đến 7 chỗ gồm sedan, hatchback, SUV, CUV, xe taxi và xe bán tải | nắp ca-pô thấp, trần thấp; xe bán tải cabin liền thân dù có thùng hở vẫn tính là car; taxi có mào trên nóc vẫn tính là car |
| truck | xe chở hàng nặng gồm xe tải thùng, xe ben, xe bồn, đầu kéo container | khung gầm cao, cabin lái tách rời thùng hàng phía sau có khớp nối ở giữa, kích thước lớn |
| bus | xe buýt nội đô, xe buýt khớp nối 2 toa, xe khách, xe đưa đón từ 16 chỗ trở lên | thân dài hình hộp, nhiều ô cửa sổ dọc sườn xe, chở đông người |
| van | xe minivan gia đình và xe tải van chở hàng thùng kín | thân xe liền khối từ đầu đến đuôi dạng khối hộp, trần cao, thường có cửa trượt bên hông, không có cốp nhô ra |

## Quy tắc chung

1. Box bám sát vật thể: kéo khung chạm khít 4 mép ngoài của thân xe gồm cản trước, cản sau, nóc và điểm tiếp đất của bánh xe, không lấy bóng dưới đường.
2. Vật thể bị che khuất: xe bị che một phần chỉ vẽ phần nhìn thấy được, không vẽ theo tưởng tượng phần bị khuất.
3. Vật thể bị cắt bởi rìa ảnh: vẽ khung chạm mép viền ảnh rồi dừng lại, không đoán phần xe ngoài khung.
4. Vật thể rất nhỏ: xe ở quá xa sát chân trời nhỏ dưới 15 pixel thì bỏ qua để tránh nhiễu.
5. Loại trừ: người đi bộ, xe máy, xe đạp, biển báo giao thông không thuộc phạm vi gán nhãn, không vẽ box.

## Quyết định cho các ca mơ hồ thực tế từ quá trình gán nhãn

| Ảnh | Tình huống thực tế trên ảnh | Quyết định | Lý do |
|---|---|---|---|
| drive_001.jpg | xe hatchback màu bạc đỗ cạnh sedan và CUV, dễ phân vân giữa car và van | gán car (0) | thân xe thấp ngang sedan bên cạnh, ca-pô vát thấp của xe con 5 chỗ, không có trần cao dạng hộp của van |
| drive_001.jpg | xe Nissan NV200 màu trắng ở ngõ bên trái, dễ phân vân van hay car | gán van (3) | có rãnh cửa trượt hông xe, trần cao vuông vức, thân liền khối đặc trưng xe tải van |
| drive_020.jpg | tool tự động vẽ 1 box gộp xe buýt tím và xe tuần tra trắng làm một | tách thành 2 box riêng: xe buýt tím gán bus (2), xe tuần tra gán car (0) | quy định không gộp 2 xe vào 1 box; xe buýt nhiều cửa sổ thuộc bus, xe tuần tra nhỏ thuộc car |
| drive_036.jpg | xe microvan bạc chạy dưới cầu vượt nhìn từ trên xuống | gán van (3) | dáng thân hộp chữ nhật đứng, trần cao phẳng kéo dài ra đuôi kiểu Suzuki cóc hoặc Wuling |
| drive_045.jpg | xe tải nhỏ chở hàng có thùng rời phía sau | gán truck (1) | đầu cabin lái tách rời hoàn toàn với thùng chở hàng phía sau, không phải vỏ liền của van |
