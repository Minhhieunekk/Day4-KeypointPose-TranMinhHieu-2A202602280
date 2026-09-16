# Mini guideline - nhóm: ______  |  người gán: Tran Minh Hieu |  ngày: 2026-09-16

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.


## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Hông = **điểm xoay của đùi**, không phải cạp quần. Đặt chấm trên đường ngang qua **đỉnh cạp quần / thắt lưng**, lệch khỏi trục giữa thân một khoảng ≈ **½ khoảng cách hai vai** về mỗi bên. Nếu thấy được đường viền thân ở tầm thắt lưng (cạp quần, nếp gấp hông, tay chống hông) → `v = 2`. Nếu vùng thắt lưng bị vật/người khác/áo khoác dài trùm kín → `v = 1`, vẫn chấm theo quy tắc trên. | Hông không bao giờ "nhìn thấy" theo nghĩa đen; nếu để mỗi người tự ước lượng thì hai người sẽ chấm lệch nhau 20-30 px theo phương dọc và model học ra một cái hông "nhảy" giữa cạp quần và đùi. Chốt một mốc giải phẫu chung (cạp quần + ½ vai) thì ai chấm cũng rơi vào cùng một vùng. Trong bài: `left_hip` 37% / `right_hip` 41% `v = 1` - đúng là hai khớp bị che nhiều nhất sau tai. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Chỉ `v = 2` khi **thấy được đường viền vành tai**. Mũ bảo hiểm kín, mũ lưỡi trai kéo thấp, tóc phủ, hay mặt quay đi (thấy gáy) → `v = 1`, đặt chấm ở **ngang tầm mắt, lùi về sau khoé mắt ngoài ≈ 1 bề rộng mắt**, nằm trên bề mặt mũ/tóc chứ không đặt sâu vào trong đầu. Không dùng `v = 0` cho tai chỉ vì "không thấy". | Vị trí tai bị ràng buộc rất chặt bởi mắt + mũi, nên dù không thấy vẫn ước lượng được trong ±5 px. Nếu bỏ `v = 0` thì khớp đó biến mất khỏi OKS và model không học được "tai nằm dưới mũ bảo hiểm", trong khi 6/20 ảnh của bộ này có mũ bảo hiểm hoặc mũ (train_02, 04, 06, 11, 15, 18). Đây là lý do `left_ear` 52% / `right_ear` 48% là hai khớp có `%v = 1` cao nhất. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Kẻ một đường tưởng tượng nối tiếp chi theo hướng đang thấy. Khớp mà đường đó **cắt ra ngoài mép ảnh** → `v = 0`, không chấm. Khớp còn **nằm trong khung nhưng bị bàn/xe/vật che** → `v = 1`, vẫn chấm. Box YOLO do `coco_kp_to_yolo_pose.py` tính từ các chấm đã đặt, nên không cần kéo box tới mép. Không gán `v = 0` cho khớp chỉ vì nó nằm ngay sát mép; chỉ khi thực sự vượt mép. | Đây là chỗ duy nhất được phép dùng `v = 0`. Toàn bộ 21 lần `v = 0` trong bài đều rơi vào cổ chân (8+8), đầu gối (2+2) và một cổ tay - tất cả ở người bị cắt mép dưới/mép trái: train_01 (cả hai người), train_04, train_07, train_10, train_11, train_13. Nếu lẫn "che" với "ra khung", khớp bị che sẽ bị loại khỏi OKS và bảng đếm `v = 0` sẽ cao bất thường ở giữa ảnh (lỗi số 3 slide 46). |
| Cổ tay nằm sau tay lái / sau thân mình | `v = 1`. Kéo dài đường **khuỷu tay → cẳng tay** đến chỗ bàn tay nắm (ghi-đông, hộp pizza, cốc) và đặt chấm tại **điểm cẳng tay gặp bàn tay**, ngay trước vật che. Nếu thấy găng tay/ngón tay nhô ra sau ghi-đông thì vẫn `v = 1` (khớp cổ tay bị che, chỉ thấy bàn tay). | Cổ tay là khớp có `%v = 1` cao thứ ba (33% cả hai bên) vì nhiều ảnh là xe đạp/mô-tô (train_02, 04, 06, 10, 15, 18). Đường cẳng tay từ khuỷu tay gần như luôn thấy được, nên vị trí cổ tay bị che ước lượng được rất tốt; xoá nó đi là mất thông tin rẻ nhất mà bài này có. |
| Hai người chồng lên nhau | Gán **xong hẳn người đứng trước** (người có nhiều khớp `v = 2` hơn), rồi mới vẽ skeleton người sau. Một khớp thuộc về **cơ thể mà xương nối nó về thân**: bật đường nối, nếu xương chạy qua thân người kia là nhầm người. Khớp của người sau bị người trước che → `v = 1` đặt tại vị trí ước lượng **phía sau** người trước (không "mượn" khớp của người trước). Box hai người được phép chồng lên nhau. | Đây là nguồn lỗi `nham_nguoi` - lỗi nặng thứ hai sau đảo trái/phải. Làm từng người một và bật xương là cách duy nhất bắt được nó bằng mắt trong 1 giây (Lượt 1, Chặng 4). Ảnh có 2 người trong bài: train_01, 03, 04, 14, 15, 16, 19. |
| Người nhỏ đến mức nào thì không gán nữa | Gán mọi người có **đầu và thân phân biệt được** và box cao ≥ **~60 px** (≈ 1/8 chiều cao ảnh). Dưới ngưỡng đó, hoặc chỉ là một mảng màu không phân biệt được tay/chân, thì không tạo skeleton. Người nhỏ nhất đã gán trong bài: train_19 người thứ 2 (box 0.11 × 0.48 ≈ 55 × 160 px trên ảnh 500 × 333) - vẫn đủ thấy mũ, vai, khuỷu, đầu gối. | Với người dưới ~60 px, sai số đặt chấm 3-4 px đã lớn hơn sigma của khớp đó trong OKS, nên nhãn tạo ra chủ yếu là nhiễu. Bộ ảnh đã được chọn để mọi người đủ lớn; trong 20 ảnh không có ca nào rơi dưới ngưỡng, nhưng phải chốt ngưỡng để hai người trong nhóm không lệch nhau về **số skeleton** (lỗi thiếu/thừa người). |

