# Pha 4 — Đo IAA và rà nguyên nhân bất đồng

Ngày kiểm: 2026-11-17. Người tổng hợp: 111, có Codex hỗ trợ đọc ảnh và chạy script.

Đã thực hiện bước đo và khảo sát bất đồng của Pha 4. **IAA trước calibration CHƯA ĐẠT**: IoU trung bình 0,809 < 0,85; đồng thuận lớp 97,6% >= 95%. Cần chuyển các ca sang Pha 5 để chốt và sửa bản nhãn cuối. Báo cáo này là phân tích của 111, chưa phải biên bản đồng thuận của ba thành viên hoặc kết quả QC/Release.

## 1. Đầu vào và các bước đã chạy

- `git pull --ff-only origin main`: Already up to date; HEAD `4028044` khi bắt đầu đo.
- Đọc Pha 4 của tài liệu lab, `docs/QC_CHEATSHEET.md`, guideline chung v1.0 và hai script so sánh/vẽ overlay.
- Chạy `scripts/validate_labels.py --all`: cả ba COCO hợp lệ về cấu trúc và phân công; 111 có 26 ảnh, 555 và 999 mỗi người 27 ảnh. Tổng **80/80 lượt**, 40 ảnh, mỗi ảnh hai người.
- Có một cảnh báo vật nhỏ: `999.json`, `img_031.jpg`, annotation **134**, person `[469,358,16,19]`. Cần kiểm lại biên phần nhìn thấy trên ảnh gốc theo R-03/R-09: nếu thực sự cao <20 px thì xử lý ignore_region; nếu box đang thiếu phần người thì sửa biên. Không tự kết luận chỉ từ cảnh báo rằng 555 đã bỏ sót người này.
- Tổng hợp 11 vấn đề cấp ảnh của 111 từ nhật ký Pha 3 vào [image_issues.csv](image_issues.csv) trước khi đo. Chưa thấy nhật ký cấp ảnh tương ứng của 555/999 trong repo; không suy ra hai bạn không có ca khó.
- Chạy `scripts/qc_compare.py` và `scripts/viz_disagreement.py --images flagged` bằng Python trong `.venv`.
- Giữ nguyên ba file nhãn độc lập, taxonomy và guideline v1.0. Hash đầu vào và các artifact được script ghi tại [iaa_summary.json](iaa_summary.json).

Các lệnh đã chạy, tính từ thư mục repo:

```powershell
git pull --ff-only origin main
.venv/Scripts/python.exe -X utf8 scripts/validate_labels.py --all
.venv/Scripts/python.exe -X utf8 scripts/qc_compare.py
.venv/Scripts/python.exe -X utf8 scripts/viz_disagreement.py --images flagged
```

## 2. Đọc đúng số đo

Nguồn: [iaa_summary.md](iaa_summary.md). **Mẫu số thực tế là 292 cặp box**, ghép một-một không phân biệt lớp với IoU >=0,5. Con số 177 trong ví dụ của lab không phải kết quả của BugSquad.

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Chưa ghép A/B | IAA |
|---|---:|---:|---:|---:|---:|---|
| Tổng | 40 | 292 | 0,809 | 285/292 = 97,6% | 211/102 | Chưa đạt |
| 111–555 | 13 | 115 | 0,756 | 113/115 = 98,3% | 71/75 | Chưa đạt |
| 111–999 | 13 | 105 | 0,812 | 102/105 = 97,1% | 101/24 | Chưa đạt |
| 555–999 | 14 | 72 | 0,889 | 70/72 = 97,2% | 39/3 | Đạt hai ngưỡng IAA |

Đồng thuận occluded_level **119/292 = 40,8%**, truncated **244/292 = 83,6%**. Có 130 cặp ghép không đạt ngưỡng IoU theo kích thước box. Chưa có box nào được cả hai vùng ignore phủ để bỏ qua (`n_skipped_in_ignore=0`).

