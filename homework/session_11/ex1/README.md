# BÁO CÁO THỰC HÀNH CI/CD: TỰ ĐỘNG HÓA KIỂM THỬ SPRING BOOT (GRADLE) VỚI GITHUB ACTIONS

> **Môn học:** IT-209 – DevOps Fundamentals  
> **Bài thực hành:** Session 11 – Bài 1 (Exercise 1)  
> **Đường dẫn nộp bài:** `homework/session_11/ex1/`  
> **Môi trường CI Runner:** Ubuntu Latest (`ubuntu-latest`)  
> **JDK:** Java 17 (Eclipse Temurin) | Build Tool: Gradle Wrapper (`gradlew`)  

---

## 1. Yêu cầu đề bài

1. Trong thư mục repository dự án Spring Boot (Gradle), khởi tạo tệp `.github/workflows/ci.yml`.
2. Khai báo điều kiện kích hoạt workflow khi có sự kiện `push` lên nhánh `main`.
3. Định nghĩa Job `test` (Run Unit Tests) chạy trên runner `ubuntu-latest`:
   - Checkout mã nguồn với `actions/checkout@v5`.
   - Cài đặt Java JDK 17 với `actions/setup-java@v5` (distribution: `temurin`).
   - Cấp quyền thực thi cho script wrapper: `run: chmod +x gradlew`.
   - Thực thi câu lệnh kiểm thử: `run: ./gradlew test`.
4. Kiểm tra:
   - Đẩy mã nguồn lên GitHub repository.
   - Truy cập tab **Actions** trên GitHub Repository.
   - Xác nhận Job **Run Unit Tests** khởi chạy tự động, thực thi JUnit test bằng Gradle và đạt trạng thái tích xanh (**Success**).
5. Sản phẩm nộp bài:
   - Tệp kịch bản `.github/workflows/ci.yml` (hoặc `homework/session_11/ex1/ci.yml`).
   - Ảnh chụp màn hình tab GitHub Actions hiển thị Job Run Unit Tests thực thi thành công (tích xanh).

---

## 2. Cấu hình kịch bản Workflow CI (`.github/workflows/ci.yml`)

Nội dung tệp `.github/workflows/ci.yml`:

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

      - name: Cấp quyền cho Gradle Wrapper
        run: chmod +x gradlew

      - name: Thực thi JUnit Test với Gradle
        run: ./gradlew test
```

---

## 3. Giải thích chi tiết các thành phần trong Workflow

| Thành phần / Chỉ lệnh | Ý nghĩa kỹ thuật |
|-----------------------|------------------|
| `name: Java Spring Boot CI (Gradle)` | Tên hiển thị của workflow trên giao diện GitHub Actions tab. |
| `on.push.branches: [ "main" ]` | Event trigger: Kích hoạt pipeline CI tự động mỗi khi có commit mới được push lên nhánh `main`. |
| `jobs.test.runs-on: ubuntu-latest` | Môi trường máy ảo runner thực thi job: Ubuntu bản mới nhất do GitHub quản lý (GitHub-hosted runner). |
| `uses: actions/checkout@v5` | Action chính thức của GitHub để clone/checkout toàn bộ mã nguồn của repository vào không gian làm việc của runner. |
| `uses: actions/setup-java@v5` | Action cài đặt máy ảo Java (JDK):<br>- `java-version: '17'`: Chỉ định phiên bản Java 17 tương thích với Spring Boot 3/4.<br>- `distribution: 'temurin'`: Sử dụng bản phân phối OpenJDK Eclipse Temurin (Adoptium) chuẩn doanh nghiệp. |
| `run: chmod +x gradlew` | Cấp quyền thực thi (`execute`) cho tệp script `gradlew` trên hệ điều hành Linux/Ubuntu runner, tránh lỗi `Permission denied` khi chạy wrapper. |
| `run: ./gradlew test` | Lệnh gọi Gradle Wrapper để biên dịch test code và thực thi toàn bộ JUnit Unit Tests (`src/test/java`). Trả về exit code 0 nếu tất cả test case đều PASS. |

---

## 4. Các bước đẩy lên GitHub và kích hoạt CI

Để đẩy mã nguồn lên GitHub Repository của bạn và kiểm tra:

### Bước 1: Commit mã nguồn tại thư mục bài làm
```bash
git add .
git commit -m "feat(ci): configure GitHub Actions CI workflow for Spring Boot Gradle test"
```

### Bước 2: Tạo repository mới trên GitHub (nếu chưa có) và gán Remote
Ví dụ tạo repo `IT_209_Devops_Fundamentals_session11` trên GitHub:
```bash
git remote add origin https://github.com/Hoangnguyenduc10-06-2006/IT_209_Devops_Fundamentals_session11.git
git branch -M main
git push -u origin main
```
*(Hoặc push vào repo bạn muốn nộp bài)*

### Bước 3: Kiểm tra trên GitHub Actions Tab
1. Mở trình duyệt, truy cập vào repository trên GitHub.
2. Nhấp vào tab **Actions**.
3. Bạn sẽ thấy workflow **Java Spring Boot CI (Gradle)** tự động khởi chạy với commit mới nhất.
4. Bấm vào chi tiết run để xem job **Run Unit Tests**:
   - Step `Checkout mã nguồn`: Hoàn thành.
   - Step `Thiết lập môi trường Java JDK 17`: Hoàn thành.
   - Step `Cấp quyền cho Gradle Wrapper`: Hoàn thành.
   - Step `Thực thi JUnit Test với Gradle`: Hoàn thành với kết quả `BUILD SUCCESSFUL`.
   - Trạng thái tổng thể: **Success** (Tích xanh ✔).

---

## 5. Kết quả thực thi (Ảnh minh chứng)

*(Đính kèm ảnh chụp màn hình tab GitHub Actions hiển thị Job Run Unit Tests thực thi tích xanh tại đây)*

![GitHub Actions Success Screenshot](./screenshot.png)
