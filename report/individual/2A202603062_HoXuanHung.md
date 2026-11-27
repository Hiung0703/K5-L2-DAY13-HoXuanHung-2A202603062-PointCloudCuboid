# Báo cáo Day 13 — Hồ Xuân Hùng, 2A202603062

## 1. Kết luận trước

- Mã số: `2A202603062`; task: `D13-2A202603062`; 10 frame: `s01f06`, `s01f35`, `s02f10`, `s02f31`, `s03f01`, `s03f47`, `s04f20`, `s04f59`, `s05f26`, `s05f43`.
- Tôi làm bài trên Windows, dùng CVAT Server/UI 2.81.0 tại `http://localhost:8080` với tài khoản `admin` và Chrome 158.0.8127.37 (64 bit). MSSV người đổi kiểm với tôi là `2A202603117`.
- Tôi đã vẽ **81 cuboid**. Hai frame có ít cuboid nhất là `s02f31` và `s03f47`, mỗi frame 6 hộp, vẫn cao hơn yêu cầu 5 hộp. Phiếu tự kiểm báo **32/81 cuboid** có ít nhất một cảnh báo. Tôi tự tin nhất với các xe ở gần vì cụm điểm dày và rõ; tôi còn chưa chắc ở phần đáy của người đi bộ và kích thước các vật xa, thưa điểm.

Trong CVAT, tôi đổi góc nhìn bằng thao tác xoay, dời và nhìn từ trên xuống. Tôi vẽ bằng **Shape**, nháy đúp để kết thúc rồi chỉnh trên Top/Side/Front. Tôi bật **Cuboid orientation** để kiểm mũi tên hướng và dùng `preview` để đối chiếu camera.

## 2. Đọc frame (Bước 2)

Hai frame đọc kỹ có đủ ảnh 3D và ảnh nhìn từ trên xuống. Trong hệ VF, **x dương sang trái xe, y dương về phía sau, z dương hướng lên**, nên phía trước xe là **−y**.

| Frame | Phía trước xe; +x, +y | Mặt đường | Vật xa nhất đọc được | Loại vật trong Bảng A | Vật phía sau / hai bên | Cụm chưa đọc chắc |
|---|---|---|---|---|---|---|
| `s01f06` (`t1-s01f06-3d.png`, `t1-s01f06-top.png`) | Phía trước theo −y; +x sang trái, +y về sau | Xấp xỉ phẳng; đáy hộp tốt quanh −1,7 đến −1,9 m | Truck ở 38,65 m | 7 Car, 1 Truck | Có xe ở y dương và xe lệch hai bên | Vật xa/thưa ngoài khoảng 40 m không đủ cơ sở gán thêm |
| `s01f35` (`t1-s01f35-3d.png`, `t1-s01f35-top.png`) | Phía trước theo −y; +x sang trái, +y về sau | Xấp xỉ phẳng; đáy Pedestrian còn chưa chắc | Car ở 50,43 m | 5 Car, 3 Pedestrian, 1 Cyclist | Có vật ở cả y âm/y dương và lệch trái/phải | Car 50,43 m chỉ có 46 điểm nên kích thước chưa đáng tin cậy |

Qua hai frame này, tôi thấy camera giúp nhận màu sắc, ngoại hình, chi tiết loại xe và bối cảnh quanh vật. LiDAR cho tôi khoảng cách theo mét, kích thước 3D và vị trí đáy so với mặt đường. Vì vậy tôi không dùng camera để thay cho số đo LiDAR, đồng thời cũng không dùng riêng LiDAR để đoán màu hoặc chi tiết bề mặt.

