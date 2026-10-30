---
layout: post
title: "WordPress на LAMP"
subtitle: "Установка WordPress на Rocky Linux вручную: Apache, MariaDB и PHP"
tags: [linux, rocky-linux, wordpress, lamp, web]
---

Оригинал: [WordPress on LAMP](https://docs.rockylinux.org/10/guides/cms/wordpress-on-lamp/)
(Rocky Linux Documentation, авторы: Joseph Brinkman, Steven Spencer, Ganna Zhyrnova).

## Требования

- Система Rocky Linux 9.x
- Права sudo

## Введение

WordPress — это система управления контентом (CMS) с открытым исходным кодом, известная своей [знаменитой 5-минутной установкой](https://developer.wordpress.org/advanced-administration/before-install/howto-install/). Обычно она развёртывается на стеке LAMP (Linux, Apache, MySQL, PHP). Хотя для локальной разработки доступны удобные инструменты, такие как [XAMPP](https://www.apachefriends.org/), [Vagrant](https://www.vagrantup.com/) и [wp-env](https://developer.wordpress.org/block-editor/reference-guides/packages/packages-env/), ручная установка WordPress на LAMP даёт ценный практический опыт для тех, кто хочет лучше понимать, как всё устроено.

Этот гайд предполагает, что Rocky Linux 9.x уже установлен, то есть покрывает часть «L» в стеке LAMP.

Здесь показано, как вручную установить WordPress на LAMP-стек в Rocky Linux 9. Это не готовое к продакшену руководство, а отправная точка. Конфигурация LAMP из этого гайда не рекомендуется ни для чего, кроме локальной разработки, без дополнительных мер безопасности.

## Обновление системы

Убедитесь, что пакеты системы обновлены:

```bash
sudo dnf upgrade -y
```

## Установка Apache

Apache — веб-сервер, который будет отдавать ваш сайт WordPress. Установите его командой:

```bash
sudo dnf install httpd -y
```

## Автозапуск Apache

После установки включите Apache, чтобы он запускался автоматически при загрузке системы:

```bash
sudo systemctl enable --now httpd
```

## Установка MariaDB

WordPress хранит динамический контент в базе данных MySQL. MariaDB — это форк MySQL с открытым исходным кодом. Установите её:

```bash
sudo dnf install mariadb-server -y
```

## Включение MariaDB

После установки включите MariaDB, чтобы она запускалась автоматически:

```bash
sudo systemctl enable --now mariadb
```

## Защита MariaDB

Запустите скрипт `mysql_secure_installation`:

```bash
sudo mysql_secure_installation --use-default
```

Скрипт выполняет следующие действия:

1. Устанавливает пароль root, если он ещё не задан
2. Удаляет анонимных пользователей
3. Запрещает удалённый вход под root
4. Удаляет доступ к тестовой базе данных
5. Перезагружает привилегии

## Установка PHP

PHP — язык, с помощью которого WordPress взаимодействует с базой данных MySQL и выполняет динамические действия. Он активно используется в ядре WordPress, темах и плагинах.

Установите PHP и необходимые пакеты для подключения к MySQL:

```bash
sudo dnf install php php-mysqlnd php-gd php-xml php-mbstring
```

После установки PHP перезапустите Apache, чтобы он подхватил PHP как модуль и прочитал свои конфигурационные файлы.

## Перезапуск Apache

```bash
sudo systemctl restart httpd
```

## Загрузка и распаковка WordPress

Скачайте последнюю версию WordPress с помощью `curl`:

```bash
curl -O https://wordpress.org/latest.tar.gz
```

Распакуйте архив с помощью `tar`:

```bash
tar -xzvf latest.tar.gz
```

Скопируйте файлы WordPress в каталог публичных файлов Apache по умолчанию:

```bash
sudo cp -r wordpress/* /var/www/html
```

## Назначение владельца

Назначьте владельцем файлов пользователя и группу Apache:

```bash
sudo chown -R apache:apache /var/www/html/
```

## Настройка прав доступа

Установите права доступа для файлов WordPress:

```bash
sudo chmod -R 755 /var/www/html/
```

## Настройка базы данных

Войдите в консоль MySQL:

```bash
sudo mysql -u root -p
```

## Создание базы данных

Создайте новую базу данных для вашего сайта WordPress:

```sql
CREATE DATABASE LOCALDEVELOPMENTENV;
```

## Создание пользователя и пароля

Создайте пользователя с паролем для базы данных:

> **Примечание:** настоятельно рекомендуется использовать более надёжный пароль.

```sql
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'password';
```

Выдайте этому пользователю все привилегии на базу данных WordPress:

```sql
GRANT ALL PRIVILEGES ON LOCALDEVELOPMENTENV.* TO 'admin'@'localhost';
```

Сбросьте привилегии, чтобы изменения вступили в силу:

```sql
FLUSH PRIVILEGES;
```

Выйдите из консоли MySQL:

```sql
EXIT;
```

## Настройка WordPress

Скопируйте шаблон `wp-config-sample.php` и переименуйте его:

```bash
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
```

Откройте файл `wp-config.php` в любом текстовом редакторе:

```bash
sudo vi /var/www/html/wp-config.php
```

## Указание параметров базы данных

В файле `wp-config.php` необходимо задать следующие константы:

```php
define ('DB_NAME', 'LOCALDEVELOPMENTENV');
define ('DB_USER', 'admin');
define ('DB_PASSWORD', 'password');
```

## Настройка брандмауэра

Откройте службы HTTP и HTTPS в брандмауэре:

```bash
sudo firewall-cmd --add-service=http --add-service=https
```

Перезагрузите `firewalld`, чтобы изменения вступили в силу:

```bash
sudo systemctl reload firewalld
```

## Настройки SELinux

Чтобы Apache имел право читать и записывать файлы WordPress, выполните:

```bash
chcon -R -t httpd_sys_rw_content_t /var/www/html/
```

Чтобы разрешить Apache сетевые соединения, выполните:

> **Примечание:** флаг `-P` делает эту настройку постоянной, то есть она сохраняется после перезагрузки.

```bash
sudo setsebool -P httpd_can_network_connect true
```

## Заключение

Чтобы завершить установку, откройте WordPress по сети, используя имя хоста или приватный IP-адрес сервера. Помните, что эта конфигурация предназначена в первую очередь для локальной разработки. Для продакшена нужно дополнительно настроить доменное имя, установить SSL-сертификат, усилить защиту Apache, тонко настроить SELinux и организовать резервное копирование. Тем не менее, следуя этому гайду, вы получаете хорошую отправную точку для разработки WordPress на стеке LAMP.
