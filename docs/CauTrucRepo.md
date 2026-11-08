# Quy ước cấu trúc repository

Khung này bám theo cấu trúc bản đồ án hoàn chỉnh. Chưa chứa file `.slnx`, `.csproj` hoặc code chức năng; các thành viên bổ sung theo lịch phân công, không tạo project rỗng để thay thế project gốc.

## Thư mục và nội dung dự kiến

| Đường dẫn | Nội dung |
|---|---|
| `.github/workflows/` | Workflow build/test và Docker build do Quốc tích hợp khi có đủ dependency |
| `src/AudioGuide.API/Controllers/` | API công khai và các controller quản trị |
| `src/AudioGuide.API/Middleware/` | Xử lý lỗi HTTP dùng chung |
| `src/AudioGuide.API/Security/` | Phát JWT |
| `src/AudioGuide.API/Storage/` | Cấu hình upload và dọn lịch sử truy cập |
| `src/AudioGuide.API/Translation/` | Dịch nội dung qua Ollama |
| `src/AudioGuide.API/wwwroot/` | Trang HTML du khách |
| `src/AudioGuide.API/wwwroot/admin/` | HTML và JavaScript quản trị |
| `src/AudioGuide.API/wwwroot/css/` | CSS giao diện |
| `src/AudioGuide.API/wwwroot/js/` | JavaScript du khách, bản đồ, GPS, âm thanh và ping |
| `src/AudioGuide.BLL/` | DTO, exception, đăng ký dịch vụ và file project |
| `src/AudioGuide.BLL/Services/` | Nghiệp vụ xác thực, địa điểm, ngôn ngữ, thống kê và truy cập |
| `src/AudioGuide.BLL/Geo/` | Tính khoảng cách địa lý |
| `src/AudioGuide.BLL/Security/` | Băm và kiểm tra mật khẩu |
| `src/AudioGuide.BLL/Visitors/` | Nhận diện thiết bị và thương lượng ngôn ngữ |
| `src/AudioGuide.DAL/` | DbContext, CHECK constraint, seeder, đăng ký DAL và file project |
| `src/AudioGuide.DAL/Entities/` | Entity dữ liệu |
| `src/AudioGuide.DAL/Repositories/` | Truy vấn và thao tác dữ liệu |
| `src/AudioGuide.DAL/Seed/` | Dữ liệu mẫu được phép chia sẻ |
| `tests/AudioGuide.Tests/` | Project xUnit và kiểm thử .NET |
| `tests/` | Kiểm thử JavaScript, ví dụ `admin-translation.test.cjs` |
| `scripts/` | Script hỗ trợ, ví dụ khởi động Ollama |
| `docs/` | Use case, SQL mẫu, hướng dẫn dịch và phân công |
| `docs/nhat-ky/` | Kết quả công việc thực tế theo ngày của từng thành viên |

Các file solution, Docker, compose và cấu hình mẫu đặt ở gốc khi được bàn giao. `Program.cs` và `appsettings.json` đặt ở gốc project API. Không đổi namespace hoặc di chuyển code sang cấu trúc khác khi đưa code lên.

## Phối hợp ở các phần dùng chung

- Quốc phụ trách sửa chính `Program.cs`, đăng ký dịch vụ BLL, cấu hình hạ tầng và README.
- Doanh phụ trách sửa chính DTO, repositories, `AdminPlacesController.cs` và `wwwroot/admin/admin.js`.
- Hậu phụ trách sửa chính `wwwroot/js/app.js`, CSS và `BllTests.cs`.
- Các thành viên trao đổi trước khi sửa phần người khác phụ trách; mỗi commit chỉ chứa thay đổi đã kiểm tra của người thực hiện.

Khi cùng một file được phân công nhiều ngày, mỗi ngày bổ sung một phần chức năng thực tế. Nếu đã đưa toàn bộ file lên, chỉ commit tiếp khi có thay đổi hữu ích hoặc nhật ký kiểm tra có bằng chứng.

## Điều kiện nghiệm thu

Chỉ bổ sung lệnh chạy chính thức khi solution/project đã được đưa lên. Cuối kỳ cần kiểm tra build .NET, xUnit, kiểm thử JavaScript, Docker/MySQL và luồng thao tác trên trình duyệt. Các ngày đầu có thể chưa chạy được vì đang bàn giao từng phần; không ghi nhận kiểm tra đạt khi chưa thực hiện.
