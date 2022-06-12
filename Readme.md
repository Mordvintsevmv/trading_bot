# Торговый робот для Тинькофф Инвестиции

---

## Описание 

Данный торговый робот представляет собой телеграм-бот, который позволяет:
- Просматривать доступную валюту на счёте
- Просматривать доступные бумаги на счёте
- Просматривать открытые ордера и закрывать их по требованию
- Покупать ценные бумаги в чате
- Продавать ценные бумаги в чате
- Просматривать статистику по торговым стратегиям
- Выводить часовые и 15-минутные графики с торговыми индикаторами
- Указывать направление и силу тренда акций
- Запускать или останавливать торговые стратегии из чата

Главное особенностью данного торгового бота является его понятность и простота в использовании. Любой пользователь может без предварительного изучения списка команд или теории по трейдингу воспользоваться данным проектом.

---

## Запуск

Для запуска торгового бота необходимо:
1. Создать файл .env (по примеру .env.example) с указанием Токена Тинькофф и токена Телеграм-бота.
   1. BOT_TOKEN - токен бота телеграм
2. Установить необходимые библиотеки из requirements.txt.

Для запуска торгового робота без телеграм-бота необходимо удалить строки, отвечающие за запуск бота из main.py:
- 10-12
- 38

Для добавления бумаг в список торгового алгоритма необходимо отредактировать файл config/str1_status.txt по аналогии:
- figi - FIGI бумаги;
- status - True, чтобы алгоритм использовал бумагу в торговле.
- остальные параметры рекомендуется не изменять из-за оптимизации торговой стратегии под эти данные.  

При запуске программы алгоритм сразу анализирует выбранные акции без действий пользователя.

---

## Requirements

Для запуска торгового бота требуются следующий библиотеки, описанные в requirements.txt:

    aiogram
    tinkoff-investments
    ta
    pandas
    matplotlib
    python-dotenv
    aioschedule
    dataframe_image


---



## Торговая стратегия

При разработке данной стратегии было решено выбрать наиболее популярные и зарекомендовавшие себя индикаторы и составить набор правил, по которым будут совершаться покупки и продажи. В данной стратегии используется 4 индикатора:

- EMA с периодом 7 дней и EMA с периодом 21 день для определения точек входа и выхода из сделки;
- MACD для определения направления тренда;
- ADX для определения силы тренда.

Несмотря на то, что некоторые используемые индикаторы могут давать инвестору схожую информацию, было решено использовать их совместно для более точной работы стратегии.
    
Покупка будет осуществляться при выполнении всех условий сразу:
1. Линия быстрой скользящей средней (EMA 7) пересечёт снизу медленную скользящую среднюю (EMA 21) и будет находиться сверху – возрастающий тренд;
2. Уровень MACD будет находиться выше нуля – возрастающий тренд;
3. Уровень ADX будет больше 20 – цена не будет стоять на месте.

Продажа будет осуществлять при выполнении одного из условий:
1. Цена возросла достаточно (тейк-профит);
2. Цена опустилась слишком низко (стоп-лосс);
3. Линия быстрой скользящей средней (EMA 7) пересечёт сверху медленную скользящую среднюю (EMA 21) – начало убывающего тренда;
4. Консолидация MACD – уровень MACD стоит на одном месте долгое время, что может говорить о возможном развороте;
5. Резкое снижение уровня MACD – нисходящий тренд.

---

## Тестирование эффективности

Для тестирования эффективности было решено запустить разработанный алгоритм на исторических данных за последние 4 недели. 

Алгоритм совершает покупки и продажи аналогично с реальными условиями. Только вместо выставления ордеров алгоритм хранит данные о всех возможных операциях в памяти. Данные о теоретических покупках и продажах заносятся в таблицу для дальнейшего анализа. 

Такой подход позволяет оперативно настраивать алгоритм, корректировать его параметры и исправлять ошибки. 

Стоит отметить, что все вычисления производятся только на основе значений за прошедшие периоды времени, поэтому 
### Акции Yandex

Проведём тестовый запуск алгоритма для акций Yandex.

На графике зелёными полосками выделены "теоретические" покупки, а красными - "теоретические" продажи.

![Alt-текст](img/str1/test/graph/hour_BBG006L8G4H1.png "ЙОУ")

В итоге мы также получаем таблицу со всеми операциями. Можно заметить, что большинство операций были успешными - алгоритм покупает по низкой цене и продаёт по высокой.

