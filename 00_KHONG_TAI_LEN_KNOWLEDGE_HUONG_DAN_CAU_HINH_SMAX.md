# HƯỚNG DẪN CẤU HÌNH SMAX — LARIS

> Tệp dành cho quản trị viên, không tải vào Knowledge.

## 1. Router duy nhất

Giữ một đường vào cho tin nhắn tự do:

```text
Messenger Default
→ AI_NHAN_TIN
→ AI_GOM_TIN
→ AI_TRA_LOI
→ sender của kênh
```

- Tắt Trigger GenAI trực tiếp và nhánh Other trả lời trực tiếp nếu Messenger Default đang làm router tổng.
- Keyword chung như giá, ưu đãi, đặt lịch phải tắt hoặc chỉ chuyển về `AI_NHAN_TIN`; không tự gọi GenAI hay tự gửi câu trả lời.
- Click To Message chỉ đưa chữ khách tự nhập vào buffer; metadata quảng cáo không được coi là yêu cầu của khách.

## 2. Chặn metadata `Updates and promotions` bằng Keyword Trigger

Theo Smax, **Messenger Default chỉ chạy khi tin nhắn không khớp một trigger cụ thể khác**, còn Trigger Keywords hỗ trợ kiểu khớp **Trùng khớp** cho tin của khách hàng. Vì vậy cách đơn giản và chắc chắn nhất là chặn metadata bằng một Keyword Trigger riêng, không đưa nó vào `AI_NHAN_TIN`.

Tạo Trigger Keyword:

```text
Tên: IGNORE_META_UPDATES_PROMOTIONS
Nguồn: Tin của khách hàng
Kiểu khớp: Trùng khớp
Từ khóa 1: Đăng kí topic: Updates and promotions
Từ khóa 2: Đăng ký topic: Updates and promotions
```

Block đích: `IGNORE_META` hoặc block rỗng chỉ để kết thúc luồng.

Block này **không được có**:

- Messenger Text/Typing gửi khách.
- Go To Block → `AI_NHAN_TIN`.
- GenAI.
- Sequence.
- JsonAPI hoặc sender khác.

Kết quả mong muốn: trigger Keyword bắt đúng metadata trước, nên Messenger Default không chạy; khách nhận **0 phản hồi**.

Nếu sau này Meta đổi nội dung metadata, thêm đúng chuỗi mới vào Trigger này. Không dùng từ khóa rộng như `Updates`, `promotions` hoặc `topic` vì có thể chặn nhầm lời khách thật.

Prompt vẫn giữ một guardrail bỏ qua metadata nếu nó lọt vào batch, nhưng lớp chặn chính phải nằm ở Trigger.

## 3. Gom tin và chống trả lời hai lần

Trong `AI_NHAN_TIN`, với các tin khách thật không bị chặn bởi Keyword metadata:

1. Nối tin khách mới vào `ai_pending_text`.
2. REMOVE sequence debounce đang chờ.
3. ADD lại đúng một sequence với thời gian 15 giây.

Sau thời gian chờ:

1. Chuyển batch sang `ai_processing_text`.
2. Xóa pending.
3. Gọi `AI_TRA_LOI` đúng một lần.
4. Chỉ một Bot AI được phép tạo nội dung gửi khách.
5. Nối thẳng sang một sender cuối của kênh.

Nếu vẫn lặp, xem Card Logs/Block Logs cho cùng message ID. Hai lượt chạy nghĩa là còn router/Keyword/sequence/sender cạnh tranh; không sửa Prompt để che lỗi flow.

## 4. Cấu hình AI_TRA_LOI

- State extractor chỉ cần lưu `laris_hair_size` và `laris_dye_package` nếu hai state này đang dùng.
- Parse Content chỉ ánh xạ các thuộc tính thật sự cần.
- Bot AI nhận CURRENT_MESSAGE, CURRENT_BATCH, STATE_RESULT, size và gói.
- Gắn K01, K02, K03 và K05. K04 là tài liệu hành vi tham chiếu; không cần gắn nếu toàn bộ luật đã nằm trong Prompt Chính.
- Bot AI chỉ xuất một phản hồi tự nhiên.

## 5. Sender

- Facebook: một card Messenger Text với `ai_answer` làm sender duy nhất.
- Instagram: chỉ một Instagram Text.
- Không dùng n8n để format, gửi, note lịch hoặc nhắc lịch.
- Không giữ `AI_JSON_GUI`, JsonAPI gửi nội dung, webhook lịch hoặc sender dự phòng có thể gửi lại cùng nội dung.

## 6. Báo giá và CTA đặt lịch

CTA sau báo giá được xử lý ở Prompt/GenAI, không tạo một block CTA riêng.

Quy tắc:

- Có báo giá → AI có thể kết thúc bằng một lời mời đặt lịch mềm.
- Nếu lượt trước vừa hỏi đặt lịch và khách chưa trả lời ý đó → không hỏi lại.
- Khách nói chỉ tham khảo/chưa đặt → không bám tiếp CTA trong cùng mạch.
- Không tạo sequence/follow-up riêng chỉ để hỏi đặt lịch.

## 7. Duỗi kết hợp Uốn

Không tạo flow riêng cho dịch vụ này. Để Bot AI xử lý theo K02/K03:

```text
Duỗi kết hợp Uốn
= Uốn C hoặc Uốn xoăn
+ Duỗi chân tóc (áp dụng khi Uốn)
```

Nếu thiếu size/kiểu Uốn, Bot hỏi phần còn thiếu. Khi đủ dữ kiện, Bot báo giá Uốn + Duỗi chân tóc 400k–700k và áp ưu đãi theo K03.

## 8. Đặt lịch thủ công

- Tin đặt lịch vẫn đi qua router chung và Bot AI.
- Sau khi khách đồng ý đặt lịch, AI chỉ hỏi dịch vụ, thời gian hoặc SĐT còn thiếu.
- Khi đủ thông tin, AI nói đã note để nhân viên hỗ trợ.
- Không tạo thuộc tính booking nếu không cần, không webhook, không Data Table và không nhắc lịch tự động.
- Đổi/hủy lịch chuyển nhân viên kiểm tra thủ công; AI không tuyên bố đã xử lý xong.

## 9. Khách xin ảnh

- Yêu cầu xin ảnh/hình mẫu không kích hoạt image generator, image search hoặc card ảnh tự động.
- Bot AI chỉ gửi lời chờ; nhân viên gửi ảnh thủ công trong cùng hội thoại.
- Chỉ giữ trigger ảnh hướng dẫn size khi Bot AI thật sự hỏi `size tóc hiện tại`.
- Tắt trigger ảnh bảng giá. Mọi yêu cầu xin ảnh khác để nhân viên gửi thủ công.

## 10. Nghiệm thu

1. Chạy toàn bộ regression test trong `00_KHONG_TAI_LEN_KNOWLEDGE_BO_TEST_DEMO_REVIEW.md`.
2. Test riêng metadata `Đăng kí topic: Updates and promotions`: phải có **0 lượt GenAI và 0 sender**.
3. Test `Duỗi kết hợp Uốn`: không được dùng giá Duỗi toàn bộ.
4. Test báo giá: có CTA mềm nhưng không lặp CTA liên tục.
5. Với bài test gom tin, xác nhận chỉ một lượt AI và một sender trong logs.
6. Chờ thêm ít nhất một chu kỳ debounce để chắc chắn không có tin lặp muộn.
