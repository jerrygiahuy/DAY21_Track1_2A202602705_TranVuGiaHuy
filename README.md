# Lab 21 — Phân tích rủi ro AI qua case study thực tế

- **Họ và tên:** Trần Vũ Gia Huy
- **MSSV / mã học viên:** 2A202602705
- **Lớp:** AI Product
- **Ngành đã chọn:** Mobility / autonomous driving

## 1. Industry Risk Snapshot

| Nội dung | Đánh giá của tôi và lý do |
| --- | --- |
| Những tác hại chính có thể xảy ra | Hệ thống lái tự động có thể nhận diện hoặc dự đoán sai người đi bộ, xe và vật cản; chọn quỹ đạo không an toàn; phanh hoặc chuyển quyền điều khiển quá muộn; và khiến người giám sát quá tin vào tự động hóa. **Người đi bộ, hành khách, tài xế, người đi xe đạp và người tham gia giao thông** có thể bị thương hoặc tử vong. Nhân viên vận hành có thể chịu áp lực và trách nhiệm; doanh nghiệp và cơ quan quản lý có thể chịu thiệt hại pháp lý, tài chính và uy tín. |
| Mức độ high-stakes | **Cao.** Đầu ra AI điều khiển một phương tiện có khối lượng lớn trong môi trường thật, với thời gian phản ứng tính bằng giây. Một false negative hoặc quỹ đạo sai có thể chuyển trực tiếp thành va chạm và tổn hại thân thể nghiêm trọng. Đây là đánh giá định tính cho bài tập, không phải kết luận phân loại pháp lý. |
| Dữ liệu nhạy cảm có thể được sử dụng | Video/ảnh đường phố có khuôn mặt và biển số; vị trí GPS và lịch sử hành trình; dữ liệu cảm biến lidar/radar; dữ liệu trong cabin về ánh mắt, khuôn mặt hoặc hành vi tài xế; âm thanh; thời gian và địa điểm đón/trả khách; log điều khiển, sự cố và thông tin tài khoản. Bài chỉ nêu loại dữ liệu, không đưa dữ liệu cá nhân thật vào repo. |
| Nhu cầu human review | **Cao.** Trước triển khai, kỹ sư an toàn và chuyên gia độc lập cần duyệt Operational Design Domain, hazard analysis và kết quả kiểm thử tình huống biên. Khi vận hành thử, safety operator/đội hỗ trợ từ xa phải được đào tạo, theo dõi mức chú ý và có quyền dừng xe. Sau sự cố, doanh nghiệp và cơ quan quản lý cần xem log/video, xác minh độc lập, báo cáo đầy đủ và chỉ cho hoạt động lại sau khi kiểm soát rủi ro được chứng minh. |

## 2. Case study 1 — Xe thử nghiệm tự lái Uber va chạm chết người tại Tempe

### Brief Case

