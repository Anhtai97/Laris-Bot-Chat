# LARIS — ƯU ĐÃI ĐANG ÁP DỤNG

## Hiệu lực

- Tên: Chương trình giảm giá 15%.
- Trạng thái: **ĐANG ÁP DỤNG LIÊN TỤC**.
- Thời hạn vận hành: chương trình tiếp tục áp dụng cho đến khi quản trị viên cập nhật chính Knowledge này và ghi rõ `ĐÃ NGỪNG`, hoặc thay bằng một chương trình khác.
- Áp dụng cho mọi dịch vụ, trừ cắt nữ có ưu đãi riêng và cắt mái riêng đang báo giá niêm yết 50k.
- Cắt nữ có giá gốc 200k và ưu đãi riêng cố định còn 150k; cắt mái riêng có giá cố định 50k. Hai mức này không dùng công thức giảm 15%.

Không có ngày bắt đầu hoặc ngày kết thúc cố định. Không tự tắt ưu đãi khi sang tháng hoặc sang năm mới; không yêu cầu quản trị viên sửa Knowledge mỗi tháng. Ngày/tháng trong lịch sử hội thoại và ví dụ cũ không được dùng để suy ra chương trình đã hết hiệu lực.

Chỉ ngừng áp dụng khi trạng thái trong Knowledge này được quản trị viên chủ động đổi sang `ĐÃ NGỪNG`, hoặc nội dung được thay bằng chương trình mới có quy tắc khác. Khi trạng thái vẫn là `ĐANG ÁP DỤNG LIÊN TỤC`, mọi dịch vụ đủ điều kiện luôn dùng giá sau giảm 15%.

### Khi khách hỏi ưu đãi đến khi nào

- Lấy tháng hiện tại duy nhất từ `TODAY_VN` do Smax truyền vào.
- Nếu `TODAY_VN` hợp lệ, trả lời theo tháng của ngày đó: `Dạ ưu đãi 15% hiện đang áp dụng đến hết tháng [tháng từ TODAY_VN] ạ. Chương trình bên em được cập nhật theo từng tháng nha chị.`
- Câu `đến hết tháng` chỉ là cách thông báo theo tháng hiện tại; không có nghĩa chương trình tự chấm dứt vĩnh viễn vào cuối tháng. Khi sang tháng mới mà trạng thái K03 vẫn đang áp dụng, dùng tháng mới từ `TODAY_VN`.
- Nếu `TODAY_VN` thiếu, rỗng hoặc không hợp lệ, chỉ nói chương trình 15% hiện đang áp dụng; không đoán tháng, không lấy tháng từ lịch sử và không nêu một năm cụ thể.
- Không nói `chỉ còn hôm nay`, `sắp hết hoàn toàn` hoặc `cơ hội cuối` nếu không có dữ liệu thật. Không nhắc Knowledge, Prompt hay cấu hình nội bộ với khách.

## Nội dung chương trình

- Giảm 10% dịch vụ.
- Thêm 5% đánh giá.
- Tổng giảm giá 15%.

## Cách dùng theo lịch sử hội thoại

- Lần đầu khách hỏi giá và lịch sử chưa có chương trình: nói chương trình giảm 15% đang áp dụng cho dịch vụ đủ điều kiện.
- Nếu chương trình đã được nói: không lặp công thức 10% + 5%.
- Mọi phản hồi có báo giá dịch vụ đủ điều kiện đều phải có đủ giá gốc, tỷ lệ giảm 15% và giá cuối cùng sau giảm, kể cả khi chương trình đã được giải thích ở lượt trước.
- Mỗi phản hồi có con số giá đã giảm phải nói rõ `giảm 15%` ít nhất một lần. Việc đã giải thích chương trình ở lượt trước chỉ cho phép bỏ công thức `10% + 5%`, không cho phép bỏ tỷ lệ `15%`.
- Dùng cách viết rõ nghĩa `giá gốc [X], sau giảm còn [Y]`; không chỉ viết `[X] còn [Y]` vì khách có thể không hiểu đó là giá trước và sau giảm.
- Không dùng lịch sử để bỏ giá cuối cùng và không dùng ngày/tháng cũ để vô hiệu hóa chương trình.
- Khi chưa biết size/gói/biến thể nhưng K02 đã có khoảng giá, báo cả khoảng giá gốc và khoảng giá sau giảm 15%, sau đó chỉ hỏi dữ liệu còn thiếu.
- Giá dạng khoảng phải giảm chính xác cả hai đầu; không dùng một giá đại diện.
- Câu hỏi không liên quan giá/ưu đãi: không nhắc chương trình.
- Không thêm lời mời báo giá nếu giá đã được báo hoặc khách đang hỏi ý khác.

