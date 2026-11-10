# Đề tài: Dự đoán phản ứng của công chúng sau khi nghệ sĩ lên tiếng về scandal

## 1. Ý tưởng chung

Khi một nghệ sĩ gặp scandal, công chúng sẽ bàn luận, chỉ trích hoặc bênh vực. Sau một thời gian, nghệ sĩ có thể đăng bài giải thích, xin lỗi hoặc đưa ra thông báo chính thức.

Dự án muốn trả lời câu hỏi:

> **Dựa vào dư luận trước khi nghệ sĩ lên tiếng (A) và phản ứng ban đầu đối với lời lên tiếng (B), liệu có dự đoán được dư luận về scandal sẽ thay đổi như thế nào trong những ngày tiếp theo (C) không?**

Chúng ta không chỉ xem lời xin lỗi có được chấp nhận hay không, mà còn xem **sự chấp nhận đó có thực sự khiến thái độ đối với nghệ sĩ thay đổi không**.

## 2. Ba nhóm dữ liệu A, B, C

Lấy **T0** là thời điểm nghệ sĩ đăng lời lên tiếng chính thức.

| Nhóm | Thu thập gì? | Thu thập khi nào? | Dùng để làm gì? |
|---|---|---|---|
| **A — Trước lên tiếng** | Các bài đăng và bình luận **liên quan đến scandal** | Ví dụ: 7 ngày trước T0 | Biết công chúng đang ủng hộ hay phản đối nghệ sĩ ở mức nào |
| **B — Phản ứng với lời lên tiếng** | Bài lên tiếng gốc, các bài đăng và bình luận **bàn trực tiếp về lời lên tiếng** | Ví dụ: 24 giờ đầu sau T0 | Biết công chúng có chấp nhận lời giải thích/xin lỗi không |
| **C — Dư luận sau lên tiếng** | Các bài đăng và bình luận **tiếp tục bàn về scandal nói chung**, không bắt buộc nói về lời xin lỗi | Ví dụ: từ sau 24 giờ đến hết ngày thứ 7 | Đo kết quả thực tế để mô hình học và dự đoán |

**Mô hình dự kiến: A + B + nội dung lời lên tiếng → dự đoán C.**

Ví dụ:
- **A:** “Hành động này thật khó chấp nhận.”
- **B:** “Lời xin lỗi nghe khá chân thành, nên cho người ta cơ hội.”
- **C:** “Dù đã xin lỗi nhưng tôi vẫn không muốn ủng hộ nữa.”

Điểm thú vị: **B tích cực chưa chắc C cũng tích cực**.

**Lưu ý:** B và C phải được tách theo thời gian. Không dùng bài đăng, bình luận của C làm đầu vào để dự đoán chính C. Những bài viết ở B cũng có thể nhắc tới scandal, nhưng khi phân tích xu hướng chung cần phân nhóm rõ để tránh tính trùng hoặc làm lệch kết quả.

## 3. Dữ liệu cụ thể cần thu thập

Với mỗi scandal, thu thập:

1. **Thông tin vụ việc:** nghệ sĩ, mô tả ngắn, ngày scandal bắt đầu, các sự kiện lớn.
2. **Lời lên tiếng chính thức:** toàn bộ nội dung, đường dẫn gốc, thời gian đăng, dạng phản hồi (xin lỗi, giải thích, phủ nhận...), và các lần phản hồi tiếp theo nếu có.
3. **Bài đăng công khai thuộc A, B, C:** nội dung, thời gian, liên kết, nền tảng, số lượt thích/bình luận/chia sẻ (nếu có).
4. **Bình luận dưới các bài đăng:** nội dung, thời gian, bài đăng chứa bình luận, bình luận cha (nếu có), lượt thích (nếu có).

**Bình luận là dữ liệu chính để dán nhãn.** Luôn giữ nội dung bài đăng gốc để hiểu đúng ngữ cảnh bình luận.

Có thể lưu dữ liệu tối thiểu trong bốn bảng/file: `scandals`, `statements`, `posts`, `comments`. Mỗi bài đăng có nhãn nhóm `A/B/C`; nếu một bài nói cả về scandal và lời lên tiếng, lưu thêm hai cờ `is_scandal_related` và `is_response_related`.

