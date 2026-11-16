# Guideline gán nhãn — đề xuất cá nhân 111 — v0.2-111

- Nhóm: BugSquad
- Mã thành viên: 111
- Ngày: 2026-11-16
- Phiên bản: v0.2-111 (đề xuất cá nhân trước cuộc họp CP2)

## 1. Mục tiêu

Tạo đặc tả bounding box cho mô hình phát hiện **người thật và phương tiện cơ giới đường bộ** trong ảnh 2D. Nhãn cần giúp mô hình phân biệt đối tượng thực với hình in, phản chiếu, tượng và đồ chơi; phát hiện được cả vật đứng yên, đỗ, di chuyển hoặc bị che/cắt một phần khi còn đủ bằng chứng.

Đề xuất cá nhân giới hạn phương tiện ở car, truck, van, bus, tuk_tuk và motorcycle. Xe đạp, xích lô đạp, xe đẩy, động vật và hàng hóa rời nằm ngoài phạm vi này; **người sử dụng các phương tiện ngoài phạm vi vẫn được xét person**. Việc giới hạn phạm vi phải được nhóm xác nhận tại CP2, vì câu “mọi người và phương tiện” của v0 chưa phân định điều đó.

Ưu tiên tránh: bỏ sót người vì bị phương tiện che; gộp nhiều người thành một vật; bỏ xe được chở; gán nhãn lên tượng/hình in; ép lớp mới vào lớp gần giống; kéo box theo hàng hóa hoặc suy ra bộ phận không nhìn thấy.

Bộ 40 ảnh được tuyển chọn để luyện ca biên, không chứng minh tính đại diện cho dữ liệu vận hành. Nội dung này phục vụ bài lab, không phải tuyên bố dữ liệu đã đủ dùng cho hệ thống an toàn giao thông hay xe tự lái.

## 2. Lớp và thuộc tính

### 2.1. Các lớp đề xuất

Mỗi đối tượng chỉ thuộc một lớp. Tên dưới đây là **đề xuất cho cuộc họp**, không thay đổi taxonomy v0 trong repository.

| Lớp | Định nghĩa và dấu hiệu phân biệt | Ví dụ trong ảnh | Phản ví dụ / dễ nhầm |
|---|---|---|---|
| car | Xe con chở người dạng sedan, hatchback, wagon hoặc SUV; cabin/khoang hành khách là khối chính, không có thùng hàng tách biệt kiểu pickup. | Xe con trên xe chở ở img_002; SUV bên trái img_012; xe đen img_024; taxi img_040. | Pickup là truck; xe đồ chơi img_038 không phải car; màu sơn hay kiểu hoạt hình không tự quyết định lớp. |
| truck | Xe có cabin và khu/thùng chở hàng tách biệt về kết cấu, gồm pickup, tải nhẹ và xe chở ô tô; gắn nắp/bạt trên thùng không biến nó thành van. | img_001, img_002, img_007, img_012, img_016, img_028, img_036, img_037. | Xe thân liền kiểu minivan img_005; tượng pickup img_027. |
| van | Xe thân hộp liền giữa cabin và khoang sau, dạng van/minivan; không có thùng tách biệt kiểu pickup và không có hình thái khoang/cửa hành khách đặc trưng của bus. | Minivan phía sau người đi xe ở img_005; xe thân liền ở img_016. | Pickup gắn nắp cao img_037 vẫn là truck; bus nhỏ img_009 không tự đổi thành van vì kích thước. |
| bus (bổ sung) | Xe chuyên chở nhiều hành khách dạng bus/minibus, có thân khoang khách kéo dài, dãy cửa sổ và cửa hành khách theo cấu trúc xe buýt; dựa hình thái thấy được, không đoán số ghế từ ảnh. | img_009, img_031, img_037, img_040. | Minivan img_005; thùng kín cao của pickup img_037. |
| tuk_tuk (bổ sung) | Xe cơ giới ba bánh có khung/thân và cabin hoặc mái tích hợp cho khu người lái/người ngồi/hàng; khác mô tô gắn sidecar. | img_004, img_016, img_035, img_040. | Xích lô đạp img_023; motorcycle gắn sidecar img_022. |
| motorcycle | Xe máy, scooter hoặc mô tô có kết cấu xe máy nhận ra được; gồm mô tô gắn sidecar cố định theo R-11. Không cần có người ngồi. | img_005, img_008, img_022, xe máy bên trái img_029, img_030. | Xe đạp bên phải img_029; tuk-tuk img_004; vật mô phỏng chưa rõ bản chất ở img_011. |
| person | Người thật có dấu hiệu cơ thể đủ để nhận ra là người, gồm người đi bộ, đứng/ngồi, người lái và hành khách trên phương tiện; gồm cả trẻ em. | img_001, img_003, img_005, img_026, img_032, img_034. | Tượng người img_017; chân dung in img_018; ma-nơ-canh img_033. Mũ bảo hiểm/ghế trống không tự chứng minh có người. |
| ignore_region | Vùng kỹ thuật để bỏ qua mục tiêu quá nhỏ hoặc vùng không thể tách từng cá thể theo R-09; không phải một lớp vật và không dùng để che quyết định khó. | Phần xa nhỏ trong img_021 nếu đạt điều kiện R-09. | Không phủ cả hàng người còn tách được ở img_032; không dùng cho tượng/đồ chơi đã xác định ngoài phạm vi. |

**R-02 — Thứ tự phân lớp:** kiểm tra vật thật và phạm vi trước; sau đó xét kết cấu xe. Pickup/cabin–thùng riêng → truck; bus/minibus hình thái rõ → bus; van thân liền → van; xe con/SUV → car. Với ba bánh, phân biệt khung cabin tích hợp (tuk_tuk) và mô tô-sidecar (motorcycle). Nếu thiếu dấu hiệu quyết định, áp dụng R-12 thay vì dùng hãng xe, biển số, màu sắc hay số bánh duy nhất để ép lớp.

Đề xuất CP2: giữ năm lớp vật của v0, thêm bus và tuk_tuk, giữ ignore_region; tổng cộng tám nhãn. Xe đạp/xích lô đạp được loại có quy tắc, không tự đổi thành motorcycle/tuk_tuk. Nếu nhóm chọn mở rộng phạm vi này thì phải sửa guideline và taxonomy đồng thời trước khi khóa v1.0.

