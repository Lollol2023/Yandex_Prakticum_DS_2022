# Модель для прогнозирования оттока клиентов банка


## Описание проекта

Из «Бета-Банка» стали уходить клиенты. Каждый месяц. Немного, но заметно. Банковские маркетологи посчитали: сохранять текущих клиентов дешевле, чем привлекать новых.

Нужно спрогнозировать, уйдёт клиент из банка в ближайшее время или нет. Вам предоставлены исторические данные о поведении клиентов и расторжении договоров с банком.

Постройте модель с предельно большим значением F1-меры. Чтобы сдать проект успешно, нужно довести метрику до 0.59. Проверьте F1-меру на тестовой выборке самостоятельно.

Дополнительно измеряйте AUC-ROC, сравнивайте её значение с F1-мерой.

## Инструкция по выполнению проекта

- Загрузите и подготовьте данные. Поясните порядок действий.
- Исследуйте баланс классов, обучите модель без учёта дисбаланса. Кратко опишите выводы.
- Улучшите качество модели, учитывая дисбаланс классов. Обучите разные модели и найдите лучшую. Кратко опишите выводы.
- Проведите финальное тестирование.

## Описание данных

Данные находятся в файле data.csv 

**Признаки:**

- `RowNumber` — индекс строки в данных
- `CustomerId` — уникальный идентификатор клиента
- `Surname` — фамилия
- `CreditScore` — кредитный рейтинг
- `Geography` — страна проживания
- `Gender` — пол
- `Age` — возраст
- `Tenure` — сколько лет человек является клиентом банка
- `Balance` — баланс на счёте
- `NumOfProducts` — количество продуктов банка, используемых клиентом
- `HasCrCard` — наличие кредитной карты
- `IsActiveMember` — активность клиента
- `EstimatedSalary` — предполагаемая зарплата

**Целевой признак**

- `Exited` — факт ухода клиента

## Вывод

После проведения финального тестирования обученной модели Random Forest Classifier мы получили допустимые результаты, свидетельствующие о том, что модель пригодна к использованию для предсказания оттока клиентов.

Accuracy нашей модели 80%. Значение метрики AUC_ROC - 0.82, F1-мера равна 0.59.

Наша модель проходит тест на адекватность и умеет предсказывать уход клиента лучше, чем случайная модель.

## Используемые инструменты

`matplotlib` `numpy` `pandas` `sklearn`

**Модели**
`LogisticRegression` `DecisionTreeClassifier` `RandomForestClassifier` 


