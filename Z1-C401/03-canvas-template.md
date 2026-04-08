# AI Product Canvas — template

Điền Canvas cho product AI của nhóm. Mỗi ô có câu hỏi guide — trả lời trực tiếp, xóa phần in nghiêng khi điền.

---

## Canvas

|   | Value | Trust | Feasibility |
|---|-------|-------|-------------|
| **Câu hỏi guide** | User nào? Pain gì? AI giải quyết gì mà cách hiện tại không giải được? | Khi AI sai thì user bị ảnh hưởng thế nào? User biết AI sai bằng cách nào? User sửa bằng cách nào? | Cost bao nhiêu/request? Latency bao lâu? Risk chính là gì? |
| **Trả lời** | Khách hàng của Vietnam Airlines. Pain: người dùng chỉ có thể nhận hỗ trợ khi đã đặt vé vì chatbot yêu cầu mã đặt vé. AI giải quyết bằng cách chuyển sang AI Agent có tool tra cứu chuyến bay real-time và xử lý ngôn ngữ tự nhiên để hiểu nhu cầu như "bay đi Sài Gòn ngày mai". | Nếu AI sai, user có thể đặt nhầm chuyến bay, sai giờ hoặc lỡ chuyến bay. User nhận biết lỗi khi đối chiếu thông tin từ câu trả lời của AI với thông tin người dùng tự tìm kiếm trực tiếp. Cách sửa: chatbot cung cấp nút "Tìm kiếm lại" hoặc chuyển hướng sang nhân viên hỗ trợ (human-in-the-loop) khi thông tin không khớp. | Cost khoảng $0.0225/request với GPT-4o, giả định trung bình 3 lượt hỏi-đáp, input khoảng 3000 tokens và output khoảng 1500 tokens. Latency gồm LLM reasoning 500ms-1s, data retrieval 700ms-1.5s, LLM synthesis 500ms-1s; tổng khoảng 3.5s. Risk chính: hallucinate giá vé, chuyến bay, lịch bay nếu không grounding tốt vào dữ liệu thực tế; phản hồi chậm. |

---

## Automation hay augmentation?

☐ Automation — AI làm thay, user không can thiệp
☑ Augmentation — AI gợi ý, user quyết định cuối cùng

**Justify:** Vì rủi ro (Risk) đã nêu là AI có thể "hallucinate" (ảo tưởng) về giá vé và lịch trình, việc để AI tự động đặt vé (Automation) là cực kỳ nguy hiểm. User cần đóng vai trò kiểm chứng thông tin cuối cùng. AI đóng vai trò là một trợ lý thông minh giúp tìm kiếm nhanh bằng ngôn ngữ tự nhiên, thay vì thay thế hoàn toàn hành động thanh toán/xác nhận của khách hàng.

Gợi ý: nếu AI sai mà user không biết → automation nguy hiểm, cân nhắc augmentation.

---

## Learning signal

| # | Câu hỏi | Trả lời |
|---|---------|---------|
| 1 | User correction đi vào đâu? | Đi vào feedback loop của chatbot: log truy vấn, kết quả AI trả lời, nút "Tìm kiếm lại", lựa chọn chuyến bay sau cùng, các case được chuyển sang nhân viên hỗ trợ, hành vi hỏi lại cùng một intent bằng từ khóa khác và session abandonment khi user rời hội thoại để tự tìm kiếm thủ công. |
| 2 | Product thu signal gì để biết tốt lên hay tệ đi? | Conversion rate: tỷ lệ user tìm được chuyến bay đúng ý và đi đến bước đặt vé thực tế. Click-through rate: tỷ lệ user click vào kết quả chuyến bay AI trả về. Correction/fallback signal: tỷ lệ user bấm "Tìm kiếm lại", tỷ lệ chuyển sang human-in-the-loop, số lần user sửa ngày/giờ/điểm đến sau câu trả lời của AI. Feedback signal: thumbs up/down sau mỗi câu trả lời và độ khớp giữa chuyến bay AI gợi ý với chuyến bay user chọn cuối cùng. |
| 3 | Data thuộc loại nào? ☐ User-specific · ☐ Domain-specific · ☐ Real-time · ☐ Human-judgment · ☐ Khác: ___ | ☑ Real-time: lịch bay, giá vé và tình trạng chỗ thay đổi liên tục. ☑ Domain-specific: dữ liệu tuyến bay, sân bay, hạng vé, chính sách đổi/hoàn và quy định hành lý của Vietnam Airlines. ☑ Human-judgment: đánh giá thumbs up/down, lựa chọn chuyến bay cuối cùng của user và correction từ nhân viên hỗ trợ khi AI trả lời chưa đúng. |

**Có marginal value không?** (Model đã biết cái này chưa? Ai khác cũng thu được data này không?)
Có. Đối thủ có thể mua dữ liệu ngành, nhưng họ không có dữ liệu hành vi khách hàng (User Intent) của riêng Vietnam Airlines. Chúng ta thu thập cách khách hàng hỏi về các chặng bay đặc thù (ví dụ: cách dùng từ địa phương, thói quen đặt vé giờ chót), từ đó tối ưu hóa Agent vượt xa các chatbot phổ thông trên thị trường.

---

## Cách dùng

1. Điền Value trước — chưa rõ pain thì chưa điền Trust/Feasibility
2. Trust: trả lời 4 câu UX (đúng → sai → không chắc → user sửa)
3. Feasibility: ước lượng cost, không cần chính xác — order of magnitude đủ
4. Learning signal: nghĩ về vòng lặp dài hạn, không chỉ demo ngày mai
5. Đánh [?] cho chỗ chưa biết — Canvas là hypothesis, không phải đáp án

---

*AI Product Canvas — Ngày 5 — VinUni A20 — AI Thực Chiến · 2026*
