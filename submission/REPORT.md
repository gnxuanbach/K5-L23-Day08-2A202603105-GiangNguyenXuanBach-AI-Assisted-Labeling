# Báo cáo Day 08 — MSHV - Họ và tên

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp | Model + cỡ | Ngưỡng | Vì sao (dẫn `run_id` trong `model_log.csv`) |
| --- | --- | --- | --- |
| person / bicycle / motorbike / car / bus / truck | YOLO26m | theo lớp, từ threshold_sweep | r01, r03, r04: Bắt tốt phương tiện phổ biến |
| three_wheeler | YOLOE-26s, prompt: three_wheeler | 0.25 - 0.4 | r02, r05: Nhận diện chính xác tuk-tuk và xe ba bánh mà YOLO26 bỏ sót |

- Lệnh `merge_prelabels.py` đã dùng (dán nguyên): python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "person=0.35,motorbike=0.5,car=0.35,three_wheeler=0.4" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json
- **Phiên bản SAM bạn chọn** (SAM 1 / 2.1 / 3) và lý do: sam2.1_b.pt (SAM 2.1 base). Lý do: Cho mask bám biên nét, cân bằng tốt giữa tốc độ xử lý và bộ nhớ GPU T4 trên Colab.
- Lỗi nào **không sửa được bằng đổi ngưỡng** (ví dụ tuk-tuk bị gọi `car`)? Sửa thế nào? Lỗi tuk-tuk bị gọi là car. Cần model chuyên biệt hoặc can thiệp bằng tay trên CVAT
- Box **điểm cao mà sai** hoặc **điểm thấp mà đúng** (ảnh nào): Box điểm cao mà sai: bóng đổ của ô tô bị nhận nhầm thành car (img_006). Box điểm thấp mà đúng: người đi bộ ở xa bị che khuất conf chỉ 0.28 nhưng vẫn đúng là person (img_018)
- Giới hạn: chọn ngưỡng trên chính gold (4 ảnh) nên số đo máy trên gold là lạc quan: Đúng, lạc quan hơn thực tế với toàn bộ tập dữ liệu

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái | Số | Tỉ lệ | Mẫu số |
| --- | --- | --- | --- |
| accepted | 323 | 93.4% | 346 box máy |
| edited | 7 | 2.0% | 346 box máy |
| deleted | 16 | 4.6% | 346 box máy |
| added | 82 | 19.9% | 412 đối tượng sau duyệt |

- Lớp nào máy tệ nhất? Vì sao (dẫn ảnh)? three_wheeler (bị xóa 6/12 box do nhầm sang car hoặc box lỏng) và motorbike ( bỏ sót nhiều trong cụm đông, phải vẽ thêm box). Ví dụ img_013.jpg và img_004.jpg.
- So với gold: precision / recall của **máy** Precision = 64.2% (34/53), Recall = 54.8% (34/62) (mẫu số: 62 đối tượng gold). (mẫu số: đối tượng gold)

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Bắt bẫy: 7/12; loại nào lọt: bẫy extra, loose, missing; vì sao (bạn đã bỏ bước nào của checklist)? Do thói quen nhìn lướt box nhanh, bỏ qua bước ẩn nhãn máy trước khi quét ảnh
- Accepted cao mà bắt bẫy thấp (hoặc ngược lại) nói gì về cách bạn duyệt? Accepted cao (93.4%) trong khi bắt bẫy trung bình (58.3%) cho thấy sự phụ thuộc vào nhãn máy có sẵn (automation bias), ngại chỉnh sửa nếu box nhìn tương đối khớp.

## 4. Máy có đáng dùng không?

- Thời gian (từ `time_log.csv`): gán tay 6-7 phút/ảnh vs duyệt 2-3 phút/ảnh. Thiên lệch (bạn gán gold trước nên đã quen cảnh): Xe cộ đông đúc
- Kết luận: có dùng cho các lớp phổ biến (person, car, motorbike), dựa trên Tỉ lệ sai các lớp này thấp hơn đáng kể khi so với các lớp hiếm như three-wheeler
- **Ba quy tắc giảm thiên kiến của bạn** (mỗi quy tắc kiểm tra được bằng số hoặc việc làm):
  1. ẩn nhãn máy trong 5–10 giây đầu để tự mắt quét toàn bộ bối cảnh ảnh
  2. phóng to kiểm tra 4 góc của box đối với các đối tượng phương tiện lớn để tránh loose box
  3. sau mỗi 8–10 ảnh phải nghỉ mắt 1 phút để tránh mỏi mắt dẫn đến accept tự động

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh | SAM (phiên bản) | Số click | Mask gồm người lái / hàng hoá? | Lỗi quan sát | File trong `masks/` |
| --- | --- | --- | --- | --- | --- |
| `img_013.jpg` | sam2.1_b.pt | 2 pos, 1 neg | Không (tách riêng được người) | Cần điểm neg chuẩn để không ăn lan vào người ngồi | `tuktuk_img013.jpg` |
| `img_004.jpg` | sam2.1_b.pt | 2 pos, 1 neg | Không | Khung kim loại phía trên mui xe hơi bị đứt | `three_wheeler_img004.jpg` |
| `img_008.jpg` | sam2.1_b.pt | 2 pos, 1 neg | Có bao gồm phần thùng chở hàng | Bánh xe sau hơi bị lẹm do bóng đổ | `three_wheeler_img008.jpg` |

