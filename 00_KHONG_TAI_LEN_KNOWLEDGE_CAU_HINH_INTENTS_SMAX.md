# CẤU HÌNH MODEL, SKILL, CAMPAIGN TARGET VÀ INTENTS CHO LARIS

> Tệp dành cho quản trị viên. Không tải tệp này vào Knowledge.

> Trạng thái khuyến nghị ngày 18/07/2026: giữ `Trigger GenAI` TẮT và dùng `Messenger Default → AI_NHAN_TIN → AI_GOM_TIN → AI_TRA_LOI` cho tới khi luồng gom tin đã chạy ổn định. Chỉ áp dụng cấu hình Intent trong tệp này khi chuyển sang kiến trúc thay thế; lúc đó phải TẮT Messenger Default và cho nhánh `Other` gửi block `AI_NHAN_TIN`. Không bật hai bộ định tuyến tổng quát cùng lúc.

## 1. Đánh giá cấu hình hiện tại

### Mô hình đọc văn bản

Đang chọn trong Smax tại thời điểm cấu hình: `GPT-5.4 mini`.

Khuyến nghị: giữ nguyên làm mốc đối chứng cho vận hành hiện tại. Đây là cấu hình hợp lý cho hội thoại ngắn, tra cứu Knowledge, báo giá và thu thập lịch, với ưu tiên tốc độ/chi phí. Chất lượng phải được quyết định bằng bộ Demo & Review, không chỉ bằng tên model.