### 2.2. Thuộc tính

| Thuộc tính | Giá trị | Định nghĩa thao tác |
|---|---|---|
| occluded_level | 0 | Không có phần thuộc vật bị đối tượng khác che trong miền ảnh; tự che do góc nhìn không tính. |
| occluded_level | 1 | Tỉ lệ phần thuộc vật bị che ước lượng p thỏa 0 < p <= 30%. |
| occluded_level | 2 | 30% < p <= 70%. |
| occluded_level | 3 | p > 70%, nhưng phần còn thấy vẫn đủ nhận ra đối tượng/lớp; không dùng mức 3 như mã “không biết”. |
| truncated | no | Không có bộ phận của vật tiếp tục ra ngoài biên ảnh theo bằng chứng quan sát được. Chỉ chạm mép nhưng chưa bị cắt không tự động là yes. |
| truncated | yes | Có bằng chứng vật bị biên ảnh cắt; chỉ vẽ phần nằm trong ảnh. |
| can_review | false | Quyết định thuộc phạm vi quy tắc rõ, chưa có điểm cần reviewer giải quyết. |
| can_review | true | Có nghi ngờ có căn cứ về lớp, mức che, biên box hoặc cách áp dụng quy tắc; cần ghi lý do cụ thể. |

**R-04 — Che khuất:** p là ước lượng phần diện tích của chính đối tượng bị vật khác che trong phần miền ảnh có liên quan; không tính phần ra ngoài ảnh, bóng tối, nhòe, phản xạ hoặc tự che do góc nhìn. Đây là ước lượng phục vụ thuộc tính, **không cho phép vẽ box toàn thân giả định**. Ví dụ người sau bàn/xe ở img_001, người chồng nhau ở img_005 và xe sau hàng rào ở img_018. Nếu không chắc khoảng p, ghi mức ước lượng tốt nhất kèm can_review=true và mô tả; reviewer phải chốt lại, không mặc định thành 0 hay 3. Tại đúng p=30% chọn 1; p=70% chọn 2.

**R-05 — Cắt mép:** truncated và occluded_level độc lập. Vật có thể vừa bị che vừa bị cắt. Ảnh img_015, img_026 và img_034 là các ca kiểm lại biên; không suy ra truncated chỉ vì một bộ phận không xuất hiện khi nó thực tế bị vật khác che.

can_review là cờ quy trình, không phải một lớp hay chất lượng thật của vật. Vùng ignore_region không yêu cầu ba thuộc tính này. Khi làm CVAT phải dùng thuộc tính occluded_level riêng; cờ occluded mặc định của CVAT không thay thế nó.

## 3. Quy tắc vẽ box

### R-01 — Vật thật, vật mô phỏng, phản chiếu và ego

- Gán vật thật đủ điều kiện dù đang chạy, đỗ, được chở, xuất hiện trong kho hoặc được trưng bày. Không loại cả img_024 chỉ vì chụp trong khu trưng bày.
- Không gán hình in, màn hình hiển thị hình vật, tượng, ma-nơ-canh, đồ chơi hoặc phản chiếu. Áp dụng cho img_017, chân dung img_018, img_020, img_033, img_038.
- Xét từng vật: loại chân dung trên biển img_018 nhưng vẫn xét xe thật sau hàng rào; loại tượng ngựa img_028 nhưng vẫn gán pickup thật.
- Không gán bộ phận xe mang camera (ego) hay gương của nó như xe ngoài cảnh. Không xác định ego chỉ vì một xe nằm gần mép ảnh; phải có bằng chứng là bộ phận của nền tảng camera, như img_020.
- Không suy ra có người bên trong tượng/mascot từ hình dáng. Với nhân vật chuột img_019, giữ câu hỏi về bản chất cho reviewer; không tự khẳng định là người mặc trang phục.
- Không dùng ignore_region để thay việc loại chắc chắn theo phạm vi.

### R-03 — Box của phần quan sát được

Dùng hộp chữ nhật song song trục ảnh nhỏ nhất bao **các phần nhìn thấy thuộc cùng một đối tượng**. Nếu phần nhìn thấy rời nhau do bị che nhưng có bằng chứng là cùng vật, dùng một box bao các phần ấy; không chia một xe thành nhiều box theo khe hàng rào.

Không kéo biên theo bộ phận bị che hoàn toàn/ở ngoài ảnh. Không thêm padding có chủ ý. Bao gương, bánh, tay lái, mái và cấu kiện gắn liền khi còn nhìn thấy và gắn chắc với đúng xe. Khoảng trống bên trong hình chữ nhật do tư thế/che khuất là hệ quả của box chữ nhật, không phải lý do chia một vật.

Nếu chỉ thấy một bộ phận không đủ xác định nó thuộc vật nào/lớp nào, áp dụng R-12. Ví dụ mũ của người và gương rời img_040 không tự chứng minh một motorcycle có thể xác định box.

### R-06 — Tách người và phương tiện

Mỗi người thật là một person riêng; mỗi phương tiện đủ điều kiện là một đối tượng riêng. Box person không bao phương tiện và box phương tiện không bao người chỉ vì họ đang ngồi lên nó; hai box được phép chồng nhau.

Một motorcycle chở hai người có thể tạo ba box: hai person và một motorcycle, **nếu từng vật đều nhận ra được và đạt ngưỡng riêng**. Áp dụng cùng cách cho img_005 và img_026. Với người đi xe đạp/xích lô, người vẫn là person dù phương tiện nằm ngoài phạm vi. Không tạo box xe chỉ từ tư thế người đang ngồi hoặc đang đội mũ.

### R-07 — Xe chở xe và thân vỏ rời

Xe chở ô tô img_002 là một truck; phần khung/sàn vận chuyển của xe chở thuộc truck. Các xe con hoàn chỉnh được chở là car riêng; trạng thái đang được chở không loại chúng khỏi tập đối tượng.

Biên truck không mở rộng chỉ để bao các xe con nhô lên; biên car không bao khung của xe chở. Box của các xe có thể chồng nhau trong ảnh.

