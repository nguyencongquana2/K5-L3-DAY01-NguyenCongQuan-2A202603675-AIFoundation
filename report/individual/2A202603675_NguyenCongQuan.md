# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

> Đổi tên file này thành `MSSV_HoTen.md` của bạn, ví dụ `2A20260000_NguyenVanAn.md`, rồi điền từng mục. Xoá các dòng hướng dẫn in nghiêng khi xong.

- **MSSV:**2A202603675
- **Họ và tên:**Nguyễn Công Quân
- **SEED** (in ở ô đầu notebook):

## 1. Bằng chứng

| Tệp | Có? |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) — notebook đã chạy, còn output | ☐ |
| [`lab1_report.json`](lab1_report.json) — do ô "Báo cáo cuối" sinh ra | ☐ |
| [`hand_calc.jpg`](hand_calc.jpg) — ảnh tính tay có các bước | ☐ |

## 2. Kết quả chính
Em đã chạy các mô hình ML, DL và Embedding. Phần LLM chưa thực nghiệm do chưa cấu hình API key.

| Mô hình | F1 test | F1 SUB 40 | p95 ms/tin | USD/1k tin |
|---|---:|---:|---:|---:|
| BoW + LogReg | Điền từ notebook | 0.857 | Điền từ notebook | 0 |
| BoW + MLP | Điền từ notebook | 0.947 | Điền từ notebook | 0 |
| Embedding + kNN | Điền từ notebook | 0.974 | Điền từ notebook | 0 |
| Embedding + LogReg | Điền từ notebook | 0.974 | Điền từ notebook | 0 |
| Embedding + MLP head | Điền từ notebook | 0.974 | Điền từ notebook | 0 |

Chi phí 0 trong bảng là không phát sinh phí gọi API, chưa bao gồm chi phí phần cứng và vận hành.
*Chép bảng so sánh cuối Phần 5 (F1 test, F1 SUB 40, p95 ms/tin, USD/1k tin) của chính bạn.*

## 3. Dự đoán và thực tế

*Chọn 3 dự đoán `predict(...)` lệch nhiều nhất so với kết quả. Mỗi cái: bạn đoán gì, ra gì, vì sao lệch.*
1. Em dự đoán BoW + LogReg nằm trên đường Pareto vì xử lý nhanh. Thực tế, BoW + MLP và Embedding + LogReg mới nằm trên đường Pareto. BoW + MLP vừa nhanh hơn vừa có F1 SUB cao hơn BoW + LogReg.

2. Em dự đoán accuracy của BoW trên bộ khó khoảng 60–75%, còn Embedding khoảng 70–85%. Thực tế, BoW + LogReg đạt 54%, BoW + MLP đạt 60%; các mô hình Embedding đạt 60–70%. Em đã đánh giá quá cao khả năng xử lý spam có cách diễn đạt khác dữ liệu train.

3. [Bổ sung một dự đoán đã ghi thật trong notebook, kết quả thực tế và lý do chênh lệch.]

## 4. Phân tích 50 case khó

*Model nào tụt nhiều nhất, ở nhóm case nào? Nêu 2 case cụ thể và cơ chế khiến model sai.*
BoW + LogReg tụt F1 nhiều nhất: từ 0.857 trên SUB thường xuống 0.378 trên bộ khó, giảm 0.479. Mô hình này xử lý kém nhất nhóm thao túng/lừa đảo, chỉ đúng khoảng 12%.

Embedding + kNN tốt nhất trên bộ khó với F1 = 0.727 và accuracy = 70%. Tuy nhiên, cả 5 mô hình đều đoán sai các case #12, #13, #14, #16 và #19 thuộc nhóm spam giọng tự nhiên.

- Case #14: tin nhắn kể chuyện làm việc tại nhà kiếm khoảng £300/ngày, thực chất là mồi nhử việc làm. Cách viết giống chia sẻ cá nhân khiến mô hình nhầm thành ham.
- Case #16: người gửi giả làm bạn cũ rồi giới thiệu việc kinh doanh sản phẩm chăm sóc da. Giọng điệu thân thiện che giấu mục đích quảng cáo, khiến mô hình bỏ sót spam.

Kết quả cho thấy F1 cao trên tập test thường chưa đảm bảo mô hình xử lý tốt những tình huống khó và khác dữ liệu huấn luyện.

## 5. Thử thách cá nhân

*Đề theo SEED của bạn, cách làm, kết quả, giải thích.*
SEED của em là 4211, tương ứng đề số 6: sửa prompt để LLM trả JSON gồm label và reason, viết hàm đọc JSON và so sánh token/chi phí trên 10 tin SUB.

Em chưa hoàn thành thử thách này và chưa có kết quả thực nghiệm do chưa cấu hình API key.
## 6. Phương án cho sản phẩm thật

*Với yêu cầu p95 < 50 ms/tin cho 10 triệu SMS/ngày: chọn phương án nào, dẫn số liệu nào, chấp nhận đánh đổi gì.*
Em chọn BoW + MLP làm phương án ban đầu vì đạt p95 dưới 50 ms trên môi trường thử nghiệm và F1 SUB = 0.947. Embedding + LogReg có F1 SUB cao hơn, đạt 0.974, nhưng vượt giới hạn độ trễ 50 ms.

Đánh đổi là BoW + MLP chỉ đạt F1 = 0.474 và accuracy = 60% trên bộ khó. Vì vậy, cần bổ sung dữ liệu spam giọng tự nhiên, spam né từ khóa và đánh giá lại trước khi triển khai.

Em đề xuất dùng mô hình nhanh xử lý những tin có độ tin cậy cao, chuyển các tin còn lưỡng lự sang mô hình mạnh hơn. Nhánh xử lý bổ sung cần được đo riêng để kiểm tra ảnh hưởng đến SLA.

Với 10 triệu SMS/ngày, tải trung bình khoảng 116 tin/giây. Kết quả p95 hiện tại chưa chứng minh hệ thống đáp ứng được tải thực tế; cần kiểm thử tải, kể cả giờ cao điểm.
## 7. AI log và điều đã học

*Mỗi lần dùng AI: hỏi gì, nhận gì, bạn sửa gì. Sau đó 2–3 câu: điều bạn hiểu ra hôm nay mà trước đó chưa hiểu.*
| Nội dung hỏi AI | Nội dung nhận được | Cách kiểm chứng/điều chỉnh |
|---|---|---|
| Giải thích kết quả và biểu đồ Pareto | Hướng dẫn đọc F1, độ trễ và phương án bị lấn át | Đối chiếu với biểu đồ và các dòng kết quả của notebook |
| Phân tích 50 case khó | So sánh mức giảm F1 và giải thích các lỗi phân loại | Đối chiếu bảng kết quả và danh sách case sai |
| Hướng dẫn tính tay | Các bước tính accuracy, sigmoid, F1, cosine và chi phí | Đối chiếu công thức và ô kiểm tra trong notebook |
| Hướng dẫn viết báo cáo và nộp bài | Gợi ý nội dung Markdown và các bước nộp | Điền theo kết quả thực tế, ghi rõ phần chưa hoàn thành |

Em hiểu hơn rằng điểm test cao chưa chắc phản ánh chất lượng trên các tình huống khó. Khi chọn mô hình, cần xét đồng thời chất lượng, độ trễ và chi phí. Em cũng nhận thấy mô hình dùng Embedding vẫn có thể nhầm spam có giọng điệu giống trò chuyện bình thường.
