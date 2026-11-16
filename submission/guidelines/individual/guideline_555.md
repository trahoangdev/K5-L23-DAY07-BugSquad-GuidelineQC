# Guideline gán nhãn — v0.555 (bản cá nhân) — mã 555

- Phiên bản: v0.555 (bản cá nhân Pha 1, chưa thống nhất nhóm)
- Ngày: 2026-11-16
- Người viết: 555 · Nhóm: BugSquad
- Kế thừa: `guideline/guideline_v0_starter.md` (v0)
- Phạm vi khảo sát: đã xem toàn bộ 40 ảnh `img_001` – `img_040`

---

## 1. Mục tiêu

Nhãn dùng huấn luyện **mô hình phát hiện vật 2D** cho camera giao thông và xe tự lái: tìm người
và phương tiện đang tham gia giao thông trong ảnh đường phố.

Hai kiểu sai nguy hiểm, xếp theo mức độ:

1. **Bỏ sót người thật hoặc xe thật đang lưu thông.** Mô hình không thấy thì xe không phanh. Đây là
   lỗi tôi ưu tiên tránh nhất, nên khi phân vân giữa gán và không gán một vật **thật**, tôi gán và tick `can_review`.
2. **Gán vật không có thật** (tượng, ma-nơ-canh, đồ chơi, hình in, phản chiếu). Mô hình học sẽ phanh
   trước một tấm biển quảng cáo. Với vật **không thật**, tôi làm ngược lại: không gán, vì bộ dữ liệu này
   cố tình chứa rất nhiều ca như vậy (`img_011`, `img_017`, `img_018`, `img_024`, `img_026`, `img_027`,
   `img_033`, `img_038`) và để lọt sẽ dạy mô hình sai một cách có hệ thống.

Nguyên tắc rút ra, dùng xuyên suốt mục 3: **vật thật thì thà gán thừa, vật không thật thì thà bỏ.**

## 2. Lớp và thuộc tính

### 2.1 Lớp

Phép thử để quyết định lớp hay thuộc tính: *mô hình có phải đoán ra thông tin này lúc suy luận không?*
Có → `classes`. Không, chỉ phục vụ lọc mẫu và QC → `attributes`.

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| `car` | Xe 4 bánh chở người, khoang hành khách liền thân, dưới 9 chỗ: sedan, hatchback, SUV, taxi con. | `img_015` xe đỏ và xe bạc, `img_023` Honda Civic đen, `img_031` các xe trên phố, `img_040` taxi xanh đỏ | Xe bán tải (→ `truck`); xe thùng kín chở hàng (→ `van`); mô hình xe (không gán) |
| `truck` | Xe có **thùng chở hàng tách rời khỏi cabin**, kể cả bán tải và xe tải nhỏ, có hay không có nắp che. | `img_001` xe bán tải trắng, `img_002` đầu kéo chở xe, `img_007` xe thùng ISUZU, `img_012` bán tải chở thùng hàng, `img_036` xe tải nhỏ xanh, `img_037` bán tải nắp cao | Xe thùng kín liền thân (→ `van`); tượng xe `img_027`; vỏ xe trên nóc `img_024` |
| `van` | Xe thùng kín **liền một khối với cabin**, chở người hoặc hàng, không có thùng rời. | `img_005` xe trắng 51LD-70-65, `img_016` xe trắng sau tuk-tuk | Bán tải gắn nắp thùng (vẫn là `truck`, xem R-11); minibus (→ `bus`) |
| `bus` | Xe khách thân dài, nhiều hàng ghế và lối đi dọc, cỡ từ minibus trở lên. | `img_009` minibus 7KH-8044, `img_031` xe buýt 62, `img_037` hai xe buýt GO xanh, `img_040` xe buýt vàng đỏ góc trên | `van` chở khách cỡ nhỏ; xe tải thùng kín |
| `tuk_tuk` | Xe cơ giới **ba bánh** chở khách hoặc hàng, có mui, kiểu auto-rickshaw. | `img_004` hai xe xanh, `img_016` xe bạc và xe xanh lá, `img_035` xe vàng chở bao tải, `img_040` xe xanh-vàng biển ÃĐ-5725 | Xe máy thường (→ `motorcycle`); xích lô đạp không động cơ (→ `bicycle`) |
| `motorcycle` | Xe máy, mô tô, xe tay ga hai bánh, **kể cả có thùng bên (sidecar)**. | `img_006` cả hàng xe, `img_008` xe đỗ, `img_022` mô tô có sidecar, `img_035` xe máy người áo trắng | Xe đạp (→ `bicycle`); tượng xe máy bằng sắt vụn `img_011` |
| `bicycle` | Xe đạp và xe ba bánh **không động cơ**, gồm xích lô và xe đạp thồ. | `img_003` xe đạp vỉa hè, `img_023` xích lô chở khách, `img_029` người đạp xe chở xà, `img_030` xe đạp góc dưới | Xe máy; `tuk_tuk` có động cơ |
| `person` | Người thật, đi bộ hoặc ngồi trên bất kỳ phương tiện nào. | `img_013` hai người đi bộ, `img_014` hai người qua đường, `img_034` người băng ngang, `img_019` người lái xe diễu hành | Ma-nơ-canh `img_033`; tượng `img_017`; chân dung in `img_018`; linh vật hoá trang `img_019` |
| `ignore_region` | Vùng chữ nhật phủ **cụm vật quá dày hoặc quá nhỏ để tách từng cái**; bỏ sót bên trong không tính lỗi. | `img_021` biển xe máy phía xa, `img_030` bãi xe máy dày, `img_032` hàng người chờ xe buýt | Không dùng để né vật đơn lẻ mà mình lười gán |

