# HƯỚNG DẪN CẤU HÌNH SMAX — LARIS

> Tệp dành cho quản trị viên, không tải vào Knowledge.

## 1. Router duy nhất

Giữ một đường vào cho tin nhắn tự do:

```text
Messenger Default
→ AI_NHAN_TIN
→ AI_GOM_TIN_FB_DEBOUNCE
→ AI_TRA_LOI
→ sender của kênh
```

- Tắt Trigger GenAI trực tiếp và nhánh Other trả lời trực tiếp nếu Messenger Default đang làm router tổng.
- Keyword chung như giá, ưu đãi, đặt lịch phải tắt hoặc chỉ chuyển về `AI_NHAN_TIN`; không tự gọi GenAI hay tự gửi câu trả lời.
- Click To Message chỉ đưa chữ khách tự nhập vào buffer; metadata quảng cáo không được coi là yêu cầu của khách.

## 2. Chặn metadata `Updates and promotions` bằng Keyword Trigger

Theo Smax, Messenger Default chỉ chạy khi tin nhắn không khớp một trigger cụ thể khác, còn Trigger Keywords hỗ trợ kiểu khớp **Trùng khớp** cho tin của khách hàng.

Tạo Trigger Keyword:

```text
Tên: IGNORE_META_UPDATES_PROMOTIONS
Nguồn: Tin của khách hàng
Kiểu khớp: Trùng khớp
Từ khóa 1: Đăng kí topic: Updates and promotions
Từ khóa 2: Đăng ký topic: Updates and promotions
```

Block đích: `IGNORE_META` hoặc block rỗng chỉ để kết thúc luồng.

Block này không được có Messenger Text/Typing, GenAI, Sequence, JsonAPI hoặc Go To Block về `AI_NHAN_TIN`.

## 3. Size tóc phải là state toàn cục

Attribute dùng:

```text
laris_hair_size      Text
laris_dye_package    Text
```

`laris_hair_size` không thuộc riêng Nhuộm. Đây là size toàn bộ tóc của khách và phải được dùng lại cho mọi dịch vụ có giá theo size.

Ví dụ:

```text
Khách hỏi Nhuộm → xác nhận Size L
↓
laris_hair_size = L
↓
Khách chuyển sang Uốn / Duỗi / Phục hồi / Tẩy / Balayage
↓
Bot tiếp tục dùng Size L
```

Không reset `laris_hair_size` khi khách đổi dịch vụ. Chỉ đổi/reset khi khách tự sửa size, nói đang hỏi cho người khác/đổi người làm, hoặc mô tả tóc mới mâu thuẫn rõ.

Trong Card `AI Trạng thái` dùng prompt `SMAX_EMBEDDED_PROMPT_STATE_2026-08-11.md` bản mới. Parse Content phải map:

```text
SIZE    → laris_hair_size
PACKAGE → laris_dye_package
```

Trong Card `Bot AI` bắt buộc truyền:

```text
PERSISTENT_HAIR_SIZE={{laris_hair_size}}
PERSISTENT_DYE_PACKAGE={{laris_dye_package}}
```

Nếu `laris_hair_size=L`, câu `chị muốn làm thêm uốn kèm duỗi` chỉ được hỏi `Uốn C hay Uốn xoăn`, không hỏi lại size.

## 4. Gom tin 15 giây — cấu hình chuẩn

Theo tài liệu Smax về Sequence: một khách không thể ADD lại vào cùng Sequence khi còn nằm trong Sequence. Muốn khởi động lại thời gian phải **REMOVE → ADD**. Cấu hình REMOVE + ADD cùng `AI_GOM_TIN_FB_DEBOUNCE` trong ảnh hiện tại là đúng nguyên tắc này.

### 4.1 Attributes

Tạo/giữ hai Text Attributes:

```text
ai_pending_text
ai_processing_text
```

### 4.2 Block `AI_NHAN_TIN`

Card 1 — Set Attributes:

```text
ai_pending_text = {{ai_pending_text}}
{{last_content_by_user}}
```

Mục tiêu: mỗi tin mới nối vào buffer hiện tại, không ghi đè tin trước.

Card 2 — Sequence, đúng thứ tự:

```text
REMOVE  AI_GOM_TIN_FB_DEBOUNCE
ADD     AI_GOM_TIN_FB_DEBOUNCE   NOW
```

Không đặt GenAI hoặc Messenger Text trong `AI_NHAN_TIN`.

### 4.3 Sequence `AI_GOM_TIN_FB_DEBOUNCE`

Chỉ giữ **một Step duy nhất**:

```text
+15 second(s)
→ AI_TRA_LOI
```

Không có Step thứ hai, không có sender và không có một Sequence debounce khác chạy song song cho cùng Facebook Page.

### 4.4 Đầu block `AI_TRA_LOI`

Card đầu tiên phải snapshot batch:

```text
ai_processing_text = {{ai_pending_text}}
ai_pending_text    = [rỗng]
```

Sau đó mới chạy:

```text
AI Trạng thái
→ Parse Content
→ Bot AI
→ một Messenger Text duy nhất
```

Card Bot AI phải đọc:

```text
CURRENT_BATCH={{ai_processing_text}}
```

Không chỉ đọc `last_content_by_user`, vì biến đó chỉ chứa tin cuối.

Nên thêm bộ lọc `ai_processing_text có giá trị / không rỗng` cho Card GenAI hoặc sender cuối. Mục tiêu: nếu vì lý do nào đó Sequence gọi `AI_TRA_LOI` lần hai sau khi pending đã được lấy hết, lượt rỗng phải dừng và không gửi lại câu cũ.

## 5. Ảnh hiện tại cho thấy lỗi chính là trả lời hai lần, không phải mất dữ kiện gom tin

