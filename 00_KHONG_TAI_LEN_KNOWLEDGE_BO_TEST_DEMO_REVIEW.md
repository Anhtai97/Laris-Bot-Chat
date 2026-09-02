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
| P11 | `Duỗi kết hợp uốn` khi chưa có size | Hỏi: size tóc hiện tại + muốn Uốn C hay Uốn xoăn | Báo giá Duỗi toàn bộ 900k–1tr100k; tự chọn Uốn C |
| P12 | Sau P11 khách trả lời `Size L` | Chỉ hỏi khách muốn Uốn C hay Uốn xoăn | Hỏi lại size; dùng Duỗi size L 1tr100k |
| P13 | Sau P11 khách trả lời `Uốn C` | Chỉ hỏi size tóc hiện tại | Hỏi lại kiểu Uốn; báo giá khi chưa có size |
| P14 | `Size L, Uốn C kết hợp Duỗi` | Uốn C L 1tr100k + Duỗi chân tóc đi kèm 400k–700k; nếu dùng ưu đãi đặt lịch trước tổng còn 1tr275k–1tr530k; CTA mềm một lần | Duỗi toàn bộ size L; tổng sai; gọi Duỗi chân là dịch vụ độc lập |
| P15 | `Size L, Uốn xoăn kết hợp Duỗi` | Uốn xoăn L 1tr300k + Duỗi chân 400k–700k; sau giảm 15% tổng 1tr445k–1tr700k; CTA mềm một lần | Duỗi toàn bộ size L; tổng sai |
| P16 | `Duỗi chân tóc bao nhiêu em?` | Nói Duỗi chân tóc bên em chỉ áp dụng khi đi kèm Uốn C/Uốn xoăn, giá 400k–700k; hỏi kiểu Uốn + size nếu cần tư vấn tổng | Báo Duỗi chân độc lập 900k/1tr/1tr100k |
| P17 | `Cho chị xin giá Duỗi size L` | Hiểu là Duỗi toàn bộ size L 1tr100k, áp ưu đãi theo K03 và CTA mềm | Tự đổi thành Duỗi chân tóc |

## P2 — Size toàn cục qua nhiều dịch vụ

| ID | Chuỗi hội thoại | Mong đợi | Cấm |
|---|---|---|---|
| P18 | Hỏi Nhuộm → khách xác nhận `Size L` → sau đó hỏi `chị muốn làm thêm uốn kèm duỗi` | Giữ `laris_hair_size=L`; chỉ hỏi Uốn C hay Uốn xoăn | Hỏi lại size |
| P19 | Đã có `Size L` từ Nhuộm → `Cho chị giá Uốn C` | Dùng trực tiếp Uốn C size L | Hỏi size lại |
| P20 | Đã có `Size L` → `Cho chị giá Duỗi` | Dùng Duỗi toàn bộ size L | Hỏi size lại hoặc dùng size mặc định khác |
| P21 | Đã có `Size L` → `Balayage giá sao em?` | Dùng giá Balayage/Ombre size L nếu đúng bảng giá | Hỏi lại size |
| P22 | Đã có `Size L` → `phục hồi Milbon bao nhiêu?` | Dùng Milbon size L | Hỏi lại size |
| P23 | Đã có `Size L` → `tóc chị giờ size M nha` → hỏi Uốn C | State đổi sang M và dùng Uốn C size M | Tiếp tục dùng L |
| P24 | Đã có `Size L` → `chị hỏi cho em gái` → hỏi giá Uốn nhưng chưa biết size em gái | Không áp Size L của khách cho người khác; hỏi size em gái | Dùng L cũ |

## P3 — Giá, ngữ cảnh và CTA

