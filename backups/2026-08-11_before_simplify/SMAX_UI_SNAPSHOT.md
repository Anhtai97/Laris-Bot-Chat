# SmaxAI Laris — snapshot trước tối giản

- Thời điểm: 2026-08-11 (Asia/Saigon).
- Business/Page: Laris Hair Studio / `fb516194644904485`.
- Trigger production: Default Reply (`6a574013de5f23f8a54504b1`).
- Chuỗi block: Default Reply → AI_NHAN_TIN → AI_TRA_LOI → AI_JSON_GUI → AI_GUI_TRA_LOI.
- Gom tin: sequence `[REMOVE] AI_GOM_TIN_FB_DEBOUNCE` rồi `[ADD] AI_GOM_TIN_FB_DEBOUNCE`, chờ 15 giây.
- Bot AI GenAI: `6a36432b301f17e2112ebf46`; model văn bản/hình ảnh GPT-5.4 mini; lịch sử 10 Messages; Skill Chat Sales Support; Campaign Leads Optimization.
- Knowledge chọn: K01, K02, K03, K05. Apply all không bật. K04, K06 và `cardconfig` không chọn.
- Intents đang bật: `dat-lich-moi`, `doi-huy-lich`, `can-nhan-vien-ho-tro`.
- Prompt toàn cục trong UI dài 65.378 ký tự và chứa cùng tiêu đề `# SYSTEM PROMPT — TƯ VẤN VIÊN LARIS HAIR STUDIO` 5 lần. Nội dung nguồn tương ứng nằm trong bản sao `Prompt Chính.Md`; UI đã bị lặp nguyên khối 5 lần.
- AI_TRA_LOI có GenAI trích trạng thái `AI Trạng Thái Lịch`, ánh xạ `answer` → `ai_state_result`; sau đó GenAI `Bot AI`, ánh xạ `answer` → `ai_answer`.
- Prompt nhúng Bot AI truyền TODAY_VN, NOW_VN, CURRENT_MESSAGE=`last_content_by_user`, CURRENT_BATCH=`ai_processing_text`, STATE_RESULT và các PERSISTENT_*; cuối prompt có luật cũ “Mỗi phản hồi có đúng một bước tiếp theo”. Bản đầy đủ có trong `00_KHONG_TAI_LEN_KNOWLEDGE_HUONG_DAN_CAU_HINH_SMAX.md` đã sao lưu cùng thư mục.
- AI_JSON_GUI dùng GenAI `AI Tạo Json`, prompt `{{ai_answer}}`, ánh xạ `answer` → `cards`.
- Đường gửi cuối production tiếp tục qua AI_GUI_TRA_LOI (JsonAPI); không chỉnh hai workflow n8n.
- Đã bấm `Tạo template` trên trigger Default Reply trước khi bắt đầu sửa để lưu bản khôi phục trong Smax.

Không có API key, bearer token, webhook secret, cookie hay thông tin đăng nhập trong snapshot này.
