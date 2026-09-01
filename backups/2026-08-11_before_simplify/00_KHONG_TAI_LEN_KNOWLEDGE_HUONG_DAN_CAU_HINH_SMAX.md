# HƯỚNG DẪN TRIỂN KHAI BỘ LARIS GENAI TỐI ƯU

> Tệp dành cho quản trị viên. Không tải tệp này vào Knowledge.

## 1. Cấu hình khuyến nghị

- Model đọc văn bản đang dùng trong Smax: `GPT-5.4 mini` là mốc đối chứng. Smax hiện có `GPT-5.4`; đây là model nên A/B test cho luồng tư vấn nhiều ràng buộc sau khi đã sửa luồng đầu vào. Không đổi model trước khi sửa luồng vì model mạnh hơn vẫn trả lời sai nếu nhận sai tin hiện tại.
- Model đọc hình ảnh: giữ `GPT-5`.
- Skill/Mục đích: `Chat Sales Support`.
- Campaign Target: `Leads Optimization`.
- Creativity/Temperature: khoảng `0.2`.
- Reasoning effort nếu có: giữ `Low` làm mốc hiện tại; chỉ thử `Medium` nếu test giá/ngữ cảnh chưa ổn và so sánh lại độ trễ.
- Verbosity nếu có: `Low`.

## 2. Prompt

Thay toàn bộ ô Prompt bằng nội dung [Prompt Chính.Md](Prompt%20Chính.Md).

Không dán bảng giá hoặc toàn bộ Knowledge vào Prompt. Prompt chỉ điều phối vai trò, ý định, trí nhớ hội thoại và các ngoại lệ quan trọng.

## 3. Xóa Knowledge cũ

Trong Smax, xóa/tắt toàn bộ 10 Knowledge cũ có tên bắt đầu từ `01_LARIS_` đến `10_LARIS_`.

Không giữ đồng thời bản cũ và mới. Chỉ một đoạn cũ như “hỏi nam hay nữ” hoặc “nhắc ưu đãi mọi lượt” cũng có thể kéo câu trả lời sai trở lại.

## 4. Tạo đúng 6 Knowledge mới

Tạo dạng `Văn bản`, dùng đúng tiêu đề và nội dung tương ứng:

1. `K01_LARIS_THONG_TIN_SALON`
2. `K02_LARIS_DICH_VU_BANG_GIA_SIZE`
3. `K03_LARIS_UU_DAI_HIEN_TAI`
4. `K04_LARIS_LOGIC_TU_VAN_HOI_THOAI`
5. `K05_LARIS_TU_VAN_MAU_VA_AN_TOAN`
6. `K06_LARIS_DAT_LICH_CHUYEN_NHAN_VIEN`

Không bật `Áp dụng tất cả (Apply all)` nếu tài khoản còn bất kỳ Knowledge kỹ thuật hoặc tài liệu cũ nào. Tắt `Apply all` và chọn thủ công đúng sáu Knowledge K01–K06. Đặc biệt không gắn Knowledge `cardconfig` vào `Bot AI`; đây không phải dữ liệu tư vấn Laris.

Không tải các tệp bắt đầu bằng `00_KHONG_TAI_LEN_KNOWLEDGE`.

Riêng khung `00_KHONG_TAI_LEN_KNOWLEDGE_KHUNG_K07_CHINH_SACH_THOI_GIAN_CHAM_SOC.md`: chỉ dùng để điền dữ liệu vận hành thật của salon. Khi đã điền đủ, kiểm tra xong và đổi tên thành `K07_LARIS_CHINH_SACH_THOI_GIAN_CHAM_SOC.md` thì mới cân nhắc tải lên Knowledge.

## 5. Lịch sử tin nhắn — rất quan trọng

Luật chống lặp và ghi nhớ size/gói chỉ hoạt động tốt khi model được đọc lịch sử.

Khuyến nghị:

- Nếu Smax cho chọn phiên: dùng `1 phiên hội thoại gần nhất`.
- Nếu chỉ chọn số tin nhắn: dùng khoảng `20–30 tin nhắn gần nhất`.
- Không tắt lịch sử.

Sau khi thay đổi số lượng lịch sử, test chuỗi nhiều lượt: hỏi giá → cho size → cho gói → hỏi so sánh.

## 6. Mẫu phản hồi cố định

Chỉ nên tạo ba mẫu ổn định, không chứa giá động hoặc ưu đãi tháng:

### Địa chỉ

- Câu hỏi: `Salon ở đâu? / Xin địa chỉ / Địa chỉ Laris`
- Trả lời: `Dạ Laris ở 39 Trần Nhân Tôn, phường An Đông, TP. HCM ạ.`

### Giờ làm việc

- Câu hỏi: `Mấy giờ mở cửa? / Salon làm tới mấy giờ?`
- Trả lời: `Dạ salon mở cửa từ 9h đến 20h, tất cả các ngày từ thứ 2 đến chủ nhật nha mình.`

### Hotline

- Câu hỏi: `Cho xin số điện thoại / Hotline salon`
- Trả lời: `Dạ hotline Laris là 08.5555.9997 ạ.`

Không thêm CTA đặt lịch vào ba mẫu này.

## 7. Kiến trúc duy nhất nên dùng ở Laris lúc này

Đây là cấu hình production đã đối chiếu trực tiếp trên Smax, Facebook và n8n đến ngày 26/07/2026. Không trộn với phương án Trigger GenAI ở Mục 12.

```text
Tin khách tự nhập
  → Messenger Default
  → block AI_NHAN_TIN
  → Sequence AI_GOM_TIN chờ 15 giây tính từ tin cuối
  → block AI_TRA_LOI
  → chụp ai_pending_text sang ai_processing_text và xóa ngay hàng đợi
  → GenAI trích xuất trạng thái
  → Parse Content cập nhật bộ nhớ CRM bền vững
  → GenAI soạn câu trả lời
  → Delay đồng bộ 0,5 phút
  → Go to Block AI_JSON_GUI
  → GenAI AI Tạo Json chuyển câu trả lời thành cards
  → Delay đồng bộ 0,3 phút
  → Go to Block AI_GUI_TRA_LOI
  → Messenger Typing
  → JsonAPI gửi text/cards sang n8n
  → n8n ưu tiên text/answer mới, chuẩn hóa cards rồi gọi Smax Partner API
  → JsonAPI đồng bộ CREATE/UPDATE/ADD/CANCEL vào database lịch
  → xóa ai_processing_text và ai_answer
```

Giữ/tắt như sau:

| Thành phần | Trạng thái | Nối tới đâu |
|---|---|---|
| `Default Reply` | BẬT | `AI_NHAN_TIN`, không gọi Gen AI trực tiếp |
| `Trigger GenAI` | TẮT | Chưa dùng ở cấu hình chính |
| `Click-to-Messenger Ads` | BẬT | `ADS_ENTRY`, tuyệt đối không nối `Default Reply` |
| `Chatbot AI - Tư Vấn Size` | BẬT | Đọc **Tin của Page**; chứa đúng `size tóc hiện tại`; gửi một ảnh size sau câu tư vấn AI |
| `Chatbot AI - Bảng giá` | BẬT | Đọc **Tin của Page**; chứa đúng `bảng giá dịch vụ`; gửi một ảnh bảng giá sau câu tư vấn AI |
| Ba Keyword cũ về đặt lịch, ưu đãi, giá cắt | TẮT | Để Prompt + Knowledge xử lý theo ngữ cảnh |

Lý do: tài liệu Smax xác nhận Messenger Default phản hồi mọi nội dung không khớp Trigger cụ thể và có thể spam khi khách gửi nhiều tin ngắn. Vì vậy Default chỉ làm cổng nhận tin; không đặt thẻ Gen AI ngay trong Default như cấu hình hiện tại.

## 8. Sửa `Default Reply`

Hiện tại block `Default Reply` có ba thẻ `GenAI → Messenger Typing → Messenger Text`. Đây là nguyên nhân mỗi tin ngắn tạo một lượt AI riêng.

Sửa Trigger `Default Reply` để chỉ có:

```text
Go to block → AI_NHAN_TIN
```

Không để Gen AI, Messenger Text hoặc Messenger Typing trực tiếp trong block gắn với Default Reply.

## 9. Tạo block `AI_NHAN_TIN`

Tạo các Attributes kiểu Văn bản:

- `ai_pending_text`
- `ai_processing_text`
- `ai_answer`
- `ai_state_result`: kết quả trung gian của bộ trích xuất, không gửi cho khách.
- `laris_hair_size`: bộ nhớ CRM bền vững của chiều dài toàn bộ mái tóc, chỉ nhận `S`, `M`, `L` hoặc `UNKNOWN`; không dùng làm size Duỗi chân tóc.
- `laris_dye_package`: bộ nhớ CRM bền vững, chỉ nhận `BASIC`, `VIP`, `CAO_CAP` hoặc `UNKNOWN`.
- `laris_booking_date`: ngày của lịch đang gom, chuẩn `YYYY-MM-DD` hoặc `UNKNOWN`.
- `laris_booking_time`: giờ của lịch đang gom, chuẩn `HH:mm` hoặc `UNKNOWN`.
- `laris_customer_name`: tên khách đã cung cấp cho lịch hoặc `UNKNOWN`.
- `laris_customer_phone`: SĐT khách đã cung cấp cho lịch hoặc `UNKNOWN`.
- `laris_booking_services`: danh sách mã dịch vụ thuộc cùng một lịch, phân tách bằng dấu phẩy hoặc `UNKNOWN`.
- `laris_booking_status`: `DRAFT`, `PENDING`, `CONFIRMED` hoặc `CANCELLED`.
- `laris_booking_action`: `NONE`, `CREATE`, `UPDATE`, `CANCEL`, `ADD_TO_EXISTING`, `CREATE_SEPARATE` hoặc `ASK_ADD_OR_NEW`.

