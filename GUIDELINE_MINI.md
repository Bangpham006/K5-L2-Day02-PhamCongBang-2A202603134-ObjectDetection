# Guideline gán nhãn thu nhỏ

Tài liệu này ghi lại các quy tắc thống nhất trước và trong quá trình gán nhãn cho tập dữ liệu xe cộ ngày 2.

## Định nghĩa các lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Toàn bộ xe con chở người từ 4–7 chỗ: sedan, hatchback, SUV, CUV, xe taxi và xe bán tải (pickup). | Nắp ca-pô thấp, trần xe thấp; xe bán tải cabin liền thân dù có thùng hàng hở nhỏ phía sau vẫn tính là `car`. Xe taxi gắn mào trên nóc vẫn tính là `car`. |
| `truck` | Xe chuyên dụng chở hàng nặng: xe tải thùng, xe ben, xe bồn, đầu kéo container. | Khung gầm cao, đầu cabin lái tách rời thùng hàng phía sau (có khoảng hở/khớp nối cơ học ở giữa); kích thước to lớn hơn xe con. |
| `bus` | Xe buýt nội đô, xe buýt khớp nối 2 toa, xe khách liên tỉnh, minibus từ 16 chỗ trở lên. | Thân xe dài hình hộp chữ nhật, có nhiều ô cửa sổ hành khách chạy dọc sườn xe, chuyên chở đông người. |
| `van` | Xe minivan gia đình và xe tải van chở hàng kín | Thân xe liền một khối  từ đầu đến đuôi dạng khối hộp chữ nhật, trần cao vút, thường có rãnh cửa trượt  bên hông; không có cốp nhô ra như sedan nhưng ngắn hơn xe buýt. |

## Quy tắc chung

- **Box bám sát vật thể nghĩa là:** Kéo khung chữ nhật chạm khít 4 mép ngoài cùng của thân xe cản trước, cản sau, điểm cao nhất của nóc và điểm tiếp đất của bánh xe. Không lấy thừa mặt đường.
- **Vật thể bị che khuất:** Nếu xe bị cây cối, dải phân cách, cột đèn hoặc xe khác che mất một phần, chỉ kéo khung bao trọn phần mắt còn nhìn thấy được. Tuyệt đối không vẽ mò theo tưởng tượng phần thân xe bị khuất.
- **Vật thể bị cắt bởi rìa ảnh:** Vẽ khung chạm mép viền ảnh rồi dừng lại, không kéo lố ra ngoài biên và không đoán phần xe ngoài khung.
- **Vật thể rất nhỏ/mơ hồ:** Những xe ở quá xa tít chân trời, bị nhòe nét hoặc nhỏ dưới 10px x 10px thì bỏ qua, không gán nhãn để tránh nhiễu vào mô hình.
- **Loại trừ tuyệt đối:** Người đi bộ, người đi xe máy, xe đạp, biển báo giao thông không thuộc phạm vi phát hiện, tuyệt đối không vẽ box.

## Ghi lại quyết định cho các ca mơ hồ thực tế từ quá trình gán nhãn

| Ảnh | Tình huống thực tế đối chiếu trên ảnh | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001.jpg` | Chiếc xe màu bạc đuôi cụt ở đường gom bên trái, thân dài, đỗ cạnh xe sedan trắng và xe CUV trắng. Dễ phân vân giữa `car` (hatchback/wagon) và `van`. | Gán nhãn **`car` (0)**. | Thân xe thấp, trần xe ngang bằng với chiếc sedan và CUV bên cạnh, nắp ca-pô vát thấp đặc trưng xe con du lịch 5 chỗ, không có trần cao dạng khối hộp vuông vức của xe van. |
| `drive_001.jpg` | Chiếc xe màu trắng đỗ ở lối rẽ ngõ bên trái, có đường rãnh ray trượt ngang hông xe, trần cao khối hộp, mũi xe ngắn dốc (Nissan NV200). Dễ phân vân `van` hay `car`. | Gán nhãn **`van` (3)**. | Có đường ray cửa trượt (cửa lùa) đặc trưng của dòng minivan/van bưu kiện, trần cao vượt trội so với xe con, thân liền khối unibody thuộc lớp `van`. |
| `drive_020.jpg` | Công cụ tự động vẽ 1 box gộp 2 xe làm một và gắn nhãn nhầm là `TRUCK`: phía sau là xe khách/buýt mini màu tím có nhiều cửa kính sườn, phía trước là xe con tuần tra nhỏ màu trắng. | **Xóa box gộp, tách thành 2 box riêng biệt:**<br>- Xe to màu tím: gán **`bus` (2)**.<br>- Xe nhỏ màu trắng: gán **`car` (0)**. | Đề bài cấm tuyệt đối gộp 2 xe vào 1 box. Xe chở khách thân dài nhiều cửa sổ thuộc `bus`; xe tuần tra nhỏ thuộc `car`. Không có xe nào là `truck` vì không có cabin tách rời thùng hàng. |
| `drive_036.jpg` | Nhìn từ trên cầu vượt xuống mặt đường ướt: chiếc xe màu bạc chạy làn giữa bật đèn pha sáng, trần xe phẳng lì kéo dài ra đuôi, đuôi xe dựng đứng vuông góc 90 độ. Dễ phân vân `van` hay `car`. | Gán nhãn **`van` (3)**. | Dáng thân hộp chữ nhật đứng, trần xe cao phẳng lì từ kính lái ra tận đuôi, không có phần cốp nhô ra, thuộc dòng microvan/minivan đô thị (Wuling/Stepwgn). |
| `drive_045.jpg` | Xe microvan màu xanh bạc có giá nóc và cửa lùa bên hông. Phân vân giữa `van` hay `truck` (xe tải nhỏ). | Gán nhãn **`van` (3)**. | Toàn bộ trần xe, sàn xe và sườn xe là một khối kim loại liền mạch từ đầu tới đuôi (unibody). Xe không có đầu cabin tách rời với thùng hàng nên không được xếp vào `truck`. |
