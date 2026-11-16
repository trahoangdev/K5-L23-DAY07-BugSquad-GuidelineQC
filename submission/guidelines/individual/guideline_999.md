# Guideline gán nhãn — cá nhân (trước họp nhóm) — 2A202603310 Nguyễn Nhân Thành Long

- Phiên bản: cá nhân (trước khi họp thống nhất v1.0)
- Ngày: 16/11/2026
- Người viết / duyệt: Nguyễn Nhân Thành Long (2A202603310) / chờ nhóm duyệt

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện vật thể trên ảnh 2D đường phố châu Á (người, xe máy, ô tô, xe tải, xe thùng nhỏ).

Chỗ sai nguy hiểm nhất:
- **Gán vật không thật** (tượng, đồ chơi, ảnh phản chiếu): mô hình báo xe, người ở nơi không có.
- **Box không ổn định** ở đám đông, vật mờ, xe chở xe: các người gán vẽ mỗi người một kiểu, IAA thấp, mô hình học nhiễu.
- **Bỏ sót vật thật** vì quy tắc quá chặt.

Nguyên tắc chung:
- **R-01.** Chỉ gán vật thật, hiện diện trong cảnh.
- **R-02.** Box ôm sát phần nhìn thấy, không đoán phần bị che.
- **R-03.** Chỉ gán khi nhận ra lớp mà không phải đoán; không đủ thì dùng `ignore_region`.
- **R-04.** Chưa chắc: vẫn vẽ theo quyết định tốt nhất và tick `can_review`.

## 2. Lớp và thuộc tính

### Lớp

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
| --- | -------------------- | ----- | ------------------------ |
| `car` | Ô tô con, SUV | Taxi, xe 4 chỗ, SUV đang chạy hoặc đỗ | Ô tô nằm trên xe chở xe, ô tô đồ chơi, xe trong gương; dễ nhầm với `van` khi là xe thùng kín nhỏ |
| `truck` | Xe tải, xe có thùng hàng riêng | Xe tải thùng, xe chở xe (tính cả hàng gắn trên xe) | Xe thùng kín cỡ nhỏ là `van`, không phải `truck` |
| `van` | Xe thùng kín cỡ nhỏ | Xe van chở hàng nhỏ | Xe tải có thùng hở hoặc thùng lớn là `truck`; SUV là `car` |
| `motorcycle` | Xe máy, mô tô (thân xe, không gồm người ngồi) | Xe máy đang chạy hoặc đỗ | Xe đạp, xe đồ chơi |
| `person` | Người đi bộ hoặc người ngồi trên xe | Người đi bộ, người lái và người ngồi sau xe máy | Tượng người, ma-nơ-canh, hình in trên biển quảng cáo |
| `ignore_region` | Vùng bỏ qua, không phải vật: nơi không gán từng vật, bỏ sót ở đây không tính là lỗi | Phần đám đông không tách được, vật mờ không nhận ra | Không dùng để né những vật rõ ràng nhưng khó vẽ |

Vật **không thuộc lớp nào** thì **không gán**. Nếu là vùng không tách được (đám đông, vật mờ nặng) thì khoanh `ignore_region`. Không tạo lớp mới trong lượt gán độc lập.

### Thuộc tính

| Thuộc tính | Các mức cố định | Nghĩa và ngưỡng |
| ---------- | --------------- | --------------- |
| `occluded_level` | `0` / `1` / `2` / `3` (mặc định `0`) | `[CHECK theo v0]` `0`: thấy trọn vật; `1`: bị che dưới 25%; `2`: bị che 25–50%; `3`: bị che trên 50% nhưng vẫn nhận ra lớp |
| `truncated` | `no` / `yes` (mặc định `no`) | `yes` nếu vật bị cắt bởi mép ảnh |
| `can_review` | checkbox (mặc định không tick) | Tick khi chưa chắc quyết định của box (R-04) |

## 3. Quy tắc vẽ box

