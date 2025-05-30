# Генератор заявлений о банкротстве граждан (Прототип)

Этот проект представляет собой прототип системы на базе Python для автоматической генерации заявлений в суд о признании гражданина банкротом на основе предоставленных клиентских данных. Система способна обрабатывать документы в форматах PDF (включая сканы с OCR), Excel и Word, извлекать из них информацию, обогащать данные с помощью AI (DeepSeek API) и формировать итоговый документ заявления в формате `.docx`.

## Цели проекта

*   Автоматизация рутинной задачи подготовки черновиков заявлений о банкротстве.
*   Снижение времени на сбор и ввод данных.
*   Уменьшение вероятности ошибок, связанных с человеческим фактором при переносе данных.
*   Демонстрация применения AI-технологий в юридической практике.

**ВНИМАНИЕ:** Данный прототип генерирует **черновик** заявления. Сгенерированный документ **ТРЕБУЕТ ОБЯЗАТЕЛЬНОЙ ПРОВЕРКИ И КОРРЕКТИРОВКИ** квалифицированным юристом перед подачей в суд.

## Основные возможности

*   **Извлечение данных из различных форматов:**
    *   PDF: извлечение текста из текстового слоя, OCR для сканированных PDF.
    *   Microsoft Excel (.xlsx, .xls): парсинг табличных данных.
    *   Microsoft Word (.docx): извлечение текстового содержимого.
    *   Изображения (.png, .jpg, .jpeg): OCR для извлечения текста.
*   **Базовое извлечение именованных сущностей (NER):** Поиск ФИО, дат, номеров документов с помощью регулярных выражений.
*   **Интеграция с DeepSeek API:**
    *   Обогащение данных клиента (например, генерация описания финансового положения).
    *   Анализ неструктурированных заметок клиента.
*   **Генерация документа:** Создание заявления о банкротстве в формате `.docx` на основе настраиваемого шаблона.
*   **Модульная структура:** Позволяет легко расширять и модифицировать отдельные компоненты.

## Архитектура (упрощенно)

1.  **Модуль приема и предобработки данных:** Загрузка файлов.
2.  **Модуль извлечения данных (Data Extraction):**
    *   OCR и анализ разметки (PDF, изображения).
    *   Парсеры структурированных данных (Excel, Word).
    *   Простой NER на регулярных выражениях.
3.  **Модуль интеллектуального заполнения (Intelligent Filling / Knowledge Mapping):**
    *   Обогащение данных с помощью DeepSeek API.
4.  **Модуль генерации документа (Document Generation):**
    *   Заполнение `.docx` шаблона (с использованием `docxtpl`).

## Требования

*   Python 3.8+
*   Tesseract OCR (с установленными языковыми пакетами, например, для русского `rus.traineddata`)
*   (Опционально, для `pdf2image`) Poppler (утилиты для работы с PDF)
*   API ключ для DeepSeek (должен быть установлен как переменная окружения `DEEPSEEK_API_KEY`)

## Установка

1.  **Клонируйте репозиторий:**
    ```bash
    git clone https://github.com/Mikforce/test_bankr
    cd test_bankr
    ```

2.  **Создайте и активируйте виртуальное окружение:**
    ```bash
    python -m venv venv
    # Windows
    venv\Scripts\activate
    # macOS / Linux
    source venv/bin/activate
    ```

3.  **Установите Tesseract OCR:**
    *   **Windows:** Скачайте установщик с [UB Mannheim Tesseract GitHub](https://github.com/UB-Mannheim/tesseract/wiki) и добавьте путь к `tesseract.exe` в системную переменную PATH.
    *   **Linux (Debian/Ubuntu):** `sudo apt-get update && sudo apt-get install tesseract-ocr tesseract-ocr-rus`
    *   **macOS:** `brew install tesseract tesseract-lang`
    Убедитесь, что установлены языковые пакеты для Tesseract (например, `rus.traineddata` для русского языка).

4.  **(Опционально) Установите Poppler (для `pdf2image`):**
    *   **Windows:** [Инструкция по установке Poppler на Windows](https://stackoverflow.com/questions/18381713/how-to-install-poppler-on-windows) (обычно включает скачивание бинарников и добавление в PATH).
    *   **Linux (Debian/Ubuntu):** `sudo apt-get install poppler-utils`
    *   **macOS:** `brew install poppler`

5.  **Установите Python-зависимости:**
    ```bash
    pip install -r requirements.txt
    ```

6.  **Настройте API ключ DeepSeek:**
    Установите переменную окружения `DEEPSEEK_API_KEY` со вашим ключом.
    *   Linux/macOS (временно для текущей сессии):
        ```bash
        export DEEPSEEK_API_KEY="ваш_ключ_сюда"
        ```
        Для постоянной установки добавьте эту строку в ваш `~/.bashrc`, `~/.zshrc` или аналогичный файл конфигурации оболочки.
    *   Windows (PowerShell, временно для текущей сессии):
        ```powershell
        $env:DEEPSEEK_API_KEY="ваш_ключ_сюда"
        ```
        Для постоянной установки используйте "Переменные среды" в настройках системы.

## Использование

Основной скрипт для запуска - `DeepSeek_API.py`.

1.  **Подготовьте входные данные:**
    *   Создайте файл `client_data.json` с базовой информацией о клиенте (скрипт создаст его с демо-данными при первом запуске, если файл отсутствует). Вы можете отредактировать этот JSON файл.
    *   Поместите документы клиента (PDF, DOCX, XLSX, PNG, JPG) в папку, из которой будет запускаться скрипт, или укажите пути к ним в коде (если модифицируете скрипт для обработки папки с документами).
        *   Для тестирования OCR PDF, переименуйте или создайте файлы:
            *   `sample_text.pdf` (PDF с текстовым слоем)
            *   `sample_scan.pdf` (PDF, являющийся сканом без текстового слоя)
            *   `sample_scan.png` (скан документа в формате изображения)

2.  **Запустите скрипт:**
    ```bash
    python bankruptcy_generator.py
    ```

3.  **Результаты:**
    *   Скрипт обработает файлы, попытается извлечь данные и, если настроен DeepSeek API, обогатит их.
    *   Будет создан (или использован существующий) файл шаблона `bankruptcy_template.docx`.
    *   Итоговое заявление будет сохранено в файл `generated_bankruptcy_statement.docx`.
    *   Логи процесса будут выведены в консоль.

## Структура проекта (примерная)
├── DeepSeek_API.py # Основной исполняемый скрипт
├── client_data.json # Входные данные клиента (могут быть созданы/изменены)
├── bankruptcy_template.docx # Шаблон заявления (может быть создан скриптом)
├── generated_bankruptcy_statement.docx # Результат работы скрипта
├── requirements.txt # Список зависимостей Python
├── README.md # Этот файл
└── (папка с документами клиента для обработки) # Например, sample_text.pdf, sample_scan.png и т.д.