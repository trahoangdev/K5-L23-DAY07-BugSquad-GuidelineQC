# Review ba guideline Pha 1 và hợp nhất Pha 2 — BugSquad

- Ngày: 2026-11-16.
- Mã thành viên nguồn: 111, 555, 999.
- Điều phối: 111; reviewer cuối theo TEAM.md: 999.
- Người thực hiện lần đối chiếu/soạn văn bản này: 111.
- Kết quả: [guideline_v1.0_agreed.md](guideline_v1.0_agreed.md), [taxonomy v1.0](../taxonomy.json), JSON CVAT sinh bằng script của repo.
- Phạm vi: So cả ba bản; đối chiếu ca ảnh đã khảo sát và mở lại trực tiếp các ảnh 005,009,011,012,019,022,024,030,032,040 nơi có diễn giải khác nhau.
- Đây là hồ sơ lựa chọn nội dung chung; không phải biên bản một cuộc họp/phiếu biểu quyết/chữ ký của 555 hoặc999 đã diễn ra. Tên file agreed tuân theo GUIDE.md; việc mọi người nhận cùng bản và công bố khóa trước lượt gán được theo dõi riêng trong guideline.
- D01…D20 là mã quyết định hợp nhất CP2, không phải calibration_decisions.csv hay case_id do IAA sinh.

## 1. Nguồn và bảo toàn bản cá nhân

| Mã | File nguồn | SHA-256 khi bắt đầu review |
|---|---|---|
| 111 | [guideline_111.md](individual/guideline_111.md) | `39c32d0d7e2b2a3f7f5ce821577479c38af71aedfacc7c759ee8eb7658ce8da7` |
| 555 | [guideline_555.md](individual/guideline_555.md) | `a6d7e924b08f5b20cde59e18b35f36da069d3fb62fbf93ee5c5914dfc353de29` |
| 999 | [guideline_999.md](individual/guideline_999.md) | `d268d0923c99aea1d039bb9151cf2a771a2c9a8de18cf1939c4a76fed1747849` |

Không sửa ba file guideline cá nhân để làm chúng trông như đã đồng ý với bản chung. Bản hợp nhất chỉ dùng mã thành viên. Trong lúc review, file 999 được cập nhật metadata trong workspace: tiêu đề và dòng người viết chuyển sang mã 999; phần từ Mục 1 trở đi không đổi so với bản Git đầu vào. Giữ nguyên cập nhật này. Hash ban đầu được giữ trong bảng trên để truy vết; SHA-256 của file 999 sau cập nhật metadata là `296e83d2c2067dc770cdd24d4a149df36e10d6ba254a9865d47bf947c7ec4047`.

Các điểm đã gần nhau ở cả ba bản: box phần nhìn thấy; tách người và xe trong quy tắc tổng quát; loại hình in/tượng/phản chiếu/đồ chơi; dùng truncated cho cắt mép; cần cơ chế review ca chưa chắc; giữ các ngưỡng QC. Tuy vậy một số ví dụ/ngoại lệ trong cùng bản đi ngược quy tắc tổng quát, nên vẫn phải giải quyết rõ.

Mã R-xx giữa ba nguồn khác nghĩa: chẳng hạn R-03 của 555 là xe chở xe, còn R-03 của999 là điều kiện nhận diện/ignore và R-03 của 111 là hình học box. Từ Pha 3 chỉ dùng mã R-xx của guideline chung; hồ sơ dưới luôn nêu mã thành viên khi dẫn nguồn.

## 2. Hai mươi chủ đề đã giải quyết

### D01 — Phạm vi lớp

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: 8 nhãn, loại xe đạp/xích lô; Mục 1–2 và R-02. |
| 555 | 555: 9 nhãn, thêm bus/tuk_tuk/bicycle; Mục 2.1. |
| 999 | 999: giữ 6 nhãn v0; vật ngoài lớp không gán; Mục 2. |

**Quyết định đưa vào v1.0:** Chọn 9 nhãn gồm car, truck, van, bus, tuk_tuk, motorcycle, bicycle, person, ignore_region.

**Lý do:** Xe đạp/xích lô lặp lại trong dữ liệu và thuộc mục tiêu phương tiện đường bộ; thêm một lớp chung bicycle hạn chế mở rộng quá nhiều lớp. Đây là lựa chọn theo phạm vi sử dụng, không phải kết quả bỏ phiếu.

Ảnh/căn cứ: img_003, img_006, img_023, img_029, img_034, img_039. Quy tắc chung: R-02.