Danh sách model Smax kiểm tra ngày 19/07/2026 có `GPT-5.4` và `GPT-5.4 mini`, chưa có GPT-5.6 Sol/Terra/Luna. Sau khi luồng đầu vào đạt test, A/B test `GPT-5.4 mini` với `GPT-5.4`; không chọn model không xuất hiện trong giao diện và không đổi model trước khi sửa lỗi luồng. Tham khảo [hướng dẫn nâng cấp GPT-5.6](https://developers.openai.com/api/docs/guides/upgrading-to-gpt-5p6-sol.md).

Không đổi model và sửa Prompt đồng thời trong cùng một lần test. Giữ nguyên Prompt, so sánh model trước; sau đó chỉ sửa Prompt theo lỗi cụ thể đã quan sát.

Nếu nút bánh răng cạnh model có các tham số tương ứng, điểm bắt đầu khuyến nghị:

- Reasoning effort: `Low`; tăng `Medium` chỉ khi test cho thấy model tính giá/hiểu ngữ cảnh chưa ổn.
- Verbosity: `Low`.
- Temperature/Creativity: khoảng `0.2`.
- Max output: khoảng `300` tokens là đủ cho giới hạn 500 ký tự và vẫn dư cho tóm tắt lịch.

Không phải phiên bản Smax nào cũng hiển thị đủ các tham số này; chỉ chỉnh mục thực sự có trên giao diện.

### Mô hình đọc hình ảnh

Đang chọn: `GPT-5`.

Khuyến nghị: giữ nguyên. Bot Laris chỉ cần ghi nhận ảnh mẫu và ước lượng sơ bộ độ dài/size; Prompt đã cấm dùng ảnh để chốt nền tóc, độ khỏe, công thức màu hay cam kết kết quả. Vì vậy không cần tăng model ảnh chỉ để “tư vấn sâu” trái quy tắc.

### Skill — Mục đích

Đang chọn: `Chat Sales Support`.

Khuyến nghị: đúng, giữ nguyên. Mục tiêu Laris là trả lời dịch vụ/giá, xử lý băn khoăn và dẫn sang đặt lịch tự nhiên. `Product/Service Consulting` có thể khiến bot thiên về tư vấn chuyên môn dài hơn; `Marketing and Customer Care` không sát luồng bán hàng tại chat; hai mục còn lại không phù hợp chatbot tư vấn salon.

### Campaign Target

Đang chọn: `Leads Optimization`.

Khuyến nghị: đúng, giữ nguyên. Chuyển đổi chính của Laris là thu được yêu cầu đặt lịch kèm tên, SĐT, ngày giờ và dịch vụ — tức một lead. Không chọn `Purchases Optimization` khi chatbot chưa tạo giao dịch/thanh toán; không chọn `Values Optimization` khi chưa có giá trị đơn hàng/doanh thu làm mục tiêu.

Tài liệu Smax hiện chưa mô tả ba lựa chọn Campaign Target, nên nhận định này dựa trên tên mục tiêu hiển thị và luồng kinh doanh thực tế của Laris.

## 2. Intents ảnh hưởng thế nào

Intent giúp AI nhận diện mục đích của tin nhắn. Khi một Intent có trường thu thập:

- `Yêu cầu = Yes`: AI sẽ hỏi để lấy trường này trước khi hoàn tất Intent.
- `Yêu cầu = No`: có thì ghi nhận, thiếu thì không cần ép khách trả lời.
- Trong Bot-Auto Trigger Gen AI, Intent có thể được cấu hình `Trả lời trực tiếp` hoặc `Gửi block`.
- Nhánh `Other` nhận các tin nhắn không khớp Intent đã tạo.

Vì tài liệu Smax không công bố quy tắc ưu tiên khi nhiều Intent cùng khớp, mô tả của từng Intent phải loại trừ nhau và số lượng nên ít. Không dùng Intent cho mọi FAQ.

## 3. Intent bắt buộc — `dat_lich_moi`

### Tên

`dat_lich_moi`

### Mô tả — sao chép nguyên văn

`Phát hiện khi khách thể hiện rõ muốn đặt hoặc giữ một lịch mới tại Laris, chọn ngày giờ muốn đến, hoặc đang cung cấp thông tin để tạo lịch mới. Dùng lại thông tin đã có trong lịch sử và chỉ hỏi phần còn thiếu. Không kích hoạt khi khách chỉ hỏi giá, ưu đãi, địa chỉ, giờ mở cửa, yêu cầu stylist tư vấn trực tiếp màu/kiểu nhưng chưa đồng ý tới salon, nói sẽ cân nhắc rồi báo sau, hoặc muốn đổi/hủy một lịch đã có.`

### Thông tin thu thập

| Thông tin thu thập | Yêu cầu | Loại giá trị | Mô tả — sao chép nguyên văn |
|---|---|---|---|
| `customer_name` | Yes | Text | `Tên khách dùng để lưu yêu cầu đặt lịch. Chỉ hỏi nếu khách chưa cung cấp trong hội thoại.` |
| `phone_number` | Yes | Text | `Số điện thoại liên hệ để salon kiểm tra và xác nhận lịch. Giữ nguyên số 0 ở đầu; chỉ hỏi nếu chưa có.` |
| `appointment_date` | Yes | Date | `Ngày cụ thể khách muốn đến salon theo múi giờ Việt Nam GMT+7. Nếu khách nói thời gian tương đối mà hệ thống không xác định chắc chắn, hỏi ngày cụ thể.` |
| `appointment_time` | Yes | Text | `Giờ cụ thể khách muốn đến, chuẩn hóa dạng 24 giờ như 14h hoặc 14h30. Chỉ nhận trong giờ salon 9h–20h.` |
| `service` | No | Text | `Dịch vụ khách dự định làm. Giữ đúng biến thể khách nói: DUOI = Duỗi, DUOI_HOI_NUOC = Duỗi hơi nước, DUOI_CHAN_TOC = Duỗi chân tóc, XU_LY_DUOI_CHAN_KEM_UON = Xử lý Duỗi chân tóc đi kèm Uốn. Không gộp các biến thể thành DUOI. DUOI_CHAN_TOC không được làm thay đổi size toàn bộ tóc; nếu vùng khách xác nhận cần duỗi dài qua ngực thì dùng DUOI. Nếu khách chưa biết, ghi stylist tư vấn trực tiếp tại salon và không hỏi lặp lại.` |

### Active

Bật `Active`.

### Hành động trong Bot-Auto

- Intent: `dat_lich_moi`.
- Hành động: `Gửi block`.
- Block đề xuất: `LARIS_TIEP_NHAN_LICH_MOI`.
- Block dùng cùng bộ nhớ lịch production. Khi đủ Tên, SĐT, Ngày, Giờ và Dịch vụ, chốt ngay và đồng bộ n8n; thiếu trường nào thì hỏi đúng trường đó.

Mẫu nội dung block:

`Dạ em xác nhận đặt lịch hẹn cho mình ạ. - Khách hàng: ... - SĐT: ... - Thời gian: ... - Dịch vụ: ...`

Bản xác nhận đủ dữ liệu được phép kết thúc ngay sau Dịch vụ. Không bắt buộc CTA/câu kết; không tự thêm câu hỏi tư vấn chung. Đổi lịch, thêm dịch vụ và hủy lịch cũng kết thúc sau thông tin lịch trừ khi còn dữ liệu bắt buộc phải hỏi.

## 4. Intent nên có — `doi_huy_lich`

### Tên

`doi_huy_lich`

### Mô tả — sao chép nguyên văn

`Phát hiện khi khách muốn đổi ngày, đổi giờ, hoãn hoặc hủy một lịch đã đặt trước đó tại Laris. Không dùng cho khách đang đặt lịch mới, chỉ hỏi giá/dịch vụ, hoặc chưa từng nói có lịch cũ.`

### Thông tin thu thập

| Thông tin thu thập | Yêu cầu | Loại giá trị | Mô tả — sao chép nguyên văn |
|---|---|---|---|
| `phone_number` | Yes | Text | `SĐT đã dùng cho lịch cũ để nhân viên tra cứu. Giữ nguyên số 0 ở đầu.` |
| `request_type` | Yes | Text | `Yêu cầu của khách: đổi lịch, hoãn lịch hoặc hủy lịch.` |
| `current_appointment_date` | No | Date | `Ngày của lịch hiện tại nếu khách nhớ và đã cung cấp.` |
| `new_appointment_date` | No | Date | `Ngày mới khách mong muốn nếu yêu cầu đổi lịch.` |
| `new_appointment_time` | No | Text | `Giờ mới khách mong muốn nếu yêu cầu đổi lịch, trong khung 9h–20h.` |
| `note` | No | Text | `Ghi chú liên quan mà khách chủ động cung cấp.` |

### Active

Bật `Active`.

### Hành động trong Bot-Auto

- Intent: `doi_huy_lich`.
- Hành động: `Gửi block` dùng cùng các thuộc tính `laris_booking_status` và `laris_booking_action`.
- Đổi lịch dùng `UPDATE`; hủy dùng `CANCEL` + `CANCELLED` và phải cập nhật cùng dòng database.
- Không dùng `Trả lời trực tiếp` cho Intent này nếu đang gặp lỗi AI gửi kèm JSON/object ra khách.
- Block chỉ được chứa câu tiếng Việt tự nhiên; không chèn toàn bộ object dữ liệu thu thập, không chèn attribute dạng JSON và không dùng các key như `phone-number`, `request-type`, `new-appointment-date` trong nội dung gửi khách.

Mẫu block:

`Dạ em xác nhận đã hủy lịch hẹn của mình ạ. - Khách hàng: ... - SĐT: ... - Thời gian: ... - Dịch vụ: ...`

Mẫu block rõ hơn cho đổi lịch:

`Dạ em xác nhận đã đổi lịch hẹn cho mình ạ. - Khách hàng: ... - SĐT: ... - Thời gian: ... - Dịch vụ: ...`

Nếu cần lưu thông tin SĐT/ngày giờ mới, hãy lưu bằng trường thu thập/attribute của Smax hoặc để nhân viên xem trong hội thoại; không in dữ liệu đó ra tin nhắn khách.

## 5. Intent nên có — `can_nhan_vien_ho_tro`

### Tên

`can_nhan_vien_ho_tro`

### Mô tả — sao chép nguyên văn

`Phát hiện khi khách yêu cầu rõ muốn gặp hoặc nói chuyện với nhân viên, đang khiếu nại/phàn nàn cần người xử lý, hoặc cần kiểm tra thông tin nội bộ mà chatbot không thể xác nhận. Không kích hoạt cho câu hỏi giá, ưu đãi, địa chỉ, giờ mở cửa, tư vấn tóc thông thường, đặt lịch mới hoặc đổi/hủy lịch vì đã có luồng riêng.`

### Thông tin thu thập

| Thông tin thu thập | Yêu cầu | Loại giá trị | Mô tả — sao chép nguyên văn |
|---|---|---|---|
| `support_reason` | No | Text | `Lý do cần nhân viên hỗ trợ; dùng nội dung khách đã nói, không bắt khách kể lại.` |

Không đặt trường bắt buộc để tránh giữ khách ở lại trong vòng hỏi đáp khi họ đã yêu cầu người thật.

### Active

Bật `Active`.

### Hành động trong Bot-Auto

- Intent: `can_nhan_vien_ho_tro`.
- Hành động: `Gửi block` chuyển nhân viên/chăm sóc khách hàng.

Mẫu block:

`Dạ em đã ghi nhận rồi ạ. Em chuyển thông tin để nhân viên Laris hỗ trợ trực tiếp cho mình nha.`

## 6. Nhánh `Other`

Trong Trigger Gen AI, cấu hình:

- Intent: `Other`.
- Không dùng hành động `Trả lời trực tiếp` cho cấu hình Laris hiện tại vì ảnh kiểm thử cho thấy hai tin liên tiếp đang tạo hai phản hồi gần giống nhau.
- Hành động bắt buộc: đưa vào block gom tin 12–15 giây, sau đó chạy một chuỗi duy nhất: `AI_TRA_LOI` (`AI Trạng Thái Lịch` → Parse Content chín thuộc tính bền vững → `Bot AI`) → `AI_JSON_GUI` (`AI Tạo Json`) → `AI_GUI_TRA_LOI` (n8n gửi và đồng bộ lịch).

Nhánh này để Prompt + Knowledge xử lý chào hỏi, giá, ưu đãi, size tóc, giới hạn tư vấn màu và chuyển stylist, địa chỉ, giờ làm việc và các câu hỏi thông thường.

Không để `Other` trả lời trực tiếp đồng thời với một Messenger Default, Keyword chung hoặc block khác cũng gọi GenAI. Mỗi tin khách chỉ được đi vào một luồng trả lời tổng quát.

Sự kiện `Click To Message` của quảng cáo phải dùng Trigger riêng, không gọi `Other` bằng nguyên content quảng cáo. Cấu hình chi tiết nằm trong `00_KHONG_TAI_LEN_KNOWLEDGE_HUONG_DAN_CAU_HINH_SMAX.md`.

## 7. Những Intent không nên tạo lúc này

Không tạo riêng:

- `hoi_gia`.
- `hoi_uu_dai`.
- `hoi_dia_chi`.
- `hoi_gio_mo_cua`.
- `tu_van_mau`.
- `tu_van_kieu_toc`.

Các ý định này không cần thu thập dữ liệu hay chạy block. Tạo thêm sẽ tăng nguy cơ một tin nhắn khớp nhiều Intent, trong khi tài liệu Smax chưa nói rõ cơ chế ưu tiên xung đột.

## 8. Tám ca bắt buộc test sau khi tạo

1. `Nhuộm VIP size M bao nhiêu?` → `Other`, trả lời trực tiếp; không hỏi tên/SĐT.
2. `Chị muốn đặt lịch nhuộm VIP chiều mai` → `dat_lich_moi`, hỏi đúng trường còn thiếu.
3. `Chị đã có lịch rồi, giờ muốn dời sang thứ 7` → `doi_huy_lich`, không vào đặt mới.
4. `Cho chị gặp nhân viên, chị cần phản ánh` → `can_nhan_vien_ho_tro`, không bắt kể lại lý do.
5. `Địa chỉ salon ở đâu?` → `Other`, trả lời trực tiếp.
6. `Chị chưa đặt đâu, hỏi giá trước thôi` → `Other`, không kích hoạt `dat_lich_moi`.
7. Gửi `Nhuộm`, sau 3–10 giây gửi `Có loại nào` → chỉ một lượt Gen AI và một phản hồi liệt kê ba gói; không trả lời hai lần.
8. Sau khi bot hỏi gói + size, gửi `Size L` → bot liệt kê giá size L của cả Basic, VIP và cao cấp rồi hỏi chọn gói; không tự chọn VIP và không kích hoạt lại ảnh size.

Nếu một câu bị nhận sai Intent, sửa phần mô tả/điều kiện loại trừ trước; không thêm quy tắc dài vào Prompt chính.