Trong case thực tế:

```text
Khách: Size L
Khách: uốn xoăn
Bot: [cùng một câu giá Uốn xoăn Size L]
Bot: [lặp lại y hệt]
```

Cả hai câu Bot đều đã biết **Size L + Uốn xoăn**. Điều này cho thấy dữ kiện đã tới được Bot; vấn đề cần tìm là **AI_TRA_LOI bị chạy hai lần hoặc cùng ai_answer bị gửi bởi hai sender**, không nên sửa Prompt để che lỗi.

### 5.1 Kiểm tra Sequence Logs trước

Vào:

```text
AI_GOM_TIN_FB_DEBOUNCE
→ Logs
```

Test bằng tài khoản thử:

```text
Tin 1: Size L
sau 1–3 giây
Tin 2: uốn xoăn
```

Chờ hơn 15 giây.

Kết quả chuẩn: chỉ có **một lần** Step `→ AI_TRA_LOI` thực thi sau tin cuối.

### 5.2 Nếu Logs cho thấy `AI_TRA_LOI` chạy 2 lần

Kiểm tra và tắt các đường cạnh tranh:

1. Có Trigger Keyword nào của `Size`, `Uốn`, `Nhuộm`, giá... tự đi tới `AI_TRA_LOI` hoặc tự gọi GenAI không.
2. Có Trigger GenAI/Other đang bật song song Messenger Default không.
3. Có hai block `AI_NHAN_TIN` hoặc hai Sequence debounce cùng được gọi không.
4. Sequence có nhiều hơn một Step tới `AI_TRA_LOI` không.
5. Cùng một Keyword có vừa Go To `AI_NHAN_TIN` vừa có sender/GenAI riêng không.

Mục tiêu cuối: mọi tin tự do chỉ có **một router** `Messenger Default → AI_NHAN_TIN`.

### 5.3 Nếu Logs cho thấy `AI_TRA_LOI` chỉ chạy 1 lần nhưng khách nhận 2 tin giống nhau

Đây là lỗi sender.

Tìm toàn bộ flow sau `AI_TRA_LOI` và chỉ giữ một nơi gửi `ai_answer`:

```text
Messenger Text = {{ai_answer}}
```

Tắt/xóa các sender cũ như:

- Messenger Text thứ hai.
- `AI_GUI_TRA_LOI` cũ nếu một sender đã nằm ngay trong `AI_TRA_LOI`.
- JsonAPI/n8n từng dùng để gửi lại text.
- `AI_JSON_GUI` hoặc block dự phòng dẫn tới sender khác.

Một lượt Bot AI chỉ được có **một final sender**.

## 6. Cấu hình `AI_TRA_LOI`

Thứ tự gọn nhất:

```text
1. Set Attributes: snapshot pending → processing, clear pending
2. AI Trạng thái
3. Parse Content: SIZE/PACKAGE
4. Bot AI
5. Messenger Text {{ai_answer}}
```

- State extractor chỉ lưu `laris_hair_size` và `laris_dye_package`.
- Bot AI nhận CURRENT_MESSAGE, CURRENT_BATCH, STATE_RESULT, PERSISTENT_HAIR_SIZE và PERSISTENT_DYE_PACKAGE.
- Gắn K01, K02, K03 và K05. K04 là tài liệu hành vi tham chiếu; không cần gắn nếu toàn bộ luật đã nằm trong Prompt Chính.
- Không dùng GenAI đóng gói JSON trung gian.

## 7. Sender

- Facebook: một card Messenger Text với `ai_answer` làm sender duy nhất.
- Instagram: chỉ một Instagram Text.
- Không dùng n8n để format, gửi, note lịch hoặc nhắc lịch.
- Không giữ sender dự phòng có thể gửi lại cùng nội dung.

## 8. Báo giá và CTA đặt lịch

CTA sau báo giá được xử lý ở Prompt/GenAI, không tạo block CTA riêng.

- Có báo giá → một lời mời đặt lịch mềm.
- Nếu lượt trước vừa hỏi đặt lịch và khách chưa trả lời ý đó → không hỏi lại.
- Khách nói chỉ tham khảo/chưa đặt → không bám tiếp CTA trong cùng mạch.

## 9. Duỗi kết hợp Uốn

Không tạo flow riêng:

```text
Duỗi kết hợp Uốn
= Uốn C hoặc Uốn xoăn
+ Duỗi chân tóc (áp dụng khi Uốn)
```

Nếu size đã lưu từ dịch vụ trước thì dùng lại. Chỉ hỏi dữ kiện còn thiếu.

## 10. Đặt lịch thủ công

- Tin đặt lịch vẫn đi qua router chung và Bot AI.
- Sau khi khách đồng ý đặt lịch, AI chỉ hỏi dịch vụ, thời gian hoặc SĐT còn thiếu.
- Không webhook, Data Table hoặc n8n nhắc lịch.

## 11. Khách xin ảnh

- Bot AI chỉ gửi lời chờ; nhân viên gửi ảnh thủ công.
- Chỉ giữ trigger ảnh hướng dẫn size khi Bot AI thật sự hỏi `size tóc hiện tại`.

## 12. Nghiệm thu

1. Test `Size L` ở Nhuộm rồi đổi sang Uốn/Duỗi/Balayage: Bot không hỏi lại size.
2. Test hai tin `Size L` → `uốn xoăn` trong dưới 15 giây: chỉ một lượt `AI_TRA_LOI` và một tin trả lời.
3. Xem Sequence Logs để phân biệt lỗi debounce và lỗi sender.
4. Test metadata `Updates and promotions`: 0 GenAI, 0 sender.
5. Chạy toàn bộ regression test trong file test.
