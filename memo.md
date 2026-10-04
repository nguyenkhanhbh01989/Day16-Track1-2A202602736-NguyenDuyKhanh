# Memo Teardown — Cursor

**Họ tên:** Nguyễn Duy Khánh  - 2A202602736
**Sản phẩm:** Cursor — AI Code Editor

**Vì sao chọn sản phẩm này:**  
Tôi chọn Cursor vì đây là một ví dụ rõ về cách một AI product tiến hóa từ việc hỗ trợ developer viết code sang việc giao cả nhiệm vụ phát triển phần mềm cho agent. Sản phẩm cũng phù hợp để quan sát cách một startup xây moat khi các foundation model mà nó phụ thuộc liên tục thay đổi.

---

## §1. Timeline các cập nhật lớn

| Thời điểm | Cập nhật | Context lúc đó | Nguyên lý |
|---|---|---|---|
| **06/2023** | Cursor đưa **Codebase Context v1** vào sản phẩm, cho phép câu trả lời sử dụng context từ toàn bộ codebase. | Các coding assistant lúc đó chủ yếu mạnh ở completion hoặc đoạn code đang mở; vấn đề lớn là model không hiểu đủ repository của developer. | **Vertical AI / Context moat:** giá trị không chỉ nằm ở model mà ở khả năng đưa đúng context chuyên ngành vào model. |
| **07/2024** | Ra mắt **Composer beta**, cho phép AI chỉnh sửa nhiều file từ một yêu cầu. | Claude 3.5 Sonnet và các model coding mạnh hơn khiến việc sinh code dài và chỉnh sửa phức tạp khả thi hơn. Cursor có thể chuyển từ “AI trả lời” sang “AI thực hiện thay đổi”. | **x10 experience:** giảm nhiều thao tác search → mở file → sửa → kiểm tra thành một intent ở cấp task. |
| **11–12/2024** | Composer có **Agent**: tự chọn context, dùng terminal, đọc lỗi lint, sửa nhiều vị trí và có thể tự chạy command. | Coding AI bắt đầu dịch chuyển từ copilot sang agent. Model không chỉ cần sinh code mà cần có tool để quan sát và hành động trong môi trường development. | **Agentic workflow:** chuyển từ AI tạo output sang AI thực hiện một chuỗi hành động có feedback từ environment. |
| **05/2025** | Cursor giới thiệu **Background Agent**, cho phép agent chạy trong remote environment và thực hiện nhiều task song song. | Khi agent đủ khả năng xử lý task dài, việc bắt developer ngồi chờ trong IDE trở thành bottleneck. | **Human leverage / delegation:** AI chuyển từ công cụ đồng bộ bên cạnh developer thành worker có thể được giao việc. |
| **06/2025** | Cursor Agents được đưa lên **web và mobile**, có thể chạy task khi developer không ở IDE và tạo PR để review. | Background Agent khiến coding không còn bắt buộc gắn với phiên làm việc trực tiếp trên máy developer. | **Workflow expansion:** mở rộng job từ “giúp tôi khi đang code” sang “tiếp tục làm việc thay tôi khi tôi rời IDE”. |
| **08–09/2025** | Cursor chuyển pricing của Teams từ request cố định sang **usage dựa trên lượng công việc/model cost**. | Một agent có thể chỉ trả lời câu hỏi syntax hoặc có thể thực hiện cả một PR; chi phí giữa hai task chênh lệch rất lớn. | **Value/cost-aligned pricing:** đơn vị giá phải tiến hóa theo đơn vị giá trị khi sản phẩm chuyển từ chat/completion sang agent. |
| **10/2025** | Ra mắt **Cursor 2.0 + Composer**, model coding riêng của Cursor, cùng interface hỗ trợ nhiều agent chạy song song. | Chỉ bọc các model bên ngoài ngày càng khó tạo khác biệt khi các đối thủ đều tiếp cận cùng frontier models. | **Wrapper → moat:** dịch chuyển từ UX wrapper sang sở hữu model, agent harness, tools và workflow riêng. |
| **04–06/2026** | Cursor mở **SDK cho agent**, tiếp tục phát triển Composer và điều chỉnh Teams pricing cho heavy agent usage. | Agent không còn chỉ là tính năng bên trong editor; doanh nghiệp bắt đầu cần nhúng agent vào workflow và automation của riêng họ. | **Platformization + learning loop:** biến capability nội bộ thành platform, đồng thời tận dụng usage thực tế để cải thiện hệ thống agent/model. |

