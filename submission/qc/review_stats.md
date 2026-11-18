# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 620; đối tượng sau duyệt: 616; ngưỡng accepted: IoU >= 0.9
- Đã **loại 25 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 600 | 600/620 = 96.8% | 620 box máy |
| edited (sửa) | 6 | 6/620 = 1.0% | 620 box máy |
| deleted (xoá) | 14 | 14/620 = 2.3% | 620 box máy |
| added (vẽ thêm) | 10 | 10/616 = 1.6% | 616 đối tượng sau duyệt |

Trong số deleted + added, **4 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 6.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 369 | 2 | 4 | 3 |
| bicycle | 6 | 0 | 3 | 1 |
| motorbike | 138 | 0 | 0 | 1 |
| car | 62 | 0 | 0 | 2 |
| bus | 2 | 0 | 1 | 1 |
| truck | 5 | 0 | 2 | 1 |
| three_wheeler | 18 | 4 | 4 | 1 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 63 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 45 | 30 | 18 | 60.0% (KTC 95%: 49–70%) | 71.4% (KTC 95%: 59–81%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 5/12 = 41.7% (KTC 95%: 19–68%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - loose: 1/3
  - wrong_class: 2/3
  - missing: 1/3
  - extra: 1/3
  - lọt: img_009.jpg (wrong_class), img_026.jpg (loose), img_034.jpg (loose), img_029.jpg (missing), img_005.jpg (extra), img_003.jpg (extra), img_022.jpg (missing)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
