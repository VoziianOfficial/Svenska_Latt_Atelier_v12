# Svenska Lätt — Atelier v12

Один проект веб-приложения для изучения шведского языка SFI A–D. Эта сборка прежде всего обновляет **оформление**, не заменяя логику упражнений и хранения результатов.

## Публикация на GitHub Pages

1. Распакуйте архив.
2. Загрузите **содержимое** папки `Svenska_Latt_Atelier_v12` в корень репозитория GitHub (рядом должны находиться `index.html`, `app.js`, `atelier.css`, JSON-файлы и прочее).
3. В GitHub `Settings → Pages` выберите ветку `main` и каталог `/(root)`.
4. После публикации обновите страницу с очисткой кэша браузера. Service Worker использует новую версию кэша `svenska-latt-v21-atelier`.

Сайт использует относительные пути и должен открываться также из подпапки `username.github.io/repository/`.

## Изображения и оформление

Используются **реальные стоковые фотографии** с Pexels, а не сгенерированные изображения. Чтобы не утяжелять архив, фотографии загружаются через `images.pexels.com` при открытии страниц; для отображения фотографий нужен интернет. Если внешний CDN заблокирован, предусмотрены цветные текстовые заглушки, а не нарисованные сцены. Доступность конкретных фотографий может измениться на стороне поставщика; изображения не хранятся в ZIP.

Источник: [Pexels](https://www.pexels.com/), условия [Pexels License](https://www.pexels.com/license/).

Версия включает отдельный `atelier.css` и `atelier.js` — единую новую визуальную надстройку поверх базовых компонентов. Старые временные `patch.css`/`patch.js` удалены. Карточки «Что учить сегодня» и тематические подборки прокручиваются горизонтально: 6 видимых на больших экранах, 4 на планшетах, 2 на телефонах. Курсы по уровням — 3 на десктопе; есть стрелки и естественный свайп.

## Локальная проверка

Открывайте проект через HTTP-сервер, не двойным кликом по `index.html`:

```bash
python3 -m http.server 8765
```

Затем посетите `http://127.0.0.1:8765`.

Для локального Python API и SQLite используйте:

```bash
python3 server.py
```

Программные тесты:

```bash
node tests/quality.cjs
python3 -m pytest -q tests/test_server.py
```

## Подключение Supabase — следующий шаг

В `supabase-config.js` заполняются **только** адрес проекта и публичный `publishable key`. В `SUPABASE_SETUP.sql` находятся таблицы и политики доступа. Не публикуйте сервисный секрет, пароль базы или `service_role`.

Пока Supabase не настроен, GitHub Pages работает в гостевом режиме и хранит результаты в конкретном браузере. Для облачной регистрации, синхронизации между устройствами и восстановления аккаунта требуется подключение Supabase и проверка реального облачного сценария.

## Что проверялось

- Работоспособность существующих учебных данных: 50 уроков, 1006 слов, 42 темы, 12 книг, 14 режимов тренировки.
- Регистрация и хранение данных на локальном Python-сервере (автоматические тесты).
- Клиентские сценарии в Chromium: горизонтальная прокрутка и кнопки слайдеров, выбор фильтров и поиск, открытие книги кликом по карточке, запуск теста, имя и уровень профиля, RU/EN/SV/UK, отсутствие горизонтального переполнения на 320/375/390/768/1024/1365 px.
- Размеры текста и кнопок в карточках, в частности шведские числа без переносов.

Это технические проверки, а не гарантия идеального отображения на всех конкретных устройствах или стопроцентной доступности внешних фотохостингов. Для выпуска публичного продукта также требуется сверить юридические документы с реальным владельцем платформы.


## v11 — voice speed, flashcard dimensions, calendar and load performance

- Playback speed: Very slow 0.56×, Slow 0.76×, Normal 1.0×. The selection is saved locally and shared by all narration buttons. Shorter TTS segments improve long-text playback reliability. A softer pitch and a preferred natural Swedish system voice are used when available; final quality depends on installed voices.
- Flashcards are compact and square, with a full-bleed photo and contrast overlay. Filter selects sit on one compact row. Review/word cards have horizontal buttons and a three-column desktop layout.
- Study dates show consecutive calendar days over two or four weeks; future dates do not count as completed activities.
- Removed a MutationObserver ↔ image-recreation feedback loop that repeatedly rebuilt thumbnails and slowed rendering. Existing photographs remain network-hosted, lazy-loaded where possible, and load-time can still vary with the network.
- The squirrel brand photograph comes from **Tiia Monto, “Red squirrel head.jpg”**, Wikimedia Commons, licensed under **CC BY-SA 4.0**. https://commons.wikimedia.org/wiki/File:Red_squirrel_head.jpg ; license https://creativecommons.org/licenses/by-sa/4.0/ . The photo is remotely hosted and a local generic-logo fallback appears when offline. Do not remove the attribution.
- `learning-polish.css` and `learning-polish.js` are loaded after v10. Update/replace all site files together (including the service worker) and hard-refresh GitHub Pages.


## v12 — performance and Swedish voice selection

- The unauthenticated site now **does not load the Supabase SDK** on start. The SDK is loaded only when the public Supabase URL and publishable key have been configured; no private credentials should ever be placed in the frontend.
- Image URLs and study data are unchanged, but CSS backgrounds are assigned only when nearing the viewport (`IntersectionObserver`), while normal image tags use lazy loading and asynchronous decoding.
- The service worker saves successfully viewed Pexels/Wikimedia photos in a bounded image cache (150 entries). **First visit still needs an internet connection**, and source websites may still block individual photos. Offline photos are available only after they were viewed successfully once while online. New release uses cache `svenska-latt-v23-shell` and `svenska-latt-v23-photos`.
- Narration controls keep the three requested speed modes and now offer a **voice picker if the device has several Swedish voices**. The selected voice and speed persist in localStorage. No bundled natural TTS audio or commercial speech API is included; voice quality remains dependent on the operating system.
- The squirrel photo uses the Wikimedia Commons file credited in the footer; the URL now uses the verified 250 px preview. Because the image is remote, a generic favicon fallback remains.
- Browser checks with an isolated in-memory copy verified the homepage, square flashcard and book narration controls at widths 320, 390, 768 and 1365 px with no reported page JavaScript errors. Normal navigation and real photo downloading were blocked by the test environment, so the deployed site still needs device-side verification.
- Cloud account creation, cross-device synchronization and authentication **cannot be verified or activated until the user's Supabase project exists and its public credentials are set in `supabase-config.js`**. The SQL migration remains included.
