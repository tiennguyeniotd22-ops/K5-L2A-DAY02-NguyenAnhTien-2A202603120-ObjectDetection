# Mini guideline — Object Detection Ngày 2

> Codex đã giúp soạn bản nháp này dựa trên GUIDE.md và ba ảnh train. Trước khi nộp, hãy mở ảnh cùng nhãn trong CVAT để kiểm tra và xác nhận hoặc sửa các quyết định dưới đây.

## 1. Schema lớp

Giữ nguyên thứ tự class ID trong data.yaml. Không đổi tên lớp hoặc ID.

| ID | Lớp | Cách áp dụng |
|---:|---|---|
| 0 | car | Xe con thuộc schema. Nếu kiểu xe không rõ hoặc dễ lẫn với van/truck, không đoán; ghi mã ảnh và hỏi giảng viên. |
| 1 | truck | Xe tải chở hàng hoặc xe đầu kéo khi hình dáng/cabin và khoang chở hàng đủ rõ. |
| 2 | bus | Xe buýt khi kiểu thân xe và đặc điểm chở khách đủ rõ. Minivan không tự động xếp vào bus. |
| 3 | van | Xe van/minivan khi nhận diện chắc theo schema. Nếu khó phân biệt với car hoặc bus, hỏi giảng viên. |

## 2. Quy tắc vẽ box

- Mỗi xe thật thuộc bốn lớp là một box riêng; không gộp nhiều xe vào cùng một box.
- Box bao sát phần thân xe nhìn thấy được, tránh lấy thừa mặt đường, bầu trời hoặc xe bên cạnh.
- Xe bị che một phần vẫn gán nếu còn nhận ra đủ dấu hiệu và class.
- Xe bị cắt bởi mép ảnh: box dừng đúng tại mép ảnh; không suy đoán phần nằm ngoài khung.
- Xe quá nhỏ, quá mờ hoặc chỉ còn một mảnh không đủ nhận diện: không đoán class; ghi mã ảnh để hỏi giảng viên.
- Xe đang đỗ vẫn được gán nhãn theo loại xe.
- Không gán người đi bộ, xe đạp, xe máy/mô tô, biển báo, đèn giao thông, hình xe trên quảng cáo hoặc phản chiếu không phải xe thật trong cảnh.
- Hai xe chồng lên nhau vẫn có hai box riêng nếu có thể tách phần nhìn thấy của từng xe.

## 3. Ba tình huống cần áp dụng nhất quán

| Ảnh | Tình huống | Cách xử lý đề xuất | Lý do |
|---|---|---|---|
| drive_010.jpg | Cảnh ban đêm; có xe nhỏ hoặc tối ở xa. | Chỉ gán xe và class khi còn đủ đặc điểm nhận diện. Nếu không chắc, ghi case ID và hỏi, không đoán. | Ảnh tối và đối tượng nhỏ làm ranh giới giữa xe thật với bóng/nhiễu khó thấy. |
| drive_025.jpg | Xe buýt ở làn phía trên; một xe con bị cắt ở mép dưới ảnh. | Nếu kiểu xe buýt rõ thì gán bus; box xe con chỉ bao phần nhìn thấy và dừng tại mép ảnh. | Áp dụng class theo hình dáng xe và quy tắc vật thể bị cắt khung. |
| drive_050.jpg | Giao thông đông; xe buýt ở góc dưới trái bị cắt, nhiều xe con sát nhau. | Giữ một box riêng cho bus và từng car; cắt box bus tại biên dưới. Nếu không tách được xe khỏi nền/xe khác thì ghi lại để hỏi. | Không gộp xe; chỉ bao phần nhìn thấy và không suy diễn vị trí nằm ngoài ảnh. |

## 4. Điểm cần hỏi giảng viên

- Tiêu chí cụ thể để phân biệt van/minivan với car và bus trong góc nhìn xa, bị che hoặc thiếu góc cạnh.
- Quy tắc lớp cho các mẫu xe bán tải hoặc xe chuyên dụng nếu hình dáng không khớp rõ với bốn lớp.
- Mức độ nhỏ/che khuất nào thì nên bỏ qua và ghi case thay vì gán nhãn.

## 5. Xác nhận trước khi nộp

Trước khi nộp, hãy mở lại ba ảnh và đối chiếu box, nhãn trong CVAT. Nếu cách xử lý chưa khớp với quy ước của lớp, hãy sửa guideline. Đồng thời, ghi lại ít nhất 10 ảnh đã tự kiểm tra. Gợi ý của Codex không thay cho bước tự rà này.