**So với v0 tôi đề nghị thêm 3 lớp.** Lý do: cả ba đều là thứ mô hình buộc phải phân biệt lúc suy luận
(kích thước, quỹ đạo và cách xử lý của xe buýt, tuk-tuk, xe đạp khác hẳn nhau), và cả ba xuất hiện nhiều
lần trong 40 ảnh nên không thể coi là ca hiếm:

| Lớp thêm | Ảnh có mặt | Nếu không thêm thì sao |
|---|---|---|
| `bus` | `img_009` `img_031` `img_037` `img_040` | Phải nhét vào `truck` hoặc `van`, cả hai đều sai nghĩa; hai người sẽ chọn hai kiểu khác nhau |
| `tuk_tuk` | `img_004` `img_016` `img_035` `img_040` | Không lớp nào khớp. Người gộp vào `motorcycle`, người gộp vào `car`, người bỏ qua — nguồn bất đồng lớn nhất của v0 |
| `bicycle` | `img_003` `img_006` `img_023` `img_029` `img_030` `img_034` `img_039` | Xe đạp là người tham gia giao thông dễ tổn thương nhất; bỏ hẳn khỏi taxonomy là lỗ hổng an toàn |

> **Đây là mục tôi dự đoán nhóm sẽ tranh luận nhất ở Pha 2**, đặc biệt là `bicycle`: thêm lớp thì phải gán
> thêm khá nhiều vật nhỏ, không thêm thì mô hình mù trước xe đạp. Tôi chọn thêm, và sẵn sàng đổi nếu nhóm
> thống nhất khác — nhưng phải chốt dứt điểm trước khi gán, không để mỗi người tự hiểu.

**Vật không thuộc lớp nào thì làm gì?**

- **Động vật** (chó ở `img_009`, chim ở `img_017`, ngựa thật nếu có): không gán. Taxonomy này không có lớp động vật, và thêm lớp chỉ vì một con chó là không đáng.
- **Vật thật nhưng ngoài phạm vi** (xe cứu hoả lạ, máy kéo…): tick `can_review` trên box tạm với lớp gần nhất, ghi vào `image_issues.csv`, mang ra họp. Không tự đặt lớp mới một mình.
- **Vật không có thật**: không gán (R-04).

