# Phân tích các yếu tố ảnh hưởng và Dự đoán kết quả học tập (Student Performance Factors Analysis)

Dự án này tập trung vào việc nghiên cứu, kiểm định giả thuyết thống kê và xây dựng mô hình hồi quy tuyến tính bội nhằm tìm ra các yếu tố tác động lớn nhất đến điểm thi (`Exam_Score`) của học sinh, đồng thời đánh giá hiệu suất và giới hạn của mô hình dự đoán. Nghiên cứu được thực hiện trên tập dữ liệu gồm **6,607 học sinh** với **20 thuộc tính** khác nhau.

## 📌 Mục tiêu & Câu hỏi nghiên cứu
1. **Kiểm định giả thuyết (RQ1):** Học sinh bị khuyết tật học tập (`Learning_Disabilities`) có điểm thi thấp hơn đáng kể so với học sinh bình thường không? Sự chênh lệch này có ý nghĩa thống kê hay do ngẫu nhiên?
2. **Xây dựng mô hình dự đoán (RQ2):** Yếu tố nào trong số các biến số định lượng (*Hours_Studied, Attendance, Sleep_Hours, Previous_Scores, Tutoring_Sessions, Physical_Activity*) có tương quan mạnh nhất với điểm thi? Mô hình hồi quy tuyến tính bội (MLR) dự đoán chính xác đến mức nào?
3. **Đánh giá giới hạn mô hình (RQ3):** Mô hình MLR có sự khác biệt về độ chính xác giữa các nhóm điểm không? (Ví dụ: nhóm điểm trung bình vs. nhóm điểm cao).

---

## 🛠 Phương pháp nghiên cứu & Kiến trúc mã nguồn

Dự án được triển khai bằng ngôn ngữ **Python** trong môi trường Jupyter Notebook, sử dụng thư viện `datascience` (thư viện chuẩn từ khóa học Data 8 của UC Berkeley) kết hợp với `numpy` và `matplotlib`.

### 1. Kiểm định giả thuyết bằng Permutation Test
- **Thống kê kiểm định (Test Statistic):** Hiệu số trung bình điểm thi giữa nhóm học sinh không bị khuyết tật học tập và nhóm có khuyết tật học tập.
- **Giả thuyết:**
  - $H_0$: Điểm trung bình hai nhóm bằng nhau, sự chênh lệch quan sát được chỉ là ngẫu nhiên.
  - $H_1$: Điểm trung bình của nhóm không bị khuyết tật học tập cao hơn nhóm còn lại.
- **Phương pháp:** Xáo trộn nhãn (shuffled labels) ngẫu nhiên **5,000 lần** để mô phỏng phân phối dưới giả thuyết $H_0$ và tính toán giá trị $p\text{-value}$.

### 2. Mô hình Hồi quy tuyến tính bội (Multiple Linear Regression)
- **Biến mục tiêu:** `Exam_Score` (Thang điểm từ 55 đến 101).
- **Biến độc lập (6 thuộc tính số):** `Hours_Studied`, `Attendance`, `Sleep_Hours`, `Previous_Scores`, `Tutoring_Sessions`, `Physical_Activity`.
- **Phân chia dữ liệu:** 50% cho tập huấn luyện (Train - 3,304 mẫu) và 50% cho tập kiểm thử (Test - 3,303 mẫu).
- **Tối ưu hóa:** Sử dụng hàm `minimize` để tìm các trọng số (slopes) tối ưu sao cho lỗi bình phương trung bình gốc (**RMSE**) là nhỏ nhất.
- **Đánh giá sai số:** Sử dụng đồ thị phần dư (Residual Plot - Thực tế vs. Dự báo) để kiểm tra các giả định của mô hình hồi quy.

---

## 📊 Kết quả nghiên cứu nổi bật

### Phần 1: Ảnh hưởng của khuyết tật học tập
- **Điểm trung bình:** Nhóm không khuyết tật đạt **67.35 điểm**, trong khi nhóm có khuyết tật đạt **66.27 điểm** (Chênh lệch quan sát: **1.08 điểm**).
- **Kết quả kiểm định:** Qua 5,000 lần hoán vị, thu được $p\text{-value} = 0.0$. Vì $p\text{-value} < 0.05$, chúng ta **bác bỏ giả thuyết $H_0$**. Điểm số thấp hơn của nhóm học sinh khuyết tật học tập thực sự có ý nghĩa thống kê.

### Phần 2: Mối tương quan & Hiệu suất mô hình MLR
- Hệ số tương quan ($r$) của các thuộc tính với điểm thi trên toàn bộ tập dữ liệu:
  - **`Attendance` (Tỷ lệ đi học): 0.581** (Tương quan mạnh nhất)
  - `Hours_Studied` (Giờ học): 0.445
  - `Previous_Scores` (Điểm thi trước đây): 0.175
  - `Tutoring_Sessions` (Số buổi phụ đạo): 0.157
  - `Physical_Activity` (Hoạt động thể chất): 0.028
  - `Sleep_Hours` (Giờ ngủ): -0.017
- **RMSE tối ưu:** Đạt **4.39** trên tập Train và **4.46** trên tập Test. Điều này chứng tỏ mô hình không bị quá khớp (overfitting) và có khả năng tổng quát hóa tốt.

### Phần 3: Thảo luận về giới hạn của mô hình (Underestimation ở điểm cao)
- **Nhóm điểm < 80:** Mô hình hoạt động rất tốt, các điểm phần dư (residuals) phân tán đều xung quanh trục 0.
- **Nhóm điểm $\ge$ 80:** Mô hình có xu hướng **dự đoán thấp hơn thực tế (underestimate)**. Khi phân tích sâu nhóm học sinh điểm cao, hệ số tương quan tuyến tính giảm mạnh (ví dụ: tương quan của điểm thi trước đây `Previous_Scores` tăng lên 0.472 nhưng các biến khác biến động không tuyến tính).
- **Kết luận:** Mối quan hệ giữa các biến số và điểm thi ở phân khúc xuất sắc không còn mang tính tuyến tính đơn thuần.

---
