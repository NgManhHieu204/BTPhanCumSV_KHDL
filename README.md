# Phân Cụm Sinh Viên Bằng Thuật Toán K-Means
# Họ và Tên: Nguyễn Mạnh Hiếu - MSSV: K225480106020 - Lớp: K58KTP

## Giới thiệu dự án

Dự án này là bài tập thực hành Chương 6 thuộc môn **Khoa học dữ liệu**. Mục tiêu của bài toán là áp dụng thuật toán học máy không giám sát **K-Means Clustering** để phân tách danh sách sinh viên trong lớp thành **3 nhóm (K=3)** riêng biệt dựa trên bảng điểm hệ số 4 tích lũy từ học kỳ 1 năm nhất đến hết học kỳ 2 năm tư.

## Video Báo Cáo Chạy Chương Trình

**[Xem Video Thuyết Trình Và Chạy Code Tại Đây](https://youtu.be/NZKRPQ2brjc)**

## Cấu Trúc Thư Mục Dự Án
```text
├── diemhe4_sinhvien.csv         # File dữ liệu bảng điểm gốc của lớp (định dạng CSV)
├── phancumsvien.py              # File mã nguồn Python xử lý dữ liệu, chạy mô hình và vẽ biểu đồ
├── dactrung.csv                 # File bảng đặc trưng (Features) sau khi trích xuất
├── kqphancum.xlsx               # File kết quả phân cụm cuối cùng
├── clustering_visualization.png # Biểu đồ trực quan hóa 6 khía cạnh phân tích chuyên sâu
└── README.md                    # File mô tả dự án này
```

## Phương Pháp Tiếp Cận Vầ Tiêu Chí Phân Cụm
Thay vì sử dụng trực tiếp điểm số thô của 52 môn học (làm tăng số chiều dữ liệu và gây nhiễu cho mô hình khoảng cách), dự án áp dụng kỹ thuật rút trích đặc trưng dựa trên các góc nhìn sư phạm để tạo ra **4 đặc trưng cốt lõi** đưa vào mô hình phân cụm:

1. **GPA (Điểm trung bình tích lũy):** Đánh giá năng lực học lực tổng thể của sinh viên trên thang điểm 4.
2. **StdDev (Độ lệch chuẩn điểm số):** Đo lường mức độ học đều hay học lệch của từng cá nhân.
   - *Độ lệch chuẩn thấp:* Sinh viên học đều tất cả các môn.
   - *Độ lệch chuẩn cao:* Sinh viên học lệch (có môn điểm rất cao nhưng có môn điểm rất thấp hoặc trượt môn).
3. **Tech_Avg (Điểm trung bình khối Kỹ thuật):** Trung bình điểm các môn liên quan đến chuyên ngành, lập trình, logic số và phần cứng (ví dụ: *Lập trình Python, Trí tuệ nhân tạo và học máy, Cấu trúc dữ liệu và giải thuật, Hệ điều hành...*).
4. **Theory_Avg (Điểm trung bình khối Lý thuyết):** Trung bình điểm các môn đại cương, lý luận chính trị và kỹ năng xã hội (ví dụ: *Triết học Mác - Lênin, Tư tưởng Hồ Chí Minh, Tiếng Anh, Pháp luật đại cương, Giao tiếp kỹ thuật...*).
5. **Tech_Theory_Ratio (Chỉ số thiên hướng):** Hiệu số giữa `Tech_Avg` và `Theory_Avg`. Chỉ số này càng lớn về phía dương thể hiện sinh viên có thiên hướng mạnh về thực hành kỹ thuật/code, ngược lại thể hiện sinh viên học tốt các môn lý thuyết xã hội hơn.

## Mô Tả Dữ Liệu Và Quy Trình Tiền Xử Lý
- **Dữ liệu thô (`diemhe4_sinhvien.csv`):** Gồm danh sách 71 sinh viên ban đầu và danh mục 52 môn học qua các năm.
- **Tiền xử lý dữ liệu (Data Cleaning & Imputation):**
  - Tự động quét và định vị chính xác vùng dữ liệu điểm, bỏ qua các dòng tiêu đề rác của file Excel gốc.
  - Xử lý các trường hợp sinh viên trùng tên bằng cách tự động gắn hậu tố định danh nhằm tránh xung đột cấu trúc bảng trong Pandas.
  - **Lọc nhiễu:** Loại bỏ **sinh viên** bỏ trống dữ liệu hoặc thiếu điểm quá 50% số môn học (các trường hợp chưa thực hiện nhập điểm).
  - **Xử lý dữ liệu khuyết thiếu:** Với số sinh viên hợp lệ còn lại, các ô điểm bị trống (`NaN`) do chưa học tới hoặc trượt môn được điền tự động bằng **điểm trung bình của chính môn học đó** để tránh làm sai lệch khoảng cách hình học của mô hình.
  - **Chuẩn hóa dữ liệu (`StandardScaler`):** Đưa 4 đặc trưng chính về cùng phân phối chuẩn (Mean = 0, Std = 1). Đây là bước bắt buộc đối với thuật toán K-Means sử dụng khoảng cách Euclidean nhằm triệt tiêu sự chênh lệch trọng số giữa các thang đo.