Cụm thân vỏ/đạo cụ không tạo thành một xe hoàn chỉnh không được gán như xe độc lập. Với cụm vàng rỗng trên nóc xe img_024, đề xuất loại cụm này theo tiêu chí đó, giữ xe đen và các xe hoàn chỉnh khác. Sự phân biệt thân vỏ với xe hoàn chỉnh ở ca này cần nhóm xác nhận, không khẳng định khả năng vận hành cơ học chỉ từ ảnh.

### R-08 — Hàng hóa, nắp thùng và pickup

Pickup luôn đề xuất truck kể cả lắp nắp thùng cao hoặc mái cải tạo: img_001, img_007, img_012, img_016, img_028, img_037. Tải nhẹ có cabin và thùng riêng img_036 cũng là truck.

Nắp thùng, bạt/khung che gắn với xe và cấu kiện lắp cố định thuộc box của xe khi nhìn thấy. Thùng giấy, bao tải, rau quả, thanh dài hoặc tượng đang được chở là hàng rời; không kéo box theo phần hàng nhô ra. Hàng hóa không đổi tuk_tuk img_035 thành truck. Riêng phương tiện hoàn chỉnh đang được chở vẫn gán riêng theo R-07.

Không gọi van chỉ vì xe có thùng kín, cao hoặc nhỏ. Khi chưa phân biệt cấu kiện cố định và hàng rời, đánh dấu review.

### R-09 — Vật nhỏ, đám đông và ignore_region

- Đo trên ảnh gốc trong data/images/, không theo kích thước hiển thị sau zoom.
- Gọi h là chiều cao box phần quan sát được theo R-03: h >= 20 px được xét gán riêng nếu đủ bằng chứng; h < 20 px đưa vào vùng ignore nhỏ phù hợp. Tại đúng 20 px không loại vì kích thước.
- Với nhóm dày, gán riêng mọi cá thể còn xác định một–một được. Chỉ khoanh vùng không tách được các cá thể mục tiêu sau khi đã kiểm ảnh gốc; ghi rõ lý do vùng đó.
- Vùng ignore phải nhỏ nhất hợp lý cho phần bị bỏ qua, không bao trùm có chủ ý các đối tượng rõ đã gán. Nếu hình chữ nhật ignore không tránh được việc phủ vật rõ, tách vùng hoặc đưa ca cho reviewer.
- Không phủ cả bãi xe img_030 hay hàng người img_032 chỉ vì đông. Img_021 cần phân biệt vùng tiền/trung cảnh với vùng xa.
- Một người đặt ignore không tự xóa lỗi của người kia: bất đồng vùng ignore cần calibration. Script cảnh báo box nhỏ hỗ trợ rà soát, không thay quyết định bằng mắt.
- Vật ngoài taxonomy, tượng hay đồ chơi không được chuyển thành ignore chỉ vì không có lớp tương ứng.

### R-10 — Thiếu sáng và nhòe chuyển động

Ảnh đêm, màu đen trắng, mưa hay chuyển động không tự làm vật ngoài phạm vi. Gán khi còn nhận ra lớp và biên lõi của vật; không tạo box riêng cho phản xạ đèn/bóng trên đường và không kéo box theo vệt nhòe không còn xác định là bộ phận vật.

Không diễn giải nhòe/thiếu sáng thành occluded_level cao. Nếu thiếu bằng chứng để xác định lớp hoặc biên, đánh dấu can_review khi có box ứng viên hợp lý; nếu chưa thể xác định box, ghi vấn đề cấp ảnh với vùng và lý do. Không lấy một xe rõ khác để suy ra hình học cho xe nhòe.

### R-11 — Mô tô-sidecar và bộ phận bên

Mô tô gắn sidecar cố định là một motorcycle, box bao cả cấu kiện sidecar nhìn thấy. Không tách sidecar thành một motorcycle thứ hai và không đổi thành tuk_tuk chỉ vì có ba bánh.

Tuk_tuk là thiết kế thân/cabin tích hợp riêng theo R-02. Xe đạp/xích lô đạp ngoài phạm vi dù cũng có hai/ba bánh. Với thùng bên img_025, chỉ gộp khi thấy nó liên kết cố định với đúng xe; liên kết chưa rõ phải review. Hàng rời đặt trong sidecar không làm box lớn hơn phần kết cấu xe.

### R-12 — Ca chưa chắc và vấn đề cấp ảnh

Phân biệt hai trạng thái:

1. **Loại có quy tắc:** đã xác định là tượng, hình in, phản chiếu, đồ chơi hoặc phương tiện ngoài phạm vi. Không tạo box mục tiêu và không bật can_review chỉ để thể hiện đã xem.
2. **Chưa đủ bằng chứng:** còn nghi ngờ bản chất, lớp, biên hoặc liên kết bộ phận. Nếu có box và lớp ứng viên có căn cứ thì gán can_review=true; nếu chưa vẽ được box đáng tin, ghi image, annotator=111, region, note để chuyển cho điều phối sau khi khóa nhãn.

Không tạo lớp unknown ngoài taxonomy đã khóa, không sửa taxonomy trong lượt gán độc lập và không mở nhãn người khác để giải quyết ca khó. Các ca giữ câu hỏi ở Pha 1: kết cấu dạng mô tô img_011, mascot img_019, cụm thân vỏ img_024 và phần xe tiền cảnh img_040. Nhận diện ranh giới chưa chắc là kết quả có giá trị; không biến chúng thành khẳng định vật lý không kiểm được từ ảnh.

### R-13 — Nhìn xuyên kính khác phản chiếu

Người/xe thật nhìn xuyên kính hoặc khe hàng rào vẫn xét gán nếu đủ dấu hiệu, kích thước và ranh giới. Phần bị kết cấu cửa/rào che được xét theo R-04. Kính trong không tự tính là che khuất.

Hình người/xe chỉ xuất hiện dưới dạng phản chiếu, hình in hoặc màn hình thì loại theo R-01. Không suy ra hành khách từ ghế, màu tối, hình dán trên kính hoặc một vật tròn giống đầu. Áp dụng cho img_005, img_009, img_018 so với img_020, img_033.

### R-14 — Thao tác và định dạng khi tới Pha 3

