# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đặng Thu Hương
- MSSV: 2A202603047
- Email: dthuong241104@gmail.com
- Lớp: K5-L2A
- Link repo bài làm: https://github.com/emdua24-hub/K5-L2A-DAY02-DangThuHuong-2A202603047-ObjectDetection-
- Link Colab đã chạy: https://colab.research.google.com/drive/1SP12AlZiXV9Aw4pinA3P_AK-H-S6x2vX?usp=sharing

## 2. Schema và mini guideline

Chi tiết: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

Em gán nhãn theo schema bắt buộc và `data.yaml`: `0 car`, `1 truck`, `2 bus`, `3 van`. Lớp mô tả loại xe, không mô tả trạng thái, nên xe đang đỗ vẫn là `car`. Xe bị che hoặc bị cắt khung vẫn gán nếu phần nhìn thấy đủ để nhận ra lớp; box chỉ ôm phần nhìn thấy. Xe quá nhỏ hoặc mờ đến mức không xác định được lớp thì không gán.

Ba ca mơ hồ đã quyết định:
- `drive_005`: sedan bạc bị rìa phải cắt mất đầu xe, vẫn thấy cửa sau, bánh và cốp → gán `car`, box kéo sát đến rìa ảnh.
- `drive_006`: xe trắng ở mép trái chỉ còn phần đầu (capo cong, đèn pha) → gán `car`, vì capo thấp, cong không phải đầu phẳng của `van`/`bus` hay cabin vuông của `truck`.
- `drive_009`: xe con bị đầu xe buýt che và bị rìa phải cắt → gán `car`, box tách riêng, không gộp với xe buýt.

Phần cần giảng viên làm rõ:
- Gold train và nhãn eval có vẻ dùng thứ tự ID lớp khác `data.yaml` (xem mục 4 và mục 5).
- Gold không gán một số xe đang đỗ trong bãi (ví dụ dãy xe đỗ bên trái ở một số ảnh), trong khi schema ghi "xe đang đỗ vẫn là `car`".

## 3. Annotation và self-QC

- Số file nhãn train: 50/50 file trong `dataset/labels/train/`, cùng basename với ảnh.
- Kết quả checker: `python3 tools/check_yolo_labels.py` → "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ" (đủ 50 file, 5 cột, tọa độ chuẩn hóa, ID lớp trong khoảng 0–3).
- Ảnh đã mở lại để rà bằng mắt (trong CVAT): `drive_001`, `drive_002`, `drive_007`, `drive_009`, `drive_018`, `drive_021`, `drive_024`, `drive_027`, `drive_028`, `drive_045`. Tổng cộng 45/50 ảnh đã được chỉnh sửa trong vòng rà và rework.
- Lỗi phát hiện khi rà và cách sửa:
  - Box thừa ở xe rất nhỏ, ở xa hoặc mơ hồ → xóa theo quy tắc "vật thể rất nhỏ/mơ hồ" (ví dụ `drive_001`, `drive_018`, `drive_028`).
  - Bỏ sót xe ở ảnh đông, xe bị che một phần sau xe khác → thêm box (ví dụ `drive_024`, `drive_027`, `drive_045`).
  - Box gộp hoặc lệch giữa các xe sát nhau → tách thành box riêng cho từng xe và kéo sát mép (ví dụ `drive_007`, `drive_009`).
- Commit khóa nhãn ban đầu: không có commit khóa nhãn trước khi nhận gold. Nhãn được commit lần đầu ở `5f2bad3` (2026-11-10 05:55), sau khi đã nhận gold và rework, nên lịch sử git không lưu bản nhãn trước rework. Số liệu "trước sửa" ở mục 4 lấy từ lần chạy evaluator đầu tiên (output terminal đã lưu lại).

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa |
|---|---:|---:|
| TP | 405 | 481 |
| FP | 319 | 136 |
| FN | 248 | 172 |
| Precision | 0.5594 | 0.7796 |
| Recall | 0.6202 | 0.7366 |
| F1 | 0.5882 | 0.7575 |
| mean_iou_of_matches | 0.7909 | 0.8047 |
| passed | false | false |

Kết quả sau sửa: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json).

**Rework:** sửa nhãn trên 45/50 ảnh, theo hai hướng chính:
- Xóa box thừa ở xe rất nhỏ/xa hoặc mơ hồ: `drive_001` (14 → 5 box), `drive_018` (8 → 2), `drive_028` (9 → 3), `drive_021` (9 → 4), `drive_002` (27 → 13).
- Bổ sung xe bị sót ở ảnh đông xe, nhiều xe bị che khuất: số box khớp gold của `drive_024` tăng từ 12 lên 22, `drive_045` từ 6 lên 13, `drive_007` từ 6 lên 12, `drive_027` từ 15 lên 21.