## Ngoại lệ cắt nữ và cắt mái riêng

- Cắt nữ: giá gốc 200k, ưu đãi riêng cố định còn 150k, đã gồm cắt/chỉnh mái.
- Không áp dụng 15% cho 200k, không báo 170k, không giảm tiếp 15% từ 150k thành 127.500đ và không gọi 150k là kết quả của chương trình giảm 15%.
- Cắt layer/cắt form/cắt kiểu nữ vẫn tính theo cắt nữ: giá gốc 200k, ưu đãi riêng còn 150k.
- Khi khách hỏi chung giá cắt tóc, báo thêm cắt mái riêng 50k để khách biết; ưu đãi 150k là của cắt nữ.
- Cắt mái riêng báo cố định 50k, không áp dụng 15%, không báo 42.500đ và không tự đổi thành giá giảm lẻ trong luồng hỏi giá cắt tóc hoặc cắt mái.

Câu chuẩn khi hỏi cắt tóc chung:

“Dạ cắt tóc nữ bên em giá gốc 200k, hiện có ưu đãi riêng còn 150k, đã bao gồm cắt/chỉnh mái; nếu mình chỉ cắt mái riêng thì 50k ạ.”

Các câu `cắt mái`, `cắt tóc mái`, `tỉa/chỉnh mái`, `cắt mái xéo/bay` chỉ báo cắt mái riêng 50k; không dùng hai mẫu cắt nữ ở trên.

## Cách tính khi khách làm nhiều dịch vụ

Không có giá combo. Khi khách hỏi nhiều dịch vụ, phải tính từng dịch vụ riêng rồi mới cộng tổng sau ưu đãi.

Quy trình bắt buộc:

1. Tách từng dịch vụ khách nói.
2. Với cắt nữ/cắt layer: dùng giá gốc 200k → ưu đãi riêng cố định còn 150k; không áp dụng 15%.
3. Với dịch vụ áp dụng 15%: lấy giá gốc từng dịch vụ rồi tra đúng giá sau giảm trong bảng quy đổi.
4. Cộng tổng bằng các giá sau ưu đãi của từng dịch vụ.
5. Không giảm thêm 15% trên tổng và không áp dụng 15% lần hai cho giá cuối của từng dịch vụ.
6. Riêng nhuộm, chỉ xem gói là đã có khi khách tự chọn rõ Basic, VIP hoặc cao cấp. Gói xuất hiện trong câu bot, bảng giá hay ví dụ không phải lựa chọn của khách.
7. Nếu còn thiếu gói/kiểu/dòng của bất kỳ dịch vụ nào, không được tự chọn giá, không được lấy giá thấp nhất và không báo tổng cuối.
8. Khi khách hỏi tổng mà nhuộm chưa có gói, báo rõ phần dịch vụ đã đủ nếu cần và hỏi khách chọn gói; tuyệt đối không lấy Basic để tạo `tổng tạm tính`.

Ví dụ đúng khi đã đủ dữ liệu size L:

- Cắt layer/cắt nữ: 200k → 150k.
- Nhuộm Basic size L: 1tr → 850k.
- Uốn xoăn lơi size L: 1tr300k → 1tr105k.
- Tổng sau ưu đãi: 150k + 850k + 1tr105k = 2tr105k.

Ví dụ sai nghiêm trọng cần tránh: nói “combo cắt + nhuộm Basic size L là 1tr” hoặc “giá gốc 1tr + 1tr300k” khi khách có cả cắt. Đây là bỏ sót giá cắt và có thể làm salon lỗ.

## Bảng quy đổi

| Giá gốc | Sau giảm 15% |
|---:|---:|
| 35k | 29.750đ |
| 100k | 85k |
| 150k | 127.500đ |
| 200k | 170k |
| 300k | 255k |
| 400k | 340k |
| 500k | 425k |
| 600k | 510k |
| 700k | 595k |
| 800k | 680k |
| 900k | 765k |
| 1tr | 850k |
| 1tr100k | 935k |
| 1tr200k | 1tr020k |
| 1tr300k | 1tr105k |
| 1tr500k | 1tr275k |
| 2tr | 1tr700k |
| 2tr500k | 2tr125k |
| 3tr | 2tr550k |
| 4tr | 3tr400k |
| 4tr500k | 3tr825k |

Lưu ý: dòng 150k → 127.500đ và 200k → 170k chỉ dùng cho dịch vụ đủ điều kiện có đúng giá gốc tương ứng, ví dụ đầu cao của giá Gội hoặc Uốn/duỗi mái. Tuyệt đối không dùng hai dòng này cho cắt nữ: cắt nữ luôn 200k → ưu đãi riêng 150k. Cắt mái riêng báo cố định 50k và không nằm trong bảng quy đổi.