### Vì sao tôi chọn những mốc này?

Tôi không chọn các bản cập nhật chỉ sửa UI, tăng tốc độ indexing hoặc thêm một model mới vào model picker vì chúng chủ yếu cải thiện sản phẩm hiện tại chứ không thay đổi **Cursor là sản phẩm gì hoặc developer giao việc cho nó như thế nào**.

Ví dụ, việc thêm hỗ trợ một frontier model mới có thể cải thiện chất lượng output nhưng không phải một product decision đủ lớn. Ngược lại, **Composer, Agent và Background Agent** lần lượt thay đổi đơn vị tương tác từ *đoạn code → task → công việc có thể giao đi*, nên chúng xứng đáng là các cột mốc.

**Nguồn chính:** Cursor Changelog và Cursor Blog: Codebase Context v1; Composer beta; Agent; Background Agent; Cursor on web/mobile; Teams pricing; Cursor 2.0/Composer; Cursor SDK.

---

## §2. Tệp user & JTBD

| | Early adopters | Tệp hiện tại |
|---|---|---|
| **Đặc điểm** | Developer cá nhân hoặc developer trong startup nhỏ; quen VS Code; chủ động thử Copilot/ChatGPT/Claude; chấp nhận sản phẩm beta để đổi lấy tốc độ coding. | Developer chuyên nghiệp, power user và engineering team; làm việc trên codebase lớn; bắt đầu giao task hoàn chỉnh cho agent và cần security, billing, quản trị, review và automation. |
| **JTBD chính** | “Khi đang code, tôi muốn AI hiểu codebase và giúp tôi viết/sửa code nhanh hơn để không phải liên tục copy context sang chatbot.” | “Khi có một engineering task, tôi muốn giao càng nhiều phần thực thi càng tốt cho AI nhưng vẫn có thể review và kiểm soát kết quả trước khi merge.” |
| **Trước đó họ làm bằng cách nào?** | VS Code + GitHub Copilot cho autocomplete; ChatGPT/Claude để hỏi code; tự copy file/context giữa IDE và chatbot. | Developer tự implement task; chia ticket cho người khác; dùng AI chat/copilot cho từng bước nhỏ; tự chạy test, terminal, sửa lỗi và tạo PR. |
| **Giá trị Cursor mang lại** | Đưa AI trực tiếp vào editor và cung cấp codebase context. | Agent có thể search, edit, chạy terminal, xử lý nhiều file, làm việc background/parallel và đưa kết quả về cho developer review. |

### Dịch chuyển tệp

Mốc quan trọng nhất tạo ra sự dịch chuyển này không phải một model mới mà là chuỗi:

**Composer → Agent → Background Agent → multi-agent.**

Composer nâng đơn vị công việc từ một completion lên nhiều file. Agent tiếp tục nâng nó thành một task có thể bao gồm search, edit và terminal. Background Agent tách task khỏi phiên làm việc trực tiếp của developer.

Vì vậy, Cursor dần dịch chuyển từ:

> **“AI giúp developer code nhanh hơn”**

sang:

> **“Developer giao engineering work cho AI và quản lý kết quả.”**

Điều này cũng giải thích vì sao các chức năng dành cho Teams/Enterprise như quản trị usage, privacy, SSO, shared context và cost control ngày càng quan trọng.

### Switching cost — phân tích theo 4 Forces

**1. Push — điều đẩy user khỏi cách làm cũ**

Developer phải liên tục chuyển giữa IDE, documentation, terminal và chatbot. Với các task lớn, autocomplete chỉ tiết kiệm thao tác gõ chứ chưa loại bỏ phần lớn công việc tìm context, sửa nhiều file, chạy test và debug.

**2. Pull — điều kéo user sang Cursor**

Cursor gom nhiều bước vào cùng workflow: hiểu repository → lập kế hoạch → chỉnh code → chạy command → sửa lỗi → tạo kết quả để review. Agent và Background Agent còn cho phép developer giao nhiều việc hơn thay vì phải tương tác từng bước.

