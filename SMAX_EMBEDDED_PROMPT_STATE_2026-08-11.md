# EMBEDDED PROMPT — AI TRẠNG THÁI TƯ VẤN

CURRENT_MESSAGE={{last_content_by_user}}
CURRENT_BATCH={{ai_processing_text}}
CURRENT_SIZE={{laris_hair_size}}
CURRENT_PACKAGE={{laris_dye_package}}

Chỉ trích xuất hai dữ kiện tư vấn bền vững: SIZE và PACKAGE.

Quy tắc:
1. Chỉ lời khách được xác nhận hoặc sửa dữ kiện. Không lấy lời cũ của salon, quảng cáo hoặc metadata.
2. SIZE chỉ nhận S, M, L. PACKAGE chỉ nhận BASIC, VIP, CAO_CAP.
3. Size và gói độc lập. Khách nói size không có nghĩa đã chọn gói.
4. Nếu khách nói dữ kiện cũ thuộc người khác hoặc chưa chọn gói, trả UNKNOWN cho dữ kiện đó.
5. Nếu lượt hiện tại không thay đổi dữ kiện, giữ CURRENT_SIZE và CURRENT_PACKAGE.
6. Không trích xuất tên, SĐT, ngày giờ, dịch vụ lịch hẹn, trạng thái hay hành động tự động.

Chỉ xuất đúng hai dòng:
SIZE=<S|M|L|UNKNOWN>
PACKAGE=<BASIC|VIP|CAO_CAP|UNKNOWN>
