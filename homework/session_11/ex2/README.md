# BÁO CÁO THỰC HÀNH CI/CD: ĐÓNG GÓI VÀ LƯU TRỮ ARTIFACT VỚI GITHUB ACTIONS

> **Môn học:** IT-209 – DevOps Fundamentals  
> **Bài thực hành:** Session 11 – Bài 2 (Exercise 2)  
> **Đường dẫn nộp bài:** `homework/session_11/ex2/`  
> **Môi trường CI Runner:** Ubuntu Latest (`ubuntu-latest`)  
> **Action lưu trữ:** `actions/upload-artifact@v4`  

---

## 1. Yêu cầu đề bài

1. Mở tệp `.github/workflows/ci.yml` đã tạo từ EX1.
2. Thêm bước đóng gói sản phẩm Java bằng Gradle:
   - `run: ./gradlew bootJar`
3. Thêm bước đính kèm Artifact sử dụng `actions/upload-artifact@v4`:
   - Đặt tên cho artifact là `app-jar`.
   - Cấu hình đường dẫn tệp tải lên: `build/libs/*.jar`.
4. Kiểm tra:
   - Đẩy commit mới lên nhánh `main`.
   - Sau khi Workflow chạy xong, kiểm tra phần **Summary** của luồng chạy trên tab **Actions**, đảm bảo xuất hiện tệp artifact `app-jar` từ thư mục `build/libs/` cho phép tải về máy.
5. Sản phẩm nộp bài:
   - Tệp kịch bản `.github/workflows/ci.yml` (hoặc `homework/session_11/ex2/ci.yml`).
   - Ảnh chụp màn hình phần Summary trên tab Actions hiển thị tệp artifact `app-jar`.

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

## 3. Giải thích 2 bước mới được bổ sung

| Bước (Step) | Chỉ lệnh / Action | Ý nghĩa kỹ thuật |
|-------------|-------------------|------------------|
| **Đóng gói tệp JAR với Gradle** | `run: ./gradlew bootJar` | Thực thi task `bootJar` của Spring Boot Gradle Plugin để đóng gói toàn bộ mã nguồn, tài nguyên và dependencies thành một tệp JAR thực thi độc lập (executable fat JAR) đặt tại thư mục `build/libs/`. |
| **Lưu trữ thành phẩm JAR Artifact** | `uses: actions/upload-artifact@v4` | Sử dụng action upload artifact chính thức v4 của GitHub để thu thập tệp `.jar` từ `build/libs/*.jar` trên runner và tải lên lưu trữ trên hạ tầng GitHub Actions dưới tên artifact `app-jar`. |

---

## 4. Các bước đẩy commit và kiểm tra trên GitHub

### Bước 1: Commit thay đổi và Push lên GitHub
```bash
git add .
git commit -m "feat(ci): add bootJar packaging and upload-artifact to CI workflow"
git push origin main
```

### Bước 2: Kiểm tra kết quả trên tab Actions
1. Truy cập vào GitHub Repository: `https://github.com/Hoangnguyenduc10-06-2006/IT_209_Devops_Fundamentals_session11`.
2. Chọn tab **Actions**.
3. Chọn lần chạy mới nhất (triggered bởi commit vừa push).
4. Quan sát:
   - Job **Run Unit Tests** hoàn thành thành công (tích xanh ✔).
   - Truy cập trang **Summary** của Workflow run.
   - Tại mục **Artifacts** ở dưới cùng trang Summary, xuất hiện tệp artifact `app-jar` (dung lượng tệp `.zip` chứa file JAR thực thi) với nút tải về.

---

## 5. Ảnh chụp minh chứng kết quả

*(Đính kèm ảnh chụp màn hình phần Summary trên tab Actions hiển thị artifact `app-jar` tại đây)*

![Artifact Summary Screenshot](./screenshot_artifact.png)
