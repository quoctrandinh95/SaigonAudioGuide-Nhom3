# Saigon Audio Guide

Hệ thống thuyết minh du lịch tự động đa ngôn ngữ tại TP.HCM. Ứng dụng xác định vị trí du khách, hiển thị địa điểm trên bản đồ và phát thuyết minh khi đi vào phạm vi địa điểm. Trang quản trị hỗ trợ quản lý nội dung, bản dịch, tệp tải lên và thống kê truy cập.

## Thành viên và phân công

| Thành viên | Phạm vi chính |
|---|---|
| Quốc — nhóm trưởng | Cấu hình, DTO/helper dùng chung, xác thực JWT, theo dõi truy cập, tích hợp, Docker và CI |
| Doanh | Cơ sở dữ liệu, repository, quản lý địa điểm/ngôn ngữ, thống kê lượt nghe và giao diện quản trị |
| Hậu | API công khai, bản đồ/GPS, phát thuyết minh, dịch Ollama và kiểm thử liên quan |

Đồ án đã có bản hoàn chỉnh trước khi tạo repository này. Lịch sử commit ghi nhận quá trình bàn giao, kiểm tra và hoàn thiện phần được phân công.

## Trạng thái hiện tại

Kiểm tra trên máy ngày **14/11/2026**:

- Đã có đầy đủ 49 file code, kiểm thử và script tương ứng với bản đồ án gốc.
- Build .NET thành công; **66/66 kiểm thử .NET** và **1/1 kiểm thử JavaScript** đạt.
- Đã có Dockerfile, Docker Compose và workflow GitHub Actions.
- Chưa xác nhận chạy Docker/MySQL, GPS thật hoặc dịch Ollama thực tế trong lần kiểm tra này. Kết quả trên máy không thay thế kết quả CI trên GitHub.

Build vẫn có cảnh báo `NU1902` về MySqlConnector 2.4.0, `EF1002` về SQL nội suy và `ASPDEPR005` về API cấu hình forwarded headers cũ; cần rà soát khi bảo trì.

## Tính năng chính

- Bản đồ Leaflet, danh sách địa điểm, ảnh và chế độ demo vị trí.
- Thuyết minh theo GPS, phát MP3 hoặc giọng đọc Web Speech API, tô sáng câu và hàng đợi phát.
- Chọn ngôn ngữ theo thiết bị hoặc thủ công; fallback khi thiếu bản dịch.
- Quản trị địa điểm, nội dung đa ngôn ngữ, ảnh, âm thanh và ngôn ngữ hỗ trợ.
- Dịch nội dung tiếng Việt bằng Ollama; xem lại bản dịch trước khi lưu.
- Thống kê lượt nghe, phiên truy cập, thiết bị, ngôn ngữ và khách đang online.
- Xác thực JWT, băm mật khẩu PBKDF2 và kiểm tra dữ liệu ở giao diện, nghiệp vụ và database.

## Công nghệ và cấu trúc

.NET 10, ASP.NET Core Web API, EF Core/Pomelo MySQL, MySQL 8.4, HTML/CSS/JavaScript, Leaflet, Ollama và xUnit.

```text
SaigonAudioGuide-Nhom3/
├── .github/workflows/ci.yml     # Restore, build, test và Docker build
├── docs/                        # Use case, hướng dẫn dịch và SQL mẫu
├── scripts/start-ollama.ps1      # Hỗ trợ chạy Ollama trên Windows
├── src/
│   ├── AudioGuide.API/          # Controller, middleware, cấu hình, JWT
│   │   └── wwwroot/             # Giao diện du khách và admin
│   ├── AudioGuide.BLL/          # DTO, validation, helper và service nghiệp vụ
│   └── AudioGuide.DAL/          # Entity, DbContext, repository, constraint, seed
├── tests/
│   ├── AudioGuide.Tests/        # Kiểm thử .NET
│   └── admin-translation.test.cjs
├── HcmcAudioGuide.slnx
├── .env.example
├── Dockerfile
└── docker-compose.yml
```

