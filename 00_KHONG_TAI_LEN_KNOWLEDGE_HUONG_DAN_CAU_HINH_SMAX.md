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
- Các Keyword chung như “Cho mình xin giá cắt tóc”, “Laris đang có ưu đãi gì” hoặc “Mình muốn đặt lịch làm tóc” phải tắt hoặc chỉ chuyển về `AI_NHAN_TIN`. Không để Keyword tự gọi GenAI hay tự gửi câu trả lời.
- Click To Message chỉ xử lý sự kiện quảng cáo; không đưa nguyên card quảng cáo vào AI.

## 2. Gom tin và chống trả lời hai lần

Trong `AI_NHAN_TIN`:

1. Nối tin khách mới vào `ai_pending_text`.
2. REMOVE sequence debounce đang chờ.
3. ADD lại đúng một sequence với thời gian 15 giây.

Sau thời gian chờ:

1. Chuyển batch sang `ai_processing_text`.
2. Xóa pending.
3. Gọi `AI_TRA_LOI` đúng một lần.
4. Chỉ một Bot AI được phép tạo nội dung gửi khách.
5. Nối thẳng sang một sender cuối của kênh; không cần GenAI đóng gói trung gian.

Nếu vẫn lặp, xem Card Logs/Block Logs cho cùng message ID. Hai lượt chạy nghĩa là còn router/Keyword/sequence cạnh tranh; không sửa Prompt để che lỗi này.

## 3. Cấu hình AI_TRA_LOI

- State extractor chỉ lưu `laris_hair_size` và `laris_dye_package`.
- Parse Content chỉ ánh xạ hai thuộc tính trên.
- Bot AI nhận CURRENT_MESSAGE, CURRENT_BATCH, STATE_RESULT, size và gói.
- Gắn K01, K02, K03 và K05. Không gắn tài liệu quản trị hoặc file test.
- Bot AI chỉ xuất một phản hồi tự nhiên.

## 4. Sender

- Facebook: dùng một card Messenger Text với nội dung `ai_answer` làm sender duy nhất.
- Instagram: chỉ một Instagram Text.
- Không dùng n8n để format, gửi, note lịch hoặc nhắc lịch.
- Bỏ block `AI_JSON_GUI`, JsonAPI gửi nội dung, JsonAPI webhook lịch, sender cũ và mọi đường gửi dự phòng có thể gửi lại cùng nội dung.

## 5. Đặt lịch thủ công

- Tin đặt lịch vẫn đi qua router chung và Bot AI.
- AI chỉ hỏi dịch vụ, thời gian hoặc SĐT còn thiếu.
- Khi đủ thông tin, AI nói đã note để nhân viên hỗ trợ.
- Không tạo thuộc tính booking, không Parse Content lịch, không webhook, không Data Table và không nhắc lịch tự động.
- Đổi/hủy lịch được chuyển nhân viên kiểm tra thủ công; AI không tuyên bố đã xử lý xong.

## 6. Khách xin ảnh

- Yêu cầu xin ảnh/hình mẫu không kích hoạt image generator, image search hoặc card ảnh tự động.
- Bot AI chỉ gửi lời chờ; nhân viên gửi ảnh thủ công trong cùng hội thoại.
- Chỉ giữ trigger ảnh hướng dẫn size khi Bot AI thật sự hỏi “size tóc hiện tại”.
- Tắt trigger ảnh bảng giá. Mọi yêu cầu xin ảnh khác đều để nhân viên gửi thủ công.

## 7. Nghiệm thu

1. Chạy P01–P15 trong bộ regression test.
2. Với P01 và P14, xác nhận chỉ một lượt AI và một sender trong logs.
3. Chờ thêm ít nhất một chu kỳ debounce để chắc chắn không có tin lặp muộn.
4. Chỉ đánh PASS production sau khi thấy kết quả trong hộp thư thử nghiệm.
