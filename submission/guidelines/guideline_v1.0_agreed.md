# Guideline gán nhãn chung — BugSquad — v1.0

- Phiên bản: v1.0.
- Ngày hợp nhất: 2026-11-16.
- Mã thành viên: 111, 555, 999.
- Điều phối/công bố bản dùng để gán: 111. Người review cuối: 999.
- Nguồn: ba [guideline cá nhân](individual/), template, ảnh gốc và ngưỡng cố định của dự án.
- Quyết định hợp nhất và nguồn của từng khác biệt: [PHASE2_REVIEW.md](PHASE2_REVIEW.md).
- Taxonomy đi kèm: [taxonomy.json](../taxonomy.json), version v1.0, **9 nhãn: 8 lớp vật + 1 vùng ignore**.
- Trạng thái: Bản chung được xây dựng theo yêu cầu của điều phối 111. Chưa ghi nhận chữ ký/xác nhận riêng của 555/999 hoặc Project CVAT đã được cấu hình. Trước lượt gán, 111 công bố phiên bản khóa để mọi người cùng áp dụng.

Bản này giải quyết các lựa chọn khác nhau trong Pha 1; không viết lại hồ sơ cá nhân và không coi mã R-xx của từng bản cá nhân là cùng một nghĩa. **R-01…R-15 dưới đây là mã của guideline chung v1.0**. Việc AI hỗ trợ đối chiếu và soạn văn bản được ghi trong hồ sơ review; không tạo biên bản họp, số đo IAA hay xác nhận người khác chưa thực hiện.

## 1. Mục tiêu và phạm vi

Gán bounding box cho mô hình phát hiện **người thật và phương tiện đường bộ thật**, gồm cả phương tiện cơ giới và xe đạp/xích lô đạp. Xét vật đang chạy, đỗ, được chở hoặc trưng bày khi đủ dấu hiệu hình ảnh; không yêu cầu chứng minh khả năng vận hành hay chuyển động từ một ảnh tĩnh.

Tập gồm 40 ảnh gốc img_001.jpg…img_040.jpg. Mỗi ảnh phải giữ trong luồng dữ liệu dù không có đối tượng. Bộ ảnh tuyển chọn luyện ca biên chưa được đánh giá tính đại diện cho vận hành; các ngưỡng QC là tiêu chí của lab, không phải chứng nhận an toàn cho hệ thống giao thông/xe tự lái.

Ưu tiên giảm bỏ sót người/xe thật mà vẫn tránh tạo nhãn trên hình in, phản chiếu, tượng và đồ chơi. Không gán “lớp gần nhất” chỉ vì có một vật thật; cần đủ bằng chứng cho một lớp hợp lệ. Động vật, xe đẩy không thuộc các lớp nêu dưới, đồ vật và hàng hóa rời nằm ngoài phạm vi.

## 2. Lớp và thuộc tính

### 2.1. Lớp — R-02

