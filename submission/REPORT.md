# Báo cáo Day 08 — 2A202603145 - Lê Văn Trung Tính

## 1. Model và recipe (Part 2–3)

YOLO26m dùng cho `person`, `bicycle`, `motorbike`, `car`, `bus`, `truck`;
YOLOE-26s với prompt `three_wheeler` dùng cho xe ba bánh. Theo
`submission/qc/threshold_sweep.csv`, ngưỡng chọn được là `person=0.15`,
`car=0.25`; các lớp còn lại dùng mặc định 0.30 vì ít mẫu hoặc chưa đạt recall
90%. Đây là kết quả in-sample trên 4 ảnh gold nên lạc quan.

Các lần chạy nằm trong `submission/model_log.csv`: YOLO26m ở conf 0.25 và
0.50, YOLOE-26s, và YOLO26m + SAM 2.1 (`sam2.1_b.pt`). Ở conf 0.50 YOLO26m
mất 76 box so với conf 0.25, chủ yếu là person và motorbike. YOLOE tìm được
three_wheeler ở 3/8 ảnh pilot nhưng bỏ sót 5 ảnh. Đổi ngưỡng không sửa được
lỗi tuk-tuk bị gọi car/truck; cần prompt YOLOE và review theo số bánh.

Lệnh merge:
`python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "person=0.15,car=0.25" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json`

SAM 2.1 được chọn vì đã có trọng số local và tạo mask tốt hơn cho vật lớn;
trên máy này thao tác mask phải chạy CPU vì MPS báo lỗi `Unsupported Border
padding mode`. Lỗi box sai lớp/box rộng không được SAM sửa, vì SAM chỉ phân
đoạn theo prompt.

## 2. AI error profile

Trên 24 ảnh, có 620 box máy và 616 đối tượng sau duyệt: accepted 600/620
(96.8%), edited 6/620 (1.0%), deleted 14/620 (2.3%), added 10/616 (1.6%).
Có 4 trường hợp đổi lớp; vì vậy added còn khoảng 6 vật máy bỏ sót thật.
Lớp khó nhất là `three_wheeler`: 18 accepted, 4 edited, 4 deleted, 1 added.

So với gold 4 ảnh và 63 đối tượng, máy có TP=45, FP=30, FN=18:
precision 45/75 = 60.0%, recall 45/63 = 71.4%. Khoảng tin cậy 95% trong
`review_stats.md` lần lượt là 49–70% và 59–81%; mẫu chỉ 4 ảnh.

## 3. Thiên kiến tự động hóa và ảnh bẫy

Máy bắt 5/12 bẫy = 41.7%, dưới ngưỡng 10/12 = 80%. Bẫy lọt gồm wrong_class
ở `img_009`, loose ở `img_026` và `img_034`, missing ở `img_029` và
`img_022`, extra ở `img_005` và `img_003`. Accepted cao nhưng bắt bẫy thấp
cho thấy dễ nhìn điểm/conf và giữ box máy mà chưa quét ảnh độc lập.

Ba quy tắc giảm thiên kiến:
1. Quét toàn ảnh tìm vật trước khi xem nhãn máy, rồi mới kiểm từng box.
2. Với mỗi box, kiểm bốn cạnh và lớp; conf không được thay cho kiểm tra hình.
3. Ghi riêng số added, deleted và bẫy lọt; không kết luận máy tốt chỉ từ accepted.

## 4. Máy có đáng dùng không?

Gold mất 20/4 = 5.0 phút/ảnh; batch review mất 30/24 = 1.25 phút/ảnh.
Pre-label giúp nhanh hơn, nhưng recall gold chỉ 45/63 = 71.4% và bắt bẫy
5/12 = 41.7%, nên chỉ đáng dùng như bản nháp cho lớp phổ biến, không được
release tự động.

## 5. Mask SAM cho 3 vật

Đã tạo ba ảnh bằng SAM 2.1, mỗi ảnh dùng 2 click dương và 1 click âm:
`submission/masks/tuktuk_img013.jpg`, `tuktuk_img004.jpg`,
`motorbike_img018.jpg`. Với tuk-tuk, mask cần bao khung xe và mui nhưng
không bao người đứng ngoài; với motorbike, không bao người lái như một phần
của xe. Các điểm âm giúp tránh nền và vật liền kề. Mask vẫn cần kiểm tra
biên, đặc biệt vùng người lái/hàng hóa.

## 6. Calibration

Có 3 quyết định trong `submission/qc/calibration_decisions.csv`: CAL-01
chốt tuk-tuk là `three_wheeler` và người là box riêng; CAL-02 chốt xe có
động cơ/pin là `motorbike`; CAL-03 bổ sung bước kiểm bốn cạnh để bắt box
loose. Các quy tắc đã được ghi vào `guideline_v1.1_calibrated.md` với ảnh
ví dụ `img_013`, `img_020`, `img_026`, `img_034`.

## 7. QC và quyết định

Mẫu QC gồm 5/24 ảnh ngẫu nhiên, 124 đối tượng, seed 42. Phiếu kiểm ghi
0/124 = 0.0% lỗi theo đối tượng và 0/5 = 0.0% ảnh có lỗi. Tuy nhiên cận
trên Wilson 95% của tỷ lệ ảnh lỗi thật là 43%; mẫu 5 ảnh không đủ chứng minh
ngưỡng 2%.

**Quyết định: Rework**, vì bắt bẫy chỉ 5/12 = 41.7%, thấp hơn 10/12 = 80%.
Cần review lại các ảnh bẫy lọt, đặc biệt lỗi loose/missing/extra, rồi chạy
lại `review_stats.py`, `build_release.py` và QC trước khi tuyên bố Release.
Release candidate hiện có 24 ảnh và 630 đối tượng nhưng chưa được coi là
final do quyết định Rework.

## 8. Ghi chú cá nhân

Đã dùng Claude Code để đọc repo/guideline, dựng môi trường, chạy script và
kiểm tra cấu trúc; nhãn gold và review batch được thực hiện trên CVAT.
Gold được gán trước khi xem pre-label. Máy dùng macOS Apple M4, CVAT
v2.74.1, model chạy MPS; riêng SAM 2.1 phải chuyển CPU do lỗi tương thích
MPS. Lần sau sẽ dành thêm thời gian cho bước quét ảnh độc lập và review các
bẫy trước khi export. Tự chạy test từng model để phân biết sự khác nhau giữa đầu ra các model, chỉ số conf tác động thế nào, build tool tích hợp cvat để auto pre-label.
