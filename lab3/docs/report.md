# Лабораторная работа №3. Bubble

**Тема:** Дневник питомца
**Dev-версия приложения:** https://my-app-261010-38880.bubbleapps.io/version-test

---

## Краткое описание выполненного

В ходе лабораторной работы было создано веб-приложение «Дневник питомца» на no-code платформе Bubble. Приложение позволяет пользователям регистрироваться, добавлять своих питомцев, вести записи о здоровье питомцев (вакцинации, визиты к ветеринару, приёмы лекарств) и управлять ими через полноценный CRUD.

Что сделано:
- Сгенерирован каркас приложения через AI-агента Bubble по промпту
- Изучен Build Guide «Add and manage pets», применена идея создания страницы формы добавления
- Вручную доработан UI: изменён заголовок, создана Global variable `primary-color`, свой стиль `PrimaryButton`, добавлен Checkbox из библиотеки
- Создана сущность `HealthRecord` с 7 полями разных типов + связь с сущностью `Питомец`
- Реализованы все 4 CRUD-workflow для HealthRecord: Create, Read, Update, Delete
- Проверена работа приложения в Preview

---

## Часть 1. Промпт и генерация каркаса

### Промпт (дословно)

```
Создай приложение на тему «Дневник питомца»

Пусть этот сайт будет требующий регистрацию

Страницу обязательно создай по структуре header + body + footer

Обязательно укажи в заготовке следующую строку: «ТВП: Боровик»

Добавь несколько интерактивных элементов (реализующих CRUD):
список питомцев, форму добавления и кнопки редактирования/удаления
```

### Результат генерации

Bubble сгенерировал каркас приложения:
- Header «Дневник питомца»
- Hero-секция «Маленькие лапы. Большая история.»
- Кнопки «Завести дневник» / «Уже есть аккаунт»
- 3 фичи-карточки: «Все питомцы рядом», «Работа без суеты», «История, которая остаётся»
- Footer: «© 2026 Дневник домашнего животного · ТВП: Боровик»
- Регистрация и вход
- Data Types: `User`, `Питомец`, `Дневниковая запись`
- Готовые workflows: Auth login / signup / open / error

**Скриншот:** `lab3/screenshots/01-prompt.png`

---

## Часть 2. Исследование идеи (Build Guides)

### Что посмотрела

Build Guide: **«Add and manage pets»** на https://bubble.io/home/buildguides
Тема гайда: CRUD для питомцев — от Data Type до workflow.

Дополнительно изучен блок **DESIGN → «Design the Add Pet Page»**.

### Что показалось полезным

- Гайд даёт готовую структуру Data Type `Pet` с полями: Name (text), Type (text), Breed (text), BirthDate (date), Photo (image)
- Блок DESIGN показывает, как собрать страницу Add Pet:
  - input-поля под каждое поле (Name, Type, Breed)
  - date picker для Birth Date
  - image uploader для Photo
  - кнопка Add Pet
- Блок LOGIC объясняет workflow для кнопки Add Pet: создать запись → привязать к Current User

### Что взяла в своё приложение

Применила идею из блока DESIGN — создала новую страницу `add-pet` с формой добавления питомца:
- 3 text input: Name, Type, Breed
- date picker: Birth date
- image uploader: Photo
- кнопка «Add Pet»

Использовала рекомендованную структуру полей из блока Database structure.

### Скриншоты «до» и «после»

- До: главная страница `index` без формы добавления питомца → `screenshots/02-before.png`
- После: новая страница `add-pet` с формой → `lab3/screenshots/02-after.png`

### Скриншоты гайдов

- `lab3/screenshots/02-buildguide-1.png` — список фич от Bubble
- `lab3/screenshots/02-buildguide-2.png` — открытый гайд «Add and manage pets»
- `lab3/screenshots/02-buildguide-3.png` — блок DESIGN: структура страницы Add Pet

---

## Часть 3. Ручная доработка UI

Всё делалось вручную, без AI.

### 3.1. Изменение заголовка

В footer на странице `index` изменён текст:
- Было: `© 2026 Дневник питомца · ТВП: Боровик`
- Стало: `© 2026 Дневник питомца · ТВП: Боровик Елизавета`

### 3.2. Global variable для цвета

- Создана Global variable `primary-color` типа Color (жёлтый/оранжевый)
- Переменная применена к фону кнопки «Завести дневник» на странице `index`

**Скриншот:** `lab3/screenshots/03-global-variable.png`

### 3.3. Новый стиль

- Создан стиль `PrimaryButton` для кнопок
- В стиле в свойстве Background использована Global variable `primary-color`
- Стиль применён к кнопкам (например, «Add Pet» на странице `add-pet`)
- Стиль помечен как `Default` для всех кнопок

