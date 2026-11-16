# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 3
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 292 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 292 | 0.809 | 97.6% | 40.8% | 83.6% | 211/102 | 0 | CHƯA ĐẠT |
| 111|555 | 13 | 115 | 0.756 | 98.3% | 19.1% | 73.9% | 71/75 | 0 | CHƯA ĐẠT |
| 111|999 | 13 | 105 | 0.812 | 97.1% | 41.0% | 90.5% | 101/24 | 0 | CHƯA ĐẠT |
| 555|999 | 14 | 72 | 0.889 | 97.2% | 75.0% | 88.9% | 39/3 | 0 | ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 969 ({'bo_sot_thua': 309, 'co_can_review': 294, 'iou_thap': 130, 'sai_thuoc_tinh': 189, 'bat_dong_ignore': 29, 'sai_lop': 7, 'can_review_anh': 11}), trên 35 ảnh cần calibration.
