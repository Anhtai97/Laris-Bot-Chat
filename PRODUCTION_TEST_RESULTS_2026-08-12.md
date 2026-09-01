# Laris — Production test 2026-08-12

Tài khoản test do chủ tài khoản chỉ định: Facebook `Phương Bùi`. Không gửi tin cho hội thoại khách khác. Không sửa/import hai workflow n8n.

## Thay đổi trong lượt kiểm thử

- Đồng bộ Prompt toàn cục bằng bản tối giản hiện tại; thêm cổng bắt buộc cho câu hỏi màu hợp da và batch chỉ hỏi loại/gói nhuộm.
- `AI_GUI_TRA_LOI`: xác minh sender cũ `Messenger Text {{noidung}}` ở trạng thái tắt; sender chính là `JsonAPI`; webhook booking vẫn bật. Tất cả thay đổi đều là công tắc có thể phục hồi, không xóa card.
- Phát hiện một tin đến trong lúc lượt trước chưa kết thúc từng bị dồn sang lượt kế tiếp. Sau khi luồng rảnh, các lượt tiếp theo chỉ có một phản hồi; T9 xác minh hai tin trong cửa sổ gom tạo đúng một phản hồi.

## Kết quả

| Test | Đầu vào rút gọn | Phản hồi production thực tế | Kết quả |
|---|---|---|---|
| T1 | Hỏi ưu đãi | “Dạ hiện tại bên em đang có chương trình giảm 15% gồm giảm 10% dịch vụ và thêm 5% khi mình để lại đánh giá ạ. Riêng cắt tóc nữ đang ưu đãi từ 200k còn 150k nha chị.” | PASS |
| T2 | Size L → hỏi Duỗi hơi nước | “Dạ duỗi hơi nước size L giá gốc 1tr100k, sau giảm 15% còn 935k ạ.” | PASS sau hồi quy; không hỏi lại size |
| T3 | Duỗi hơi nước size L | “Dạ duỗi hơi nước size L giá gốc 1tr100k, sau giảm 15% còn 935k ạ.” | PASS |
| T4 | Có cắt layer không | “Dạ bên em có cắt layer ạ.” | PASS |
| T5 | Ba gói khác nhau sao | “Dạ ba gói nhuộm khác nhau chủ yếu ở dòng thuốc, độ dưỡng, độ mềm bóng và khả năng giữ màu ạ: Basic dùng thuốc Hàn/Trung, VIP dùng thuốc Nhật Luminous, còn cao cấp là L’Oréal Pháp hoặc Milbon Nhật.” | PASS |
| T6 | Giá nhuộm → Size L | “Dạ nhuộm nữ size L bên em có gói Basic giá gốc 1tr, sau giảm 15% còn 850k; VIP giá gốc 1tr100k, sau giảm còn 935k; cao cấp giá gốc 1tr300k, sau giảm còn 1tr105k ạ. Mình muốn chọn gói nào để em báo đúng giá tiếp nha chị?” | PASS sau sửa state PACKAGE=UNKNOWN và hồi quy |
| T7 | VIP nha | “Dạ nhuộm nữ gói VIP size L có giá gốc 1tr100k, sau giảm 15% còn 935k ạ.” | PASS |
| T8 | Dạ cảm ơn em | “Dạ không có gì ạ.” | PASS |
| T9 | `Nhuộm.` + `Có loại nào em?` trong 1 giây | “Dạ bên em có 3 gói nhuộm là Basic, VIP và cao cấp ạ.” | PASS: đúng một phản hồi, ba gói một lần, không giá/size/CTA |
| T10 | Hỏi địa chỉ | “Dạ Laris ở 39 Trần Nhân Tôn, phường An Đông (Quận 5 cũ), TP. HCM ạ.” | PASS |
| T11 | Tổng cắt nữ + Duỗi M | “Dạ cắt nữ ưu đãi còn 150k, duỗi hơi nước size M sau giảm 15% còn 850k nên tổng hai dịch vụ là 1tr.” | PASS |
| T12 | Đặt cắt 14h ngày mai | “Dạ em đã ghi lịch cắt cho chị lúc 14h ngày mai ạ.” | PASS: chỉ lúc này mới vào booking; hồ sơ test đã có sẵn tên/SĐT nên không còn trường bắt buộc để hỏi |
| T13 | Da hơi ngăm hợp màu nào | “Dạ phần chọn màu hợp còn tùy nền tóc, tình trạng tóc và tổng thể của chị nên stylist cần xem trực tiếp mới tư vấn chính xác ạ.” | PASS sau sửa và hồi quy |
| T14 | Hỏi hotline sau lịch sử giá/ưu đãi | “Dạ hotline Laris là 08.5555.9997 ạ.” | PASS |

## Production

- Thay đổi Prompt/Flow nêu trên đang ở production và đã được thử trực tiếp qua Messenger.
- 14/14 test PASS. T12 không hỏi thêm vì hồ sơ test Phương Bùi đã có sẵn tên/SĐT; ngày, giờ và dịch vụ có ngay trong tin hiện tại.
- Tài khoản Phương Bùi đã có tên/SĐT lưu sẵn trong Smax; đây là nguyên nhân T12 không cần hỏi thêm. Không gửi thêm tin để thay đổi/hủy lịch và không chỉnh workflow n8n.