**Скриншот:** `lab3/screenshots/04-style.png`

### 3.4. Готовый компонент из библиотеки

- Из панели Design → Input forms добавлен **Checkbox**
- Размещён на странице `add-pet` под формой добавления питомца
- Label: «Напоминать о прививках»
- Preset status: Unchecked

**Скриншот:** `lab3/screenshots/05-component.png`

---

## Часть 4. Data и Workflows

### 4.1. Data: новая сущность

#### Промпт для генерации полей (дословно)

```
Для приложения «Дневник питомца» предложи новую сущность (Data Type)
и её поля. Тема: записи о здоровье питомца — вакцинации, визиты
к ветеринару, приёмы лекарств.

Формат ответа:
- Название сущности (на английском, CamelCase, например HealthRecord)
- Список полей: имя поля — тип (text / date / number / yes-no / link / image)
- Минимум 5 полей разных типов
- Краткое описание каждого поля
```

#### Ответ AI

AI предложил сущность `HealthRecord` со следующими полями:
- `recordType` — text — тип записи (вакцинация, визит, лекарство)
- `eventDate` — date — дата события
- `description` — text — описание (диагноз, название вакцины)
- `medicationDose` — number — дозировка
- `isCompleted` — yes/no — завершено ли
- `veterinarian` — link — ссылка на ветеринара/клинику
- `documentPhoto` — image — фото документа/чека

#### Созданная сущность

В Data → Data types создан новый Data Type **`HealthRecord`** с полями:

| Поле | Тип |
|------|-----|
| `recordType` | text |
| `eventDate` | date |
| `description` | text |
| `medicationDose` | number |
| `isCompleted` | yes/no |
| `veterinarian` | text |
| `documentPhoto` | image |
| `Pet` | Питомец (связь с Data Type «Питомец») |

6 разных типов полей — требование «минимум 3» выполнено с запасом.

**Скриншоты:**
- `lab3/screenshots/06-data-type.png` — типы данных и поля
- `lab3/screenshots/07-data-records.png` — 3 записи HealthRecord, созданные вручную через App Data

#### Записи (созданы вручную через App Data)

1. Прививка от бешенства — Вакцинация, 10.10.2026, доза 1, завершено 
2. Плановый осмотр — Визит к ветеринару, 10.12.2026, доза 0, не завершено 
3. Антибиотик, курс 5 дней — Лекарство, 10.02.2026, доза 2, завершено 

### 4.2. Workflows: полный CRUD

UI создан с использованием AI на новой странице `health-records`. Workflows реализованы и проверены вручную.

#### Create — workflow «Добавить запись о здоровье»

- **Event:** Add Save Button is clicked
- **Step 1:** Create a new HealthRecord
  - recordType = Add Type Input's value
  - eventDate = Add Date Date picker's value
  - description = Add Description Input's value
  - medicationDose = Add Dose Input's value
  - isCompleted = Add Completed Checkbox is checked
  - Pet = Add Pet Dropdown's value
- **Step 2:** Reset relevant inputs

**Скриншот:** `lab3/screenshots/08-workflow-create.png`

#### Read — Repeating Group на странице `health-records`

Repeating Group со списком всех HealthRecord. Data source: `Search for HealthRecords`.
В каждой карточке отображаются: recordType, eventDate, description, isCompleted + кнопки «Редактировать» и «Удалить».

Проверено в Preview.

#### Update — workflow `Edit save`

- **Event:** Edit Save Button is clicked
- **Step 1:** Make changes to HealthRecord
  - Thing to change: Edit Popup's HealthRecord
  - recordType = Edit Type Input's value
  - eventDate = Edit Date Date picker's value
  - description = Edit Description Input's value
  - medicationDose = Edit Dose Input's value
  - isCompleted = Edit Completed Checkbox is checked
  - Pet = Edit Pet Dropdown's value
- **Step 2:** Hide Edit Popup
- **Step 3:** Reset relevant inputs

**Скриншот:** `lab3/screenshots/09-workflow-update.png`

#### Delete — workflow `Del confirm`

- **Event:** Del Confirm Button is clicked
- **Step 1:** Delete HealthRecord
  - To delete: Del Popup's HealthRecord
- **Step 2:** Hide Del Popup

**Скриншот:** `lab3/screenshots/10-workflow-delete.png`

#### Проверка в Preview

Все 4 действия проверены в Preview на странице `health-records`:
- Добавление новой записи через форму — работает ✅
- Отображение списка записей — работает ✅
- Редактирование существующей записи через попап — работает ✅
- Удаление записи через попап подтверждения — работает ✅

