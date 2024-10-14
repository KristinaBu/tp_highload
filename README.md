# Telegram
## 1. Тема и целевая аудитория

### ЦА
* Число активных пользователей в месяц: 950 млн в месяц

* Возрастное распределение аудитории:

| Возраст | Процент |
|---------|----------|
| До 17   | 6.4%    |
| 18 - 24 | 18.8%   |
| 25 - 34 | 29.4%   |
| 35 - 44 | 23.8%   |
| Более 45| 21.6%   |

* Основные страны по количеству пользователей Telegram [^2] :
1. Индия - около 104 млн
2. Россия - около 34-35 млн
3. США - около 26-29 млн

### Требования к функционалу
- MVP
1. регистрация + авторизация
2. отправка сообщений
3. удаление сообщений
4. просмотр прошлых сообщений
5. создание канала пользователя
6. список чатов, каналов
7. поиск по списку

- Ключевой функционал: Передача сообщений между пользователями
щз
- Ключевые продуктовые решения:
1. Для мессенджера был создан протокол MTProto(написан на C++), предполагающий использование нескольких протоколов шифрования
2. Секретные чаты - режим реализует сквозное шифрование(пакет данных шифруется независимо от предыдущих пакетов) с применением алгоритма AES-256. Переписка шифруется на устройстве отправителя, а расшифровывается на устройстве собеседника. Нет никаких промежуточных остановок в виде сервера

## 2. Расчет нагрузки

### Продуктовые метрики

- MAU: 950 млн [^1] 
- DAU: 196 млн. [^4]

| Данные      | Вес             |
|-------------------|-----------------------|
| Аватар         |  500-700 КБ (512 х 512)        |
| Ник    | 32 байт    |
| Имя и Фамилия      | 128 байт   |
| Статус и описание     | 255 байт      |
| Сообщения (за год)     |  примерно 34 (33,3) сообщений отправляет 1 пользователь; 34*4 КБ = 136 Кб [^8]; 136 * 365 * 11 = 546 040 КБ |

Подсчитаем суммарно данные пользователя:  546 040 КБ + 600 КБ (в среднем аватарка) + 415 байт (данные пользователя)  = 0,55 ГБ (примерно)

Действия по MVP:

| Действие по MVP      | За год | Среднее число повторений за день | Рассчеты, если есть |
|-------------------|-----------------------|-----------------------|-----------------------|
| Регистрация  | 2.5 млн |   0,0127 на 1 пользователя в день [^7] | - |
| Авторизация  | 2 146,2 млрд |  0,21 на 1 пользователя в день  | DAU / MAU  |
| Отправка сообщений  | 142.5 мдрд | 34 [^8] |  число сообщений в день в 2016 = 15млрд [^5] ; 2016 MAU=100млн, 2024 MAU=950млн; (950/100)=9.5 раз увеличение аудитории. Число сообщений в 2024 = 9.5*15 = 142.5 мдрд  |
| Пуш-уведомления  | 97,6 млрд | 11 |  68,5% отключают уведомдения [^11] -> 10,71 сообщений приходят с пуш-уведомлениями. 142.5 млрд * 68,5% = 97,6 млрд пуш-уведомлений в год  |
| Создание каналов  |1 124 000 | 0,006 |  825 000 каналов в 2022 [^9], 700 млн MAU в 2022. Увеличение аудитории с 2022 по 2024: (950 / 700) ≈ 1.36 раза больше. Предполагаемое количество каналов: 825 000 * 1.36 = 1 124 000 каналов.  [^6] [^7] |
| Просмотр каналов  | 1 786 814 400 | 12.84 | [^7]  100543100 (28,9% читают до 5 каналов) + 261968700 (25,1% до 10 каналов) + 346160500(19,9% до 15 каналов) + 397997600(14,3% до 25 каналов) + 422698500(8,1% до 50 каналов) + 257446000(3,7% 50+ каналов) = 1 786 814 400 чтений каналов |

### Технические метрики

1. Хранилище.
   - Пользователи: 522 500 ТБ (считается, что есть только активные пользователи: 0.55 * 950 000 000 пользователей)
   - Каналы: 287 КБ информации + аватар канала ~600 КБ -> 887 КБ. Предполагаемое количество каналов: 1 124 000 каналов. Без сообщений = 0.997 ТБ. 136 КБ * 365 * 9 (каналы появились в 2015) = 446 760 КБ( для одного канала), 613 748 960 000 КБ = 502.16 ТБ + 0.997 ТБ = 503,16 ТБ