| Tên chính xác trong taxonomy/CVAT | Định nghĩa | Ví dụ | Phản ví dụ / dấu hiệu dễ nhầm |
|---|---|---|---|
| `car` | Ô tô con dạng sedan, hatchback, wagon, SUV hoặc taxi con; không có thùng hàng tách cabin kiểu pickup. Xe thật được chở hoặc trưng bày vẫn thuộc lớp này nếu đủ bằng chứng. | img_002: xe con được chở; img_023: xe con đen; img_040: taxi. | Pickup -> truck; toy038 và thân vỏ rời024 không là car. |
| `truck` | Xe có cabin và khu/thùng chở hàng tách biệt về kết cấu, gồm pickup, tải nhẹ và xe chở ô tô; nắp thùng, bạt hoặc thùng cao không biến xe thành van. | img_001/012: pickup; img_036: tải nhẹ; img_037: pickup nắp cao. | Van thân liền005; tượng pickup027. |
| `van` | Xe dạng van/minivan có cabin và khoang sau liền thân, không có thùng hàng tách cabin và không có hình thái bus/minibus đặc trưng. | Minivan img_005; xe trắng thân liền img_016. | Pickup có nắp037 vẫn truck; minibus009 là bus. |
| `bus` | Xe bus/minibus chở khách có thân khoang khách kéo dài, dãy cửa sổ và cửa hành khách theo kết cấu xe buýt; phân loại theo dấu hiệu nhìn thấy, không đoán số ghế. | img_009, img_031, img_037, img_040. | Không đoán số ghế; van005 không thành bus vì có cửa sổ. |
| `tuk_tuk` | Xe cơ giới ba bánh kiểu auto-rickshaw có khung/thân và cabin hoặc mái tích hợp; khác mô tô gắn sidecar và xích lô đạp. | img_004, img_016, img_035, img_040. | Sidecar022 -> motorcycle; xích lô đạp023 -> bicycle. |
| `motorcycle` | Xe máy, scooter hoặc mô tô có kết cấu nhận ra được, kể cả có sidecar gắn liền; mỗi cụm mô tô-sidecar là một đối tượng, không gồm người và hàng rời. | img_005/008/030; mô tô-sidecar022; xe máy bên trái029. | Xe đạp bên phải029; tượng mô phỏng xe; không suy xe chỉ từ mũ040. |
| `bicycle` | Xe đạp hoặc xe ba bánh không động cơ có kết cấu đạp nhận ra được, gồm xích lô đạp và xe đạp thồ; cả cụm xích lô đạp là một đối tượng, không gồm người và hàng rời. | Xe đạp img_003/006/029/039; xích lô đạp023; xe đạp hàng010. | Scooter/mô tô có động cơ -> motorcycle; tuk-tuk có động cơ -> tuk_tuk. |
| `person` | Người thật nhận diện được trong cảnh, gồm người đi bộ, người lái và hành khách trên mọi phương tiện, kể cả sau kính khi đủ bằng chứng; không gồm hình in, tượng, ma-nơ-canh hoặc người chỉ được suy đoán. | Người đi bộ001/034; người đi xe005/026; hàng người032. | Tượng017, ảnh in018, mannequin033; mũ/ghế không tự chứng minh có người. |
| `ignore_region` | Vùng kỹ thuật cho mục tiêu có chiều cao phần nhìn thấy dưới 20 px hoặc cụm mục tiêu không thể tách từng cá thể; không dùng cho vật ngoài phạm vi, lớp chưa chắc hoặc vật rõ còn gán riêng được. | Phần nhỏ xa/không tách được tại021 sau khi gán các vật rõ. | Không phủ cả hàng032 hoặc bãi030 còn nhận rõ; không dùng cho tượng/đồ chơi. |

**R-02 — Phân lớp theo dấu hiệu quan sát được:**

1. Kiểm tra vật trực tiếp trong cảnh và phạm vi bằng R-01.
2. Cabin–khu hàng tách biệt về kết cấu: truck, kể cả tải nhẹ/pickup gắn nắp cao.
3. Bus/minibus: có hình thái khoang khách kéo dài, dãy cửa sổ và cửa khách theo kết cấu xe buýt; không đoán số ghế hoặc lối đi bị khuất.
4. Xe van/minivan thân liền: van; xe con/SUV: car. Không quyết định bằng hãng xe, màu, biển số, chiều cao thùng hay cảm giác xe lớn/nhỏ.
5. Xe ba bánh có khung cabin tích hợp và động cơ: tuk_tuk; mô tô-sidecar: motorcycle; xích lô đạp: bicycle.
6. Chỉ một lớp trên mỗi vật. Thiếu bằng chứng phân biệt lớp thì áp dụng R-12.

Xe đạp thồ/xích lô đạp không tách thêm lớp: dùng bicycle cho cả kết cấu xe; người và hàng rời không thuộc box xe. Không ép mọi xe nhỏ hai bánh vào bicycle; cần thấy cấu trúc xe đạp/đạp hoặc có căn cứ phân biệt. Xe điện/hybrid khó phân loại từ ảnh được ghi review, không suy động cơ từ biển hiệu hay màu.

### 2.2. Thuộc tính và ranh giới — R-04, R-05

Ba thuộc tính áp dụng cho tám lớp vật; ignore_region không yêu cầu thuộc tính.

