# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trần Minh Hiếu   Nhóm: ______   Ngày: 2026-09-16

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.



## 1. Nhãn của tôi



| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 / 20 (`files: 20`) |
| Số skeleton | 27 (`people: 27`; 7 ảnh có 2 người: train_01, 03, 04, 14, 15, 16, 19) |
| v=2 / v=1 / v=0 | 319 / 119 / 21 (tổng 459 = 27 × 17, không thiếu khớp nào; trung bình 16.22 khớp có v > 0 mỗi người) |
| Thời gian trung bình mỗi ảnh | 10 phút  |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear` - 14/27 = 52% (v=2 13, v=1 14, v=0 0)
2. `right_ear` - 13/27 = 48% (v=2 14, v=1 13, v=0 0)
3. `right_hip` - 11/27 = 41% (v=2 16, v=1 11, v=0 0); kế đó `left_hip` 37%, hai cổ tay 33%, `left_knee` 33%

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Không. Tai đứng đầu bảng vì hay bị che, không phải vì khó đặt: 6/20 ảnh có mũ bảo hiểm
hoặc mũ (train_02, 04, 06, 11, 15, 18) và 4 ảnh có người quay lưng/ngửa mặt (train_06, 09, 14,
16), nên tai mất vành nhưng vị trí vẫn bị mắt + mũi khoá chặt trong vài pixel. Khớp **khó xác
định vị trí giải phẫu** thật sự là hông: `right_hip` 41% / `left_hip` 37% v=1, và ngay cả 17 lần
v=2 cũng chỉ là "thấy cạp quần", không thấy khớp - ví dụ train_11 (con mèo trùm thân dưới, phải suy
hông từ tỉ lệ vai-đầu) và train_14 người thứ 1 (ngồi xổm, hông gập). Nhóm thứ hai là cổ tay sau
ghi-đông (train_02, 04, 10, 15) - bị che nhưng ước lượng tốt nhờ đường cẳng tay còn thấy. Cổ chân
không nằm trong top `%v=1` nhưng chiếm 16/21 lần `v=0` vì 6 ảnh cắt người ở mép dưới (train_01, 04,
07, 10, 11, 13) - đó là ca "ra khung", không phải ca khó.

## 2. Chấm với gold


| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.933  | 0.935 |
| OKS@0.50 | 0.931 | 0.95 |
| OKS@0.75 | 0.931 | 0.951 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

Tôi đã sửa gì giữa hai lần chạy (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):
Không phải sửa gì, 2 người gold báo thiếu là ở ảnh 13, tấm này 2 người đằng sau là quá mờ để gán skeleton cho chuẩn


**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?
Tôi không bị nhầm trái phải ảnh nào cả, đơn giản tôi mô phỏng bản thân tôi là người trong ảnh nên kéo trái phải sẽ rất chuẩn


## 3. Kiểm chéo

Bạn cùng nhóm: ______


Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |


Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:


- Đề xuất sẵn (mục 4 GUIDELINE_MINI): *một khớp chỉ được `v=0` khi toạ độ ước lượng theo hướng
  chi rơi ngoài `[0, W) × [0, H)`; mọi trường hợp còn trong khung - bị mũ, vật, người hay chính
  cơ thể che - đều `v=1` và có chấm.* Kiểm chứng: với mỗi khớp `v=0`, khớp liền kề còn chấm được
  phải cách mép ảnh ít hơn một độ dài đoạn xương đo trên chính người đó. Trong nhãn của tôi, 21/21
  lần `v=0` đều là cổ chân/đầu gối/cổ tay của 8 người ở 6 ảnh cắt mép (train_01, 04, 07, 10, 11,
  13) và thoả điều kiện này (ví dụ train_07: đầu gối y ≈ 580/640, đùi ≈ 150 px → cổ chân ngoài
  khung).


## 4. Model


| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook


1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?

   `pose_mAP50-95` tăng nhẹ (+0.0055). Dù chỉ với 20 ảnh (27 người), model đã học được đôi chút phong cách gán nhãn của tập dữ liệu mới (nhiều khớp bị che `v=1` như tai, hông). Tuy nhiên, chỉ số hộp (box_mAP) lại giảm nhẹ do kích thước tập dữ liệu cực kỳ nhỏ, khiến mạng dễ bị nhiễu đối với việc nhận diện hình hộp, hoặc do hiện tượng overfitting nhẹ trên 20 ảnh.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?

    `box_mAP50_95` (0.8041) cao hơn `pose_mAP50_95` (0.6908) với độ chênh lệch là 0.1133. Model tìm *người* (box) dễ hơn tìm *khớp* (pose). Hộp (box) chỉ cần thỏa mãn Intersection over Union (IoU) ở mức vùng ảnh tổng quát, trong khi Pose cần phải xác định chính xác tọa độ của 17 điểm hội tụ chặt chẽ theo OKS.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

    test_06 model vẽ hai người sát nhau có thể nhầm lẫn tay chân, hoặc lệch nhẹ các khớp tay/chân bị che khuất

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

     Ảnh `train_06` có OKS thấp nhất (0.643). Một vài bức ảnh khác model nhận diện ra số lượng người khác với nhãn gốc (như train_03 model đoán 4 người / bạn gán 2 người). Để quyết định ai đúng, cần chạy `tools/visualize_pose.py` kiểm tra xem model có bị ảo giác (hallucinate) người thừa không hay bản thân nhãn bỏ sót người.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?

  Ảnh model thấp nhất ở câu 4; ứng viên khó nhất theo nhãn của tôi: train_11 - 7 khớp `v=1` + 2 `v=0` vì con mèo và mép ảnh, và train_10 - người nằm ngang trên xe, 3 `v=0` và đầu nghiêng 90°). Những bức khó gán do thiếu góc nhìn, bị che khuất hoặc tư thế phức tạp (như train_10, train_11) cũng khiến model gặp nhiều khó khăn tương tự (suy giảm độ chính xác OKS), cho thấy những hình ảnh không tuân theo bố cục thông thường hoặc có ngoại cảnh can thiệp mạnh là thử thách chung cho cả người đánh nhãn và AI.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

train_04, người thứ 1 (người bên trái, áo xám, mũ bảo hiểm đen), `right_wrist` → `v=0`; đối
chứng cùng người: `left_wrist` → `v=1`.** Bằng chứng: khuỷu tay phải đã chấm được ở (13, 449) px
trên ảnh 640 × 457 - cách mép trái 13 px, mép dưới 8 px - và cánh tay đang duỗi tiếp về phía
trái-dưới nắm ghi-đông ngoài khung. Cẳng tay trái của chính người này đo được ≈ 100 px (khuỷu
(263, 350) → cổ tay (365, 366)); đặt độ dài đó từ khuỷu phải theo hướng cánh tay thì cổ tay rơi ở
x < 0 hoặc y > 457, tức đã ra khỏi khung thật sự, nên `v=0` và không đặt chấm. Ngược lại cổ tay
trái nắm ghi-đông ở giữa ảnh: găng tay nhô ra sau ghi-đông, khớp bị ghi-đông che nhưng chắc chắn
còn trong khung → `v=1`, chấm tại điểm cẳng tay gặp bàn tay ở (365, 366). Cùng logic đó, hai đầu
gối và hai cổ chân của người này `v=0` vì mép dưới cắt ngay dưới hông (hông ở y ≈ 452-455/457,
box chạm mép), còn hai hông vẫn trong khung nên có chấm (`right_hip` `v=1` vì bị thân xe che,
`left_hip` `v=2`).