### Quy mô thu thập thử nghiệm

Bắt đầu với **1–2 scandal**, ưu tiên trường hợp có mốc lên tiếng rõ ràng và nhiều bình luận công khai. Mục tiêu tham khảo mỗi scandal:

- Khoảng **100–200 bài đăng bàn về scandal** (A và C, phân bổ cân đối trước/sau).
- Khoảng **30–60 bài đăng bàn trực tiếp về lời lên tiếng** (B).
- Khoảng **1.000–2.000 bình luận trước T0** và **1.000–2.000 bình luận sau T0**, tùy lượng dữ liệu thực tế.

Đây chỉ là mức thử nghiệm, **không phải yêu cầu cố định**. Nên lấy mẫu theo ngày, theo bài đăng và nguồn đăng để tránh một bài viral chi phối toàn bộ dữ liệu.

## 4. Hệ thống nhãn: 2 cấp độ

### Cấp độ 1 — Nhãn cho từng bình luận

**Nhãn 1: Stance — Thái độ đối với nghệ sĩ/scandal**

Áp dụng chủ yếu cho A và C (có thể dùng cho B nếu cần).

| Nhãn | Ý nghĩa | Ví dụ |
|---|---|---|
| `SUPPORT` | Ủng hộ hoặc bênh vực nghệ sĩ | “Ai cũng có lúc sai, nên cho cơ hội.” |
| `OPPOSE` | Chỉ trích hoặc phản đối nghệ sĩ | “Tôi không muốn ủng hộ người này nữa.” |
| `NEUTRAL` | Không thể hiện quan điểm rõ ràng | “Vụ này bắt đầu từ hôm nào vậy?” |
| `IRRELEVANT` | Không liên quan hoặc spam | “Shop bán áo ở đâu?” |

**Nhãn 2: Acceptance — Thái độ đối với lời lên tiếng**

Áp dụng cho bình luận có đánh giá lời giải thích/xin lỗi, đặc biệt là nhóm B.

| Nhãn | Ý nghĩa | Ví dụ |
|---|---|---|
| `ACCEPT` | Chấp nhận lời lên tiếng | “Ít nhất đã biết nhận sai và xin lỗi.” |
| `REJECT` | Không chấp nhận lời lên tiếng | “Giải thích vòng vo, không thuyết phục.” |
| `CONDITIONAL` | Chấp nhận có điều kiện | “Phải thay đổi thực sự thì mới chấp nhận.” |
| `UNCLEAR` | Có bàn đến lời lên tiếng nhưng chưa rõ thái độ | “Không biết bài giải thích này sẽ ra sao.” |
| `NOT_APPLICABLE` | Không bàn hoặc không đánh giá lời lên tiếng | “Vụ việc diễn ra từ tuần trước.” |

**Phân biệt quan trọng:** Một người có thể **chấp nhận lời xin lỗi (`ACCEPT`) nhưng vẫn phản đối nghệ sĩ (`OPPOSE`)**. Vì vậy không gộp hai nhãn này thành một.

### Cấp độ 2 — Nhãn kết quả của một scandal/lần lên tiếng

Sau khi dán nhãn bình luận của A và C, tính tỷ lệ `SUPPORT` và `OPPOSE` ở từng giai đoạn, sau đó so sánh để xác định hướng thay đổi:

| Nhãn | Ý nghĩa |
|---|---|
| `RECOVERY` | Dư luận chuyển biến tích cực hơn: ủng hộ tăng, phản đối giảm |
| `BACKLASH` | Phản ứng tiêu cực tăng lên sau khi nghệ sĩ lên tiếng |
| `STABLE` | Thái độ chung gần như không thay đổi |
| `POLARIZATION` | Dư luận chia rẽ mạnh hơn giữa các nhóm quan điểm |

**Không nên dán nhãn cấp độ 2 bằng cảm nhận cá nhân.** Nên suy ra từ thống kê nhãn bình luận, đặt ngưỡng rõ ràng và kiểm tra tính ổn định. Có thể giữ cả **tỷ lệ phản đối/ủng hộ** dưới dạng số thay vì chỉ dùng 4 nhãn trên.

