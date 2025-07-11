# Анализ поведения клиентов и оценка эффекта воздействия (Criteo Uplift Prediction)

## Обзор проекта

Этот проект посвящен глубокому анализу влияния рекламного воздействия (`treatment`) на поведение клиентов, используя большой датасет Criteo Uplift Prediction (более 13 миллионов записей). Основная цель — не только измерить общий эффект от рекламной кампании, но и заложить основу для выявления сегментов клиентов, которые реагируют на рекламу по-разному, что является ключевым для оптимизации маркетинговых стратегий.

Проект охватывает следующие этапы:
1.  **Загрузка и предобработка данных**: Эффективная работа с крупным датасетом, включая проверку на пропуски и валидацию данных.
2.  **Разведочный анализ данных (EDA)**: Детальное изучение распределений признаков, их корреляций и дифференциального влияния на целевые переменные (`visit` и `conversion`) в зависимости от получения рекламного воздействия.
3.  **Расчет NWOE (Net Weight of Evidence) и NIV (Net Information Value)**: Трансформация числовых признаков в бины и оценка их информативности для оценки эффекта воздействия. Эти метрики помогают понять, насколько признак полезен для разделения клиентов по их реакции на рекламу.
4.  **Оценка среднего эффекта воздействия (ATE)**: Количественная оценка общего (среднего) влияния рекламной кампании на целевые метрики.
5.  **Демонстрация концепции Uplift-моделирования (QINI-кривая)**: Построение QINI-кривой, которая визуализирует способность потенциальной модели выявлять клиентов, демонстрирующих наибольший прирост конверсии или посещений благодаря рекламе.

## Используемые технологии

* **Python 3.x**
* **pandas**: Для эффективной работы с данными.
* **numpy**: Для численных операций.
* **matplotlib** и **seaborn**: Для создания качественных визуализаций.
* **scikit-learn**: Для разбиения данных на обучающую/тестовую выборки и биннинга (`KBinsDiscretizer`).
* **pylift**: Библиотека для uplift-моделирования, используемая для демонстрации QINI-кривой.

## Установка и запуск

Для запуска проекта локально выполните следующие шаги:

1.  **Клонируйте репозиторий:**
    ```bash
    git clone https://github.com/Geewh1z-code/NWOE-and-NIV-Analysis-for-ATE.git
    cd NWOE-and-NIV-Analysis-for-ATE
    ```

2.  **Установите зависимости (рекомендуется использовать виртуальное окружение):**
    ```bash
    # Создайте виртуальное окружение
    python -m venv venv
    # Активируйте виртуальное окружение
    # Для Windows:
    .\venv\Scripts\activate
    # Для macOS/Linux:
    source venv/bin/activate
    # Установите библиотеки
    pip install pandas numpy matplotlib seaborn scikit-learn pylift jupyter
    ```

3.  **Поместите файл данных:**
    Убедитесь, что файл `Criteo data.csv` находится по пути, указанному в `NWOE and NIV Analysis for ATE.ipynb` (по умолчанию `/Users/igor/Downloads/Criteo data.csv`). Если путь другой, измените переменную `DATA_PATH` в первой ячейке ноутбука.

4.  **Запустите Jupyter Notebook:**
    ```bash
    jupyter notebook "NWOE and NIV Analysis for ATE.ipynb"
    ```
    Откройте ноутбук в браузере и запустите все ячейки последовательно.

## Ключевые результаты и выводы

### 1. Состояние данных:
* Обработаны 13,979,592 записи без пропусков и невалидных значений.
* Распределение `treatment` сильно несбалансировано (85% пользователей получили воздействие, 15% — контрольная группа).
* Целевые события (`visit` и `conversion`) являются редкими: ~4.7% для `visit` и ~0.29% для `conversion`.

### 2. Разведочный анализ данных (EDA):
* **Низковариативные признаки**: `f1`, `f4`, `f5`, `f7`, `f10`, `f11` имеют много уникальных значений, но большинство данных сосредоточено в нескольких пиках, что делает их "низковариативными" с точки зрения биннинга на равные квантили. Несмотря на это, для этих признаков наблюдается положительный дифференциальный эффект `treatment` на `visit`.
* **Корреляция**: Обнаружена заметная корреляция между признаками (например, f8/f9, f7/f5, f4/f5), что может быть учтено при построении моделей.

### 3. NWOE и NIV:
* Для большинства признаков, особенно "низковариативных", биннинг привел к созданию одного доминирующего бина. Это привело к схожим высоким значениям NWOE и NIV (~3.16 - ~3.31). Высокие значения NIV показывают, что признаки в целом несут информацию, полезную для предсказания `visit` и `conversion`, особенно в контексте разделения групп.
* NWOE значения для бинов указывают на различия в реакции между бинами для признаков с несколькими бинами (`f0`, `f2`, `f6`, `f8`, `f9`).

### 4. Средний эффект воздействия (ATE):
* **ATE для `visit`**: $0.010145$ (1.01% прироста вероятности посещения).
* **ATE для `conversion`**: $0.001115$ (0.11% прироста вероятности конверсии).
* Кампания оказывает положительный, но относительно небольшой средний эффект на обе метрики по всей популяции.

### 5. Uplift-моделирование (QINI-кривая):
* QINI-кривая находится выше случайной линии, что подтверждает принципиальную возможность построения uplift-модели. Это означает, что в данных есть структура, позволяющая выявлять клиентов, которые с большей вероятностью отреагируют на рекламное воздействие положительно.

## Бизнес-рекомендации

1.  **Переход от ATE к персонализированному таргетингу**: Общий положительный ATE — это хорошо, но настоящий потенциал лежит в определении сегментов клиентов с наибольшим uplift. Необходимо сфокусироваться на выявлении "Persuadables" (тех, кто реагирует только при воздействии), чтобы максимизировать ROI рекламных кампаний.
2.  **Использование NWOE/NIV для выбора признаков**: Признаки с более высоким NIV (например, `f8_binned` и `f9_binned` в этом анализе) являются более информативными для дифференциального эффекта и должны быть приоритетными.
3.  **Разработка стратегий для 4 типов клиентов**:
    * **Persuadables (целевая группа)**: Инвестировать в их таргетинг.
    * **Sure Things**: Оценить, стоит ли на них тратить бюджет или они совершат конверсию и без рекламы.
    * **Lost Causes**: Исключить из таргетинга.
    * **Do Not Disturb**: Исключить из таргетинга, чтобы избежать негативной реакции.
4.  **Постоянный мониторинг и A/B тестирование**: Модели uplift не являются статичными. Регулярные A/B тесты и обновление моделей позволят адаптироваться к изменениям в поведении клиентов и рыночных условиях.

## Контакты

Если у вас есть вопросы или предложения, свяжитесь со мной:

* **Ваше имя**: Игорь Бабенков
* **Email**: permanent176@gmail.com
