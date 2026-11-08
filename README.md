# Saigon Audio Guide

Đồ án hệ thống thuyết minh tự động đa ngôn ngữ.

## Thành viên

- Quốc: nhóm trưởng.
- Doanh: thành viên.
- Hậu: thành viên.

## Trạng thái repository

Repository đang có khung thư mục để nhóm bàn giao code trong 10 ngày. Chưa có solution, project hoặc code chức năng nên chưa thể build/chạy ứng dụng.

Đồ án đã có bản hoàn chỉnh trước khi tạo repository này. Các commit ở đây ghi nhận thời điểm bàn giao, kiểm tra và hoàn thiện từng phần.

## Cấu trúc

```text
SaigonAudioGuide-Nhom3/
├── .github/workflows/         # CI, bổ sung khi có project và kiểm thử
├── docs/
│   ├── CauTrucRepo.md         # Quy ước và trách nhiệm thư mục
│   └── nhat-ky/               # Nhật ký của Quốc, Doanh và Hậu
├── scripts/                  # Script hỗ trợ chạy dự án
├── src/
│   ├── AudioGuide.API/       # HTTP API, cấu hình và giao diện web
│   ├── AudioGuide.BLL/       # Nghiệp vụ, DTO và validation
│   └── AudioGuide.DAL/       # Entity, EF Core, repository và seed
└── tests/
    └── AudioGuide.Tests/     # Kiểm thử .NET; kiểm thử JS đặt trong tests/
```

Chi tiết các thư mục con: [Quy ước cấu trúc repository](docs/CauTrucRepo.md).

## Phân công

| Thành viên | Phạm vi chính |
|---|---|
| Quốc — nhóm trưởng | Solution/project, cấu hình, bảo mật, theo dõi truy cập, Docker, CI và tích hợp |
| Doanh | DAL, quản lý địa điểm/ngôn ngữ, thống kê lượt nghe và giao diện admin |
| Hậu | API công khai, giao diện du khách, GPS, âm thanh, Ollama và kiểm thử liên quan |

Mỗi thành viên thực hiện 1 commit công việc/ngày trong 10 ngày bằng tài khoản của mình. Các commit chuẩn bị repository được tính riêng.

## Làm việc hằng ngày

1. Chạy `git pull --rebase origin main` trước khi làm việc.
2. Đưa phần code được giao vào đúng thư mục; kiểm tra nội dung và dependency liên quan.
3. Ghi kết quả thực tế vào nhật ký cá nhân trong `docs/nhat-ky/`.
4. Dùng `git add <duong-dan>` để chọn đúng file; kiểm tra bằng `git diff --cached`.
5. Commit, đồng bộ lại nếu cần, rồi `git push origin main`. Không force push lên `main`.

File `.gitkeep` chỉ giữ các thư mục rỗng trong Git; có thể xóa khi thư mục đã có code. Không commit `.env`, thông tin xác thực, `bin/`, `obj/` hoặc dữ liệu chạy thực tế.
