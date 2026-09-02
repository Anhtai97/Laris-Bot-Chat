# BỘ REGRESSION TEST LARIS BOTAI

> Tệp dành cho quản trị viên, không tải vào Knowledge. Chạy lại trên Demo & Review và một tài khoản Messenger thử nghiệm sau mỗi lần sửa Prompt hoặc flow.

## Cách chấm

- Mỗi tin hoặc nhóm tin đã gom chỉ được tạo đúng một phản hồi.
- Nội dung trong cột mong đợi là ý bắt buộc, không cần khớp từng chữ trừ khi ghi rõ.
- Không đánh PASS chỉ vì Prompt nhìn đúng; phải xem Card Logs/Block Logs và hộp thư thực tế.
- Không dùng hội thoại khách thật để thử.

## P0 — Các lỗi và yêu cầu thực tế

| ID | Tin khách | Mong đợi | Cấm |
|---|---|---|---|
| P01 | `Cho mình xin giá cắt tóc!` | Một phản hồi báo cắt nữ 200k → ưu đãi riêng 150k, cắt mái riêng 50k; cuối câu có CTA đặt lịch mềm một lần | Phản hồi thứ hai; giảm cắt nữ thành 170k; CTA lặp |
| P02 | `mình đặt lịch cắt tóc` | `Dạ chị cho em xin số điện thoại và thời gian mình ghé để em note lịch lại nha.` hoặc câu tự nhiên cùng ý | Báo giá cắt; ưu đãi; nói đã tạo lịch tự động |
| P03 | `Mình đặt cắt lúc 15h chiều mai` | Chỉ hỏi số điện thoại còn thiếu | Hỏi lại dịch vụ/ngày/giờ; báo giá |
| P04 | `Mình đặt lịch cắt, số em 0901234567` | Chỉ hỏi thời gian còn thiếu | Hỏi lại SĐT; báo giá |
| P05 | `Dạ mình có hình khách đã cắt hông ạ` | Báo khách đợi một chút để nhân viên gửi hình thủ công | Tự tư vấn tình trạng tóc; tự gửi ảnh; báo giá; CTA |
| P06 | `Cho chị xin hình nhuộm uốn nâu đỏ` | Báo khách đợi để gửi hình thủ công | Tự tạo/tìm/gửi ảnh; chen giá hoặc CTA |
| P07 | `Cho em xin hình uốn C bên mình` | Báo khách đợi để gửi hình thủ công | Tự tạo/tìm/gửi ảnh; chen giá hoặc CTA |
| P08 | `Bạn là bot hay AI vậy?` | `Dạ chị đang nhắn với Laris Hair Studio ạ.` hoặc cùng ý | Tự nhận là bot/AI; tự giới thiệu là tư vấn viên online |
| P09 | `Bên mình đang có ưu đãi gì ạ?` | Đúng ý: giảm 15% cho khách đặt lịch trước + cắt tóc 200k còn 150k + hỏi khách đang quan tâm dịch vụ nào | Chỉ nói “áp dụng liên tục”; bảng giá dài; yêu cầu đánh giá |
| P10 | `Đăng kí topic: Updates and promotions` | **Không có phản hồi; 0 lượt GenAI; 0 sender** | Bất kỳ tin nhắn trả lời nào |

## P1 — Duỗi kết hợp Uốn

| ID | Tin khách | Mong đợi | Cấm |
|---|---|---|---|
| P11 | `Duỗi kết hợp uốn` | Hỏi: size tóc hiện tại + muốn Uốn C hay Uốn xoăn | Báo giá Duỗi toàn bộ 900k–1tr100k; tự chọn Uốn C |
| P12 | Sau P11 khách trả lời `Size L` | Chỉ hỏi khách muốn Uốn C hay Uốn xoăn | Hỏi lại size; dùng Duỗi size L 1tr100k |
| P13 | Sau P11 khách trả lời `Uốn C` | Chỉ hỏi size tóc hiện tại | Hỏi lại kiểu Uốn; báo giá khi chưa có size |
| P14 | `Size L, Uốn C kết hợp Duỗi` | Uốn C L 1tr100k + Duỗi chân tóc đi kèm 400k–700k; nếu dùng ưu đãi đặt lịch trước tổng còn 1tr275k–1tr530k; CTA mềm một lần | Duỗi toàn bộ size L; tổng sai; gọi Duỗi chân là dịch vụ độc lập |
| P15 | `Size L, Uốn xoăn kết hợp Duỗi` | Uốn xoăn L 1tr300k + Duỗi chân 400k–700k; sau giảm 15% tổng 1tr445k–1tr700k; CTA mềm một lần | Duỗi toàn bộ size L; tổng sai |
| P16 | `Duỗi chân tóc bao nhiêu em?` | Nói Duỗi chân tóc bên em chỉ áp dụng khi đi kèm Uốn C/Uốn xoăn, giá 400k–700k; hỏi kiểu Uốn + size nếu cần tư vấn tổng | Báo Duỗi chân độc lập 900k/1tr/1tr100k |
| P17 | `Cho chị xin giá Duỗi size L` | Hiểu là Duỗi toàn bộ size L 1tr100k, áp ưu đãi theo K03 và CTA mềm | Tự đổi thành Duỗi chân tóc |

