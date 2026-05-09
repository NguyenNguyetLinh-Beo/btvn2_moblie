# btvn2_moblie
Sinh Viên
Họ tên: Nguyễn Nguyệt Linh
MSV: K225480106039
Lớp: 58KTPM
Môn: TEE0421 - Phát triển ứng dụng với mã nguồn mở  
Tính năng chính của hệ thống quản lý tiệm cầm đồ   
Quản lý khách hàng (thêm, sửa, xoá thông tin khách hàng).  
Quản lý tài sản cầm cố của khách hàng.  
Quản lý phiếu cầm đồ:  
số tiền vay  
lãi suất  
ngày cầm  
ngày đến hạn  
trạng thái đã trả/chưa trả.  
Sử dụng khóa ngoại (Foreign Key) để liên kết dữ liệu giữa các bảng.  
Quản trị dữ liệu bằng Django Admin (CRUD dữ liệu trực tiếp trên web).  
Lưu trữ dữ liệu bằng MariaDB.  
Kiểm tra dữ liệu bằng phpMyAdmin.  
Hiển thị danh sách con nợ quá hạn bằng template HTML + view Django.  
Docker hóa hệ thống gồm:  
Django  
MariaDB  
phpMyAdmin. 
Public website bằng Cloudflare Tunnel.  
Sử dụng SSH và nano để thao tác, chỉnh sửa file trên Ubuntu. 
Kiến trúc hệ thống  

Hệ thống được xây dựng theo mô hình Client – Server kết hợp Docker Container.  

Các thành phần chính:  
Người dùng truy cập hệ thống bằng trình duyệt web.  
Django đóng vai trò Backend xử lý nghiệp vụ và giao diện quản trị.  
MariaDB dùng để lưu trữ cơ sở dữ liệu.  
phpMyAdmin dùng để kiểm tra và quản lý dữ liệu trực quan.  
Docker Compose dùng để quản lý và chạy các container.  
Cloudflare Tunnel dùng để public hệ thống ra internet.  
Luồng hoạt động:  
Người dùng  
    ↓ 
Trình duyệt Web  
    ↓ 
Django Server   
    ↓  
MariaDB Database    
PHẦN 1 — CÀI UBUNTU + DOCKER  
Bước 1: Update Ubuntu  

Mở terminal:

sudo apt update
sudo apt upgrade -y
Bước 2: Cài Docker
sudo apt install docker.io -y
Bước 3: Kiểm tra Docker
docker --version

Nếu hiện kiểu:

Docker version 26.x.x

là OK.

Bước 4: Cài Docker Compose
sudo apt install docker-compose-v2 -y

Kiểm tra:

docker compose version
PHẦN 2 — TẠO THƯ MỤC DỰ ÁN
Bước 5: Tạo thư mục project
mkdir pawnshop-system

Vào thư mục:

cd pawnshop-system
PHẦN 3 — TẠO FILE docker-compose.yml
Bước 6: Tạo file
sudo nano docker-compose.yml

Dán:

version: '3.9'

services:

  mariadb:
    image: mariadb:11

    container_name: mariadb_pawnshop

    restart: always

    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: pawnshop_db
      MYSQL_USER: pawnuser
      MYSQL_PASSWORD: pawn123

    ports:
      - "3307:3306"

    volumes:
      - mariadb_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin

    container_name: phpmyadmin_pawnshop

    restart: always

    ports:
      - "8080:80"

    environment:
      PMA_HOST: mariadb
      MYSQL_ROOT_PASSWORD: root123

    depends_on:
      - mariadb

  django:
    build: ./django_app

    container_name: django_pawnshop

    restart: always

    ports:
      - "8000:8000"

    volumes:
      - ./django_app:/app

    depends_on:
      - mariadb

    command: python manage.py runserver 0.0.0.0:8000

volumes:
  mariadb_data:
Bước 7: Lưu file nano

Nhấn:

CTRL + O
ENTER
CTRL + X
PHẦN 4 — TẠO THƯ MỤC DJANGO
Bước 8: Tạo thư mục django_app
mkdir django_app

Vào:

cd django_app
PHẦN 5 — TẠO Dockerfile
Bước 9: Tạo file Dockerfile
sudo nano Dockerfile

Dán:

# Dùng image python chính thức
FROM python:3.11

# Không tạo file pyc
ENV PYTHONDONTWRITEBYTECODE=1

# Hiển thị log realtime
ENV PYTHONUNBUFFERED=1

# Tạo thư mục làm việc
WORKDIR /app

# Copy requirements
COPY requirements.txt /app/

# Update pip
RUN pip install --upgrade pip

# Cài thư viện
RUN pip install -r requirements.txt

# Copy source code
COPY . /app/

# Mở port
EXPOSE 8000

Lưu lại.

PHẦN 6 — TẠO requirements.txt
Bước 10:
sudo nano requirements.txt

Dán:

Django==5.2

mysqlclient==2.2.4

Lưu lại.

PHẦN 7 — BUILD CONTAINER
Bước 11: Quay lại thư mục gốc
cd ..

Kiểm tra:

ls

Phải có:

django_app
docker-compose.yml
Bước 12: Build Docker
docker compose up --build

Đợi tải image.

Nếu hiện:

django_pawnshop
mariadb_pawnshop
phpmyadmin_pawnshop

là OK.

PHẦN 8 — TẠO PROJECT DJANGO
Bước 13: Tạo project

Mở terminal mới.

Vào project:

cd pawnshop-system

Chạy:

docker compose run django django-admin startproject pawnshop .
Bước 14: Tạo app core
docker compose run django python manage.py startapp core
PHẦN 9 — CẤU HÌNH DATABASE
Bước 15: Mở settings.py
sudo nano django_app/pawnshop/settings.py
Bước 16: Thêm app core

Tìm:

INSTALLED_APPS = [

Thêm:

'core',

Ví dụ:

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'core',
]
Bước 17: Sửa DATABASES

Tìm:

DATABASES = {

Sửa thành:

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'pawnshop_db',
        'USER': 'pawnuser',
        'PASSWORD': 'pawn123',
        'HOST': 'mariadb',
        'PORT': '3306',
    }
}

Lưu lại.

PHẦN 10 — TẠO MODEL
Bước 18: Mở models.py
sudo nano django_app/core/models.py

Dán:

from django.db import models


class Customer(models.Model):

    full_name = models.CharField(max_length=200)

    phone = models.CharField(max_length=20)

    address = models.TextField()

    def __str__(self):
        return self.full_name


class Asset(models.Model):

    customer = models.ForeignKey(
        Customer,
        on_delete=models.CASCADE
    )

    asset_name = models.CharField(max_length=200)

    estimated_value = models.IntegerField()

    def __str__(self):
        return self.asset_name


class PawnTicket(models.Model):

    customer = models.ForeignKey(
        Customer,
        on_delete=models.CASCADE
    )

    asset = models.ForeignKey(
        Asset,
        on_delete=models.CASCADE
    )

    loan_amount = models.IntegerField()

    interest_rate = models.FloatField()

    start_date = models.DateField()

    due_date = models.DateField()

    is_paid = models.BooleanField(default=False)

    def __str__(self):
        return f"Ticket #{self.id}"

Lưu.

PHẦN 11 — ADMIN
Bước 19: Mở admin.py
sudo nano django_app/core/admin.py

Dán:

from django.contrib import admin

from .models import Customer
from .models import Asset
from .models import PawnTicket


admin.site.register(Customer)

admin.site.register(Asset)

admin.site.register(PawnTicket)
PHẦN 12 — MIGRATION
Bước 20: Tạo migration
docker compose exec django python manage.py makemigrations
Bước 21: Migrate database
docker compose exec django python manage.py migrate

Nếu thấy:

Applying ...
OK

là thành công.

PHẦN 13 — TẠO ADMIN LOGIN
Bước 22:
docker compose exec django python manage.py createsuperuser

Ví dụ:

Username: admin
Email: admin@gmail.com
Password: 123456Aa@
PHẦN 14 — CHẠY WEB
Bước 23:
docker compose up
PHẦN 15 — TRUY CẬP WEB
Django admin

Mở trình duyệt:

http://IP:8000/admin

Ví dụ localhost:

http://localhost:8000/admin

Login tài khoản admin.

PHẦN 16 — KIỂM TRA phpMyAdmin
Bước 24:

Mở:

http://localhost:8080

Login:

user: root
pass: root123

Kiểm tra:

pawnshop_db

Sẽ thấy bảng:

core_customer
core_asset
core_pawnticket
PHẦN 17 — TEMPLATE HTML
Bước 25: Tạo thư mục templates
mkdir -p django_app/core/templates
Bước 26: Tạo views.py
sudo nano django_app/core/views.py

Dán:

from django.shortcuts import render

from .models import PawnTicket

from datetime import date


def home_page(request):

    debtors = PawnTicket.objects.filter(
        due_date__lte=date.today(),
        is_paid=False
    )

    return render(
        request,
        'home.html',
        {
            'debtors': debtors
        }
    )
Bước 27: Tạo urls.py cho app
sudo nano django_app/core/urls.py

Dán:

from django.urls import path

from .views import home_page

urlpatterns = [
    path('', home_page),
]
Bước 28: Sửa urls.py project
sudo nano django_app/pawnshop/urls.py

Sửa:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [

    path('admin/', admin.site.urls),

    path('', include('core.urls')),
]
Bước 29: Tạo home.html
sudo nano django_app/core/templates/home.html

Dán:

<!DOCTYPE html>
<html>

<head>
    <title>Danh sách con nợ</title>
</head>

<body>

    <h1>DANH SÁCH CON NỢ QUÁ HẠN</h1>

    <table border="1">

        <tr>
            <th>Khách hàng</th>
            <th>Tài sản</th>
            <th>Số tiền vay</th>
            <th>Ngày đến hạn</th>
        </tr>

        {% for item in debtors %}

        <tr>
            <td>{{ item.customer.full_name }}</td>

            <td>{{ item.asset.asset_name }}</td>

            <td>{{ item.loan_amount }}</td>

            <td>{{ item.due_date }}</td>
        </tr>

        {% endfor %}

    </table>

</body>

</html>
PHẦN 18 — CLOUDFLARE TUNNEL
Bước 30: Cài cloudflared
curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb -o cloudflared.deb
Bước 31:
sudo dpkg -i cloudflared.deb
Bước 32: Public web
cloudflared tunnel --url http://localhost:8000

Nó sẽ hiện:

https://xxxxx.trycloudflare.com

Mở link là được.

PHẦN 19 — SSH + NANO
SSH vào server
ssh username@ip_server
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/0ca3633c-c5b5-4619-a5bd-d972f4267c50" />

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ab2b1ae1-d475-45ac-9b5e-be2115261fe8" />
<img width="1468" height="1015" alt="image" src="https://github.com/user-attachments/assets/b22138d6-d94f-413c-ae68-b028f8a3c13a" />

Ví dụ:

ssh ubuntu@192.168.1.10
Edit file bằng nano
sudo nano ten_file.py