2. Сетевой трафик.

Пиковое значение:
   - Регистрация: 0,0127  * 196 млн * (600 КБ + 415 КБ)/86 400 = 0.03  ГБ/c = 0.24 Гбит/c
   - Отправка сообщения: 34 * 196 млн * (136 КБ)/86 400 = 10.5  ГБ/c = 84 Гбит/c
   - Создание канала: 0,006 * 196 млн * (887 КБ)/86 400 = 1043.112  ГБ/c = 8 344.896 Гбит/c

 Суточное значение:
  - Регистрация: 2 073.6 Гбит/c
  - Отправка сообщения: 7 257 600 Гбит/c = 7.26 Пбит/с
  - Создание канала: 720 999 360 Гбит/c = 721 Пбит/с
  
  Значение RPS:   
  Пиковая аудитория: 80 ед / 24 (часов в сутках) = 3.333 ед; 3.333 = DAU; Пик = 7.5 ед, то есть примерно в 2 раза больше DAU. [^10]  
  
  | Запрос      | RPS             | RPS в пике            |
  |---------------|-----------------|-----------------|
  | Регистрация         | 28,8  | 57,6  |
  | Авторизация         | 476,4  | 953  |
  | Отправка сообщения | 77 129   | 154 258  | 
  | Пуш-уведомления | 24 953,7  | 49 907 | 
  | Создание канала | 13,6      | 27,2     |    

  Считается, что telegram сам отправляет пуш-уведомления без посредников (например, Apple Push Notification).

## 3. Глобальная балансировка нагрузки

### Функциональное разбиение по доменам (при наличии, нужно для следующего пункта)

1. auth.telegram.com   
   Этот домен будет отвечать за регистрацию и авторизацию пользователей. Он будет содержать API для обработки запросов на регистрацию, вход в систему и управление аккаунтами.   
2. chat.telegram.com   
   Домен для работы с чатами и каналами. Здесь будут обрабатываться запросы на отправку, удаление и просмотр сообщений, создание каналов, управление списком чатов и каналов, а также поиск по ним.
3. media.telegram.com   
   Домен отвечает за хранение и доставку медиа-контента. В качестве него выступают аватарки на канал или для пользователя.

Схема работы:   
- Пользователь вводит в адресной строке браузера адрес telegram.com.  
- Браузер перенаправляет пользователя на домен auth.telegram.com для авторизации или регистрации.  
- После успешной авторизации пользователь перенаправляется на домен chat.telegram.com для работы с чатами и каналами. Все последующие взаимодействия пользователя с чатами и каналами будут происходить через домен chat.telegram.com.
- При загрузке аватарки для пользователя или для канала запрос отправляется на media.telegram.com. Удаление и другие действия с аватаркой также связаны с этим доменом.


### Обоснования расположения ДЦ (влияние на продуктовые метрики)