### D02 — Xe chở xe

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: gán xe chở và từng car riêng; R-07. |
| 555 | 555: truck và car riêng; R-03. |
| 999 | 999: một truck bao cả xe và các xe được chở; R-05. |

**Quyết định đưa vào v1.0:** Truck bao kết cấu xe chở/khung vận chuyển; mỗi xe con hoàn chỉnh nhận ra được là car riêng; không mở box truck chỉ để bao car phía trên.

**Lý do:** Đối tượng vẫn thuộc lớp dù đang được chở. Chồng box không phải lỗi; cần giữ mục tiêu phát hiện từng vật.

Ảnh/căn cứ: img_002, img_024. Quy tắc chung: R-03, R-07.

### D03 — Hàng hóa trên xe

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: loại hàng rời khỏi biên, giữ cấu kiện gắn xe; R-08. |
| 555 | 555: không bao hàng nhô ra; R-07; R-11 giữ nắp pickup. |
| 999 | 999: hàng gắn chắc tính vào box; R-09; ca img_035 còn gộp người. |

**Quyết định đưa vào v1.0:** Không kéo box theo bao tải/ống/thùng giấy/đồ chở dù đã buộc chắc. Bao nắp thùng/bạt trên khung/cấu kiện cố định; người luôn riêng.

**Lý do:** Dây buộc hoặc chuyển động cùng nhau không biến hàng thành kết cấu xe. Cần tách phạm vi vật chủ, người và hàng.

Ảnh/căn cứ: img_001, img_003, img_012, img_028, img_029, img_035, img_037. Quy tắc chung: R-03, R-06, R-08, R-15.

### D04 — Người và phương tiện

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: từng person, từng xe đủ bằng chứng; R-06. |
| 555 | 555: hai loại box riêng, nhiều người có nhiều person; R-02. |
| 999 | 999: R-10 tách người-xe nhưng ca img_035 gộp vào một box; ví dụ img_010 ghi ba box. |

**Quyết định đưa vào v1.0:** Tách từng người và từng phương tiện. Xe hai người thường có hai person và một xe nếu từng vật đạt điều kiện; không coi ví dụ ba box là tổng số toàn ảnh.

**Lý do:** Loại mâu thuẫn nội bộ và tránh đếm thiếu các vật khác trong ảnh.

Ảnh/căn cứ: img_004, img_005, img_010, img_026, img_035, img_040. Quy tắc chung: R-06.

### D05 — Người nhìn qua kính

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: gán người thật đủ rõ qua kính; R-13. |
| 555 | 555: không gán người trong xe kín; R-13. |
| 999 | 999: person gồm người ngồi trên xe, không có quy tắc loại toàn bộ người sau kính. |

**Quyết định đưa vào v1.0:** Gán person sau kính khi có bằng chứng người thật, ranh giới và chiều cao hợp lệ; không suy người từ ghế/hình dán/bóng phản xạ.

**Lý do:** Phạm vi person không phụ thuộc xe hở/kín; nhìn xuyên kính và phản chiếu phải được phân biệt.

Ảnh/căn cứ: img_005, img_009, img_018, img_033, img_040. Quy tắc chung: R-01, R-06, R-13.

### D06 — Ngưỡng che khuất

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: 0 chỉ không che; (0,30], (30,70], >70; Mục 2.2/R-04. |
| 555 | 555: 0–10%, 11–30%, 31–70%, >70%; Mục 2.2. |
| 999 | 999: dưới 25%, 25–50%, trên 50%; còn nhãn CHECK; Mục 2. |

**Quyết định đưa vào v1.0:** 0: p=0; 1: 0<p<=30%; 2: 30%<p<=70%; 3: p>70% nhưng vẫn nhận ra. Không dùng 3 cho không biết; không có khoảng trống ở p thập phân.

**Lý do:** Giữ nghĩa mức 0 là không che, chốt ranh giới không chồng lấp và không có khoảng hở; tách che khỏi nhòe/cắt mép.

Ảnh/căn cứ: img_001, img_005, img_018, img_026. Quy tắc chung: R-04, R-05, R-10.

### D07 — Người chỉ thấy đầu

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: dựa nhận diện và kích thước nhìn thấy; không suy người từ mũ. |
| 555 | 555: nhận ra được thì gán, kể cả che nặng; Mục 2.2. |
| 999 | 999: đám đông cần thấy đầu và thân/30%; bỏ đầu-only nhưng ví dụ khác vẫn gán người che 80%; R-06/R-10. |