Nối tóc lông vũ 9D giá gốc 35k/sợi, giá sau giảm 15% là 29.750đ/sợi. Chỉ tính tổng nối tóc khi biết số sợi khách dự định nối; nếu chưa biết số sợi thì không tự đoán tổng tiền.

Với giá dạng khoảng, giảm cả hai đầu khoảng. Ví dụ:

- 500k–600k → 425k–510k.
- 400k–700k → 340k–595k.
- 1tr200k–2tr → 1tr020k–1tr700k.

## Mẫu quan trọng

### Đã biết size nhưng chưa biết gói nhuộm

Khi khách chỉ cung cấp size mà chưa chọn gói, phải đưa đủ ba lựa chọn tại chính size đó:

- Size S: Basic 800k → 680k; VIP 900k → 765k; cao cấp 1tr100k → 935k.
- Size M: Basic 900k → 765k; VIP 1tr → 850k; cao cấp 1tr200k → 1tr020k.
- Size L: Basic 1tr → 850k; VIP 1tr100k → 935k; cao cấp 1tr300k → 1tr105k.

Sau khi liệt kê, chỉ hỏi khách muốn chọn gói nào. Không tự chọn VIP, Basic hoặc cao cấp; không hỏi lại size; không lặp công thức 10% + 5% nếu đã nói trước đó. Tuy nhiên vẫn phải nói rõ các giá đang `giảm 15%`.

Mẫu khi khách chỉ trả lời `Size L`:

“Dạ size tóc mình đang là size L ạ. Bên em có 3 gói size L đang giảm 15%: Basic giá gốc 1tr, sau giảm còn 850k; VIP giá gốc 1tr100k, sau giảm còn 935k; cao cấp giá gốc 1tr300k, sau giảm còn 1tr105k. Mình muốn chọn gói nào để em tư vấn tiếp nha chị?”

Khách hỏi chung giá nhuộm khi chưa biết gói và size:

“Dạ nhuộm bên em có gói Basic giá gốc 800k–1tr, sau giảm 15% còn 680k–850k; VIP giá gốc 900k–1tr100k, sau giảm còn 765k–935k; cao cấp giá gốc 1tr100k–1tr300k, sau giảm còn 935k–1tr105k ạ. Chị cho em biết mình quan tâm gói nào và size tóc hiện tại là S, M hay L để em báo đúng giá nha.”

Nếu công thức `10% dịch vụ + 5% đánh giá` chưa được giải thích trong lịch sử, có thể nói thêm một lần. Nếu đã giải thích, không lặp công thức nhưng vẫn phải giữ đủ giá gốc, cụm `giảm 15%` và giá cuối trong mọi lượt báo giá.

Đủ gói/size:

“Dạ nhuộm nữ gói VIP size L có giá gốc 1tr100k, sau giảm 15% còn 935k ạ. Nếu mình muốn đặt lịch, em hỗ trợ ghi nhận thông tin nha.”

Khách nam hỏi nhuộm:

“Dạ nhuộm nam giá 500k–600k, giá sau khi giảm 15% còn 425k–510k ạ. Chương trình gồm 10% dịch vụ + 5% đánh giá nha anh.”

## Điều cấm

- Không quên giá sau giảm khi báo giá cụ thể.
- Không quên khoảng giá sau giảm khi chưa biết size/gói/biến thể nhưng K02 đã có khoảng giá.
- Không báo giá đã giảm mà thiếu cụm `giảm 15%`, `giá gốc` hoặc `sau giảm còn`.
- Không tính tổng nhuộm khi gói chỉ do bot suy ra hoặc tự chọn.
- Không lặp công thức 10% + 5% ở nhiều lượt; vẫn phải nêu tỷ lệ 15% và giá cuối trong mọi phản hồi có báo giá.
- Không nhắc ưu đãi trong câu trả lời không liên quan giá.
- Không cộng ưu đãi khác hoặc giảm thêm.
- Không tự đặt ngày hết hạn hoặc ngừng chương trình khi trạng thái K03 vẫn là `ĐANG ÁP DỤNG LIÊN TỤC`.
- Không báo cắt mái riêng theo giá giảm lẻ nếu khách hỏi cắt tóc/cắt mái; giá cắt mái riêng đang dùng là 50k.

Khi quản trị viên ngừng chương trình hoặc có chương trình mới, cập nhật trạng thái/nội dung của chính Knowledge này; không giữ nhiều Knowledge ưu đãi cùng lúc.
