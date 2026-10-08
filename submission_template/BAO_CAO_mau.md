# Báo cáo lab: chọn tracker cho 5 video

**Nhóm:** Clauder **Thành viên:** \
Đoàn Anh Quân - 2A202602803\
Hoàng Đức Dũng - 2A202602798


Detector cố định: `yolo26n.pt`, ảnh 640 px, Re-ID `osnet_x0_25_msmt17`. Không đổi các mục này trong bài nộp chính.

## 1. Cấu hình đã chọn

Mỗi video: tracker bạn nộp, `conf`, `iou`, điều bạn **nhìn thấy** trên video, và một cấu hình đã thử rồi loại.

| Video | Tracker | conf | iou | Quan sát khi xem video | Đã thử nhưng loại |
|---|---|---|---|---|---|
| video_1 (quảng trường, tĩnh, ban ngày) | `botsort` | 0.3 | 0.7 | Người ở gần và tầm trung giữ ID ổn (ID 46, 54, 58 còn nguyên từ frame 400 đến 550). Nhóm đứng sát nhau có hộp chồng lên nhau. Người nhỏ ở cuối quảng trường phần lớn không có hộp. | `bytetrack` 0.3/0.5: ít đổi ID nhất (12 lần) nhưng bỏ sót nhiều hơn, HOTA 26.9. `strongsort` 0.15/0.5: 110 lần đổi ID, 1432 hộp giả. `botsort` conf 0.5: mất người, HOTA 27.3. |
| video_2 (phố đêm, tĩnh, rất đông) | `botsort` | 0.15 | 0.5 | Người đứng cạnh xe máy giữ ID 4 suốt từ frame 100 đến 1000. Cặp đi cạnh nhau (ID 8, 25) không tráo ID. Người đi sát mép dưới khung hình bị đổi ID (3 → 63). Đám đông xa ở góc trên trái gần như không có hộp. | `bytetrack` 0.3/0.5: ít hộp hơn (9.4 so với 13.2 hộp/frame) và chính người đứng yên cạnh xe máy bị đổi ID 4 → 45. `botsort` conf 0.5: chỉ còn 8.1 hộp/frame. |
| video_3 (camera di động, ảnh nhỏ) | `ocsort` | 0.3 | 0.5 | Hai người đi trước camera giữ ID (59, 93) qua các frame liền nhau; người ở sát camera có hộp lớn, ổn định (ID 90). Hộp của người rất nhỏ ở xa hiện rồi mất. Track nhìn chung ngắn vì người lướt qua khung hình nhanh. | `botsort` 0.3/0.5: 168 ID so với 137, nhiều track cụt dưới 10 frame hơn (67 so với 47). `ocsort` conf 0.15: 221 ID, 108 track dưới 10 frame — hộp nhấp nháy. |
| video_4 (trong nhà, camera di chuyển) | `bytetrack` | 0.15 | 0.5 | Người áo đỏ giữ ID 2 từ frame 150 đến 450. Người áo trắng bị đổi ID (5 → 38) sau khi một người đi sát camera che ngang. Ở các frame đã xem không thấy hộp trên bóng phản chiếu trong kính. | `botsort` 0.15/0.5: cũng giữ ID 2, nhưng tổng 70 ID so với 56 và track ngắn hơn (trung vị 60 so với 90 frame). `bytetrack` conf 0.5: 64 ID, track ngắn hơn. |
| video_5 (trên xe bus, giao lộ đông) | `botsort` | 0.15 | 0.5 | Người sang đường giữ ID 108 từ frame 400 đến 430 dù xe đang rẽ; nhóm đứng trên vỉa hè (116, 120) giữ ID. Người nhỏ ở xa (ID 158) vẫn có hộp và giữ ID qua 30 frame. | `bytetrack` 0.3/0.5: chỉ 2.7 hộp/frame so với 4.5, bỏ sót người nhỏ trên vỉa hè. `botsort` conf 0.5: 2.4 hộp/frame. |

Cách thử: mỗi video chạy đủ frame cả 5 tracker ở `conf` 0.3 / `iou` 0.5, rồi với tracker khá hơn đổi từng tham số một (`conf` 0.15 / 0.5, `iou` 0.4 / 0.7). Số ID, hộp/frame và độ dài track trong bảng đếm từ file kết quả `.txt`; chúng không phải điểm chấm. Quan sát lấy từ các frame có vẽ ID.

## 2. Số liệu video_1

Dán bảng HOTA / MOTA / IDF1 do `scripts/evaluate_practice.py` in ra.

```
HOTA: Clauder_video1-pedestrian    HOTA      DetA      AssA      DetRe     DetPr     AssRe     AssPr     LocA      OWTA      HOTA(0)   LocA(0)   HOTALocA(0)
video_1                            30.002    18.43     49.105    19.157    75.064    52.423    80.978    83.07     30.624    37.163    76.951    28.597

CLEAR: Clauder_video1-pedestrian   MOTA      MOTP      MODA      CLR_Re    CLR_Pr    MTR       PTR       MLR       sMOTA     CLR_TP    CLR_FN    CLR_FP    IDSW      MT        PT        ML        Frag
video_1                            19.283    80.817    19.461    22.491    88.127    14.516    17.742    67.742    14.969    4179      14402     563       33        9         11        42        105

Identity: Clauder_video1-pedestrianIDF1      IDR       IDP       IDTP      IDFN      IDFP
video_1                            29.765    18.68     73.197    3471      15110     1271

Count: Clauder_video1-pedestrian   Dets      GT_Dets   IDs       GT_IDs
video_1                            4742      18581     54        62
```

