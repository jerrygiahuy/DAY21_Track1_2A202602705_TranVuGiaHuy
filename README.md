# Lab 21 — Phân tích rủi ro AI qua case study thực tế

- **Họ và tên:** Trần Vũ Gia Huy
- **MSSV / mã học viên:** 2A202602705
- **Lớp:** H201
- **Ngành đã chọn:** AI Product

## 1. Industry Risk Snapshot

| Nội dung | Đánh giá của tôi và lý do |
| --- | --- |
| Những tác hại chính có thể xảy ra | Trong AI Product, câu trả lời sai có thể khiến **người dùng** mất tiền hoặc ra quyết định sai; dự đoán/nhận dạng sai có thể khiến **khách hàng và các nhóm yếu thế** bị từ chối dịch vụ, theo dõi hoặc buộc tội oan; thu thập dữ liệu quá mức có thể xâm phạm **quyền riêng tư**; còn doanh nghiệp có thể chịu khiếu nại, trách nhiệm pháp lý và mất uy tín. Nhân viên tuyến đầu cũng bị ảnh hưởng nếu phải hành động dựa trên cảnh báo AI thiếu tin cậy. |
| Mức độ high-stakes | **Cao, nhưng phụ thuộc use case.** Một chatbot chỉ gợi ý nội dung có thể có rủi ro trung bình; tuy nhiên khi đầu ra tác động đến giá, quyền lợi, danh tiếng, quyền tiếp cận dịch vụ hoặc dẫn đến việc gọi cảnh sát thì hậu quả có thể nghiêm trọng và khó đảo ngược. Hai case dưới đây cho thấy cùng là AI Product nhưng mức độ tăng mạnh khi đầu ra được biến thành hành động ngoài đời. |
| Dữ liệu nhạy cảm có thể được sử dụng | Dữ liệu định danh và liên hệ; lịch sử giao dịch/hành vi; nội dung hội thoại hỗ trợ; vị trí và thời gian xuất hiện; ảnh khuôn mặt và mẫu sinh trắc học; các thuộc tính nhân khẩu học có thể được suy ra như tuổi, giới hoặc chủng tộc; dữ liệu về khiếu nại hay nghi ngờ vi phạm. Bài chỉ nêu **loại dữ liệu**, không chứa dữ liệu thật của cá nhân. |
| Nhu cầu human review | **Cao** tại các quyết định có thể gây thiệt hại. Product owner và chuyên gia nghiệp vụ cần duyệt nguồn tri thức, chính sách và thay đổi trước khi phát hành; đội QA/Safety kiểm thử theo nhóm người dùng và tình huống biên; nhân viên có thẩm quyền phải xác minh độc lập trước khi từ chối quyền lợi, buộc tội, đuổi khách hoặc báo cơ quan chức năng. Sau triển khai cần có giám sát sự cố, kênh khiếu nại và quyền chuyển sang người thật. |

## 2. Case study 1 — Chatbot Air Canada cung cấp sai chính sách giá vé tang chế

### Brief Case