Ví dụ giả định:
- Trước lên tiếng (A): **70% bình luận phản đối**.
- Sau lên tiếng (C): **40% bình luận phản đối**.
- Mức thay đổi: **giảm 30 điểm phần trăm** → xu hướng phục hồi tích cực, nếu phép đo và mẫu đủ tin cậy.

Đây là **sự thay đổi quan sát được**, chưa đủ để khẳng định chính lời lên tiếng gây ra thay đổi; tin tức khác và sự kiện mới cũng có thể ảnh hưởng.

## 5. Mô hình dự đoán sẽ làm gì?

**Đầu vào:**
- **A:** Tỷ lệ ủng hộ/phản đối, nội dung và diễn biến dư luận trước lên tiếng.
- **B:** Mức `ACCEPT/REJECT/CONDITIONAL`, nội dung và phản ứng ban đầu với lời lên tiếng.
- **Thông điệp chính thức:** Nội dung nghệ sĩ đã nói.

**Đầu ra (C):**
- Ưu tiên dự đoán **tỷ lệ `SUPPORT`/`OPPOSE` trong giai đoạn C**, hoặc mức thay đổi so với A.
- Từ các tỷ lệ đó, có thể suy ra nhóm kết quả `RECOVERY`, `BACKLASH`, `STABLE`, `POLARIZATION`.

Ví dụ: dự đoán trong các ngày tiếp theo tỷ lệ phản đối sẽ giảm từ **70% xuống 45%**. Khi dữ liệu C thực tế xuất hiện, so sánh kết quả dự đoán với kết quả đo được.

## 6. Quy trình thực hiện đơn giản

1. **Chọn scandal** có mốc lên tiếng chính thức rõ ràng.
2. **Thu thập A, B, C** theo mốc thời gian; lưu cả bài đăng và bình luận.
3. **Lọc spam, trùng lặp**, kiểm tra nội dung có thực sự liên quan không.
4. **Dán nhãn bình luận:** có thể dùng LLM hỗ trợ, sau đó kiểm tra thủ công một mẫu để đánh giá độ chính xác và sự thống nhất nhãn.
5. **Tổng hợp nhãn:** đo thái độ A, mức chấp nhận B và thái độ C theo ngày.
6. **Xây dựng mô hình A + B → C**, so sánh với các cách dự đoán đơn giản chỉ dùng A hoặc dùng A + B.
7. **Đánh giá trên scandal chưa xuất hiện trong dữ liệu huấn luyện**, không chia ngẫu nhiên bình luận cùng scandal vào cả train và test để đánh giá dự báo cấp scandal.

## 7. Lưu ý về tính khả thi

- **Dễ dán nhãn hơn:** `Stance` và `Acceptance` ở cấp bình luận tương đối rõ ràng nếu có ngữ cảnh đầy đủ.
- **Khó nhất là dự đoán cấp scandal:** 10 scandal chỉ tạo ra khoảng 10 kết quả độc lập ở cấp scandal, dù có rất nhiều bình luận. Vì thế cần mở rộng số lượng sự kiện nếu muốn huấn luyện mô hình dự đoán đáng tin cậy.
- **Cần tránh thiên lệch:** Không chỉ lấy bài viral hoặc bình luận dưới bài xin lỗi; áp dụng cách chọn mẫu nhất quán cho A và C.
- **Cần giữ đúng thời gian:** B chỉ gồm phản ứng có sẵn tại thời điểm dự đoán; C là dữ liệu tương lai.
- **Chỉ thu thập dữ liệu công khai**, hạn chế thông tin cá nhân không cần thiết và tuân thủ quy định nền tảng.

## 8. Kết quả mong muốn

Dự án có thể tạo ra:

- Bộ dữ liệu bài đăng và bình luận từ nhiều scandal, chia theo A/B/C.
- Bộ nhãn **Stance** và **Acceptance** ở cấp bình luận.
- Các biểu đồ cho thấy dư luận thay đổi trước và sau lời lên tiếng.
- Mô hình dự đoán diễn biến dư luận ở C từ A và B.

**Tóm lại:** Chúng ta muốn biết **lời lên tiếng được đón nhận như thế nào (B)**, và từ đó cùng với **dư luận trước đó (A)** dự đoán **công chúng có tiếp tục phản đối hay dần ủng hộ nghệ sĩ trong những ngày sau (C)**.
