# 🌐 Multi-Client-Broadcast — Collaborative Whiteboard & Mini-Game (MQTT over WebSockets)

**Chủ đề:** Lập trình Multi-Client Server kết hợp Broadcast (TCP/WebSocket & MQTT Pub/Sub).  
**Mối liên hệ học phần:** Minh họa trực quan cho **Chương 5 (Multi-Client Server và cơ chế Broadcast 1-to-Many)**.

---

## 📌 1. Giới thiệu tổng quan
Dự án xây dựng một **Bảng vẽ phân tán thời gian thực (Collaborative Whiteboard)** kết hợp **Mini-Game Đuổi Hình Bắt Chữ** và **Bộ giám sát lưu lượng mạng (Network Telemetry)**.
- Khi một Client chấm màu, vẽ nét cọ, vẽ hình học, gõ chữ, di chuyển chuột hoặc gửi tin nhắn/cảm xúc, dữ liệu lập tức được **Broadcast (1-to-Many)** tới toàn bộ các Client khác đang kết nối trong cùng phòng.
- Minh họa rõ nét mô hình **1-to-Many Broadcast**: 1 người thao tác, hàng chục thành viên khác thấy nét vẽ, con trỏ chuột và thông báo nhảy theo thời gian thực.

---

## ✨ Các tính năng mới đã được thêm vào (Nâng cấp so với bản gốc)

1. **🏠 Tách Phòng Riêng (Custom Room ID) & Đổi Tên Hiển Thị:**
   - Cho phép nhập và chuyển đổi **Mã Phòng (Room ID)** động ngay trên giao diện (`btltm/whiteboard/<ma_phong>`), tách biệt hoàn toàn khỏi phòng mặc định.
   - Cho phép người dùng đặt **Tên hiển thị** thay vì chỉ hiển thị mã `Client_XXXX` ngẫu nhiên.
2. **🖱️ Con trỏ chuột Realtime của mọi thành viên (Live Cursors):**
   - Hiển thị trực tiếp vị trí di chuyển chuột kèm nhãn tên đầy màu sắc của tất cả các Client khác đang thao tác trên bảng vẽ theo thời gian thực.
3. **🎨 Bộ 6 Công cụ vẽ nâng cao, Xem trước hình học & Chọn màu tự do:**
   - Bổ sung công cụ vẽ **Đường thẳng (`📏`)**, **Hình chữ nhật (`▭`)**, **Hình tròn (`⭕`)** (có lớp Canvas Preview xem trước khi kéo chuột), **Gõ chữ lên bảng (`🔤`)** và **Cục tẩy (`🧹`)**.
   - Bổ sung bảng chọn màu sắc tự do (**Custom Color Picker**) bên cạnh các màu mặc định.
4. **↩️ Hoàn tác (Undo) đồng bộ toàn phòng & 💾 Xuất ảnh PNG:**
   - **Hoàn tác (Undo):** Cho phép xóa nét vẽ/hình khối gần nhất của mình và tự động Broadcast lệnh xóa đó tới mọi màn hình trong phòng.
   - **Tải PNG:** Lưu toàn bộ bảng vẽ hiện tại về máy dưới dạng ảnh `.png` nền trắng sắc nét.
5. **🔄 Đồng bộ bảng vẽ cho người vào sau (Late-Joiner State Sync):**
   - Khắc phục nhược điểm bảng trắng khi vào muộn: Client mới vào phòng sẽ tự động gửi yêu cầu `request_sync` và nhận lại toàn bộ các nét vẽ trước đó (`sync_state`) từ các máy đang có trong phòng.
6. **🎮 Mini-Game "Đuổi Hình Bắt Chữ" (Draw & Guess) & Bảng Xếp Hạng Điểm:**
   - Tích hợp trò chơi tương tác: 1 người nhận từ khóa bí mật (ngẫu nhiên từ ngân hàng từ vựng hoặc tự ra đề) và vẽ minh họa; các thành viên khác nhìn gợi ý số chữ cái (`_ _ _`) và gõ đáp án vào ô Chat.
   - Hệ thống tự động bắt đáp án đúng, cộng **+10 điểm** lên **Bảng Điểm trực tuyến** và phát thông báo chúc mừng toàn phòng.
7. **💬 Phòng Chat Broadcast & ⚡ Thả Cảm Xúc Bay (Floating Emoji Reactions):**
   - Khung chat thời gian thực kết hợp thanh cảm xúc (`🔥`, `❤️`, `👏`, `😂`, `🎉`, `🤩`, `👍`) tạo hiệu ứng Emoji kèm tên người gửi bay lên trên màn hình cả phòng.
8. **📊 Giám Sát Lưu Lượng Mạng Realtime (Network Telemetry & JSON Inspector):**
   - Đo và hiển thị trực tiếp **Độ trễ mạng (Ping / RTT `ms`)**, **Tốc độ gói tin (`pkt/s`)**, **Băng thông tiêu thụ (`B/s`, `KB/s`)**, **Tổng gói tin gửi/nhận (`TX / RX`)**.
   - Tích hợp **Live Payload Inspector** hiển thị chuỗi JSON thô của gói tin vừa truyền/nhận ngay trên giao diện.
