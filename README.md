# Проектная работа «Веб-ларек»

## Используемые технологии:

- HTML
- SCSS
- TypeScript (TS)
- Webpack
- ООП

## Установка и запуск проекта

1. Установите зависимости проекта:
   ```
   npm install
   ```
2. Запустите проект:
   ```
   npm run start
   ```

## Подход разработки

В данном проекте используется событийно-ориентированный подход, который предполагает, что взаимодействие между различными компонентами приложения осуществляется через обмен сообщениями, вызванными определенными событиями.

Кроме того, в проекте используется модель MVP (Model-View-Presenter), которая разделяет приложение на три основных компонента: модель, представление и презентатор. Модель отвечает за работу с данными и бизнес-логикой, представление представляет собой интерфейс пользователя, а презентатор связывает модель и представление, обрабатывая события и передавая данные между ними.

## Базовый код

### Класс `Api`

Класс `Api` хранит основные поля и методы, необходимые при работе с сервером. Получает и хранит базовый URL (`baseUrl`) и опции запроса (`options`). Методы позволяют обработать запрос, отправить и получить данные.

**Конструктор:**
- Принимает на вход `baseUrl` и `options`.
- Устанавливает заголовок `Content-Type` в `application/json`.

**Методы:**
- `handleResponse`: Обрабатывает ответ сервера.
- `get`: Выполняет GET-запрос.
- `post`: Выполняет POST-запрос.

### Класс `Component`

Абстрактный класс, обобщающий конструктор и основные методы работы с компонентами отображения.

**Конструктор:**
- Принимает на вход `container` типа `HTMLElement`.

**Методы:**
- `toggleClass`: Переключает класс элемента.
- `setDisabled`: Блокирует/разблокирует кнопку.
- `setText`: Устанавливает текст элемента.
- `setImage`: Устанавливает изображение элемента и его `alt`.
- `render`: Слияние входящих данных и отрисовка элемента.

### Класс `EventEmitter`

Класс `EventEmitter` обеспечивает работу с событиями. Функциональность класса стандартная: возможность установить/снять слушатели событий, вызвать слушателей при возникновении события.

**Методы:**
- `on`: Устанавливает обработчик на событие.
- `off`: Снимает обработчик с события.
- `emit`: Инициирует событие с данными.
- `onAll`: Слушает все события.
- `offAll`: Сбрасывает все обработчики.
- `trigger`: Создает коллбек триггер, генерирующий событие при вызове.

### Компоненты модели данных (Model)

#### Класс `LarekApi`

Основной класс работы с сетью в проекте. Расширяется базовым классом `Api`.

**Конструктор:**
- Принимает передаваемые в родительский конструктор `Api` поля `baseUrl` и `options`.
- Принимает и сохраняет входящий `url` запроса в `cdn`.

**Поля:**
- `cdn` — хранит входящий `url`.

**Методы:**
- `getProductList`: Метод получения списка товаров с сервера.
- `orderProducts`: Метод отправки данных заказа на сервер.

#### Класс `AppState`

Является моделью данных приложения в целом. Данный класс содержит в себе все основные группы данных страницы и методы работы с ними. Тут распределяются данные частей приложения: каталог, превью, корзина, форма заказа. Расширяется базовым абстрактным классом `Model` по интерфейсу `IAppState`.

```tsx
export interface IAppState {
  catalog: IProduct[];
  basket: IProduct[];
  preview: string | null;
  order: IOrder;
  formErrors: FormErrors;
}
```

**Поля:**
- `catalog` — для данных списка товаров, пришедших с сервера.
- `basket` — для данных товаров, добавленных в корзину.
- `preview` — для данных товара, открытого в превью.
- `order` — для данных заказа, который отправляется на сервер.
- `formErrors` — для ошибок валидации.