Tổng 313 box chưa ghép không nằm trong mẫu số IoU/cùng lớp. Có 309 dòng bo_sot_thua và 4 ca chưa ghép được đưa vào bat_dong_ignore do chỉ một người phủ ignore; 25 bất đồng vùng ignore còn lại tạo thành tổng 29 dòng bat_dong_ignore. Không thể kết luận IoU cao đồng nghĩa không bỏ sót.

Script tạo **969 dòng tín hiệu trên 35/40 ảnh**:

| Loại | Số dòng |
|---|---:|
| bo_sot_thua | 309 |
| co_can_review | 294 |
| iou_thap | 130 |
| sai_thuoc_tinh | 189 |
| bat_dong_ignore | 29 |
| sai_lop | 7 |
| can_review_anh | 11 |

Đây không phải 969 lỗi đã xác nhận: một vật có thể tạo nhiều tín hiệu; can_review là yêu cầu kiểm, còn bo_sot_thua chưa cho biết ai đúng. 294 cờ box gồm 266 của 111, 27 của 555, 1 của 999. Số cờ khác nhau cũng không tự chứng minh ai gán tốt hơn.

## 3. Quan sát overlay và phân loại nguyên nhân

Đã xem tổng quan đủ 35 overlay qua ba bảng chia nhỏ tại `work/phase4/contact_*.jpg`; mở riêng overlay 035/037/040/031 và ảnh gốc 040/017 để kiểm các nhận định dưới đây. Đây là khảo sát Pha 4, chưa phải rà chi tiết từng dòng của 969 ca hoặc phiếu QC toàn bộ 40 ảnh.

| Bằng chứng thực tế | Đối chiếu và nhận định của 111 | Hướng xử lý đề xuất cho Pha 5 |
|---|---|---|
| **035, C870/C875**: cùng tuk-tuk chở hàng; 111 #366 `[380.85,211.27,170.14,201.69]`, 999 #178 `[341.98,28.93,231.35,384.16]`. Box xanh bao cả khối bao tải. | R-08/V23 đã nói rõ không bao hàng rời. Đây là lệch cách áp dụng quy tắc đã chốt của 999; chưa có bằng chứng cần đổi nghĩa guideline. Hai box cùng vật nhưng IoU <0,5 nên script xuất hai dòng bo_sot_thua, không phải bằng chứng có hai tuk-tuk riêng. | Ghép hai ca để thảo luận cùng một vật, sửa biên bản cuối theo R-03/R-08; vẫn rà biên của 111 thay vì mặc định lấy nguyên box. |
| **037, C927**: bus bên trái; 111 #388 truncated=yes, 555 #298 truncated=no, box khớp khoảng 0,98. | Xe bị mép trái cắt thật; R-05 rõ. Ghi chú 555 cũng khai chưa đặt truncated. Phù hợp nhóm một người chưa hoàn tất thuộc tính, không phải bằng chứng guideline mơ hồ. | Rà thuộc tính 555 toàn bộ ảnh, dùng 037 làm ca hướng dẫn lại. Pickup nắp cao #389/#297 đều là truck: không có bất đồng truck/van ở bản này. |
| **040, phát hiện bổ sung P4-S01**: bus ở mép trên; 555 #307 và 999 #204 đều truncated=no, hai box IoU khoảng 0,97. | Ảnh gốc cho thấy phần xe buýt bị cắt tại mép trên. Theo R-05, cả hai cùng sai truncated dù gần trùng box và cùng lớp. **C966 chỉ báo lệch occluded_level của chính cặp bus này; script không báo truncated vì hai người giống nhau.** | Mang phát hiện này cùng C966 vào calibration, kiểm truncated trên nhãn cuối và rà cả lô theo R-05. P4-S01 là mã ghi chú thủ công, không phải case_id được script sinh. |
| **031, C767**: person #134 của 999 cao 19 px; validator cũng cảnh báo. | Đây là ca kiểm ngưỡng R-09 và biên R-03. Chưa kết luận từ dòng bo_sot_thua rằng 555 bỏ sót; phải xác định đúng phần người nhìn thấy trước. | Mở ảnh ở kích thước gốc, chốt person đủ 20 px hay ignore_region theo đúng phần vật; không tăng box để vượt ngưỡng. |
| **011, C305; 015, C346/C347; 035, C878/C879**: đồ kim loại dạng mô tô, vệt nhòe, người sau kính chưa rõ. | Có vấn đề về bằng chứng nhận diện/biên theo R-01/R-10/R-12/R-13. Hiện chưa đủ cơ sở nói cả hai hiểu sai một quy tắc hoặc tuyên bố nhóm đã thống nhất loại/gán. | Giữ mở, đối chiếu ảnh gốc và ví dụ/phản ví dụ. Chỉ đề xuất thêm hướng dẫn v1.1 nếu xác định điều kiện còn thiếu; không ép lớp hoặc dùng ignore để bỏ qua sự mơ hồ. |