Tóm tắt: **HOTA 30.0 · MOTA 19.3 · IDF1 29.8**.

`video_2` đến `video_5` không có nhãn trong gói lab. Không điền số cho các video đó.

## 3. Phân tích

**video_1 (có số).** Các cấu hình đã chấm, đủ 600 frame:

| Tracker | conf | iou | HOTA | MOTA | IDF1 | Recall | Hộp giả | Đổi ID |
|---|---|---|---|---|---|---|---|---|
| `botsort` (nộp) | 0.3 | 0.7 | 30.0 | 19.3 | 29.8 | 22.5 | 563 | 33 |
| `botsort` | 0.15 | 0.7 | 29.7 | 20.3 | 29.9 | 24.3 | 700 | 38 |
| `strongsort` | 0.15 | 0.5 | 29.2 | 19.9 | 32.6 | 28.2 | 1432 | 110 |
| `strongsort` | 0.3 | 0.5 | 28.7 | 19.7 | 29.9 | 21.2 | 231 | 41 |
| `botsort` | 0.3 | 0.5 | 28.2 | 19.8 | 28.5 | 21.8 | 338 | 26 |
| `ocsort` | 0.3 | 0.5 | 27.5 | 19.8 | 28.7 | 21.4 | 253 | 42 |
| `deepocsort` | 0.3 | 0.5 | 27.2 | 19.8 | 28.5 | 21.4 | 249 | 48 |
| `bytetrack` | 0.3 | 0.5 | 26.9 | 17.3 | 25.7 | 17.9 | 107 | 12 |
| `botsort` | 0.5 | 0.5 | 27.3 | 16.0 | 24.7 | 16.5 | 93 | 9 |

Điểm thấp chủ yếu do detector: recall chỉ khoảng 16–28%, tức phần lớn người nhỏ ở xa không có hộp, và tracker nào cũng không cứu được hộp không tồn tại. Vì vậy chênh lệch giữa các tracker chỉ 1–3 điểm HOTA. `bytetrack` giữ ID sạch nhất (12 lần đổi ID) nhưng bỏ bớt hộp điểm thấp nên recall và MOTA thấp nhất. `botsort` giữ được nhiều hộp hơn mà vẫn ít đổi ID (33 lần), nên HOTA cao nhất. Camera đứng yên, ban ngày, người đi chậm nên chuyển động dễ đoán; Re-ID giúp nối lại track sau khi hai người đi ngang che nhau. Tăng `iou` từ 0.5 lên 0.7 giữ lại hộp của người đứng sát nhau mà NMS trước đó gộp mất: HOTA tăng 1.8 điểm, đổi lại hộp giả tăng từ 338 lên 563 và MOTA giảm nhẹ. Hạ `conf` xuống 0.15 tăng recall và MOTA nhưng HOTA không tăng; với `strongsort` thì hộp giả và đổi ID tăng vọt.

**video_2 (bằng mắt).** Cảnh đêm, rất đông, camera tĩnh trên cao. Vấn đề nhìn thấy rõ nhất là thiếu hộp: ở `conf` 0.3 phần lớn đám đông phía xa không được phát hiện, nên chúng tôi hạ `conf` xuống 0.15 (13.2 so với 11.7 hộp/frame với `botsort`) và không thấy hộp trên nền hay đèn ở các frame đã xem. Giữa hai tracker, `bytetrack` cho ít ID hơn nhưng làm đổi ID của một người đứng yên suốt video (4 → 45), còn `botsort` giữ nguyên ID 4 từ frame 100 đến 1000. Người đi sát nhau trong cảnh đông có hộp chồng lấn nhiều, nên chỉ dựa vào vị trí dễ gán nhầm; ngoại hình (quần áo sáng/tối dưới đèn) giúp phân biệt. `botsort` vẫn sai ở mép khung hình: người đi sát mép dưới bị đổi ID 3 → 63.

**video_3 và video_5 (bằng mắt) — hai camera chuyển động cho kết luận ngược nhau.** Ở video_5 (ảnh 1080p, xe bus rung và rẽ), `botsort` hơn hẳn `bytetrack`: nhiều hộp hơn (4.5 so với 2.7 hộp/frame), vẫn giữ ID người sang đường khi cả khung hình trượt ngang, vì `botsort` bù chuyển động camera và có Re-ID. Ở video_3 (ảnh 640×480, ít khung hình/giây), Re-ID không giúp: người ở xa chỉ cao vài chục pixel nên đặc trưng ngoại hình kém tin cậy, và `botsort` sinh nhiều ID và track cụt hơn `ocsort`. Tracker nào ở video_3 cũng cho track ngắn (trung vị khoảng 15 frame), nên đây là video chúng tôi kém chắc chắn nhất.

**video_4 (bằng mắt).** Người to, rõ, sáng đều nên chuyển động là đủ: `bytetrack` cho track dài nhất và ít ID nhất. Lỗi nhìn thấy là đổi ID sau khi bị người đi sát camera che hoàn toàn (áo trắng 5 → 38), và `botsort` cũng đổi ID ở đúng tình huống đó.

## 4. Nếu có thêm thời gian

Xem lại từng đoạn che khuất ở video_2 và video_4 trên video preview để đếm tay số lần đổi ID, thay vì dựa vào vài frame mẫu. Quét `conf` mịn hơn quanh 0.15–0.3 cho video_3, và thử `deepocsort` / `strongsort` với `conf` thấp ở video_5 để xem Re-ID hay bù chuyển động camera mới là phần tạo khác biệt.
