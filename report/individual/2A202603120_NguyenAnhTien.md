# Báo cáo cá nhân — Ngày 2 Object Detection

> Báo cáo này dựa trên nhãn, kết quả lưu từ Colab và các lần kiểm tra đã thực hiện. Những việc chưa có bằng chứng sẽ được ghi rõ; việc sử dụng AI được trình bày ở mục 8.

## 1. Thông tin học viên

- Họ tên: Nguyễn Anh Tiến
- MSSV: 2A202603120
- Email: Chưa tìm thấy địa chỉ email trong thư mục bài làm.
- Lớp: K5-L2A
- Repo bài làm: [K5-L2A-DAY02-NguyenAnhTien-2A202603120-ObjectDetection](https://github.com/tiennguyeniotd22-ops/K5-L2A-DAY02-NguyenAnhTien-2A202603120-ObjectDetection)
- Colab: [Notebook đã chạy](https://colab.research.google.com/drive/10ExS0mPWyx_5ilpIih_YQAQs-ASKPdoj)

## 2. Schema và mini guideline

Mini guideline nằm tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ đúng thứ tự ID trong [data.yaml](../../data.yaml): 0 = car, 1 = truck, 2 = bus, 3 = van.

Ba tình huống được đưa vào guideline:

1. drive_010: ảnh ban đêm có xe nhỏ/tối ở xa. Chỉ gán class khi còn đủ dấu hiệu; nếu không phân biệt chắc thì ghi mã ảnh và hỏi, không đoán.
2. drive_025: xe buýt ở làn trên và xe con bị cắt ở mép dưới. Dùng bus khi kiểu thân xe đủ rõ; box xe bị cắt kết thúc đúng mép ảnh.
3. drive_050: xe buýt bị cắt ở góc dưới trái và nhiều xe con sát nhau. Vẽ box riêng cho từng xe, không gộp; box xe buýt dừng ở biên ảnh.


## 3. Annotation và self-QC

- Số file nhãn train: 50/50 file .txt trực tiếp trong dataset/labels/train, khớp với 50 ảnh train.
- Checker: [check_yolo_labels.py](../../tools/check_yolo_labels.py) chạy đạt cấu trúc 50/50, không phát hiện lỗi định dạng hoặc box vượt biên. Checker chỉ xác nhận cấu trúc/toạ độ, không chứng minh mọi xe đã được gán đúng.
- Tổng số box: 593 — car 503 (84,8%), truck 18 (3,0%), bus 19 (3,2%), van 53 (8,9%). Bộ nhãn mất cân bằng rõ rệt về car.
- Danh sách ít nhất 10 ảnh đã tự kiểm tra: chưa tìm thấy trong thư mục bài làm. Codex đã xem drive_010, drive_025 và drive_050 để gợi ý ví dụ cho guideline; việc đó không thay cho yêu cầu tự rà ít nhất 10 ảnh.
- Lỗi phát hiện và cách sửa: chưa có ghi chép để nêu cụ thể. Các chỉ số FP/FN cho biết cần xem lại nhãn, nhưng phải mở ảnh để xác định box nào sai và vì sao.
- Commit khóa nhãn ban đầu: chưa kiểm tra được vì thư mục D: là bản giải nén, không chứa lịch sử Git.

## 4. Gold QC và rework

Evaluator ghép box cùng class tại IoU ≥ 0,50. Lần chạy hiện tại dùng ngưỡng F1 ≥ 0,50 và mean IoU ≥ 0,70; kết quả trùng với [annotation_evaluation.json](../../outputs/annotation_evaluation.json).

| Chỉ số | Kết quả hiện tại | Sau khi sửa nhãn |
|---|---:|---|
| TP | 403 | Chưa có kết quả sau rework |
| FP | 190 | Chưa có kết quả sau rework |
| FN | 250 | Chưa có kết quả sau rework |
| Precision | 0,6796 | Chưa có kết quả sau rework |
| Recall | 0,6172 | Chưa có kết quả sau rework |
| F1 | 0,6469 | Chưa có kết quả sau rework |
| mean_iou_of_matches | 0,8372 | Chưa có kết quả sau rework |
| passed | true theo ngưỡng 0,50/0,70 | Chưa có kết quả sau rework |

Theo thông tin em cung cấp, giáo viên đã đổi ngưỡng F1 tối thiểu xuống 0,50. Với ngưỡng mới, F1 0,6469 và mean IoU 0,8372 đều đạt yêu cầu. GUIDE.md và RUBRIC.md trong thư mục vẫn ghi F1 ≥ 0,80; báo cáo này dùng mức mới mà em đã xác nhận. Chưa có kết quả của lần chấm sau khi sửa nhãn. Ví dụ, drive_001 có 10 box của học viên và 5 box gold, khớp 5 cặp; drive_050 có 10 box của học viên và 18 box gold, khớp 6 cặp. Cần mở từng ảnh để tìm nguyên nhân; các con số này chưa cho biết chính xác box nào sai.

## 5. Kết quả YOLO

- Nền tảng: Google Colab. Tệp cấu hình cho thấy lần train dùng device 0, nhưng không ghi tên GPU cụ thể.
- Phiên bản Ultralytics và PyTorch: không tìm thấy trong các tệp kết quả. Notebook cài bản Ultralytics mới nhất tại thời điểm chạy.
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

1. **Lớp có recall thấp nhất:** bus (xe buýt), gần 0,00 theo confusion matrix chuẩn hóa. Tập eval chỉ có hai xe buýt nên kết quả này dễ biến động. Trong ảnh drive_058, xe buýt nhỏ gần mép phải bị mô hình nhận nhầm thành car.
2. **Nếu mAP50 cao hơn nhiều so với mAP50–95:** cần kiểm tra độ sát của cạnh box với xe. IoU 0,50 chấp nhận box lệch nhiều hơn; các ngưỡng IoU cao trong mAP50–95 làm lộ box quá rộng/hẹp, lệch vị trí hoặc không bám sát phần xe nhìn thấy. Ở kết quả hiện tại, mAP50 là 0,3702 và mAP50–95 là 0,2701; cả hai còn thấp.
3. **Lỗi phân lớp liên quan đến guideline:** trong drive_058, mô hình nhận nhầm một xe buýt thành car. Confusion matrix cũng cho thấy model không nhận đúng xe buýt trong lượt eval này. Đây là lỗi dự đoán của mô hình, chưa phải bằng chứng nhãn gold sai. Tập train có 19 box bus và 18 box truck, so với 503 box car; vì vậy cần xem xét cả mất cân bằng lớp lẫn chất lượng nhãn.
4. **Vì sao không dùng eval để train:** không đưa ảnh eval vào quá trình cập nhật trọng số giúp tránh rò rỉ dữ liệu và giữ kết quả đánh giá có ý nghĩa. Notebook dùng eval làm tập validation để theo dõi và chọn checkpoint tốt nhất; vì vậy các chỉ số này không phải kết quả trên một tập test độc lập.

## 6. Khó khăn và cách xử lý

| Bước | Lỗi/tình huống | Cách xử lý | Kết quả/còn thiếu |
|---|---|---|---|
| Đồng bộ nhãn lên Colab | Khi đồng bộ lên Colab, notebook báo thiếu nhãn train vì các tệp export chưa nằm trong dataset/labels/train theo đúng cấu trúc. | Chép 50 file nhãn vào đúng thư mục train của repo trước khi đồng bộ lại. | Thư mục hiện có đủ 50 file nhãn. Kết quả Colab cho thấy mô hình đã train 50 epochs và tạo dự đoán cho 10 ảnh eval. Không còn log Colab đầy đủ để đối chiếu nguyên văn thông báo lỗi. |
| Tự rà nhãn và khóa nhãn | Chưa tìm thấy danh sách 10 ảnh đã xem hoặc commit ban đầu. | Tự mở ảnh và nhãn, ghi lại các ảnh đã rà rồi kiểm tra commit khóa trên repo. | Chưa thể xác nhận hai bước này chỉ dựa vào checker và thư mục hiện có. |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Bằng chứng và trạng thái |
|---|---|
| Định dạng YOLO, đủ 50 file | Đạt cấu trúc theo checker 50/50. |
| Đầy đủ vật thể, box và phân lớp | Kết quả đối chiếu nhãn đạt ngưỡng F1 mới: F1 0,6469, mean IoU 0,8372. Còn 190 FP và 250 FN cần xem lại; chưa có kết quả sau khi sửa nhãn. |
| Mini guideline và tình huống mơ hồ | Đã ghi ba tình huống và quy tắc cơ bản trong GUIDELINE_MINI.md. Em cần đối chiếu lại từng tình huống với ảnh và nhãn. |
| Đối chiếu với nhãn gold | Kết quả đạt ngưỡng F1 mới; chưa có kết quả sau khi sửa nhãn. |
| Huấn luyện YOLO và phân tích | Có log train, metrics và 10 ảnh eval; đã trả lời bốn câu hỏi. mAP/recall còn thấp; tập eval cũng được dùng để chọn checkpoint. |
| Tự rà ít nhất 10 ảnh và khóa nhãn | Chưa có danh sách ảnh đã rà hoặc thông tin commit khóa. Cần tự kiểm tra và lưu lại các thông tin này. |

## 8. Công cụ AI và tài liệu tham khảo

- Em đã dùng Codex để hỗ trợ báo cáo.
- Để giữ nguyên outputs/annotation_evaluation.json, lần kiểm tra lại được lưu vào một file tạm. Nhãn gold không được đưa vào báo cáo hoặc assets nộp bài.
- Trước khi nộp, em cần tự kiểm tra nhãn và xác nhận ba quyết định trong guideline. Các gợi ý của Codex không thay cho những bước tự làm này.
- Tài liệu trong repo: [GUIDE.md](../../GUIDE.md), [RULES.md](../../RULES.md), [RUBRIC.md](../../RUBRIC.md), [SUBMISSION.md](../../SUBMISSION.md), [notebook bài lab](../../notebooks/day2_annotation_to_yolo.ipynb), [script checker](../../tools/check_yolo_labels.py), [script gold evaluation](../../tools/evaluate_annotations.py).
- Repo GitHub đã mở được và nhánh main tồn tại. Link Colab lấy từ báo cáo trước; quyền xem và trạng thái chạy của notebook chưa được kiểm tra.