| ID | Tin khách | Mong đợi | Cấm |
|---|---|---|---|
| P25 | `Giá cắt tóc sao ạ` | Báo đúng giá cắt + CTA đặt lịch mềm một lần | Không CTA; CTA hai lần |
| P26 | `Cho mình xin địa chỉ salon ạ` | Chỉ gửi địa chỉ | Giá, ưu đãi, lịch |
| P27 | `Tóc mình hư có duỗi được không ạ` | Nói ngắn stylist cần kiểm tra trực tiếp tình trạng tóc | Tự khẳng định chắc chắn; báo giá khi chưa hỏi |
| P28 | `Dạ cảm ơn em` | Một câu đáp ngắn tự nhiên | CTA hoặc mở chủ đề mới |
| P29 | `Giá cắt tóc và địa chỉ salon ở đâu ạ` | Một phản hồi trả đủ hai ý; sau phần giá có thể có một CTA đặt lịch mềm | Tách thành hai phản hồi; lặp CTA |
| P30 | `Nhuộm` rồi trong 15 giây gửi `Có loại nào em?` | Một phản hồi: Basic, VIP, cao cấp | Hai lượt GenAI; hai phản hồi; tự báo giá |
| P31 | `Size L` sau khi hỏi giá nhuộm | Báo đủ ba gói size L theo K02/K03, có nhãn ưu đãi đặt lịch trước; hỏi gói quan tâm và CTA nhẹ nếu phù hợp | Tự chọn VIP/Basic; hỏi lại size |
| P32 | Sau P31 khách nói `VIP nha e` | Báo VIP size L 1tr100k → 935k; CTA đặt lịch chỉ một lần nếu P31 chưa vừa hỏi CTA | Tự đổi gói; hỏi lại size; CTA lặp nếu vừa hỏi ở P31 |
| P33 | Sau một câu báo giá có CTA, khách hỏi `thuốc VIP là thuốc gì em?` | Chỉ giải thích VIP theo K02; không hỏi đặt lịch lại ngay | Lặp CTA đặt lịch |
| P34 | Khách nói `chị chỉ tham khảo thôi em` | Trả lời nhẹ, không tiếp tục CTA đặt lịch | Tiếp tục mời đặt lịch |
| P35 | `Ưu đãi áp dụng đến khi nào em?` | Nói chương trình áp dụng liên tục, không giới hạn theo tháng | Tự đặt ngày/tháng hết hạn |
| P36 | `Nhuộm nam giá bao nhiêu?` | Báo 500k–600k → 425k–510k nếu đặt lịch trước; CTA nhẹ | Quên nhãn ưu đãi; CTA lặp |

## P4 — Gom tin, debounce và chống sender lặp

### P37 — Case lỗi thực tế

Gửi từ tài khoản test:

```text
Size L
```

sau 1–3 giây gửi:

```text
uốn xoăn
```

Chờ hơn 15 giây.

PASS khi:

- `ai_processing_text` chứa đủ cả `Size L` và `uốn xoăn`.
- Sequence Logs chỉ có một Step cuối gọi `AI_TRA_LOI` sau tin cuối.
- Khách chỉ nhận một phản hồi.
- Phản hồi dùng Uốn xoăn size L.

FAIL khi:

- Hai lần `AI_TRA_LOI` chạy.
- Một `AI_TRA_LOI` nhưng hai Messenger Text cùng gửi `ai_answer`.
- Có phản hồi ngay sau tin `Size L` trước khi hết debounce.

### Checklist cấu hình

1. Tin tự do chỉ đi qua một router tổng quát: `Messenger Default → AI_NHAN_TIN`.
2. Metadata `Updates and promotions` bị chặn trước `ai_pending_text`; không tạo sequence, GenAI hoặc sender.
3. Keyword chung như giá, ưu đãi, size, Uốn, Nhuộm và đặt lịch phải tắt hoặc chỉ chuyển về `AI_NHAN_TIN`; không tự trả lời hay gọi GenAI riêng.
4. `AI_NHAN_TIN` phải nối `last_content_by_user` vào `ai_pending_text`, sau đó REMOVE rồi ADD lại đúng một `AI_GOM_TIN_FB_DEBOUNCE`.
5. Sequence chỉ có một Step `+15s → AI_TRA_LOI`.
6. Đầu `AI_TRA_LOI` phải snapshot `ai_pending_text → ai_processing_text`, rồi xóa `ai_pending_text`.
7. Bot AI đọc `CURRENT_BATCH={{ai_processing_text}}`, không chỉ đọc `last_content_by_user`.
8. Chỉ một Messenger Text/Instagram Text được gửi `ai_answer`.
9. Không còn webhook note lịch, n8n lịch hẹn hoặc flow nhắc lịch.
10. Yêu cầu xin ảnh chỉ tạo lời chờ; không gắn image generator/search.

## Biên bản chạy

| Ngày | Kênh/tài khoản test | P01–P37 | Kiểm tra cấu hình | Ghi chú |
|---|---|---|---|---|
| Chưa chạy | Chưa chỉ định | Chưa chạy | Chưa chạy | Cần triển khai bản mới lên Smax trước khi nghiệm thu production |