Ba thuộc tính `ai_pending_text`, `ai_processing_text`, `ai_answer` là bộ đệm. `ai_pending_text` được xóa ngay ở đầu `AI_TRA_LOI` sau khi chụp sang `ai_processing_text`; `ai_processing_text` và `ai_answer` chỉ được xóa ở cuối `AI_GUI_TRA_LOI`. Chín thuộc tính bắt đầu bằng `laris_` **không được reset ở các block gửi**: chúng tồn tại theo hồ sơ Facebook của khách cho đến khi lời khách làm thay đổi trạng thái. `ai_state_result` là chuỗi trung gian được ghi đè ở mỗi lượt.

`ai_last_batch_key` và MD5 chỉ là lớp chống trùng nâng cao; chưa tạo ở lần cấu hình đầu.

Trong block `AI_NHAN_TIN`, xếp thẻ đúng thứ tự:

1. `Set Attributes`: đặt `ai_pending_text` bằng hai dòng sau:

   ```text
   {{ai_pending_text}}
   {{last_content_by_user}}
   ```

2. `Sequence`: chọn `REMOVE` khách khỏi `AI_GOM_TIN`.
3. `Sequence`: chọn `ADD → NOW` vào lại `AI_GOM_TIN`.
4. Kết thúc block; không có Gen AI và không gửi Messenger Text.

Tài liệu Smax xác nhận Set Attributes nhận giá trị động, Sequence có `REMOVE`, `ADD NOW`, và muốn khởi động lại thời gian thì bắt buộc `REMOVE → ADD`. Cách ghép hai Attributes thành nhiều dòng cần test một lần trên tài khoản thật; nếu giao diện không giữ được giá trị cũ, không dùng Delay rời cho từng tin mà liên hệ Smax để bật buffer/append.

## 10. Tạo Sequence `AI_GOM_TIN`

Trong `Kịch bản chăm sóc`, tạo Sequence:

- Tên: `AI_GOM_TIN`.
- Page: Laris Hair Studio.
- Step đầu: chờ `15 Seconds`.
- Block được gọi sau 15 giây: `AI_TRA_LOI`.
- Bật Active và Save.

Mỗi tin mới chạy lại `REMOVE → ADD NOW`, nên mốc 15 giây được tính lại từ tin cuối. Mốc 10 giây đã cho phản hồi tách đôi trong bài test thực tế khi khách gõ câu bổ sung chậm hơn vài giây. Không bật `Run trigger theo khoảng thời gian` để xử lý lỗi này; theo tài liệu Smax, tùy chọn đó là khung giờ trigger được phép chạy, không phải cơ chế gom tin.

## 11. Cấu hình chuẩn block `AI_TRA_LOI`

Cấu hình đã được thiết lập và kiểm tra trực tiếp đến ngày 26/07/2026. Luồng được tách thành ba block xử lý/gửi để tránh Smax dùng `ai_answer` cũ và để `AI Tạo Json` đảm nhiệm riêng việc đóng gói nội dung.

### Block `AI_TRA_LOI` — xử lý, không gửi Messenger

Các thẻ đang dùng theo đúng thứ tự:

1. `Set Attributes` số 1:
   - `ai_processing_text = {{ai_pending_text}}`.
   - `ai_pending_text = __EMPTY__`.
   - Hai dòng phải nằm trong cùng một thẻ. Đây là thao tác snapshot-and-clear: tin đang xử lý được giữ cố định, còn tin khách gửi thêm trong lúc AI chạy sẽ vào hàng đợi mới thay vì bị mất hoặc nhập lại batch cũ.
2. `Gen AI — trích xuất trạng thái` — BẬT:
   - Phiên bản: `AI Trạng Thái Lịch`.
   - Ánh xạ `ai_state_result` ← JSON Path `answer`.
   - Dùng Prompt trích xuất trạng thái ở bên dưới.
3. `Parse Content` — BẬT:
   - Nguồn: `ai_state_result`.
   - Mẫu: `SIZE={{laris_hair_size}}|PACKAGE={{laris_dye_package}}|BOOKING_DATE={{laris_booking_date}}|BOOKING_TIME={{laris_booking_time}}|CUSTOMER_NAME={{laris_customer_name}}|CUSTOMER_PHONE={{laris_customer_phone}}|SERVICES={{laris_booking_services}}|STATUS={{laris_booking_status}}|ACTION={{laris_booking_action}}`.
4. `Set Attributes` số 2:
   - Attribute: `ai_pending_text`.
   - Giá trị: `__EMPTY__`. Có thể giữ thẻ này BẬT như lớp dọn dự phòng; việc snapshot-and-clear chính vẫn nằm ở thẻ số 1.
5. `Messenger Typing` cũ — TẮT.
6. `Gen AI — soạn câu trả lời` — BẬT:
   - Phiên bản: `Bot AI`.
   - Prompt: bắt buộc truyền tin hiện tại, batch, `ai_state_result` và toàn bộ chín thuộc tính bền vững; dùng Prompt trả lời ở bên dưới.
   - Ánh xạ kết quả: `ai_answer`.
   - JSON Path: `answer`.
7. `Gen AI — thẻ trả lời cũ` — TẮT.
8. `Messenger Text` cũ — TẮT.
9. `Delay` — BẬT, đặt `0,5 Phút`.
10. `Go to Block` — BẬT, chọn `AI_JSON_GUI`.
11. `Set Attributes` cuối cũ — TẮT.

Sau mỗi lần sửa Prompt ngay trong Card GenAI, phải bấm nút **Xác nhận** của chính Card đó rồi mới đóng block. Nếu khi đóng xuất hiện cảnh báo “Một số card thay đổi nhưng chưa lưu” thì thay đổi chưa có hiệu lực production. Prompt toàn cục của phiên bản GenAI và Prompt nhúng trong Card là hai nơi riêng; phải đồng bộ cả hai.

### Block `AI_JSON_GUI` — tối ưu cấu trúc trả lời

Thứ tự đang dùng:

1. `GenAI — AI Tạo Json` — BẬT:
   - Đầu vào ưu tiên `ai_answer` mới.
   - Chỉ đóng gói nội dung thành JSON/cards; không tự đổi giá, size, gói, ngày, giờ, dịch vụ hay trạng thái lịch.
   - Kết quả phải giữ nguyên đầy đủ nội dung tư vấn và CTA hợp lệ.
2. `Delay` — BẬT, đặt `0,3 Phút`.
3. `Go to Block` — BẬT, chọn `AI_GUI_TRA_LOI`.

Không đặt thêm một `AI Tạo Json` đang bật ở `AI_GUI_TRA_LOI`; một lượt chỉ được có một đường đóng gói.

### Block `AI_GUI_TRA_LOI` — gửi kết quả mới qua n8n

Thứ tự các thẻ đang dùng:

1. `Messenger Typing`: 1,5–2 giây — BẬT.
2. `Messenger Text {{ai_answer}}` cũ — TẮT để không tạo đường gửi thứ hai.
3. `JsonAPI — tối ưu nội dung GenAI` — BẬT:
   - Phương thức `POST`.
   - URL production webhook chỉ lưu trong Smax; không chép UUID webhook vào Knowledge hoặc tài liệu công khai.
   - Header `Content-Type = application/json`.
   - Body JSON:

     ```json
     {
       "customer_pid": "{{pid}}",
       "page_pid": "{{page_pid}}",
        "text": "{{ai_answer}}",
        "answer": "{{ai_answer}}",
        "cards": "{{cards}}"
     }
     ```

4. `Set Attributes` cuối — BẬT:
   - `ai_processing_text = __EMPTY__`.
   - `ai_answer = __EMPTY__`.
   - Không xóa `ai_pending_text`, `ai_state_result` hoặc bất kỳ thuộc tính `laris_*` nào.
5. `JsonAPI — lịch hẹn` — BẬT và gửi thêm `booking_status` cùng `booking_action`.

Thứ tự bắt buộc là `JsonAPI tối ưu nội dung → Set Attributes dọn biến → JsonAPI lịch hẹn`. Nếu đặt thẻ dọn biến trước webhook tối ưu thì `ai_answer` trở thành `__EMPTY__` và khách không nhận được lời tư vấn. n8n formatter phải ưu tiên `body.text`/`body.answer` mới hơn `body.cards` để không gửi lại cards cũ.

Các khoảng chờ `0,5 phút` sau Bot AI và `0,3 phút` sau AI Tạo Json là hàng rào đồng bộ cho ánh xạ GenAI/JSON Path bất đồng bộ của Smax. n8n chỉ kiểm tra, trình bày, gửi và upsert lịch; không tự tư vấn lại, không thay đổi giá, size, gói hoặc ý khách.

Prompt trong thẻ `Gen AI — trích xuất trạng thái`:

