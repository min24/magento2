# Cài đặt magento2


https://www.youtube.com/watch?v=6FulByH7-gU&t=432s

1. Config
xampp: 7.4.33 : https://www.apachefriends.org/download.html

- Kích hoạt extension trong file php.ini:
;extension=intl
;extension=soap
;extension=sockets
;extension=sodium
;extension=xsl
memory_limit=4G

- Thêm nội dung cấu hình file hosts trong C:\Windows\System32\drivers\etc\hosts:
127.0.0.1 www.localhost.com
- Tạo cơ sở dữ liệu trong: http://localhost/phpmyadmin/


elasticsearch: 7.6.0
https://www.elastic.co/fr/downloads/past-releases/elasticsearch-7-6-0
(chạy file bin\elasticsearch.bat) LUON PHAI CHAY FILE NAY KHI CAI DAT VA SU DUNG


+ Link tải Composer: https://getcomposer.org/download/
Lệnh tạo project (tải source code magento2):
composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.4.2 magento2

Magento 2.4.2
- Nhấn giữ phím shift và nháy chuột phải, chọn 
Open Powershell window here
- Thực hiện lệnh:
php bin/magento setup:install --base url="http://localhost/magento2/" --db-host="localhost" --db-name="magento2" --db-user="root" --db-password="" --admin-firstname="admin" --admin-lastname="admin" --admin-email="user@example.com" --admin-user="admin" --admin-password="admin123" --language="en_US" --currency="USD" --timezone="America/Chicago" --use-rewrites="1" --backend-frontname="admin" --search-engine=elasticsearch7 --elasticsearch-host="localhost" --elasticsearch-port=9200

- Chỉnh sửa dòng điều kiện If hàm validateURLScheme trong file: vendor\magento\framework\Image\Adapter\Gd2.php:
Thêm điều kiện && !file_exists($filename) vào dòng phía dưới:
if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename))

- Chỉnh sửa dòng 138 trong file vendor\magento\framework\View\Element\Template\File\Validator.php thành: 
$realPath = str_replace('\\', '/',$this->fileDriver->getRealPath($path)); (thay dấu @ thành dấu lớn hơn)

- Chạy lệnh reindex:
php bin/magento indexer:reindex
php bin/magento setup:upgrade
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush

- Copy 2 file index.php và.htaccess từ thư mục /pub/ ra thư mục gốc thư_mục_chứa_project
Chỉnh sửa trong file index.php thành: 
require _DIR_ . '/app/bootstrap.php'; (xoa 1 cap thu muc ../)

- Truy cập vào cơ sở dữ liệu trong phpmyadmin chạy lệnh SQL:
INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/secure/base_static_url', 'http://localhost/magento2/pub/static/', current_timestamp());
INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/unsecure/base_static_url', 'http://localhost/magento2/pub/static/', current_timestamp());
INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/secure/base_media_url', 'http://localhost/magento2/pub/media/', current_timestamp());
INSERT INTO `core_config_data` (`config_id`, `scope`, `scope_id`, `path`, `value`, `updated_at`) VALUES (NULL, 'default', '0', 'web/unsecure/base_media_url', 'http://localhost/magento2/pub/media/', current_timestamp());


- Xoa cache:
php bin/magento cache:flush

- Loi dang nhap trang admin:
The account sign-in was incorrect or your account is disabled temporarily. Please wait and try again later.
php bin/magento module:disable Magento_TwoFactorAuth

- Loi CSS