## P2 — Giá, ngữ cảnh và CTA

| ID | Tin khách | Mong đợi | Cấm |
|---|---|---|---|
| P18 | `Giá cắt tóc sao ạ` | Báo đúng giá cắt + CTA đặt lịch mềm một lần | Không CTA; CTA hai lần |
| P19 | `Cho mình xin địa chỉ salon ạ` | Chỉ gửi địa chỉ | Giá, ưu đãi, lịch |
| P20 | `Tóc mình hư có duỗi được không ạ` | Nói ngắn stylist cần kiểm tra trực tiếp tình trạng tóc | Tự khẳng định chắc chắn; báo giá khi chưa hỏi |
| P21 | `Dạ cảm ơn em` | Một câu đáp ngắn tự nhiên | CTA hoặc mở chủ đề mới |
| P22 | `Giá cắt tóc và địa chỉ salon ở đâu ạ` | Một phản hồi trả đủ hai ý; sau phần giá có thể có một CTA đặt lịch mềm | Tách thành hai phản hồi; lặp CTA |
| P23 | `Nhuộm` rồi trong 15 giây gửi `Có loại nào em?` | Một phản hồi: Basic, VIP, cao cấp | Hai lượt GenAI; hai phản hồi; tự báo giá |
| P24 | `Size L` sau khi hỏi giá nhuộm | Báo đủ ba gói size L theo K02/K03, có nhãn ưu đãi đặt lịch trước; hỏi gói quan tâm và CTA nhẹ nếu phù hợp | Tự chọn VIP/Basic; hỏi lại size |
| P25 | Sau P24 khách nói `VIP nha e` | Báo VIP size L 1tr100k → 935k; CTA đặt lịch chỉ một lần nếu P24 chưa vừa hỏi CTA | Tự đổi gói; hỏi lại size; CTA lặp nếu vừa hỏi ở P24 |
| P26 | Sau một câu báo giá có CTA, khách hỏi `thuốc VIP là thuốc gì em?` | Chỉ giải thích VIP theo K02; không hỏi đặt lịch lại ngay | Lặp CTA đặt lịch |
| P27 | Khách nói `chị chỉ tham khảo thôi em` | Trả lời nhẹ, không tiếp tục CTA đặt lịch | Tiếp tục mời đặt lịch |
| P28 | `Ưu đãi áp dụng đến khi nào em?` | Nói chương trình áp dụng liên tục, không giới hạn theo tháng | Tự đặt ngày/tháng hết hạn |
| P29 | `Nhuộm nam giá bao nhiêu?` | Báo 500k–600k → 425k–510k nếu đặt lịch trước; CTA nhẹ | Quên nhãn ưu đãi; CTA lặp |

## P3 — Gom tin và flow

1. Tin tự do chỉ đi qua một router tổng quát: `Messenger Default → AI_NHAN_TIN`.
2. Metadata `Updates and promotions` bị chặn trước `ai_pending_text`; không tạo sequence, GenAI hoặc sender.
3. Các Keyword chung như giá, ưu đãi và đặt lịch phải tắt hoặc chỉ chuyển về `AI_NHAN_TIN`; không tự trả lời hay gọi GenAI riêng.
4. `AI_NHAN_TIN` REMOVE rồi ADD lại đúng một sequence debounce 15 giây.
5. Sau debounce chỉ gọi một block soạn câu trả lời và một sender.
6. Không còn webhook note lịch, n8n lịch hẹn hoặc flow nhắc lịch.
7. Yêu cầu xin ảnh chỉ tạo lời chờ; không gắn image generator/search.
8. CTA đặt lịch nằm trong câu do Bot AI soạn; không tạo sender/block CTA thứ hai.

## Biên bản chạy

| Ngày | Kênh/tài khoản test | P01–P29 | Kiểm tra cấu hình | Ghi chú |
|---|---|---|---|---|
| Chưa chạy | Chưa chỉ định | Chưa chạy | Chưa chạy | Cần triển khai bản mới lên Smax trước khi nghiệm thu production |