**Ca tham chiếu âm tính:** tài liệu lab lấy `img_017.jpg` (tượng người cưỡi ngựa) làm ví dụ lỗi đồng thuận sai. Đã mở ảnh gốc và kiểm bản 555/999: cả hai giữ ảnh trong COCO, 0 annotation, phù hợp R-01/R-14. Ca này không có cặp box nên không đóng góp vào mẫu số 292; nó không chứng minh các ca đồng thuận khác đều đúng. Phát hiện bus040 ở trên là ví dụ cùng sai thực tế của lô này. Chưa có bộ đáp án coach riêng ngoài tài liệu lab để đối chiếu.

Kết luận nguyên nhân hiện có: chứng cứ rõ cho lỗi áp dụng/thiếu thuộc tính của một người và cho hai người cùng sai. Các ca ảnh khó còn mở là giả thuyết cần kiểm, chưa tự quy hết thành guideline mơ hồ để sửa đặc tả.

## 4. Giới hạn đầu vào và tính độc lập

- [Ghi chú 555](../individual_notes/555.md) khai **301/301 đối tượng truncated=no**, một số ảnh chưa đặt occluded_level, và có dùng **YOLO11x pre-label** rồi sửa tay. Kết quả đo hiện tại phản ánh đúng snapshot đã nộp, bao gồm phần chưa hoàn tất này. 301 là số đối tượng, không gồm 6 ignore_region.
- `RULES.md` hiện có quy định pre-label bằng mô hình thuộc Day 08. Cần 555/điều phối đối chiếu với coach cách xử lý bản gán đó trong bài Day 07; chưa có xác nhận ngoại lệ. Không coi phép chạy thành công là chứng nhận tuân thủ phương pháp gán độc lập của cả nhóm. Không chạy thêm mô hình trong Pha 4.
- [Ghi chú 999](../individual_notes/999.md) chưa cập nhật phần hoàn thành Pha 3 dù COCO 999 đã có trong repo. Cần bạn ấy bổ sung quá trình gán và các ca chưa chắc; không ghi thay xác nhận của bạn.
- `image_issues.csv` hiện chỉ có 11 dòng của 111. Nếu bổ sung đầu vào sau này thì phải chạy lại Pha 4 và dùng cùng một bộ hash mới cho Pha 5; không ghép quyết định với một snapshot khác hoặc sửa kết quả sinh tự động.

## 5. Bàn giao sang Pha 5

Đầu vào đã sinh: [iaa_summary.json](iaa_summary.json), [iaa_summary.md](iaa_summary.md), [calibration_candidates.csv](calibration_candidates.csv), [image_scores.csv](image_scores.csv), [flagged_images.txt](flagged_images.txt), [consensus_draft.json](consensus_draft.json). 35 overlay và tổng quan nằm ở `work/viz/`, chỉ dùng nội bộ, không đưa `work/` vào gói nộp.

Ưu tiên theo số tín hiệu/điểm xếp hạng của script: **021 (172/225), 032 (83/109), 008 (82/100), 030 (62/87), 025 (61/81)**. Điểm có trọng số, không phải số lỗi. Nên chốt trước ca quy tắc rõ 035/037/040 và cảnh báo 031 để có ví dụ thống nhất, rồi rà những ảnh đông vật và 294 cờ review.