Dùng Rectangle trên CVAT; xuất COCO 1.0, tắt Save images. COCO bbox là [x, y, w, h], gốc trên trái theo pixel, w/h dương; không dùng [x1, y1, x2, y2] làm trường bbox. Box phải nằm trong kích thước ảnh gốc.

Mỗi thành viên chỉ xuất đúng ảnh được giao, tên file 111.json; ảnh không có đối tượng vẫn phải có bản ghi trong images và có thể có 0 annotation. Các ảnh img_017, img_020, img_027, img_033, img_038 là ca âm tính theo phạm vi đề xuất, sau khi rà không có mục tiêu thật khác.

Chỉ cấu hình nhãn sau khi nhóm chốt taxonomy v1.0 và sinh JSON cho CVAT. Không chạy chia/gán chỉ để tạo số liệu cho Pha 1.

### R-15 — Cơ thể, trang phục và đồ mang

Box person bao các phần cơ thể nhìn thấy cùng quần áo, tóc, mũ/nón bảo hộ đang đội và giày dép đang mang. Không mở biên chỉ để chứa túi/ba lô, giỏ hàng, đòn gánh, ô cầm tay hoặc vật dài đang cầm. Nếu đồ mang che người thì xét che khuất của người, không coi đồ đó là phần cơ thể.

Thử quy tắc với người gánh hàng img_003, người bán hàng img_010 và người chở thanh dài img_029. Việc một đồ rời nằm tình cờ bên trong box chữ nhật person không phải lỗi; không chủ ý kéo biên box tới đồ rời.

## 4. Khảo sát ca khó và kiểm tra nhất quán

### 4.1. Ba ca đầu tiên theo thứ tự ảnh

- **img_001 →** v0 không xác định box nhìn thấy hay toàn thân khi người bị bàn/xe che và pickup bị cắt → **đề xuất:** tách person và truck, box theo phần quan sát được, tách occlusion khỏi truncation → **lý do:** tránh dựng phần vật không có trong ảnh; R-01, R-03, R-04, R-05.
- **img_002 →** xe con trên xe chở có bị xem là hàng bỏ qua không → **đề xuất:** một truck cùng khung vận chuyển và car riêng cho từng xe hoàn chỉnh quan sát được → **lý do:** vật thuộc taxonomy vẫn là mục tiêu khi đang được chở; R-02, R-03, R-07.
- **img_003 →** có bao giỏ/đòn gánh vào person, có ép xe đạp vào motorcycle không → **đề xuất:** person chỉ bao người/trang phục, loại đồ mang khỏi biên và giữ xe đạp ngoài phạm vi phương tiện cơ giới → **lý do:** tách vật chủ, đồ dùng và phạm vi lớp; R-02, R-06, R-15.

### 4.2. Nhật ký 40 ảnh đã khảo sát

Bảng dưới ghi quan sát và quyết định đề xuất sau khi mở từng ảnh gốc, không phải nhãn bbox hay quyết định nhóm đã thống nhất. C01–C40 là mã dòng khảo sát cá nhân, **không phải case_id do qc_compare.py sinh**.