**Quyết định đưa vào v1.0:** Đầu/mặt người thật đủ bằng chứng là cá thể riêng và h>=20 px vẫn gán person, kể cả che >70%. Mũ/đốm tròn không tự chứng minh có người.

**Lý do:** Không đặt thêm ngưỡng thấy 30% mâu thuẫn với occluded_level=3; tránh bỏ sót có hệ thống.

Ảnh/căn cứ: img_005, img_009, img_019, img_021, img_032. Quy tắc chung: R-04, R-06, R-09, R-12.

### D08 — Vật nhỏ đơn lẻ

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: h<20 dùng ignore phù hợp kể cả vùng nhỏ; R-09. |
| 555 | 555: nhỏ lẻ bỏ, dày thì ignore; R-09. |
| 999 | 999: nhỏ lẻ bỏ, cụm nhỏ thì ignore; Mục 3. |

**Quyết định đưa vào v1.0:** Mục tiêu chắc chắn nhưng h<20 px không gán lớp vật, khoanh ignore nhỏ phù hợp; đúng 20 px được xét gán riêng. Đo trên ảnh gốc.

**Lý do:** Theo QC cheatsheet của lab và ngăn mục tiêu nhỏ bị hiểu thành ảnh sạch/bỏ sót; không để zoom đổi ngưỡng.

Ảnh/căn cứ: img_021, img_025, img_034. Quy tắc chung: R-09.

### D09 — Đám đông/bãi xe

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: tách cá thể còn nhận được, không ignore cả hàng/bãi; R-09. |
| 555 | 555: điều kiện không tách được nhưng ví dụ img_030 phủ cả bãi; R-10. |
| 999 | 999: dùng ignore cho phần đám đông không tách; thêm điều kiện đầu/thân không nhất quán. |

**Quyết định đưa vào v1.0:** Gán mọi vật đủ rõ trước; ignore chỉ phần thật sự không phân biệt cá thể. Không mặc định ignore toàn img_019/img_030/img_032.

**Lý do:** Ảnh gốc còn nhiều người/xe rõ; density không thay thế điều kiện không tách được. Vùng ignore không được che vật rõ.

Ảnh/căn cứ: img_008, img_019, img_021, img_030, img_032. Quy tắc chung: R-09.

### D10 — Ego ở img_012

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: không suy ego chỉ vì sát ảnh; gán pickup img_012; R-01/R-08. |
| 555 | 555: img_012 là xe chủ nên bỏ; R-06. |
| 999 | 999: loại thân/capô xe mang camera, chưa có chứng cứ riêng cho img_012. |

**Quyết định đưa vào v1.0:** Gán pickup img_012 theo truck; không đủ bằng chứng nó là xe mang camera. Loại gương/thân ego rõ ở img_020; sát mép tự nó không phải ego.

**Lý do:** Đổi một suy đoán về vị trí camera thành quy tắc loại sẽ gây bỏ sót xe lớn trong ảnh.

Ảnh/căn cứ: img_012, img_020, img_032. Quy tắc chung: R-01, R-02, R-05.

### D11 — Ảnh mờ/thiếu sáng

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: đủ lớp/biên thì gán, không vẽ theo vệt; khó thì review/image issue; R-10/R-12. |
| 555 | 555: nhận được lớp và biên thì gán can_review, không biên thì image issue; R-12. |
| 999 | 999: lớp không nhận ra hoặc nhòe nặng thì ignore; R-03/R-07. |

**Quyết định đưa vào v1.0:** Nhận rõ và biên ổn định thì gán; biên/lớp ứng viên chưa chắc thì can_review và ghi lý do; chưa có box đáng tin thì image issue. Ignore không phải mặc định cho nhòe/không biết lớp.

**Lý do:** Không che bất đồng bằng ignore; không tạo vật từ vệt màu. Nhòe/tối không phải occluded_level.

Ảnh/căn cứ: img_013, img_014, img_015, img_031. Quy tắc chung: R-04, R-09, R-10, R-12.

### D12 — Vật lạ/ngoài taxonomy

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: loại chắc ngoài phạm vi; ứng viên có căn cứ thì can_review; R-12. |
| 555 | 555: vật thật lạ gán lớp gần nhất rồi review; Mục 2.1. |
| 999 | 999: ngoài taxonomy không gán, không nhận ra dùng ignore; R-03. |

