# Практическая работа: Сбор логов

## Цель работы

Изучить процесс сбора и централизованного хранения логов с использованием протокола rsyslog и системы Loki. Настроить передачу логов между виртуальными машинами и организовать их визуализацию.

## Задачи

1. Создать две виртуальные машины на базе ОС Debian 12.
2. Настроить сетевой обмен между виртуальными машинами.
3. Включить на одной из ВМ передачу логов по протоколу rsyslog на вторую ВМ.
4. Установить и настроить получение логов на сервер с использованием Loki.
5. Проверить работоспособность системы сбора логов и визуализировать данные.

## Выполнение

### 1. Создание виртуальных машин

- Установлены две виртуальные машины с Debian 12 в среде виртуализации VMware.
- Использованы образы с официального сайта: [Debian 12](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.1.0-amd64-netinst.iso).

**Скриншот 1: Создание ВМ в VMware**

### 2. Настройка сетевого обмена

- В VMware настроен сетевой режим `NAT network`.
- Проверена связь между машинами с помощью `ping`.
![SSSL1_1](https://github.com/user-attachments/assets/4f583874-670c-409d-bd28-38105b0606f2)

**Скриншот 2: Проверка сетевого соединения между ВМ**

### 3. Настройка rsyslog для передачи логов

#### На отправителе (первая ВМ):
1. Устанавливаем rsyslog (если не установлен):
   ```bash
   sudo apt update && sudo apt install -y rsyslog
   ```
   ![SSSL1_2](https://github.com/user-attachments/assets/949cbb6c-0abf-4965-93b7-181a01063097)

2. Редактируем конфигурационный файл `/etc/rsyslog.conf`:
  ![image](https://github.com/user-attachments/assets/343f0326-0d73-40cb-a158-c3d339f7e886)
![image](https://github.com/user-attachments/assets/5d30e526-f6fe-4297-965e-f0e092f6a57e)

3. Перезапускаем службу:
   ```bash
   sudo systemctl restart rsyslog
   ```
![image](https://github.com/user-attachments/assets/afdd5cf4-29a7-4a9b-a4d5-88727b4f6211)

**Скриншот 3: Конфигурация rsyslog на первой ВМ**

#### На сервере (вторая ВМ):
1. Разрешаем приём логов, редактируя `/etc/rsyslog.conf`:
   ```
   $ModLoad imudp
   $UDPServerRun 514
   ```
2. Перезапускаем службу:
   ```bash
   sudo systemctl restart rsyslog
   ```
![image](https://github.com/user-attachments/assets/dde746cf-9f43-4836-a7d1-02c6c310da1e)

**Скриншот 4: Настройка rsyslog на сервере**

### 4. Установка и настройка Loki

1. Скачиваем и устанавливаем Loki:
   ```bash
   curl -O -L "https://github.com/grafana/loki/releases/latest/download/loki-linux-amd64.zip"
   unzip loki-linux-amd64.zip
   chmod +x loki-linux-amd64
   ```
2. Создаём конфигурационный файл `loki-config.yaml`:
   ```yaml
   auth_enabled: false
   server:
     http_listen_port: 3100
   ```
3. Запускаем Loki:
   ```bash
   ./loki-linux-amd64 -config.file=loki-config.yaml &
   ```
![image](https://github.com/user-attachments/assets/6c4ca484-2ce5-41b2-84e6-d0e1a9621519)

**Скриншот 5: Настройка и запуск Loki**

### 5. Проверка системы сбора логов

- Логи из первой ВМ успешно передаются на вторую ВМ.
- Loki корректно сохраняет и обрабатывает логи.
- Данные визуализируются с помощью Grafana или другого инструмента.
![image](https://github.com/user-attachments/assets/33723da8-a007-49cd-9efb-0fa3310a813a)

**Скриншот 6: Проверка передаваемых логов**

## Вывод

В ходе работы была реализована система централизованного сбора логов с использованием rsyslog и Loki. Успешно настроена передача логов между виртуальными машинами в среде VMware, а также их сохранение и обработка. Данный метод позволяет эффективно анализировать события и повышать уровень мониторинга систем.

## Автор

Брестер Андрей Николаевич, группа ББМО-02-23.
