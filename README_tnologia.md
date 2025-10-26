🛠️ Paso a paso de instalación
1. Preparar el servidor
sudo apt update && sudo apt upgrade -y


Configura zona horaria según tu región:

sudo timedatectl set-timezone "America/Santo_Domingo"


Crea el usuario para correr Frappe:

sudo adduser frappeuser
sudo usermod -aG sudo frappeuser
su - frappeuser

2. Instalar dependencias del sistema
sudo apt install git python3-dev python3-venv python3-setuptools python3-pip software-properties-common -y
sudo apt install mariadb-server mariadb-client -y
sudo apt install redis-server -y
sudo apt install xvfb libfontconfig wkhtmltopdf -y


Configura MariaDB (edita /etc/mysql/mariadb.conf.d/50-server.cnf o similar) para usar utf8mb4, por ejemplo:

[mysql]
default-character-set = utf8mb4

[mysqld]
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
innodb-file-format = barracuda
innodb-file-per-table = 1


Reinicia MariaDB:

sudo systemctl restart mariadb

3. Instalar Node.js y Yarn

Instala Node.js (por ejemplo la v18 o 20):

curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs


Instala Yarn:

sudo npm install -g yarn

4. Instalar Frappe Bench
sudo pip3 install frappe-bench


Inicializa un nuevo bench (por ejemplo “frappe-bench”):

bench init frappe-bench --frappe-branch version-15
cd frappe-bench


Asegúrate de que el usuario tenga permisos adecuados:

chmod -R o+rx ~

5. Crear un nuevo sitio
bench new-site site1.yourdomain.com


Durante esto se te pedirá: contraseña de MySQL root (o configuración), contraseña del administrador del sitio.

6. Instalar la aplicación ERPNext
bench get-app erpnext --branch version-15
bench --site site1.yourdomain.com install-app erpnext

7. Ejecutar en modo desarrollo / probar
bench start


Accede al sitio en http://<tu-servidor>:8000 para verificar que todo funciona.

8. (Opcional) Configurar producción

Cuando quieras pasar a producción:

sudo bench setup production frappeuser


Esto configura Nginx, Supervisor, certificados SSL, etc.


* Configurar puerto a sitio
bench set-nginx-port site1.youdomain.com 2401
sudo service nginx reload

* CSS

sudo chmod 755 /home/username

bench build
bench build --force

*Enviar archivos ssh
scp /home/administrador/projects/frappe/frappe-bench-prod/sites/advertech.prod/private/backups/20251025_223341-advertech_prod-database.sql.gz /home/administrador/projects/frappe/frappe-bench-prod/sites/advertech.prod/private/backups/20251025_223341-advertech_prod-files.tar   /home/administrador/projects/frappe/frappe-bench-prod/sites/advertech.prod/private/backups/20251025_223341-advertech_prod-private-files.tar frappe@10.0.0.251:/home/frappe/frappe-bench-prod/backups

*Restore site backup con archivos bench
bench --site riverautoparts.prod --force restore /home/frappe/frappe-bench-prod/backups/20251025_165821-riverautoparts_prod-database.sql.gz --with-private-files /home/frappe/frappe-bench-prod/backups/20251025_165821-riverautoparts_prod-private-files.tar --with-public-files /home/frappe/frappe-bench-prod/backups/20251025_165821-riverautoparts_prod-files.tar

*Backup site bench 
bench --site sitename backup --with-files

*App Quick Kanban activar
https://github.com/tridz-dev/quick_kanban
bench set-config kanban_beta 1


Verificar Configuracion Nginx (modo producción)(VERIFICAS PUERTOS ASIGNADOS)

Si estás en producción:

bench setup nginx
sudo service nginx reload