| Thuộc tính | Giá trị cố định | Nghĩa và điều kiện biên |
|---|---|---|
| occluded_level | 0 | p=0: không bị vật khác che trong miền ảnh. |
| occluded_level | 1 | 0 < p <= 30%. |
| occluded_level | 2 | 30% < p <= 70%. |
| occluded_level | 3 | p > 70%, nhưng vẫn nhận ra lớp/cá thể từ phần còn thấy; không dùng như mã “không biết”. |
| truncated | no | Không có bằng chứng vật bị biên ảnh cắt; chỉ tiếp xúc biên chưa tự động là bị cắt. |
| truncated | yes | Có bộ phận của vật tiếp tục ra ngoài biên ảnh và bị cắt. |
| can_review | false | Chưa có điểm cần reviewer giải quyết theo quy tắc đang dùng. |
| can_review | true | Có nghi ngờ có căn cứ về lớp, bản chất, biên, liên kết bộ phận hoặc mức che; ghi lý do riêng cho box/vùng. |

**R-04 — Che khuất:** p là phần diện tích của chính đối tượng bị vật khác che, ước lượng trên phần đối tượng liên quan trong miền ảnh; không tính phần ngoài ảnh, tự che do góc nhìn, bóng tối, nhòe hay phản xạ. Ước lượng mức che không cho phép vẽ box toàn thân giả định. Nếu ở gần ranh giới hoặc chưa chắc mức, chọn mức ước lượng tốt nhất, bật can_review và mô tả; không mặc định thành 0/3. Các khoảng không có chỗ hở với số thập phân: p=10.5% -> 1; p=30% -> 1; p=30.1% -> 2; p=70% -> 2; p=70.1% -> 3 nếu còn đủ bằng chứng.

Không đặt điều kiện bắt buộc nhìn thấy 30% cơ thể: một đầu/mặt người thật nhận ra được, đủ cao và xác định là cá thể riêng vẫn có thể là person dù bị che >70%. Mũ bảo hiểm/đốm tròn đơn độc không tự chứng minh có người. Không gán vật hoàn toàn khuất hoặc không nhận ra được.

**R-05 — Cắt mép:** occluded_level và truncated độc lập. Vật vừa bị vật khác che vừa bị biên cắt có thể mang cả hai thuộc tính. Box chỉ ở trong ảnh. Không nhầm chân bị xe che với chân ra ngoài ảnh. Kích thước tối thiểu vẫn đánh giá trên phần nhìn thấy theo R-09.

Thuộc tính occluded_level là trường riêng của Project. Cờ occluded có sẵn của CVAT (phím Q) không được script dùng thay thế nó.

## 3. Quy tắc vẽ box

### R-01 — Vật thật, mô phỏng, phản chiếu và ego

Gán người/xe thật đủ điều kiện kể cả đang đỗ, trong kho, được chở hoặc trưng bày. Không dùng “đang tham gia giao thông” như điều kiện bắt buộc; không loại cả ảnh có tượng, tranh tường hoặc bối cảnh bảo tàng.

Không gán hình in, hình trên màn hình, phản chiếu, tượng, ma-nơ-canh, đồ chơi hoặc thân vỏ/đạo cụ mô phỏng không phải một phương tiện hoàn chỉnh. Loại theo từng vật: bỏ chân dung biển quảng cáo018 nhưng giữ xe thật phía sau; bỏ tượng ngựa028 nhưng giữ pickup; bỏ tranh tường026 nhưng giữ hai người/xe máy phía trước.

Ego chỉ được loại khi có bằng chứng nó thuộc nền tảng mang camera, như gương/thân xe chủ ở020. Không suy ego chỉ vì xe ở sát máy, sát mép hoặc chiếm phần lớn ảnh. **Pickup tiền cảnh012 vẫn gán truck** vì ảnh đơn lẻ chưa chứng minh nó là ego.

Người thật mặc trang phục vẫn có thể là person. Không tự coi mascot là người bên trong hoặc mặc định mọi nhân vật hóa trang là vật giả: cần dấu hiệu người thật. Mascot chuột019 chưa đủ bằng chứng người bên trong thì không tạo person suy đoán; ghi vấn đề cấp ảnh nếu còn nghi ngờ. Nhân vật kim loại lớn011 là mô phỏng; không suy mọi mô tô trưng bày gần đó đều vận hành hay đều là tượng.

### R-03 — Box theo phần quan sát được

Hộp chữ nhật song song trục ảnh, nhỏ nhất bao các phần nhìn thấy chắc chắn thuộc cùng vật; không thêm padding có chủ ý và không suy phần bị che/ngoài ảnh. Nếu thấy các phần rời nhau qua khe rào hoặc do bị người che nhưng có căn cứ chúng thuộc cùng vật, dùng một box bao các phần đó.

