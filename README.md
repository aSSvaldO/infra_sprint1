Kittygram
Если вы любите делиться котиками, то вы на правильном пути. Данный проект позволяет авторизованным пользователям публиковать фото с именами и данными вашего любимого питомца.
Установка и настройка проекта
(Данное описание предполагает, что на вашем сервере установлен Git, пакетный менеджер npm, создана ветка на репозиторий infra_sprint1)
Подготовка сервера к установке проекта
1. Склонируйте проект из репозитория
2. Настройте виртуальное окружение
3. Установите пакеты из файла requirements.txt
4. Выполните миграции
5. Создайте суперпользователя
git clone https://github.com/<your_profile>/infra_sprint1
python3 -m venv venv
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser 
Создайте файл .env в корневой директории проекта и укажите в нем переменные:
- SECRET_KEY=YOUR_SECRET_KEY
- DEBUG=False
- ALLOWED_HOSTS=<YOUR_SERVER_IP_ADDRESS> localhost <YOUR_DOMAIN_NAME>
Запуск Фронта
1. Установите зависимости фронтенд-приложения из директории /frontend/
2. Запустите приложение
npm i
npm run start
Запуск бэкенда. Установка и запуск Gunicorn
1. На удаленном сервере при активированном виртуальном окружении проекта установите пакет Gunicorn
2. Cоздайте линк из файла конфига в системную директорию
pip install gunicorn==20.1.0
ln -s /home/yc-user/infra_sprint1/infra/gunicorn_kittygram.service /etc/systemd/system/gunicorn_kittygram.service
3. Запустите процесс gunicorn_kittygram.service
4. Добавьте процесс в автозапуск
Команды sudo systemctl start gunicorn_kittygram.service и sudo systemctl enable gunicorn_kittygram.service используются для запуска и включения службы gunicorn_kittygram на сервере.
Настройка веб-сервера. Установка и запуск Nginx
- В виртуальной среде проекта на удаленном сервере установите Nginx и затем запустите его. После установки возможно потребуется перезагрузить операционную систему – выполните это. Затем запустите Nginx.
sudo apt install nginx -y
sudo systemctl start  sudo nano /etc/nginx/sites-enabled/default
- Включите файрвол и укажите порты, которые необходимо открыть (Команда sudo ufw allow 'Nginx Full' активирует разрешение принимать запросы на порты 80 и 443).
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable
Настройка Nginx
- Запустите сборку фронтенд-приложения из директории ./frontend. Затем скопируйте содержимое директории /home/yc-user/infra_sprint1/frontend/build/ в директорию /var/www/kittygram/.
npm run build
sudo cp -r build/. /var/www/kittygram/
- Создайте симлинк из файла конфигурации в системную директорию. Отредактируйте файл конфигурации /home/yc-user/infra_sprint1/infra/default в соответствии с вашим IP-адресом и доменом.
ln -s /home/yc-user/infra_sprint1/infra/default /etc/nginx/sites-enabled/default
- Перезагрузите конфигурацию Nginx.
sudo systemctl reload nginx
- В виртуальной среде проекта перейдите в директорию с файлом manage.py и выполните команды:
python manage.py collectstatic
sudo cp -r /home/yc-user/infra_sprint1/backend/static_backend/ /var/www/kittygram/
Безопасность. Получение и настройка SSL-сертификата
- Установите certbot на вашем удаленном сервере. Затем запустите certbot и получите SSL-сертификат для вашего веб-сервера Nginx.
sudo apt install snapd
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
- Перезагрузите конфигурацию Nginx.