**Методы:**
- `clearBasket`: Очистка данных корзины.
- `clearOrder`: Очистка данных заказа.
- `setCatalog`: Установка данных в каталог.
- `setPreview`: Установка данных в превью.
- `addToBasket`: Добавление товара в заказ.
- `removeFromBasket`: Удаление товара из заказа.
- `updateBasket`: Обновление данных корзины.
- `setDeliveryField`: Установка поля доставки.
- `setContactField`: Установка полей контактов.
- `validateDelivery`: Проведение валидации данных доставки.
- `validateContact`: Проведение валидации данных контактов.

#### Класс `Product`

Является моделью хранения данных товара: идентификатора, заголовка, описания, категории, изображения, цены. Расширяется базовым абстрактным классом `Model` по интерфейсу `IProduct`.

```tsx
export interface IProduct {
  id: string;
  title: string;
  price: number | null;
  description: string;
  category: string;
  image: string;
}
```

### Компоненты представления (View)

#### Класс `Card`

Отвечает за отображение данных карточки товара в каталоге. Поля отвечают за связь с разметкой, методы за наполнение разметки данными. Расширяется базовым абстрактным классом `Component` по интерфейсу `ICard`.

```tsx
interface ICard {
  title: string;
  category: string;
  image: string;
  price: number;
  buttonTitle?: string;
}
```

**Конструктор:**
- Принимает `container` типа `HTMLElement` и опциональный объект события `actions` типа `IActions`.
- Передает `container` в родительский конструктор.
- Сохраняет необходимые элементы разметки в полях.
- Если объект `actions` был передан, то вешает слушатель клика на `container` с вызовом объекта события `actions`.

**Поля:**
- `_title` — хранит разметку заголовка карточки.
- `_category` — хранит разметку категории карточки.
- `_image` — хранит разметку изображения карточки.
- `_price` — хранит разметку цены карточки.
- `_button` — хранит разметку кнопки карточки.
- `_categoryClasses` — словарь для дальнейшей модификации класса категории в зависимости от её содержимого.

**Методы:**
- `set title` — установка содержимого заголовка.
- `set category` — установка содержимого категории.
- `set image` — установка содержимого изображения.
- `set price` — установка содержимого цены.
- `set buttonTitle` — установка текста кнопки.
- `disablePriceButton` — блокировка кнопки, если цена равна `null`.

#### Класс `Basket`

Отвечает за отображение корзины в модальном окне. Можно сказать, что служит контентом для модалки. Имеет возможность удаления позиции товара из корзины. Расширяется базовым абстрактным классом `Component` по интерфейсу `IBasket`.

```tsx
interface IBasket {
  items: HTMLElement[];
  total: number;
}
```

**Конструктор:**
- Принимает `container` типа `HTMLElement` и объект `event` типа `IEvent`.
- Передает `container` в родительский конструктор.
- Сохраняет необходимые элементы разметки в полях.
- Если есть `button`, вешает на неё слушатель `click` с регистрацией события `order:open` в качестве колбека.

**Поля:**
- `_list` — хранит разметку обертки списка товаров.
- `_total` — хранит разметку для суммы товаров.
- `_button` — хранит разметку кнопки перехода на шаг оформления заказа.
- `items` — хранит список товаров в корзине.

**Методы:**
- `set items` — устанавливает товары в разметку `_list`.
- `set total` — устанавливает значение суммы товаров.
- `toggleButton` — переключает состояние кнопки.

#### Класс `Page`

Отвечает за отображение данных составляющих элементов страницы: каталог, корзина, счетчик товаров в корзине. Поля отвечают за связь с разметкой, методы за наполнение разметки данными, а также метод закрытия/открытия для прокрутки страницы при открытии/закрытии модального окна. Расширяется базовым абстрактным классом `Component` по интерфейсу `IPage`.

```tsx
interface IPage {
  counter: number;
  catalog: HTMLElement[];
}
```

**Конструктор:**
- Принимает `container` типа `HTMLElement` и объект `event` типа `IEvent`.
- Передает `container` в родительский конструктор.
- Сохраняет необходимые элементы разметки в полях.
- Вешает на кнопку корзины `_basket` слушатель события `click`, при котором регистрируется событие `basket:open`.

