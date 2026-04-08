**Nhóm**: C401 - Z1
**Track**: Vinmec
**Problem statement:** Người dùng muốn đặt lịch khám tại Vinmec nhưng phải tự nhập nhiều thông tin, chọn chuyên khoa/bác sĩ/cơ sở, dẫn đến trải nghiệm rườm rà và phải xếp hàng lâu khi đến viện. AI sẽ hỗ trợ hội thoại thông minh, gợi ý chuyên khoa phù hợp với triệu chứng, điền form nháp và chỉ gửi yêu cầu đặt lịch sau khi người dùng xác nhận toàn bộ thông tin.

## AI Product Canvas

- **User / Value:**
  - Người dùng lần đầu đi khám, phụ huynh đặt lịch cho con, người lớn tuổi hoặc người có triệu chứng nhưng chưa biết chọn chuyên khoa.
  - Pain: form đặt lịch nhiều bước, phải chọn chuyên khoa/bác sĩ/cơ sở, chờ nhân viên gọi xác nhận, dễ chọn nhầm slot, đến quầy xếp hàng, có thể hết lịch phù hợp.
  - Value: AI chat hỏi từng bước, gợi ý chuyên khoa dựa trên triệu chứng, điền nháp form và hiển thị tóm tắt trước khi user xác nhận đặt lịch/nhận QR.

- **Trust:**
  - AI chỉ gợi ý chuyên khoa theo hướng tham khảo, không chẩn đoán bệnh.
  - Hiển thị toàn bộ thông tin cuộc hẹn (tên, SĐT, ngày giờ, cơ sở, chuyên khoa, bác sĩ, triệu chứng, QR) trước khi đặt.
  - User được phép sửa trực tiếp từng trường hoặc chuyển sang nhân viên khi AI không đủ tự tin.

- **Feasibility:**
  - Sử dụng mô hình ngôn ngữ lớn để hiểu triệu chứng, phân loại chuyên khoa, điền thông tin và tạo đề xuất lịch.
  - Yêu cầu tích hợp realtime với hệ thống lịch bác sĩ và danh mục chuyên khoa/cơ sở Vinmec.
  - Mục tiêu latency < 3s cho chat text, chi phí tham chiếu khoảng $0.05/phiên đặt lịch.

- **Then chốt:**
  - Augmentation: AI hỗ trợ đặt lịch nhanh hơn, nhưng user xác nhận cuối cùng.
  - Tránh automation hoàn toàn vì một lỗi nhỏ có thể làm user đến sai cơ sở/ngày giờ hoặc phải sửa lại tại quầy.
  - Priority: precision-first cho các trường quan trọng như chuyên khoa, ngày giờ, cơ sở, bác sĩ, SĐT.

## User Stories

### Feature 1: AI Tư vấn chuyên khoa và Tìm lịch khám (Smart Triaging)

**Trigger:** User nhập triệu chứng hoặc nhu cầu khám → AI phân tích ngữ cảnh → đề xuất chuyên khoa & bác sĩ → hiển thị lịch trống.

| Path | Câu hỏi thiết kế | Mô tả |
|------|-------------------|-------|
| **Happy — AI đúng, tự tin** | User thấy gì? Flow kết thúc ra sao? | AI xác định đúng khoa Tim mạch. Hiển thị đề xuất khám Tim mạch với bác sĩ phù hợp và slot trống, user chọn "Đồng ý" để hoàn tất đặt lịch. |
| **Low-confidence — AI không chắc** | AI đánh giá không đủ tự tin | Triệu chứng chồng chéo (đau bụng vùng hạ sườn). AI hỏi thêm: "Triệu chứng này có thể liên quan đến Tiêu hóa hoặc Gan mật. Bạn có bị kèm theo vàng da hay buồn nôn không để tôi tìm bác sĩ phù hợp nhất?" |
| **Failure — AI sai** | User biết AI sai bằng cách nào? Recover ra sao? | AI gợi ý nhầm chuyên khoa (ví dụ đau cơ vai nhưng đề xuất khoa Phổi). User chọn "Chọn lại khoa thủ công" hoặc "Chat trực tiếp với nhân viên y tế" để sửa. |
| **Correction — user sửa** | User sửa yêu cầu sau khi AI đề xuất | User gõ: "Không, tôi muốn khám chấn thương chỉnh hình". AI cập nhật danh sách bác sĩ tương ứng và lưu log để cải thiện bộ lọc triệu chứng. |

