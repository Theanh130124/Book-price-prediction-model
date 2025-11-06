# Book-price-prediction-model
Fine-tune (Llama-3.1-8B) Book price prediction based on description 


Quá trình Fine-tuning cho dự án này
1. Dataset
Chúng ta sử dụng bộ dữ liệu từ Hugging Face: 👉 Amazon-Reviews-2023-Books-Meta

2. Data Preparation Process
Quy trình xử lý dữ liệu gồm 4 bước:

Data Cleaning
Loại bỏ các thông tin không liên quan như mã sản phẩm, ký tự đặc biệt và các trường thông tin không giúp ích cho việc dự đoán giá.

Tokenization
Sử dụng tokenizer của mô hình Llama để đếm và giới hạn số lượng token trong mỗi ví dụ.

Quality Filtering
Chỉ giữ lại những mẫu dữ liệu:

Đủ nội dung hữu ích (hơn 150 tokens).

Không quá dài (dưới 160 tokens).

Prompt Standardization
Chuẩn hóa tất cả các prompt theo cấu trúc:

   **Question: "How much does this cost to the nearest dollar?"**
   **Content: (nội dung sách)**
   **Answer: (giá)**
3. Machine Learning Modeling (Before Fine-tuning)
Random model: Sai số: $423.71 (rất kém).

Linear Regression: Sai số giảm còn $52.94.

Word2Vec: Sai số còn $50.94.

HistGradientBoosting: Sai số thấp nhất $50.74.

4. Fine-tuning Techniques
LoRA (Low-Rank Adaptation): Tài liệu: LoRA Guide - Huggingface

QLoRA (Quantized LoRA): Tài liệu: QLoRA Guide - Huggingface

Paper tham khảo: 📄 QLoRA Paper (2023)

5. Important Hyperparameters
5.1 R (Rank)
Ý nghĩa: Xác định kích thước không gian con trong LoRA (số lượng tham số thấp hơn được huấn luyện).

Giá trị càng nhỏ → ít tham số cần cập nhật → nhanh hơn, nhưng có thể giảm hiệu quả fine-tuning.

Docs: PEFT LoRA Parameters

5.2 Alpha
Ý nghĩa: Hệ số scale khuếch đại ảnh hưởng của các tham số LoRA lên trọng số ban đầu.

Alpha càng lớn → ảnh hưởng LoRA càng mạnh mẽ.

Docs: LoRA Alpha Scaling (gốc LoRA paper).

5.3 Target Modules
Ý nghĩa: Các module trong mô hình mà LoRA sẽ áp dụng.

Ví dụ: "q_proj", "k_proj", "v_proj", "o_proj" (các lớp attention projection).

Docs: Choosing Target Modules

6. LLMs Training Process
6.1 Forward Pass
Đưa dữ liệu đầu vào qua mô hình để mô hình dự đoán token tiếp theo.

6.2 Loss Calculation
So sánh đầu ra của mô hình với nhãn thực tế để tính toán loss.

6.3 Backward Pass
Tính toán gradient của loss với respect tới các tham số mô hình.

6.4 Optimization
Cập nhật trọng số theo công thức:

new_weight = old_weight - learning_rate * gradient

📚 Docs: Deep Learning Book - Optimization

7. Training Configuration
7.1 SFTTrainer Configuration
Epochs: Số lần duyệt toàn bộ tập dữ liệu huấn luyện (có thể tăng lên 4+).

Batch size: 1 (per device) × 16 (gradient accumulation) = 16 mẫu/lần update.

7.2 Optimizer
paged_adamw_32bit:

Phiên bản AdamW tối ưu bộ nhớ khi fine-tune mô hình lớn.

Tài liệu: PagedAdamW Optimizer

7.3 Learning Rate
Tham số quyết định tốc độ cập nhật trọng số.

Nếu quá cao → có thể nhảy khỏi điểm tối ưu.

Nếu quá thấp → mô hình học rất chậm hoặc kẹt local minimum.

7.4 Scheduler
Cosine scheduler:

Learning rate giảm theo hình dạng sóng cosine.

Bắt đầu giảm nhẹ → giảm mạnh → ổn định dần.

Docs: Cosine LR Scheduler

7.5 Warmup Ratio
Giai đoạn khởi động, learning rate tăng dần từ thấp đến mức cao nhất rồi mới bắt đầu giảm theo scheduler.

Một số platforms phổ biến tools & framework trong Llms
Hugging Face: Nền tảng phổ biến cho các mô hình, bộ dữ liệu, bảng xếp hạng và cả các ứng dụng
LangChain – Framework mã nguồn mở cung cấp các kết nối nhiều thao tác với Mô Hình Ngôn Ngữ Lớn (LLM) thông qua một API đơn giản
Gradio – Một framework UI cực kỳ đơn giản, cho phép bạn tạo giao diện người dùng prototype chỉ với một dòng mã, không cần kinh nghiệm frontend.
Alternatives – Các lựa chọn thay thế bao gồm Streamlit, Dash và Mesop từ Google
Weights & Biases – Công cụ phân tích và trực quan hóa trong quá trình huấn luyện mô hình.
Google Colab – Viết, đánh giá và chia sẻ các notebook từ xa trên một máy chủ trong Google Cloud.
Modal.com – Nền tảng AI không máy chủ (serverless) dành cho việc triển khai mô hình.
**"Ngoài ra, nếu bạn muốn học sâu hơn về lĩnh vực này, mình khuyến khích các bạn tham gia các khóa học uy tín để nắm vững nền tảng trước khi đi sâu vào từng khía cạnh."

https://nvdam.widen.net/s/wlbgbqr7cj/nvidia-learning-training-course-catalog

Tai lieu tham khao trong video: Llm course

Lưu ý: Toàn bộ những gì mình chia sẽ đều là những gì mình học và tổng hợp được. No COMMERCIAL intent!!