**3. Anxiety — điều khiến user ngại chuyển**

AI có thể sửa sai nhiều file, hiểu sai requirement, chạy command không mong muốn hoặc tạo code nhìn hợp lý nhưng có lỗi. Với doanh nghiệp còn có thêm lo ngại về privacy, security và chi phí agent khó dự đoán.

**4. Habit — điều giữ user ở giải pháp cũ**

Developer đã quen VS Code/JetBrains, GitHub workflow, shortcut và extension ecosystem. GitHub Copilot cũng có lợi thế vì nằm sẵn trong workflow GitHub/Microsoft.

### Lực giữ user mạnh nhất

Theo tôi, switching cost bền vững nhất của Cursor không phải **“user đã quen giao diện”**, vì Cursor dựa nhiều trên mô hình editor quen thuộc và UI có thể bị sao chép.

Lực mạnh hơn có khả năng nằm ở **workflow + context + agent behavior**: rules, repository context, lịch sử tương tác và cách team tổ chức việc giao task cho agent. Khi Cursor trở thành một phần của quy trình từ issue → implementation → review, chi phí chuyển đổi không còn chỉ là học một editor mới mà là thay đổi workflow của cả engineering team.

---

## §3. Ba dự đoán hướng đi trong 6–12 tháng tới

> **Lưu ý:** Phần này là phán đoán dựa trên trajectory ở §1–§2, không phải roadmap đã được Cursor xác nhận.

### Dự đoán 1 — Cursor sẽ tiếp tục dịch chuyển từ “AI editor” sang “agentic software engineering platform”

**Loại:** Mở rộng tính năng

- **Dự đoán:** Cursor sẽ tăng các workflow mà developer chỉ mô tả outcome, sau đó nhiều agent tự chia nhỏ, implement, test và chuẩn bị thay đổi để con người review, thay vì developer điều khiển từng bước trong editor.
- **Lập luận:** trajectory từ Composer → Agent → Background Agent → multi-agent → SDK liên tục làm giảm lượng thao tác trực tiếp mà developer phải thực hiện. JTBD hiện tại vì thế cũng đang dịch từ *“giúp tôi code”* sang *“thực hiện engineering task cho tôi”*.

### Dự đoán 2 — Teams/Enterprise sẽ trở thành lớp sản phẩm ngày càng quan trọng

**Loại:** Mở rộng segment + mô hình kiếm tiền

- **Dự đoán:** Cursor sẽ đầu tư thêm vào governance cho engineering organization: shared agent configuration/context, observability, policy, security, cost control và đo lường hiệu quả của agent.
- **Lập luận:** Background/Cloud Agents tạo nhiều giá trị hơn khi chúng xử lý nhiều task của một team, nhưng cũng tạo ra vấn đề mới về quyền truy cập, chi phí và kiểm soát. Việc Cursor liên tục thay đổi Teams pricing và bổ sung admin/security controls cho thấy sản phẩm đang phải giải quyết những vấn đề vượt khỏi nhu cầu của developer cá nhân.

### Dự đoán 3 — Cursor sẽ cố sở hữu nhiều hơn “stack” thay vì chỉ phụ thuộc vào frontier model bên ngoài

**Loại:** Đe dọa Big Tech / moat

- **Dự đoán:** Cursor sẽ tiếp tục đầu tư vào model, routing, agent harness, evaluation và infrastructure riêng; đồng thời vẫn cho phép sử dụng model của nhiều provider.
- **Lập luận:** Nếu giá trị của Cursor chỉ là đưa Claude/GPT/Gemini vào VS Code thì moat rất yếu vì Microsoft/GitHub, OpenAI, Anthropic và các IDE khác có thể cung cấp trải nghiệm tương tự. Việc phát triển Composer và SDK cho thấy Cursor đang dịch chuyển moat từ **“model access” → “system tối ưu riêng cho software engineering”**.

### Dự đoán tôi tự tin nhất

Tôi tự tin nhất với **Dự đoán 1**, vì đây không phải một bước nhảy hoàn toàn mới mà là sự tiếp tục của cùng một trajectory đã xuất hiện qua nhiều product decision.