Pha 5 cần nhóm duyệt từng ca, ghi quyết định/lý do/Rule ID thật, xử lý phát hiện cùng sai và các giới hạn đầu vào, rồi tạo bản nhãn sau calibration theo pipeline. `consensus_draft.json` vẫn chỉ là nháp, chưa phải đáp án. Chưa tạo quyết định thay nhóm, chưa nâng guideline v1.1 và chưa có QC/Release cuối.

## 6. Đối chiếu checklist CP4 trong trang HTML

Kiểm lại ngày 2026-11-17 theo `PHASES[id=cp4]` của `K5-L23-Day07-Guideline-Taxonomy-QC.html`. URL localhost:5500 đang từ chối kết nối ở lượt kiểm này, nên đọc file HTML trong repo; chưa xác nhận trạng thái các ô tick lưu riêng trong trình duyệt. Chạy lại validator và kiểm hash hiện tại: cả ba file vẫn hợp lệ về cấu trúc, cảnh báo 031 còn nguyên; toàn bộ 50 hash đầu vào/artifact khớp.

| Mục checklist | Bằng chứng hiện có | Trạng thái |
|---|---|---|
| 1. Tập hợp đủ nhãn, chạy validate_labels.py --all | Đủ 80/80, ba COCO hợp lệ; vẫn có WARN img_031 #134 của 999 | Đã chạy, **chưa thể gọi validator sạch** theo mục tiêu CP4 |
| 2. Chạy qc_compare.py, kiểm độ phủ và khóa hash | Có đủ artifact, 50 hash kiểm lại đều khớp | Đủ bằng chứng kỹ thuật |
| 3. Sinh overlay bằng viz_disagreement.py --images flagged | Đủ 35/35 overlay và _overview.png | Đủ bằng chứng kỹ thuật |
| 4. Đọc IoU, cùng lớp, mẫu số, box chưa ghép, ignore | Mục 2 của báo cáo giải thích 292 cặp, 313 box chưa ghép và 29 bất đồng ignore; IoU chưa đạt | Đã thực hiện việc đọc và diễn giải; không đánh dấu IAA đạt |
| 5. Phân loại nguyên nhân trên overlay | Mục 3 có ca 035/037 theo quy tắc đã rõ, ca cùng sai 040 và các giả thuyết còn mở | Có phân tích của 111; chưa có xác nhận thảo luận của cả nhóm |
| 6. Đối chiếu mẫu coach để tìm ca đồng thuận sai | Đã đối chiếu ví dụ 017 trong tài liệu và tìm ca cùng sai thực tế ở 040; chưa có bộ tham chiếu riêng do coach xác nhận | **Một phần**, chưa xác nhận trọn yêu cầu mẫu coach |

`docs/COACH_GUIDE.md`, mục “Trước khi mở bài”, bước 3 yêu cầu coach chuẩn bị bộ tham chiếu riêng, chốt quy tắc cho các ca 017, 005/026, 002 rồi mới mở sau khi thu nhãn độc lập. Bảng gợi ý này không tự trở thành bộ đáp án đã được coach duyệt. Cần đối chiếu bộ đó nếu lớp đã cung cấp, hoặc ghi nhận coach dùng chính các ca trong tài liệu làm mẫu xác nhận; không tự tạo xác nhận này.

Phần vai trò CP4 còn yêu cầu mỗi người đọc bất đồng của mình và nhóm cùng thảo luận. Ghi chú Pha 4 của 555 vẫn là chỗ trống; của 999 vẫn ghi chưa thực hiện. Vì vậy hiện đủ phần đo và phân tích của 111, **chưa đủ bằng chứng để tick toàn bộ Pha 4 của nhóm**. Cần kết luận cảnh báo 031, bổ sung đối chiếu coach và phần giải thích của 555/999. Nếu đổi nhãn đầu vào sau kiểm thì phải giữ snapshot cũ, tạo snapshot mới và chạy lại Pha 4; không sửa tay artifact đã khóa. IAA thấp là kết quả cần mang sang calibration, không phải yêu cầu sửa số đo thành đạt trước khi hoàn tất bước đo.
