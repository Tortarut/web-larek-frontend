# Проектная работа "Веб-ларек"

Стек: HTML, SCSS, TS, Webpack

Структура проекта:
- src/ — исходные файлы проекта
- src/components/ — папка с JS компонентами
- src/components/base/ — папка с базовым кодом

Важные файлы:
- src/pages/index.html — HTML-файл главной страницы
- src/types/index.ts — файл с типами
- src/index.ts — точка входа приложения
- src/scss/styles.scss — корневой файл стилей
- src/utils/constants.ts — файл с константами
- src/utils/utils.ts — файл с утилитами

## Установка и запуск
Для установки и запуска проекта необходимо выполнить команды

```
npm install
npm run start
```

или

```
yarn
yarn start
```
## Сборка

```
npm run build
```

или

```
yarn build
```

---
## Архитектурное разделение и принципы проектирования
Проект разработан с учетом следующих ключевых принципов:

- **Разделение на слои (Separation of Concerns)**:

	- **Слой данных (Data Layer)**: включает интерфейсы и модели данных (например, `IProduct`, `ICustomerData`, `IOrder`), которые описывают структуру информации, поступающей от API и используемой в приложении.
	- **Слой отображения (Presentation Layer)**: содержит компоненты пользовательского интерфейса, отвечающие за визуализацию данных (например, `ProductCard`, `Header`, `AddToCartButton`).
	- **Слой навигации (Navigation Layer)**: управляет переходами между страницами и модальными окнами (например, логика перехода от `ProductCatalog` к `ProductDetailModal`).

- **Принцип единственной ответственности (Single Responsibility Principle)**:
	- Каждый класс или модуль выполняет только одну задачу. Например, компонент `ProductCard` отвечает исключительно за отображение информации о товаре, а модель `IProduct` описывает только данные товара.

- **Слабое связывание (Loose Coupling)**:
	- Взаимодействие между компонентами осуществляется через передачу данных (например, через параметры или события), а не через жесткую зависимость внутри классов. Это позволяет изменять и тестировать отдельные части системы независимо друг от друга.

## Типизация и модели данных
Все типы данных собраны в файле `src/types/index.ts`, что обеспечивает централизованное управление интерфейсами и типами. Типы разделены по функциональным группам:

#### Модели API: описывают данные, получаемые от сервера, например:

```typescript
export interface IApiProduct {
  productId: string;
  productDescription: string;
  productImage: string;
  productTitle: string;
  productCategory: string;
  productPrice: number | null;
}

export interface IApiProductList {
  totalCount: number;
  productItems: IApiProduct[];
}
```

#### UI модели: описывают данные, используемые в интерфейсе:

```typescript
export interface IProduct {
  productId: string;
  productDescription: string;
  productImage: string;
  productTitle: string;
  productCategory: string;
  productPrice: number | null;
}

export interface ICustomerData {
  paymentMethod: string;
  customerEmail: string;
  customerPhone: string;
  customerAddress?: string;
}

export interface IOrder {
  orderedProducts: IProduct[];
  customerInfo: ICustomerData;
  orderTotal: number;
}
```

#### Прочие типы: для описания пропсов компонентов, навигационных событий и т.д.

## API эндпоинты
#### 1. Список товаров
* Метод: GET
* Эндпоинт: `{{baseUrl}}/product/`
* Пример успешного ответа (200 OK):

```json
{
  "totalCount": 10,
  "productItems": [
    {
      "productId": "854cef69-976d-4c2a-a18c-2aa45046c390",
      "productDescription": "If you plan to solve tasks on the simulator, buy two.",
      "productImage": "/5_Dots.svg",
      "productTitle": "+1 hour in a day",
      "productCategory": "soft-skills",
      "productPrice": 750
    },
    ...
  ]
}
```

#### 2. Товар
* Метод: GET
* Эндпоинт: `{{baseUrl}}/product/{productId}`
* Пример успешного ответа (200 OK):

```json
{
  "productId": "854cef69-976d-4c2a-a18c-2aa45046c390",
  "productDescription": "If you plan to solve tasks on the simulator, buy two.",
  "productImage": "/5_Dots.svg",
  "productTitle": "+1 hour in a day",
  "productCategory": "soft-skills",
  "productPrice": 750
}
```

Ответ при ошибке (404 Not Found):

```json
{
  "error": "NotFound"
}
```

#### 3. Заказ
* Метод: POST
* Эндпоинт: `{{baseUrl}}/order`
* Пример тела запроса:

```json
{
  "paymentMethod": "online",
  "customerEmail": "test@test.com",
  "customerPhone": "+71234567890",
  "customerAddress": "Spb Vosstania 1",
  "orderTotal": 2200,
  "productIds": [
    "854cef69-976d-4c2a-a18c-2aa45046c390",
    "c101ab44-ed99-4a54-990d-47aa2bb4e7d9"
  ]
}
```

Пример успешного ответа (200 OK):

```json
{
  "orderId": "28c57cb4-3002-4445-8aa1-2a06a5055ae5",
  "confirmedTotal": 2200
}
```

Ошибки (400 Bad Request):

- Товар не найден:

```json
{
  "error": "Product with id c101ab44-ed99-4a54-990d-47aa2bb4e7d not found"
}
```

- Неверная сумма заказа:

```json
{
  "error": "Incorrect order total"
}
```

- Не указан адрес:

```json
{
  "error": "Address not provided"
}
```

## Компоненты и страницы

#### ProductCard
Отображает информацию о товаре (название, цена, категория и т.д.) и включает кнопку "Добавить в корзину".

#### AddToCartButton
Добавляет товар в корзину. Используется в: `ProductDetailModal`.

#### Header
Верхняя панель с логотипом и кнопкой корзины. Присутствует на главной странице и в каталоге товаров.

#### PaymentOptions
Позволяет выбрать способ оплаты (например, "online", "cash"). Используется в: `PaymentAddressModal`.

#### Form
Переиспользуемая форма для ввода/редактирования данных покупателя (телефон, email, адрес). Используется в: `ContactsModal`.

#### ProductCollection
Коллекция карточек товаров, отображаемая на странице каталога товаров.

#### CartCollection
Отображает список товаров, добавленных в корзину, и используется в `CartModal`.

## Страницы и модальные окна
#### HomePage:
Главная страница с каталогом товаров и шапкой.

#### ProductCatalog:
Страница со списком всех доступных товаров. При клике на товар открывается `ProductDetailModal`.

#### ProductDetailModal:
Отображает подробную информацию о товаре с кнопкой "Добавить в корзину".

#### CartModal:
Отображает текущую корзину и содержит кнопку для перехода к оформлению заказа, открывая `PaymentAddressModal`.

#### PaymentAddressModal:
Позволяет выбрать способ оплаты и ввести адрес. При нажатии "Далее" открывается `ContactsModal`.

#### ContactsModal:
Форма для ввода телефона и email. При нажатии "Оплатить" открывается `OrderSuccessModal`.

#### OrderSuccessModal:
Подтверждает успешное оформление заказа. Кнопка "За новыми покупками" возвращает пользователя в каталог товаров.