![image](https://github.com/user-attachments/assets/5179524d-4b13-42ae-9fb1-1a6b5a910845)

| Страна               | Количество пользователей (млн) | Подходящие города для ДЦ |
|----------------------|---------------------------|------------|
| Индия                | 104.04                    | Мумбаи, Ченнай |
| Россия + Казахстан   | 34.40 + 7.58                     | Екатеринбург, Ярославль   |
| Индонезия + Филиппины + Малайзия | 27.21 + 9.57 + 6.37 | Джакарта, Сингапур |
| США                  | 26.85                     | Майами, Лос-Анджелес, Анкоридж |
| Бразилия             | 21.94                     | Форталеза |
| Египет               | 14.85                     | Александрия |
| Вьетнам              | 11.84                     | Дананг   |
| Мексика              | 11.61                     | Канкун    |
| Украина              | 10.76                     | Одесса      |
| Турция               | 9.79                      | Стамбул  |
| Нигерия              | 8.47                      | Лагос     |
| Франция + Италия + Испания | 6.52 + 4.73 + 5.57  | Марсель    |
| Колумбия             | 6.13                      | Картахена    |
| Саудовская Аравия    | 5.88                      | Джедда  |
| Германия             | 5.04                      | Нюрнберг    |


Некоторые страны были объединены по географическому признаку. Также для стран с большим числом пользователей взято больше городов, чтобы охватить максимальную площадь. Города выбраны в соответствии с совпадением точек, где пролегают подводные каналы интернет-связи и где находятся точки обмена интернет-трафиком [^2] [^12] .

### Расчет распределение запросов из секции "Расчет нагрузки" по типам запросов по датацентрам

| ДЦ | % от общего числа пользователей | Регистрация (RPS / пик RPS) | Отправка сообщений (RPS / пик RPS) | Пуш-уведомления (RPS / пик RPS) | Создание канала (RPS / пик RPS) |
|--------------------------|--------------------------|-----------------------------|----------------------------------|--------------------------------|--------------------------------|
| Мумбаи, Ченнай           | 31.24%                   | 8.99 / 17.99               | 24073.71 / 47947.42             | 7790.69 / 15581.38             | 4.24 / 8.49                     |
| Екатеринбург, Ярославль   | 12.53%                   | 3.60 / 7.19                | 9651.45 / 19302.90             | 3126.26 / 6252.52              | 1.72 / 3.43                     |
| Джакарта, Сингапур      | 11.34%                   | 3.24 / 6.48               | 8842.56 / 17685.12             | 2827.24 / 5654.48              | 1.54 / 3.07                     |
| Майами, Лос-Анджелес, Анкоридж | 9.53%                    | 2.72 / 5.44               | 7415.27 / 14830.54             | 2334.98 / 4669.96              | 1.25 / 2.51                     |
| Форталеза                | 7.83%                    | 2.24 / 4.48               | 6153.61 / 12307.22             | 1940.67 / 3881.34              | 1.04 / 2.08                     |
| Александрия              | 4.99%                    | 1.42 / 2.84               | 3815.49 / 7630.98              | 1200.48 / 2400.96              | 0.65 / 1.29                     |
| Дананг                   | 3.36%                    | 0.96 / 1.92               | 2653.54 / 5307.08               | 836.11 / 1672.22               | 0.45 / 0.90                     |
| Канкун                   | 3.32%                    | 0.94 / 1.88               | 2619.99 / 5239.98               | 825.36 / 1650.72               | 0.44 / 0.88                     |
| Одесса                  | 3.08%                    | 0.88 / 1.76               | 2437.03 / 4874.06               | 768.72 / 1537.44               | 0.41 / 0.82                     |
| Стамбул                 | 2.81%                    | 0.80 / 1.60               | 2216.84 / 4433.68               | 700.40 / 1400.80               | 0.38 / 0.75                     |
| Лагос                   | 2.42%                    | 0.69 / 1.38               | 1899.18 / 3798.36               | 598.29 / 1196.58               | 0.32 / 0.64                     |
| Марсель                  | 1.89%                    | 0.54 / 1.08               | 1487.45 / 2974.90               | 468.54 / 937.08                | 0.25 / 0.50                     |
| Картахена                | 1.75%                    | 0.50 / 1.00               | 1382.85 / 2765.70               | 436.07 / 872.14                | 0.23 / 0.47                     |
| Джедда                  | 1.69%                    | 0.48 / 0.96               | 1334.44 / 2668.88               | 420.24 / 840.48                | 0.22 / 0.45                     |
| Нюрнберг                | 1.44%                    | 0.41 / 0.82               | 1131.46 / 2262.92               | 356.44 / 712.88                 | 0.19 / 0.38                     |


### Схема DNS балансировки (при наличии)

Больше всего для Telegram подходят  Geo-based DNS и Latency-based DNS использует время отклика для оптимизации маршрутизации DNS-запросов). С одной стороны пользователи Telegram расположены по всему миру, и Geo-based DNS может лучше распределить запросы, используя географическое положение пользователя. С другой стороны, при отправке сообщений или важных постов в канал необходимо обеспечить скорость засчет как ожно меньшего latency, и здесь больше подойдет Latency-based DNS.  
   
Итог:  Latency-based DNS. Так как серверы периодически обмениваются информацией о своих временах отклика, эта схема балансировки адаптивна к динамическим изменениям производительности серверов.

## 4. Локальная балансировка

### Схема балансировки для входящих и межсервисных запросов

Рассмотрим несколько балансировок [^13]:

- DNS-балансировка: имеет низкую нагрузку на сеть, но не подходит из-за высокой вероятности длительных соединений и необходимости быстрой реакции на изменения в инфраструктуре
- L4-балансировка: может использоваться для базовой балансировки, но не подходит для сложных сценариев, так как имеет ограниченные возможности по управлению сессиями, также нет поддержки сложных протоколов
- L7-балансировка: наиболее подходящий вариант для Telegram-сервисов. Позволяет эффективно распределять нагрузку, управлять сессиями пользователей, оптимизировать трафик и реализовать дополнительные функциональные возможности, однако из минусов есть высокая сложность настройки и потенциально более низкая производительность.

Вывод: L7-балансировка. 
При управлении сессиями Telegram использует долгоживущие соединения. L7-балансировщик позволяет привязывать пользователя к определенному серверу, обеспечивая более стабильное соединение и упрощая обработку контекста пользователя. Также часто используемые данные могут быть кэшированы на уровне балансировщика, что снижает нагрузку на бэкенд-серверы и ускоряет ответ. К тому же L7 балансировщики хорошо интегрируются с Kubernetes.

В итоге получается такая примерная схема работы:

Клиент -> Latency-based DNS -> CDN/Nginx + Service Discovery + Kubernetes [^14]

1. Клиент: отправляет запросы
2. Latency-based DNS: При первом обращении клиент получает IP-адрес наименьшего по времени отклика сервера Telegram.
3. CDN: используется для кэширования(аватарок, так как они постоянно запрашиваются);
4. Nginx: работает как L7 балансировщик и прокси-сервер, то есть распределяет запросы между различными сервисами Telegram.
5. Service Discovery поможет Nginx определить доступные сервисы. При добавлении или удалении реплик сервиса, Service Discovery автоматически обновляет информацию о доступных адресах. Nginx, получая новые данные, может перенаправлять трафик на новые экземпляры сервиса. При увеличении нагрузки на сервис можно легко добавить новые реплики, и Service Discovery автоматически обнаружит их и сделает доступными для клиентов.
6. Kubernetes-кластер содержит контейнеризованные версии различных сервисов Telegram. К тому же Kubernetes предоставляет встроенный механизм Service Discovery, который позволяет сервисам находить друг друга без необходимости знать их точные IP-адреса.

*Интеграция с MTProto*   
Для того, чтобы интегрировать кастомный протокол MTProto, создается отдельный сервер, который будет обрабатывать все MTProto запросы. Он будет интегрирован в Kubernetes-кластер и будет работать как прокси между клиентом и основными Telegram-сервисами (при специальной настройке Ingress, который описывает правила проксирования трафика от внешнего источника до сервисов внутри кластера K8S, для маршрутизации трафика на нужный сервис). Также отдельный порт Nginx может быть выделен для приема MTProto-трафика.

### Схема отказоустойчивости

1. Kubernetes при отказе одного пода (наименьшая единица развертывания) или сервера автоматически запускает новый экземпляр. Так что если, например, сервер обработки сообщений выходит из строя, Kubernetes создаст новый под для этого сервиса. В Kubernetes есть репликация - механизм, который гарантирует, что определенное количество подов всегда будет запущено, поэтому у сервисов может быть несколько реплик, чтобы обеспечить высокую доступность даже при отказе одной из них.
2. Для критически важных сервисов можно использовать StatefulSets, который гарантирует сохранение данных при перезапуске.
3. Использование кластеров: многозонные кластеры (распределение узлов по разным зонах доступности, зоны легко добавляются), Active-Active кластеры (все узлы могут обрабатывать запросы одновременно и если один узел выйдет из строя, остальные продолжат работу).
4. Kubernetes использует Service Discovery для определения доступных сервисов и при отказе одного сервера другие продолжают работать, а клиент автоматически направляет запросы на работающие.
5. Nginx распределяет нагрузку между оставшимися серверами, чтобы снизить влияние отказа одного из них на пользователя.
6. Для мониторинга можно использовать Grafana.

### SSL

Session tickets - для более быстрой повторной аутентификации клиентов:
- При первом подключении клиента к Telegram-серверу происходит обычное TLS-рукопожатие. Сервер генерирует session ticket и отправляет его клиенту.
- При последующих подключениях клиент может предоставить этот session ticket серверу. Сервер использует его для восстановления параметров предыдущего соединения.

Таким образом меньшее количество полных рукопожатий TLS снижает нагрузку на сервер. Хранение session ticket будет происходить локально (в куках, например).

## 5. Логическая схема БД

### Схема БД