Bao cấu kiện gắn liền quan sát được: bánh, gương đúng xe, tay lái, mái, nắp/khung che. Không kéo biên sang người, hàng rời hoặc vật lân cận. Hai box được chồng nhau; khoảng trống nằm trong hộp chữ nhật do tư thế/che khuất là bình thường. Không tách một vật thành nhiều box chỉ để tránh chồng.

Một bộ phận rời không liên kết được với vật/lớp, như gương tiền cảnh040, không đủ để dựng nguyên một xe; chuyển R-12.

### R-06 — Người và phương tiện là các đối tượng riêng

Mỗi người đủ điều kiện có một person; mỗi phương tiện đủ điều kiện có một box theo lớp xe. Không gộp người lái/hành khách vào box xe. Xe chở hai người thường có hai person và một box xe **chỉ khi từng đối tượng đều đủ bằng chứng/kích thước**.

Áp dụng cùng cách cho motorcycle, bicycle, tuk_tuk, car, van, bus và truck. Người nhìn qua kính xét R-13; người trên xích lô không bị bỏ chỉ vì kết cấu xe che. Không tạo xe từ tư thế người, tạo người từ ghế/kính chắn hoặc dùng số box của một nhóm minh họa làm tổng số vật trong ảnh.

### R-07 — Xe được chở, khung vận chuyển và thân vỏ

Ở002, xe chở cùng khung/sàn vận chuyển là một truck; từng ô tô hoàn chỉnh nhận ra được trên sàn là car riêng nếu đạt ngưỡng. Box truck không kéo lên chỉ để bao xe con nhô trên khung. Xe con không bao khung xe chở. Trạng thái hàng vận chuyển không loại một phương tiện hoàn chỉnh khỏi taxonomy.

Cụm thân vỏ vàng rỗng đặt trên nóc xe024 được xử lý là thân vỏ/đạo cụ, không gán car độc lập; các xe hoàn chỉnh thật khác trong ảnh vẫn gán. Không lấy việc một ô tô đang trưng bày hoặc không thấy động cơ làm lý do loại; trường hợp khác chưa xác định được là thân vỏ hay xe hoàn chỉnh áp dụng R-12.

### R-08 — Hàng rời khác cấu kiện xe

Không mở box xe theo bao tải, thùng carton, ống, thanh dài, hàng thồ hay tượng được chở, **kể cả đã buộc chặt hoặc chuyển động cùng xe**. Pickup012/028 và tuk-tuk035 áp dụng cùng nguyên tắc. Bao tải cao không đổi tuk_tuk thành truck.

Nắp thùng cứng, mái, khung che và bạt lắp trên cấu trúc xe thuộc box xe khi thấy được; pickup037 có nắp cao vẫn truck. Không dùng “gắn chắc” làm tiêu chí duy nhất vì hàng cũng có thể buộc chắc. Phân biệt theo chức năng/kết cấu xe với vật đang được vận chuyển. Phương tiện hoàn chỉnh được chở có nhãn riêng theo R-07.

### R-09 — Vật nhỏ, đám đông và vùng ignore

- Đo h theo chiều cao box **phần nhìn thấy** trên ảnh gốc trong data/images, không theo pixel màn hình khi zoom.
- h >= 20 px: xét gán lớp vật nếu đủ bằng chứng, kể cả phần thấy chỉ là đầu/mặt rõ của người. Đúng 20 px không loại vì kích thước.
- Mục tiêu nhận ra được nhưng h < 20 px: không tạo box lớp vật; đặt ignore_region nhỏ phù hợp, kể cả mục tiêu đơn lẻ. Không coi ảnh là sạch sau khi âm thầm bỏ mục tiêu nhỏ.
- Với nhóm dày, gán riêng mọi cá thể còn tách được; ignore chỉ phần thật sự không thể tách một–một sau khi xem ảnh gốc. Không có quy tắc ignore cả img_019/030/032 chỉ vì đông.
- Vùng ignore không được cố ý phủ các vật rõ đã gán. Tách vùng hoặc ghi vấn đề cấp ảnh nếu hình chữ nhật không thể tránh phủ vật rõ.
- Ngoài taxonomy chắc chắn, tượng/đồ chơi, không biết lớp hoặc nhòe đơn lẻ không tự động là lý do dùng ignore. Nhòe dẫn tới nhóm thật sự không tách được cá thể mới có thể đáp ứng điều kiện nhóm ở trên.
- Ghi lý do vùng ignore trong nhật ký/ghi chú CVAT; không tự thêm thuộc tính ngoài schema. Bất đồng ignore sẽ được đưa vào calibration; một người đặt vùng không xóa lỗi của người kia.