**Quyết định đưa vào v1.0:** Không ép lớp gần nhất chỉ vì vật thật. Ngoài phạm vi chắc chắn thì loại; chưa xác định lớp hợp lệ có căn cứ thì ghi vấn đề cấp ảnh. Chỉ dùng can_review trên box có lớp ứng viên hợp lý.

**Lý do:** Tránh trộn lỗi phân lớp với vùng ignore; không tự thêm lớp trong lượt độc lập.

Ảnh/căn cứ: img_011, img_019, img_040. Quy tắc chung: R-02, R-09, R-12.

### D13 — Pickup, van, bus

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: theo kết cấu, không đoán ghế; R-02/R-08. |
| 555 | 555: pickup là truck nhưng car có tiêu chí dưới 9 chỗ; van/bus theo kết cấu; Mục 2.1/R-11. |
| 999 | 999: van/truck có yếu tố nhỏ/lớn, chưa có bus; Mục 2. |

**Quyết định đưa vào v1.0:** Cabin–thùng riêng -> truck kể cả pickup có nắp; van thân liền; bus/minibus theo thân/cửa/khoang khách. Không dùng số ghế suy đoán, kích thước cảm nhận hoặc hãng xe.

**Lý do:** Các dấu hiệu phải quan sát được; cùng xe không đổi lớp khi lắp nắp, chở hàng, chụp xa hoặc ban đêm.

Ảnh/căn cứ: img_005, img_007, img_009, img_016, img_031, img_036, img_037. Quy tắc chung: R-02, R-08.

### D14 — Xe thật đang đỗ/trưng bày/kiểu lạ

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: xe thật đỗ, được chở, trưng bày vẫn gán; R-01. |
| 555 | 555: mục tiêu đang lưu thông và phép thử tự di chuyển ở R-05, nhưng nhiều ví dụ xe đỗ vẫn gán. |
| 999 | 999: car/motorcycle gồm đang chạy và đỗ; R-01 loại mô phỏng. |

**Quyết định đưa vào v1.0:** Gán xe thật đủ bằng chứng dù đỗ/trong kho/trưng bày/được chở. Xe diễu hành 019 vẫn car theo hình thái xe cỡ thật và người lái; không đòi chứng minh chuyển động từ ảnh tĩnh.

**Lý do:** Chỉ xét chuyển động sẽ mâu thuẫn với ảnh đỗ và làm mất nhiều mẫu. Kiểu dáng hoạt hình không tự tạo lớp đồ chơi.

Ảnh/căn cứ: img_002, img_007, img_008, img_019, img_024, img_030. Quy tắc chung: R-01, R-02.

### D15 — Tượng, thân vỏ và đồ chơi

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: loại vật mô phỏng; phân biệt thân vỏ 024, không loại cả ảnh; R-01/R-07. |
| 555 | 555: loại mô phỏng, nhưng danh sách ảnh không có thật chứa cả 026/028/030/040 có mục tiêu thật; R-04. |
| 999 | 999: loại tượng/hình in/đồ chơi; img_011 nêu không gán chưa tách rõ vùng thật. |

**Quyết định đưa vào v1.0:** Loại từng vùng mô phỏng: tượng 017/027, mannequin/phản chiếu 033, toys038, cụm thân vỏ vàng024; giữ người/xe thật khác trong cùng ảnh. Không dùng danh sách ảnh như danh sách bỏ cả ảnh.

**Lý do:** Bản chất phải xét từng vật; tranh tường 026 không loại hai người/xe máy phía trước; biển in 018 không loại bãi xe thật.

Ảnh/căn cứ: img_011, img_017, img_018, img_024, img_026, img_027, img_028, img_030, img_033, img_038, img_040. Quy tắc chung: R-01, R-07, R-13.

### D16 — Mô tô-sidecar, xích lô và người giả định

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: một motorcycle gồm sidecar, không suy người từ kính/ghế; R-11. |
| 555 | 555: có sidecar vẫn motorcycle; ca022 ghi thêm một person; bicycle gồm xích lô. |
| 999 | 999: chưa có sidecar/bicycle; người-xe tách riêng. |

**Quyết định đưa vào v1.0:** Một motorcycle-sidecar; một bicycle cho cả cụm xích lô đạp. Người thật xét riêng, không có mặc định một person vì có kính chắn/ghế. Liên kết thùng bên chưa rõ thì review.

**Lý do:** Số bánh không đủ phân lớp; bộ phận gắn liền không là phương tiện thứ hai; không dùng số đếm viết sẵn thay bằng chứng.