Giả định quan trọng nhất của dự đoán này là **agent tiếp tục đủ đáng tin cậy để xử lý task dài với ít supervision hơn**. Nếu chất lượng model/agent chững lại và developer vẫn phải kiểm tra hoặc sửa gần như toàn bộ output, lợi thế của việc chuyển từ copilot sang autonomous agent sẽ giảm đáng kể.

---

## §4. AI Log

| Việc | AI làm hay tôi làm? | Tôi kiểm chứng/phán đoán lại thế nào? |
|---|---|---|
| Tìm danh sách các Cursor changelog/blog có khả năng là mốc lớn | **AI hỗ trợ** | Tôi mở lại nguồn chính thức của Cursor, kiểm tra ngày và nội dung trước khi sử dụng. |
| Tổng hợp khoảng 10–15 mốc ứng viên | **AI hỗ trợ** | Tôi loại các release chỉ sửa UI, bug, latency hoặc thêm model vì chúng chưa đủ để coi là product decision. |
| Chọn 8 mốc cuối cùng | **Tôi quyết định** | Tôi dùng tiêu chí: nếu bỏ mốc này, trajectory của sản phẩm có thay đổi đáng kể không? |
| Gắn nguyên lý x10, Vertical AI, wrapper/moat, learning loop | **AI gợi ý + tôi phán đoán** | Tôi kiểm tra xem nguyên lý có giải thích được *tại sao quyết định đó hợp lý*, thay vì chỉ đặt một nhãn chung chung lên feature. |
| Phân tích early adopters và JTBD | **AI hỗ trợ tổng hợp** | Tôi viết lại JTBD dưới dạng “việc user muốn hoàn thành”, không dùng tên feature như Agent/Composer làm JTBD. |
| Phân tích 4 Forces | **Tôi + AI hỗ trợ** | Tôi đối chiếu từng force với workflow thực tế của developer thay vì coi mọi feature là switching cost. |
| Viết 3 dự đoán | **Tôi quyết định, AI hỗ trợ phản biện** | Mỗi prediction phải dẫn ngược được về ít nhất một product decision ở §1 và một thay đổi user/JTBD ở §2. |
| Kiểm tra memo cuối | **AI hỗ trợ** | Tôi kiểm tra lại nguồn, phân biệt dữ kiện với nhận định và bảo đảm mình có thể tự giải thích từng kết luận khi bị phản biện. |

---

## Nguồn tham khảo chính

1. **Cursor — Codebase Context v1**, 06/06/2023.  
2. **Cursor — Our Problems**, 12/10/2023.  
3. **Cursor Changelog 0.37 — Composer (beta)**, 13/07/2024.  
4. **Cursor Changelog 0.43 — New Composer UI, Agent, Commit Messages**, 24/11/2024.  
5. **Cursor Changelog 0.44 — Agent Improvements, Yolo Mode, Cursor Tab Update**, 17/12/2024.  
6. **Cursor Changelog 0.50 — Background Agent and Simplified Pricing**, 15/05/2025.  
7. **Cursor — Cursor on web and mobile**, 30/06/2025.  
8. **Cursor — Updates to Teams pricing**, 12/08/2025.  
9. **Cursor — Introducing Cursor 2.0 and Composer**, 29/10/2025.  
10. **Cursor — Build programmatic agents with the Cursor SDK**, 29/04/2026.  
11. **Cursor — Improvements to Teams Pricing**, 01/06/2026.

---

## Kết luận ngắn

Nhìn xuyên suốt timeline, thay đổi quan trọng nhất của Cursor không đơn thuần là **model ngày càng thông minh hơn**. Đơn vị công việc mà sản phẩm nhận từ developer đang lớn dần:

**completion → edit → multi-file task → agent task → background task → parallel agents → programmable agents.**

Điều đó cũng làm thay đổi định nghĩa một AI coding product “tốt”: từ **sinh code chính xác** sang **hoàn thành engineering outcome với đủ context, tốc độ, khả năng kiểm soát và mức độ tin cậy để developer sẵn sàng giao việc**.

Nếu trajectory này tiếp tục, cạnh tranh trong AI coding có thể ngày càng ít xoay quanh câu hỏi *“ai có chatbot viết code tốt hơn?”* và nhiều hơn quanh câu hỏi *“hệ thống nào có thể hoàn thành phần lớn software-engineering workflow đáng tin cậy nhất?”*
