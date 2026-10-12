# Báo Cáo Tối Ưu Tốc Độ CI Bằng Gradle Dependency Caching

## 1. Mục tiêu
Bật tính năng Caching cho Gradle Dependencies trong GitHub Actions Workflow nhằm giảm thời gian tải các thư viện từ Internet trong mỗi lần chạy CI, qua đó rút ngắn thời gian thực thi pipeline.

## 2. Cấu hình triển khai
Bổ sung tham số `cache: 'gradle'` vào bước cài đặt Java (`actions/setup-java@v5`) trong file `.github/workflows/ci.yml`:

```yaml
- name: Thiết lập môi trường Java JDK 17
  uses: actions/setup-java@v5
  with:
    java-version: '17'
    distribution: 'temurin'
    cache: 'gradle'
```

## 3. Kết quả so sánh thời gian thực thi

| Tiêu chí | Lần chạy 1 (Chưa có Cache) | Lần chạy 2 (Đã có Cache) |
| :--- | :--- | :--- |
| **Trạng thái Cache** | Cache not found (Lưu cache mới ở Post job) | `Cache restored successfully from key: gradle-...` |
| **Bước Setup Java** | Tải JDK và chuẩn bị lưu cache | Khôi phục cache Gradle từ key lưu trữ |
| **Bước Test & Build** | Tải các dependency từ Maven Central | Tái sử dụng các dependency từ Cache cục bộ |
| **Tổng thời gian chạy** | Lâu hơn (~25s - 30s) | Nhanh hơn đáng kể (~15s - 20s) |

## 4. Kết luận
- Việc kích hoạt `cache: 'gradle'` giúp GitHub Actions lưu trữ thư mục `~/.gradle/caches` và khôi phục lại ở các lần chạy tiếp theo.
- Giảm tải băng thông mạng, loại bỏ thời gian tải lại các thư viện kiểm thử (JUnit, Gradle Wrapper).
- Tốc độ hoàn thành của pipeline CI được tối ưu hóa rõ rệt.