**Скриншот Preview:** `lab3/screenshots/08-preview.png`

---

## Использованные AI-промпты (дословно)

### 1. Промпт для генерации каркаса (Часть 1)

```
Создай приложение на тему «Дневник питомца»

Пусть этот сайт будет требующий регистрацию

Страницу обязательно создай по структуре header + body + footer

Обязательно укажи в заготовке следующую строку: «ТВП: Боровик»

Добавь несколько интерактивных элементов (реализующих CRUD):
список питомцев, форму добавления и кнопки редактирования/удаления
```

### 2. Промпт для новой сущности (Часть 4.1)

```
Для приложения «Дневник питомца» предложи новую сущность (Data Type)
и её поля. Тема: записи о здоровье питомца — вакцинации, визиты
к ветеринару, приёмы лекарств.

Формат ответа:
- Название сущности (на английском, CamelCase, например HealthRecord)
- Список полей: имя поля — тип (text / date / number / yes-no / link / image)
- Минимум 5 полей разных типов
- Краткое описание каждого поля
```

### 3. Промпт для генерации UI страницы health-records (Часть 4.2)

```
Создай новую страницу "health-records" для ведения записей о здоровье питомцев.

На странице должно быть:
1. Header и footer как на других страницах приложения
2. Repeating Group со списком всех HealthRecord. В каждой карточке показывай:
   - recordType (тип записи: вакцинация, визит и т.д.)
   - eventDate (дата)
   - description (описание)
   - isCompleted (завершено да/нет)
   - Кнопки "Редактировать" и "Удалить"
3. Форма добавления новой записи над списком:
   - input для recordType
   - date picker для eventDate
   - input для description
   - input для medicationDose (число)
   - checkbox для isCompleted
   - dropdown для выбора Pet (из Data Type Питомец)
   - Кнопка "Добавить запись"
```

---

## Освоенные элементы и workflows

### Элементы Bubble
- **Text** — заголовки, подписи
- **Input** (текстовые поля) — Name, Type, Breed, Description, Dose
- **Date Picker** — выбор даты
- **Image Uploader** — загрузка фото питомца
- **Image** — отображение картинок
- **Button** — кнопки действий
- **Checkbox** — флаг «Напоминать о прививках»
- **Dropdown** — выбор питомца
- **Repeating Group** — список записей HealthRecord
- **Popup** — попапы редактирования и удаления
- **Group / Container** — контейнеры для layout

### Data Types
- **User** — встроенный (с регистрацией)
- **Питомец** — сущность питомцев (6 полей)
- **Дневниковая запись** — записи о питомцах (от AI)
- **HealthRecord** — собственная сущность (7 полей + связь с Питомец)

### Типы полей
- text, date, number, yes/no, image, link, ссылка на другую сущность (Pet)

### Workflows (освоено)
- **Create a new thing** — создание записи
- **Make changes to a thing** — редактирование
- **Delete a thing** — удаление
- **Reset relevant inputs** — очистка формы после действия
- **Show / Hide popup** — управление попапами
- **Element is clicked** — событие клика
- **Sign the user up / log in** — регистрация и вход (от AI)

### Global / Styles
- **Global variables** — `primary-color` (Color)
- **Styles** — `PrimaryButton` с использованием переменной

---

## Выводы

В ходе работы я освоила платформу Bubble как инструмент визуального программирования без кода. Ключевые выводы:

1. **AI-агент Bubble** позволяет быстро сгенерировать рабочий каркас приложения по текстовому промпту — это сильно ускоряет старт, но требует последующей ручной доработки.

2. **Build Guides** дают конкретные рекомендации по структуре данных и UI — изучение гайда «Add and manage pets» помогло правильно спроектировать страницу добавления питомца.

3. **Data Types и связи** — важнейшая часть приложения. Связь `HealthRecord → Питомец` (через поле типа `Питомец`) позволяет фильтровать записи по конкретному питомцу.

4. **Workflows в Bubble** строятся по схеме «Event → Actions». Для CRUD нужно минимум 4 workflow: Create, Read (Repeating Group), Update, Delete.

5. **Global variables и Styles** — мощный механизм для поддержания единообразия дизайна. Изменение переменной `primary-color` автоматически обновляет все элементы, использующие стиль `PrimaryButton`.

6. **Preview** критически важен для проверки работоспособности — все workflows проверены в живом приложении.

7. **Git и коммиты** — ведение осмысленной истории коммитов помогает отслеживать прогресс и упрощает защиту работы.

В результате получилось рабочее веб-приложение «Дневник питомца» с полным CRUD для записей о здоровье, регистрацией пользователей и продуманным UI.