| Ca | Ảnh | Chỗ v0 chưa rõ | Quyết định đề xuất | Lý do | Quy tắc |
|---|---|---|---|---|---|
| C01 | [img_001.jpg](../../../data/images/img_001.jpg) | Người bị bàn/xe che; bán tải cắt mép; v0 không nói box nhìn thấy hay toàn thân. | Gán người thật theo từng cá thể; bán tải thuộc truck. Box chỉ ôm phần quan sát được, không dựng phần xe/người ngoài ảnh; không gán người in trên biển. | Tránh tự suy diễn hình học và không nhầm che khuất với cắt mép. | R-01, R-03, R-04, R-05 |
| C02 | [img_002.jpg](../../../data/images/img_002.jpg) | Xe chở ô tô là một vật hay gồm cả các xe đang được chở? Khung vận chuyển thuộc box nào? | Một truck cho xe chở cùng khung vận chuyển; từng ô tô con nhận một car riêng (quan sát thấy 8 xe con). Box truck không mở rộng chỉ để bao các car phía trên; người đứng bên phải xét riêng. | Mỗi xe con là đối tượng mục tiêu độc lập dù đang được vận chuyển; hàng hóa không làm thay đổi ranh giới vật chủ. | R-02, R-03, R-07 |
| C03 | [img_003.jpg](../../../data/images/img_003.jpg) | Người gánh hàng có bao cả đòn gánh/giỏ? Xe đạp có thuộc motorcycle? | Person bao người cùng quần áo/nón đang đội; không kéo box theo giỏ hàng hoặc đòn gánh. Xe đạp ngoài phạm vi đề xuất, người dắt/đi xe vẫn là person; xe máy thật xét riêng. | Phân biệt cơ thể, đồ dùng rời và phương tiện có/không động cơ; không ép lớp gần giống. | R-02, R-06, R-15 |
| C04 | [img_004.jpg](../../../data/images/img_004.jpg) | Tuk-tuk chưa có lớp; tài xế bị cabin che có gộp vào xe không? | Đề xuất tuk_tuk cho hai xe ba bánh có cabin riêng. Gán person riêng cho tài xế nếu phần nhìn thấy đủ điều kiện; mái và khung xe thuộc box xe. | Cabin tích hợp khác motorcycle; che khuất không biến người và xe thành một vật. | R-02, R-04, R-06 |
| C05 | [img_005.jpg](../../../data/images/img_005.jpg) | Nhiều người ngồi trên xe, tay/chân và xe chồng nhau; minivan dễ nhầm bus. | Tách từng person với từng motorcycle thực sự nhận ra; không gán chung người-xe. Xe thân liền dạng minivan phía sau đề xuất van; không đoán số người qua vùng tối. | Mỗi đối tượng có ranh giới và lớp riêng; dựa cấu trúc quan sát được thay vì suy ra từ tư thế. | R-02, R-03, R-04, R-06, R-13 |
| C06 | [img_006.jpg](../../../data/images/img_006.jpg) | Xe máy, xe đạp và người đan xen; vùng xa nhỏ có bỏ cả ảnh? | Gán person và motorcycle nhận ra riêng; xe đạp ngoài phạm vi nhưng người đi xe đạp vẫn gán. Chỉ vùng xa nhỏ/không tách được mới cân nhắc ignore_region. | Giới hạn phạm vi ở phương tiện cơ giới không loại bỏ người; độ đông không đủ để bỏ toàn bộ ảnh. | R-02, R-06, R-09 |
| C07 | [img_007.jpg](../../../data/images/img_007.jpg) | Xe bán tải gắn thùng cao và xe tải nhỏ có phải van? | Đề xuất truck cho xe có cabin và khu chở hàng tách biệt, kể cả bán tải gắn thùng. Không phân loại theo kích thước nhỏ hay biển hãng xe. | Kết cấu chở hàng phân biệt truck với van thân liền. | R-02, R-08 |
| C08 | [img_008.jpg](../../../data/images/img_008.jpg) | Xe máy đỗ dày, người đứng và người ngồi; có chỉ gán xe đang chạy? | Gán cả motorcycle đỗ và đang chạy, người riêng. Tách xe còn nhận ra từng cá thể; chỉ phần không thể tách mới xem xét ignore. | Mục tiêu là phát hiện vật, không phải trạng thái chuyển động. | R-01, R-06, R-09 |
| C09 | [img_009.jpg](../../../data/images/img_009.jpg) | Xe chở khách dạng minibus chưa có lớp; người sau kính có gán? | Đề xuất bus cho xe chở khách thân dài, nhiều dãy cửa sổ và cửa hành khách riêng; person sau kính chỉ khi đủ dấu hiệu người và kích thước. | Không ép bus vào van; kính trong không phải lý do loại người thật. | R-02, R-09, R-13 |
| C10 | [img_010.jpg](../../../data/images/img_010.jpg) | Người ngồi trên xe máy và người bán hàng cạnh xe đạp/giỏ dễ thành một cụm. | Tách từng person và motorcycle. Không gán xe đạp chở hàng trong phạm vi đề xuất; không kéo box người/xe máy theo giỏ hoặc hàng hóa rời. | Vật thể được xác định theo cấu trúc, không theo nhóm hoạt động mua bán. | R-02, R-06, R-15 |
| C11 | [img_011.jpg](../../../data/images/img_011.jpg) | Nhân vật kim loại và vật dạng xe ở khu trưng bày có phải người/xe thật? | Không gán person cho nhân vật kim loại lớn. Người thật và xe thật đủ dấu hiệu ở nền vẫn xét riêng; các kết cấu dạng mô tô chưa phân biệt được xe trưng bày thật hay tác phẩm mô phỏng cần reviewer. | Không dùng hình dáng giống người/xe để suy ra bản chất; cũng không loại cả ảnh vì có tượng. | R-01, R-12 |
| C12 | [img_012.jpg](../../../data/images/img_012.jpg) | Hàng hóa trên thùng bán tải có được bao vào box truck? | Pickup là truck; box bao kết cấu xe quan sát được, không mở rộng lên các thùng/hàng rời nhô ra. Các xe thật khác vẫn xét riêng. | Phải phân biệt kết cấu xe và đồ vật đang được chở. | R-02, R-03, R-08 |
| C13 | [img_013.jpg](../../../data/images/img_013.jpg) | Ảnh đêm có phản chiếu mặt đường, người xa và xe nhỏ. | Gán đối tượng thật đủ rõ và đủ cao; không gán thêm box cho vệt phản chiếu/ánh sáng trên đường. Vùng tối không tự động là occlusion. | Thiếu sáng, phản chiếu và vật bị vật khác che là các hiện tượng khác nhau. | R-01, R-04, R-09, R-10 |
| C14 | [img_014.jpg](../../../data/images/img_014.jpg) | Người đi bộ rõ nhưng người/xe đang chạy mờ; box có bao toàn vệt nhòe? | Hai người đi bộ rõ được xét riêng. Vật chuyển động chỉ gán khi còn nhận ra lớp và vùng lõi; không kéo box theo bóng ma/vệt nhòe, thiếu ranh giới thì ghi review. | Không suy ra vật chỉ từ vệt màu; mờ chuyển động không đồng nghĩa bị che. | R-03, R-10, R-12, R-15 |
| C15 | [img_015.jpg](../../../data/images/img_015.jpg) | Ô tô cắt mép và xe nhòe; ảnh phản chiếu có tạo thêm vật? | Ô tô thật ở mép vẫn gán phần nhìn thấy và truncated=yes nếu bị cắt; đối tượng nhòe đủ dấu hiệu xét một lần, không theo vệt phản xạ trên đường. | Cắt mép giữ một phần vật hợp lệ; phản xạ không phải đối tượng mới. | R-01, R-03, R-05, R-10 |
| C16 | [img_016.jpg](../../../data/images/img_016.jpg) | Tuk-tuk, pickup có mái/thùng và van cùng xuất hiện; hình dáng cao dễ gây nhầm. | Dùng tuk_tuk cho xe ba bánh cabin tích hợp, truck cho pickup có khu hàng tách cabin, van cho xe thân liền phía sau; người và motorcycle tách riêng. | Cùng chiều cao/mái che không có nghĩa cùng lớp; phân loại theo kết cấu. | R-02, R-06, R-08 |
| C17 | [img_017.jpg](../../../data/images/img_017.jpg) | Tượng người cưỡi ngựa có được gán person không? | Loại tượng người và ngựa; không dùng ignore để che đi vật đã xác định ngoài phạm vi. Giữ ảnh trong COCO, có thể không có annotation mục tiêu. | Bản chất là tác phẩm mô phỏng, không phải người thật trong cảnh. | R-01, R-14 |
| C18 | [img_018.jpg](../../../data/images/img_018.jpg) | Chân dung trên billboard và các ô tô thật sau hàng rào có xử lý giống nhau? | Không gán person cho hình in trên biển. Vẫn gán xe thật nhận ra được sau hàng rào; một xe không tách thành nhiều box theo từng khe hàng rào. | Loại hình in không đồng nghĩa loại ảnh; phần nhìn thấy rời nhau của cùng vật vẫn có một box bao. | R-01, R-03, R-04 |
| C19 | [img_019.jpg](../../../data/images/img_019.jpg) | Xe diễu hành mang dáng hoạt hình có phải đồ chơi; nhân vật chuột có phải người mặc đồ? | Đề xuất car cho phương tiện cỡ thật có thân/bánh và người lái trong cảnh; gán người lái và khán giả đủ điều kiện. Không tự suy ra nhân vật chuột là người, chuyển vùng nhân vật này cho reviewer. | Kiểu dáng hoạt hình không tự làm xe thật thành mô hình; sự có mặt người trong mascot chưa được chứng minh trên ảnh. | R-01, R-02, R-09, R-12 |
| C20 | [img_020.jpg](../../../data/images/img_020.jpg) | Xe xuất hiện qua gương và bộ phận xe mang camera có phải đối tượng mục tiêu? | Không gán xe phản chiếu hoặc phần xe chủ/gương. Theo phạm vi đề xuất, ảnh không có đối tượng mục tiêu trực tiếp đủ điều kiện nhưng vẫn giữ trong COCO. | Tránh đếm lặp phản chiếu và gán bộ phận ego như xe ngoại cảnh. | R-01, R-14 |
| C21 | [img_021.jpg](../../../data/images/img_021.jpg) | Dòng người/xe dày; khi nào gán riêng, khi nào ignore? | Gán riêng người và xe còn tách được ở tiền/trung cảnh. Chỉ khoanh vùng xa dưới ngưỡng hoặc thật sự không tách được cá thể; không phủ vùng ignore lên các vật đã gán rõ. | Khó đếm không đồng nghĩa toàn ảnh không thể gán; ignore phải có phạm vi giải thích được. | R-06, R-09, R-12 |
| C22 | [img_022.jpg](../../../data/images/img_022.jpg) | Mô tô gắn sidecar có ba bánh: motorcycle hay tuk_tuk, một hay hai box? | Một motorcycle bao mô tô cùng sidecar gắn liền; không tạo person chỉ vì có kính/ghế nếu không thấy người. | Sidecar là phần gắn của mô tô, khác xe ba bánh có cabin/khung riêng tích hợp. | R-02, R-11, R-12 |
| C23 | [img_023.jpg](../../../data/images/img_023.jpg) | Xích lô ba bánh có bị coi là tuk-tuk; người đi xích lô có bỏ theo xe? | Xích lô đạp ngoài phạm vi cơ giới đề xuất. Người đạp/người ngồi vẫn xét person riêng; car và motorcycle khác trong cảnh vẫn gán. | Số bánh không đủ quyết định tuk_tuk; người được xét độc lập với lớp của phương tiện. | R-02, R-06 |
| C24 | [img_024.jpg](../../../data/images/img_024.jpg) | Thân vỏ vàng rỗng đặt trên nóc ô tô có tương đương xe hoàn chỉnh được chở ở img_002? | Đề xuất không gán riêng cụm thân vỏ vàng thiếu khoang xe hoàn chỉnh; các ô tô hoàn chỉnh trong khu trưng bày vẫn là car. Box xe đen không kéo theo cụm trên giá. | Phân biệt thân vỏ/đạo cụ mô phỏng với xe hoàn chỉnh; việc trưng bày tự nó không loại xe thật. | R-01, R-03, R-07, R-12 |
| C25 | [img_025.jpg](../../../data/images/img_025.jpg) | Người xa, xe cạnh đường và mô tô có thùng bên; vật nhỏ xét trên ảnh nào? | Đo kích thước trên ảnh gốc, gán person/motorcycle/car đủ điều kiện; hàng/thùng rời không tự thành một xe mới. Bộ phận bên chỉ gộp vào motorcycle nếu quan sát được liên kết cố định. | Không đổi ngưỡng khi zoom; phân biệt phụ kiện gắn liền với đồ vật rời. | R-02, R-09, R-11, R-12 |
| C26 | [img_026.jpg](../../../data/images/img_026.jpg) | Hai người trên một xe bị chồng lấp và xe ở sát mép dưới. | Nếu các đối tượng đều đủ điều kiện, tách hai person và một motorcycle; box của từng người theo phần thấy được, bao mũ đang đội, không suy ra chân/bánh ngoài ảnh. | Một phương tiện có thể chở nhiều người; mỗi đối tượng có occlusion/truncation độc lập. | R-03, R-04, R-05, R-06, R-15 |
| C27 | [img_027.jpg](../../../data/images/img_027.jpg) | Tượng đá hình pickup trông giống truck thật. | Không gán truck cho tượng pickup trên bệ; giữ ảnh trong lô kể cả không có annotation mục tiêu. | Chất liệu/bệ và ngữ cảnh cho thấy tác phẩm mô phỏng; hình dáng pickup không đủ. | R-01, R-14 |
| C28 | [img_028.jpg](../../../data/images/img_028.jpg) | Pickup thật chở tượng ngựa; có bao cả tượng vào box truck? | Pickup thật là truck; tượng ngựa không thuộc mục tiêu và không làm box xe cao lên. Xe thật phía sau vẫn xét riêng. | Quyết định theo từng vật, không loại xe thật vì nó chở vật giả. | R-01, R-02, R-08 |
| C29 | [img_029.jpg](../../../data/images/img_029.jpg) | Một xe máy và một xe đạp cùng chở thanh dài; gộp thanh vào người/xe? | Xe máy còn cấu trúc nhận ra được là motorcycle; xe đạp ngoài phạm vi. Người trên cả hai phương tiện là person; không kéo box theo các thanh hàng. | Phân loại từng phương tiện theo cấu trúc; hàng hóa dài là đồ rời, không phải kích thước cơ thể/xe. | R-02, R-06, R-08, R-15 |
| C30 | [img_030.jpg](../../../data/images/img_030.jpg) | Xe máy đỗ chồng dày, ảnh hơi mờ và chân dung in ở nền. | Gán từng motorcycle thực sự tách được và người thật; không gán person cho ảnh in. Không khoanh ignore cả bãi xe chỉ vì nhiều xe. | Xe đỗ vẫn thuộc mục tiêu; tách vùng mơ hồ khỏi các vật còn nhận dạng rõ. | R-01, R-09, R-10 |
| C31 | [img_031.jpg](../../../data/images/img_031.jpg) | Bus và xe/người trong cảnh đêm; ánh đèn phản xạ dễ thành box phụ. | Bus dùng lớp bus đề xuất; gán car/motorcycle/person đủ điều kiện. Không coi vệt đèn/phản xạ mặt đường là vật; vùng tối khó nhận cần review. | Phạm vi lớp không đổi giữa ngày/đêm; chất lượng ảnh là vấn đề riêng. | R-01, R-02, R-09, R-10, R-12 |
| C32 | [img_032.jpg](../../../data/images/img_032.jpg) | Hàng người khá đông nhưng nhiều cá thể còn tách rõ; car tiền cảnh bị cắt. | Gán riêng từng person còn phân biệt, không ignore toàn hàng. Car bên trái cắt mép vẫn gán phần thấy và truncated=yes; người/xe sau kính xét theo dấu hiệu thật. | Đông không tự động là không thể tách; che khuất và cắt mép đánh giá độc lập. | R-03, R-05, R-09, R-13 |
| C33 | [img_033.jpg](../../../data/images/img_033.jpg) | Ma-nơ-canh, hình người in và xe phản chiếu trong kính cùng xuất hiện. | Không gán person cho ma-nơ-canh/hình in và không gán xe phản chiếu. Theo phạm vi đề xuất, giữ ảnh âm tính trong COCO. | Vật mô phỏng và phản chiếu đều bị loại có quy tắc; không dùng ignore thay cho quyết định loại. | R-01, R-13, R-14 |
| C34 | [img_034.jpg](../../../data/images/img_034.jpg) | Người đi bộ và người lái xe đều là person; xe đạp và xe máy lẫn nhau; vật cắt mép phải bỏ? | Gán người đi bộ và người lái xe theo cùng định nghĩa person, phương tiện xét riêng theo phạm vi; người/xe máy cắt mép phải xét kích thước và truncated, không bỏ mặc định. | Vai trò giao thông không tạo lớp người mới; cắt mép không đồng nghĩa không hợp lệ. | R-02, R-05, R-06, R-09 |
| C35 | [img_035.jpg](../../../data/images/img_035.jpg) | Tuk-tuk chở hàng cao quá mái: có đổi thành truck, bao luôn hàng không? | Vẫn là tuk_tuk theo kết cấu; box bao thân, mái và khung quan sát được, không kéo lên bao tải nhô cao. Motorcycle/người và tuk-tuk khác trong ảnh xét độc lập. | Hàng chở không đổi lớp vật chủ và không phải kết cấu cố định của xe. | R-02, R-03, R-08 |
| C36 | [img_036.jpg](../../../data/images/img_036.jpg) | Xe tải nhỏ, cabin kiểu van và thùng phủ bạt khiến truck/van mơ hồ. | Đề xuất truck: cabin và khu thùng hàng tách biệt; bạt/khung che gắn thùng là cấu kiện xe khi bao box, khác hàng rời. | Kết cấu cabin-thùng có giá trị hơn chiều dài xe hay việc thùng kín. | R-02, R-08 |
| C37 | [img_037.jpg](../../../data/images/img_037.jpg) | Pickup lắp nắp thùng cao có đổi thành van; bus nền bị che có bỏ? | Pickup vẫn là truck; nắp thùng gắn liền thuộc box xe. Bus ở nền vẫn là bus nếu đủ dấu hiệu và kích thước, phần che không tự suy diễn. | Phụ kiện/nắp không làm đổi kiến trúc cabin-thùng; không gộp nhiều xe chồng nhau. | R-02, R-03, R-04, R-08 |
| C38 | [img_038.jpg](../../../data/images/img_038.jpg) | Mô hình ô tô nhiều kích cỡ trong tủ có được gán car vì đủ pixel? | Không gán các xe đồ chơi/mô hình; không gán phản chiếu trong kính. Giữ ảnh âm tính trong lô. | Kích thước pixel chỉ áp dụng sau khi xác định vật thuộc phạm vi; đồ chơi lớn trên ảnh vẫn là đồ chơi. | R-01, R-09, R-14 |
| C39 | [img_039.jpg](../../../data/images/img_039.jpg) | Xe đạp, xe máy, người đứng và người đi bộ trong cùng cảnh; chỉ thấy một phần phương tiện. | Gán person độc lập; xe máy thực sự nhận ra được vẫn gán, xe đạp ngoài phạm vi đề xuất. Phần bị che/cắt không tự dựng lại. | Không bỏ người theo phương tiện ngoài phạm vi; không cần người ngồi mới gán xe máy đỗ. | R-01, R-02, R-03, R-06 |
| C40 | [img_040.jpg](../../../data/images/img_040.jpg) | Người đội mũ và các gương rời có chứng minh một motorcycle ngoài khung; taxi/tuk-tuk/bus chồng nhau? | Gán person, taxi car, tuk_tuk và bus đủ dấu hiệu riêng. Không suy ra một motorcycle chỉ từ mũ bảo hiểm hoặc các gương không liên kết được thân xe; ghi vùng dưới ảnh để review nếu không vẽ được box xe chắc chắn. | Quy tắc người-xe tách rời không cho phép tạo đối tượng không có đủ bằng chứng hình ảnh. | R-02, R-03, R-06, R-12 |