![image](https://github.com/user-attachments/assets/3ab1111c-0818-4744-add4-eb8f864ff566)

Примечание: отметка прочтения работает только для чатов.

### Таблицы:
| Таблица | Описание | Размер (байты) |
|---------|----------|----------------|
| User    | Хранит данные пользователей | 520 |
| Chat    | Таблица чатов. В чате могут быть только 2 пользователя | 105 |
| Channel | Таблича данных о канале | 528 |
| Message | Хранит данные о сообщениях пользователей в зашифрованном виде | 3232 |
| Channel_subscriptions | Таблица, реализующая отношение М:М | 64 |
| Encrypton_key | Хранит ключи шифрования сообщений | 304 |
| Media    | Хранит аватарки пользователей и каналов | 1064 |
| Session  | Хранит сессии | - |

Таблица User содержит следующие поля: 
- id: 32 байта
- username: 64 байта  
- first_name: 64 байта
- last_name: 64 байта
- password_hash: 256 байт
- description: 256 байта
- media_id: 32 байта
- created_at: 8 байт
- updated_at: 8 байт

Таблица Chat содержит следующие поля:
- id: 32 байта
- user1_id: 32 байта
- user2_id: 32 байта
- created_at: 8 байт
- is_secret: 1 байт
- last_message_id: 32 байта

Таблица Channel содержит следующие поля: 
- id: 32 байта
- name: 64 байта
- description: 256 байта
- admin_id: 32 байта
- status: 64 байта
- media_id: 32 байта
- last_message_id: 32 байта
- created_at: 8 байта

Таблица Message содержит следующие поля: 
- id: 32 байта
- chat_id: 32 байта
- channel_id: 32 байта
- sender_id: 32 байта
- encrypted_data: 2064 байт
- status: 64 байта
- created_at: 8 байта
- updated_at: 8 байта
- encryption_key_id: 32 байта
- ttl: 64 байта

Таблица Channel_subscriptions содержит следующие поля:
- channel_id: 32 байта
- user_id: 32 байта

Таблица Encrypton_key содержит следующие поля: 
- id: 32 байта
- public_key: 256 байт
- created_at: 8 байта
- updated_at: 8 байта

Таблица Media содержит следующие поля:
- id: 32 байта
- media_url: 1024 байта
- created_at: 8 байта


### Расчет [^15]:

encrypted_data:   
![image](https://github.com/user-attachments/assets/57764212-a77c-4a66-aa1d-bcc23634550a)

- Шифрование: 512 = 64 байт 
- Начальная длина сообщения - 2000 символов
Итого: 2064

Остальное: 
- Пользователи: 950 000 000, 494 ГБ  
- Сообщения: 142.5 мдрд, 460,6 ТБ  
- Каналы: 1 124 000: 0,59 ГБ  
- Группы: 170 000: 0,01785 ГБ  
- М-М: 5 (статистика чтения каналов) * 32 байта * 950 000 000 ≈ 15 ТБ  
- Медиа: 23 013 750, 11,5 ТБ  
- Encryption_key:   
   Количество ключей: 142.5 * 365 * 11 ≈ 5.7 миллиарда, 5.7 * 304 байта ≈ 1.74 ТБ

Итого ≈ 490 ТБ

### Требования консистентности:

- Уникальность ключей: Все поля, являющиеся ключами в таблицах (id, username, media_id и т.д.), должны быть уникальными.
- Синхронизация при изменении каналов: При создании или удалении каналов, необходимо синхронизировать данные во всех связанных таблицах (Channel, Channel_subscriptions, Message).
- Каждое сообщение должно иметь свой уникальный ключ шифрования.
- При создании нового сообщения, использовать актуальный ключ из таблицы Encrypton_key.
- Обновлять ключи шифрования периодически для обеспечения безопасности.
- При обновлении данных, всегда устанавливать новое значение updated_at.
- Если сообщение относится к каналу, то id чата null и наоборот.
- В чате должны быть заполнены оба user_id.
- Триггеры для согласованности: при создании новой записи в таблице, где есть created_at - должен срабатывать триггер для согласованности.

### Нагрука по ключам

Cамая большая нагрузка на Message (и Encrypton_key соответственно). Message - нагрузка в сложности запросов и в большом их количестве.
Для снижения нагрузки было добавлено поле *last_message_id* в таблицы к чатам и каналам, так как при открытии страницы каналов/чатов часто запрашивается последнее сообщение.
Кроме того, на втором месте по нагрузке каналы, поэтому можно сделать индексы для быстрого доступа к данным:  
  
Message:
- индекс по полю chat_id для быстрого поиска сообщений в конкретном чате
- индекс по полю created_at для поиска сообщений по временным интервалам

Channel_subscriptions:
- составной индекс по полям channel_id и user_id для эффективного поиска подписчиков канала и каналов, на которые подписан пользователь
- индексируем название канала 




## 6. Физическая схема БД

### Схема

![image](https://github.com/user-attachments/assets/02c2d100-e17a-491e-a75d-c6058116eafb)


| Таблица | Описание | 
|---------|----------|
| User    | Хранит данные пользователей | 
| Chat    | Таблица чатов. В чате могут быть только 2 пользователя |
| Channel | Таблича данных о канале | 
| Message | Хранит данные о сообщениях пользователей в зашифрованном виде |
| Channel_subscriptions | Таблица, реализующая отношение М:М |
| Encrypton_key | Хранит ключи шифрования сообщений | 
| Media    | Хранит аватарки пользователей и каналов |
| Session  | Хранит сессии | 

### **Индексы**

- поле username, media_id таблицы User
- поле name, media_id таблицы Channel
- поля created_at, channel_id, chat_id таблицы Message

### **Денормализация**

- last_message_id в таблицах Chat и Channel: хранение предварительно вычисленных значений для оптимизации запросов. Интерфейс Телеграма требует частых запросов последнего сообщения, чтобы отображать его пользователям, поэтому хранение last_message_id снизит нагрузку на БД.   
- media_id в таблицах User и Channel: позволяет избежать многократных запросов к таблице Media при получении информации о пользователе/канале.


### **Выбор СУБД (потаблично)**

Потребности: 
- Высокая нагрузка на запись: Огромное количество сообщений, регистраций и создания каналов.
- Большой объем данных: Несколько терабайт данных за короткий промежуток времени.
- Время отклика: Необходимо обеспечить быструю авторизацию и доставку сообщений.
- Сложная структура данных: Связи многие-ко-многим, шифрование, хранение медиаданных.
- Масштабируемость: Система должна быть готова к дальнейшему росту нагрузки.

Выбор БД:
- Cassandra обеспечивает высокую производительность записи и горизонтальное масштабирование, что идеально подходит для хранения большого объема сообщений и пользовательских данных.
- Redis обеспечивает быстрый доступ к часто используемым данным.
- PostgreSQL обеспечивают надежное хранение ключей шифрования и возможность выполнения сложных аналитических запросов.

| Таблица | СУБД | Обоснование |
|---------|----------|----------------|
| User    | Cassandra | Высокая нагрузка на запись, масштабируемость, возможность хранения большого количества данных |
| Chat    | Cassandra | Связана с таблицей User, требует высокой производительности записи |
| Channel | Cassandra | Аналогично таблице User |
| Message | Cassandra | Основной объем данных, высокая нагрузка на запись |
| Channel_subscriptions | Cassandra | Связана с таблицами User и Channel |
| Encrypton_key | PostgreSQL | Требуется высокая согласованность данных и возможность сложных запросов для управления ключами |
| Media    | Cassandra + S3 | Может храниться в виде ссылок на объекты в хранилище объектов (например, S3), а метаданные - в Cassandra |
| Session  | Redis | Высокая скорость доступа, кэширование сессий для улучшения производительности |



### **Шардирование и резервирование СУБД (потаблично)**

Выбор стратегии:
- Шардирование
  - Шардирование по ключу - для таблиц с равномерным распределением данных по ключу.
  - Шардирование по диапазону - для таблиц с упорядоченным по диапазону ключом (например, таблица сообщений по времени создания).
- Резервирование
  - Полное резервное копирование.
  - Инкрементальное резервное копирование - меньший объем данных для резервного копирования, более быстрое восстановление. То есть для частых изменений данных
  - Репликация - высокая доступность данных.  

| Таблица | Шардирование | Резервирование |Обоснование |
|---------|----------|----------------|----------------|
| User    | По ключу (user_id) | Инкрементальное резервное копирование | Равномерное распределение нагрузки, частые изменения данных |
| Chat    | По ключу (chat_id) |  Инкрементальное резервное копирование | Связана с таблицей User, аналогичные требования |
| Channel | По ключу (channel_id) |  Инкрементальное резервное копирование | Связана с таблицей User, аналогичные требования |
| Message | По диапазону (время создания) | Инкрементальное резервное копирование + репликация | Высокая скорость записи, необходимость быстрого доступа к последним сообщениям |
| Channel_subscriptions | По ключу (user_id) |Инкрементальное | Связана с таблицей User |
| Encrypton_key | Не требуется шардирование | Полное резервное копирование | Высокая важность данных |
| Media    | Не хранить в базе данных, использовать внешнее хранилище (S3) | Резервирование на уровне хранилища объектов | Большой объем данных, специфические требования к хранению |
| Session  | - | - | Не требуется шардирование и резервирование, данные кешируются и периодически обновляются |

### Клиентские библиотеки / интеграции

- Для Cassandra:
  - Java: DataStax Java Driver - драйвер, предоставляющий широкий набор функций для работы с Cassandra.
  - Python: DataStax Python Driver
  - Go: DataStax Go Driver 
- Для PostgreSQL:
  - Java: JDBC драйвер PostgreSQL 
  - Python: psycopg2 
  - Go: pq 
- Для Redis:
  - Java: Jedis
  - Python: redis-py 
  - Go: go-redis
- Для S3:
  - Java: AWS SDK for Java 
  - Python: Boto3 
  - Go: AWS SDK for Go 

### Балансировка запросов / мультиплексирование подключений

Технология HTTP/2: Позволяет устанавливать одно долгоживущее соединение и передавать по нему множество запросов, что снижает задержки и повышает производительность. Хотя HTTP/3 предлагает некоторые преимущества, HTTP/2 является более подходящим выбором. Оно обеспечивает хорошую производительность, широкую поддержку и простоту реализации, что критично для масштабируемого и надежного чата.   

Веб-сокеты: Обеспечивают двустороннюю связь между клиентом и сервером в реальном времени, что идеально подходит для чатов и уведомлений

### Резервное копирование

1. Резервное копирование базы данных

*Cassandra*    
Snapshot: моментальный снимок состояния всего кластера в конкретный момент времени. Все данные, включая копии таблиц, индексы и т.д., сохраняются в виде файла.      
Incremental backups: сохраняются только изменения, произошедшие с момента последнего снимка или инкрементального бэкапа (экономия объема данных)

*PostgreSQL*    
pg_dump: утилита командной строки, позволяющая создавать полные или инкрементальные дампы базы данных. Полный дамп содержит все объекты базы данных, инкрементальный содержит только изменения с момента последнего дампа.   

*Redis*     
BGSAVE: фоновый процесс для сохранения текущего набора данных в файл - обслуживание запросов во время создания резервной копии.   
AOF: Использовать журнал изменений (Append Only File)в который записываются все операции, выполняемые с данными. При восстановлении данные восстанавливаются из AOF.

2. Резервное копирование S3

Версии объектов: AWS S3 может создавать несколько версий одного и того же объекта. Каждая версия сохраняется независимо, так что можно легко восстановить предыдущие версии данных при необходимости.     
Lifecycle policies: настройка политики жизненного цикла для автоматического удаления старых версий.

Источники:
[^1]:  https://www.demandsage.com/telegram-statistics/
[^2]:  https://worldpopulationreview.com/country-rankings/telegram-users-by-country
[^3]:  https://tlgrm.ru/docs/mtproto
[^4]:  https://backlinko.com/telegram-users#telegram-daily-active-users
[^5]:  https://www.businessofapps.com/data/telegram-statistics/
[^6]:  https://tgstat.com/
[^7]:  https://inclient.ru/telegram-stats/#telegram11  
[^8]:  https://backlinko.com/whatsapp-users#number-of-whats-app-messages-sent-globally-per-day 
[^9]:  https://usesignhouse.com/blog/telegram-stats/#how-many-channels-are-there-on-telegram 
[^10]: https://semantica-media.ru/blog/luchshee-vremya-dlya-postov-kogda-luchshe-vykladyvat-kontent-v-soczsetyah.html
[^11]: https://elama.ru/blog/kak-auditoriya-ispolzuet-telegram-na-rubezhe-20222023-godov-rezultaty-issledovaniya/
[^12]: https://www2.telegeography.com/
[^13]: https://habr.com/ru/companies/vk/articles/347026/ 
[^14]: https://habr.com/ru/companies/flant/articles/583660/
[^15]: https://tgstat.com/
