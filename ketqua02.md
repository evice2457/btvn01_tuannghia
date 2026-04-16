| 1 | Package build không có warning sau khi thêm dependencies | `colcon build` |
<img width="1083" height="164" alt="Screenshot 2026-04-16 at 16 41 45" src="https://github.com/user-attachments/assets/88a9ddaf-6640-466a-a3f7-f4909251088a" />

| 2 | `distance_tf_broadcaster` broadcast đủ 2 frame lên `/tf` | `ros2 run tf2_ros tf2_echo base_link sensor_link` |
<img width="2914" height="1542" alt="image" src="https://github.com/user-attachments/assets/6cd8426d-399b-40be-a6f8-6d2055c5b147" />

| 3 | `sensor_link` x thay đổi theo giá trị `/distance_topic` | So sánh log publisher và tf2_echo |
<img width="388" height="129" alt="Screenshot 2026-04-16 at 16 45 45" src="https://github.com/user-attachments/assets/cc7fa6f5-f174-4feb-a4de-c64485435720" />
<img width="1043" height="647" alt="Screenshot 2026-04-16 at 16 45 31" src="https://github.com/user-attachments/assets/b8033d24-3639-4476-ba2f-c041c8403a55" />
cả 2 ảnh đều có các số x = {0.867, 0.664}

| 4 | `distance_tf_listener` tính đúng khoảng cách từ TF | Quan sát log |
-> True

| 5 | Khi sensor > `tf_threshold`, tự động gọi `/set_threshold` | Quan sát log cả 2 node |
-> True

| 6 | `/distance_reliable` publish đúng 1 Hz | `ros2 topic hz /distance_reliable` |
<img width="1094" height="349" alt="Screenshot 2026-04-16 at 16 57 25" src="https://github.com/user-attachments/assets/c9b04902-634b-40d2-beaa-82bdfb754a46" />

| 7 | `/distance_best_effort` publish đúng 10 Hz | `ros2 topic hz /distance_best_effort` |
<img width="1158" height="454" alt="Screenshot 2026-04-16 at 16 58 16" src="https://github.com/user-attachments/assets/701412f4-e275-4593-a9c0-fe7de4b74dd9" />


| 8 | Bảng stats in đúng số lượng sau 5 giây | Quan sát log |
<img width="847" height="526" alt="Screenshot 2026-04-16 at 17 01 19" src="https://github.com/user-attachments/assets/b4415016-d8a2-457f-9456-d875bdcd969d" />

| 9 | QoS incompatible test → warning xuất hiện | `ros2 topic echo ... --qos-reliability reliable` |
<img width="1463" height="159" alt="Screenshot 2026-04-16 at 17 02 39" src="https://github.com/user-attachments/assets/2ea3e1c2-df25-4026-9815-5ae514fc2d68" />

| 10 | Launch file full khởi động đủ 8 node | `rqt_graph` |
<img width="1299" height="513" alt="Screenshot 2026-04-16 at 17 07 02" src="https://github.com/user-attachments/assets/72b90d3a-6d92-4ab5-944e-5f127b4f958b" />
