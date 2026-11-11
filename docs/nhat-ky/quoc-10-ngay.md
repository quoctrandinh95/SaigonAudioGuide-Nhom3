# Nhật ký 10 ngày — Quốc

Chỉ điền kết quả sau khi thực hiện. Mốc ngày 1 được nhóm thống nhất khi bắt đầu.

| Ngày | Ngày thực hiện | Công việc/file | Cách kiểm tra và kết quả thực tế | SHA/link commit |
|---|---|---|---|---|
| 1 | 09/11/2026; bổ sung nhật ký 10/11/2026 | Bàn giao solution, project API/BLL/Tests; rà soát `.gitignore` | 4 file solution/project;. Chưa xác nhận build. | - |
| 2 | 10/11/2026 | Bàn giao `.env.example`, `appsettings.json`; bổ sung hướng dẫn cấu hình, biến proxy và nhật ký | JSON và XML project đọc được; kiểm tra các tham chiếu project, nhóm cấu hình, khóa JWT mẫu dài tối thiểu 32 ký tự và biến môi trường của bản Compose gốc. `.env`/`.env.local` được bỏ qua, `.env.example` được phép theo dõi. Xem `docs/CauHinhMoiTruong.md`; chưa chạy API, MySQL, Docker hoặc kiểm thử tích hợp. | - |
| 3 | 11/11/2026 | Chuẩn bị và kiểm tra `Dtos.cs`, `Exceptions.cs`, `Security/PasswordHasher.cs`, `Visitors/UserAgentParser.cs`, `Visitors/LanguageNegotiator.cs` trong BLL | - |
| 4 | Chưa thực hiện | — | — | — |
| 5 | Chưa thực hiện | — | — | — |
| 6 | Chưa thực hiện | — | — | — |
| 7 | Chưa thực hiện | — | — | — |
| 8 | Chưa thực hiện | — | — | — |
| 9 | Chưa thực hiện | — | — | — |
| 10 | Chưa thực hiện | — | — | — |

## Chi tiết kiểm tra ngày 3 — 11/11/2026

### Phạm vi và kết quả

Đối chiếu với lịch điều chỉnh theo phụ thuộc trong `PhanCongCongViec10Ngay.md` ở bản đồ án gốc. Cả 5 file sau đã có trong thư mục repo mới, có nội dung khớp blob `HEAD` tương ứng của bản đồ án gốc:

- `src/AudioGuide.BLL/Dtos.cs`: DTO cho API công khai, quản trị, thống kê và truy cập.
- `src/AudioGuide.BLL/Exceptions.cs`: `BusinessException` và `NotFoundException`.
- `src/AudioGuide.BLL/Security/PasswordHasher.cs`: tạo và kiểm tra hash PBKDF2.
- `src/AudioGuide.BLL/Visitors/UserAgentParser.cs`: nhận diện thiết bị, trình duyệt và hệ điều hành.
- `src/AudioGuide.BLL/Visitors/LanguageNegotiator.cs`: chọn ngôn ngữ theo header, trọng số q và fallback.

Tại lúc kiểm tra, cả 5 file là file chưa được Git theo dõi (`??`). Đủ nội dung code theo phân công ngày 3 trên máy; chưa được commit hoặc push và chưa có SHA để ghi vào bảng.

### Biên dịch và kiểm thử đã chạy

Môi trường: .NET SDK `10.0.401`.

Đã thử build project thực tế:

```powershell
dotnet build src/AudioGuide.BLL/AudioGuide.BLL.csproj -c Release
```

Kết quả **không đạt** do `CS0234` tại `src/AudioGuide.DAL/DependencyInjection.cs`: namespace `AudioGuide.DAL.Repositories` chưa tồn tại. Repo mới chưa có `src/AudioGuide.DAL/Repositories/Repositories.cs`, là phần nền tảng ngày 2 của Doanh theo lịch điều chỉnh. Không bổ sung thay phần Doanh hoặc tạo namespace giả để vượt build. Cần chạy lại build sau khi file repository được bàn giao.

Lần build này cũng có cảnh báo NuGet `NU1902` đối với dependency `MySqlConnector 2.4.0`; chưa thay phiên bản package trong công việc ngày 3. Cảnh báo này tách biệt với lỗi thiếu repository ở trên.

Để kiểm tra riêng phần Quốc mà không phụ thuộc DAL chưa đủ, đã tạo project xUnit tạm ngoài repository, liên kết trực tiếp cả 5 file ngày 3. Dùng lại nguyên các lớp kiểm thử `PasswordHasherTests`, `UserAgentParserTests`, `LanguageNegotiatorTests` từ `BllTests.cs` và `VisitorTests.cs` của bản đồ án gốc; chỉ lấy các lớp độc lập này, không đưa test của các service chưa được bàn giao vào project tạm.

Project kiểm tra tạm trên máy:

```text
C:\Users\HP\AppData\Local\Temp\AudioGuide-Day3-c35599686cf44f648209cbf54c0f14a1\Day3Checks.csproj
```

Lệnh đã chạy:

```powershell
dotnet test "$env:TEMP\AudioGuide-Day3-c35599686cf44f648209cbf54c0f14a1\Day3Checks.csproj" -c Release --logger "console;verbosity=minimal"
```

Kết quả: **19 passed, 0 failed, 0 skipped**. Các ca kiểm tra mật khẩu đúng/sai, hash sai định dạng, salt ngẫu nhiên; nhận diện thiết bị/trình duyệt/HĐH; chọn ngôn ngữ, trọng số q và fallback. DTO và exception được biên dịch cùng project kiểm tra; không tuyên bố chúng có kiểm thử hành vi riêng. Project tạm chỉ là công cụ kiểm tra trên máy, không phải bộ test chính thức đã bàn giao vào repo.

### Việc còn lại

- Người dùng kiểm tra thay đổi, commit 5 file cùng nhật ký khi sẵn sàng; tiêu đề gợi ý: `feat: them DTO exception va cac helper nghiep vu`.
- Sau khi có commit, điền SHA hoặc link thật vào hàng ngày 3; tự push khi muốn bàn giao cho Hậu làm `PlaceService`.
- Doanh bổ sung file repository còn thiếu, sau đó chạy lại build BLL. Build toàn solution/API và kiểm thử tích hợp vẫn chờ các phần của những ngày sau.