### 4.3. Đối chiếu cùng quy tắc trên ảnh khác

Đây là rà soát logic của đề xuất bằng ảnh gốc, không phải số đo IAA hay nghiệm thu QC.

| Quy tắc / điểm cần giữ nhất quán | Ảnh đối chiếu | Kết quả rà soát và giới hạn |
|---|---|---|
| R-06: người và phương tiện là các vật riêng | 005 ↔ 026 ↔ 034 | Cùng tách từng người khỏi xe; không đổi số người chỉ vì chung một xe. Chỉ tạo xe khi chính xe đủ bằng chứng. |
| R-06/R-12: không suy xe từ mũ/tư thế | 026 ↔ 040 | 026 thấy cấu trúc motorcycle; 040 cần giữ vấn đề phần xe/gương ở tiền cảnh thay vì tự bịa box. |
| R-08: không bao hàng nhô ra | 003 ↔ 012 ↔ 029 ↔ 035 | Đòn gánh, thùng giấy, thanh dài và bao tải không mở rộng box vật chủ; xe hoàn chỉnh được chở là ngoại lệ có nhãn riêng theo R-07. |
| R-02/R-08: pickup không thành van vì nắp cao | 007 ↔ 016 ↔ 036 ↔ 037 | Dùng dấu hiệu cabin–thùng riêng; nắp/bạt gắn xe thuộc box. Van thân liền 005 là phản ví dụ. |
| R-07: xe hoàn chỉnh khác cụm thân vỏ | 002 ↔ 024 | Xe con hoàn chỉnh đang được chở vẫn gán; đề xuất loại cụm rỗng trên nóc ở 024 cần người/nhóm xác nhận. |
| R-01: vật thật khác tượng/đồ chơi | 017 ↔ 027 ↔ 028 ↔ 038 | Loại tượng/đồ chơi; vẫn giữ pickup thật chở tượng ở 028. Không loại chỉ vì xe ở khu trưng bày. |
| R-01/R-13: hình in và phản chiếu khác vật trực tiếp | 018 ↔ 020 ↔ 033 | Loại hình in/phản chiếu; xe thật sau hàng rào 018 vẫn xét gán. |
| R-09: đông không tự động là ignore | 008 ↔ 021 ↔ 030 ↔ 032 | Chỉ khoanh phần nhỏ/không tách được; 032 là phản ví dụ của việc phủ ignore cả hàng người. |
| R-02: lớp không đổi do ánh sáng/kích thước cảm nhận | 009 ↔ 031 ↔ 037 ↔ 040 | Bus dùng một định nghĩa ở cả ngày/đêm; không ép vào van chỉ vì xa/nhỏ. |
| R-10: nhòe khác che | 014 ↔ 015 ↔ 031 | Giữ lớp nếu còn bằng chứng, không dùng occluded_level để mã hóa nhòe; biên không chắc phải review. |
| R-11: ba bánh không đủ quyết định tuk_tuk | 004 ↔ 022 ↔ 023 | Cabin tích hợp → tuk_tuk; mô tô-sidecar → motorcycle; xích lô đạp ngoài phạm vi. |
| R-02/R-06: người không bị loại theo phương tiện | 006 ↔ 023 ↔ 029 ↔ 039 | Người trên xe đạp/xích lô vẫn là person, phương tiện xét theo phạm vi riêng. |