**Поля:**
- `_counter` — хранит разметку счетчика товаров в корзине.
- `_catalog` — хранит разметку каталога товаров.
- `_wrapper` — хранит разметку обертки страницы.
- `_basket` — хранит разметку кнопки корзины.

**Методы:**
- `set counter` — устанавливает значение в счетчике товаров корзины.
- `set catalog` — устанавливает каталог.
- `set locked` — устанавливает класс, препятствующий прокрутке страницы.

#### Класс `Modal`

Отвечает за отображение модального окна. По сути своей является «коробкой» для любого содержимого. Потому и имеет минимальное количество полей и методов, которые связаны в основном с закрытием и открытием окна, а также наполнением его нужным контентом. Расширяется базовым абстрактным классом `Component` по интерфейсу `IModalData`.

```tsx
interface IModalData {
  content: HTMLElement;
}
```

**Конструктор:**
- Принимает `container` типа `HTMLElement` и объект `event` типа `IEvent`.
- Передает `container` в родительский конструктор.
- Сохраняет необходимые элементы разметки в полях.
- Вешает слушатель `click` на кнопку закрытия `_closeButton` и `container` с методом закрытия окна `close` в качестве колбека.
- Вешает слушатель `click` на `content`, для остановки распространения события при совершении клика на контенте модального окна.

**Поля:**
- `_closeButton` — разметка кнопки закрытия модального окна.
- `_content` — разметка контейнера для контента модального окна.

**Методы:**
- `set content` — устанавливает контент модального окна.
- `open` — открывает модальное окно.
- `close` — закрывает модальное окно.
- `render` — отрисовывает данные контента и открывает модальное окно.

#### Класс `Form`

Отвечает за основные способы работы с формой и её валидацию. Класс является дженериком и принимает в переменной `T` тип данных компонента отображения. Расширяется базовым абстрактным классом `Component` по интерфейсу `IFormState`.

```tsx
interface IFormState {
  valid: boolean;
  errors: string[];
}
```

**Конструктор:**
- Принимает `container` типа `HTMLFormElement` и объект `event` типа `IEvent`.
- Передает `container` и `event` в родительский конструктор.
- Сохраняет необходимые элементы разметки в полях.
- Вешает на `container` слушатель `input`, при котором определяется поле воздействия, его имя и значение, которые после отправляются методу класса `onInputChange` в качестве аргументов.
- Вешает на `container` слушатель `submit`, при котором происходит регистрация события с указанием имени `container`.

**Поля:**
- `_submit` — хранит разметку кнопки отправки формы.
- `_errors` — хранит разметку вывода ошибок валидации.

**Методы:**
- `onInputChange` — регистрирует событие с именем конкретного поля.
- `set valid` — метод установки валидности.
- `set errors` — метод установки ошибки.
- `render` — отрисовка формы и её элементов.

#### Класс `DeliveryForm` и `ContactForm`

Классы `DeliveryForm` и `ContactForm` отвечают за отображение первой и второй шагов заказа соответственно. Расширяются классом `Form` по интерфейсу `IOrderForm`.

**Конструктор:**
- Принимает `container` типа `HTMLFormElement` и объект `event` типа `IEvent`.

**Методы:**
- `toggleButtons`: Переключает активность кнопок.
- `set address`, `set phone`, `set email`: Устанавливают соответствующие поля формы.

#### Класс `Success`

Отвечает за отображение успешного оформления заказа в модальном окне. Можно сказать, что служит контентом для модалки. Расширяется базовым абстрактным классом `Component` по интерфейсу `ISuccess`.

```tsx
interface ISuccess {
  total: number;
}
```

**Конструктор:**
- Принимает `container` типа `HTMLElement` и опциональный объект события `actions` типа `ISuccessActions`.
- Передает `container` в родительский конструктор.
- Сохраняет необходимые элементы разметки в полях.
- Если объект `actions` был передан, то вешает слушатель клика на `_close` с вызовом объекта события `actions`.

**Поля:**
- `_total` — разметка общей суммы товаров в заказе.
- `_close` — разметка кнопки закрытия окна.

**Методы:**
- `set total` — устанавливает значение общей суммы.
