# Thông tin học viên

- Họ và tên: Giang Nguyễn Xuân Bách
- MSHV: 2A202603105
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: gnxuanbach
- Repo bài nộp (URL): https://github.com/gnxuanbach/K5-L23-Day08-2A202603105-GiangNguyenXuanBach-AI-Assisted-Labeling
- Commit chốt bài (hash): chot-bai-final
- Máy: Windows 11, GPU: Google Colab T4
- CVAT (Docker): v2.81.0

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted / edited / deleted: 323 / 7 / 16 (mẫu số: 346 box máy); added: 82 (mẫu số: 412 đối tượng)
- So với gold: precision / recall của **máy**: 64.2% / 54.8% (mẫu số: 62 đối tượng gold)
- Bắt bẫy: 7/12 = 58.3%
- Thời gian: gán tay 6.25 phút/ảnh; duyệt có pre-label 2.7 phút/ảnh
- Lỗi QC: 20.0% theo ảnh (1/5), 1.2% theo đối tượng (1/84)
- **Quyết định:** Rework — lý do một dòng: Tỷ lệ bắt bẫy đạt 7/12 = 58.3% (< 80%), cần rà soát lại các bẫy sót trước khi release.