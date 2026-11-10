# Báo cáo cá nhân — Ngày 2 Object Detection

> Báo cáo được tổng hợp từ các tệp trong repo, artifacts Colab đã lưu và lần chạy checker/gold evaluation khi hoàn thiện báo cáo. Những bước không có bằng chứng được ghi rõ là chưa xác nhận; phần AI hỗ trợ được nêu tại mục 8.

## 1. Thông tin học viên

- Họ tên: Nguyễn Anh Tiến
- MSSV: 2A202603120
- Email: Chưa có trong tài nguyên hiện tại.
- Lớp: K5-L2A
- Repo bài làm: [K5-L2A-DAY02-NguyenAnhTien-2A202603120-ObjectDetection](https://github.com/tiennguyeniotd22-ops/K5-L2A-DAY02-NguyenAnhTien-2A202603120-ObjectDetection)
- Colab: [Notebook đã chạy](https://colab.research.google.com/drive/10ExS0mPWyx_5ilpIih_YQAQs-ASKPdoj)

## 2. Schema và mini guideline

Mini guideline nằm tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ đúng thứ tự ID trong [data.yaml](../../data.yaml): 0 = car, 1 = truck, 2 = bus, 3 = van.

Ba tình huống được đưa vào guideline:

1. drive_010: ảnh ban đêm có xe nhỏ/tối ở xa. Chỉ gán class khi còn đủ dấu hiệu; nếu không phân biệt chắc thì ghi mã ảnh và hỏi, không đoán.
2. drive_025: xe buýt ở làn trên và xe con bị cắt ở mép dưới. Dùng bus khi kiểu thân xe đủ rõ; box xe bị cắt kết thúc đúng mép ảnh.
3. drive_050: xe buýt bị cắt ở góc dưới trái và nhiều xe con sát nhau. Vẽ box riêng cho từng xe, không gộp; box xe buýt dừng ở biên ảnh.

Đây là các ví dụ AI hỗ trợ đọc ảnh, chưa phải xác nhận rằng học viên đã tự rà nhãn hoặc chốt quyết định trong CVAT. Cần hỏi giảng viên khi không rõ ranh giới van/minivan với car hoặc bus, hay khi xe bị che/nhỏ đến mức không thể xác định class.

## 3. Annotation và self-QC

- Số file nhãn train: 50/50 file .txt trực tiếp trong dataset/labels/train, khớp với 50 ảnh train.
- Checker: [check_yolo_labels.py](../../tools/check_yolo_labels.py) chạy đạt cấu trúc 50/50, không phát hiện lỗi định dạng hoặc box vượt biên. Checker chỉ xác nhận cấu trúc/toạ độ, không chứng minh mọi xe đã được gán đúng.
- Tổng số box: 593 — car 503 (84,8%), truck 18 (3,0%), bus 19 (3,2%), van 53 (8,9%). Bộ nhãn mất cân bằng rõ rệt về car.
- Danh sách ít nhất 10 ảnh đã được học viên tự rà bằng mắt: chưa có nhật ký trong tài nguyên hiện tại. AI đã xem drive_010, drive_025 và drive_050 để soạn ví dụ guideline; việc đó không thay cho yêu cầu học viên tự kiểm tra tối thiểu 10 ảnh.
- Lỗi được phát hiện/sửa trong lượt tự rà: chưa có ghi chép đủ để xác nhận. FP/FN của gold evaluation là tín hiệu cần mở ảnh đối chiếu, nhưng chỉ số tổng hợp không chỉ ra chính xác box nào sai hoặc nguyên nhân.
- Commit khóa nhãn ban đầu: chưa xác minh được. Bản thư mục hiện tại không có thư mục .git nên không thể kiểm tra lịch sử/hash commit.

## 4. Gold QC và rework

Evaluator ghép box cùng class tại IoU ≥ 0,50. Lần chạy hiện tại dùng ngưỡng F1 ≥ 0,50 và mean IoU ≥ 0,70; kết quả trùng với [annotation_evaluation.json](../../outputs/annotation_evaluation.json).

| Chỉ số | Lần chấm hiện có | Sau rework |
|---|---:|---|
| TP | 403 | Chưa có kết quả sau rework |
| FP | 190 | Chưa có kết quả sau rework |
| FN | 250 | Chưa có kết quả sau rework |
| Precision | 0,6796 | Chưa có kết quả sau rework |
| Recall | 0,6172 | Chưa có kết quả sau rework |
| F1 | 0,6469 | Chưa có kết quả sau rework |
| mean_iou_of_matches | 0,8372 | Chưa có kết quả sau rework |
| passed | true theo ngưỡng 0,50/0,70 | Chưa có kết quả sau rework |

Theo xác nhận của học viên, giáo viên đã đổi ngưỡng F1 tối thiểu thành 0,50; vì vậy lần chấm này đạt cả F1 và mean IoU theo ngưỡng mới. Các bản GUIDE.md và RUBRIC.md trong thư mục vẫn ghi F1 ≥ 0,80; báo cáo áp dụng thông tin mới hơn do học viên xác nhận. Chưa có artifact chứng minh một vòng rework sau lần chấm này. Ví dụ, drive_001 có 10 box học viên so với 5 box gold và 5 cặp khớp; drive_050 có 10 box học viên so với 18 box gold và 6 cặp khớp. Các chênh lệch này cần được kiểm tra trực tiếp trên ảnh, không đủ để tự kết luận box sai nào.

## 5. Kết quả YOLO

- Nền tảng: Google Colab; artifacts ghi device 0. Tên GPU cụ thể không được lưu.
- Phiên bản Ultralytics và PyTorch: không có trong artifacts; notebook cài Ultralytics bản mới nhất tại thời điểm chạy.
- Cấu hình từ args.yaml: yolo26n.pt, 50 epochs, imgsz 640, batch -1, seed 20260909, device 0.
- Best checkpoint được ghi tại /content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt; file checkpoint .pt không có trong assets bài nộp.
- Tập eval gồm 10 ảnh. Notebook dùng tập này làm validation để theo dõi và chọn best.pt, nên đây không phải test set độc lập.

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0,6593 |
| Recall | 0,3277 |
| mAP50 | 0,3702 |
| mAP50–95 | 0,2701 |

Minh chứng: [metrics](assets/model_eval_metrics.json), [log theo epoch](assets/yolo26_runs/vehicle_annotation/results.csv), [confusion matrix chuẩn hóa](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png), [dự đoán drive_058](assets/eval_predictions/visuals/drive_058.jpg), [10 ảnh eval](assets/eval_predictions/visuals/), [log và biểu đồ train/eval](assets/yolo26_runs/).

### Phân tích bốn câu hỏi trong notebook

1. **Lớp có recall thấp nhất:** bus, xấp xỉ 0,00 trên confusion matrix chuẩn hóa. Tập eval chỉ có hai xe bus nên số liệu lớp này còn biến động mạnh. drive_058 là ví dụ: xe bus nhỏ sát mép phải bị model nhận nhầm thành car trong ảnh dự đoán.
2. **Nếu mAP50 cao hơn nhiều so với mAP50–95:** cần kiểm tra độ sát của cạnh box với xe. IoU 0,50 chấp nhận box lệch nhiều hơn; các ngưỡng IoU cao trong mAP50–95 làm lộ box quá rộng/hẹp, lệch vị trí hoặc không bám sát phần xe nhìn thấy. Ở kết quả hiện tại, mAP50 là 0,3702 và mAP50–95 là 0,2701; cả hai còn thấp.
3. **Lỗi class liên quan guideline:** drive_058 cho thấy một bus bị dự đoán là car. Confusion matrix cũng cho thấy model không nhận đúng class bus trong lượt eval này. Đây là lỗi của model, không tự chứng minh nhãn gold sai. Train có 19 box bus và 18 box truck so với 503 box car, nên mất cân bằng dữ liệu là yếu tố cần xem xét cùng chất lượng dữ liệu.
4. **Vì sao không dùng eval để train:** giữ eval ngoài dữ liệu cập nhật trọng số giúp tránh rò rỉ và giữ phép đánh giá có ý nghĩa. Notebook đặt eval làm validation để theo dõi và chọn best checkpoint; do đó kết quả này là validation metric, không phải kết quả test độc lập.

## 6. Khó khăn và cách xử lý

| Bước | Lỗi/tình huống | Cách xử lý | Kết quả/còn thiếu |
|---|---|---|---|
| Đồng bộ nhãn lên Colab | Bản ghi trong báo cáo hiện có nêu notebook từng báo thiếu nhãn train vì các file export chưa ở dataset/labels/train theo cấu trúc yêu cầu. | Chép 50 file nhãn vào đúng thư mục train của repo trước khi đồng bộ lại. | Thư mục hiện có đủ 50 file trực tiếp; artifacts lưu được kết quả train 50 epochs và dự đoán trên 10 ảnh eval. Không có log Colab đầy đủ để xác minh thêm chi tiết lỗi. |
| Tự rà nhãn/khóa nhãn | Chưa có danh sách 10 ảnh tự rà hoặc bằng chứng commit khóa trong tài nguyên hiện tại. | Cần học viên tự mở ảnh và nhãn, ghi ảnh đã rà và xác nhận commit ban đầu trên repo cá nhân. | Chưa thể đánh dấu hoàn tất chỉ từ checker hoặc thư mục đã giải nén. |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Bằng chứng và trạng thái |
|---|---|
| Định dạng YOLO, đủ 50 file | Đạt cấu trúc theo checker 50/50. |
| Đầy đủ vật thể, box và phân lớp | Lần gold hiện có F1 0,6469, mean IoU 0,8372; đạt theo ngưỡng F1 ≥ 0,50 đã được học viên xác nhận. Còn 190 FP và 250 FN để xem lại; chưa có kết quả rework. |
| Mini guideline và quyết định mơ hồ | Có ba case và quy tắc nền trong GUIDELINE_MINI.md; người học cần tự xác nhận các quyết định sau khi rà ảnh. |
| Gold QC | Có kết quả và vượt ngưỡng mới. Chưa có vòng rework sau QC. |
| Pipeline YOLO và phân tích | Có log train, metrics và 10 ảnh eval; đã trả lời bốn câu hỏi. mAP/recall còn thấp; eval đã được dùng làm validation. |
| Self-QC ít nhất 10 ảnh và commit khóa nhãn | Chưa có bằng chứng trong thư mục hiện tại; cần bổ sung bằng hoạt động thực tế. |

## 8. Công cụ AI và tài liệu tham khảo

- Codex hỗ trợ đọc cấu trúc repo và artifacts, đếm box theo class, xem ba ảnh mẫu và confusion matrix, chạy check_yolo_labels.py, chạy evaluate_annotations.py với ngưỡng F1 0,50/mean IoU 0,70, rồi soạn báo cáo và mini guideline.
- Lần gold evaluation khi hoàn thiện báo cáo được ghi vào file tạm để đối chiếu; outputs/annotation_evaluation.json hiện có được giữ nguyên. Gold labels không được chép vào báo cáo hay đưa vào artifacts nộp.
- Học viên cần tự kiểm tra lại nhãn và ba quyết định guideline trước khi nộp; phần AI hỗ trợ không được coi là bằng chứng cho thao tác cá nhân.
- Tài liệu trong repo: [GUIDE.md](../../GUIDE.md), [RULES.md](../../RULES.md), [RUBRIC.md](../../RUBRIC.md), [SUBMISSION.md](../../SUBMISSION.md), [notebook bài lab](../../notebooks/day2_annotation_to_yolo.ipynb), [script checker](../../tools/check_yolo_labels.py), [script gold evaluation](../../tools/evaluate_annotations.py).
- Repo GitHub đã được xác nhận mở được và nhánh main tồn tại. Link Colab lấy từ báo cáo cá nhân có sẵn; quyền truy cập và trạng thái runtime của notebook chưa được xác minh.