**Phân tích phần chưa đạt và quyết định giữ:** để không suy nguyên nhân chỉ từ số FP/FN, em so khớp từng box với gold theo vị trí (IoU ≥ 0.5, không xét lớp). Kết quả: nhãn sau sửa khớp 603/653 xe của gold, chỉ thừa 14 box và sót 50 box (F1 không xét lớp ≈ 0.95). Phần chênh lệch còn lại là 116 box trùng vị trí nhưng lệch lớp theo một quy luật cố định: box `bus` của em ứng với gold ID 3, `van` ứng với gold ID 1, `truck` ứng với gold ID 2. Ví dụ ở `drive_009`, các xe buýt được gold ghi ID 3 và xe tải thùng màu xanh được ghi ID 2. Điều này cho thấy gold train có thể dùng thứ tự lớp khác `data.yaml` (`0 car, 1 truck, 2 bus, 3 van`).

Em giữ nhãn theo `data.yaml` và schema, không đổi ID cho khớp gold, vì đổi ID sẽ khiến model học xe buýt thành `van`. Em không sửa gold labels. Phần chưa đạt ngưỡng F1 0.80 cần giảng viên xác nhận về thứ tự lớp của gold.

## 5. Kết quả YOLO

- Runtime: Google Colab với GPU (`device: 0`; 50 epoch train hết khoảng 73 giây). Ultralytics được notebook cài bản mới nhất tại thời điểm chạy (2026-11-10); Torch là bản có sẵn của Colab.
- Tham số thay đổi so với notebook: không thay đổi. Dùng mặc định `yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1` (tự chọn), `seed=20260909` ([args.yaml](assets/yolo26_runs/vehicle_annotation/args.yaml)).
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không commit file `.pt`). `best.pt` được chọn ở **epoch 2/50**: mAP50-95 trên eval cao nhất ở epoch 2 (0.201), sau đó giảm còn 0.158 ở epoch 50 ([results.csv](assets/yolo26_runs/vehicle_annotation/results.csv)).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.316 |
| Recall | 0.307 |
| mAP50 | 0.274 |
| mAP50–95 | 0.201 |

AP50 theo lớp ([PR curve](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png)): `car` 0.738, `truck` 0.235, `van` 0.115, `bus` 0.008.