Ảnh mẫu cho từng luật (từ nhãn đã khoá):

| Luật | Ảnh mẫu | Nhìn vào đâu |
| --- | --- | --- |
| Hông | ![train_11](reports/guideline_samples/train_11.jpg) | Người thứ 1: con mèo trùm toàn bộ vùng thắt lưng; hai hông (chấm vàng) đặt ngang tầm mép bàn-áo, cách nhau ≈ ½ vai. `v = 1` cả hai hông, hai đầu gối. |
| Tai bị mũ che | ![train_04](reports/guideline_samples/train_04.jpg) | Cả hai người đội mũ bảo hiểm kín: 4 tai đều `v = 1`, chấm nằm ngang tầm mắt, trên bề mặt mũ. Mắt/mũi vẫn `v = 2` vì thấy qua khe mũ. |
| Người bị cắt mép | ![train_01](reports/guideline_samples/train_01.jpg) | Cả hai người bị cắt ở mép dưới: đầu gối còn trong khung → có chấm; cổ chân đã vượt mép → `v = 0`, không chấm (đầu gối ở y ≈ 420/427, ống chân dài ≈ 90-100 px). |
| Cổ tay sau tay lái | ![train_02](reports/guideline_samples/train_02.jpg) | Người đạp xe: cổ tay trái nắm ghi-đông, khớp bị găng/ghi-đông che → đặt tại điểm cẳng tay gặp bàn tay. Mặt quay đi + mũ bảo hiểm → mũi, mắt, tai đều `v = 1`. |
| Hai người chồng nhau | ![train_16](reports/guideline_samples/train_16.jpg) | Hai cầu thủ bật nhảy, đầu sát nhau; mắt/tai phải người áo đỏ quay đi và lọt sau đầu người áo trắng → 2 khớp đó `v = 1`, đặt trên đầu người áo đỏ chứ không lấy khớp của người áo trắng. Cả mặt người áo trắng ngửa lên → 5 điểm mặt `v = 1`. Không có xương nào chạy sang thân người kia. |
| Người nhỏ | ![train_19](reports/guideline_samples/train_19.jpg) | Người thứ 2 bên trái ≈ 55 × 160 px, vẫn gán đủ 17 điểm; đây là mốc dưới của nhóm. |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_04`, người thứ `1` (người bên trái, áo xám), khớp `right_wrist` vs `left_wrist`

- Mơ hồ ở chỗ nào: hai cổ tay cùng "không thấy" nhưng vì hai lý do khác nhau. Cổ tay trái
  (bên phải ảnh) nắm ghi-đông ở giữa ảnh, bị ghi-đông + găng che. Cổ tay phải (bên trái ảnh)
  duỗi về phía mép trái-dưới: khuỷu tay phải đã ở (13, 449) px - cách mép trái 13 px và mép
  dưới 8 px - cẳng tay tiếp tục đi ra ngoài ảnh.
- Bạn quyết thế nào: `left_wrist` → `v = 1`, đặt chấm tại điểm cẳng tay gặp ghi-đông.
  `right_wrist` → `v = 0`, không chấm. Cùng người này: hai đầu gối và hai cổ chân cũng
  `v = 0` vì mép dưới cắt ngang đùi.
- Vì sao: luật "kéo dài chi theo hướng đang thấy": cẳng tay trái của chính người này dài
  ≈ 100 px (khuỷu (263, 350) → cổ tay (365, 366)); đặt độ dài đó từ khuỷu phải (13, 449) theo
  hướng cánh tay thì cổ tay rơi ở x < 0 hoặc y > 457, tức ra ngoài khung thật sự. Cổ tay trái thì có ghi-đông ngay đó, bàn tay nhô ra sau ghi-đông,
  chắc chắn còn trong khung.
- Nếu người khác quyết ngược lại thì model học sai cái gì: gán `right_wrist = v 1` với một
  chấm bịa ở mép ảnh sẽ dạy model rằng cổ tay nằm ở "mép ảnh" - một vị trí không có bằng
  chứng giải phẫu; ngược lại gán `left_wrist = v 0` thì khớp có thể ước lượng tốt nhất trong
  ảnh bị loại khỏi OKS và model không bao giờ học được "tay nắm ghi-đông".

### Ca 2 - ảnh `train_11`, người thứ `1`, khớp `left_hip` / `right_hip` / `left_knee` / `right_knee`

- Mơ hồ ở chỗ nào: con mèo đứng trên bàn che kín toàn bộ thân dưới từ ngực trở xuống. Không
  có cạp quần, không có đường viền hông, không thấy đùi. Có hai cách hiểu: (a) hông/gối
  "không gán được" → `v = 0`; (b) hông/gối vẫn trong khung → `v = 1` và ước lượng.
- Bạn quyết thế nào: cả 4 khớp `v = 1`, đặt chấm. Hông đặt ở y ≈ 285 px (tầm thắt lưng suy
  từ tỉ lệ đầu → vai, hai vai cách nhau 142 px), hai hông cách nhau 124 px ≈ ½-¾ khoảng vai;
  đầu gối đặt ngay trên mép dưới ảnh (y ≈ 366-388), phía dưới mặt bàn. Hai cổ chân → `v = 0`
  vì mép dưới ảnh (y = 427) cắt ngang trước khi tới cổ chân (box kết thúc ở y ≈ 0.91).
- Vì sao: người này ngồi/đứng sau bàn với hai vai và khuỷu tay thấy rõ; tỉ lệ cơ thể từ
  đầu → vai → khuỷu cho phép suy hông trong ±15 px. Luật lớp nói rõ: che nhưng còn trong
  khung → `v = 1`. Gold COCO có thể để `v = 0` ở đây, nhưng đó là mục `gold_khong_gan_nhan`,
  không phải lỗi.
- Nếu người khác quyết ngược lại thì model học sai cái gì: gán `v = 0` xoá 4 khớp khỏi hàm
  loss → model được dạy rằng "người sau bàn không có hông", và với `flip_idx` augmentation nó
  học điều đó hai lần. Với 20 ảnh train, mất 4/17 khớp của 1/27 người là mất ~1% dữ liệu
  thân dưới.

### Ca 3 - ảnh `train_14`, người thứ `2` (người đứng bên trái, dưới ô), khớp `nose` / `left_eye` / `right_eye` / `left_ear`

- Mơ hồ ở chỗ nào: người này quay lưng về máy ảnh và cúi đầu dưới ô; toàn bộ mặt không thấy,
  chỉ thấy gáy và một phần tai phải. Mặt "không tồn tại" trong ảnh theo nghĩa pixel, nhưng
  cái đầu thì hoàn toàn ở trong khung. Có cám dỗ gán `v = 0` cho cả 5 điểm mặt.
- Bạn quyết thế nào: `nose`, `left_eye`, `right_eye`, `left_ear` → `v = 1`, đặt chấm ở vị trí
  chúng sẽ ở nếu nhìn xuyên qua đầu (mũi ở giữa gáy, hai mắt hai bên, tai trái ở mép đầu bên
  phải ảnh vì người quay lưng). `right_ear` → `v = 2` vì thấy được vành tai. Đồng thời tự
  kiểm trái/phải: người quay lưng thì tay trái của họ ở bên **trái** ảnh - xương xanh phải ở
  bên trái, và trong ảnh mẫu đúng như vậy.
- Vì sao: luật "ra ngoài mép ảnh" nói về **khung ảnh**, không nói về "bị đầu che". Mặt sau
  gáy vẫn nằm trong khung → `v = 1`. Ước lượng vị trí mắt/mũi từ vành tai + đỉnh đầu đủ tốt
  cho sigma của các khớp mặt trong OKS (0.025-0.035).
- Nếu người khác quyết ngược lại thì model học sai cái gì: đây là ca dễ đảo trái/phải nhất
  trong bài - người quay lưng làm não tự động "lật" trái/phải theo ảnh. Nếu gán `v = 0` cho
  mặt thì mất luôn tín hiệu để kiểm hướng mặt; nếu gán tai trái/phải đảo nhau thì đó chính là
  lỗi `dao_trai_phai` model học vĩnh viễn, và các ảnh có người quay lưng/quay mặt đi (train_06, 09,
  14, 16) sẽ dạy nó cùng một cái sai.

### Ca 4 (thêm) - ảnh `train_16`, người thứ `2` (áo đỏ số 33), khớp `right_eye` / `right_ear`

- Mơ hồ ở chỗ nào: người áo đỏ ngửa mặt về phía đĩa (sang phải-lên), nửa mặt phải quay khỏi
  máy ảnh và lọt sát đầu/vai người áo trắng ngay bên cạnh (mũi hai người cách nhau ≈ 75 px).
  Mắt phải và tai phải không thấy; các chấm ước lượng cho chúng nằm cách khớp của người áo
  trắng chỉ vài chục pixel, rất dễ "kéo nhầm" sang người kia hoặc CVAT tự bắt dính vào
  skeleton bên cạnh.
- Bạn quyết thế nào: gán xong toàn bộ người áo trắng trước; sau đó vẽ người áo đỏ và đặt
  `right_eye`, `right_ear` `v = 1` ở vị trí suy từ mắt trái + mũi (đối xứng qua sống mũi),
  nằm trên bề mặt đầu người áo đỏ, **không** chạm vào
  skeleton người áo trắng. Kiểm lại bằng đường nối: không có xương nào
  của người áo đỏ chạy vào thân người áo trắng.
- Vì sao: luật "hai người chồng nhau" ở mục 2; và OKS sẽ tính khớp này cho đúng người theo
  box, nên nếu nhầm người thì cả hai skeleton đều mất điểm.
- Nếu người khác quyết ngược lại thì model học sai cái gì: `nham_nguoi` - model học rằng mắt
  của một người có thể nằm trên cánh tay người khác; với dữ liệu thể thao nhiều người chồng
  nhau, đây là lỗi lan rất nhanh.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `______` (bạn `___%` / họ `___%`)
  - Số của bạn để đối chiếu nhanh (từ `reports/visibility_report.md`): `left_ear` 52%,
    `right_ear` 48%, `right_hip` 41%, `left_hip` 37%, `left_wrist` 33%, `right_wrist` 33%,
    `left_knee` 33%.
  - Dự đoán trước khi so: nếu lệch ở **tai** → bên kia gán `v = 2` cho tai dưới mũ bảo hiểm
    (guideline chưa rõ, xem luật 2). Nếu lệch ở **hông** → bên kia coi hông bị áo che là
    `v = 2` "vì đoán được" (guideline chưa rõ). Nếu lệch ở **cổ chân** và họ có `v = 1` ở
    chỗ mình `v = 0` → một trong hai bên nhầm "che" với "ra khung" (gán sai).
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**:
- Luật mới bổ sung vào mục 2 sau khi thống nhất:
  - Luật đề xuất sẵn, kiểm chứng được: *"Một khớp chỉ được `v = 0` khi toạ độ ước lượng của
    nó theo hướng chi rơi ngoài `[0, W) × [0, H)`; mọi trường hợp khác còn trong khung -
    dù bị mũ, vật, người hay chính cơ thể che - đều `v = 1` và có chấm."* Kiểm chứng: với mỗi
    khớp `v = 0`, khớp liền kề còn chấm được (đầu gối cho cổ chân, khuỷu cho cổ tay) phải cách
    mép ảnh **ít hơn một độ dài đoạn xương** đo trên chính người đó. Ví dụ train_07: đầu gối ở
    y ≈ 580/640, đùi dài ≈ 150 px → cổ chân rơi ở y ≈ 730 > 640 → `v = 0` đúng. Lưu ý box YOLO
    được tính từ các chấm đã có nên **không** dùng "box chạm mép" làm phép kiểm.
