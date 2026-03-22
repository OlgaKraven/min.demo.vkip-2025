# Модуль 2. Пример решения

!!! info "Стек"
    Пример выполнен на **MySQL 8.x** + **DBeaver** (GUI и внешний клиент). Операционная система — Windows 10/11.

 
## 1. Развёртывание среды и установка MySQL

### 2.1. Установка MySQL через XAMPP

Вариант А — **XAMPP** (быстро, всё в одном):

1. Скачайте XAMPP с [apachefriends.org](https://www.apachefriends.org).
2. Установите, выберите компоненты: **Apache**, **MySQL**, **phpMyAdmin**.
3. Запустите XAMPP Control Panel → нажмите **Start** рядом с **MySQL**.

Вариант Б — **MySQL Installer** (официальный):

1. Скачайте с [dev.mysql.com/downloads/installer](https://dev.mysql.com/downloads/installer).
2. Тип установки — **Server only** или **Developer Default**.
3. При настройке:
    - **Config Type:** Development Computer
    - **Port:** 3306
    - **Server Name:** `lab_sql_XY` (XY — номер вашего места, например `lab_sql_01`)
    - Установите пароль root.
4. Запустите установку.

### 2.2. Установка DBeaver

1. Скачайте с [dbeaver.io/download](https://dbeaver.io/download/) — Community Edition.
2. Установите, запустите.
3. `Database → New Database Connection` → выберите **MySQL**.
4. Заполните:
    - Host: `127.0.0.1`
    - Port: `3306`
    - Username: `root`
    - Password: ваш пароль root
5. При первом подключении DBeaver предложит скачать драйвер MySQL — нажмите **Download**.

### 2.3. Параметры подключения (зафиксировать в отчёт)

| Параметр | Значение |
|----------|---------|
| СУБД | MySQL 8.x |
| Порт | 3306 |
| Хост | 127.0.0.1 |
| Директория БД | `C:\ProgramData\MySQL\MySQL Server 8.0\Data\` |
| Служба ОС | `MySQL80` |
| Имя сервера | `lab_sql_01` (замените на свой номер) |

---

## 3. Создание БД, пользователей и таблицы

Выполняйте SQL-запросы в DBeaver (SQL Editor → New SQL Script) или в командной строке MySQL.

### 3.1. Создание базы данных

```sql
CREATE DATABASE corpdata_01
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_general_ci;

USE corpdata_01;
```

> Замените `01` на номер своего места.

### 3.2. Создание таблицы clients

```sql
CREATE TABLE clients (
    id       INT          NOT NULL AUTO_INCREMENT,
    name     VARCHAR(100) NOT NULL,
    email    VARCHAR(100),
    reg_date DATE,
    PRIMARY KEY (id)
);
```

### 3.3. Создание пользователей

Пароли должны содержать буквы, цифры и спецсимволы (минимум 8 символов).

```sql
CREATE USER 'client01'@'localhost' IDENTIFIED BY 'Cl!ent01Pass';
CREATE USER 'client02'@'localhost' IDENTIFIED BY 'Cl!ent02Pass';
CREATE USER 'client03'@'localhost' IDENTIFIED BY 'Cl!ent03Pass';
CREATE USER 'client04'@'localhost' IDENTIFIED BY 'Cl!ent04Pass';
CREATE USER 'client05'@'localhost' IDENTIFIED BY 'Cl!ent05Pass';
```

### 3.4. Назначение прав доступа

```sql
-- client01, client02: SELECT, INSERT
GRANT SELECT, INSERT ON corpdata_01.clients TO 'client01'@'localhost';
GRANT SELECT, INSERT ON corpdata_01.clients TO 'client02'@'localhost';

-- client03: только SELECT
GRANT SELECT ON corpdata_01.clients TO 'client03'@'localhost';

-- client04: SELECT, UPDATE
GRANT SELECT, UPDATE ON corpdata_01.clients TO 'client04'@'localhost';

-- client05: SELECT, DELETE
GRANT SELECT, DELETE ON corpdata_01.clients TO 'client05'@'localhost';

FLUSH PRIVILEGES;
```

### 3.5. Проверка прав (для отчёта)

```sql
SHOW GRANTS FOR 'client01'@'localhost';
SHOW GRANTS FOR 'client03'@'localhost';
```

---

## 4. Диагностика и проверка через DBeaver

### 4.1. Установка DBeaver

1. Скачайте с [dbeaver.io](https://dbeaver.io/download/) — Community Edition.
2. Установите, запустите.

### 4.2. Подключение от имени client03

1. `Database → New Database Connection` → выберите **MySQL**.
2. Заполните:
    - Host: `127.0.0.1`
    - Port: `3306`
    - Database: `corpdata_01`
    - Username: `client03`
    - Password: `Cl!ent03Pass`
3. Нажмите **Test Connection** → **Finish**.

### 4.3. Проверка прав

В SQL-редакторе DBeaver выполните от имени client03:

```sql
-- Это должно работать (SELECT разрешён)
SELECT * FROM clients;

-- Это должно вернуть ошибку (INSERT запрещён)
INSERT INTO clients (name, email, reg_date) VALUES ('Тест', 'test@test.ru', '2026-01-01');
```

Скриншот ошибки — приложить к отчёту.

### 4.4. Добавление тестовых данных (через root)

Перед проверкой добавьте несколько строк через DBeaver от имени root:

```sql
INSERT INTO clients (name, email, reg_date) VALUES
    ('Иванов Иван',  'ivanov@corp.ru',  '2025-01-15'),
    ('Петрова Анна', 'petrova@corp.ru', '2025-03-22'),
    ('Сидоров Олег', 'sidorov@corp.ru', '2025-07-08');
```

---

## 5. Создание дампа БД

### Через командную строку (mysqldump)

```cmd
mysqldump -u root -p corpdata_01 > C:\corpdata_01_dump.sql
```

Введите пароль root при запросе.

### Через DBeaver

1. ПКМ на базе `corpdata_01` → **Tools → Dump database**.
2. Выберите таблицы → укажите путь к файлу `corpdata_01_dump.sql`.
3. Нажмите **Start**.

### Фрагмент дампа (для отчёта)

```sql
-- MySQL dump 8.0
-- Host: 127.0.0.1    Database: corpdata_01

CREATE DATABASE IF NOT EXISTS `corpdata_01`;
USE `corpdata_01`;

CREATE TABLE `clients` (
  `id`       int NOT NULL AUTO_INCREMENT,
  `name`     varchar(100) NOT NULL,
  `email`    varchar(100) DEFAULT NULL,
  `reg_date` date DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO `clients` VALUES
(1,'Иванов Иван','ivanov@corp.ru','2025-01-15'),
(2,'Петрова Анна','petrova@corp.ru','2025-03-22'),
(3,'Сидоров Олег','sidorov@corp.ru','2025-07-08');
```

---

## 6. Системное администрирование (Windows)

### 6.1. Создание системного пользователя dbadmin_XY

Откройте **PowerShell от имени администратора**:

```powershell
# Создать пользователя (замените XY на номер места)
net user dbadmin_01 "Adm!n01Pass" /add /fullname:"DB Administrator 01"

# Создать рабочую папку
mkdir C:\DBAdmin_01
```

### 6.2. Настройка переменной окружения DB_HOME

```powershell
# Системная переменная DB_HOME
[System.Environment]::SetEnvironmentVariable(
    "DB_HOME",
    "C:\Program Files\MySQL\MySQL Server 8.0",
    "Machine"
)
```

Или через GUI: `Параметры → Система → Доп. параметры → Переменные среды → Системные → Создать`.

### 6.3. Создание каталога sql_reports_XY и настройка прав

```powershell
# Создать каталог на рабочем столе (от имени текущего пользователя)
mkdir "$env:USERPROFILE\Desktop\sql_reports_01"
```

Настройка прав через **icacls**:

```cmd
REM Дать Администраторам полный доступ
icacls "%USERPROFILE%\Desktop\sql_reports_01" /grant "Администраторы:(OI)(CI)F"

REM Дать client03 только просмотр содержимого
icacls "%USERPROFILE%\Desktop\sql_reports_01" /grant "client03:(OI)(CI)R"
```

Проверка через GUI: ПКМ на папке → «Свойства» → «Безопасность» — сделать скриншот.

### 6.4. Точка восстановления ОС

```powershell
# Создать точку восстановления
Checkpoint-Computer -Description "Перед демоэкзаменом" -RestorePointType "MODIFY_SETTINGS"
```

Или через GUI: `Панель управления → Система → Защита системы → Создать`.

---

## 7. Отчёт — структура документа

Файл сохраняется как `отчёт_модуль2.docx`.

```
1. СУБД и параметры подключения
   - MySQL 8.x, порт 3306, хост 127.0.0.1
   - Служба ОС: MySQL80
   - Директория: C:\ProgramData\MySQL\MySQL Server 8.0\Data\

2. Созданные объекты
   - БД: corpdata_01
   - Таблица: clients (id, name, email, reg_date)
   - Пользователи: client01–client05
   - Права: согласно заданию

3. Скриншоты
   [скриншот 1 — установка MySQL]
   [скриншот 2 — создание БД в DBeaver]
   [скриншот 3 — создание таблицы]
   [скриншот 4 — назначение прав]
   [скриншот 5 — подключение через DBeaver]
   [скриншот 6 — ошибка при попытке INSERT от client03]

4. Дамп БД (первые 10 строк)
   [вставить фрагмент из раздела 5]

5. Настройка ОС
   - Создан пользователь dbadmin_01
   - Переменная окружения DB_HOME установлена
   - Каталог sql_reports_01 создан, права настроены

6. Проверка совместимости
   - Порт 3306: свободен (проверено netstat -ano | findstr 3306)
   - Конфликтов служб не обнаружено

7. Проблемы и предложения
   [описать всё, что пошло не так в процессе]
```
 