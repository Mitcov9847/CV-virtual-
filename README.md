# Лабораторная работа 1: Установка и настройка Debian, LAMP, PhpMyAdmin и Drupal

**Фамилия, имя студента:** Митков Евгений  
**Группа:** I2302  

## Описание задачи
В данной лабораторной работе необходимо:
- Установить операционную систему Debian на виртуальную машину
- Настроить LAMP-стек (Apache, MySQL, PHP)
- Установить PhpMyAdmin и CMS Drupal
- Настроить виртуальные хосты для обоих сайтов

## Описание выполнения работы

### 1. Создание папок и файлов
Создаем папку `lab01`, затем создаем внутри неё папку `dvd` и файл `readme.md`. В папку `dvd` помещаем ISO-образ дистрибутива Debian.

![image](https://github.com/user-attachments/assets/a5070a42-97b0-447e-b748-1ba79c283092)

### 2. Создание образа диска для виртуальной машины
В директории `lab01` создаем диск размером 8 ГБ в формате qcow2:

qemu-img create -f qcow2 debian.qcow2 8G


### 3. Запуск установки ОС Debian и установка LAMP (PHP)
Запускаем установку Debian на виртуальную машину:

qemu-system-x86_64 -hda debian.qcow2 -cdrom dvd/debian.iso -boot d -m 2G
![image](https://github.com/user-attachments/assets/cd224f83-2207-407d-8c6c-f4c8e15ceb9a)
![image](https://github.com/user-attachments/assets/317fe73c-ecc2-4f63-b030-a15c254fc0b3)
> **Примечание:** На этапе установки рекомендуется выбирать минимальную установку системы, чтобы снизить нагрузку на ресурсы виртуальной машины и избежать установки ненужных пакетов.

### 4. Скачивание и установка PhpMyAdmin и Drupal
Скачиваем архивы PhpMyAdmin и Drupal с официальных сайтов, затем распаковываем их:

unzip phpMyAdmin-5.2.2-all-languages.zip
mv phpMyAdmin-5.2.2-all-languages /var/www/phpmyadmin

unzip drupal-10.0.5.zip
mv drupal-10.0.5 /var/www/drupal

![image](https://github.com/user-attachments/assets/cb3914f6-0e73-4bf8-b752-b272ef24b78d)

### 5. Создание базы данных и пользователя для Drupal
В MySQL создаем базу данных и пользователя:

CREATE DATABASE drupal_db;
CREATE USER 'drupal_user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON drupal_db.* TO 'drupal_user'@'localhost';
FLUSH PRIVILEGES;

![image](https://github.com/user-attachments/assets/01a881ab-6e4a-425e-a7ca-950223af073f)

### 6. Настройка виртуальных хостов для PhpMyAdmin и Drupal
Создаем конфигурационные файлы для Apache.

#### Конфигурация для PhpMyAdmin:
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot "/var/www/phpmyadmin"
    ServerName phpmyadmin.localhost
    ErrorLog "/var/log/apache2/phpmyadmin.localhost-error.log"
    CustomLog "/var/log/apache2/phpmyadmin.localhost-access.log" common
</VirtualHost>
```

#### Конфигурация для Drupal:
```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot "/var/www/drupal"
    ServerName drupal.localhost
    ErrorLog "/var/log/apache2/drupal.localhost-error.log"
    CustomLog "/var/log/apache2/drupal.localhost-access.log" common
</VirtualHost>
```

### 7. Проверка доступности сайтов
После настройки Apache проверяем доступность сайтов через браузер:
- [PhpMyAdmin](http://phpmyadmin.localhost)
- [Drupal](http://drupal.localhost)
  
![image](https://github.com/user-attachments/assets/d221b0c4-327b-4185-808f-7154d64c382e)

## Вывод по работе
В ходе лабораторной работы были изучены ключевые процессы установки и настройки серверной инфраструктуры:
- Установка Debian и настройка LAMP
- Настройка PhpMyAdmin для управления базами данных
- Установка и конфигурирование Drupal
- Практическое взаимодействие с виртуальными машинами
- Работа с файловыми разрешениями и пользователями MySQL

> **Вывод:** Полученные знания и навыки позволят эффективно развертывать и администрировать веб-приложения в серверной среде, что является важным аспектом работы системного администратора и DevOps-инженера.

## Ответы на вопросы
1. **Как скачать файл в консоли при помощи утилиты wget?**  
   wget <URL файла>

2. **Зачем необходимо создавать для каждого сайта свою базу и своего пользователя?**  
   - Это обеспечивает безопасность, разделение данных и изоляцию доступа.
3. **Как поменять доступ к системе управления БД на порт 1234?**  
   - Редактируем конфигурационный файл MySQL:

   nano /etc/mysql/my.cnf

   В разделе `[mysqld]` меняем порт:
   port = 1234

4. **Какие преимущества даёт виртуализация?**  
   - Использование одной физической машины для нескольких виртуальных серверов
   - Улучшение ресурсоиспользования и упрощение управления
5. **Для чего необходимо устанавливать время/дату на сервере?**  
   - Для синхронизации операций, корректной работы логов и приложений.
6. **Сколько места занимает установленная ОС (виртуальный диск) на хостовой машине?**
   ![image](https://github.com/user-attachments/assets/cc0a5d88-3e6c-4df1-a964-6934b58c5d46)

   - Проверить можно командой:
   du -sh debian.qcow2
   
8. **Какие есть рекомендации по разбиению диска для серверов?**  
   Разбиение диска улучшает безопасность, производительность и управление:
   - `/root` – для системных файлов
   - `/home` – для пользовательских данных
   - `/var` – для логов и баз данных
   - `/tmp` – для временных файлов
   - `/swap` – для расширения памяти
   - `/boot` – для загрузочных файлов

Причины:
- **Безопасность:** изоляция данных, предотвращение переполнения.
- **Производительность:** оптимизация для разных типов данных.
- **Управление:** упрощение резервного копирования и расширения разделов.
