# btvn2_moblie
Sinh Viên
Họ tên: Nguyễn Nguyệt Linh
MSV: K225480106039
Lớp: 58KTPM
Môn: TEE0421 - Phát triển ứng dụng với mã nguồn mở  
1. TỔ CHỨC CSDL CHO HỆ THỐNG QUẢN LÝ TIỆM CẦM ĐỒ: viết tay ra giấy, lấy điện thoại chụp lại, upload ảnh lên github (đã nói về các nghiệp vụ trên lớp, ghi bảng)

2. SỬ DỤNG DOCKER TRÊN UBUNTU ĐỂ:

Mariadb : chứa csdl của hệ thống này
Phpmyadmin: để soi được csdl (chỉ để xem, ko cần tạo bảng từ đây, django sẽ làm hết)
Django: build 1 docker container (dùng Dockerfile): trên nền python, sử dụng django, nhớ mount thư mục để dễ edit, edit dùng: sudo nano ten_file
sau khi có 3 service này trong file docker-compose.yml :

run nó, cấu hình để Django nhận csdl mariadb (sửa file settings.py), cấu hình user login ban đầu, mô tả các bảng trong models.py, .... (đc phép sử dụng AI để làm) => KQ được trang admin, y/c đăng nhập, vào trang admin: cho phép thêm sửa xoá dữ liệu các bảng. các trường là khoá ngoại chỉ việc chọn text (mặc dù là csdl tại trường FK đó lưu ID của PK mà nó tham chiếu : sử dụng phpmyadmin để kiểm chứng)
chú ý kết hợp ssh để chạy lệnh tác động vào django và sudo nano để edit file.
sử dụng template (file html, sử dụng cú pháp jinja2), lấy context từ 1 view home_page, để tạo trang liệt kê các con nợ đến hạn mà chưa trả tiền!
sử dụng cloudflare tunnel để public kết quả lên 1 sub-domain => chụp kết quả
Hướng dẫn:

Tạo thư mục để chứa image tự buid cho django
Vào thư mục đó tạo file tên: Dockerfile (nội dung hỏi AI xem file này cần có nội dung gì, full comment cho từng dòng lệnh trong file này => mục tiêu kép: để hiểu và để hệ thống chạy được)
AI sẽ nói cần thêm file requirements.txt để cài các thư viện cho python (cài qua lệnh pip) => tạo file requirements.txt với nội dung tưng ứng, trong file này cũng comment được => comment xem thư viện nào dùng để làm gì
Sau mỗi lần sửa đỏi có thể phải chạy lệnh dạng : docker compose exec TÊN_SERVICE_DJANGO_CỦA_BẠN python manage.py migrate để tác động vào django (còn nhiều lệnh khác chứ ko luôn như này), để django thay đổi csdl hoặc thay đổi cấu hình.
TỔ CHỨC CSDL CHO HỆ THỐNG QUẢN LÝ TIỆM CẦM ĐỒ  
Cách làm
PHẦN 1 — CÀI UBUNTU + DOCKER  
Bước 1: Update Ubuntu  
Bước 2: Cài Docker
Bước 3: Kiểm tra Docker
Bước 4: Cài Docker Compose
sudo apt install docker-compose-v2 -y  
Kiểm tra: 
docker compose version 
PHẦN 2 — TẠO THƯ MỤC DỰ ÁN
Bước 5: Tạo thư mục project
PHẦN 3 — TẠO FILE docker-compose.yml
Bước 6: Tạo file
sudo nano docker-compose.yml
Bước 7: Lưu file nano
<img width="1468" height="1015" alt="image" src="https://github.com/user-attachments/assets/b22138d6-d94f-413c-ae68-b028f8a3c13a" />
Bước 8: Tạo thư mục django_app
PHẦN 5 — TẠO Dockerfile
Bước 9: Tạo file Dockerfile
PHẦN 6 — TẠO requirements.txt
Bước 10:
sudo nano requirements.txt
PHẦN 7 — BUILD CONTAINER
Bước 11: Quay lại thư mục gốc
PHẦN 8 — TẠO PROJECT DJANGO
Bước 13: Tạo project
Mở terminal mới.
Vào project: cd pawnshop-system
Chạy:
docker compose run django django-admin startproject pawnshop .
Bước 14: Tạo app core
docker compose run django python manage.py startapp core
PHẦN 9 — CẤU HÌNH DATABASE
Bước 15: Mở settings.py
sudo nano django_app/pawnshop/settings.py
Bước 16: Thêm app core
Bước 17: Sửa DATABASES
PHẦN 10 — TẠO MODEL
Bước 18: Mở models.py
sudo nano django_app/core/models.py
PHẦN 11 — ADMIN
Bước 19: Mở admin.py
sudo nano django_app/core/admin.py
<img width="1462" height="739" alt="image" src="https://github.com/user-attachments/assets/111f8ba0-92d4-431f-a5d6-9dd031fe4caf" />
PHẦN 12 — MIGRATION
Bước 20: Tạo migration
docker compose exec django python manage.py makemigrations
Bước 21: Migrate database
docker compose exec django python manage.py migrate
PHẦN 13 — TẠO ADMIN LOGIN
Bước 22:
docker compose exec django python manage.py createsuperuser
Username: admin
Email:
Password: ...
PHẦN 14 — CHẠY WEB
Bước 23:
<img width="1893" height="909" alt="image" src="https://github.com/user-attachments/assets/727a1350-fe50-42ba-9d25-17f1304b58a8" />
PHẦN 15 — TRUY CẬP WEB
Django admin
Mở trình duyệt:
http://localhost:8000/admin/
<img width="1920" height="1020" alt="Screenshot 2026-05-09 122809" src="https://github.com/user-attachments/assets/eb225351-4d0f-4f4a-a5cd-f658010b7cd9" />
<img width="1892" height="915" alt="image" src="https://github.com/user-attachments/assets/41489676-3244-4d96-baef-62c3f7ec3653" /
<img width="1885" height="922" alt="Screenshot 2026-05-09 223721" src="https://github.com/user-attachments/assets/6a5bf7c0-ae43-4c44-bb7e-aaf1fa2cfc85" />
PHẦN 16 — KIỂM TRA phpMyAdmin
PHẦN 17 — TEMPLATE HTML
Bước 25: Tạo thư mục templates
mkdir -p django_app/core/templates
Bước 26: Tạo views.py