## Chạy bằng Docker Desktop

Cài và bật Docker Desktop. Các container cung cấp API, MySQL và phpMyAdmin; không cần cài .NET SDK hoặc MySQL riêng cho cách chạy này. Tính năng dịch tự động cần Ollama chạy riêng trên máy host.

Nếu chưa có repository trên máy:

```powershell
git clone https://github.com/quoctrandinh95/SaigonAudioGuide-Nhom3.git
Set-Location SaigonAudioGuide-Nhom3
```

Từ thư mục gốc dự án, tạo `.env` nếu chưa có:

```powershell
if (-not (Test-Path -LiteralPath .env)) {
    Copy-Item -LiteralPath .env.example -Destination .env
}
```

Sửa `.env` theo môi trường sử dụng, đặc biệt là `MYSQL_ROOT_PASSWORD`, `ADMIN_PASSWORD` và `JWT_KEY`. Khóa JWT phải dài tối thiểu 32 ký tự. Không commit `.env` hoặc thông tin xác thực thật.

Khởi động:

```powershell
docker compose up -d --build
docker compose logs -f api
```

Ứng dụng tạo bảng và seed dữ liệu khi khởi động lần đầu với database mới. Chờ quá trình khởi tạo hoàn tất rồi truy cập:

| Dịch vụ | Địa chỉ | Tài khoản/cấu hình |
|---|---|---|
| Du khách | http://localhost:8080/ | Không cần đăng nhập |
| Admin | http://localhost:8080/admin/ | Theo `ADMIN_USERNAME` / `ADMIN_PASSWORD` lúc seed; mẫu là `admin` / `Admin@123` |
| Kiểm tra sức khỏe API | http://localhost:8080/health | Endpoint health check |
| phpMyAdmin | http://localhost:8081/ | `root` và `MYSQL_ROOT_PASSWORD` |
| MySQL từ máy host | `localhost:3307` | Database `hcmc_audioguide` |

Thay `ADMIN_PASSWORD` trong `.env` không tự đổi mật khẩu tài khoản đã có trong database; dùng chức năng đổi mật khẩu của trang admin. Dữ liệu MySQL nằm trong volume `mysql_data`, file tải lên nằm trong volume `uploads`.

Dừng dịch vụ và giữ các volume dữ liệu:

```powershell
docker compose down
```

## Cấu hình môi trường

| Biến trong `.env` | Tác dụng |
|---|---|
| `MYSQL_ROOT_PASSWORD` | Mật khẩu MySQL; Compose cấp cùng giá trị cho kết nối API |
| `ADMIN_USERNAME`, `ADMIN_PASSWORD` | Tài khoản quản trị khi seed ban đầu |
| `JWT_KEY` | Khóa ký JWT |
| `OLLAMA_BASE_URL` | Docker Desktop truy cập Ollama host qua `http://host.docker.internal:11434` |
| `OLLAMA_MODEL` | Model dịch, mẫu `translategemma:4b` |
| `OLLAMA_TIMEOUT_SECONDS` | Thời gian chờ dịch, mẫu 600 giây |
| `ADMIN_LOCAL_ONLY` | Mẫu `true`: chặn các đường dẫn admin khi yêu cầu đi qua Cloudflare Tunnel theo middleware hiện tại |
| `TRUST_PROXY` | Mẫu `true` trong Compose; bật xử lý forwarded headers khi chạy sau proxy |

**Chạy API trực tiếp bằng .NET:** cần .NET SDK 10 và MySQL sẵn sàng. ASP.NET Core đọc [appsettings.json](src/AudioGuide.API/appsettings.json); `dotnet run` không tự đọc `.env` của Compose. Ghi đè bằng biến môi trường .NET như `ConnectionStrings__Default`, `Jwt__Key` và `Seed__AdminPassword`.