| Frame | Phía trước | Điều đáng chú ý từ Bảng A / tự kiểm |
|---|---|---|
| `s02f10` | −y | Có 2 Bus; Bus idx 2 ở 39,64 m, 462 điểm |
| `s02f31` | −y | 6 Car; ít cuboid nhất nhưng vẫn đạt ngưỡng |
| `s03f01` | −y | 8 Car; Car idx 2 có đáy bị cờ cao 0,53 m |
| `s03f47` | −y | 4 Car, 2 Cyclist; hai Cyclist có cảnh báo kích thước/hướng |
| `s04f20` | −y | 10 cuboid; Car idx 9 gần nhất toàn bài, 1,15 m |
| `s04f59` | −y | Vật xa nhất 40,45 m; điểm giảm rõ theo khoảng cách |
| `s05f26` | −y | Car idx 2 bị cờ đáy cao 0,66 m và nhiều điểm sát ngoài mép |
| `s05f43` | −y | Truck idx 7 ở 53,26 m; kích thước xa còn chưa chắc |

## 3. Ba cuboid có hướng dẫn (Bước 3)

| Vật | Frame, idx | Class | Mũi tên yaw | Đáy | Kiểm hình | Kích thước |
|---|---|---|---|---|---|---|
| Xe con gần (`t2-a-car.png`) | `s02f10`, idx 9 | Car | 274,0°, gần 270° | z_bottom −1,844 m; gap −0,12 m | Tôi đã kiểm trên ảnh và ba hình chiếu | 4,773 × 2,104 × 1,663 m, trong khoảng |
| Xe buýt (`t2-b-bus-truck.png`) | `s02f10`, idx 2 | Bus | 85,7°, gần 90° | z_bottom −2,069 m; gap −0,03 m | Tôi đã kiểm trên ảnh và ba hình chiếu | 11,900 × 3,286 × 3,313 m; chiều rộng hơi vượt khoảng tham khảo |
| Người (`t2-c-ped-cyclist.png`) | `s02f10`, idx 8 | Pedestrian | Tôi ít chắc hướng hơn vì thân người gần đối xứng | z_bottom −1,147 m; bị cờ lơ lửng 0,53 m | Ảnh có đối tượng Pedestrian nhưng phần chọn chưa nổi bật | 0,319 × 0,706 × 1,177 m; đáy và kích thước cần xem lại |

Trong vòng hiệu chuẩn, tôi chưa ghi lại mức lệch cụ thể nên không nêu con số khi chưa chắc. Sau phần hiệu chuẩn, tôi giữ cách hiểu: `length` nằm dọc thân xe, yaw chỉ theo đầu xe, đáy hộp bám mặt đường và hộp phải khớp ở cả Top/Side/Front. Tôi bỏ qua vật không tạo thành cụm điểm đủ rõ, nhất là vật xa hoặc bị che; khi thiếu cơ sở tôi không đoán class.

## 4. Bốn hiện tượng chất lượng (Bước 4)

| Hiện tượng | Frame / ảnh / nguồn | Nguyên nhân (giả thuyết) | Dấu hiệu | Hệ quả khi gán |
|---|---|---|---|---|
| Bóng che | `t4-1-bong-che.png`, task cá nhân | Vật gần có thể chắn tia tới vật sau | Cụm phía sau mất một phần theo hướng nhìn | Chỉ vẽ khi phần còn lại đủ xác định; không kéo hộp tùy ý |
| Thưa ở xa | `t4-2-thua-o-xa.png`, task cá nhân | Khoảng cách giữa tia tăng theo cự ly | Cụm xa ít điểm; Car 50,43 m chỉ có 46 điểm | Kích thước/class kém chắc; bỏ qua nếu thiếu cơ sở |
| Phản xạ thấp | `t4-3-phan-xa-thap.png`, task cá nhân | Vật liệu/hướng bề mặt có thể phản xạ ít | Một phần vật ít điểm hơn vùng ở cự ly tương tự | Kết hợp hình dạng và camera, không thu hộp quá chặt |
| Điểm lạ | `t4-4-diem-la.png`, task cá nhân | Có thể do nhiễu, phản xạ nhiều đường hoặc vật nhỏ | Điểm/cụm nhỏ tách khỏi bề mặt hợp lý | Không mở rộng hộp để ôm điểm lạ |

Tôi chỉ xem các nguyên nhân trên là **giả thuyết**, không khẳng định đó là nguyên nhân thật của từng điểm.

## 5. Số từ cuboid của tôi (Bước 5)

