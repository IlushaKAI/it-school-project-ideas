## Интернет‑магазин на Fake Store API

### 1.1 Маршрутизация и структура файлов

```bash
/src
  App.jsx
  main.jsx
  hooks/useCart.js
  pages/
    Home.jsx
    Product.jsx
    Cart.jsx
  components/
    ProductCard.jsx
    CartItem.jsx

```

#### App.jsx

- Оборачиваем приложение в `<BrowserRouter>`
    
- Объявляем три `<Route>`:

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/product/:id" element={<Product />} />
    <Route path="/cart" element={<Cart />} />
  </Routes>
</BrowserRouter>

```

[W3Schools.com](https://www.w3schools.com/react/react_router.asp?utm_source=chatgpt.com)


### 1.2 Хук работы с корзиной (`useCart.js`)

- **Состояние** `cart` через `useState(() => JSON.parse(localStorage.getItem(KEY)) || [])`
    
- **Синхронизация**: в `useEffect` при изменении `cart` выполнять `localStorage.setItem(KEY, JSON.stringify(cart))` [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Storage/setItem?utm_source=chatgpt.com)
    
- **Методы:**
```js
const addToCart = item => setCart(prev => [...prev, item]);
const removeFromCart = id => setCart(prev => prev.filter(i => i.id !== id));
```
### 1.3 Home.jsx (список товаров)

- **Состояние** `products`, `setProducts` через `useState([])`
    
- **useEffect** (пустой массив зависимостей) вызывает `fetch('https://fakestoreapi.com/products')` [React Router](https://reactrouter.com/web/api/Hooks?utm_source=chatgpt.com) и сохраняет массив в `products`
    
- **Рендер:**
```jsx
products.map(p => <ProductCard key={p.id} product={p} />)
```

### 1.4 Product.jsx (детали товара)

- Чтение `id` через `const { id } = useParams()` [React Router API](https://api.reactrouter.com/v7/functions/react_router.useParams.html?utm_source=chatgpt.com)
    
- **Состояние** `product`, `setProduct`
    
- **useEffect** по `[id]` делает `fetch('https://fakestoreapi.com/products/'+id)` и сохраняет в `product`
    
- Кнопка “В корзину” вызывает `addToCart(product)` из `useCart` и программно переходит на `/cart` через `useNavigate()` [React Router API](https://api.reactrouter.com/v7/functions/react_router.useNavigate.html?utm_source=chatgpt.com)
    

### 1.5 Cart.jsx (корзина)

- **useCart** предоставляет `{ cart, removeFromCart }`
    
- Если `cart.length === 0`, показываем сообщение “Корзина пуста”
    
- Иначе рендерим `cart.map(item => <CartItem key={item.id} item={item} onRemove={removeFromCart} />)` и сумму цен

### Компоненты

### 1.1 ProductCard.jsx

**Назначение:** отображать краткую информацию о товаре в списке на главной странице.

- **Пропсы:**
    
    - `product` (объект) — полный объект товара из API.
        
    - `onAddToCart` (функция) — колбэк для добавления товара в корзину (можно не передавать, если кнопка “Подробнее” ведёт на страницу товара).
        
- **Что внутри:**
    
    1. Деструктурировать из `product` поля `id`, `title`, `price`, `image`.
        
    2. Вывести картинку (`<img>`), заголовок (`<h2>`) и цену (`<p>`).
        
    3. Кнопка “Подробнее” должна быть `<Link>` на `/product/{id}`.
        
    4. (Опционально) кнопка “В корзину” вызывает `onAddToCart(product)`.
        
- **Стилизация:**  
    — flex/ grid для карточки; отступы и тень; адаптивная высота картинки.
    

---

### 1.2 CartItem.jsx

**Назначение:** отображать один товар в корзине на странице Cart.

- **Пропсы:**
    
    - `item` (объект) — товар, как в `product`.
        
    - `onRemove` (функция) — колбэк для удаления из корзины по `item.id`.
        
- **Что внутри:**
    
    1. Деструктурировать `item.id`, `title`, `price`, `image`.
        
    2. Показать мини‑картинку и название товара.
        
    3. Вывести цену.
        
    4. Кнопка “Удалить” вызывает `onRemove(id)`.
        
- **Стилизация:**  
    — flex‑строка, выравнивание по центру, кнопка справа.
    

---

### 1.3 Дополнительные небольшие компоненты

- **LoadingSpinner.jsx**  
    — простой индикатор загрузки (анимация или текст).
    
    - Пропсы: `size`, `message`.
        
    - Использовать во всех местах, где ждём `fetch`.
        
- **ErrorMessage.jsx**  
    — выводит текст ошибки.
    
    - Пропсы: `text`.
        
    - Показывать вместо контента при ошибке запроса.