Chuỗi kết nối mẫu trong JSON dùng `localhost:3306` với mật khẩu rỗng. Khi API chạy trực tiếp và kết nối MySQL của Compose, dùng `localhost:3307` với mật khẩu đã đặt. API bên trong Compose kết nối tới `db:3306`.

Sau khi cấu hình kết nối và khóa phù hợp:

```powershell
dotnet run --project src/AudioGuide.API/AudioGuide.API.csproj --no-launch-profile --urls http://localhost:52612
```

Truy cập http://localhost:52612/ và http://localhost:52612/admin/. Khi chạy trực tiếp không có proxy, giữ `Hosting:TrustProxy=false` như cấu hình JSON mặc định.

## Dịch tự động bằng Ollama

Ollama không được đóng gói trong Compose. Cần cài/chạy Ollama và tải model tương ứng trước khi dùng nút dịch trên admin.

Xem [hướng dẫn Ollama](docs/OllamaTranslation.md), gồm script chạy trên Windows, cấu hình để Docker truy cập host và kiểm tra model. Các đường dẫn mặc định trong script có thể cần chỉnh theo máy của bạn.

## Demo và truy cập từ điện thoại

- Mở trang du khách, nhấn **Bắt đầu**, chọn **Chế độ demo** và nhấp vào vùng bán kính địa điểm trên bản đồ. Vị trí demo không được ghi thành vị trí GPS thật.
- Trình duyệt cần thao tác chạm/click để mở khóa âm thanh. Giọng đọc phụ thuộc ngôn ngữ được cài trên thiết bị; có thể dùng file MP3 trong nội dung địa điểm.
- GPS thật cần quyền vị trí và môi trường trình duyệt phù hợp; trên điện thoại nên dùng HTTPS.

Compose có profile Cloudflare Tunnel để chia sẻ trang du khách:

```powershell
docker compose --profile tunnel up -d --build
docker compose logs tunnel
```

Lấy URL HTTPS từ log tunnel. Máy host và Docker phải tiếp tục chạy; URL có thể đổi khi tạo lại tunnel. Với `ADMIN_LOCAL_ONLY=true`, middleware chặn `/admin` và `/api/admin` khi nhận diện header Cloudflare Tunnel. MySQL và phpMyAdmin được bind vào `127.0.0.1` trên máy host trong Compose.

## Build và kiểm thử

Cần .NET SDK 10; dùng Node.js 24 để khớp cấu hình CI hiện tại.

```powershell
dotnet restore HcmcAudioGuide.slnx
dotnet build HcmcAudioGuide.slnx -c Release --no-restore
dotnet test HcmcAudioGuide.slnx -c Release --no-build
node --check src/AudioGuide.API/wwwroot/js/app.js
node --check src/AudioGuide.API/wwwroot/admin/admin.js
node --test tests/admin-translation.test.cjs
```

Các test tự động không thay thế kiểm tra GPS/âm thanh trên thiết bị thật, dịch Ollama thật hoặc CHECK constraint trên MySQL. Phần kiểm thử dữ liệu dùng EF Core InMemory không xác nhận đầy đủ hành vi của MySQL.

Workflow [CI](.github/workflows/ci.yml) chạy khi push vào `main` hoặc mở/cập nhật PR hướng tới `main`: restore → build → test .NET/JavaScript → build Docker image. Workflow chưa triển khai ứng dụng lên hosting.

## Tài liệu

- [Use case, business rules và sơ đồ](docs/FocusedUseCases.md).
- [Hướng dẫn dịch Ollama](docs/OllamaTranslation.md).
- [Truy vấn SQL mẫu](docs/queries.sql).

## Quy trình làm việc nhóm

Đồng bộ thay đổi trước khi làm việc, chỉ stage phần của mình và kiểm tra `git diff --cached` trước khi commit. Không đổi tác giả/ngày của commit đã có, không force push lên `main`. Mỗi thành viên tự commit và push phần thực tế đã kiểm tra của mình.