Bước 27: Tạo urls.py cho app

Bước 28: Sửa urls.py project

Bước 29: Tạo home.html
sudo nano django_app/core/templates/home.html

<img width="1885" height="922" alt="image" src="https://github.com/user-attachments/assets/35214535-dcea-4383-a7a2-5fe02d9dc790" />

PHẦN 18 — CLOUDFLARE TUNNEL
Bước 30: Cài cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
<img width="1904" height="834" alt="image" src="https://github.com/user-attachments/assets/ab9c5220-5b86-4414-b739-7cf254226299" />  
<img width="1894" height="918" alt="image" src="https://github.com/user-attachments/assets/a96c9049-c4a3-4d9d-9657-91e80272fcc9" />  
Link public:  
<img width="1894" height="918" alt="Screenshot 2026-05-09 225449" src="https://github.com/user-attachments/assets/75655f5f-d90a-4ec9-8d69-9f329ba0a67a" />  
Bước 31:
sudo dpkg -i cloudflared.deb
Bước 32: Public web  
cloudflared tunnel --url http://localhost:8000  
Link public: https://blackberry-received-chronicles-imported.trycloudflare.com  
<img width="1894" height="918" alt="Screenshot 2026-05-09 225449" src="https://github.com/user-attachments/assets/75655f5f-d90a-4ec9-8d69-9f329ba0a67a" />  
<img width="817" height="421" alt="image" src="https://github.com/user-attachments/assets/57022de5-0428-4675-958d-f1b73cf53ac5" />  
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/0ca3633c-c5b5-4619-a5bd-d972f4267c50" />
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ab2b1ae1-d475-45ac-9b5e-be2115261fe8" />
<img width="1468" height="1015" alt="image" src="https://github.com/user-attachments/assets/b22138d6-d94f-413c-ae68-b028f8a3c13a" />
<img width="1733" height="945" alt="image" src="https://github.com/user-attachments/assets/0fa938be-baec-4510-8d06-92c0cc5fd82b" />
<img width="1878" height="915" alt="image" src="https://github.com/user-attachments/assets/24108542-b2c5-4e3b-960c-912fe6369f6a" />
<img width="1866" height="835" alt="image" src="https://github.com/user-attachments/assets/8ecd60bf-08e3-45f0-8b79-57ef31978d62" />
