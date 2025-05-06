
## Низкий уровень (HTML/CSS/JS + лёгкий API)

1. **Галерея случайных питомцев**  
    — Использовать API случайных изображений животных: например, Dog API. [Mixed Analytics](https://mixedanalytics.com/blog/list-actually-free-open-no-auth-needed-apis/?utm_source=chatgpt.com)  
    — Создать кнопки «Следующий»/«Предыдущий», которые меняют картинку, вставляя новый `<img>` в DOM.
    
2. **Список советов**  
    — Подключить Advice Slip API (https://api.adviceslip.com). [GitHub](https://github.com/public-apis/public-apis?utm_source=chatgpt.com)  
    — При загрузке страницы и по клику «Дать совет» делать `fetch()`, брать текст совета и вставлять в `<blockquote>`.
    
3. **Информация о стране**  
    — Использовать REST Countries API ([https://restcountries.com](https://restcountries.com)). [DEV Community](https://dev.to/ruppysuppy/7-free-public-apis-you-will-love-as-a-developer-166p?utm_source=chatgpt.com)  
    — Пользователь вводит название страны, JS запрашивает данные и показывает флаг, столицу и население, вставляя результат в список `<ul>`.
    
4. **Часы с мировым временем**  
    — Работать с World Time API (http://worldtimeapi.org/api). [GitHub](https://github.com/public-apis/public-apis?utm_source=chatgpt.com)  
    — Ввести select с часовыми поясами, по выбору отображать текущее время этого пояса, обновляя раз в секунду через `setInterval`.
    

---

## Средний уровень (React + Vite + хуки + компоненты)

1. **Каталог пользователей**  
    — Использовать Random User API ([https://randomuser.me](https://randomuser.me)). [Mixed Analytics](https://mixedanalytics.com/blog/list-actually-free-open-no-auth-needed-apis/?utm_source=chatgpt.com)  
    — Главный компонент `App` загружает список пользователей через `useEffect`, сохраняет в `useState`, рендерит `<UserCard>` для каждого (`props` для имени, аватара и т.д.).
    
2. **Галерея астрономических снимков**  
    — Подключить NASA APOD API ([https://api.nasa.gov](https://api.nasa.gov)). [Reddit](https://www.reddit.com/r/Frontend/comments/1dbyqda/what_are_some_cool_apis_you_can_use_for_free_eg/?utm_source=chatgpt.com)  
    — Компонент `Gallery` подтягивает данные, выводит карточки с изображением и описанием; при клике — модальное окно (`useState` для показа).
    
3. **Калькулятор валют**  
    — Использовать API обменных курсов (e.g. ExchangeRate‑API). [Apipheny](https://apipheny.io/free-api/?utm_source=chatgpt.com)  
    — Компоненты `CurrencySelector`, `AmountInput`, `ResultDisplay`. `useEffect` на изменение селекторов или суммы, пересчитывает курс.
    
4. **Справочник цитат**  
    — API the quotes hub https://thequoteshub.com/ 
    

---

## Высокий уровень (React Router + многопоточность + LocalStorage)

1. **Интернет-магазин (фейковые товары)**  
    — Fake Store API ([https://fakestoreapi.com](https://fakestoreapi.com)). [Mixed Analytics](https://mixedanalytics.com/blog/list-actually-free-open-no-auth-needed-apis/?utm_source=chatgpt.com)  
    — Многостраничное приложение:
    — Состояние корзины хранить в LocalStorage через `useEffect`; компоненты из shadcn/ui по желанию.



## Пример лендингов, если хотите просто попрактиковать стилизацию элементов

но мороки там тоже много:)

https://www.figma.com/community/file/1127302394641561751