Ảnh/căn cứ: img_004, img_022, img_023, img_025, img_029. Quy tắc chung: R-02, R-06, R-11, R-12.

### D17 — Mascot và người hóa trang

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: không tự suy người trong mascot; giữ vấn đề cần xác nhận; R-01/R-12. |
| 555 | 555: loại mascot chuột như vật không thật, gọi là đồ hóa trang; R-05. |
| 999 | 999: chưa quy định riêng. |

**Quyết định đưa vào v1.0:** Không gán person chỉ từ hình mascot ở 019 khi chưa có bằng chứng người bên trong; ghi vấn đề cấp ảnh nếu còn nghi ngờ. Người thật mặc trang phục vẫn là person khi có bằng chứng cơ thể người; không loại toàn bộ người hóa trang.

**Lý do:** Một ảnh không chứng minh cấu tạo bên trong nhân vật; giữ trường hợp nghi ngờ cho calibration thay vì biến giả định thành sự thật.

Ảnh/căn cứ: img_011, img_019. Quy tắc chung: R-01, R-12.

### D18 — Ranh giới và đồ mang của person

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: quần áo/mũ/giày gồm trong người; đồ rời không kéo biên; R-03/R-15. |
| 555 | 555: box phần nhìn thấy; không bao hàng rời; R-01/R-07. |
| 999 | 999: box phần nhìn thấy nhưng ca035 gộp người/xe/hàng; R-02/R-09. |

**Quyết định đưa vào v1.0:** Box theo phần nhìn thấy của từng vật; bao trang phục/mũ đang đội, không kéo biên theo túi, giỏ, ba lô, đòn gánh, thanh dài hay xe. Cho phép box chồng, khoảng trống trong hộp là bình thường.

**Lý do:** Đưa cùng một cách xác định đối tượng vào cả người và phương tiện; tránh box phụ thuộc tải hàng.

Ảnh/căn cứ: img_001, img_003, img_010, img_026, img_029, img_035. Quy tắc chung: R-03, R-06, R-08, R-15.

### D19 — Số đếm/nhận diện trong ví dụ

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: không ép số xe tiền cảnh040 từ mũ/gương; R-12; ca022 không suy người. |
| 555 | 555: ca015 ghi 4 xe; ca022 thêm một person; img030 dùng minh họa bicycle. |
| 999 | 999: thử img010 ghi 3 box, dễ bị hiểu thành tổng ảnh. |

**Quyết định đưa vào v1.0:** Chỉ dùng số đếm cho một nhóm vật xác định rõ; không chốt tổng ảnh trước lượt gán. Bỏ yêu cầu mặc định person022, carcount015 và total3boxes010; bicycle dùng ví dụ rõ 003/023/029, không mặc định mọi xe ở030 là bicycle.

**Lý do:** Ảnh gốc là căn cứ, không phải số đếm trong văn bản. Cần quan sát cấu trúc thực và áp ngưỡng từng vật.

Ảnh/căn cứ: img_010, img_015, img_022, img_030, img_040. Quy tắc chung: R-02, R-06, R-09, R-12.

### D20 — Mã quy tắc, phân công và trạng thái bằng chứng

| Nguồn | Quan điểm cần đối chiếu |
|---|---|
| 111 | 111: 15 mã R-xx riêng; phạm vi40 ảnh; Mục 5. |
| 555 | 555: 13 mã với nghĩa khác; phụ lục tự nêu 27 ảnh; người chốt999. |
| 999 | 999: 10 mã với nghĩa khác; có CHECK chưa chốt; Mục 5. |

**Quyết định đưa vào v1.0:** Đánh số R-01…R-15 theo bản chung, ghi ánh xạ nội dung trong bảng này. Assignment chính thức do split_assign sinh sau CP2, không lấy phụ lục cá nhân làm chứng cứ phân công đã khóa. 111 điều phối, 999 review cuối; không tạo xác nhận hay cuộc họp giả cho 555/999.

**Lý do:** Tránh cùng Rule ID nhưng khác nghĩa; giữ nguồn cá nhân và không gán tiến độ/đồng thuận chưa thực hiện.

Ảnh/căn cứ: 40 ảnh; hồ sơ nguồn và TEAM.md. Quy tắc chung: R-12, R-14.

## 3. Rà các mâu thuẫn trong ví dụ