## 5. QC, phiên bản và bàn giao phần cá nhân

### 5.1. Ngưỡng giữ nguyên từ taxonomy của lab

| Khóa rules | Giá trị | Cách dùng |
|---|---|---|
| min_box_height_px | 20 | Ngưỡng chiều cao theo R-09; đo trên ảnh gốc. |
| match_iou | 0.5 | Ghép box một–một, không phân biệt lớp. |
| large_px | 50 | Trong script, dùng trung bình chiều cao hai box của cặp để chọn ngưỡng lớn/nhỏ. |
| iou_thr_large | 0.85 | Cờ lệch hình học cho cặp box lớn. |
| iou_thr_small | 0.70 | Cờ lệch hình học cho cặp box nhỏ. |
| iaa_iou | 0.85 | IoU trung bình của các cặp đã ghép. |
| iaa_class | 0.95 | Đồng thuận lớp trên các cặp đã ghép. |
| error_rate_img_max | 0.02 | Tỉ lệ ảnh lỗi khi duyệt toàn lô để xét Release. |

Chỉ đo IAA khi đủ 100% lượt phân công. Box chưa ghép được đếm riêng; không có cặp thì là N/A. IAA cao không chứng minh đúng thực tế hoặc không có vật cả hai cùng bỏ sót. Với 40 ảnh, một ảnh lỗi là 2.5%, vượt ngưỡng 2%. Số đo thuộc pha sau, không điền số giả cho Pha 1.