9. **🎯 Chuẩn hóa tỷ lệ tọa độ Canvas (Responsive Coordinate Scaling):**
   - Sửa lỗi lệch tọa độ nét vẽ khi kích thước hiển thị của bảng vẽ co giãn trên các màn hình PC/Điện thoại khác nhau.

---

## 🚀 2. Hướng dẫn sử dụng chi tiết các chức năng mới (Tại Tab 3: Phòng Lab Whiteboard)

### 🏠 2.1. Quản lý Phòng riêng (Room ID) & Định danh người dùng
- **Đặt tên hiển thị:** Nhập tên của bạn vào ô **Tên hiển thị của bạn** rồi bấm **Lưu Tên**. Tên này sẽ xuất hiện trên bảng xếp hạng, khung chat và đi kèm con trỏ chuột của bạn.
- **Đổi Phòng (Room ID):**
  - Mặc định ứng dụng chạy ở phòng riêng `phong_rieng_01` (Topic: `btltm/whiteboard/phong_rieng_01`), hoàn toàn tách biệt với phòng chung của lớp.
  - Bạn và nhóm có thể nhập bất kỳ **Mã Phòng** nào (ví dụ: `nhom_5`, `phong_test`) và bấm **Đổi Phòng** để tạo không gian vẽ riêng tư.
- **Bảng Thành Viên & Điểm số:** Tự động cập nhật danh sách các Client đang trực tuyến qua cơ chế Heartbeat (gửi gói tin `presence` định kỳ 3 giây/lần) và sắp xếp theo điểm số Mini-Game.

### 🖱️ 2.2. Con trỏ chuột Realtime (Live Cursors)
- Khi bất kỳ thành viên nào di chuột trên bảng vẽ, tọa độ chuột được đóng gói và Broadcast cho cả phòng.
- Các máy khác sẽ nhìn thấy **con trỏ màu kèm tên người dùng** di chuyển mượt mà theo thời gian thực (tương tự Figma / Canva).

### 🎨 2.3. Bộ công cụ vẽ nâng cao & Đồng bộ dữ liệu
1. **6 chế độ vẽ đa năng:**
   - `✏️ Bút`: Vẽ nét tự do theo chuyển động chuột hoặc cảm ứng điện thoại.
   - `📏 Đường`: Kéo thả để vẽ đường thẳng (có lớp xem trước nét kéo trước khi thả chuột).
   - `▭ Khung`: Kéo thả để vẽ hình chữ nhật.
   - `⭕ Tròn`: Kéo thả để vẽ hình tròn theo tâm và bán kính.
   - `🔤 Chữ`: Click vào vị trí bất kỳ trên bảng để nhập văn bản và Broadcast chữ đó lên bảng của tất cả mọi người.
   - `🧹 Tẩy`: Xóa từng phần nét vẽ trên bảng.
2. **Màu sắc & Độ dày:** Chọn nhanh 6 màu cơ bản hoặc mở **Bảng chọn màu tự do (Color Picker)** và thanh trượt kích thước nét (`1px - 28px`).
3. **↩️ Hoàn Tác (Undo):** Xóa thao tác vẽ gần nhất của bạn và đồng bộ lệnh xóa đó tới tất cả các máy trong phòng.
4. **💾 Tải PNG:** Xuất toàn bộ bảng vẽ hiện tại về máy dưới dạng file ảnh `.png`.
5. **🔄 Đồng bộ cho người vào sau (Late-Joiner State Sync):** Khi một Client mới vào phòng, ứng dụng tự động gửi gói tin `request_sync` để nhận lại toàn bộ lịch sử nét vẽ (`sync_state`) từ các máy đang có mặt trong phòng.

### 🎮 2.4. Mini-Game "Đuổi Hình Bắt Chữ" (Draw & Guess)
- **Bắt đầu lượt chơi:**
  - Bấm **🎲 Bắt Đầu Vẽ (Ngẫu nhiên)** để hệ thống chọn ngẫu nhiên 1 từ khóa từ ngân hàng từ vựng, hoặc bấm **✍️ Tự Ra Đề** để tự nhập từ khóa bí mật.
  - **Người vẽ** sẽ nhìn thấy từ khóa bí mật trên thanh thông báo.
  - **Các thành viên khác** sẽ nhận được gợi ý số lượng chữ cái (ví dụ: `_ _ _ _   _ _ _ (7 chữ cái)`).
- **Đoán từ khóa & Tính điểm:**
  - Người xem gõ đáp án dự đoán vào khung **💬 Phòng Chat & Đoán Từ Khóa**.
  - Khi gõ đúng từ khóa (không phân biệt chữ hoa/thường), hệ thống lập tức phát pháo hoa chúc mừng toàn phòng, cộng **+10 điểm** cho người đoán đúng và cập nhật **Bảng Điểm**!