| Cuboid | z | h | z đáy | dist_m | r | yaw_deg | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|
| `s04f20`, idx 9, Car | −0,990 m | 1,601 m | −1,791 m | 1,15 m | 1,52 m | 268,5° | Chạm đường; gap −0,02 m |
| `s01f06`, idx 1, Car | −1,044 m | 1,766 m | −1,927 m | 4,35 m | 4,48 m | 271,1° | Gần mặt đường; gap −0,14 m |
| `s05f43`, idx 7, Truck | −0,811 m | 3,875 m | −2,748 m | 53,26 m | 53,26 m | 91,2° | Xa; cờ width 3,545 m |

Gần nhất là `s04f20` idx 9 (Car): `dist_m = 1,15 m`, `r = 1,52 m`. Xa nhất là `s05f43` idx 7 (Truck): `dist_m = 53,26 m`, `r = 53,26 m`.

Pedestrian `s01f35` idx 4 có `l × w × h = 0,716 × 0,803 × 1,491 m`; kích thước thân hợp lý nhưng đáy bị cờ cao 0,54 m. Car `s01f06` idx 1 có `5,104 × 2,119 × 1,766 m`, trong khoảng tham khảo. Xe cùng chiều `s01f06` idx 1 có yaw 271,1°, gần 270°; vật hướng ngược `s01f06` idx 8 có yaw 89,1°, gần 90°.

| Cuboid | dist_m | points_in |
|---|---:|---:|
| `s03f01`, idx 2, Car | 5,2 m | 2304 |
| `s01f06`, idx 2, Car | 18,5 m | 446 |
| `s05f43`, idx 6, Car | 25,3 m | 275 |
| `s04f59`, idx 9, Car | 32,5 m | 149 |
| `s01f35`, idx 7, Car | 47,2 m | 57 |

Khoảng cách tăng từ 5,2 m lên 47,2 m (khoảng 9,1 lần), số điểm giảm từ 2304 xuống 57 (còn khoảng 2,5%). Xu hướng phù hợp slide: mật độ giảm mạnh theo khoảng cách, dù còn phụ thuộc kích thước, che khuất và góc bề mặt.

Phiếu tự kiểm báo 32/81 cuboid có cảnh báo. Hai ca tôi cần ưu tiên xem lại là `s01f35` idx 4 (Pedestrian, đáy cao 0,54 m) và `s03f47` idx 6 (Cyclist, `length < width`, có điểm sát mép). Tôi chưa ghi chép lại chính xác hai cuboid đã sửa sau lần kiểm nên không tự điền khi chưa chắc. Tôi giữ một số cảnh báo kích thước ở vật xa vì cảnh báo tự động chỉ là gợi ý, chưa đủ để kết luận cuboid sai.

Tôi đổi kiểm với bạn có MSSV `2A202603117`. Tôi chưa ghi lại chính xác hai số đã đối chiếu và kết quả khớp/lệch, nên không tự điền khi chưa chắc.

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

Nhãn 3D dùng mét vì model cần học vị trí/kích thước thật, độc lập với độ phân giải và phối cảnh camera. Vật thưa điểm cần quy tắc riêng vì ranh giới quan sát không đầy đủ; đoán từ quá ít điểm tạo nhãn nhiễu.

- Công cụ tôi đã dùng: Codex.
- Tôi dùng công cụ để kiểm tra cấu trúc bài nộp, tính lại các thống kê từ Bảng A và phiếu tự kiểm, đồng thời rà các mục còn thiếu trong báo cáo.
- Tôi tự chịu trách nhiệm về nội dung cuối cùng; công cụ không thay đổi cuboid trên CVAT, Bảng A hay phiếu tự kiểm.

Tôi còn chưa chắc về đáy của một số Pedestrian/Car bị cờ cao hoặc thấp, kích thước các vật trên 40 m, hướng của Cyclist có `length < width` và các trường hợp có nhiều điểm sát ngoài mép. Tôi cần xem lại các ca này trên point cloud/CVAT gốc; việc một cuboid không bị cảnh báo chưa có nghĩa là nó chắc chắn đúng.