- **Tổ chức / sản phẩm AI:** Uber Technologies, Advanced Technologies Group (Uber ATG) — xe thử nghiệm Volvo XC90 được sửa đổi với hệ thống lái tự động đang phát triển (ADS).
- **Thời gian, địa điểm / bối cảnh:** 21:58 ngày 18/03/2018, đường N. Mill Avenue, Tempe, Arizona, Hoa Kỳ. Xe đang chạy thử trên đường công cộng ở chế độ tự động và có một safety operator ngồi ghế lái.
- **AI được dùng để làm gì:** Nhận biết môi trường, theo dõi và phân loại đối tượng, dự đoán quỹ đạo, lập kế hoạch chuyển động và điều khiển xe trong tuyến thử nghiệm.
- **Vấn đề hoặc sự kiện đáng chú ý:** Xe đâm một người đi bộ đang dắt xe đạp qua đường; nạn nhân tử vong. NTSB xác định nguyên nhân có khả năng nhất là safety operator không giám sát môi trường và ADS do bị phân tâm bởi điện thoại. Các yếu tố góp phần gồm quy trình đánh giá rủi ro an toàn không đầy đủ, giám sát operator kém hiệu quả và thiếu cơ chế xử lý automation complacency của Uber ATG.
- **Số liệu có nguồn:** ADS phát hiện nạn nhân **5,6 giây trước va chạm** nhưng không phân loại chính xác hoặc dự đoán đúng đường đi. Xe chạy khoảng **45 mph** khi tiếp cận; operator nhìn lại đường khoảng **1 giây trước va chạm**; xe va chạm ở khoảng **39 mph**. Xe đã chạy khoảng **19 phút ở chế độ tự động** trước sự cố. Báo cáo ghi nhận **1 người tử vong**.
- **Nguồn:** [“Collision Between Vehicle Controlled by Developmental Automated Driving System and Pedestrian” — National Transportation Safety Board (NTSB), điều tra HWY18MH010 — sự cố 18/03/2018, báo cáo cuối được thông qua 19/11/2019 — mục What Happened và What We Found](https://www.ntsb.gov/investigations/Pages/HWY18MH010.aspx); [*Highway Accident Report HAR-19/03* — NTSB — 19/11/2019 — Executive Summary và Chương 1](https://www.ntsb.gov/investigations/accidentreports/reports/har1903.pdf).
- **Phân biệt bằng chứng và nhận định:** **NTSB xác nhận** diễn biến, dữ liệu thời gian/tốc độ, cái chết và kết luận probable cause/contributing factors. **Tôi phân tích** lỗi nằm trên nhiều layer Model, Safety và UX. Tôi không suy luận rằng AI là nguyên nhân duy nhất: báo cáo chính thức nêu cả hành vi operator, thiết kế tổ chức và giám sát của bang.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Trong 5,6 giây từ khi ADS phát hiện nạn nhân đến lúc va chạm: hệ thống liên tục thay đổi phân loại, không dự đoán đúng đường đi và cuối cùng dựa vào operator can thiệp khi thời gian còn lại rất ngắn. |
| Stakeholder bị ảnh hưởng | Nạn nhân và gia đình; safety operator; người đi đường tại Tempe; nhân viên Uber ATG; cơ quan cấp phép/điều tra; công chúng sử dụng đường thử nghiệm và ngành xe tự hành. |
| Failure mode | **Model/perception failure** (phân loại và dự đoán quỹ đạo sai), kết hợp **over-reliance/automation complacency** và **escalation failure** (cơ chế chuyển trách nhiệm/phanh khẩn cấp không bảo đảm can thiệp kịp thời). |
| Layer bắt đầu lỗi | **Model:** ADS phát hiện nhưng không nhận dạng và dự đoán đúng nạn nhân. Harm còn bắt nguồn ở **Safety:** thiết kế không kích hoạt emergency braking để giảm nhẹ va chạm trong tình huống này; và **UX/operations:** một operator bị kỳ vọng giám sát liên tục nhưng cơ chế chống mất chú ý không đủ. Các layer tương tác, nên không quy toàn bộ lỗi cho một layer. |
| Harm xảy ra là gì? | **Đã xảy ra:** một người đi bộ tử vong sau va chạm; operator và các bên liên quan chịu hậu quả tâm lý, pháp lý và nghề nghiệp có thể có. Nguồn xác nhận cái chết nhưng không định lượng các hậu quả gián tiếp. **Nguy cơ:** lỗi tương tự trong thử nghiệm công cộng có thể gây thêm va chạm nếu kiểm soát không thay đổi. |
| Harm lens | **Injury** (tổn hại thể chất/tử vong) là chính; ngoài ra có dignity loss, mất niềm tin công chúng và rủi ro cơ hội phát triển/triển khai công nghệ an toàn. |
| Severity | **Critical** vì hậu quả thực tế là tử vong — mức nghiêm trọng đặc biệt và không thể đảo ngược. |
| Scale | **Low trong sự cố được phân tích:** 1 người tử vong trong một vụ va chạm. Phạm vi nguy cơ có thể rộng hơn vì thử nghiệm diễn ra trên đường công cộng, nhưng nguồn này không cho phép suy ra số người sẽ bị ảnh hưởng trong tương lai. |
| Probability | **Đã xảy ra trong case này; chưa đủ dữ liệu để tính xác suất chung.** Báo cáo cung cấp chuỗi lỗi của một sự cố, không cung cấp mẫu số tổng số dặm/tình huống tương đương để tính tỷ lệ. |
| Frequency | **Một sự cố trong phạm vi case.** Không dùng một vụ để kết luận lỗi thường xuyên hay hiếm trên toàn bộ chương trình; cần dữ liệu tổng số lượt gặp người đi bộ và disengagement/crash. |
| Vì sao? | Severity dựa trên hậu quả tử vong. Scale, Probability và Frequency được giữ tách biệt: hậu quả Critical không tự động có nghĩa quy mô hay tần suất cao. Các đánh giá kỹ thuật bám vào HAR-19/03; giới hạn là đây là điều tra một vụ va chạm, không phải phép đo error rate của toàn đội xe. |

## 3. Case study 2 — Cruise AV kéo lê người đi bộ sau va chạm tại San Francisco

### Brief Case

- **Tổ chức / sản phẩm AI:** Cruise LLC — xe tự hành không người lái (Cruise AV) vận hành tại San Francisco.
- **Thời gian, địa điểm / bối cảnh:** Khoảng 21:30 ngày 02/10/2023 tại giao lộ 5th Street và Market Street, San Francisco, California. Một xe do người lái trước tiên đâm người đi bộ và hất nạn nhân vào làn của Cruise AV đang chạy ở chế độ tự động.
- **AI được dùng để làm gì:** Phát hiện vật cản/va chạm, phanh và thực hiện quy trình phản ứng sau va chạm, gồm maneuver tấp xe vào vị trí an toàn.
- **Vấn đề hoặc sự kiện đáng chú ý:** Cruise AV phanh mạnh nhưng không tránh được va chạm ban đầu. Sau khi dừng, hệ thống nhận dạng sai tiếp xúc với người đi bộ là va chạm bên hông và thực hiện pullover maneuver khi nạn nhân vẫn ở dưới xe, kéo nạn nhân đi tiếp. CPUC sau đó xem xét cả sự cố và việc Cruise không cung cấp đầy đủ chi tiết về maneuver này cho cơ quan quản lý trong các trao đổi ban đầu.
- **Số liệu có nguồn:** CPUC ghi nhận xe kéo nạn nhân thêm khoảng **20 feet** với tốc độ lên tới **7,7 mph** (quyết định cuối làm tròn khoảng 7 mph). Quyết định CPUC cho biết Cruise không cung cấp đầy đủ thông tin về phần kéo lê trong **15 ngày**; thỏa thuận gồm khoản phạt **1,5 triệu USD**. Các số liệu này thuộc đúng sự cố 02/10/2023 và quá trình báo cáo với CPUC.
- **Nguồn:** [*Decision Approving Settlement Between the Consumer Protection and Enforcement Division and Cruise LLC* — California Public Utilities Commission (CPUC), Decision 24-06-022 — 20/06/2024 — Findings of Fact 1–5 và Ordering Paragraphs](https://docs.cpuc.ca.gov/PublishedDocs/Published/G000/M536/K690/536690391.PDF); [*Joint Motion for Adoption of Settlement Agreement* — CPUC Transportation Enforcement Branch và Cruise LLC — 05/02/2024 — Statement of Facts, tr. 5–6](https://docs.cpuc.ca.gov/PublishedDocs/Efile/G000/M524/K233/524233975.PDF); [“DMV Statement on Cruise LLC Suspension” — California Department of Motor Vehicles — 24/10/2023](https://www.dmv.ca.gov/portal/news-and-media/dmv-statement-on-cruise-llc-suspension/).
- **Phân biệt bằng chứng và nhận định:** **Hồ sơ CPUC xác nhận** chuỗi va chạm, pullover maneuver, quãng đường/tốc độ kéo lê, thiếu sót báo cáo và khoản phạt. **Tôi suy luận** đây là lỗi Safety bắt đầu từ phân loại va chạm/logic phản ứng sau va chạm. Va chạm đầu tiên với nạn nhân diễn ra sau khi một xe người lái hất nạn nhân vào đường xe Cruise; không nên mô tả Cruise AV là bên tạo ra toàn bộ tình huống ban đầu.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Sau khi Cruise AV va chạm và dừng lần đầu, hệ thống phải quyết định giữ nguyên vị trí hay tấp vào lề. Nó chọn pullover trong lúc nạn nhân vẫn nằm dưới xe. |
| Stakeholder bị ảnh hưởng | Người đi bộ và gia đình; người đi đường/nhân chứng; đội vận hành Cruise; hành khách và cư dân San Francisco; lực lượng cứu hộ; DMV, CPUC và các tổ chức phát triển xe tự hành khác. |
| Failure mode | **Misclassification/Model failure:** tiếp xúc với nạn nhân bị nhận dạng sai là side-impact. **Safety failure:** quy trình sau va chạm cho phép xe tiếp tục di chuyển khi chưa chắc vùng dưới xe đã an toàn. **Governance failure:** thông tin quan trọng không được chuyển đầy đủ, kịp thời cho regulator. |
| Layer bắt đầu lỗi | **Model** ở bước phân loại kiểu va chạm; lỗi gây harm trực tiếp ở **Safety** vì collision-response protocol kích hoạt pullover thay vì giữ xe đứng yên. Phần báo cáo sau sự cố thuộc governance, không phải bằng chứng rằng model gây ra việc che giấu. |
| Harm xảy ra là gì? | **Đã xảy ra:** nạn nhân vốn đã bị xe người lái đâm tiếp tục bị Cruise AV kéo khoảng 20 feet. Hồ sơ xác nhận sự cố nghiêm trọng nhưng nguồn được dẫn ở đây không tách định lượng phần thương tích do từng lần va chạm gây ra. **Nguy cơ:** xe tiếp tục chạy sau va chạm có thể làm nặng thêm thương tích hoặc cản trở cứu hộ. |
| Harm lens | **Injury**, dignity loss, mất niềm tin công chúng; ngoài ra có rủi ro minh bạch/accountability vì cơ quan quản lý cần dữ liệu đầy đủ để bảo vệ cộng đồng. |
| Severity | **Critical** theo tiềm năng và bối cảnh tổn hại thể chất đặc biệt nghiêm trọng: một người đang ở dưới xe bị kéo thêm. Không khẳng định phần kéo lê là nguyên nhân duy nhất của toàn bộ thương tích vì nguồn không phân tách như vậy. |
| Scale | **Low đối với sự cố cụ thể:** 1 nạn nhân. **Tác động hệ thống Medium:** sự cố dẫn tới đình chỉ giấy phép tại California và gián đoạn hoạt động, nhưng không có căn cứ để đồng nhất tác động vận hành với số người bị harm thể chất. |
| Probability | **Đã xảy ra trong case; xác suất tái diễn chưa đủ dữ liệu để đánh giá.** Không có mẫu số về tổng số va chạm/tình huống có người dưới xe hoặc tỷ lệ collision misclassification trong nguồn. |
| Frequency | **Một sự cố được xác nhận trong phạm vi phân tích.** Hồ sơ gọi đây là sự cố nghiêm trọng nhất trong lịch sử Cruise tại thời điểm điều tra nội bộ, nhưng không cung cấp tần suất lỗi cùng loại để gán Low/Medium/High đáng tin cậy. |
| Vì sao? | Các số liệu 20 feet, tối đa 7,7 mph, 15 ngày và 1,5 triệu USD có trong hồ sơ CPUC. Severity dựa trên cơ chế kéo lê người đang bị mắc dưới xe; còn Probability/Frequency để “chưa đủ dữ liệu” nhằm tránh biến một case nổi bật thành tỷ lệ tổng quát. |

## Kết luận

Hai case cùng ngành cho thấy rủi ro xe tự hành không chỉ nằm ở độ chính xác của perception model. Harm xuất hiện khi lỗi nhận biết kết hợp với thiết kế safety fallback, kỳ vọng không thực tế đối với người giám sát, quy trình vận hành và trách nhiệm báo cáo sau sự cố.

Với một AI Product trong mobility, các kiểm soát tối thiểu nên gồm: xác định rõ Operational Design Domain; kiểm thử tình huống biên và phản ứng sau va chạm; thiết kế trạng thái an toàn mặc định là dừng khi chưa chắc chắn; giám sát sự chú ý của safety operator; lưu log không thể sửa; điều tra sự cố độc lập; báo cáo regulator đầy đủ, đúng hạn; và chỉ mở rộng triển khai sau khi có bằng chứng an toàn theo quãng đường và theo từng loại tình huống.