### 2.2 Thuộc tính

**`occluded_level`** — v0 viết "che ít / che nhiều / không rõ", hai người đọc ra hai cách. Tôi thay bằng
ngưỡng phần trăm, tính trên **diện tích vật nếu nhìn thấy trọn vẹn** (ước lượng bằng mắt, không cần đo):

| Mức | Ngưỡng | Cách nhận ra nhanh |
|---|---|---|
| `0` | bị che 0–10% | Thấy gần như trọn vật |
| `1` | 11–30% | Mất một phần nhỏ: bánh xe sau khuất, chân người khuất sau xe |
| `2` | 31–70% | Mất quá nửa: `img_005` những người ngồi sau chỉ còn thấy đầu và vai |
| `3` | > 70% nhưng **vẫn chắc chắn** nhận ra loại | `img_030` các xe máy lớp trong chỉ lộ tay lái và yên |

Che **quá 70% và không còn chắc chắn** vật đó là gì: **không gán**, ghi vào `image_issues.csv`.
Đây là chỗ v0 mập mờ nhất — "không rõ" của v0 vừa có nghĩa che nhiều, vừa có nghĩa không nhận ra.

Phần trăm tính trên vật thật, nhưng **box chỉ ôm phần nhìn thấy** (R-01). Hai điều này không mâu thuẫn:
một cái mô tả mức độ che, một cái quy định ranh giới box.

**`truncated`** — `yes` khi vật **bị mép ảnh cắt mất một phần**. Bị vật khác che **không** tính là
`truncated` (đó là việc của `occluded_level`). Ví dụ `yes`: `img_001` xe bán tải cụt ở mép phải,
`img_015` xe đỏ và xe bạc cụt hai bên, `img_040` người đội mũ bảo hiểm cụt ở đáy ảnh.

**`can_review`** — tôi tick khi: không chắc lớp (bán tải nắp cao, minibus hay van); không chắc vật có
thật không; biên box nhòe không xác định được (`img_015`); hoặc gặp ca guideline chưa nói tới.
Tick `can_review` **không** thay cho việc gán — vẫn phải vẽ box rồi mới tick.

## 3. Quy tắc vẽ box

