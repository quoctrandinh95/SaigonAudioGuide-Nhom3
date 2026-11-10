# Cấu hình môi trường và kiểm tra ngày 1–2

## Trạng thái bàn giao

Quốc đã bàn giao solution và project API/BLL/Tests ở commit `316ffa4` ngày 09/11/2026. `.gitignore` đã được thêm ở commit tạo khung `8a95d6c`. Hai file cấu hình của ngày 2 là `.env.example` và `src/AudioGuide.API/appsettings.json`.

Tại thời điểm kiểm tra ngày 10/11/2026, Doanh đã đưa project DAL lên và các đường dẫn project trong solution/tham chiếu project đều tồn tại. Repo chưa có đủ mã khởi động, dịch vụ và kiểm thử; chưa thể xác nhận ứng dụng chạy hoặc build thành công. Các bước chạy dưới đây dùng khi các phần liên quan đã được bàn giao.

## Hai cách cấp cấu hình

- **Chạy trực tiếp bằng .NET:** ASP.NET Core đọc `appsettings.json` và biến môi trường như `ConnectionStrings__Default`. `dotnet run` không tự đọc `.env` của Docker Compose. Repo hiện chưa thêm thư viện đọc dotenv.
- **Chạy Docker Compose:** sao chép `.env.example` thành `.env` ở thư mục gốc. Compose đọc các tên viết hoa và ánh xạ sang khóa cấu hình .NET. File compose/Docker thuộc phần bàn giao sau; hiện chưa chạy được các lệnh Docker từ repo này.

Biến môi trường .NET dùng hai dấu gạch dưới `__` thay cho dấu `:` trong tên khóa cấu hình, ví dụ `Jwt__Key` ghi đè `Jwt:Key`.

## Các biến của Docker Compose

Bảng này đối chiếu với `docker-compose.yml` trong bản đồ án hoàn chỉnh, sẽ được bàn giao ở phần Docker.

| Biến trong `.env` | Khóa .NET/đích sử dụng | Ý nghĩa |
|---|---|---|
| `MYSQL_ROOT_PASSWORD` | MySQL và `ConnectionStrings:Default` | Mật khẩu DB; Compose cấp cùng giá trị cho DB và API |
| `ADMIN_USERNAME` | `Seed:AdminUsername` | Tài khoản quản trị dùng khi seed |
| `ADMIN_PASSWORD` | `Seed:AdminPassword` | Mật khẩu seed ban đầu; không đảm bảo đổi mật khẩu tài khoản đã tồn tại |
| `JWT_KEY` | `Jwt:Key` | Khóa ký token, tối thiểu 32 ký tự |
| `ADMIN_LOCAL_ONLY` | `Admin:LocalOnly` | Giới hạn truy cập quản trị theo middleware của dự án |
| `OLLAMA_BASE_URL` | `Ollama:BaseUrl` | Docker Desktop gọi Ollama trên máy host qua `http://host.docker.internal:11434` |
| `OLLAMA_MODEL` | `Ollama:Model` | Tên model phải có trong Ollama, mẫu `translategemma:4b` |
| `OLLAMA_TIMEOUT_SECONDS` | `Ollama:TimeoutSeconds` | Mẫu 600 giây; validation dự án chấp nhận 30–3600 |
| `TRUST_PROXY` | `Hosting:TrustProxy` | Mẫu `true` cho cấu hình Docker/tunnel của dự án; khi chạy trực tiếp giữ `false` nếu không có proxy |

Chỉ commit `.env.example` chứa giá trị mẫu. Mật khẩu và khóa mẫu phải được thay bằng giá trị riêng khi triển khai. Không đưa `.env` hoặc khóa thực vào Git.

## Chuỗi kết nối MySQL

| Nơi chạy API và DB | Host/port sử dụng | Ghi chú |
|---|---|---|
| API trực tiếp, MySQL cài trên máy | `localhost:3306` | `appsettings.json` đang để mật khẩu rỗng; phải sửa theo DB thực tế |
| API trực tiếp, MySQL của Compose trên cùng máy | `localhost:3307` | Dùng mật khẩu `MYSQL_ROOT_PASSWORD` đã đặt; đây là cổng host trong compose gốc |
| API và MySQL cùng Compose | `db:3306` | Compose cung cấp chuỗi kết nối này cho API; không dùng `localhost` để trỏ sang container DB |