Hold khi thiếu bằng chứng, còn cờ review, có vấn đề guideline/dữ liệu hoặc vòng demo; Rework khi vượt tỉ lệ ảnh lỗi hoặc lỗi cùng loại ít nhất ba lần và chiếm ít nhất 50% tổng lỗi; chỉ Release khi đủ bằng chứng và reviewer xác nhận. Nếu sửa nhãn, giữ các vòng cũ và mở vòng mới.

### 5.2. Ca cần chốt ở CP2

- Phạm vi phương tiện cơ giới: thêm bus/tuk_tuk, loại xe đạp/xích lô nhưng vẫn gán người.
- Định nghĩa pickup/van và một đơn vị motorcycle-sidecar.
- Box phần nhìn thấy; hàng rời so với nắp/khung gắn liền; xe được chở và cụm thân vỏ.
- Ngưỡng mức che, phân biệt che/cắt/nhòe, và cách dùng ignore cho vùng xa/đông.
- Các trường hợp cần người xác nhận thêm: vật dạng mô tô ở 011, mascot ở 019, thân vỏ ở 024, phần xe/gương ở 040.
- Người dùng 111 xác nhận phần AI hỗ trợ trước khi nộp theo yêu cầu của lớp.

Nhóm chưa họp nên không gán các đề xuất này thành quyết định đã được 555/999 đồng ý. Guideline cá nhân sau khi gửi riêng giữ nguyên để đối chiếu với guideline nhóm v1.0.

### 5.3. Lịch sử

| Phiên bản | Thay đổi | Căn cứ |
|---|---|---|
| v0.1-draft | Khởi tạo khung từ template cho 111. | Chuẩn bị Pha 1. |
| v0.2-111 | Hoàn thiện đề xuất phạm vi, tám nhãn, thuộc tính, R-01…R-15; ghi đủ 40 ca và 12 phép đối chiếu logic. | Codex mở từng ảnh gốc theo yêu cầu của 111; có ghi rõ nguồn hỗ trợ AI. |

### 5.4. Trạng thái kiểm tra

- [x] Có đủ năm phần, ví dụ/phản ví dụ, ngưỡng thuộc tính, ba ca đầu tiên và quy tắc có mã.
- [x] Đã kiểm tra logic trên các ảnh tương tự và ghi riêng các ca còn cần chốt.
- [x] Đã cập nhật phần Pha 1.
- [x] Đã gửi riêng theo kênh quy định; chỉ chia sẻ/commit nhánh chung sau khi cả nhóm nộp xong.