- Đối chiếu guideline mục 4.2: quy tắc nào giúp, quy tắc nào thiếu? 4.2: Quy tắc loại bỏ bóng đổ giúp ích nhiều khi đặt điểm âm (neg). Thiếu quy tắc cụ thể cho việc phân chia vùng ranh giới giữa hàng hóa dính liền và thùng xe
- *(Tuỳ chọn)* Interactor SAM trong CVAT: làm được / không (lỗi gì, đã thử gì); khác gì offline: 

## 6. Calibration (Part 6)

- Số quyết định: 3 (`calibration_decisions.csv`); quan trọng nhất (dẫn `CAL-01`): phân loại xe máy điện và xe đạp điện
- Quy tắc mới đã vào `guideline_v1.1_calibrated.md`: Phân chia xe điện 2 bánh dựa vào kết cấu và vận tốc; chuẩn hóa dung sai viền box không quá 5px
- Nguyên nhân gốc: guideline mơ hồ

## 7. QC và quyết định (Part 6)

- Mẫu: 5 ảnh 0 ảnh `can_review` + 5 ngẫu nhiên, seed 42; người kiểm: bạn (**hạn chế**: bạn vừa là người duyệt).
- Lỗi theo **đối tượng**: 1/84 = 1.2% · theo **ảnh**: 1/5 = 20% (ngưỡng Release ≤ 2%)
- Top lỗi (trong: bỏ sót, thừa, sai lớp, sai vị trí/kích thước, sai thuộc tính, sai mask) + một ảnh ví dụ mỗi loại: sai_vi_tri_kich_thuoc (box xe máy bị lỏng viền ở img_006.jpg)
- **Giới hạn mẫu nhỏ** (cận trên 95% trong `qc_report_auto.md`): Wilson 95% của tỷ lệ ảnh lỗi lên tới 43%. Mẫu 5 ảnh chỉ đủ để phát hiện lỗi thô mang tính hệ thống chứ chưa đủ ý nghĩa thống kê để chứng minh tỷ lệ lỗi thực tế toàn bộ lô đạt dưới 2%
- Script kiểm được / không kiểm được (ví dụ ôm sát, cùng bỏ sót với máy, theo checklist thật): Script kiểm tra được tính toàn vẹn dữ liệu (đủ trường, đúng định dạng COCO, IoU so khớp), nhưng không kiểm tra được độ ôm sát thẩm mỹ của box, không tự nhận biết được việc người duyệt có thực sự kiểm tra kỹ hay chỉ bấm Accept hàng loạt, và không phát hiện được trường hợp cả người và máy cùng bỏ sót một vật bị che khuất sâu

**Quyết định: Rework **, ngày 2026-11-18. Lý do (đi theo Hold → Rework → Release): Lý do : Guideline v1.1 đã chốt tốt các ca khó, nhưng tỷ lệ bắt bẫy thực tế chỉ đạt 7/12 (58.3% < ngưỡng đạt 80%) vẫn bị lọt 5 bẫy (extra và loose box). Theo đúng quy trình quản lý chất lượng, bộ dữ liệu cần được Rework để loại bỏ triệt để thiên kiến tự động hóa.
Nếu Rework / Hold, việc cần làm tiếp: Mở lại CVAT với task batch-review, thực hiện rà soát lượt hai cho các ảnh có mật độ phương tiện dày, áp dụng quy tắc ẩn nhãn máy 10 giây đầu và siết chặt các box lỏng

## 8. Ghi chú cá nhân

- **Dùng AI để làm bài** (công cụ nào, việc gì, kiểm tra thế nào; "không dùng" nếu không): Dùng mô hình YOLO26m và YOLOE-26s để pre-label phát hiện vật thể, SAM 2.1 (qua notebook Colab) để tạo mask. Dùng trợ lý AI để sumarize đề bài, yêu cầu
- Gold có bị nhiễm không (bạn có nhìn pre-label hay hỏi AI trước khi gán xong)? Không, 4 ảnh gold được gán tay hoàn toàn từ đầu trên CVAT.
- Máy / GPU / CVAT dùng; vướng mắc cài đặt: Laptop Windows 11; chạy mô hình AI trên Google Colab GPU T4; CVAT v2.81 chạy qua Docker Desktop cục bộ trên Windows, Cvat không hỗ trợ trích xuất tọa độ --pos và --neg mà phải dùng AI để giúp trích xuất tọa độ tại vị trí đã chọn
- Dùng cache `data/cache/` thay vì tự chạy? Không, tự chạy trực tiếp bằng GPU T4 trên Colab và tải kết quả stage1/outputs về máy.
- Lỗi / bug script đã gặp (nếu có): Không có
- Một điều bạn làm khác lần sau: tuân thủ triệt để checklist review 