Script chỉ cảnh báo box nhỏ trong một số bước; người gán vẫn phải áp quy tắc này khi làm và khi review.

### R-10 — Ảnh mờ, đêm, mưa và đen trắng

Không loại ảnh chỉ vì tối, mờ hoặc đen trắng. Nếu lớp và biên vật đủ rõ, gán bình thường. Nếu biên/lớp ứng viên còn nghi ngờ nhưng vẫn có căn cứ vẽ box, gán can_review=true và ghi lý do. Chưa xác định được box/lớp đáng tin thì ghi vấn đề cấp ảnh theo R-12.

Không kéo box theo bóng ma/vệt màu không còn nhận ra bộ phận vật; không tạo nhãn cho vệt đèn phản xạ trên đường. Nhòe/thiếu sáng không phải occluded_level. 014/015 phải theo cùng điều kiện nhận diện, không dùng số xe viết sẵn trong bản cá nhân để quyết định.

### R-11 — Một đơn vị xe phức hợp

Một mô tô-sidecar gắn liền là một motorcycle, gồm kết cấu sidecar nhìn thấy. Một xích lô đạp là một bicycle, gồm kết cấu xe ba bánh và khoang chở khách; người/hàng vẫn riêng theo R-06/R-08.

Không đổi motorcycle-sidecar022 thành tuk_tuk vì có ba bánh. Thùng bên025 chỉ gộp vào phương tiện nếu liên kết cấu kiện nhìn thấy rõ; chưa rõ phải review. Không suy thêm một người từ kính/ghế của sidecar022.

### R-12 — Cách ghi vấn đề và giữ độc lập

1. Đã xác định ngoài phạm vi hoặc mô phỏng: loại theo quy tắc, không tạo box “để review” và không dùng ignore thay quyết định.
2. Có vật/box và một lớp hợp lệ có căn cứ nhưng chưa chắc biên/lớp/thuộc tính: gán tốt nhất, bật can_review, ghi ảnh/vùng, vấn đề và rule liên quan.
3. Chưa có đủ bằng chứng cho box hoặc lớp hợp lệ: không dựng box/lớp gần nhất. Ghi image, annotator, region, note vào nhật ký cá nhân để điều phối tổng hợp thành submission/qc/image_issues.csv **sau khi khóa nhãn**. note phải chỉ rõ vì sao cần chốt.

Ví dụ vấn đề cấp ảnh hợp lệ: kết cấu dạng mô tô011 chưa rõ là tác phẩm hay xe thật; mascot019 chưa có bằng chứng người bên trong; phần xe/gương040 không liên kết được. Không xóa việc còn nghi ngờ chỉ để đạt số lượng box hoặc IAA đẹp.

Trong Pha 3 chỉ dùng v1.0 đã khóa, không đổi taxonomy và không xem nhãn người khác/hỏi cách gán ảnh đang làm. Hỗ trợ kỹ thuật được phép; bất đồng nội dung giữ tới calibration.

### R-13 — Nhìn xuyên kính khác phản chiếu

Người thật/xe thật thấy xuyên kính hoặc khe rào được gán khi đủ bằng chứng, kích thước và biên. Không loại toàn bộ người ở trong xe kín; tài xế/hành khách đủ rõ sau kính009 áp dụng cùng định nghĩa person như trên xe hở004.

Không suy người từ ghế, hình dán trên kính, vùng tối hoặc vật giống đầu. Phản chiếu/hình in bị loại theo R-01. Kính trong không tự tính là che; kết cấu khung cửa/rào thực sự che được xét R-04.

### R-14 — CVAT, COCO và ảnh không có nhãn

Dùng Rectangle. Các lớp/thuộc tính phải từ [taxonomy v1.0](../taxonomy.json), tạo bằng scripts/make_cvat_labels.py rồi nạp [labels_generated.json](../../cvat/labels_generated.json) qua Project -> Labels -> Raw.