```text
NHIỆM VỤ DUY NHẤT: cập nhật bộ nhớ CRM từ lời KHÁCH, không tư vấn và không giải thích.
CURRENT_MESSAGE={{last_content_by_user}}
CURRENT_BATCH={{ai_processing_text}}
CURRENT_SIZE={{laris_hair_size}}
CURRENT_PACKAGE={{laris_dye_package}}
CURRENT_BOOKING_DATE={{laris_booking_date}}
CURRENT_BOOKING_TIME={{laris_booking_time}}
CURRENT_CUSTOMER_NAME={{laris_customer_name}}
CURRENT_CUSTOMER_PHONE={{laris_customer_phone}}
CURRENT_SERVICES={{laris_booking_services}}

Chỉ lời khách xác nhận/chỉnh trong CURRENT_MESSAGE hoặc CURRENT_BATCH mới được đổi trạng thái; tuyệt đối không lấy giá trị từ lời bot từng liệt kê. Hiểu lỗi gõ: siz/size/sze; nhộm/nhuộm. Size hợp lệ chỉ S, M, L. Gói hợp lệ chỉ BASIC, VIP, CAO_CAP. Một chữ S/M/L đứng riêng được xem là xác nhận size khi ngữ cảnh trước đó bot vừa hỏi size toàn bộ tóc. Khi ngữ cảnh hiện tại là Duỗi chân tóc, size S/M được suy ra hoặc khách nói cho **vùng cần xử lý** chỉ là dữ liệu cục bộ của dịch vụ: giữ nguyên `CURRENT_SIZE`, không ghi S/M vào SIZE và không đổi size toàn bộ đang lưu. Chỉ lưu gói khi khách thật sự chọn hoặc hỏi đích danh gói đó; không tự chọn Basic. Chỉ thêm dịch vụ vào SERVICES khi khách xác nhận muốn làm/đặt/thêm, ví dụ `thêm gội`, `ok gội`, `làm luôn`, `đặt luôn`. Tin chỉ hỏi có dịch vụ không, hỏi giá hoặc hỏi thông tin không phải xác nhận và không được làm thay đổi SERVICES. Nếu tin mới không đổi một trường, giữ nguyên giá trị hiện có; nếu giá trị hiện có trống hoặc __EMPTY__ thì dùng UNKNOWN. Nếu khách đang hỏi giúp người khác, không ghi đè dữ liệu của chính khách.

Ngày lịch dùng `YYYY-MM-DD`, giờ dùng `HH:mm`. Trong ngữ cảnh salon 9h–20h, `2h` không ghi sáng/tối là `14:00`; `1h`–`8h` lần lượt là `13:00`–`20:00`. Chỉ thay ngày/giờ/tên/SĐT khi chính khách sửa, đổi lịch, đặt cho người khác hoặc tạo lịch riêng. Khi khách cung cấp ngày/giờ ngay sau khi hỏi một dịch vụ, có thể xem đó là xác nhận đặt dịch vụ gần nhất. SERVICES dùng các mã `CAT_MAI,CAT_NU,GOI,NHUOM,UON,DUOI,DUOI_HOI_NUOC,DUOI_CHAN_TOC,XU_LY_DUOI_CHAN_KEM_UON,PHUC_HOI,TAY,NANG_SANG,BOC_MAU,TONE_SAU_TAY,KHAC` và phân tách bằng dấu phẩy. Ánh xạ bắt buộc: `DUOI` = Duỗi; `DUOI_HOI_NUOC` = Duỗi hơi nước; `DUOI_CHAN_TOC` = Duỗi chân tóc; `XU_LY_DUOI_CHAN_KEM_UON` = Xử lý Duỗi chân tóc đi kèm Uốn. Không gộp các biến thể khi khách yêu cầu đúng biến thể; riêng khi khách xác nhận vùng cần duỗi dài qua ngực, phân loại là Duỗi nguyên đầu và lưu `DUOI`, không lưu `DUOI_CHAN_TOC`. Chỉ lưu `XU_LY_DUOI_CHAN_KEM_UON` khi khách đang làm Uốn, chân tóc bị phồng và đã xác nhận cần bước xử lý sau đánh giá của stylist.

CHỈ xuất đúng một dòng, không thêm chữ nào khác:
SIZE=<S|M|L|UNKNOWN>|PACKAGE=<BASIC|VIP|CAO_CAP|UNKNOWN>|BOOKING_DATE=<YYYY-MM-DD|UNKNOWN>|BOOKING_TIME=<HH:mm|UNKNOWN>|CUSTOMER_NAME=<tên|UNKNOWN>|CUSTOMER_PHONE=<số|UNKNOWN>|SERVICES=<danh_sách_mã|UNKNOWN>
```

Prompt trong thẻ `Gen AI — soạn câu trả lời mới`:

```text
CURRENT_MESSAGE={{last_content_by_user}}
CURRENT_BATCH={{ai_processing_text}}
PERSISTENT_HAIR_SIZE={{laris_hair_size}}
PERSISTENT_DYE_PACKAGE={{laris_dye_package}}
PERSISTENT_BOOKING_DATE={{laris_booking_date}}
PERSISTENT_BOOKING_TIME={{laris_booking_time}}
PERSISTENT_CUSTOMER_NAME={{laris_customer_name}}
PERSISTENT_CUSTOMER_PHONE={{laris_customer_phone}}
PERSISTENT_BOOKING_SERVICES={{laris_booking_services}}

Quy tắc bắt buộc:
1. Xóa thầm __EMPTY__, quảng cáo và metadata. CURRENT_MESSAGE là mốc bắt buộc; batch trống/lỗi/thiếu tin mới nhất thì dùng CURRENT_MESSAGE. Batch có nhiều tin thì trả lời đủ các ý đúng một lần.
2. PERSISTENT_HAIR_SIZE là size toàn bộ mái tóc và PERSISTENT_DYE_PACKAGE là gói khách đã xác nhận. Nếu hợp lệ thì phải dùng đúng phạm vi và CẤM hỏi lại. Ngoại lệ: Duỗi chân tóc bỏ qua PERSISTENT_HAIR_SIZE khi báo giá; size S/M của vùng xử lý không được ghi đè trường này. Tin hiện tại sửa giá trị toàn bộ tóc thì tin hiện tại ưu tiên. UNKNOWN/trống không hợp lệ.
3. Chỉ lời khách tạo lựa chọn; không lấy lời bot từng liệt kê làm xác nhận. Không tự chọn Basic/VIP/cao cấp. Thiếu gói khi cần tính tổng thì hỏi gói nhưng không hỏi lại size đã lưu.
4. Nếu khách hỏi giúp người khác, không áp hoặc ghi đè size/gói bền vững của chính khách cho người đó; hỏi riêng dữ liệu còn thiếu.
5. Hiểu lỗi gõ/viết tắt. “nhộm vip siz l bn e, đc giảm ko” = nhuộm VIP size L hỏi giá/ưu đãi; báo đúng VIP size L, không hỏi lại và không lôi giá cắt.
6. Chuẩn hóa hôm nay/mai/mốt/tên thứ theo Asia/Ho_Chi_Minh và nói ngày D/M (thứ); không chép nguyên câu khách. “Mai cắt” là ý định đặt lịch.
7. Trước khi tính tổng phải đủ gói/size/biến thể. Khi K03 ghi `ĐANG ÁP DỤNG LIÊN TỤC`, mọi phản hồi có báo giá dịch vụ đủ điều kiện phải nêu rõ giá gốc, giảm 15% và giá cuối; chưa biết size/gói nhưng có khoảng giá thì nêu cả khoảng gốc và khoảng cuối. Tính ưu đãi từng dòng trước khi cộng, không giảm hai lần. Không dùng ngày/tháng để tự làm chương trình hết hạn. Khi khách hỏi thời hạn, lấy tháng từ `TODAY_VN`; nếu thiếu/không hợp lệ thì không đoán tháng.
8. AI KHÔNG tư vấn chọn màu, màu hợp da/khuôn mặt, khả năng lên màu, công thức hay có cần tẩy. “Tư vấn nhuộm tóc” = tư vấn gói Basic/VIP/cao cấp, dòng thuốc, giá và size; hỏi khách quan tâm GÓI NÀO, tuyệt đối không hỏi màu nào. Nếu khách hỏi màu hợp/gửi ảnh/hỏi lên màu được không, nói stylist cần xem trực tiếp và mời tới salon.
9. Size tóc dùng chung cho nhuộm, Duỗi nguyên đầu, Duỗi hơi nước nguyên đầu, uốn, phục hồi, Karatin, hấp dầu, tẩy, nâng sáng, bóc màu, tone sau tẩy và nhuộm sáng tạo; đã có size thì cấm hỏi lại ở mọi dịch vụ này. Riêng Duỗi chân tóc là ngoại lệ tuyệt đối: bỏ qua `PERSISTENT_HAIR_SIZE`; ưu tiên phạm vi trong CURRENT_MESSAGE/CURRENT_BATCH, sau đó đến ngữ cảnh đang trực tiếp xác định vùng cần xử lý, nếu chưa có thì mặc định size S. Khoảng 5–15cm/chưa tới vai = size S, giá gốc 900k, sau giảm 15% còn 765k; qua vai một chút = size M, giá gốc 1tr, sau giảm còn 850k. Phải giải thích căn cứ và không hỏi S/M/L. Không có Duỗi chân tóc size L; vùng cần duỗi qua ngực chuyển thành Duỗi nguyên đầu size L, giá gốc 1tr100k, sau giảm còn 935k. Nếu khách chỉ nói tóc tổng thể qua ngực nhưng muốn duỗi chân, hỏi đúng một câu: “Dạ mình muốn duỗi phần chân mới mọc khoảng 5–15cm hay muốn duỗi cả phần tóc dài qua ngực ạ?” Không ghi đè `PERSISTENT_HAIR_SIZE`. Mức 400k–700k chỉ là giá gốc của xử lý Duỗi chân tóc đi kèm Uốn khi chân bị phồng, không phải giá Duỗi chân tóc độc lập; nếu báo mức này phải nêu sau giảm còn 340k–595k.
10. Ngày, giờ, tên, SĐT và danh sách dịch vụ là trạng thái của một lịch đang gom. Chỉ khi khách xác nhận muốn làm/đặt/thêm dịch vụ mới thì thêm dịch vụ đó vào cùng lịch và dùng chung dữ liệu đã có. Nếu khách chỉ hỏi có dịch vụ không hoặc hỏi giá, chỉ tư vấn và hỏi có muốn thêm vào lịch; không nói đã ghi nhận. Không hỏi lại trường hợp lệ. Chỉ đổi khi khách nói rõ đổi/sửa, đặt cho người khác hoặc tạo lịch riêng. Hỏi trường lịch đầu tiên còn thiếu theo thứ tự ngày → giờ → tên và SĐT.
11. Phân loại cắt: `cắt mái`, `cắt tóc mái`, `tỉa/chỉnh mái`, `cắt mái xéo/bay` chỉ báo cắt mái riêng cố định 50k; không áp 15%, không báo 42.500đ và không báo cắt nữ 200k/150k. Chỉ câu chung `cắt tóc`, `cắt nữ`, `cắt layer/form/kiểu` mới dùng mẫu cắt nữ: giá gốc 200k, ưu đãi riêng còn 150k; không báo 170k và không giảm tiếp trên 150k.
12. Nếu khách hỏi `tổng bao nhiêu`, `tổng tiền`, `hết bao nhiêu` hoặc tương tự: chỉ liệt kê ngắn từng dịch vụ được hỏi và tổng tiền; cấm thêm CTA, lời mời đặt lịch, tóm tắt lịch, tên, SĐT, ngày hoặc giờ. Nếu thiếu gói/size/biến thể bắt buộc thì chỉ nói phần đã tính được và hỏi đúng dữ liệu còn thiếu.
13. Với phản hồi tư vấn/báo giá không phải câu hỏi tổng: kết thúc bằng đúng một bước tiếp theo; thiếu gói/biến thể/size thì chỉ hỏi phần thiếu; đủ giá thì hỏi có muốn thêm dịch vụ vào lịch hoặc hỏi trường lịch đầu tiên còn thiếu. Khi đủ Tên, SĐT, Dịch vụ, Ngày và Giờ, phải chốt ngay bằng bản xác nhận đầy đủ; cấm “ghi nhận”, “sẽ kiểm tra” và “xác nhận lại”. Bản xác nhận đủ dữ liệu được phép kết thúc ngay sau dòng Dịch vụ; không bắt buộc CTA, không tự hỏi khách cần tư vấn thêm dịch vụ gì, không mở chủ đề mới. Thêm dịch vụ, đổi lịch và hủy lịch ưu tiên kết thúc ngay sau thông tin đã xác nhận. Chỉ hỏi tiếp khi còn thiếu dữ liệu hoặc thật sự cần khách trả lời; mỗi xác nhận tối đa một câu kết ngắn phù hợp và không lặp máy móc.
14. Lịch quá khứ hoặc `CANCELLED` là lịch đã đóng, không được tái sử dụng ngày/giờ/dịch vụ. Khách đặt lại sau hủy là `CREATE`. Nếu có lịch tương lai và khách xác nhận thêm dịch vụ nhưng chưa nói dùng lịch nào, đặt `ACTION=ASK_ADD_OR_NEW`, hỏi “thêm vào lịch ... hay tạo lịch mới” và chưa upsert. Chỉ `ADD_TO_EXISTING` khi khách chọn lịch cũ; chỉ `CREATE_SEPARATE` khi khách chọn lịch mới sau câu hỏi hai lựa chọn.
15. Khi chốt lịch, tên thứ phải được tính lại từ ngày ISO theo `Asia/Ho_Chi_Minh`; không sao chép tên thứ từ lịch cũ hay từ lời khách.
16. Không lặp lại giá/danh sách/địa chỉ/ưu đãi cũ nếu tin mới không yêu cầu; không dùng cùng CTA ở hai lượt liên tiếp. Giữ nguyên tên biến thể dịch vụ từ lúc nhận diện đến báo giá, tính tổng, lưu trạng thái và xác nhận lịch: `Duỗi` không được đổi thành `Duỗi hơi nước`, còn `Duỗi hơi nước` không được rút gọn thành `Duỗi`. Trả lời tự nhiên như nhân viên salon, ngắn gọn, đúng trọng tâm.
```

