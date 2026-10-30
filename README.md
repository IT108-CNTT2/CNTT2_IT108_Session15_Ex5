# Thiết kế Cloud Workspace – Startup Smart-Home

## 1. Mục tiêu và mô hình

- **Nhóm:** 10 người làm việc từ xa tại Hà Nội, Đà Nẵng và TP.HCM.
- **Công cụ:** Google Drive/Google Workspace để lưu tài liệu; Git để quản lý code; Lark để chat, giao việc và đặt lịch.
- **Nguyên tắc:** Chỉ cấp quyền cần thiết (**Least Privilege**), chia sẻ **Restricted**, làm việc bất đồng bộ.

### Sơ đồ luồng cộng tác

```text
Thành viên (3 thành phố)
        |
        v
Lark: trao đổi theo Channel / Thread -> giao Task + Deadline
        |
        v
Làm việc trên máy (Local)
        |
        +--> Code -> Git repository -> Pull Request -> Review -> Merge
        |
        +--> Docs / Assets -> Google Drive (Auto Sync)
                                  |
                                  +--> Phân quyền theo nhóm
                                  +--> Version History / Thùng rác
                                  +--> Sao lưu định kỳ riêng biệt
```

> Không đồng bộ `node_modules`, thư mục `build` hoặc file chứa mật khẩu/API key lên Cloud. Chỉ lưu mã nguồn cần thiết trên Git và tài liệu trên Drive.

## 2. Cấu trúc thư mục Cloud

```text
Smart-Home/
├── 00_Chung/                 # Quy định, lịch họp, tài liệu được phép xem chung
├── 01_KyThuat/              # Tài liệu API, kiến trúc, hướng dẫn triển khai
├── 02_ThietKe/              # UI/UX, hình ảnh, bản thiết kế
├── 03_KinhDoanh/            # Kế hoạch kinh doanh, tài liệu đối tác
├── 04_VanHanh/              # Tiến độ, ngân sách, quy trình nội bộ
├── 05_DuAnChung/            # Bản trình bày, tài liệu phối hợp liên ban
└── 99_Backup/               # Bản sao lưu; chỉ quản trị viên được sửa
```

**Khuyến nghị:** Dùng **Shared Drive** nếu có Google Workspace để tài liệu thuộc tổ chức, không phụ thuộc tài khoản một cá nhân. Bản sao lưu quan trọng nên đặt ở **vị trí/tài khoản độc lập**, không chỉ trong cùng Drive.

## 3. Phân quyền

| Nhóm | Thư mục riêng | `00_Chung` | `05_DuAnChung` | `99_Backup` |
|---|---|---|---|---|
| Trưởng nhóm / quản trị | Quản lý theo nhiệm vụ | Editor | Editor | Quản lý |
| Ban Kỹ thuật | Editor `01_KyThuat` | Viewer | Editor khi được giao | Không truy cập |
| Ban Thiết kế | Editor `02_ThietKe` | Viewer | Editor khi được giao | Không truy cập |
| Ban Kinh doanh | Editor `03_KinhDoanh` | Viewer | Editor khi được giao | Không truy cập |
| Ban Vận hành | Editor `04_VanHanh` | Viewer | Editor khi được giao | Không truy cập |

- Các thư mục ban khác: **không có quyền mặc định**. Nếu cần tham khảo, cấp **Viewer** cho đúng file.
- **Viewer:** chỉ xem; **Commenter:** xem và nhận xét; **Editor:** được chỉnh sửa. Không cấp Editor nếu chỉ cần đọc.
- Tắt quyền chia sẻ lại của người chỉnh sửa khi phù hợp; chỉ quản trị viên/người phụ trách được mời thêm người. Rà soát quyền hàng tháng.

## 4. Channel Lark cần thiết

| Channel | Mục đích | Quy tắc đăng |
|---|---|---|
| `#thong-bao` | Deadline, quyết định chính thức | Chỉ trưởng nhóm/người được phân công đăng |
| `#ky-thuat` | Code, API, lỗi hệ thống | Thành viên kỹ thuật; trao đổi bằng Thread |
| `#thiet-ke` | UI/UX, góp ý giao diện | Thành viên thiết kế và người liên quan |
| `#kinh-doanh` | Khách hàng, kế hoạch, đối tác | Thành viên kinh doanh và người liên quan |
| `#van-hanh` | Tiến độ, lịch, quy trình | Thành viên vận hành và trưởng nhóm |
| `#hoi-dap-chung` | Câu hỏi liên ban | Mọi thành viên; ưu tiên Thread |

## 5. Ba quy tắc văn hóa chat bất đồng bộ

1. **Không No-Hello Trap:** Không nhắn mỗi “Chào bạn” rồi chờ. Gửi luôn **bối cảnh + vấn đề + việc cần hỗ trợ + thời hạn** trong một tin.
2. **Không spam `@all`:** Chỉ trưởng nhóm/người được ủy quyền dùng khi có sự cố khẩn hoặc thông báo thật sự quan trọng. Việc thường ngày chỉ tag đúng người và tạo Task.
3. **Tôn trọng giờ nghỉ:** Tin không khẩn sau **21:00** dùng **Schedule Send** gửi lúc **08:00 ngày làm việc tiếp theo**. Không yêu cầu phản hồi ngay trong đêm; thống nhất thời hạn rõ ràng.

## 6. Bàn giao khi trưởng phòng rời nhóm

1. Chỉ định người tiếp nhận và kiểm kê tài liệu, Task, Channel đang quản lý.
2. Nếu dùng **My Drive**: chuyển **Ownership** của các file có thể chuyển quyền cho người tiếp nhận (lưu ý giới hạn theo loại tài khoản/tổ chức); kiểm tra cả file con. Nếu dùng **Shared Drive**: cấp vai trò **Manager** cho người mới, vì file thuộc Shared Drive.
3. Bàn giao quyền quản trị Channel, lịch họp, Task và tài khoản dịch vụ liên quan.
4. Kiểm tra quyền người mới, **thu hồi quyền người cũ ngay**, vô hiệu hóa tài khoản công ty và thay thông tin xác thực nếu cần.

## 7. Kiểm thử: Lỡ xóa toàn bộ thư mục dự án

1. **Ngừng thao tác đồng bộ** trên các máy để tránh làm sự cố lan rộng; báo quản trị viên.
2. Mở **Trash/Thùng rác** trên Google Drive, tìm thư mục và chọn **Restore/Khôi phục**. Thông thường, mục đã xóa nằm trong thùng rác **30 ngày** trước khi bị xóa vĩnh viễn.
3. Kiểm tra lại cấu trúc thư mục và quyền truy cập. Nếu file bị **ghi đè**, dùng **Version History** để khôi phục phiên bản cũ (khác với khôi phục file đã xóa).
4. Nếu không còn trong thùng rác, nhờ quản trị viên kiểm tra khả năng khôi phục của Google Workspace hoặc phục hồi từ **backup độc lập**.

**Kết luận:** Smart-Home dùng **Drive + Git + Lark**, phân quyền tối thiểu, trao đổi theo Thread/Task, hẹn giờ gửi tin và có quy trình bàn giao, sao lưu để giảm rủi ro mất dữ liệu.
