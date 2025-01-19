# Урок 3. Сетевые запросы
## Цель: Разработать веб-приложение, которое каждый день будет отображать новое случайное изображение из коллекции Unsplash, давая пользователю возможность узнать больше о фотографе и сделать "лайк" изображению.

Регистрация на Unsplash:

    • Перейдите на веб-сайт Unsplash (https://unsplash.com/).
    • Зарегистрируйтесь или войдите в свой аккаунт. (если у вас не было регистрации до этого, новый аккаунт создавать не нужно).

Создание приложения:

    • Перейдите на страницу разработчика Unsplash (https://unsplash.com/developers).
    • Нажмите "New Application".
    • Заполните необходимую информацию о приложении (можете использовать http://localhost для тестирования).
    • Получите свой API-ключ после создания приложения.

Разработка веб-приложения:

    • Создайте HTML-страницу с элементами: изображение, имя фотографа, кнопка "лайк" и счетчик лайков.
    • Используя JavaScript и ваш API-ключ, получите случайное изображение из Unsplash каждый раз, когда пользователь загружает страницу.
    • Отобразите информацию о фотографе под изображением.
    • Реализуйте функционал "лайка". Каждый раз, когда пользователь нажимает кнопку "лайк", счетчик должен увеличиваться на единицу.

### * Дополнительные задачи (по желанию):

    • Добавьте функцию сохранения количества лайков в локальное хранилище, чтобы при новой загрузке страницы счетчик не сбрасывался.
    • Реализуйте возможность просмотра предыдущих "фото дня" с сохранением их в истории просмотров.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>

    <div class="content">
        <img src="#" alt="случайное изображение" class="img-box">
        <p class="img-author"></p>
        <p class="likes-count">Количество лайков: 0</p>
        <button class="like">Понравилось</button>
    </div>

    <script>

        const contentEl = document.querySelector('.content');
        const imgBox = contentEl.querySelector('.img-box');
        const authorP = contentEl.querySelector('.img-author');
        const likesP = contentEl.querySelector('.likes-count');
        const likeBtn = contentEl.querySelector('.like');

        // Читаем счетчик лайков из хранилища
        const likeCountersFromStorage = localStorage.getItem('likeCounters');
        // Объект счетчик лайков для картинок создаем из хранилища или новый пустой
        const likeCounters = likeCountersFromStorage ? JSON.parse(likeCountersFromStorage) : {};

        async function fetchImage() {
            try {
                const response = await fetch('https://api.unsplash.com/photos/random/?client_id=NYxz42js1jTJOhND-yz0NWrv6giD7tVCiSyLmErWLYg');
                const data = await response.json();
                return data;
            } catch (error) {
                console.log(`Произошла ошибка: ${error}`);
                return {};
            }
        }

        async function prepareHTMLForPicture() {
            const pictureData = await fetchImage();

            if (pictureData) { // Картинка загружена

                imgBox.setAttribute('src', `${pictureData.urls.small}`);
                imgBox.setAttribute('alt', 'случайное изображение');
                authorP.textContent = pictureData.user.name;

                // Первый раз заносим количество лайков в счетчик лайков пришедших из api
                if (!(pictureData.id in likeCounters)) {
                    // Заносим количество лайков в счетчик лайков пришедших из api
                    likeCounters[pictureData.id] = (pictureData.likes || 0);
                    // Пишем объект лайков в локальное хранилище
                    localStorage.setItem('likeCounters', JSON.stringify(likeCounters));
                }
                // Отображаем лайки
                likesP.textContent = `Количество лайков: ${likeCounters[pictureData.id]}`;

                likeBtn.addEventListener('click', function (e) {
                    // Увеличиваем и отображаем лайки
                    likesP.textContent = `Количество лайков: ${++likeCounters[pictureData.id]}`;
                    // Пишем объект лайков в локальное хранилище
                    localStorage.setItem('likeCounters', JSON.stringify(likeCounters));
                });

            } else { // Не удалось загрузить картинку
                imgBox.setAttribute('src', '#');
                imgBox.setAttribute('alt', 'не удалось загрузить изображение');
                authorP.textContent = '';
                likesP.textContent = 'Количество лайков: 0';
            }

        }

        window.addEventListener('load', function (e) {
            prepareHTMLForPicture();
        });

    </script>
</body>
</html>
```