Sau khi đổi từ giá trị trống sang `__EMPTY__`, phải chạy một lần block reset cho tài khoản test cũ hoặc test bằng tài khoản mới; nếu không, dữ liệu tích lũy từ lần cấu hình trước vẫn có thể còn trong Attributes.

### Block reset dùng một lần cho tài khoản test cũ

Tạo block quản trị `AI_RESET_CONTEXT`, gồm đúng một thẻ `Set Attributes`:

- `ai_pending_text = __EMPTY__`
- `ai_processing_text = __EMPTY__`
- `ai_answer = __EMPTY__`
- `ai_state_result = __EMPTY__` (không bắt buộc, vì lượt sau sẽ ghi đè)

Chỉ gửi block này thủ công cho tài khoản test sau khi đã dừng Sequence `AI_GOM_TIN` của tài khoản đó. Muốn xóa cả trí nhớ CRM để test từ đầu, thêm `laris_hair_size = __EMPTY__`, `laris_dye_package = __EMPTY__`, `laris_booking_date = __EMPTY__`, `laris_booking_time = __EMPTY__`, `laris_customer_name = __EMPTY__`, `laris_customer_phone = __EMPTY__`, `laris_booking_services = __EMPTY__`, `laris_booking_status = __EMPTY__`, `laris_booking_action = __EMPTY__`. Bình thường tuyệt đối không reset các trường này; block reset chỉ dùng thủ công cho tài khoản test.

Trạng thái block production dùng `__EMPTY__` cho 13 mục: ba bộ đệm, `ai_state_result` và chín thuộc tính `laris_*`. Prompt coi `__EMPTY__` và `UNKNOWN` đều là chưa biết; dùng đồng nhất `__EMPTY__` giúp nhìn log/reset dễ hơn. Sau khi gửi block phải tải lại hồ sơ rồi kiểm tra tab `Attributes → Custom attributes`; không kết luận chỉ dựa vào thông báo `Đã gửi thành công` vì bảng Attributes đang mở có thể chưa tự làm mới.

Sau khi ba block `AI_TRA_LOI → AI_JSON_GUI → AI_GUI_TRA_LOI` chạy đúng mới cân nhắc lớp chống trùng nâng cao bằng `ai_last_batch_key`, MD5 và Bộ lọc nâng cao trên từng card. Sequence `REMOVE → ADD NOW`, snapshot-and-clear và block gửi tách riêng hiện là ba lớp chống lặp chính.

## 12. Trigger GenAI và Intents — chỉ dùng ở giai đoạn sau

Tài khoản hiện có ba Intent: `dat_lich_moi`, `doi_huy_lich`, `can_nhan_vien_ho_tro`. Trigger GenAI hiện đang tắt; giữ nguyên trong lúc ổn định luồng gom tin.

Khi cần cho ba Intent chạy block tự động, chuyển sang kiến trúc thay thế:

- TẮT `Default Reply`.
- BẬT `Trigger GenAI`.
- Ba Intent nghiệp vụ: chọn `Gửi block` tương ứng, không chọn `Trả lời trực tiếp`.
- `Other`: chọn `Gửi block → AI_NHAN_TIN`.
- Không để bất kỳ Intent nào gọi thêm một thẻ Gen AI khác sau khi đã gửi block trả lời.

Nguyên tắc tuyệt đối: hoặc Messenger Default là bộ định tuyến tổng quát, hoặc Trigger GenAI + Other là bộ định tuyến tổng quát. Không bật cả hai để cùng trả lời tin tự do.

## 13. Sửa `Click-to-Messenger Ads`

Trạng thái đã kiểm tra trực tiếp ngày 19/07/2026: `Click-to-Messenger Ads` đang trỏ `ADS_ENTRY`, không trỏ `Default Reply` hoặc `AI_NHAN_TIN`.

Block `ADS_ENTRY` là block chặn sự kiện quảng cáo:

1. Không có thẻ Gen AI và không gọi `AI_NHAN_TIN`.
2. Không dùng `last_content_by_user`, không gửi lại content quảng cáo.
3. Không gửi Messenger Text khi khách mới chỉ bấm quảng cáo; thẻ Messenger Text cũ trong block đã được tắt. Chỉ tin thật khách tự gõ sau đó mới được Messenger Default tiếp nhận.

Trong Trigger `Click-to-Messenger Ads`, mục `Sau đó trả lời bằng` chọn `ADS_ENTRY`, không chọn `Default Reply` hoặc `AI_NHAN_TIN`. Tin khách tự nhập tiếp theo sẽ đi vào Messenger Default và luồng gom tin.

## 14. Lịch sử và hai Trigger ảnh một chiều

Cấu hình đã kiểm tra trực tiếp trong `Bot AI`:

- `Lịch sử tin nhắn`: đang chọn `Messages`.
- Số lượng: `20 Messages`.

Giữ nguyên 20 Messages. Đây là cấu hình đúng; không cần chuyển sang Conversation Sessions lúc này vì có thể kéo thêm ngữ cảnh cũ không liên quan.

Giữ BẬT hai trigger ảnh và bắt buộc nguồn điều kiện là **Tin của Page** (`Messages from fanpage`). Hai trigger này không trả lời thay AI; chúng chỉ đính kèm ảnh sau khi AI đã gửi một câu tự nhiên có cụm điều khiển:

- `Chatbot AI - Tư Vấn Size`: toán tử `Chứa nội dung`; chỉ một từ khóa `size tóc hiện tại`; dẫn tới block chỉ có ảnh hướng dẫn size.
- `Chatbot AI - Bảng giá`: toán tử `Chứa nội dung`; chỉ một từ khóa `bảng giá dịch vụ`; dẫn tới block chỉ có ảnh bảng giá.