- Bản555 vừa giới hạn mục tiêu “đang lưu thông” vừa gán xe đỗ; v1.0 giữ gán xe đỗ/trưng bày thật.
- Bản555 xếp nhiều ID ảnh có tượng/hình in vào danh sách vật không thật. V1.0 áp quy tắc theo từng vùng/vật, không loại cả026/028/030/040 vốn vẫn có mục tiêu thật.
- Bản555 đề xuất bicycle có ví dụ030 chưa đủ rõ; dùng cấu trúc xe thực và các ví dụ003/023/029, không áp tên ảnh làm lớp.
- Bản999 tách người-xe ở quy tắc tổng quát nhưng gộp xe-người-hàng ở 035; v1.0 bỏ ngoại lệ gộp.
- Bản999 loại người chỉ thấy đầu theo ngưỡng thấy 30% nhưng vẫn gán người che80% trong ví dụ khác; v1.0 dùng nhận diện + h>=20, tách khỏi mức che.
- “Dưới9 chỗ”, “lối đi dọc”, “tự di chuyển được” hoặc “có người trong sidecar” không được khẳng định nếu ảnh không cho thấy. V1.0 thay bằng dấu hiệu quan sát và cơ chế review.
- Bảng ca khó không thay COCO: không nhận các số đếm một nhóm vật như tổng số chuẩn toàn ảnh.

## 4. Các lựa chọn cuối của bản chung

- 9 nhãn: car, truck, van, bus, tuk_tuk, motorcycle, bicycle, person, ignore_region.
- Thêm bicycle dù111/999 chưa đề xuất: mở rộng phạm vi để không loại xe đạp/xích lô có mặt nhiều lần; chỉ một lớp chung cho các xe đạp hai/ba bánh.
- occluded_level: 0 cho không che, 1 cho (0,30%], 2 cho (30%,70%], 3 cho >70% nhưng còn nhận ra; không dùng như mã không biết.
- Mỗi người/phương tiện là đối tượng riêng. Xe được chở vẫn có lớp xe; hàng rời không mở box vật chủ.
- Người thật đủ điều kiện sau kính được gán; hình in/phản chiếu không gán.
- Vật nhỏ dưới 20 px dùng ignore phù hợp; vùng đông chỉ ignore phần không tách được; lớp chưa chắc/biên không rõ không mặc định ignore.
- Không suy ego cho pickup012, không suy motorcycle040 chỉ từ mũ/gương, không tự thêm person022 từ kính/ghế.
- Giữ nguyên rules/thuộc tính kỹ thuật; taxonomy đổi từ v0 lên v1.0 và JSON CVAT được sinh lại.
- Trường hợp ảnh thực sự chưa đủ chứng cứ được ghi theo R-12 để calibration, thay vì dùng một khẳng định không kiểm được như quy tắc chung.

## 5. Phạm vi hoàn thành CP2

Đã có nội dung thống nhất một nghĩa cho các vấn đề được liệt kê. Kiểm schema và chạy bộ test repo được thực hiện sau khi ghi các file; kết quả kiểm tra được báo cùng lần bàn giao.

Bước triển khai còn phải thực hiện trước CP3: mọi người đọc cùng bản, thực hiện phép thử ảnh chung; tạo/cấu hình Project CVAT đúng JSON; 111 công bố commit/phiên bản khóa. Chưa tạo assignment, nhãn độc lập, IAA, calibration hoặc QC ở bước review này. Chưa commit/push bộ file Pha 2.

Không diễn giải file RULES.md đang được người dùng sửa như một thay đổi do lần review này tạo ra; giữ nguyên nội dung đó ngoài phạm vi chỉnh sửa.

## 6. Bằng chứng kiểm tra kỹ thuật

- `scripts/make_cvat_labels.py` đã kiểm JSON Schema thành công và sinh lại `cvat/labels_generated.json` với taxonomy v1.0.
- `python -m unittest discover -s tests -v`: 20/20 test OK trong môi trường Python tạm đã có đủ dependencies (Python 3.14). Không coi đây là bằng chứng kiểm CVAT trực tiếp hoặc chất lượng nhãn.
- Tên lớp/thứ tự lớp và ba thuộc tính mỗi lớp vật đã đối chiếu giữa guideline, taxonomy và JSON CVAT; ignore_region không có thuộc tính.
- Tám giá trị rules giữ nguyên; ba guideline cá nhân không bị lần hợp nhất này thay đổi quy tắc. Cập nhật metadata999 được ghi riêng ở Mục 1.