| ID | Tình huống | Quy tắc | Ảnh đối chứng |
|---|---|---|---|
| **R-01** | Ranh giới box | Box ôm sát **phần nhìn thấy** của vật. Không đoán phần bị che, không nới ra cho "đẹp". Lý do: phần bị che là suy đoán, hai người đoán khác nhau thì IoU tụt mà không ai sai. | toàn bộ |
| **R-02** | Người đi xe máy / xe đạp | **Hai box riêng**: một `person`, một `motorcycle`/`bicycle`. Mỗi box ôm phần nhìn thấy của riêng nó, chồng lấn nhau là bình thường. Chở hai người thì hai box `person`. | `img_006` `img_008` `img_026` `img_035` |
| **R-03** | Xe chở xe | Gán `truck` cho xe đầu kéo, **và** gán từng ô tô trên sàn nếu cao ≥ 20 px và nhận ra được. Lý do: đó là xe thật, mô hình nhìn thấy thật. Trên `img_002` là 1 `truck` + các `car` trên hai tầng. | `img_002` |
| **R-04** | Vật không có thật | **Không gán**: tượng, ma-nơ-canh, đồ chơi, mô hình, hình in, tranh tường, phản chiếu trong gương hay kính. | `img_011` `img_017` `img_018` `img_020` `img_024` `img_026` `img_027` `img_028` `img_030` `img_033` `img_038` `img_040` |
| **R-05** | Xe thật hình dạng lạ | Vẫn gán bình thường nếu nó là **phương tiện thật đang chạy trên đường thật**, dù kiểu dáng giả tưởng hay hoá trang. Phép thử: nó tự di chuyển được và đang ở trong luồng giao thông. | `img_019` xe diễu hành vàng = `car`, người lái = `person`, nhưng con chuột khổng lồ **không** phải `person` |
| **R-06** | Xe chủ (ego) | Phần thân xe mình đang ngồi trong/trên, chiếm mép ảnh và không bao giờ là vật cần phát hiện: **không gán**. | `img_012` thùng xe bán tải ở tiền cảnh, `img_020` gương và thân xe |
| **R-07** | Hàng hoá chở trên xe | Box ôm **thân xe**, **không bao hàng hoá** chất cao hay thò ra ngoài. Lý do: hàng hoá không phải xe, và ranh giới đống hàng là thứ hai người không bao giờ vẽ giống nhau. | `img_035` bao tải trên nóc tuk-tuk, `img_029` thanh xà dài, `img_001` ống sắt trên thùng, `img_012` thùng carton |
| **R-08** | Vật bị cắt mép ảnh | Vẫn gán, box dừng ở mép ảnh, đặt `truncated=yes`. Còn thấy **dưới 20 px** chiều cao thì bỏ (R-09). | `img_001` `img_015` `img_032` `img_040` |
| **R-09** | Vật quá nhỏ | Cao **< 20 px** thì không gán riêng. Vài cái lẻ tẻ thì bỏ qua; dày đặc thì phủ `ignore_region` (R-10). | `img_025` người tắm nắng phía xa, `img_034` xe máy cuối đường |
| **R-10** | Đám đông, bãi xe dày | Khi **không tách được từng vật** bằng mắt: phủ một `ignore_region` ôm trọn cụm. Vật ở rìa cụm còn tách được thì vẫn gán riêng ở ngoài vùng. Không dùng `ignore_region` để né vật đơn lẻ dễ thấy. | `img_021` `img_030` `img_032`, khán giả sau rào `img_019` |
| **R-11** | `van` hay `truck` | Nhìn **thùng hàng có tách rời cabin không**: tách rời → `truck` (kể cả bán tải, kể cả có nắp che cứng); liền khối → `van`. | `img_036` xe xanh có thùng bạt = `truck`; `img_037` bán tải nắp cao = `truck`; `img_005` xe trắng = `van` |
| **R-12** | Ảnh mờ, nhoè chuyển động | Còn xác định được loại **và** biên thì gán, tick `can_review`. Nhoè tới mức thành vệt trong suốt, không xác định được biên: **không gán**, ghi `image_issues.csv`. | `img_014` xe máy thành vệt, `img_015` xe "bóng ma" giữa ảnh |
| **R-13** | Người ngồi trong xe kín | Người nhìn thấy qua kính xe buýt / ô tô: **không gán**. Lý do: không phải người tham gia giao thông độc lập, và mức nhìn thấy phụ thuộc hoàn toàn vào phản chiếu kính. Người ngồi trên xe hở (xe máy, tuk-tuk, xích lô) thì **có** gán. | không gán: `img_009` người trong minibus · có gán: `img_004` tài xế tuk-tuk, `img_023` khách xích lô |

## 4. Ca khó

Toàn bộ các ca dưới đây là do tôi tự xem 40 ảnh mà ghi lại.