`Prompt Chính.Md` và K04 phải buộc AI dùng đúng cụm `size tóc hiện tại` khi size chưa biết, và đúng cụm `bảng giá dịch vụ` khi khách xin bảng giá tổng. Nhờ vậy khách luôn nhận lời tư vấn trước rồi mới nhận ảnh. Khi size đã biết, AI bị cấm hỏi lại và không được dùng cụm kích hoạt size, nên ảnh không gửi lặp.

Không thêm từ khóa rộng hoặc biến thể như `size`, `size tóc`, `giá`, `bảng giá`, `nhuộm`, vì chúng có thể kích hoạt ảnh ngoài ý muốn hoặc gửi lặp. Block ảnh không chứa GenAI/Messenger Text để không tạo vòng lặp.

### Kết luận audit log ngày 19/07/2026

Cuộc test `Bao Le` cho thấy:

- Ba tin `Tư vấn chị uốn tóc` + `Duỗi bên em giá sao?` + `Có những gói phục hồi nào?` đã đi qua ba lượt `AI_NHAN_TIN` nhưng chỉ một lượt `AI_TRA_LOI`. Cơ chế `REMOVE → ADD NOW → chờ 15 giây` đang gom tin đúng.
- Tin `nhộm vip siz l bn e, đc giảm ko` cũng chỉ tạo một lượt GenAI; vì vậy lỗi hỏi lại size không phải do hai GenAI trả lời cùng lúc.
- Nguyên nhân cũ là Card GenAI chỉ nhận `{{ai_processing_text}}` và chưa có bộ nhớ CRM bền vững. Cấu hình mới đã thêm `CURRENT_MESSAGE`, bộ trích xuất trạng thái, Parse Content và chín thuộc tính bền vững cho size, gói, lịch và hành động đang gom.
- Bộ trích xuất đã test trực tiếp với `nhộm vip siz l bn e, đc giảm ko` và trả đúng `SIZE=L|PACKAGE=VIP`.
- Theo yêu cầu vận hành ngày 19/07/2026, hai trigger ảnh dùng **Messages from fanpage** với đúng một cụm điều khiển hẹp: `size tóc hiện tại` hoặc `bảng giá dịch vụ`. Câu AI được gửi trước; trigger chỉ gửi ảnh và không có GenAI/Messenger Text nên không tạo vòng lặp.
- Bài test thật `nhuộm vip size L giá bao nhiêu, có giảm 15% không em?` đã trả đúng gói VIP size L: giá gốc 1tr100k, giảm 15% còn 935k, không hỏi lại size.
- Hai tin `Nhuộm` và `Có loại nào` cách nhau khoảng 5 giây chỉ tạo một câu trả lời tổng hợp; không có câu thứ hai bị lặp sau thời gian chờ kiểm tra.
- Các yêu cầu size/bảng giá đi qua Default Reply và AI trước; sau đó đúng một trigger ảnh chạy theo cụm điều khiển trong câu Page.
- Các ca cắt tóc/cắt mái, ngày 25/7 (thứ bảy), lịch 18h, tên + SĐT, thêm gội, tổng tiền và từ chối tư vấn màu đã được test qua tài khoản Phương Bùi và cho kết quả đúng theo Knowledge.
- Ca `Uốn bên em giá bao nhiêu?` dùng ngay size L đã nhớ, báo uốn C/uốn xoăn với giá gốc và giá sau giảm 15%, không hỏi lại size và không tự ghi nhận uốn. Ca kiểm tra chéo `Tính lại giúp chị tổng cắt tóc và gội bao nhiêu?` chỉ cộng cắt + gội thành `235k–277.500đ`, không cộng uốn, không kèm CTA hoặc tóm tắt lịch.

Không tạo thêm luồng Default/GenAI song song. `AI Trạng Thái Lịch` và `Bot AI` nằm tuần tự trong `AI_TRA_LOI`; `AI Tạo Json` nằm riêng trong `AI_JSON_GUI`; thao tác gửi nằm trong `AI_GUI_TRA_LOI`. Đây là chia việc theo chức năng, không phải ba đường trả lời song song.

## 15. Kiểm tra bằng Logs

Sau khi cấu hình, test trên Messenger bằng tài khoản không phải admin:

1. `Nhuộm`, sau 5–10 giây gửi `Có loại nào`.
2. Logs phải có nhiều lượt `AI_NHAN_TIN` nhưng chỉ một lượt `AI_TRA_LOI`, một lượt `AI_JSON_GUI`, tiếp theo đúng một lượt `AI_GUI_TRA_LOI`; n8n có đúng một execution gửi thành công và Messenger chỉ nhận một chuỗi trả lời.
3. Gửi `nhộm vip siz l bn e, đc giảm ko`; bot phải báo đúng VIP size L: giá gốc 1tr100k, giảm 15% còn 935k; không hỏi lại size/gói, không lôi giá cắt và không gửi ảnh tự động.
4. Bấm quảng cáo nhưng chưa nhập câu hỏi; Logs không được có Card Gen AI.
5. Sau quảng cáo, gửi `Cho mình xin giá cắt tóc`; AI chỉ trả lời câu khách, không trả lời content quảng cáo.
6. Sau khi khách đã xác nhận `Size L`, gửi một yêu cầu dịch vụ tính theo chiều dài toàn bộ tóc ở lượt sau; bot phải dùng L và không hỏi lại. Kiểm tra hồ sơ khách thấy `laris_hair_size = L`. Sau đó hỏi chung giá Duỗi chân tóc: bot phải bỏ qua L để báo nhánh S 900k → 765k, đồng thời hồ sơ vẫn giữ `laris_hair_size = L`.
7. Gửi `cho chị gói VIP`, rồi ở lượt sau hỏi `tính tổng`; bot phải dùng VIP và không tự chuyển Basic. Kiểm tra `laris_dye_package = VIP`.
8. Khi size chưa biết, gửi `Giá dịch vụ uốn ạ`; AI phải trả lời tự nhiên, hỏi bằng đúng cụm `size tóc hiện tại`, sau đó nhận đúng một ảnh size. Khi size đã lưu, hỏi dịch vụ theo size lần nữa phải không hỏi lại và không gửi lại ảnh.
9. Gửi `Cho mình xin bảng giá`; AI phải trả lời có đúng cụm `bảng giá dịch vụ`, sau đó nhận đúng một ảnh bảng giá. Không được chỉ gửi ảnh trống lời.
10. Gửi `cắt tóc mái`; bot chỉ báo cắt mái riêng 50k, không được nói cắt nữ 200k/150k.
11. Gửi `cắt tóc`; bot báo cắt nữ 200k đã gồm cắt/chỉnh mái, cắt mái riêng 50k và ưu đãi cắt nữ còn 150k.
12. Trong ngữ cảnh đặt lịch, gửi `2h` rồi ở lượt sau hỏi giá gội; bot phải dùng lại 14h và tuyệt đối không hỏi lại giờ. Chỉ thêm `GOI` vào lịch sau khi khách xác nhận muốn làm.
13. Với một lịch tương lai đang có, gửi `Chị muốn làm thêm gội`; bot phải hỏi thêm vào lịch cũ hay tạo lịch mới, `ACTION=ASK_ADD_OR_NEW`, database chưa đổi. Chỉ sau câu `Thêm vào lịch cũ giúp chị` mới đặt `ACTION=ADD_TO_EXISTING`, thêm `GOI` và upsert đúng dòng cũ.
14. Hủy lịch phải trả lời mở đầu đúng `Dạ em xác nhận đã hủy lịch hẹn của mình ạ`, hiển thị đủ thông tin lịch và cập nhật đúng dòng database thành `CANCELLED`.
15. Sau khi hủy hoặc khi lịch cũ đã qua ngày, gửi yêu cầu đặt mới; bộ trạng thái phải dùng `CREATE`, không lấy lại ngày/giờ/dịch vụ cũ. Nếu đủ năm trường, bot chốt ngay bằng danh sách Khách hàng/SĐT/Thời gian/Dịch vụ.

Nếu vẫn có hai `AI_TRA_LOI`, kiểm tra Sequence cũ đã được REMOVE trước khi ADD chưa. Nếu chỉ có một `AI_TRA_LOI` nhưng khách nhận hai câu giống nhau, kiểm tra Messenger Text cũ trong cả `AI_TRA_LOI` và `AI_GUI_TRA_LOI` đều đã TẮT, đồng thời chỉ có một Card `AI Tạo Json` đang bật trong `AI_JSON_GUI`.

## 16. Trình tự triển khai

1. Thay Prompt hệ thống bằng bản mới trong `Prompt Chính.Md`.
2. Cập nhật lại bốn Knowledge có thay đổi: `K02`, `K03`, `K04`, `K06`; giữ nguyên `K01`, `K05`.
3. Trong Bot AI, chọn đúng K01–K06, để `Apply all` TẮT và bấm `Cập nhật`.
4. Giữ lịch sử `Messages → 20 Messages`.
5. Tạo/giữ các Attributes: ba bộ đệm, `ai_state_result` và chín trường bền vững `laris_hair_size`, `laris_dye_package`, `laris_booking_date`, `laris_booking_time`, `laris_customer_name`, `laris_customer_phone`, `laris_booking_services`, `laris_booking_status`, `laris_booking_action`.
6. Giữ `AI_NHAN_TIN` với `REMOVE → ADD NOW`.
7. Giữ Sequence `AI_GOM_TIN` chờ 15 giây.
8. Trong `AI_TRA_LOI`, dùng snapshot-and-clear, `AI Trạng Thái Lịch` → Parse Content → `Bot AI`, Delay 0,5 phút và `Go to Block → AI_JSON_GUI`; tắt Typing, Messenger Text và thẻ dọn cuối cũ.
9. Trong `AI_JSON_GUI`, bật đúng một `AI Tạo Json`, Delay 0,3 phút và `Go to Block → AI_GUI_TRA_LOI`.
10. Trong `AI_GUI_TRA_LOI`, bật Typing, JsonAPI tối ưu nội dung, thẻ dọn `ai_processing_text` + `ai_answer` và JsonAPI lịch hẹn; tắt Messenger Text `{{ai_answer}}` cũ cùng toàn bộ GenAI.
11. Giữ Default Reply chỉ trỏ `AI_NHAN_TIN`.
12. Giữ `ADS_ENTRY` và Click-to-Messenger tách khỏi Default Reply.
13. Giữ Trigger GenAI tắt trong giai đoạn đầu.
14. Giữ hai Trigger ảnh ở chế độ **Tin của Page** với đúng hai cụm điều khiển ở Mục 14; tắt ba Keyword cũ. Chỉ Default Reply/AI được quyền soạn lời; hai block ảnh chỉ đính kèm hình.
15. Bấm **Xác nhận** trên từng Card GenAI đã sửa, sau đó Save/Update block. Chỉ reset chín trường bền vững khi cần ca test sạch; khi vận hành thật không reset chúng.