COCO bbox là [x,y,w,h], gốc trên trái theo pixel, w/h dương, không vượt ảnh; không nhầm với [x1,y1,x2,y2]. Ảnh trống vẫn có trong images, annotations có thể rỗng. 017/020/027/033/038 là các ca âm tính tham chiếu sau khi kiểm không có mục tiêu thật khác.

Phân công chính thức do split_assign.py với mã 111,555,999 sinh khi bắt đầu Pha 3; mỗi ảnh K=2, tổng 80 lượt trên 40 ảnh. Không dùng phụ lục phân công trong bản cá nhân làm thay assignment.csv.

Mỗi người tạo Task riêng từ đúng ảnh được giao, xuất COCO 1.0, tắt Save images, lưu submission/labels/<mã>.json rồi chạy validate_labels. Người chưa có box ghi vấn đề, không bỏ ảnh khỏi Task/COCO. Chỉ tập hợp nhãn sau khi mọi người khóa bản độc lập.

### R-15 — Cơ thể, trang phục và đồ mang

Box person bao phần cơ thể quan sát được, tóc, quần áo, mũ/nón bảo hộ đang đội và giày dép. Không kéo biên chỉ để bao túi/ba lô, giỏ, đòn gánh, ô cầm tay hoặc hàng dài. Đồ mang che người là vấn đề che khuất của người; việc một vật rời tình cờ nằm trong hình chữ nhật không phải lỗi.

Thử cùng quy tắc trên003/010/029. Người và đồ mang không được gộp thành một vật chỉ vì đang cùng di chuyển.

## 4. Ca khó và phép thử chung

Các dòng dưới là hướng dẫn đối chiếu quy tắc, không phải COCO, tổng box chuẩn hay kết quả QC đã thực hiện. Mã V01…V23 chỉ là ca tham chiếu v1.0, không phải case_id do qc_compare.py sinh.

