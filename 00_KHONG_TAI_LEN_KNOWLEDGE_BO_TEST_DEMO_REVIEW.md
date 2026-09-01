# BỘ REGRESSION TEST LARIS BOTAI

> Tệp dành cho quản trị viên, không tải vào Knowledge. Chạy lại trên Demo & Review và một tài khoản Messenger thử nghiệm sau mỗi lần sửa Prompt hoặc flow.

## Cách chấm

- Mỗi tin hoặc nhóm tin đã gom chỉ được tạo đúng một phản hồi.
- Nội dung trong cột mong đợi là ý bắt buộc, không cần khớp từng chữ trừ khi ghi rõ.
- Không đánh PASS chỉ vì Prompt nhìn đúng; phải xem Card Logs/Block Logs và hộp thư thực tế.
- Không dùng hội thoại khách thật để thử.

## P0 — Các lỗi từ hội thoại thực tế

| ID | Tin khách | Mong đợi | Cấm |
|---|---|---|---|
| P01 | `Cho mình xin giá cắt tóc!` | Một phản hồi báo đúng giá cắt nữ theo K02/K03 | Phản hồi thứ hai; CTA đặt lịch; hỏi ngày giờ |
| P02 | `mình đặt lịch cắt tóc` | `Dạ chị cho em xin số điện thoại và thời gian mình ghé để em note lịch lại nha.` hoặc câu tự nhiên cùng ý | Báo giá cắt; ưu đãi; CTA khác; nói đã tạo lịch tự động |
| P03 | `Mình đặt cắt lúc 15h chiều mai` | Chỉ hỏi số điện thoại còn thiếu | Hỏi lại dịch vụ/ngày/giờ; báo giá |
| P04 | `Mình đặt lịch cắt, số em 0901234567` | Chỉ hỏi thời gian còn thiếu | Hỏi lại SĐT; báo giá |
| P05 | `Dạ mình có hình khách đã cắt hông ạ` | `Dạ chị đợi em chút, em gửi hình cho mình ngay ạ.` hoặc câu tự nhiên cùng ý | Tự tư vấn tình trạng tóc; tự gửi ảnh; báo giá; hỏi lịch |
| P06 | `Cho chị xin hình nhuộm uốn nâu đỏ` | Báo khách đợi một chút để nhân viên gửi hình thủ công | Tự tạo/tìm/gửi ảnh; chen giá hoặc CTA |
| P07 | `Cho em xin hình uốn C bên mình` | Báo khách đợi một chút để nhân viên gửi hình thủ công | Tự tạo/tìm/gửi ảnh; chen giá hoặc CTA |
| P08 | `Bạn là bot hay AI vậy?` | Trả lời dưới danh nghĩa Laris, ví dụ `Dạ chị đang nhắn với Laris Hair Studio ạ.` | Tự nhận là bot/AI; tự giới thiệu là tư vấn viên online |

## P1 — Đúng ngữ cảnh và văn phong

| ID | Tin khách | Mong đợi | Cấm |
|---|---|---|---|
| P09 | `Giá cắt tóc sao ạ` | Chỉ báo giá cắt nữ và ưu đãi đang áp dụng | `Mình dự định khi nào cắt`; mời đặt lịch |
| P10 | `Cho mình xin địa chỉ salon ạ` | Chỉ gửi địa chỉ | Giá, ưu đãi, lịch |
| P11 | `Tóc mình hư có duỗi được không ạ` | Nói ngắn rằng stylist cần kiểm tra trực tiếp tình trạng tóc | Tự khẳng định chắc chắn; báo giá khi chưa hỏi |
| P12 | `Dạ cảm ơn em` | Một câu đáp ngắn tự nhiên | CTA hoặc mở chủ đề mới |
| P13 | `Giá cắt tóc và địa chỉ salon ở đâu ạ` | Một phản hồi trả đủ đúng hai ý, mỗi ý một lần | Tách thành hai phản hồi; hỏi lịch |
| P14 | `Nhuộm` rồi trong 15 giây gửi `Có loại nào em?` | Một phản hồi: có ba gói Basic, VIP, cao cấp | Hai lượt GenAI; hai phản hồi |
| P15 | `Size L` sau khi hỏi giá nhuộm | Báo đủ ba gói size L theo K02/K03 và chỉ hỏi gói còn thiếu | Tự chọn VIP/Basic; hỏi lại size |
| P16 | `Bên mình đang có ưu đãi gì ạ?` | Chỉ nói khách đặt lịch trước được giảm trực tiếp 15% cho dịch vụ đủ điều kiện | Tách ưu đãi thành nhiều phần; yêu cầu đánh giá; chen bảng giá hoặc CTA |
| P17 | `Ưu đãi áp dụng đến khi nào em?` | Nói chương trình áp dụng liên tục, không giới hạn theo tháng | Tự đặt ngày/tháng hết hạn; nói chỉ áp dụng trong tháng hiện tại |
| P18 | `Nhuộm nam giá bao nhiêu?` | Báo giá gốc 500k–600k và giá giảm 15% khi đặt lịch trước là 425k–510k | Áp giá giảm không kèm điều kiện đặt lịch trước; yêu cầu đánh giá |

## P2 — Kiểm tra cấu hình, không hỏi model

1. Tin tự do chỉ đi qua một router tổng quát: `Messenger Default → AI_NHAN_TIN`.
2. Các Keyword câu hỏi chung như giá cắt, ưu đãi và đặt lịch phải tắt hoặc chỉ chuyển về `AI_NHAN_TIN`; không được tự trả lời hay gọi GenAI riêng.
3. `AI_NHAN_TIN` phải REMOVE rồi ADD lại đúng một sequence debounce 15 giây.
4. Sau debounce chỉ gọi một block soạn câu trả lời và một card gửi khách.
5. Card gửi cũ, GenAI trực tiếp, nhánh `Other` trực tiếp và mọi sender thứ hai phải tắt.
6. Không còn webhook note lịch, workflow n8n lịch hẹn hoặc flow nhắc lịch.
7. Yêu cầu xin ảnh chỉ tạo lời chờ; không gắn card ảnh, công cụ tạo ảnh hay tìm ảnh vào nhánh này. Tắt trigger ảnh bảng giá; chỉ trigger hướng dẫn size được giữ.

## Biên bản chạy

| Ngày | Kênh/tài khoản test | P01–P18 | Kiểm tra cấu hình | Ghi chú |
|---|---|---|---|---|
| Chưa chạy | Chưa chỉ định | Chưa chạy | Chưa chạy | Cần triển khai bản mới lên Smax trước khi nghiệm thu production |
