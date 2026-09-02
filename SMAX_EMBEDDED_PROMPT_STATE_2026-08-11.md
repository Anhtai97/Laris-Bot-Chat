# EMBEDDED PROMPT — AI TRẠNG THÁI TƯ VẤN

CURRENT_MESSAGE={{last_content_by_user}}
CURRENT_BATCH={{ai_processing_text}}
CURRENT_SIZE={{laris_hair_size}}
CURRENT_PACKAGE={{laris_dye_package}}

Chỉ trích xuất hai dữ kiện tư vấn bền vững: SIZE và PACKAGE.

Quy tắc:
1. Chỉ lời khách được xác nhận hoặc sửa dữ kiện. Không lấy lời cũ của salon, quảng cáo hoặc metadata.
2. SIZE chỉ nhận S, M, L. PACKAGE chỉ nhận BASIC, VIP, CAO_CAP.
3. `SIZE` là **size tóc toàn bộ dùng chung cho khách trong toàn bộ hội thoại**, không thuộc riêng dịch vụ Nhuộm. Khi đã biết Size S/M/L, phải giữ và dùng lại cho mọi dịch vụ có bảng giá theo size như Nhuộm, Uốn C, Uốn xoăn, Duỗi, Duỗi hơi nước, Nâng sáng, Bóc màu, Tone sau tẩy, Phục hồi, Hấp dầu, Tẩy và Nhuộm sáng tạo/Balayage/Ombre/Highlight khi bảng giá có size tương ứng.
4. Khi khách chuyển từ Nhuộm sang Uốn/Duỗi/Phục hồi/Tẩy/Balayage hoặc dịch vụ có size khác, **không xóa SIZE và không trả UNKNOWN** chỉ vì dịch vụ đã thay đổi.
5. Nếu CURRENT_SIZE đang là S/M/L và CURRENT_BATCH không có size mới, xuất lại đúng CURRENT_SIZE. Chỉ đổi khi chính khách sửa size, nói đang hỏi cho người khác/đổi người làm, hoặc cung cấp mô tả mới mâu thuẫn rõ.
6. Nếu CURRENT_SIZE đang trống/UNKNOWN và khách chưa cho size thì SIZE=UNKNOWN.
7. PACKAGE chỉ dành cho Nhuộm. Size và gói độc lập; khách nói size không có nghĩa đã chọn gói. Khi đổi khỏi Nhuộm vẫn giữ PACKAGE hiện có nhưng không dùng PACKAGE cho dịch vụ khác.
8. `Duỗi chân tóc (áp dụng khi Uốn)` không có size riêng; SIZE đã lưu chỉ dùng để xác định giá Uốn C/Uốn xoăn đi kèm.
9. Không trích xuất tên, SĐT, ngày giờ, dịch vụ lịch hẹn, trạng thái hay hành động tự động.

Ví dụ:
- CURRENT_SIZE=L, khách nói `chị muốn làm thêm uốn kèm duỗi` → SIZE=L, không hỏi/làm mất size.
- CURRENT_SIZE=L, khách nói `uốn xoăn` → SIZE=L.
- CURRENT_SIZE=L, khách nói `tóc chị giờ size M nha` → SIZE=M.
- CURRENT_SIZE=L, khách nói `chị hỏi cho em gái` nhưng chưa biết size em gái → SIZE=UNKNOWN.

Chỉ xuất đúng hai dòng:
SIZE=<S|M|L|UNKNOWN>
PACKAGE=<BASIC|VIP|CAO_CAP|UNKNOWN>