Также можно заметить, что не все операции были успешными, так как алгоритму приходилось продавать по цене ниже той, что была указаан при покупке.

Тем не менее алгоритм показал положительный результат и теоретически пользователь получил бы прибыль в 100₽ за 4 недели (около 5%).

![Alt-текст](img/str1/test/total/hour_BBG006L8G4H1.png "ЙОУ")

### Акции Лукойл

Аналогичный тестовый запуск алгоритма был проведён для акций Лукойл.

![Alt-текст](img/str1/test/graph/hour_BBG004731032.png "ЙОУ")

Заметим, что теоретически пользователь также получит прибыль в 309₽ (около 6%).

![Alt-текст](img/str1/test/total/hour_BBG004731032.png "ЙОУ")

### Вывод

Алгоритм доказал свою эффективность при работе на исторических данных. Таким образом, данный алгоритм можно применять в реальных условиях. 

Другие примеры выполнения тестового алгоритма с различными ценными бумагами можно изучить в папке с тестовыми ррезультатами:
    
    /img/test/total - таблицы с операциями
    /img/test/graph - графики движения цены
    /img/test/ind   - таблицы с индикаторами

---


## Структура торгового робота

- bot/ - каталог с файлами телеграм-бота
  - handlers/ - обработчики сообщений
    - __init__.py
    - bot_handlers.py - основные обработчики
    - buy_handlers.py - обработчики покупки акций
    - info_handlers.py - обработчики получения информации
    - sell_handlers.py - обработчики продажи акций
    - strategy_handlers.py - обработчики торговых стратегий
  - keyboards/ - клавиатуры для бота
    - start_menu_keyboard.py - стартовая клавиатура
- config/ - файлы конфигураций
  - personal_data.py - получение информации (токен и тд)
  - str1_status.txt - данные о ценных бумагах и статусе стратегии
- img/ - каталог с изображениями
  - str1/ - стратегия 1
    - graph/ - графики 
    - ind/ - таблицы с индикаторами
    - test/ - тестовые алгоритмы
- pandas_style/ - стили для таблиц pandas
  - strategy_1.py
- trading/ - функции трейдинга
  - candles/ - функции получения свечей
    - add_indicators.py - добавление индикаторов
    - get_candles.py - получение свечей различных периодов
  - strategy/ - торговые стратегии
    - ema_adx_macd.py - первая торговая стратегия
  - get_by_figi.py - функции получения информации по FIGI
  - get_info.py - функции получения информации от брокера
  - place_order.py - функции размещения ордеров
  - trade_help.py - вспомогательные функции 
- main.py - запуск бота
- readme.md
- requirements.txt - необходимые библиотеки


## Контакты

**TG**: @mordvintsevmv

**e-mail**: mordvintsevmv@gmail.com

---

# Планы


## Работа - 2 дня

- [ ] Везде try except
- [ ] Проверка ввода всех данных


- [ ] Оптимизировать создание графика, если возможно


- [ ] Удаление стратегий при изменении токена
- [ ] Удаление всех стратегий по user_id


## Украшение - 1 день

- [ ] Украсить весь текст
- [ ] Написать файл Readme до конца

- [ ] Сделать пункт с помощью
- [ ] Сделать приветственный экран со ссылками
- [ ] Дополнить каждый пункт сопроводительным текстом

---

# Будущее

- [ ] Новая стратегия
- [ ] Новые индикаторы

---

# Готово

- [X] Запись всех сделок
- [X] Добавить "Лучшая цена"
- [X] Добавить удаление бумаги из стратегии
- [X] Добавить создание бумаги для стратегии
- [X] Вывод всех своих операций 
- [X] Отредактировать покупку/продажу
- [X] Поиск бумаг
- [X] Добавить редактирование стратегии по каждой бумаге
- [X] Шифровка токена и аккаунта
- [X] Исключить время для песочницы
- [X] Проверка на правильность токена
- [X] Добавить проверку на начало торгов / продажа
- [X] Добавить проверку на начало торгов / покупка
- [X] Добавить проверку на доступность денег / стратегия
- [X] Добавить проверку на начало торгов / стратегия
- [X] Добавить поле currency в str1
- [X] Проверить весь код
- [X] Упростить код
- [X] Проверка на токен чтения
- [X] Добавить проверку на доступность денег / покупка

