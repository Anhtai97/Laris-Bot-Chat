# Laris / SmaxAI — Deployment audit 2026-08-11

## Backup

- Local pre-change copy: `backups/2026-08-11_before_simplify/` (14/14 source files plus UI snapshot).
- No Knowledge, Block, Flow, Intent, Sequence or workflow was deleted.
- The two n8n workflow JSON files were read only and not imported or modified.

## Root cause evidence

| Layer | Symptom/source | Correction |
|---|---|---|
| Global Prompt | Live prompt was 65,378 characters and repeated the same system prompt 5 times. It required every consulting/price reply to have a next step. | Replaced by one 5.6k-character current-message-first prompt; default CTA removed. |
| Embedded Bot prompt | Ended with `Mỗi phản hồi có đúng một bước tiếp theo`; booking state dominated regular consultation. | Replaced with current-message/current-batch wrapper; no default CTA; booking only on explicit intent. |
| Knowledge | K05 explicitly asked for a visit day/time; K03 price example invited booking. | K05 rewritten as stable remote-advice/safety facts; K03 and K01 CTA examples removed. K04 rewritten minimal but remains detached from Bot AI. |
| State | Extractor carried booking logic through all messages and risked treating service questions as booking data. | Questions about price/promo/service/total/contact/thanks preserve booking fields, set ACTION=NONE and never add SERVICES; size and dye package remain independent. |
| Flow/Trigger | Dormant duplicate GenAI/Messenger cards exist; old promo/cut/booking keyword triggers and Trigger GenAI could be competing paths if enabled. | Verified all those paths are disabled/recoverable. Active Default Reply has one 15-second debounce sequence, one Bot AI composer, one JSON packager and one final customer sender. |

## Live configuration after change

- Bot AI: GPT-5.4 mini unchanged; 10-message history unchanged.
- Knowledge attached: K01, K02, K03, K05 only; Apply all off; K04/K06/admin/test/n8n sources detached.
- Active router: Default Reply.
- Aggregation: `AI_NHAN_TIN` appends the latest customer content, then `REMOVE → ADD` on `AI_GOM_TIN_FB_DEBOUNCE`; sequence waits 15 seconds and calls `AI_TRA_LOI` once.
- State result maps `answer` to `ai_state_result`; Bot AI maps `answer` to `ai_answer`; AI Tạo Json is constrained to preserve text and only emit Messenger-card JSON.
- Final delivery: one active customer-send API in `AI_GUI_TRA_LOI`; the other active API is appointment upsert and does not compose customer text.
- Active image triggers use exact narrow fanpage-output phrases `size tóc hiện tại` and `bảng giá dịch vụ`; each sends one Messenger Image and does not call AI.

## Demo & Review

T1 passed once after deployment:

- Input: `Tiệm cho em hỏi còn chương trình giảm nào hong ạ`
- Actual: `Dạ hiện tại tháng 8 bên em đang có chương trình giảm 15% gồm giảm 10% dịch vụ và thêm 5% khi mình để lại đánh giá ạ. Riêng cắt tóc nữ đang ưu đãi từ 200k còn 150k nha chị.`
- Result: PASS.

After that run, the only final prompt change was adding the two narrow image-trigger phrases. Subsequent Demo & Review calls returned no message for both Bot AI and the independent JSON-packaging GenAI, including in multiple fresh browser tabs after waits of 70–100 seconds. Therefore T1 also requires a final regression rerun; the remaining acceptance cases must not be marked PASS until Smax Demo returns actual output again or an explicitly designated test account is supplied.

## Production retest 2026-08-12

The owner explicitly designated Facebook account `Phương Bùi` for production tests. Full actual outputs and PASS/FAIL evidence are recorded in `PRODUCTION_TEST_RESULTS_2026-08-12.md`. Final result after fixes and regression: 14/14 PASS. T12 did not ask for name/phone because those fields already existed on the designated test contact.

Additional production fixes from the retest:

- Global Bot AI prompt now has hard gates for skin-tone questions and the two-message dye-package batch.
- State-agent prompt resets `PACKAGE=UNKNOWN` when the customer says an old package belonged to another person or has not selected a package. T6 was rerun and passed with all three size-L prices followed by only the missing package question; T7 was rerun and still passed.
- Final sender audit after reload: active cards are Messenger Typing, main JsonAPI customer sender, Set Attributes cleanup and appointment-upsert JsonAPI. Legacy `Messenger Text {{noidung}}` remains disabled and recoverable.
