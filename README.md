# Saigon Audio Guide

Đồ án hệ thống thuyết minh tự động đa ngôn ngữ.

## Thành viên

- Quốc: nhóm trưởng.
- Doanh: thành viên.
- Hậu: thành viên.

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

## Cấu hình môi trường

Đã có cấu hình mẫu trong `.env.example` và `src/AudioGuide.API/appsettings.json`. Xem [hướng dẫn cấu hình và kết quả kiểm tra](docs/CauHinhMoiTruong.md) trước khi chạy.

Repository đang được bàn giao từng phần, chưa có đủ code để chạy API hoặc kiểm thử tích hợp. Đồ án đã có bản hoàn chỉnh trước khi tạo repository này; các commit ghi nhận việc bàn giao và hoàn thiện code.

## Phân công

| Thành viên | Phạm vi chính |
|---|---|
| Quốc — nhóm trưởng | Solution/project, cấu hình, bảo mật, theo dõi truy cập, Docker, CI và tích hợp |
| Doanh | DAL, quản lý địa điểm/ngôn ngữ, thống kê lượt nghe và giao diện admin |
| Hậu | API công khai, giao diện du khách, GPS, âm thanh, Ollama và kiểm thử liên quan |


## Làm việc hằng ngày

1. Chạy `git pull --rebase origin main` trước khi làm việc.
2. Đưa phần code được giao vào đúng thư mục; kiểm tra nội dung và dependency liên quan.
3. Ghi kết quả thực tế vào nhật ký cá nhân trong `docs/nhat-ky/`.
4. Dùng `git add <duong-dan>` để chọn đúng file; kiểm tra bằng `git diff --cached`.
5. Commit, đồng bộ lại nếu cần, rồi `git push origin main`. Không force push lên `main`.

File `.gitkeep` chỉ giữ các thư mục rỗng trong Git; có thể xóa khi thư mục đã có code. Không commit `.env`, thông tin xác thực, `bin/`, `obj/` hoặc dữ liệu chạy thực tế.
