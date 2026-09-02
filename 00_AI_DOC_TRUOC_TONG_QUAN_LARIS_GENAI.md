# ĐỌC TRƯỚC KHI SỬA BỘ LARIS GENAI

## Mục tiêu

Bot trả lời như cách salon nhắn thật: ngắn, tự nhiên, đúng câu khách vừa hỏi và không tự mở thêm chủ đề.

## Kiến trúc đang dùng

```text
Messenger / Instagram
→ một router tổng quát
→ AI_NHAN_TIN
→ debounce 15 giây bằng REMOVE → ADD sequence
→ AI_TRA_LOI
   → trích xuất Size + gói nhuộm
   → Bot AI soạn đúng một phản hồi
→ một sender đúng với từng kênh
```

Không còn flow note lịch, đồng bộ lịch hoặc nhắc lịch qua n8n. Yêu cầu đặt lịch chỉ được AI thu phần còn thiếu để nhân viên note thủ công.

## File dùng trong Smax

- `Prompt Chính.Md`: hành vi và văn phong.
- `K01`: thông tin salon.
- `K02`: dịch vụ, size và giá gốc.
- `K03`: ưu đãi và giá cuối.
- `K04`: luật hội thoại tối giản; chỉ dùng để tham chiếu nếu luật đã có trong Prompt.
- `K05`: tư vấn màu và an toàn.
- `SMAX_EMBEDDED_PROMPT_STATE_2026-08-11.md`: chỉ nhớ Size + gói nhuộm.
- `SMAX_EMBEDDED_PROMPT_BOT_AI_2026-08-11.md`: wrapper truyền đúng tin hiện tại vào Bot AI.

Không tải tài liệu quản trị, bộ test hoặc file backup vào Knowledge.

## Năm nguyên tắc bắt buộc

1. Không tự giới thiệu và không nhận mình là bot/AI hoặc nhân viên online.
2. Một tin hoặc một batch chỉ tạo một phản hồi.
3. Khách hỏi gì trả lời đúng phần đó; không tự thêm giá, ưu đãi hay CTA.
4. Khách xin ảnh thì chỉ báo chờ để nhân viên gửi thủ công.
5. Đặt lịch chỉ hỏi dịch vụ, thời gian hoặc SĐT còn thiếu; không có automation lịch.

## Dữ liệu không được tự ý sửa

K01/K02/K03/K05 là nguồn dữ liệu salon. Không đổi giá, dịch vụ, ưu đãi hoặc thông tin salon khi yêu cầu chỉ liên quan văn phong/flow.

## Kiểm thử

Chạy `00_KHONG_TAI_LEN_KNOWLEDGE_BO_TEST_DEMO_REVIEW.md`. Lỗi lặp phải được xác minh ở Card Logs/Block Logs và hộp thư thật; Prompt không thể chặn hai flow song song.
