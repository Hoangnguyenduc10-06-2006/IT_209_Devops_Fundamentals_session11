# BÁO CÁO THỰC HÀNH CI/CD: TỐI ƯU HÓA THỜI GIAN THỰC THI WORKFLOW VỚI GRADLE CACHE

> **Môn học:** IT-209 – DevOps Fundamentals  
> **Bài thực hành:** Session 11 – Bài 3 (Exercise 3)  
> **Đường dẫn nộp bài:** `homework/session_11/ex3/`  
> **Action sử dụng:** `actions/setup-java@v5` với tham số `cache: 'gradle'`  
> **Mục tiêu:** Cấu hình caching cho Gradle dependencies, thực hiện push 2 lần liên tiếp để đo lường và đánh giá sự chênh lệch thời gian thực thi.

---

## 1. Yêu cầu đề bài

1. Mở tệp `.github/workflows/ci.yml`.
2. Trong bước `actions/setup-java@v5`, bổ sung tham số `cache: 'gradle'`.
3. Thực hiện push code 2 lần liên tiếp để kiểm tra sự chênh lệch thời gian thực thi của bước `setup-java` và `./gradlew test`.
4. Kiểm tra:
   - **Lần push thứ 2:** Kiểm tra log của bước `setup-java`, thấy dòng thông báo khôi phục cache Gradle thành công:
     ```text
     Cache restored successfully from key: gradle-...
     ```
   - Thời gian chạy toàn bộ Job giảm đáng kể so với lần đầu tiên.
5. Sản phẩm nộp bài:
   - Tệp kịch bản `.github/workflows/ci.yml` (hoặc `homework/session_11/ex3/ci.yml`).
   - Tệp báo cáo so sánh thời gian thực thi `report.md` giữa lần đầu (chưa có cache / tạo cache) và lần sau (đã có cache).

---

## 2. Kịch bản Workflow hoàn chỉnh (`.github/workflows/ci.yml`)

```yaml
name: Java Spring Boot CI (Gradle)

on:
  push:
    branches: [ "main" ]

jobs:
  test:
    name: Run Unit Tests
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v5

      - name: Thiết lập môi trường Java JDK 17
        uses: actions/setup-java@v5
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'gradle'

      - name: Cấp quyền cho Gradle Wrapper
        run: chmod +x gradlew

      - name: Thực thi JUnit Test với Gradle
        run: ./gradlew test

      - name: Đóng gói tệp JAR với Gradle
        run: ./gradlew bootJar

      - name: Lưu trữ thành phẩm JAR Artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-jar
          path: build/libs/*.jar
```

---

## 3. Cơ chế hoạt động của Gradle Cache trong `setup-java`

Khi cấu hình `cache: 'gradle'` trong action `actions/setup-java`:
1. **Lần chạy đầu tiên (Cache Miss):**
   - Runner kiểm tra cache key dựa trên hash của các tệp cấu hình Gradle (như `build.gradle`, `settings.gradle`, `gradle-wrapper.properties`).
   - Không tìm thấy cache có sẵn trên GitHub Actions Cache Server.
   - Quá trình build/test tải các dependencies và wrapper từ Maven Central / Gradle services.
   - Tại bước Post Action (`Post Thiết lập môi trường Java JDK 17`), toàn bộ thư mục `.gradle/caches` và `.gradle/wrapper` được nén và tải lên GitHub Cache Storage.

2. **Lần chạy thứ hai (Cache Hit):**
   - Action kiểm tra và tìm thấy cache key khớp (`Cache restored successfully from key: gradle-...`).
   - Các dependencies và wrapper được giải nén trực tiếp vào Runner ngay ở bước `setup-java`.
   - Khi bước `./gradlew test` chạy, Gradle không cần tải lại bất kỳ dependency nào qua mạng, giúp giảm thiểu tối đa thời gian thực thi và tiết kiệm tài nguyên mạng/CI runner.

---

## 4. Nhật ký thực nghiệm 2 lần Push

*(Dữ liệu đang được ghi nhận chi tiết sau lần push 2)*