Không đổi cổng mặc định trong `appsettings.json` chỉ để chạy với một máy cụ thể; có thể ghi đè bằng biến môi trường.

Ví dụ PowerShell cho API chạy trực tiếp với MySQL cài trên máy, sau khi repo có đủ code và máy có .NET 10:

```powershell
# Thay các giá trị mẫu trước khi chạy, không commit cấu hình riêng.
$env:ConnectionStrings__Default = 'Server=localhost;Port=3306;Database=hcmc_audioguide;User=root;Password=MAT_KHAU_DB_CUA_BAN;CharSet=utf8mb4;'
$env:Jwt__Key = 'THAY_BANG_KHOA_RIENG_CO_IT_NHAT_32_KY_TU'
$env:Seed__AdminUsername = 'admin'
$env:Seed__AdminPassword = 'THAY_BANG_MAT_KHAU_RIENG'
$env:Ollama__BaseUrl = 'http://127.0.0.1:11434'
$env:Hosting__TrustProxy = 'false'

dotnet run --project src/AudioGuide.API/AudioGuide.API.csproj
```

Các biến trên áp dụng cho phiên PowerShell hiện tại. Nếu dùng MySQL của Compose từ máy host, đổi thành `Port=3307`.

## Các nhóm còn lại trong appsettings.json

| Nhóm | Cấu hình mẫu và tác dụng dự kiến khi có đủ code |
|---|---|
| `Logging` | Mức log chung `Information`, ASP.NET Core `Warning` |
| `AllowedHosts` | Mẫu `*`; cấu hình theo môi trường triển khai |
| `Database:ServerVersion` | Rỗng: để tầng dữ liệu tự xác định phiên bản DB theo code gốc |
| `Hosting:TrustProxy` | `false` khi chạy trực tiếp |
| `Retention:VisitDays` | 90 ngày; tác vụ dọn lịch sử đọc khóa này, 0 để tắt dọn |
| `Storage:UploadPath` | Rỗng: code gốc dùng `data/uploads` dưới thư mục nội dung API; Compose ghi đè thành `/app/data/uploads` |
| `Jwt:Issuer` / `Audience` | `AudioGuide` / `AudioGuide.Admin` |
| `Jwt:ExpireMinutes` | 480 phút |
| `Seed` | Tài khoản quản trị mẫu cho khởi tạo dữ liệu |

`Admin:LocalOnly` chưa khai báo trong JSON; middleware gốc dùng mặc định `true`, có thể ghi đè bằng `Admin__LocalOnly` hoặc biến `ADMIN_LOCAL_ONLY` qua Compose.

## Kết quả kiểm tra thực tế ngày 10/11/2026

- File solution và 3 project của Quốc khớp bản đồ án gốc; cấu trúc XML hợp lệ.
- Các project được khai báo trong solution và các `ProjectReference` hiện trỏ tới file tồn tại, bao gồm project DAL do Doanh đã bàn giao.
- `appsettings.json` đọc được bằng `ConvertFrom-Json`, đủ các nhóm cấu hình đang được bản đồ án gốc sử dụng; nội dung khớp bản gốc.
- Khóa JWT mẫu trong JSON dài 51 ký tự; khóa mẫu trong `.env.example` dài 48 ký tự. Đây chỉ là kiểm tra độ dài, không phải xác nhận an toàn của khóa mẫu.
- Đã đối chiếu các biến môi trường với Compose gốc; bổ sung `TRUST_PROXY` vào `.env.example` để thể hiện biến Compose đã hỗ trợ.
- `.env` và `.env.local` bị Git bỏ qua; `.env.example` không bị bỏ qua. Không có `.env` hoặc `.env.local` trong danh sách file được theo dõi.
- Chưa chạy restore/build/API/MySQL/Ollama/Docker hoặc kiểm thử tích hợp cho repo mới. Các mục này cần kiểm tra khi có đủ code và dịch vụ.