| Ca | Ảnh tham chiếu | Quyết định chung | Căn cứ / rule |
|---|---|---|---|
| V01 | [001](../../data/images/img_001.jpg) | Pickup -> truck; người thật xét riêng; phần bị che không dựng lại, phần ra ngoài biên dùng truncated. | R-02, R-03, R-04, R-05, R-06 |
| V02 | [002](../../data/images/img_002.jpg) | Xe chở + khung vận chuyển -> truck; từng xe con hoàn chỉnh -> car. Không dùng một truck bao cả hàng làm thay nhãn car. | R-03, R-07 |
| V03 | [003](../../data/images/img_003.jpg) / [010](../../data/images/img_010.jpg) / [029](../../data/images/img_029.jpg) | Người riêng, xe đạp/xích lô đạp -> bicycle, xe máy -> motorcycle; không kéo box theo giỏ/đòn/thanh hàng. | R-02, R-06, R-08, R-15 |
| V04 | [004](../../data/images/img_004.jpg) / [016](../../data/images/img_016.jpg) | Tuk-tuk -> tuk_tuk; tài xế nhìn thấy đủ điều kiện -> person riêng. | R-02, R-06 |
| V05 | [005](../../data/images/img_005.jpg) / [026](../../data/images/img_026.jpg) | Một box mỗi người đủ dấu hiệu/kích thước, một box mỗi xe nhận ra được. Không bỏ đầu người rõ chỉ vì không thấy thân; không ấn định tổng box cả ảnh. | R-03, R-04, R-06, R-09 |
| V06 | [006](../../data/images/img_006.jpg) / [023](../../data/images/img_023.jpg) / [039](../../data/images/img_039.jpg) | Xe đạp/xích lô đạp -> bicycle; person trên xe vẫn riêng; các motorcycle/car khác không bị bỏ theo nhóm xe đạp. | R-02, R-06, R-11 |
| V07 | [007](../../data/images/img_007.jpg) / [036](../../data/images/img_036.jpg) / [037](../../data/images/img_037.jpg) | Cabin–thùng riêng -> truck, kể cả tải nhẹ hoặc nắp/bạt cao. | R-02, R-08 |
| V08 | [009](../../data/images/img_009.jpg) / [031](../../data/images/img_031.jpg) / [040](../../data/images/img_040.jpg) | Bus/minibus -> bus; người thật sau kính đủ điều kiện được xét person. | R-02, R-06, R-13 |
| V09 | [011](../../data/images/img_011.jpg) | Không gán nhân vật kim loại lớn; vẫn gán người/xe thật nhận ra được. Kết cấu dạng mô tô chưa phân biệt được tác phẩm/xe thật thì review theo vùng. | R-01, R-12 |
| V10 | [012](../../data/images/img_012.jpg) | Pickup tiền cảnh -> truck, không tự loại là ego; không kéo biên theo thùng hàng nhô ra. | R-01, R-02, R-08 |
| V11 | [013](../../data/images/img_013.jpg) / [014](../../data/images/img_014.jpg) / [015](../../data/images/img_015.jpg) | Không nhầm tối/nhòe với che. Vật đủ bằng chứng thì gán; chưa có biên/lớp thì image issue; không tạo box cho vệt phản chiếu. | R-04, R-05, R-10, R-12 |
| V12 | [017](../../data/images/img_017.jpg) / [027](../../data/images/img_027.jpg) / [038](../../data/images/img_038.jpg) | Tượng người/xe, xe đồ chơi ngoài phạm vi; ảnh âm tính vẫn giữ. | R-01, R-14 |
| V13 | [018](../../data/images/img_018.jpg) | Không gán chân dung in; gán xe thật sau hàng rào nếu đủ điều kiện, một box cho các phần thấy cùng xe. | R-01, R-03, R-04, R-13 |
| V14 | [019](../../data/images/img_019.jpg) | Xe cỡ thật diễu hành -> car; người lái/khán giả rõ -> person. Mascot chưa có bằng chứng người thì không suy person, ghi vấn đề nếu nghi ngờ. Không ignore toàn bộ khán giả. | R-01, R-02, R-09, R-12 |
| V15 | [020](../../data/images/img_020.jpg) / [033](../../data/images/img_033.jpg) | Loại phản chiếu, ego rõ, hình in và ma-nơ-canh; không dùng ignore để thay quyết định loại. | R-01, R-13, R-14 |
| V16 | [008](../../data/images/img_008.jpg) / [021](../../data/images/img_021.jpg) / [030](../../data/images/img_030.jpg) | Xe đỗ vẫn thuộc mục tiêu; chỉ ignore phần nhỏ/không tách được. Không coi tất cả xe trong030 là bicycle. | R-01, R-02, R-09 |
| V17 | [022](../../data/images/img_022.jpg) | Mô tô-sidecar là một motorcycle; không tự thêm person từ kính/ghế, xét dấu hiệu người độc lập. | R-02, R-06, R-11, R-12 |
| V18 | [024](../../data/images/img_024.jpg) | Cụm thân vỏ vàng rỗng không gán car; xe đen và các xe hoàn chỉnh thật vẫn xét car. | R-01, R-03, R-07 |
| V19 | [025](../../data/images/img_025.jpg) | Kích thước trên ảnh gốc; thùng bên chỉ gộp nếu rõ là kết cấu gắn đúng xe; thiếu căn cứ thì review. | R-09, R-11, R-12 |
| V20 | [028](../../data/images/img_028.jpg) | Pickup thật -> truck; không kéo box theo tượng ngựa. Xe con thật phía sau vẫn xét car. | R-01, R-02, R-08 |
| V21 | [032](../../data/images/img_032.jpg) | Hàng người còn tách được gán riêng; car cắt mép vẫn xét gán, không mặc định là ego; không phủ ignore cả hàng người. | R-01, R-05, R-09 |
| V22 | [034](../../data/images/img_034.jpg) | Người đi bộ và người lái đều person, phương tiện riêng; cắt mép không tự loại nếu đủ điều kiện. | R-05, R-06, R-09 |
| V23 | [035](../../data/images/img_035.jpg) / [040](../../data/images/img_040.jpg) | Tuk-tuk035 không đổi lớp vì bao tải và không bao hàng/người. 040 không dựng motorcycle từ mũ/gương rời, không gán chân dung dán trên taxi. | R-01, R-02, R-06, R-08, R-12 |

**Phép thử trước khi khóa:** cùng giải thích cặp người-xe005/026; nhóm xe đạp003/023/029; hàng012/035; pickup016/037 so với van005; người sau kính009 so với phản chiếu020; vùng dày021/030 so với hàng người032. Cùng Rule ID phải dẫn tới cùng cách chọn vật/lớp/biên/thuộc tính; ca thực sự còn nghi ngờ được ghi review theo R-12.

