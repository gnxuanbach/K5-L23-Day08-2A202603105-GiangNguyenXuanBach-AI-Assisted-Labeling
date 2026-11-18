# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 346; đối tượng sau duyệt: 412; ngưỡng accepted: IoU >= 0.9
- Đã **loại 25 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 323 | 323/346 = 93.4% | 346 box máy |
| edited (sửa) | 7 | 7/346 = 2.0% | 346 box máy |
| deleted (xoá) | 16 | 16/346 = 4.6% | 346 box máy |
| added (vẽ thêm) | 82 | 82/412 = 19.9% | 412 đối tượng sau duyệt |

Trong số deleted + added, **10 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 72.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 183 | 4 | 1 | 26 |
| bicycle | 4 | 0 | 0 | 7 |
| motorbike | 81 | 2 | 2 | 30 |
| car | 37 | 0 | 3 | 9 |
| bus | 2 | 0 | 1 | 2 |
| truck | 4 | 0 | 3 | 1 |
| three_wheeler | 12 | 1 | 6 | 7 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 62 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 34 | 19 | 28 | 64.2% (KTC 95%: 51–76%) | 54.8% (KTC 95%: 43–67%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 7/12 = 58.3% (KTC 95%: 32–81%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - extra: 1/3
  - wrong_class: 3/3
  - missing: 2/3
  - loose: 1/3
  - lọt: img_005.jpg (extra), img_033.jpg (extra), img_027.jpg (loose), img_028.jpg (missing), img_025.jpg (loose)

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