| Ảnh | Tình huống | Rule | Quyết định | Lý do | Loại |
|---|---|---|---|---|---|
| `img_002` | Đầu kéo chở 8 ô tô trên 2 tầng | R-03 | Gán `truck` + từng `car` trên sàn | Xe thật, mô hình phải thấy | loại theo quy tắc |
| `img_004` | Hai tuk-tuk đỗ, tài xế ngồi trong | `tuk_tuk` R-13 | Gán 2 `tuk_tuk` + `person` tài xế | Xe hở, người nhìn rõ | loại theo quy tắc |
| `img_009` | Minibus, thấy rõ khách qua cửa kính | R-13 | Gán `bus`, **không** gán khách bên trong | Người trong xe kín không phải vật cần phát hiện riêng | **hỏi reviewer** |
| `img_011` | Tượng xe máy bằng sắt vụn + tượng Predator ở hội chợ | R-04 | Không gán tượng; vẫn gán người thật và xe van trắng phía xa | Tác phẩm điêu khắc, không chạy được | loại theo quy tắc |
| `img_012` | Tiền cảnh là thùng xe bán tải của người chụp | R-06 R-07 | Không gán xe chủ; không bao thùng carton | Xe chủ không phải mục tiêu phát hiện | **hỏi reviewer** |
| `img_014` | Hai người đi bộ nét, xe máy nhoè thành vệt | R-12 | Gán 2 `person`; xe máy nhoè còn nhận ra thì gán kèm `can_review` | Biên vệt nhoè không ổn định | **hỏi reviewer** |
| `img_015` | Một xe hoàn toàn trong suốt do phơi sáng | R-12 | Không gán xe bóng ma; gán 4 xe còn lại | Không xác định được biên | **hỏi reviewer** |
| `img_017` | Tượng đồng người cưỡi ngựa, có chóp nón giao thông | R-04 | Ảnh này **0 đối tượng** | Tượng và chim, không có vật thật thuộc lớp nào | loại theo quy tắc |
| `img_018` | Chân dung ứng viên in trên biển quảng cáo, sau là bãi xe | R-04 R-09 | Không gán chân dung; gán các ô tô trong bãi còn thấy đủ | Hình in | loại theo quy tắc |
| `img_019` | Xe diễu hành giả tưởng, người lái thật, linh vật chuột, khán giả đông | R-05 R-10 | Gán `car` + `person` lái; không gán chuột; khán giả phủ `ignore_region` | Xe thật đang chạy, linh vật là đồ hoá trang | **hỏi reviewer** |
| `img_020` | Toàn ảnh là gương chiếu hậu, xe chỉ thấy qua phản chiếu | R-04 R-06 | Ảnh này **0 đối tượng** | Phản chiếu không phải vật | loại theo quy tắc |
| `img_021` | Hàng trăm xe máy kẹt lúc chạng vạng | R-09 R-10 | Gán riêng các xe/người rõ ở tiền cảnh, phần xa phủ `ignore_region` | Không tách nổi từng vật | loại theo quy tắc |
| `img_022` | Ảnh đen trắng cũ, mô tô có thùng bên | `motorcycle` | Gán 1 `motorcycle` (gồm sidecar) + 1 `person` | Sidecar là bộ phận của xe | loại theo quy tắc |
| `img_023` | Xích lô đạp chở khách | `bicycle` R-02 | Gán `bicycle` + `person` đạp + `person` khách ngồi | Không động cơ, ba bánh | **hỏi reviewer** |
| `img_024` | Vỏ xe vàng gắn trên nóc xe đen trong bảo tàng | R-04 | Gán xe đen và các xe thật xung quanh; **không** gán vỏ xe trên nóc | Vỏ trưng bày, không phải xe hoạt động | **hỏi reviewer** |
| `img_026` | Người đi xe máy trước bức tranh tường vẽ cảng biển, tàu, cần cẩu | R-04 | Gán xe máy + 2 người; không gán gì trong tranh | Tranh vẽ | loại theo quy tắc |
| `img_027` | Bia mộ tạc hình xe bán tải bằng đá granite | R-04 | Ảnh này **0 đối tượng** | Tượng đá | loại theo quy tắc |
| `img_028` | Xe bán tải cổ thật, trên thùng là **tượng ngựa trắng** | R-04 R-07 | Gán `truck` và `car` đỏ phía sau; không gán tượng ngựa; box xe không bao tượng | Xe thật + tượng trên thùng = hàng hoá không thật | **hỏi reviewer** |
| `img_029` | Người đi xe máy và người đạp xe cùng khiêng thanh xà rất dài | R-02 R-07 | Gán `motorcycle` + `bicycle` + 2 `person`; box không bao thanh xà | Hàng hoá thò ra ngoài | loại theo quy tắc |
| `img_030` | Bãi xe máy dày đặc + ảnh người in trên poster tường | R-04 R-10 | Phủ `ignore_region` cho bãi xe; gán người mặc đồ xanh; không gán mặt người in | Hai quy tắc cùng áp dụng một ảnh | loại theo quy tắc |
| `img_033` | Ma-nơ-canh trong tủ kính + ảnh in người mẫu + phản chiếu | R-04 | Ảnh này **0 đối tượng** | Cả ba thứ đều không phải người thật | loại theo quy tắc |
| `img_035` | Tuk-tuk chở bao tải cao gấp đôi thân xe | R-07 | Gán `tuk_tuk` ôm **thân xe**, không bao bao tải | Đây là ca dễ lệch IoU nhất cả bộ | **hỏi reviewer** |
| `img_036` | Xe tải nhỏ xanh, thùng phủ bạt | R-11 | `truck` | Thùng tách rời cabin | **hỏi reviewer** |
| `img_037` | Bán tải gắn nắp thùng cứng rất cao, sau là xe buýt | R-11 | `truck` cho bán tải, `bus` cho hai xe xanh | Nắp che không biến thùng rời thành khoang liền | **hỏi reviewer** |
| `img_038` | Tủ kính cửa hàng đầy xe mô hình và thú nhồi bông | R-04 | Ảnh này **0 đối tượng** | Đồ chơi | loại theo quy tắc |
| `img_040` | Tuk-tuk, taxi, xe buýt cụt mép, **mặt người in trên cửa taxi** | R-04 R-08 | Gán xe thật; không gán mặt in; xe buýt `truncated=yes` | Hình in dán trên xe thật | loại theo quy tắc |