Kết quả: [ảnh dự đoán 10 ảnh eval](assets/eval_predictions/visuals/), [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [đường cong train](assets/yolo26_runs/vehicle_annotation/results.png), [metrics JSON](assets/model_eval_metrics.json), [log train đầy đủ](assets/yolo26_runs/).

**Số lượng theo lớp:** train có 492 `car`, 20 `truck`, 54 `bus`, 51 `van`; eval có 94 `car`, 5 `truck`, 2 `bus`, 12 `van`. `car` chiếm khoảng 80% ở cả hai tập.

**Trả lời câu hỏi cuối notebook:**

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
`bus` (AP50 0.008) và `van`. Theo confusion matrix, model không bắt đúng box `bus` nào trong 2 box (1 bị đoán thành `car`, 1 bị bỏ sót) và không bắt đúng `van` nào trong 12 box. Tuy nhiên, khi mở ảnh thì thấy nguyên nhân phần lớn nằm ở nhãn eval: ở [drive_058](assets/eval_predictions/visuals/drive_058.jpg), chiếc xe buýt vàng bên trái được nhãn eval ghi ID 3 (`van` theo `data.yaml`), còn model dự đoán đúng là `bus 0.69`. Trường hợp này bị tính là vừa FN `van` vừa FP `bus`. Confusion matrix cho thấy 11/12 box "van" của eval bị đoán thành `bus`. Một lỗi thật của model: ở [drive_051](assets/eval_predictions/visuals/drive_051.jpg), xe sedan đỏ ở góc dưới bị đoán là `van 0.99`, và xe màu trắng phía trên bên phải (nhãn eval ID 2) không được phát hiện.

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
mAP50-95 tính trung bình ở các ngưỡng IoU khắt khe hơn (0.5 đến 0.95), nên nếu nó tụt xa so với mAP50 thì box dự đoán đã trúng xe nhưng không sát: lỏng, lệch cạnh, cắt thiếu, hoặc gộp hai xe. Cần kiểm tra độ sát của box trong nhãn train (box lỏng thì model học box lỏng) và box dự đoán trên ảnh eval. Ở bài này, mAP50 (0.274) và mAP50-95 (0.201) chênh lệch không lớn, và mean IoU của nhãn train so với gold là 0.80. Vì vậy vấn đề chính là phân lớp, không phải độ sát của box.

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
Lỗi chính là `bus`/`van`/`truck`, và nó liên quan đến **thứ tự ID lớp** giữa nhãn của em (theo `data.yaml`: 0 car, 1 truck, 2 bus, 3 van) và gold/eval. Dữ kiện: (a) ở gold QC, 116 box của em trùng vị trí với gold nhưng lệch lớp theo một quy luật cố định (`bus` của em ↔ gold ID 3, `van` ↔ ID 1, `truck` ↔ ID 2); (b) ở eval, 11/12 box ID 3 bị model đoán thành `bus`, và kiểm tra `drive_058` cho thấy ID 3 ứng với xe buýt. Ngoài ra còn lỗi ở ranh giới `car`/`van`: ở `drive_051`, sedan đỏ bị đoán là `van`. Trong train, `van` (51) và `bus` (54) quá ít so với `car` (492), nên model khó học được ranh giới giữa các lớp này.

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
Nếu đưa ảnh eval vào train, model sẽ được "xem đề trước" (data leakage), và điểm trên eval không còn phản ánh khả năng với ảnh mới. Trong notebook, 10 ảnh eval được dùng làm validation sau mỗi epoch để chọn `best.pt` (ở đây là epoch 2). Do eval đã tham gia vào việc chọn model, kết quả trên eval có phần lạc quan và không phải test set độc lập. Việc `best.pt` rơi vào epoch 2 rồi mAP giảm dần có thể do model càng học kỹ cách đặt lớp theo nhãn train thì càng lệch với thứ tự lớp của nhãn eval, cộng thêm việc overfit trên chỉ 50 ảnh.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Mở lại CVAT sau khi khởi động máy | Không vào được `localhost:8080` dù các container cvat hiện đang chạy | Kiểm tra trong Docker Desktop, bật lại các container chưa chạy | CVAT chạy lại được, tiếp tục gán nhãn |
| Chạy evaluator trên PowerShell | Lỗi `Missing expression after unary operator '--'` | Dấu `\` xuống dòng chỉ dùng cho bash; viết lệnh trên một dòng | Chạy được |
| Chạy evaluator lần đầu | "Không có gold labels trong gold_train_labels" | Chép thư mục gold cùng cấp với `dataset/` | Chạy được |
| Gold QC | F1 0.59, chưa đạt | Rework 45 ảnh; so vị trí box không xét lớp để tìm nguyên nhân | F1 0.76; phát hiện lệch thứ tự ID lớp giữa gold và `data.yaml`, cần giảng viên xác nhận |
| Khóa nhãn | Chưa commit nhãn trước khi nhận gold | Commit sau rework (`5f2bad3`) | Thiếu bản khóa nhãn ban đầu trong lịch sử git |
| Train YOLO | mAP thấp, `bus`/`van` gần như bằng 0 | Mở ảnh eval và confusion matrix để tìm nguyên nhân | Phát hiện nhãn eval có cùng kiểu lệch thứ tự lớp như gold train |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Tình trạng |
|---|---|
| Hoàn thành và định dạng YOLO (10) | Đạt: 50/50 file, checker ĐẠT cấu trúc. |
| Đầy đủ vật thể (20) | Tốt: so vị trí với gold khớp 603/653 xe, sót 50. |
| Độ bám sát box (20) | Tốt: mean IoU 0.80. |
| Phân lớp nhất quán (15) | Nhất quán theo `data.yaml`/schema; lệch thứ tự ID với gold/eval, cần giảng viên xác nhận. |
| Mini guideline (10) | Đủ định nghĩa 4 lớp, 4 quy tắc, 3 ca mơ hồ có lý do. |
| Gold-set QC (15) | Chưa đạt F1 0.80 (0.76 sau rework); có vòng rework và giải thích nguyên nhân lệch lớp bằng dữ kiện. |
| Pipeline YOLO (10) | Colab chạy hết, không dùng eval để train, có metrics, ảnh eval và phân tích lỗi trên ảnh cụ thể. |

Còn thiếu: commit khóa nhãn trước khi nhận gold; xác nhận của giảng viên về thứ tự lớp của gold/eval.

## 8. Công cụ AI và tài liệu tham khảo

- Claude (Anthropic): hỗ trợ khởi động lại Docker/CVAT, gợi ý nội dung mini guideline (em đã đối chiếu với schema và chỉnh theo ảnh thực tế), sửa lệnh PowerShell, phân tích kết quả gold QC (so khớp box theo vị trí, phát hiện lệch thứ tự ID lớp), hướng dẫn mở notebook trên Colab, phân tích kết quả model và soạn báo cáo. Em tự gán nhãn và rework trong CVAT, tự chạy checker, evaluator và notebook, và kiểm tra lại các số liệu trong báo cáo.
- Tài liệu: README, GUIDE, RUBRIC, RULES, SUBMISSION trong repo; tài liệu Ultralytics YOLO.
- Không dùng gold train trước khi nhận theo mốc lớp; không đính kèm gold train trong repo hay báo cáo.