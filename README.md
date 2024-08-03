# Passport Image Generator

## Описание
Проект предназначен для генерации синтетических изображений паспортов с использованием Python и библиотеки Pillow. Скрипт позволяет создавать изображения паспортов, автоматически заполняя их текстом и изображениями, с возможностью использования пользовательских фонов.

## Установка зависимостей
Для работы скрипта требуется Python 3.x и библиотеки Pillow и tqdm. Установить необходимые зависимости можно командой:
```bash
pip install Pillow tqdm
```

## Структура файлов
Все ресурсы должны быть размещены в одной директории (source_root). Ниже приведена структура необходимых файлов и папок, а также навзвания, которые заданы по умолчанию:

+ all_fms_units.csv: CSV-файл с кодами и названиями подразделений. Колонка с кодами называется "code", c подразделениями - "name".
+ background/: Папка с фонами для паспортов.
+ faces/: Папка с изображениями лиц.
+ fonts/: Папка со шрифтами.
+ machine_recording/: Папка с примерами машинной записи.
+ passports_pattern/: Папка с шаблонами паспортов.
+ place_of_birth.csv: CSV-файл с местами рождения. Имеется две колонки с названиями 'city' и 'village', откуда случайно берется значение.
+ russian_fio.csv: CSV-файл с примерами российских ФИО. Названия колонок: "name", "surname" и "patronymic"
+ signatures/: Папка с подписями.

## Использование
Для генерации изображений необходимо использовать следующую команду, указав нужные параметры:

```bash
python passport_generator.py --source_root [путь к папке с ресурсами] --output_root [путь к папке для сохранения] --count [количество генераций] --use_background [True/False]
```
Параметры команды:
+ --source_root: Путь к папке с исходными ресурсами.
+ --output_root: Путь для сохранения результатов.
+ --count: Количество генерируемых изображений (по умолчанию 10).
+ --use_background: Использовать ли фон (по умолчанию False).