Năm ảnh tôi cho là **0 đối tượng**: `img_017`, `img_020`, `img_027`, `img_033`, `img_038`.
Theo `docs/CVAT_SETUP.md`, các ảnh này **vẫn phải nằm trong Task** và xuất ra COCO với 0 annotation.

## 5. QC và phiên bản

Ngưỡng lấy từ mục `rules` của `submission/taxonomy.json`, **không được sửa**:

| Đại lượng | Ngưỡng |
|---|---|
| Chiều cao box tối thiểu | 20 px |
| IoU ghép cặp khi so hai người | 0.5 |
| IAA — IoU trung bình | ≥ 0.85 |
| IAA — đồng thuận lớp | ≥ 95% |
| Tỉ lệ ảnh lỗi tối đa để Release | ≤ 2% (lô 40 ảnh: 1 ảnh lỗi là đã vượt) |

Ca mơ hồ: tick `can_review` khi gán, người chốt cuối là `999` tại họp Pha 2 và Pha 5.

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v0.555 | Thêm `bus`, `tuk_tuk`, `bicycle`; đặt ngưỡng % cho `occluded_level`; tách `truncated` khỏi che khuất; thêm 13 quy tắc R-01…R-13; ghi 26 ca khó | v0 không xử lý được tuk-tuk, xe buýt, vật không thật, hàng hoá quá khổ và đám đông |

---

## Phụ lục — 27 ảnh được giao cho mã 555

`img_001` `img_002` `img_004` `img_005` `img_007` `img_008` `img_010` `img_012` `img_013` `img_014`
`img_017` `img_018` `img_019` `img_021` `img_022` `img_023` `img_025` `img_027` `img_028` `img_029`
`img_031` `img_033` `img_034` `img_036` `img_037` `img_038` `img_040`

Guideline áp dụng cho **cả 40 ảnh**, không riêng 27 ảnh này.