## 17. Bảo trì

- Đổi giá: chỉ sửa `K02_LARIS_DICH_VU_BANG_GIA_SIZE`.
- Đổi chương trình: thay toàn bộ `K03_LARIS_UU_DAI_HIEN_TAI`; không giữ ưu đãi cũ.
- Đổi địa chỉ/giờ/hotline: chỉ sửa `K01_LARIS_THONG_TIN_SALON` và Mẫu phản hồi tương ứng.
- Đổi logic hội thoại/mẫu câu: chỉ sửa `K04_LARIS_LOGIC_TU_VAN_HOI_THOAI` và phần lõi liên quan trong Prompt.
- Sau mỗi cập nhật, xóa lịch sử Demo và chạy test lại.

## 18. Tài liệu tham khảo model

OpenAI khuyến cáo không thay model hàng loạt mà chưa giữ nguyên vai trò chi phí, độ trễ và chất lượng, đồng thời nên rút chỉ dẫn lặp/mâu thuẫn và đánh giá bằng tập ca thực tế. Smax hiện chưa có GPT-5.6 Sol/Terra/Luna trong danh sách của tài khoản; vì vậy không chọn model không tồn tại. Sau khi sửa luồng, A/B test `GPT-5.4 mini` với `GPT-5.4` trên cùng bộ test. Xem [hướng dẫn nâng cấp GPT-5.6](https://developers.openai.com/api/docs/guides/upgrading-to-gpt-5p6-sol.md) và [hướng dẫn prompting GPT-5.6](https://developers.openai.com/api/docs/guides/prompt-guidance-gpt-5p6.md).

## 19. Nhắc lịch trước 4 giờ

### Phần đã cấu hình trực tiếp trên Smax

- Đã kích hoạt module `Messenger Templates (FUM)`.
- Đã tạo mẫu được Meta phê duyệt: `laris_appointment_reminder_4h`.
- Đã tạo block `NHAC_LICH_4H_GUI_FUM`, Block ID `6a648c1b5ab32fdf364d9d35`.
- Block dùng đúng kênh `Laris Hair Studio` và đúng mẫu FUM đã phê duyệt.
- Ánh xạ biến:
  - `{{1}}` = `{{laris_customer_name}}`
  - `{{2}}` = `Laris Hair Studio (dịch vụ: services_display)`; n8n phải ánh xạ mã trong `laris_booking_services` sang tên hiển thị trước khi gửi FUM.
  - `{{3}}` = `{{laris_booking_date}}`
  - `{{4}}` = `{{laris_booking_time}}`

FUM là bắt buộc khi lời nhắc có thể được gửi ngoài cửa sổ Messenger 24 giờ. Không thay thẻ FUM bằng Messenger Text thường.

### Giới hạn đã xác minh của Sequence Smax

Sequence chỉ hỗ trợ khoảng chờ cố định hoặc các mốc lịch cố định như thứ trong tuần, ngày trong tháng và ngày trong năm. Sequence không nhận một thời điểm động kiểu `laris_booking_datetime - 4 giờ` cho từng khách.

Vì vậy tuyệt đối không cấu hình `Delay 4 giờ` ngay sau lúc khách đặt lịch: cách đó có nghĩa là gửi bốn giờ sau khi khách đặt, không phải bốn giờ trước giờ làm dịch vụ.

Để chạy đúng, cần bộ lập lịch ngoài Smax (n8n/Make hoặc một task queue trên máy chủ) thực hiện:

1. Nhận sự kiện lịch đã đủ Tên, SĐT, Ngày, Giờ và Dịch vụ.
2. Chuẩn hóa múi giờ `Asia/Ho_Chi_Minh`.
3. Tính `reminder_at = appointment_at - 4 giờ`.
4. Upsert công việc theo một khóa duy nhất của khách + lịch; đổi lịch phải hủy công việc cũ.
5. Đến `reminder_at`, gọi Smax Partner API để chạy block `6a648c1b5ab32fdf364d9d35`.
6. Chỉ đánh dấu `SENT` sau khi API Smax trả thành công; retry có giới hạn nếu lỗi tạm thời.
7. Không gửi nếu lịch đã hủy, đã đổi, thiếu trường hoặc thời điểm nhắc đã qua.

### Bộ thuộc tính trạng thái nên dùng

- `laris_booking_datetime`: ngày giờ hẹn đầy đủ theo chuẩn ISO có múi giờ.
- `laris_reminder_at`: thời điểm nhắc trước bốn giờ.
- `laris_reminder_status`: `PENDING`, `SENT`, `CANCELLED` hoặc `FAILED`.
- `laris_reminder_key`: khóa duy nhất để chống gửi lặp.
- `laris_reminder_sent_at`: thời điểm gửi thành công.

### Điều kiện nghiệm thu

1. Lịch 18:00 phải tạo mốc nhắc 14:00 cùng ngày theo giờ Việt Nam.
2. Đổi lịch 18:00 thành 19:00 phải hủy mốc 14:00 và chỉ giữ mốc 15:00.
3. Khách nhắn thêm nhưng không đổi lịch không được tạo công việc nhắc thứ hai.
4. Hủy lịch phải chuyển trạng thái `CANCELLED` và không gửi.
5. Log phải chứng minh chỉ một lần gọi block FUM và một tin được gửi.
6. Mẫu gửi phải có đúng Tên, Dịch vụ, Ngày và Giờ; không chứa khuyến mãi.

## 20. Trạng thái triển khai n8n ngày 25/07/2026

### Thành phần đang chạy

- Workflow nhận lịch: `LARIS - Nhận và cập nhật lịch hẹn`, ID `X4dC4UNsQD5VrwA1`, trạng thái **Published**.
- Production webhook: `https://n8n.nguyenthephuong.com/webhook/laris-appointment-upsert`.
- Workflow nhắc lịch: `LARIS - Gửi nhắc lịch trước 4 giờ`, ID `Kxx5T49xJs5N6tlO`, trạng thái **Published**, quét mỗi 2 phút.
- Data Table: `laris_appointment_reminders`, ID `lTFTZ7EdVYmwFEd9`.
- Smax Messenger API FUM trigger: `NHAC_LICH_4H_API_FUM_N8N`.
- Mẫu Meta đã duyệt: `laris_appointment_reminder_4h`.

Token webhook và bearer FUM chỉ được lưu trong cấu hình Smax/n8n. Không ghi các giá trị này vào Markdown, JSON bàn giao hoặc Knowledge.

### Luồng nhận và cập nhật lịch

`AI_GUI_TRA_LOI` gửi JSON gồm `customer_pid`, `page_pid`, tên, số điện thoại, dịch vụ, ngày, giờ, `booking_status` và `booking_action` sang webhook. n8n thực hiện:

1. Chuẩn hóa ngày/giờ theo `Asia/Ho_Chi_Minh`.
2. Giữ `services` bằng mã để upsert/chống trùng và sinh `services_display` bằng ánh xạ: `DUOI` = Duỗi, `DUOI_HOI_NUOC` = Duỗi hơi nước, `DUOI_CHAN_TOC` = Duỗi chân tóc, `XU_LY_DUOI_CHAN_KEM_UON` = Xử lý Duỗi chân tóc đi kèm Uốn. Không gộp các biến thể vào `DUOI`.
3. Từ chối ngày quá khứ; không upsert khi `ACTION=NONE`, `ACTION=ASK_ADD_OR_NEW` hoặc `STATUS=DRAFT`.
4. Tính `reminder_at = appointment_at - 4 giờ`.
5. Kiểm tra chống lặp trước khi upsert.
6. `CREATE`, `UPDATE` và `ADD_TO_EXISTING` upsert đúng `booking_key`; `CANCEL` cập nhật cùng dòng thành `CANCELLED`; `CREATE_SEPARATE` chỉ được chấp nhận sau khi khách đã chọn lịch mới.

Bộ lọc `Bỏ qua lịch đã gửi` dùng `If Row Does Not Exist` và bắt buộc đồng thời bốn điều kiện: cùng `booking_key`, cùng `appointment_at`, cùng `services`, trạng thái `SENT`. Vì vậy một tin nhắn khách gửi thêm sau khi nhắc lịch không thể đổi lịch cũ từ `SENT` về `PENDING`. Nếu khách đổi ngày/giờ hoặc dịch vụ thì dữ liệu khác và lịch mới vẫn được cập nhật.

### Luồng gửi FUM

Scheduler chỉ chọn dòng `PENDING` có `reminder_at <= now` và `appointment_at > now`, sau đó gọi trực tiếp Messenger API FUM. Nội dung dịch vụ ưu tiên `services_display`; nếu dòng cũ chưa có trường này thì workflow tách `services` và ánh xạ bằng cùng bảng mã trước khi gửi. Không dùng đường Smax Partner API chạy block cũ vì kiểm thử thực tế trả lỗi quyền `pages_utility_messaging`.

Chỉ đánh dấu `SENT` khi phản hồi có `status = 200`, `subStatus = 200` và `message = OK`. Nếu API không thỏa ba điều kiện này, dòng chuyển `FAILED` và lưu `last_error`, không được ghi nhận giả là đã gửi.

### Bằng chứng nghiệm thu

- Execution `317` lúc 19:32:51 chạy thành công toàn bộ scheduler.
- Smax nhận tin FUM lúc 19:32 với đúng: Phương Bùi, dịch vụ cắt nữ và gội kiểm thử, ngày `25/07/2026`, giờ `20:00`.
- Dòng kiểm thử được chuyển sang `SENT` và có `sent_at`.
- Execution tự động `318` lúc 19:34:10 chạy thành công nhưng không có lịch `PENDING` đến hạn, vì vậy không gửi lại.
- Kiểm thử production webhook sau khi thêm bộ lọc: tạo một lịch giả ở tương lai, chuyển sang `SENT`, gọi lại cùng payload nhiều lần; trạng thái vẫn là `SENT` và `updatedAt` không đổi ở lần gọi cuối.
- Secret header `X-Laris-Webhook-Key` đã được xoay, xác nhận lưu bền vững trên thẻ JsonAPI Smax và đồng bộ với phiên bản n8n đã Published. Giá trị bí mật không được ghi vào file bàn giao.
- Sau nghiệm thu đã xóa toàn bộ dữ liệu kiểm thử; Data Table hiển thị `Total 0`.

### Giới hạn cần biết

Flow hiện xử lý chắc chắn việc tạo/cập nhật lịch và chống gửi lặp. Hủy lịch chỉ tự động dừng nhắc khi Smax gửi thêm trạng thái hủy vào webhook; nếu chưa tạo thuộc tính `laris_booking_status` và ánh xạ trường này trong JsonAPI thì nhân viên cần xóa/chuyển `CANCELLED` dòng lịch tương ứng trong Data Table.

Không sửa trực tiếp bearer, endpoint FUM hoặc webhook secret từ các file JSON bàn giao. Hai file `n8n_LARIS_UPSERT_LICH.json` và `n8n_LARIS_GUI_NHAC_LICH_4H.json` là tài liệu tham chiếu đã loại bỏ bí mật, không phải bản import ghi đè.

## 21. Tối ưu nội dung GenAI qua n8n — nghiệm thu ngày 26/07/2026

### Workflow đang chạy

- Workflow n8n: `Smax Tài`, ID `AXe1aUxpSW4sDkyV`, trạng thái **Published**.
- Kiến trúc: `Webhook → HTTP Request → Smax Partner API`.
- Webhook nhận một trong hai dạng:
  - `cards`: mảng Messenger cards đã có sẵn.
  - `text` hoặc `answer`: câu trả lời thô của GenAI.
- n8n làm sạch token `__EMPTY__`, bỏ ký tự rỗng, chuẩn hóa dòng trống và tách nội dung thành tối đa 5 thẻ `messenger_text`.
- Mỗi thẻ tối đa 600 ký tự, luôn có `buttons: []`; ưu tiên tách ở ranh giới đoạn văn, sau đó mới tách ở khoảng trắng.
- n8n không gọi thêm model AI. Nội dung tư vấn vẫn do Bot AI chính tạo; n8n chỉ chuẩn hóa định dạng và gửi.

### Biểu thức JSON Body

Node HTTP Request phải để toàn bộ trường JSON Body ở chế độ **Expression** và biểu thức phải trả về một object hoàn chỉnh:

```javascript
{{ (() => {
  const body = $json.body ?? {};
  let cards = [];

  if (Array.isArray(body.cards) && body.cards.length) {
    cards = body.cards;
  } else if (typeof body.cards === 'string' && body.cards.trim()) {
    try {
      const parsed = JSON.parse(body.cards);
      if (Array.isArray(parsed)) cards = parsed;
    } catch (e) {}
  }

  if (!cards.length) {
    const text = String(body.text ?? body.answer ?? '')
      .replace(/__EMPTY__/g, '')
      .replace(/\r/g, '')
      .replace(/\u0000/g, '')
      .replace(/\n{3,}/g, '\n\n')
      .trim();

    if (text) {
      const max = 600;
      const units = text.split(/\n{2,}/).map(v => v.trim()).filter(Boolean);
      const chunks = [];
      let current = '';

      for (const unitValue of units) {
        let unit = unitValue;
        if (unit.length > max) {
          if (current) {
            chunks.push(current);
            current = '';
          }
          while (unit.length > max) {
            let cut = unit.lastIndexOf(' ', max);
            if (cut < 200) cut = max;
            chunks.push(unit.slice(0, cut).trim());
            unit = unit.slice(cut).trim();
          }
          current = unit;
        } else if (!current) {
          current = unit;
        } else if ((current + '\n\n' + unit).length <= max) {
          current += '\n\n' + unit;
        } else {
          chunks.push(current);
          current = unit;
        }
      }

      if (current) chunks.push(current);
      cards = chunks.filter(Boolean).slice(0, 5).map(value => ({
        type: 'messenger_text',
        config: { text: value, buttons: [] }
      }));
    }
  }

  return {
    customer_pid: String(body.customer_pid ?? ''),
    page_pid: String(body.page_pid ?? ''),
    cards
  };
})() }}
```

Không đặt object JSON cố định bên ngoài rồi chèn một biểu thức `{{...}}` vào trường `cards`; cấu hình đó đã được kiểm chứng là gây lỗi `The value in the "JSON Body" field is not valid JSON`.

### Bằng chứng kiểm thử production

- Execution `752`: kiểm thử trực tiếp sau khi sửa JSON Body — **Succeeded**.
- Execution `757`: tin thật từ Facebook `Cho mình xin giá uốn size M ạ` — **Succeeded**, Smax API trả `200 OK`.
- Facebook nhận đúng một câu: uốn C size M giá gốc 1tr còn 850k; uốn xoăn size M giá gốc 1tr200k còn 1tr020k; có CTA chọn kiểu uốn.
- Execution `761`: nội dung dài được tách thành đúng hai thẻ; Facebook hiển thị đủ `1/2` và `2/2`, tiếng Việt đúng, không cắt giữa từ, không có thẻ rỗng.
- Execution `763`: sau khi đã cung cấp size M, khách hỏi `Còn duỗi thì giá bao nhiêu ạ` — bot dùng lại size M, báo giá gốc 1tr, giảm 15% còn 850k và không hỏi lại size.
- Execution `766`: khách hỏi `Cho mình xin bảng giá` — Facebook nhận câu có cụm `bảng giá dịch vụ` và đúng một ảnh bảng giá. Điều này xác nhận tin gửi qua Smax Partner API vẫn kích hoạt được trigger đọc **Tin của Page**.
- Ngày 26/07/2026: với lịch tương lai 28/7 lúc 15h, câu `Chị muốn làm thêm gội` đã được bot hỏi chọn lịch cũ hay lịch mới; database không đổi trước lựa chọn.
- Câu `Thêm vào lịch cũ giúp chị` đã cập nhật đúng dòng hiện hữu từ `CAT_NU,DUOI` thành `CAT_NU,DUOI,GOI`, không tạo dòng trùng.
- Hủy lịch đã trả lời đúng câu xác nhận hủy và database chuyển đúng dòng sang `CANCELLED`.
- Đặt mới sau hủy đã bỏ dữ liệu lịch đóng, chốt ngày `28/7 (thứ ba)` lúc `15h` và không sao chép sai tên thứ. Dòng trùng phát sinh trong lần thử cấu hình trung gian đã được xóa; bảng kiểm tra còn đúng một dòng lịch chính.

### Quy tắc bảo trì

- Không bật lại Messenger Text `{{ai_answer}}` trong `AI_GUI_TRA_LOI` khi JsonAPI tối ưu nội dung đang bật.
- Không đổi thứ tự để thẻ dọn `ai_answer` chạy trước JsonAPI tối ưu.
- Không bật thêm Card `AI Tạo Json` thứ hai ngoài `AI_JSON_GUI`.
- Formatter n8n luôn ưu tiên `body.text`/`body.answer` hơn `body.cards`; workflow lịch luôn chặn DRAFT/ASK/NONE/ngày quá khứ.
- Không ghi bearer Smax, UUID webhook production hoặc secret header vào Markdown/Knowledge.
- Khi sửa workflow, phải Published phiên bản mới rồi test đủ ba lớp: execution n8n `Succeeded` → Smax API `200 OK` → Facebook hiển thị đúng số tin.

## 22. Cấu hình production cho Instagram — nghiệm thu ngày 30/07/2026

Không dùng nguyên xi thẻ gửi của Facebook cho Instagram. Có thể dùng chung Prompt, Knowledge, bộ nhớ khách và hai workflow n8n xử lý nội dung/lịch hẹn, nhưng lớp gửi cuối phải đúng loại thẻ của kênh.

Luồng Instagram đã kiểm chứng:

```text
Instagram Default
  → AI_TRA_LOI_IG
  → AI_JSON_GUI_IG
  → AI_GUI_TRA_LOI_IG
  → JsonAPI gọi workflow Smax Tài
  → ánh xạ JSON Path answer vào attribute noidung
  → Instagram Text {{noidung}}
```

Cấu hình bắt buộc:

- Trigger Default phải chọn đúng tài khoản Instagram `Laris Hair Studio`, ID `ig17841429640037092`.
- Webhook của workflow `Smax Tài` phải để `Respond = When Last Node Finishes` và `Response Data = First Entry JSON`. Nếu để `Immediately`, Smax nhận phản hồi trước khi node Code tạo trường `answer`.
- Card JsonAPI trong `AI_GUI_TRA_LOI_IG` ánh xạ JSON Path `answer` vào attribute `noidung`.
- Card gửi cuối của Instagram là `Instagram Text` với nội dung `{{noidung}}`.
- Không dùng endpoint Partner `/partner/bizs/{bizAlias}/send` để gửi Instagram; endpoint đó chỉ gửi Messenger. Nếu gọi với `customer_pid/page_pid` Instagram có thể vẫn trả `200 OK` nhưng `total = 0`.
- Không để thêm `Messenger Text`, GenAI hoặc đường gửi thứ hai trong `AI_GUI_TRA_LOI_IG`.

Hai trigger ảnh Instagram:

| Trigger | Nguồn đọc | Điều kiện duy nhất | Block đích |
|---|---|---|---|
| `Chatbot AI - Tư Vấn Size` | `Tin của Page` | Chứa `size tóc hiện tại` | Chỉ một card `Instagram Image` ảnh size |
| `Chatbot AI - Bảng giá` | `Tin của Page` | Chứa `bảng giá dịch vụ` | Chỉ một card `Instagram Image` ảnh bảng giá |

Chuỗi từ khóa phải nhập lại bằng Unicode tiếng Việt chuẩn, đúng hệt câu Bot AI gửi. Không sao chép chuỗi dấu tổ hợp cũ như `bảng giá dịch vụ`, vì giao diện trông giống nhau nhưng bộ so khớp có thể không kích hoạt. Từ khóa production đã chuẩn hóa là `bảng giá dịch vụ`.

Các keyword cũ/trùng như `Laris đang có ưu đãi gì?` đang tắt thì giữ tắt. Không bật lại để tránh một tin khách chạy song song Default và keyword, gây trả lời lặp.

### Bằng chứng kiểm thử Instagram thật ngày 30/07/2026

- `Cho mình xin giá cắt mái riêng ạ - IGTEST0730C` → nhận đúng một câu: cắt mái riêng 50k và CTA hỏi ngày ghé.
- `Giá dịch vụ uốn ạ - IGTESTSIZE0730` → nhận câu tư vấn uốn, hỏi đúng cụm `size tóc hiện tại`, sau đó nhận đúng một ảnh size.
- Lần đầu `Cho mình xin bảng giá dịch vụ ạ - IGTESTPRICE0730` → nhận lời nhưng thiếu ảnh; xác định trigger lưu chuỗi Unicode dấu tổ hợp.
- Sau khi chuẩn hóa trigger, `Cho mình xin bảng giá dịch vụ ạ - IGTESTPRICE0730B` → nhận lời tự nhiên trước, sau đó nhận đúng một ảnh bảng giá.
- Workflow `Smax Tài` và workflow lịch hẹn đều ở trạng thái **Published** sau khi sửa.

Tiêu chí nghiệm thu đa kênh: execution n8n thành công chỉ là lớp giữa. Bắt buộc kiểm tra tiếp Smax đã nhận được trường `answer`, đúng card theo kênh và tin/ảnh thực sự hiển thị trong hộp thư Facebook hoặc Instagram.

### Cập nhật production cuối: gom tin riêng theo từng nền tảng

Kiểm thử trực tiếp ngày 30/07/2026 cho thấy cách xử lý từng tin độc lập vẫn có thể làm hai lượt GenAI cùng đọc tin cuối và trả lời lặp. Cấu hình production cuối dùng một Sequence riêng cho mỗi nền tảng; không dùng chung tên Sequence giữa Facebook và Instagram.

Luồng Facebook:

```text
Facebook Default Reply
  → AI_NHAN_TIN
  → REMOVE + ADD AI_GOM_TIN_FB_DEBOUNCE
  → AI_TRA_LOI
  → AI_JSON_GUI
  → AI_GUI_TRA_LOI
  → JsonAPI gọi workflow Smax Tài
  → n8n gửi qua Smax Partner API
```

Luồng Instagram:

```text
Instagram Default
  → Default Reply IG
  → AI_NHAN_TIN_IG
  → REMOVE + ADD AI_GOM_TIN_IG_DEBOUNCE
  → AI_TRA_LOI_IG
  → AI_JSON_GUI_IG
  → AI_GUI_TRA_LOI_IG
  → JsonAPI gọi workflow Smax Tài
  → ánh xạ answer vào noidung
  → Instagram Text {{noidung}}
```

Quy tắc bắt buộc:

- Facebook chỉ dùng Sequence `AI_GOM_TIN_FB_DEBOUNCE`; Instagram chỉ dùng `AI_GOM_TIN_IG_DEBOUNCE`. Không đặt trùng tên và không nối chéo kênh.
- `AI_TRA_LOI` và `AI_TRA_LOI_IG` đều phải nhận `CURRENT_BATCH={{ai_processing_text}}`. Không thay batch bằng riêng `last_content_by_user`.
- `AI_NHAN_TIN_IG` nối thêm tin mới vào `ai_pending_text` và đồng thời cập nhật `ai_processing_text`; card đầu của `AI_TRA_LOI_IG` hợp nhất processing với pending trước khi xóa pending.
- Facebook gửi cuối bằng n8n/Smax Partner API; giữ tắt Messenger Text cũ trong Smax để không gửi hai lần.
- Instagram gửi cuối bằng `Instagram Text {{noidung}}`; không thêm Messenger Text hoặc một card GenAI gửi thứ hai.
- Thời gian 15 hoặc 30 giây trên Sequence là thời gian cấu hình danh nghĩa. Trong kiểm thử thật, scheduler Smax có thể thực thi sau khoảng 1–2 phút; không kết luận flow hỏng chỉ vì chưa thấy phản hồi ngay sau 30 giây.

### Cấu hình chịu lỗi của workflow n8n `Smax Tài`

Workflow production đã Published với phiên bản `Retry Partner API và tiếp tục Instagram`:

- `Webhook → HTTP Request → Code in JavaScript`.
- HTTP Request bật `Retry On Fail`, tối đa 3 lần, cách nhau 2 giây.
- Khi Partner API lỗi, dùng chế độ `Continue` để node Code vẫn trả trường `answer` cho Smax.
- Facebook cần HTTP Request để gửi Messenger qua Partner API.
- Instagram không phụ thuộc việc Partner API gửi thành công; Smax lấy `answer` rồi card `Instagram Text` gửi đúng kênh.
- Webhook giữ `Respond = When Last Node Finishes` và `Response Data = First Entry JSON`.

### Bằng chứng kiểm thử production cuối ngày 30/07/2026

- Facebook, một tin `Giá cắt mái bao nhiêu ạ FBREBIND0730` → đúng một phản hồi, giá cắt mái riêng 50k.
- Facebook, hai tin cách nhau 7 giây `Cho chị xin giá cắt mái FBBATCHPASS0730` và `Địa chỉ salon ở đâu ạ FBBATCHPASS0730` → đúng một phản hồi trả đủ giá 50k và địa chỉ; chờ thêm 55 giây không có tin lặp. Execution n8n `4136` thành công.
- Instagram, hai tin cách nhau 7 giây `Cho chị xin giá cắt mái IGFINALPASS0730` và `Địa chỉ salon ở đâu ạ IGFINALPASS0730` → đúng một phản hồi trả đủ giá 50k và địa chỉ; chờ thêm 50 giây không có tin lặp.
- Execution n8n `4174` thành công trong 7,115 giây dù đường Partner API cần cơ chế retry/continue. Instagram vẫn nhận đủ nội dung qua card đúng kênh.

## 23. Kiểm soát token production

Nguyên tắc: ưu tiên đúng trước, sau đó mới giảm token.

- `Bot AI` giữ model `gpt-5.4-mini-2026-03-17`; đây là lớp cần suy luận ngữ cảnh, giá, size và lịch hẹn.
- `AI Tạo Json` dùng `gpt-5.4-nano-2026-03-17`; nhiệm vụ chỉ là tạo cấu trúc ổn định, không cần model lớn.
- Lịch sử hội thoại của Bot AI giữ 10 tin gần nhất thay vì 20.
- Bot AI chỉ gắn bốn Knowledge cần thiết `K01`, `K02`, `K03`, `K05`. Logic hội thoại và đặt lịch đã nằm trong Prompt/trạng thái bền vững nên không gắn lặp `K04`, `K06`.
- Không gọi thêm GenAI chỉ để đổi văn phong nếu formatter n8n có thể xử lý xác định được.

Số liệu đo trực tiếp:

- Mốc cũ Bot AI: 729 request, 17.602.378 token, trung bình khoảng 24.146 token/request.
- Ngay sau khi giảm lịch sử và Knowledge: request thứ 730 tiêu thụ 15.568 token, giảm khoảng 35,5% so với trung bình cũ.
- Đến 747 request: 17.858.652 token. 17 request sau mốc tối ưu tiêu thụ 240.706 token, trung bình khoảng 14.159 token/request, thấp hơn khoảng 41,4% so với trung bình lịch sử.
- `AI Tạo Json`: 123 request; giao diện Smax đang hiển thị tổng 8.105 token.
- `AI Trạng Thái Lịch`: 109 request, 309.356 token.

Không giảm thêm model/lịch sử/Knowledge nếu chưa chạy lại bộ hồi quy về size, gói nhuộm, ngày giờ, đặt/đổi/hủy lịch và hai tin liên tiếp trên cả hai nền tảng.
