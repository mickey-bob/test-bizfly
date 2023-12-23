# Nội dung thực hành buổi 5 Bizflycloud Workshop với chủ đề CI/CD

## Nội dung chính
Người tham dự cần thực hiện được các nội dung sau
- Cài đặt argocd lên BKE
- Lấy mật khẩu admin, truy cập dashboard của argocd
- Tạo application trên argocd
- Làm quen với công cụ Kustomize
- Thay đổi image cho ứng dụng

## Mô tả
Có 2 repository được sử dụng trong phần demo bao gồm repo chứa code của ứng dụng và repo chứa kubernetes manifest dùng để triển khai ứng dụng
```bash
https://gitlab.com/bke-workshop/ip-tracker-app.git
https://gitlab.com/bke-workshop/application-deployment.git

### Cài đặt Argocd
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.9.3/manifests/install.yaml

### Lấy mật khẩu admin
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

Port-forward, truy cập dashboard thông qua localhost:8888
```bash
kubectl port-forward svc/argocd-server 8888:80 -n argocd

### Tạo repository trên gitlab
Tạo repository cá nhân trên gitlab, có thể để public để dễ dàng thao tác cho bài lab. Sau đó chạy các lệnh sau:
```bash
git clone https://gitlab.com/bke-workshop/application-deployment.git
cd application-deployment
rm -rf .git
git init --initial-branch=main
git remote add origin repo_url (lưu ý thay repo_url bằng repo_url đã được tạo ra trước đó, dử dụng https thay vì ssh nếu như chưa có ssh key)
git add .
git commit -m "Initial commit"
git push --set-upstream origin main

### Tạo application trên Argocd
Truy cập dashboard với thông tin đăng nhập đã lấy được từ bước 2 và tạo application theo hướng dẫn của diễn giả.

### Làm quen với công cụ Kustomize (generate kubernetes manifest)
Từ thư mục application-deployment
```bash
cd kustomize/ip-tracker-app/overlays/workshop
kubectl kustomize

### Thay đổi image cho ứng dụng
Tại thư mục kustomize/ip-tracker-app/overlays/workshop
```bash
sed -i "s/newTag: .*/newTag: v1.0.1/g" kustomization.yaml

### Cập nhật thay đổi trên git remote
```bash
git add .
git commit -m "update(ip-tracker-app): change image tag to v1.0.1"
git push -u origin main
