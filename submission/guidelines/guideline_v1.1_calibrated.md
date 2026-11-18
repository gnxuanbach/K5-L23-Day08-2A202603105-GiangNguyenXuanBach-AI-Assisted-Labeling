# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603105 - Giang Nguyễn Xuân Bách

> **Chỉ viết phần thay đổi so với v1.0** (không copy cả file). Chốt các câu hỏi mở ở mục 6 của v1.0 (hoặc nói rõ câu nào chưa chốt, vì sao)
> và thêm quy tắc mới rút ra từ lỗi của máy / lỗi của bạn. Mỗi ca khó **có ảnh ví dụ** (`img_0xx`) và khớp một dòng trong `qc/calibration_decisions.csv`.



## 1. Câu hỏi mở đã chốt (từ mục 6 của v1.0)

| # | Câu hỏi | Quyết định | Ảnh ví dụ | `case_id` |
|---|---|---|---|---|
| 1 | Xe điện: `bicycle` hay `motorbike`? |  `motorbike` nếu xe có kết cấu xe máy điện. Gán `bicycle` nếu là xe đạp trợ lực có bàn đạp. | `img_023.jpg` | CAL-01 |
| 2 | Hàng nhô ra xe máy / ba gác chở cồng kềnh thì box thế nào? | Box xe ôm cả hàng nếu hàng gắn cố định hoặc chiếm dưới 20% chiều dài xe. Nếu hàng nhô quá dài ra sau/trước thì box xe chỉ ôm thân xe, không tính phần hàng rời. | `img_008.jpg` | CAL-04 |

## 2. Ca khó mới (từ lỗi của máy, bẫy lọt, box `can_review`)

| Ảnh | Tình huống | Máy làm gì (model nào) | Quyết định của bạn | Quy tắc mới (vào mục nào của v1.0) |
|---|---|---|---|---|
| `img_013.jpg` | Xe tuk-tuk chở khách | YOLO26 gán nhầm thành `car` | Đổi nhãn thành `three_wheeler`, tách thêm box `person` cho người ngồi nhìn thấy | Bổ sung mục 4.1: Kiểm tra kỹ phương tiện 3 bánh để phân biệt với `car` |
| `img_027.jpg` | Box xe máy bị lỏng (bẫy loose) | Bẫy tạo box rộng hơn vật ~30% | Siết sát mép bánh xe và tay lái | Bổ sung mục 4.1: Độ sai lệch viền box so với mép vật không vượt quá 5px |
| `img_028.jpg` | Người đi bộ bị che khuất | Máy bỏ sót | Vẽ thêm box `person` | Bổ sung mục 4.1: Người nhìn thấy ≥ 20% cơ thể dù bị che khuất vẫn phải gán |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Lý do (ca nào, `case_id`) |
|---|---|---|
| v1.0 | Bản giảng viên | — |
| v1.1 | Chốt quy tắc xe điện hai bánh, siết dung sai bounding box, làm rõ phân tách xe 3 bánh và người ngồi trong xe | CAL-01, CAL-02, CAL-03 |