### ⚡ 2.5. Phòng Chat & Thả Cảm Xúc Bay (Floating Reactions)
- **Thả cảm xúc nhanh:** Bấm vào các nút `🔥 ❤️ 👏 😂 🎉 🤩 👍` ngay dưới bảng vẽ để bắn hiệu ứng Emoji kèm tên của bạn bay vút lên trên màn hình của tất cả thành viên trong phòng.
- **Trò chuyện trực tiếp:** Gửi tin nhắn văn bản thời gian thực cho toàn bộ thành viên trong phòng.

### 📊 2.6. Giám Sát Lưu Lượng Mạng (Network Telemetry & JSON Inspector)
Hỗ trợ phân tích chuyên sâu cho môn **Lập trình mạng**:
- **Độ trễ (RTT / Ping `ms`):** Đo thời gian thực tế một gói tin đi từ trình duyệt lên MQTT Broker và phản hồi ngược về.
- **Tốc độ gói tin (`pkt/s`) & Băng thông (`B/s`, `KB/s`):** Thống kê lưu lượng gói tin truyền/nhận mỗi giây.
- **Tổng TX / RX:** Tổng số gói tin đã gửi đi (`TX`) và nhận về (`RX`).
- **Live Payload Inspector:** Hiển thị trực tiếp cấu trúc chuỗi JSON của gói tin vừa truyền/nhận ngay trên giao diện mà không cần mở Developer Tools (F12).

---

## 📦 3. Cấu trúc các loại gói tin JSON (Application Protocol)
Toàn bộ dữ liệu trao đổi qua Topic `btltm/whiteboard/<ma_phong>/draw` được đóng gói chuẩn JSON:

| Loại gói tin (`type`) | Ý nghĩa | Cấu trúc Payload JSON mẫu |
| :--- | :--- | :--- |
| `draw` | Vẽ nét bút / tẩy | `{"sender":"Client_1234","name":"SV_1234","strokeId":"...","type":"draw","x1":100,"y1":120,"x2":105,"y2":125,"color":"#ef4444","size":"4"}` |
| `shape` | Vẽ hình học / chữ | `{"sender":"Client_1234","name":"SV_1234","strokeId":"...","type":"shape","shape":"rect","x1":50,"y1":50,"x2":200,"y2":150,"color":"#38bdf8","size":"4"}` |
| `cursor` | Vị trí chuột | `{"sender":"Client_1234","name":"SV_1234","type":"cursor","x":340,"y":210,"color":"#22c55e"}` |
| `undo` | Hoàn tác nét vẽ | `{"sender":"Client_1234","name":"SV_1234","type":"undo","strokeId":"Client_1234_17924..."}` |
| `clear` | Xóa sạch bảng | `{"sender":"Client_1234","name":"SV_1234","type":"clear"}` |
| `emoji` | Thả cảm xúc bay | `{"sender":"Client_1234","name":"SV_1234","type":"emoji","emoji":"🔥"}` |
| `chat` | Tin nhắn phòng chat | `{"sender":"Client_1234","name":"SV_1234","type":"chat","text":"Xin chào cả nhóm!"}` |
| `game_start` | Bắt đầu Mini-Game | `{"sender":"Client_1234","name":"SV_1234","type":"game_start","word":"CÁ MẬP","hint":"_ _   _ _ _ (5 chữ cái)"}` |
| `game_win` | Báo người đoán đúng | `{"sender":"Client_5678","type":"game_win","winnerId":"Client_5678","winnerName":"Tiên","word":"CÁ MẬP","newScore":10}` |
| `request_sync` / `sync_state` | Đồng bộ cho người vào sau | `{"sender":"Client_1234","target":"Client_5678","type":"sync_state","history":[...],"game":{...}}` |
| `ping` | Đo độ trễ mạng (RTT) | `{"sender":"Client_1234","type":"ping","ts":1792411800000}` |

---

## 🛠️ 4. Cách chạy dự án trên máy cá nhân (Local / LAN)
1. Mở thư mục dự án trong **VS Code**.
2. Bấm nút **Go Live** (tiện ích *Live Server*) ở góc dưới bên phải màn hình.
3. Trình duyệt sẽ tự động mở địa chỉ theo IP máy của bạn (cấu hình tự động nhận IP trong `.vscode/settings.json`).
4. Để kiểm thử **Multi-Client Broadcast** ngay trên 1 máy: Mở **2 hoặc 3 cửa sổ trình duyệt đặt cạnh nhau** và cùng vào **Tab 3: Phòng Lab Whiteboard Phân Tán**.

## 🌐 GitHub Pages

Sau khi bật Pages cho nhánh `main` tại **Settings → Pages → Deploy from a branch → `main` / `/(root)`**, trang sẽ có địa chỉ:

`https://ngoctienh.github.io/ve/`

Repo, trang web và MQTT broker đều công khai. Mã phòng chỉ là tên topic, không phải mật khẩu; không dùng phòng này để trao đổi thông tin riêng tư.