- **Phần nhìn thấy (R-02):** box ôm sát phần nhìn thấy, không kéo dài ra phần bị che.
- **Vật bị che:** gán nếu còn nhận ra lớp, ghi mức che vào `occluded_level`. Che nặng (`3`) mà còn nhận ra vẫn gán; không còn nhận ra lớp thì không gán (R-03).
- **Vật bị cắt mép ảnh:** gán phần nằm trong ảnh, đặt `truncated = yes`.
- **Phản chiếu, hình in, tượng, đồ chơi (R-01):** không gán. Xe thật bị khung gương che một phần vẫn gán theo phần thấy được (R-08).
- **Xe chủ (ego):** phần thân xe hoặc capô của xe gắn camera ở mép ảnh không gán `[CHECK v0]`.
- **Vật quá nhỏ:** box cao dưới **20 px** (`min_box_height_px`) thì không vẽ; nếu là cụm nhiều vật nhỏ thì khoanh `ignore_region`.
- **Đám đông (R-06):** gán từng người nhìn rõ đầu và thân (phần nhìn thấy khoảng 30% trở lên `[CHECK]`); phần còn lại, gồm người chỉ thấy đầu, cho vào `ignore_region`, không đoán số người.
- **Một vật có nhiều phần, người và xe (R-10):** hai loại box: một `motorcycle` (thân xe) và một `person` cho mỗi người ngồi, cho phép chồng lên nhau. Người bị che nhiều vẫn gán theo phần nhìn thấy với `occluded_level` tương ứng. Nếu nhóm chọn một box chung thì đổi theo, miễn mọi người dùng giống nhau.
- **Xe chở xe (R-05):** một box `truck` ôm cả xe tải và xe được chở; không gán từng xe được chở.
- **Hàng cồng kềnh (R-09):** hàng gắn chắc trên xe tính vào box của xe; không tách thành box riêng.
- **Vật mờ do chuyển động (R-07):** còn nhận ra lớp thì gán; không còn nhận ra thì `ignore_region`.

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
| --- | ---------- | ------- | ---------- | ----- |
| img_002 | Xe tải chở nhiều ô tô con | R-05 | 1 box `truck` ôm cả hàng, không gán từng `car` trên xe. Hỏi reviewer, `can_review` | Ô tô trên xe tải là hàng hóa, không phải phương tiện độc lập. Gán từng chiếc tạo nhiều box chồng nhau, IoU thấp giữa người gán |
| img_003 | Đông người, chồng nhau | R-06 | Gán từng `person` nhận ra được; vùng không tách được cho vào `ignore_region` | Ép gán từng người cho số box khác nhau giữa người gán, làm IAA thấp mà lỗi nằm ở ảnh |
| img_011 | Có tượng | R-01 | Không gán, kể cả tượng giống người hay xe. Loại theo quy tắc | Mô hình phải phân biệt vật thật với mô phỏng |
| img_014 | Người đi xe máy bị mờ nặng | R-07 | Còn nhận ra thì gán `motorcycle` và `person`, tick `can_review`; mờ quá thì `ignore_region`. Hỏi reviewer | Biên box trên vệt mờ không ổn định; điểm "còn nhận ra" mang tính chủ quan, cần ảnh mẫu của nhóm |
| img_020 | Xe nhìn qua gương | R-01, R-08 | Chỉ gán xe thật, không gán ảnh trong gương. Loại theo quy tắc | Ảnh phản chiếu không phải vật tại vị trí đó, gán sẽ sai chỗ |
| img_035 | Xe chở hàng cồng kềnh | R-09 | 1 box ôm xe, người lái và hàng gắn trên xe; tick `can_review` nếu quá khổ. Hỏi reviewer | Hàng di chuyển cùng xe, một box giữ quy tắc đơn giản. Nếu mục tiêu là ước lượng kích thước xe thì nên bỏ hàng khỏi box |
| img_038 | Ô tô đồ chơi | R-01 | Không gán là `car`. Loại theo quy tắc | Gán nhầm dạy mô hình báo xe ở nơi không có |

Chưa chốt: đánh dấu `can_review` trên box, hoặc ghi vấn đề cấp ảnh để nhóm thu vào `image_issues.csv` sau khi khóa nhãn. Dùng mã `R-xx` trong nhật ký QC.

### Thử quy tắc trên ảnh khác

| Quy tắc | Ảnh dùng để thử | Kết quả | Cần sửa quy tắc? |
| ------- | --------------- | ------- | ---------------- |
| R-01 | img_017 | Phân biệt được tượng | Không. Quy tắc dùng được. |
| R-06 | img_005 | Gán được 3 người phía trước. Hàng sau khoảng 3 người chỉ thấy đầu, nên khoanh 1 `ignore_region`. | Có. Bổ sung vào R-06: người chỉ thấy đầu, không thấy thân thì đưa vào `ignore_region`. |
| R-10 | img_010 | Vẽ 3 box: 1 `motorcycle`, 2 `person` chồng lên nhau. Người ngồi trước bị người ngồi sau che khoảng 80%, vẫn gán theo phần thấy được. | Không |

## 5. QC và phiên bản

- **Ngưỡng QC áp dụng** (theo mục `rules` trong `taxonomy.json`, giữ nguyên):
  - Box cao tối thiểu 20 px (`min_box_height_px`).
  - Ghép cặp box khi IoU từ 0.5 (`match_iou`).
  - IoU mục tiêu: 0.85 cho vật lớn từ 50 px (`iou_thr_large`, `large_px`), 0.7 cho vật nhỏ (`iou_thr_small`).
  - IAA: IoU 0.85 (`iaa_iou`), đồng thuận lớp 0.95 (`iaa_class`).
  - Tỉ lệ ảnh lỗi tối đa 2% (`error_rate_img_max`); vượt thì Rework.
- **Ca mơ hồ do ai quyết:** các ca ghi "Hỏi reviewer" (img_002, 014, 035) và câu hỏi mục 4 do cả nhóm chốt trong buổi họp.
- **Lịch sử thay đổi:**

| Phiên bản | Thay đổi | Lý do (ca nào) |
| --------- | -------- | -------------- |
| cá nhân | Bản nháp đầu, 7 ca khó và quy tắc R-01 đến R-10 | img_002, 003, 011, 014, 020, 035, 038 |
| cá nhân (bổ sung) | Khớp lớp, thuộc tính, ngưỡng với `taxonomy.json` v0; thêm lớp `van`; điền bảng thử quy tắc; bổ sung R-06 cho người chỉ thấy đầu | img_005, img_010, img_017 |