- **Tổ chức / sản phẩm AI:** Air Canada — chatbot hỗ trợ khách hàng trên website.
- **Thời gian, địa điểm / bối cảnh:** Tháng 11/2022, một hành khách tại British Columbia, Canada dùng chatbot khi đặt chuyến bay gấp sau khi bà qua đời; phán quyết của Civil Resolution Tribunal (CRT) được ban hành ngày 14/02/2024.
- **AI được dùng để làm gì:** Trả lời câu hỏi về chính sách và hướng dẫn khách mua vé, cụ thể là vé ưu đãi dành cho trường hợp có tang.
- **Vấn đề hoặc sự kiện đáng chú ý:** Chatbot nói hành khách có thể mua vé thường rồi xin hoàn phần chênh lệch trong vòng 90 ngày. Thông tin này trái với chính sách thực tế của Air Canada, vốn không cho phép xin ưu đãi sau khi chuyến đi đã hoàn tất. Hành khách làm theo hướng dẫn, bị từ chối hoàn tiền và khởi kiện. CRT kết luận Air Canada đã trình bày sai do bất cẩn và không thực hiện sự cẩn trọng hợp lý để bảo đảm chatbot chính xác.
- **Số liệu có nguồn:** CRT yêu cầu Air Canada trả tổng cộng **812,02 CAD**, gồm **650,88 CAD** tiền bồi thường, **36,14 CAD** lãi trước phán quyết và **125 CAD** phí CRT. Đây là số tiền cho một nguyên đơn trong vụ `2024 BCCRT 149`, không phải ước tính thiệt hại của toàn bộ người dùng chatbot.
- **Nguồn:** [*Moffatt v. Air Canada*, 2024 BCCRT 149 — British Columbia Civil Resolution Tribunal — 14/02/2024 — bản quyết định, đặc biệt các đoạn 2–6, 23–31 và 35–37](https://decisions.civilresolutionbc.ca/crt/crtd/en/item/525448/index.do); [“What’s Hot on CanLII? — February 2024” — CanLII — 07/03/2024 — phần về *Moffatt v. Air Canada*](https://blog.canlii.org/2024/03/07/whats-hot-on-canlii-%F0%9F%94%A5-february-2024/).
- **Phân biệt bằng chứng và nhận định:** **Nguồn xác nhận** nội dung chatbot, việc hành khách dựa vào nội dung đó, chính sách thật, kết luận trách nhiệm và khoản tiền phải trả. **Tôi suy luận** đây là lỗi quản trị nguồn tri thức/grounding và kiểm thử sản phẩm. Phán quyết không mô tả kiến trúc, dữ liệu huấn luyện hay loại mô hình, nên chưa thể khẳng định nguyên nhân kỹ thuật là hallucination của mô hình sinh.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Khi chatbot đưa ra hướng dẫn đủ cụ thể để khách quyết định mua vé giá thường trước, với kỳ vọng sẽ được hoàn chênh lệch sau chuyến đi. |
| Stakeholder bị ảnh hưởng | Trực tiếp: hành khách và gia đình đang trong hoàn cảnh có tang. Gián tiếp: nhân viên chăm sóc khách hàng, Air Canada, cơ quan giải quyết tranh chấp và những khách khác có thể đã nhận cùng câu trả lời. |
| Failure mode | **Thông tin chính sách sai và mâu thuẫn giữa các kênh**; người dùng không được cảnh báo về độ không chắc chắn hoặc chuyển sang nhân viên trước khi thực hiện giao dịch tài chính. |
| Layer bắt đầu lỗi | Gần nhất là **Grounding**: câu trả lời không bám đúng chính sách hiện hành; đồng thời có lỗi **UX/governance** vì chatbot trình bày chỉ dẫn như thông tin có thể tin cậy mà không buộc xác minh. Chưa đủ bằng chứng để quy lỗi cho **Model** cụ thể. |
| Harm xảy ra là gì? | **Đã xảy ra:** hành khách trả mức giá cao hơn, mất thời gian khiếu nại và phải theo đuổi tranh chấp. **Nguy cơ rộng hơn:** nhiều khách có thể ra quyết định tài chính sai nếu cùng nội dung được tái sử dụng; nguồn hiện có không cho biết có trường hợp nào khác. |
| Harm lens | Thiệt hại kinh tế; thông tin sai lệch; tổn hại trải nghiệm và niềm tin; rủi ro pháp lý và uy tín của doanh nghiệp. |
| Severity | **Medium** đối với cá nhân trong vụ việc: thiệt hại tiền và công sức là thực, nhưng không có bằng chứng về thương tích hoặc tước quyền thiết yếu. Với doanh nghiệp, rủi ro có thể cao hơn nếu lỗi lặp lại ở quy mô lớn. |
| Scale | **Thấp theo bằng chứng đã công bố:** phán quyết chứng minh một nguyên đơn và 812,02 CAD. Chatbot là kênh công khai nên có khả năng ảnh hưởng nhiều người, nhưng không có số liệu người dùng nhận cùng câu trả lời; không được suy rộng từ một vụ kiện. |
| Probability | **Đã xảy ra trong tình huống được ghi nhận.** Với một lượt hỏi tương lai, đánh giá định lượng là **chưa xác định** vì nguồn không công bố error rate, số lượt hỏi hoặc thời gian câu trả lời sai tồn tại. |
| Frequency | **Ít nhất 1 sự cố được xác nhận.** Không đủ dữ liệu để gọi là hiếm hay thường xuyên trên toàn hệ thống. |
| Vì sao? | Mức độ và thiệt hại dựa trên phán quyết CRT. Việc gán Grounding/UX là phân tích sản phẩm từ biểu hiện quan sát được, không phải kết luận kỹ thuật của CRT. Giới hạn quan trọng nhất là không có log hệ thống, mẫu kiểm thử hay mẫu số tổng lượt tương tác. |

## 3. Case study 2 — Rite Aid dùng nhận diện khuôn mặt và tạo cảnh báo nhầm

### Brief Case

- **Tổ chức / sản phẩm AI:** Rite Aid — hệ thống nhận diện khuôn mặt do hai nhà cung cấp hỗ trợ, dùng tại các cửa hàng bán lẻ ở Hoa Kỳ.
- **Thời gian, địa điểm / bối cảnh:** Theo đơn kiện của Federal Trade Commission (FTC), Rite Aid triển khai hệ thống từ **2012 đến 2020** tại **hàng trăm cửa hàng** ở Hoa Kỳ. FTC công bố vụ việc và lệnh dàn xếp đề xuất ngày 19/12/2023.
- **AI được dùng để làm gì:** So sánh khuôn mặt khách vào cửa hàng với cơ sở dữ liệu “person of interest” nhằm phát hiện người bị cho là từng trộm cắp hoặc có hành vi đáng ngờ.
- **Vấn đề hoặc sự kiện đáng chú ý:** FTC cáo buộc Rite Aid không đánh giá độ chính xác trước triển khai, dùng ảnh chất lượng thấp, không theo dõi tỷ lệ false positive và đào tạo nhân viên không đầy đủ. Nhân viên đã hành động theo cảnh báo sai: theo dõi, lục soát, yêu cầu khách rời đi, công khai buộc tội hoặc gọi cảnh sát. FTC cũng cho rằng tác động bất cân xứng rơi vào người da màu.
- **Số liệu có nguồn:** Hệ thống hoạt động khoảng **8 năm (2012–2020)** tại **hàng trăm cửa hàng**, dùng cơ sở dữ liệu có **hàng chục nghìn ảnh** và tạo ra **hàng nghìn false-positive matches**, theo đơn kiện FTC. Dàn xếp đề xuất cấm Rite Aid dùng nhận diện khuôn mặt cho mục đích giám sát trong **5 năm**. Nguồn không cung cấp mẫu số tổng số lượt quét/cảnh báo nên không tính được tỷ lệ lỗi.
- **Nguồn:** [“Rite Aid Banned from Using AI Facial Recognition…” — Federal Trade Commission — 19/12/2023 — thông cáo và liên kết hồ sơ vụ việc](https://www.ftc.gov/news-events/news/press-releases/2023/12/rite-aid-banned-using-ai-facial-recognition-after-ftc-says-retailer-deployed-technology-without); [*Complaint for Permanent Injunction and Other Relief*, FTC v. Rite Aid Corp. — FTC, U.S. District Court for the Eastern District of Pennsylvania — nộp 19/12/2023 — đặc biệt đoạn 8–12, 18–50](https://www.ftc.gov/system/files/ftc_gov/pdf/2023190_riteaid_complaint_filed.pdf); [“FTC Warns About Misuses of Biometric Information…” — FTC — 18/05/2023](https://www.ftc.gov/news-events/news/press-releases/2023/05/ftc-warns-about-misuses-biometric-information-harm-consumers).
- **Phân biệt bằng chứng và nhận định:** **Hồ sơ FTC xác nhận đây là các cáo buộc của cơ quan quản lý và mô tả dữ liệu/sự cố làm căn cứ;** thông cáo ghi lệnh khi đó còn cần phê duyệt của tòa phá sản và tòa liên bang. Vì vậy, tôi không diễn đạt mọi cáo buộc như một phán quyết sau xét xử. **Tôi suy luận** chuỗi lỗi trải qua Model/Data, Safety và UX: matching kém tin cậy trở thành hành động cưỡng chế vì thiếu ngưỡng dừng và xác minh độc lập.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Khi hệ thống tạo một match alert và nhân viên coi cảnh báo là căn cứ để theo dõi, khám xét, đuổi khách, buộc tội công khai hoặc gọi cảnh sát mà chưa xác minh độc lập. |
| Stakeholder bị ảnh hưởng | Khách hàng bị nhận dạng nhầm, đặc biệt người da màu và phụ nữ; gia đình/bạn bè chứng kiến; nhân viên cửa hàng; cộng đồng quanh cửa hàng; Rite Aid, nhà cung cấp và cơ quan thực thi pháp luật. |
| Failure mode | **False positive**, dữ liệu ảnh đầu vào chất lượng thấp, chênh lệch hiệu năng theo nhóm, thiếu kiểm thử trước triển khai, thiếu giám sát sau triển khai và automation bias của nhân viên. |
| Layer bắt đầu lỗi | **Model/Data** là điểm kỹ thuật sớm nhất có thể quan sát: ảnh enrollment kém chất lượng và match sai. Lỗi trở thành harm ở **Safety/UX** vì thiếu kiểm soát confidence, cảnh báo false positive, quy trình xác minh và đào tạo. Không đủ dữ liệu để tách chính xác tỷ trọng lỗi thuật toán và lỗi vận hành. |
| Harm xảy ra là gì? | **FTC cáo buộc đã xảy ra:** khách bị làm nhục, quấy rối, theo dõi, lục soát, đuổi khỏi cửa hàng, buộc tội trước người khác hoặc bị cảnh sát đối chất. Đơn kiện nêu một phụ nữ da đen bị hệ thống ghép với ảnh một phụ nữ da trắng tóc vàng; nhân viên gọi cảnh sát và yêu cầu bà rời đi trước khi nhận ra lỗi. **Nguy cơ:** leo thang thành bắt giữ, bạo lực hoặc rò rỉ dữ liệu sinh trắc học; hồ sơ được dẫn không xác nhận các hậu quả cực đoan này đã xảy ra trong vụ Rite Aid. |
| Harm lens | Phân biệt đối xử và công bằng; phẩm giá và danh tiếng; quyền riêng tư/sinh trắc học; an toàn thân thể; quyền tiếp cận dịch vụ; rủi ro pháp lý. |
| Severity | **High**, có thể **Critical** nếu cảnh báo sai leo thang thành cưỡng chế hoặc thương tích. Chọn High cho bằng chứng trong hồ sơ vì hành vi đã vượt khỏi màn hình sang đối xử trực tiếp và có trường hợp gọi cảnh sát, nhưng chưa có căn cứ trong nguồn này về thương tích hay tử vong. |
| Scale | **Cao:** hàng trăm cửa hàng, hàng chục nghìn ảnh và hàng nghìn false positives trong giai đoạn 2012–2020 theo FTC. Tuy vậy, “match sai” không đồng nghĩa mọi trường hợp đều dẫn đến hành động gây hại, và nguồn không công bố số nạn nhân duy nhất. |
| Probability | **Đáng kể trong hệ thống đã triển khai:** hàng nghìn false positives được cáo buộc. Không thể đưa ra phần trăm vì thiếu tổng số so sánh, tổng cảnh báo và định nghĩa đo lường nhất quán. Rủi ro cao hơn khi ảnh kém chất lượng và nhân viên không xác minh. |
| Frequency | **Lặp lại**, không phải sự cố đơn lẻ, vì FTC mô tả hàng nghìn match sai trong khoảng tám năm. Không thể chuyển thành tần suất theo ngày/cửa hàng do thiếu phân bố theo thời gian và địa điểm. |
| Vì sao? | Đánh giá dựa trên quy mô và các hành vi cụ thể trong hồ sơ FTC, đồng thời giữ đúng địa vị pháp lý là cáo buộc/dàn xếp đề xuất tại thời điểm công bố. Severity cao vì đầu ra tự động kích hoạt can thiệp ngoài đời; giới hạn lớn nhất là thiếu mẫu số, audit độc lập và số người duy nhất bị ảnh hưởng. |

## Kết luận ngắn cho AI Product

Hai case cho thấy độ chính xác của mô hình chỉ là một phần của an toàn sản phẩm. Với Air Canada, một câu trả lời sai trở thành thiệt hại vì nội dung không được gắn với nguồn chính sách đáng tin cậy và người dùng không được chuyển sang người thật trước giao dịch. Với Rite Aid, false positive trở thành tổn hại nghiêm trọng vì nhân viên được đặt vào quy trình có thể hành động lên con người mà thiếu xác minh và cơ chế dừng.

Vì vậy, một AI Product an toàn cần: xác định quyết định nào không được tự động hóa hoàn toàn; duy trì nguồn dữ liệu có chủ sở hữu và phiên bản; kiểm thử theo nhóm người dùng; hiển thị độ không chắc chắn; yêu cầu human review trước hành động bất lợi; ghi log, đo false positive/false negative; và cung cấp cơ chế khiếu nại, sửa sai, xóa dữ liệu.
