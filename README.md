# Xử Lý Song Song Các Job Trong Kubernetes

## Tổng Quan

Dự án này minh họa cách sử dụng Kubernetes để chạy các job xử lý song song. Mỗi job sẽ xử lý một item cụ thể, cụ thể là apple, banana hoặc cherry. Việc này được thực hiện thông qua việc sử dụng tài nguyên Job trong Kubernetes, được sinh ra từ một mẫu sử dụng Jinja2.

## Yêu Cầu Cần Thiết

Trước khi bắt đầu, hãy đảm bảo bạn đã cài đặt những thứ sau:

- Một cluster Kubernetes (Minikube, hoặc truy cập vào dịch vụ Kubernetes được quản lý)
- Công cụ dòng lệnh `kubectl`, đã được cấu hình để giao tiếp với cluster của bạn
- Python và Jinja2 nếu bạn muốn sử dụng các tính năng tạo mẫu nâng cao

## Thiết Lập

### 1. Cài Đặt Jinja2

Nếu bạn muốn sử dụng các tính năng tạo mẫu nâng cao, bạn cần cài đặt Jinja2 bằng pip:

```
pip install --user jinja2
```

### 2. Clone Kho Lưu Trữ

Clone kho lưu trữ này về máy tính của bạn để bắt đầu với dự án:

```
git clone <đường-dẫn-kho>
cd <thư-mục-kho>
```

## Sử Dụng

### Tạo Manifest Các Job

Bạn có thể sinh ra các manifest Job Kubernetes sử dụng lệnh sed cho tạo mẫu cơ bản hoặc một script Python cho tạo mẫu Jinja2 nâng cao.

### Sử Dụng sed Cho Tạo Mẫu Cơ Bản

Tải mẫu job:

```
curl -L -s -O https://k8s.io/examples/application/job/job-tmpl.yaml
```

### Tạo manifest job cho mỗi item:

```
mkdir ./jobs

for i in apple banana cherry
do
cat job-tmpl.yaml | sed "s/\$ITEM/$i/" > ./jobs/job-$i.yaml
done
```

### Sử Dụng Jinja2 Cho Tạo Mẫu Nâng Cao

Tạo manifest job sử dụng mẫu Jinja2:

```
cat job.yaml.jinja2 | python -c "from jinja2 import Template; import sys; print(Template(sys.stdin.read()).render());" > jobs.yaml
```

### Tạo Các Job Trong Kubernetes

Áp dụng các manifest đã tạo vào cluster của bạn:

```
kubectl apply -f ./jobs
```

### Giám Sát Các Job

Kiểm tra trạng thái của các job:

```
kubectl get jobs -l jobgroup=jobexample
```

### Xem Logs

Xem logs từ tất cả các job:

```
kubectl logs -f -l jobgroup=jobexample
```

### Dọn Dẹp

Xóa tất cả các job đã tạo:

```
kubectl delete job -l jobgroup=jobexample
```

File README này hướng dẫn người dùng từng bước cần thực hiện để thiết lập môi trường, tạo các job từ mẫu, và quản lý cũng như dọn dẹp các tài nguyên sau khi các job đã được chạy. Đây là một hướng dẫn đầy đủ cho việc triển khai và quản lý các job xử lý song song trong một môi trường Kubernetes.