## Eval metrics + Threshold

### Task-level Metrics
| Metric | Định nghĩa | Công thức | Target | Insight |
| --- | --- | --- | --- | --- |
| **Intent Accuracy** | AI hiểu đúng mục đích user (đặt lịch / hỏi info / emergency) | correct intents / total | ≥ 92% (Emergency recall ≥ 98%) | Nếu sai ở đây sẽ làm hỏng toàn bộ flow. |
| **Symptom → Department Accuracy** | Mapping triệu chứng → chuyên khoa | Top-1 / Top-3 accuracy | Top-1 ≥ 80%, Top-3 ≥ 95% | Gợi ý nhiều lựa chọn khi Top-1 không chắc. |
| **Slot Filling Accuracy** | Điền đúng các field form | correct slots / total | ≥ 95% (Critical ≥ 98%) | Ưu tiên các field quan trọng: khoa, ngày, cơ sở. |
| **Conversation Efficiency** | Số lượt hội thoại để hoàn tất booking | total turns / bookings | ≤ 6 turns | Quá dài dễ drop, quá ngắn dễ thiếu info. |

### Business Metrics
| Metric | Định nghĩa | Công thức | Target | Insight |
| --- | --- | --- | --- | --- |
| **Conversion Rate** | Tỷ lệ chat → đặt lịch thành công | bookings / sessions | ≥ 30% | Tổng chỉ số giá trị của sản phẩm. |
| **Drop-off Rate** | Tỷ lệ rơi ở từng bước funnel | 1 - (step i+1 / step i) | Giảm đều qua từng step | Dùng để tìm điểm nghẽn UX/AI. |
| **Time-to-Booking** | Thời gian hoàn tất đặt lịch | end - start | ≤ 3 phút | Cần đi cùng accuracy để giữ user. |

### AI Metrics
| Metric | Định nghĩa | Công thức | Target | Insight |
| --- | --- | --- | --- | --- |
| **Response Relevance** | Câu trả lời đúng ngữ cảnh, không lan man | human/LLM score | ≥ 4.2/5 | Ảnh hưởng trực tiếp đến UX. |
| **Hallucination Rate** | AI bịa thông tin (bệnh, bác sĩ, slot) | hallucinated / total | < 2% | Quan trọng để đảm bảo an toàn. |

### Human-in-the-loop Metrics
| Metric | Định nghĩa | Công thức | Target | Insight |
| --- | --- | --- | --- | --- |
| **Escalation Rate** | Tỷ lệ chuyển sang tư vấn viên | escalated / total | 10–25% | Quá thấp nghĩa là AI quá tự tin. |
| **Manual Handling Time** | Thời gian xử lý của người | end - start | Giảm dần theo thời gian | Đo hiệu quả vận hành. |
| **CSAT (Satisfaction)** | Mức hài lòng người dùng | positive / total | ≥ 4.2/5 | Đánh giá tổng thể hệ thống. |

## Top 3 failure modes

1. **Sai lệch chuyên môn (medical hallucination)**
   - Trigger: Người dùng mô tả triệu chứng phức tạp hoặc hiếm.
   - Hậu quả: AI trả lời tự tin nhưng sai, user có thể đi nhầm chuyên khoa.
   - Mitigation: Detect out-of-scope và fallback: "Tôi không đủ thông tin, vui lòng gọi điện trực tiếp cho bác sĩ." 

