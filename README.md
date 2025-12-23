# Dự án Dịch Máy Song Ngữ Anh - Việt

## Giới thiệu
Đây là dự án bài tập lớn môn Xử lý ngon ngữ tự nhiên - UET

Dự án này triển khai hai bài toán dịch máy song ngữ Anh – Việt:

1. **Bài toán 1**: Xây dựng mô hình Transformer **from scratch** .  
2. **Bài toán 2**: Dịch máy lĩnh vực Y tế với các mô hình Tiền huấn luyện (Pre-training) giới hạn

Dự án tập trung vào việc:
- Tiền xử lý dữ liệu để đảm bảo chất lượng và đồng nhất của cặp câu song ngữ.  
- Huấn luyện và tối ưu Transformer từ đầu.  
- Fine-tune mô hình pre-trained cho lĩnh vực y tế.  
- Đánh giá mô hình bằng các metric BLEU, CHRF, COMET, TER, METEOR.  

---

## Thành viên nhóm
- Trịnh Trung Đức  
- Nguyễn Đoàn Hoài Thương  
- Nguyễn Thị Thanh Tuyền

---

## Bài toán 1: Transformer from scratch

### 1. Tiền xử lý dữ liệu
- Lọc câu quá ngắn/quá dài và tỉ lệ từ bất thường giữa tiếng Anh và tiếng Việt.  
- Loại bỏ trùng lặp.  
- Kiểm tra ngôn ngữ bằng regex + langdetect.  
- Kết quả giữ lại ~128,068/133,317 cặp câu (~96%).  

### 2. Huấn luyện mô hình
- Tokenizer: SentencePiece BPE (~16,000 token).  
- Optimizer: Adam với Noam learning rate scheduler.  
- Regularization: Weight Decay, Dropout 0.3.  
- Loss: Cross-Entropy với Label Smoothing 0.1.  
- Gradient Clipping L2-norm, CLIP=1.0.  
- Batch padding + attention mask cho hiệu quả GPU.  

### 3. Decoding
- Greedy decoding: nhanh, ổn định trong huấn luyện.  
- Beam search: cải thiện BLEU và COMET, beam size=3 là tối ưu.  

### 4. Kết quả đánh giá
| Decoding | BLEU  | chrF  | COMET  |
|----------|--------|--------|---------|
| Greedy   | 28.21  | 46.64  | 73.63   |
| Beam 3   | 28.95  | 47.41  | 74.79   |

---

## Bài toán 2: Fine-tuning mô hình Qwen3 cho y tế

### 1. Dữ liệu
- Tập VLSP 2025: ~344,426 cặp câu Anh-Việt sau lọc.  
- Dữ liệu kiểm thử: 3,000 cặp câu.  
- Lọc nhiễu, trùng lặp, kiểm tra độ dài và tỉ lệ câu.  

### 2. Huấn luyện
- Mô hình: Qwen3 Unsloth 1.7B, 4-bit, pre-trained.  
- Giai đoạn 1: Huấn luyện 2,000 bước, batch size=64.  
- Giai đoạn 2: Huấn luyện thêm 2,000 bước với dropout 0.05.  
- Giai đoạn 3: Huấn luyện riêng từng chiều 1,000 bước.  

### 3. Kết quả đánh giá
| Chiều | BLEU  | chrF  | TER  | METEOR  | COMET  |
|-------|--------|--------|-------|-----------|---------|
| EN → VI | 41.9  | 57.33  | 48.85 | 62.91     | 0.8539  |
| VI → EN | 32.75 | 55.73  | 62.63 | 62.15     | 0.8205  |

### 4. Nhận xét
- Mô hình giữ tốt ý nghĩa câu, đặc biệt chiều VI → EN.  
- Beam search cải thiện chất lượng dịch chiều dài phức tạp.  
- Một số hạn chế khi dịch các cụm từ chuyên ngành dài hoặc phức tạp.  

---

## Cài đặt và chạy (Kaggle Notebook)

Các notebook hiện tại được thiết kế để chạy trực tiếp trên **Kaggle**. Trước khi chạy, cần chuẩn bị:

1. Tạo một Notebook mới trên Kaggle, upload file `.ipynb` từ repo vào Notebook vừa tạo.
2. Thêm các dataset cần thiết qua **Kaggle Datasets**:
   - [[IWSLT Dataset](https://www.kaggle.com/datasets/tuannguyenvananh/iwslt15-englishvietnamese)](link) – cho bài toán dịch máy từ scratch.
   - [VLSP 2025 Medical MT](link) – cho fine-tune mô hình y tế.
3. Chạy các cell lần lượt theo thứ tự:
   - Tiền xử lý dữ liệu
   - Huấn luyện mô hình
   - Đánh giá kết quả


