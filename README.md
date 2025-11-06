# 📘 Book Price Prediction Model  

### 🎯 Fine-tuning Llama 3.1-8B for Predicting Book Prices from Descriptions

---

## 🧠 Overview
Dự án này tập trung vào **fine-tuning mô hình ngôn ngữ Llama 3.1-8B** để **dự đoán giá sách** dựa trên mô tả nội dung.  
Toàn bộ pipeline bao gồm các bước từ chuẩn bị dữ liệu, huấn luyện mô hình cho đến đánh giá kết quả.

---

## 📊 1. Dataset
- **Nguồn dữ liệu:** [Amazon-Reviews-2023-Books-Meta](https://huggingface.co/datasets) từ Hugging Face.  
- Dữ liệu chứa thông tin mô tả sản phẩm, tiêu đề, và giá bán, phục vụ cho bài toán **price prediction**.

---

## 🧹 2. Data Preparation Pipeline

### 🔸 Step 1: Data Cleaning  
- Loại bỏ các thông tin không cần thiết như mã sản phẩm, ký tự đặc biệt, hoặc các trường không giúp ích cho việc dự đoán giá.

### 🔸 Step 2: Tokenization  
- Sử dụng tokenizer của **Llama** để giới hạn số lượng token trong mỗi mẫu.  
- Giới hạn:
  - **Tối thiểu:** 150 tokens (đảm bảo đủ nội dung).  
  - **Tối đa:** 160 tokens (tránh quá dài).

### 🔸 Step 3: Quality Filtering  
- Chỉ giữ lại những mẫu dữ liệu có nội dung chất lượng và giá hợp lệ.

### 🔸 Step 4: Prompt Standardization  
Mọi mẫu dữ liệu đều được chuẩn hóa theo cấu trúc:


---

## 🤖 3. Machine Learning Baselines (Before Fine-tuning)

| Mô hình | Sai số (MAE) | Ghi chú |
|----------|---------------|---------|
| Random Model | $423.71 | Rất kém |
| Linear Regression | $52.94 | Cải thiện rõ |
| Word2Vec | $50.94 | Ổn định |
| HistGradientBoosting | **$50.74** | Tốt nhất trước fine-tune |

---

## 🧩 4. Fine-tuning Techniques

- **LoRA (Low-Rank Adaptation):** Giảm số tham số cần huấn luyện, tăng tốc độ fine-tuning.  
  🔗 [LoRA Guide - Hugging Face](https://huggingface.co/docs/peft/conceptual_guides/lora)

- **QLoRA (Quantized LoRA):** Tối ưu bộ nhớ cho mô hình lớn.  
  📄 [QLoRA Paper (2023)](https://arxiv.org/abs/2305.14314)

---

## ⚙️ 5. Key Hyperparameters

### 🔹 R (Rank)
- Xác định kích thước không gian con trong LoRA.  
- Nhỏ hơn → nhanh hơn, ít tham số hơn, nhưng độ chính xác có thể giảm.  
📘 [Docs: PEFT LoRA Parameters](https://huggingface.co/docs/peft/main/en/package_reference/lora)

### 🔹 Alpha
- Hệ số khuếch đại ảnh hưởng của tham số LoRA lên trọng số gốc.  
📘 [Docs: LoRA Alpha Scaling](https://arxiv.org/pdf/2106.09685.pdf)

### 🔹 Target Modules
- Các lớp trong mô hình mà LoRA được áp dụng: `"q_proj"`, `"k_proj"`, `"v_proj"`, `"o_proj"`.  
📘 [Docs: Choosing Target Modules](https://huggingface.co/docs/peft/conceptual_guides/lora)

---

## 🧮 6. LLMs Training Workflow

1. **Forward Pass:** Dữ liệu đi qua mô hình → dự đoán token tiếp theo.  
2. **Loss Calculation:** So sánh đầu ra với nhãn thật để tính loss.  
3. **Backward Pass:** Tính gradient theo các tham số.  
4. **Optimization:**  


📘 [Deep Learning Book – Optimization](https://www.deeplearningbook.org/)

---

## ⚙️ 7. Training Configuration

### 🔸 SFTTrainer
- **Epochs:** 3–4  
- **Batch size:** 1 × 16 (gradient accumulation)  
- **Optimizer:** `paged_adamw_32bit` – tối ưu bộ nhớ khi fine-tune mô hình lớn  
📘 [PagedAdamW Optimizer](https://huggingface.co/docs/transformers/main/en/main_classes/optimizer_schedules#paged-adamw)

### 🔸 Learning Rate
- Quá cao → nhảy khỏi điểm tối ưu  
- Quá thấp → học chậm hoặc kẹt local minimum  

### 🔸 Scheduler
- **Cosine Scheduler:** giảm learning rate theo sóng cosine  
📘 [Cosine LR Scheduler](https://arxiv.org/abs/1608.03983)

### 🔸 Warmup Ratio
- Giai đoạn khởi động: learning rate tăng dần trước khi giảm theo scheduler.

---

## 🧰 8. Useful Tools & Frameworks for LLMs

| Công cụ | Mô tả |
|----------|--------|
| 🤗 **Hugging Face** | Mô hình, datasets, leaderboard, demo app |
| 🔗 **LangChain** | Kết nối nhiều thao tác với LLM qua API đơn giản |
| 🎛️ **Gradio** | Tạo giao diện demo UI nhanh gọn |
| 📊 **Weights & Biases** | Phân tích & trực quan hóa quá trình huấn luyện |
| 🧩 **Streamlit / Dash / Mesop** | Alternatives để xây dựng giao diện tương tác |
| ☁️ **Google Colab** | Notebook Cloud miễn phí |
| ⚡ **Modal.com** | Nền tảng serverless triển khai mô hình AI |

---

## 🎓 9. Học liệu & Tài nguyên
> “Nếu bạn thật sự muốn đào sâu vào lĩnh vực LLMs và Fine-tuning, hãy bắt đầu từ những khóa học uy tín.”

📚 **NVIDIA Learning Catalog:**  
🔗 [https://nvdam.widen.net/s/wlbgbqr7cj/nvidia-learning-training-course-catalog](https://nvdam.widen.net/s/wlbgbqr7cj/nvidia-learning-training-course-catalog)

🎥 **Llm Course (Video reference)**  

---

## 🧾 10. Notes
> Tất cả kiến thức và nội dung trong dự án này được tổng hợp từ quá trình học tập và nghiên cứu cá nhân.  
> **Không có mục đích thương mại – purely educational use.**

---

### ✍️ Author: **Trần Thế Anh**  
🚀 Developer | AI Engineer (NLP & Computer Vision)  
📧 *Contact:* updating...
