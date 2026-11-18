# Báo cáo Day 08 — 2A202603145 - Lê Văn Trung Tính

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
|---|---|---|---|
| person / bicycle / motorbike / car / bus / truck | <<YOLO26m>> | <<theo lớp, từ threshold_sweep>> | <<>> |
| three_wheeler | <<YOLOE-26s, prompt: ...>> | <<>> | <<>> |

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): <<>>
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: <<chất lượng mask, tốc độ, RAM, quyền truy cập>>
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? <<>>
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): <<>>
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: <<>>

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted | <<>> | <<>> | <<box máy>> |
| edited | <<>> | <<>> | <<>> |
| deleted | <<>> | <<>> | <<>> |
| added | <<>> | <<>> | <<đối tượng sau duyệt>> |

- Lớp nào máy tệ nhất? Vì sao (dẫn ảnh)? <<>>
- So với gold: precision / recall của **máy** <<>> (mẫu số: <<đối tượng gold>>)

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt bẫy: <<.../12>>; loại nào lọt: <<>>; vì sao (bạn đã bỏ bước nào của checklist)? <<>>
- Accepted cao mà bắt bẫy thấp (hoặc ngược lại) nói gì về cách bạn duyệt? <<>>

## 4. Máy có đáng dùng không?

- Thời gian (từ `time_log.csv`): gán tay <<... phút/ảnh>> vs duyệt <<... phút/ảnh>>. Thiên lệch (bạn gán gold trước nên đã quen cảnh): <<>>
- Kết luận: <<có / không / chỉ cho lớp nào>>, dựa trên <<số liệu>>
- **Ba quy tắc giảm thiên kiến của bạn** (mỗi quy tắc kiểm tra được bằng số hoặc việc làm):
  1. <<>>
  2. <<>>
  3. <<>>

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
|---|---|---|---|---|---|
| <<img_0xx>> | <<>> | <<>> | <<>> | <<>> | <<>> |

- Đối chiếu guideline mục 4.2: quy tắc nào giúp, quy tắc nào thiếu? <<>>
- *(Tuỳ chọn)* Interactor SAM trong CVAT: làm được / không (lỗi gì, đã thử gì); khác gì offline: <<>>

## 6. Calibration (Part 6)

- Số quyết định: <<>> (`calibration_decisions.csv`); quan trọng nhất (dẫn `CAL-xx`): <<>>
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: <<>>
- Nguyên nhân gốc: <<guideline mơ hồ / chưa nắm quy tắc / máy>>

## 7. QC và quyết định (Part 6)

- Mẫu: <<n>> ảnh (<<m>> ảnh `can_review` + <<k>> ngẫu nhiên, seed 42); người kiểm: bạn (**hạn chế**: bạn vừa là người duyệt).
- Lỗi theo **đối tượng**: <<.../... = ...%>> · theo **ảnh**: <<.../... = ...%>> (ngưỡng Release ≤ 2%)
- Top lỗi (trong: bỏ sót, thừa, sai lớp, sai vị trí/kích thước, sai thuộc tính, sai mask) + một ảnh ví dụ mỗi loại: <<>>
- **Giới hạn mẫu nhỏ** (cận trên 95% trong `qc_report_auto.md`): <<>>
- Script kiểm được / không kiểm được (ví dụ ôm sát, cùng bỏ sót với máy, theo checklist thật): <<>>

**Quyết định: <<Release / Hold / Rework>>**, ngày <<YYYY-MM-DD>>. Lý do (đi theo Hold → Rework → Release): <<>>
Nếu Rework / Hold, việc cần làm tiếp: <<>>

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): Claude Code (Anthropic). Phạm vi: giải thích khái niệm, đọc repo và guideline; dựng venv, tải model, nối git remote; tạo task CVAT `gold-manual` (#27) và `batch-review` (#28) rồi upload ảnh qua API; export COCO của gold qua API và chép vào `submission/labels/gold_manual.json`; chạy `validate_labels.py`. Kiểm tra: số ảnh và tên frame trong CVAT khớp thư mục `work/tasks/`; task `gold-manual` chỉ có box vẽ tay. AI không gán nhãn, không nhìn ảnh gold, không chạy model lên ảnh gold. Nhãn, số đo và kết luận do tôi tự làm.
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? Không. Task `gold-manual` chỉ có box vẽ tay (CVAT ghi `source=manual` cho cả 67 box), không dùng Automatic annotation hay AI Tools, không chạy model và không xem pre-label của 4 ảnh gold trước khi export `gold_manual.json`. Có nhờ AI tóm tắt **văn bản** guideline v1.0 trước khi gán; không hỏi AI gì về nội dung ảnh.
- Ca `can_review` trong gold (3 box, nguyên liệu cho calibration ở Part 6):
  - `img_020`, 1 box `person`: <<không chắc quy tắc nào>>
  - `img_020`, 1 box `three_wheeler`: <<không chắc quy tắc nào>>
  - `img_036`, 1 box `person`: <<không chắc quy tắc nào>>
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: macOS, Apple M4, model chạy trên MPS; CVAT v2.74.1 chạy Docker ở cổng 8081 (giữ 2.74.1 thay vì 2.81.0 của hướng dẫn vì dùng chung dữ liệu với các lab khác). Vướng mắc: 17 container CVAT bị dừng (Exited 137) từ ngày 17/11, bật lại bằng `docker compose start`; ngay sau khi bật, trang Models báo lỗi 500 vài giây vì bộ kiểm tra quyền (OPA) chưa nạp xong, sau đó tự hết.
- Dùng cache `data/cache/` thay vì tự chạy? <<không / có, vì ...>>
- Lỗi / bug script đã gặp (nếu có): <<>>
- Một điều bạn làm khác lần sau: <<>>