2. **Gợi ý sai chuyên khoa**
   - Trigger: Triệu chứng overlap nhiều khoa (đau ngực, đau bụng...).
   - Hậu quả: Đặt lịch sai khoa, tốn thời gian sửa.
   - Mitigation: Đưa ra nhiều lựa chọn chuyên khoa, giải thích rõ và để user chọn.

3. **Trường hợp khẩn cấp (emergency)**
   - Trigger: Triệu chứng nguy hiểm (đột quỵ, đau tim).
   - Hậu quả: Chatbot xử lý bình thường, chậm cấp cứu.
   - Mitigation: Detect emergency và yêu cầu gọi cấp cứu ngay.

> Các rủi ro bổ sung: dữ liệu bác sĩ/lịch không cập nhật, thất thoát hiệu năng trong giờ cao điểm.

## ROI 3 kịch bản

| Tiêu chí | Conservative (Thận trọng) | Realistic (Thực tế) | Optimistic (Lạc quan) |
| --- | --- | --- | --- |
| **Assumption** | AI bị loop, accuracy < 50%, tốn nhiều token. | AI điều hướng đúng > 70%, cần 3-5 lượt chat. | AI chính xác 95%, ít token nhất. |
| **Benefit** | $0/ngày | $150/ngày | $500/ngày |
| **Net** | -$50/ngày | +$120/ngày | +$485/ngày |

### Kill Criteria

1. Token burn rate > $0.5/ca đặt lịch thành công.
2. Conversion failure > 50% sau 2 tháng tinh chỉnh prompt.
3. Data integrity sai thông tin quan trọng > 20% trong 1 tuần liên tục.
4. Cost > Benefit nếu chi phí model lớn hơn giá trị thời gian tiết kiệm trong 2 tháng.

## Mini AI Spec

- **Dự án:** AI Booking Assistant cho VinMec / MyVinMec.
- **Mục tiêu:** Giảm friction đặt lịch khám, tăng conversion, giảm thời gian user dành cho form và xếp hàng.
- **Cách thức AI hoạt động:**
  - Thu triệu chứng/nguyện vọng qua chat/speech.
  - Phân loại chuyên khoa, gợi ý bác sĩ và slot trống.
  - Điền nháp form đặt lịch và hiển thị tổng hợp thông tin.
  - Chỉ gửi yêu cầu đặt lịch khi user xác nhận.
- **Augmentation:** AI hỗ trợ và đề xuất; user vẫn giữ quyền kiểm soát cuối cùng. Khi AI không chắc, chuyển sang nhân viên hoặc hỏi thêm câu.
- **Logic sàng lọc:**
  - Xác định low-confidence, hỏi thêm thông tin hoặc cung cấp nhiều lựa chọn.
  - Detect emergency và yêu cầu gọi cấp cứu.
  - Detect out-of-scope và fallback sang tư vấn viên hoặc hotline.
- **Chất lượng & Rủi ro:**
  - Ưu tiên recall tuyệt đối hơn precision: thà dẫn user đi khám thừa còn hơn bỏ sót ca nặng.
  - Nguy cơ: hallucination chuyên khoa, dữ liệu lịch/bác sĩ lỗi thời, sai thông tin cá nhân/y tế.
  - Cần màn xác nhận cuối để tránh submit nhầm slot.
- **Data Flywheel:**
  - Thu correction log từ sửa của user/tư vấn viên.
  - Ghi nhận implicit signal: tỷ lệ hoàn tất, thời gian form, lượt hỏi lại, tỉ lệ handoff, tỉ lệ sửa quầy.
  - Ghi nhận explicit signal: rating, feedback "gợi ý không đúng", lý do bỏ flow.
  - Dùng dữ liệu domain-specific, real-time và human judgment để cải thiện prompt, mapping chuyên khoa và rule.

**Phân công:**
Đức: AI Product Canvas
Nguyên: User Stories 
Huân: Eval metrics
Thắng: Top failure modes 
Tuấn Anh: ROI 3 kịch bản 
Bảo: Mini AI Spec