## 5. QC, phiên bản và khóa đặc tả

### 5.1. Ngưỡng cố định

Giữ nguyên toàn bộ rules của taxonomy v0; không điều chỉnh để làm đẹp số đo.

| Khóa rules | Giá trị | Ý nghĩa |
|---|---|---|
| min_box_height_px | 20 | Chiều cao phần nhìn thấy trên ảnh gốc. |
| match_iou | 0.5 | Ghép box một–một không phân biệt lớp. |
| large_px | 50 | Script dùng trung bình chiều cao hai box để chọn ngưỡng lớn/nhỏ. |
| iou_thr_large | 0.85 | Cờ lệch box lớn. |
| iou_thr_small | 0.70 | Cờ lệch box nhỏ. |
| iaa_iou | 0.85 | IoU trung bình trên các cặp đã ghép. |
| iaa_class | 0.95 | Đồng thuận lớp trên các cặp đã ghép. |
| error_rate_img_max | 0.02 | Tỉ lệ ảnh lỗi tối đa khi xét bàn giao. |

Chỉ đo IAA khi đủ 100% lượt phân công. Box không ghép được đếm riêng, không có cặp thì N/A; IAA cao không chứng minh không có vật cả nhóm cùng bỏ sót hoặc cùng gán sai. Giữ số đo trước calibration.

QC cuối mặc định duyệt toàn lô. Hold khi thiếu bằng chứng/khác phiên bản/còn can_review hoặc dùng vòng demo; Rework khi ảnh lỗi >2% hoặc một loại lỗi lặp ít nhất ba lần và chiếm ít nhất 50% tổng lỗi; chỉ Release khi đủ bằng chứng và reviewer xác nhận. Với 40 ảnh, 1/40=2.5% đã vượt ngưỡng. Lỗi theo ảnh, annotation hiện có và vật bỏ sót phải báo riêng, kèm mẫu số.

### 5.2. Khóa v1.0 và thay đổi sau đó

111 điều phối, 999 review bản cuối theo TEAM.md; không coi vai trò reviewer như bằng chứng đã duyệt. Mỗi thành viên dùng cùng bản guideline, taxonomy và JSON CVAT; không tự chỉnh quy tắc trong lượt độc lập. Mã R-xx cá nhân chỉ dùng đối chiếu nguồn; khi gán/QC dẫn mã của bản chung này.

Trước khi bắt đầu Pha 3:
- [x] Đã đối chiếu cả ba guideline và ghi 20 chủ đề trong PHASE2_REVIEW.md.
- [x] Đã tạo guideline chung, taxonomy version v1.0 và giữ nguyên rules.
- [ ] Nhóm xác nhận đã đọc cùng phiên bản và hoàn thành phép thử ảnh chung.
- [ ] Project CVAT đã nạp JSON sinh từ taxonomy, kiểm đúng chín nhãn và thuộc tính.
- [ ] 111 công bố commit/phiên bản khóa cho lượt gán độc lập.

Nếu cần chỉnh trước khi khóa, cập nhật nhất quán guideline/taxonomy/JSON và lưu dấu vết. Sau khi bắt đầu gán, không sửa bản v1.0 dùng đo IAA; giải quyết ở calibration bằng guideline_v1.1_calibrated.md và guideline_change.json. Đổi nghĩa về chọn vật/lớp/box/thuộc tính yêu cầu semantic, quyết định phạm vi lo và kiểm lại cả lô; đổi taxonomy thì tăng phiên bản, giữ rules và sinh cấu hình CVAT cuối.

### 5.3. Lịch sử

| Phiên bản | Nội dung | Căn cứ |
|---|---|---|
| v0 | Bản khởi đầu sáu nhãn, chưa đủ ca biên. | guideline/guideline_v0_starter.md và taxonomy v0. |
| Các bản cá nhân | Các quy tắc độc lập của 111/555/999, giữ nguyên nguồn. | Ba file trong individual/. |
| v1.0 | Hợp nhất chín nhãn, chọn một hệ ngưỡng che và R-01…R-15; giải quyết xe chở xe, hàng rời, người sau kính, ignore và ego. | PHASE2_REVIEW.md; đối chiếu tài liệu/ảnh gốc theo yêu cầu 111. Chưa tạo nhãn hay số QC. |
