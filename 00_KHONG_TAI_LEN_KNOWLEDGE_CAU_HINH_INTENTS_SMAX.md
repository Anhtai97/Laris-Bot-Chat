# CẤU HÌNH ROUTING VÀ INTENT SMAX — LARIS

> Tệp dành cho quản trị viên, không tải vào Knowledge.

## Quyết định hiện tại

Dùng `Messenger Default → AI_NHAN_TIN` làm router tổng. Không bật Trigger GenAI/Other song song cho các câu tư vấn thông thường.

Không cần Intent riêng cho giá, ưu đãi, địa chỉ, xin ảnh, Duỗi kết hợp Uốn hoặc đặt lịch. Các nội dung này đi qua buffer 15 giây và Bot AI để giữ đúng ngữ cảnh.

Nếu dùng Intent, chỉ giữ Intent thật sự cần hành động khác như `can-nhan-vien-ho-tro` để chuyển người thật.

## Quy tắc routing

- Tin tự do, câu hỏi giá, yêu cầu đặt lịch, xin ảnh và tư vấn dịch vụ: chuyển về `AI_NHAN_TIN`.
- Keyword nếu còn giữ chỉ được chuyển block, không trả lời trực tiếp và không gọi GenAI riêng, ngoại trừ keyword chặn metadata hệ thống được cấu hình để kết thúc im lặng.
- Click To Message: tách metadata quảng cáo; chỉ chữ khách tự nhập mới vào buffer.
- Dòng `Đăng kí topic: Updates and promotions` / `Đăng ký topic: Updates and promotions` là metadata hệ thống: chặn trước buffer, không gọi AI và không gửi phản hồi.
- Yêu cầu gặp nhân viên/khiếu nại có thể chuyển người thật nhưng phải dừng luồng AI hiện tại trước khi sender khác chạy.

## Tiếp nhận lịch

Không dùng Intent để thu field hoặc chạy automation. Bot AI chỉ hỏi dịch vụ, thời gian hoặc SĐT còn thiếu sau khi khách thật sự muốn đặt; nhân viên note thủ công.

## Xin ảnh

Không dùng Intent để gửi ảnh. Bot AI gửi một câu chờ tự nhiên, sau đó nhân viên gửi hình thủ công.

## Checklist chống lặp

- Chỉ một router tổng quát đang bật cho tin tư vấn.
- Không có Keyword chung trả lời trực tiếp.
- Không có GenAI trực tiếp cạnh tranh với buffer 15 giây.
- Không có sender thứ hai trong hoặc sau `AI_TRA_LOI`.
- Metadata quảng cáo bị loại trước buffer.
- Một message ID chỉ xuất hiện trong một lượt xử lý ở logs.
