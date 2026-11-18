# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603145 - Lê Văn Trung Tính

## 1. Câu hỏi mở đã chốt

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 1 | Xe đạp điện: bicycle hay motorbike? | Có động cơ, pin hoặc tay ga: `motorbike`; xe đạp thường đạp bằng chân: `bicycle`. | `img_020` | CAL-02 |

## 2. Ca khó mới

| Ảnh | Tình huống | Máy làm gì | Quyết định | Quy tắc mới |
|---|---|---|---|---|
| `img_013` | Tuk-tuk có mui và người lái/người ngồi bên trong | YOLO26/YOLOE có thể gọi nhầm car/truck hoặc bỏ sót người | Một `three_wheeler` cho xe; mỗi người nhìn thấy >=20% là `person` riêng | Bổ sung mục 2: phân biệt xe và người trên xe |
| `img_026` | Box máy hơi rộng, bẫy loose lọt | Box có điểm khá nhưng còn nền | Kiểm bốn cạnh trước khi accept; sửa nếu nền thừa | Bổ sung mục 4.1.1: conf không thay việc kiểm biên |
| `img_034` | Bẫy loose tương tự trong cảnh đông | Box gần đúng nhưng không ôm sát | Không giữ nguyên chỉ vì IoU cảm quan cao | Áp dụng cùng quy tắc kiểm biên |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Chốt xe có động cơ là `motorbike`; bổ sung cách tách tuk-tuk/người; bắt buộc kiểm biên box trước khi accept | CAL-01, CAL-02, CAL-03; ví dụ `img_013`, `img_020`, `img_026`, `img_034` |
