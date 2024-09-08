# Telegram
## 1. Тема и целевая аудитория

### ЦА
* Число активных пользователей в месяц:
![image](https://github.com/user-attachments/assets/b12d887a-274a-4b6a-a47a-8adbabdd24ac)
* Возрастное распределение аудитории:

| Возраст | Процент |
|---------|----------|
| До 17   | 6.4%    |
| 18 - 24 | 18.8%   |
| 25 - 34 | 29.4%   |
| 35 - 44 | 23.8%   |
| Более 45| 21.6%   |

* Основные страны по количеству пользователей Telegram:
1. Индия - около 104 млн
2. Россия - около 34-35 млн
3. США - около 26-29 млн

### Требования к функционалу
- MVP
1. регистрация
2. отправка сообщений
3. удаление сообщений
4. просмотр прошлых сообщений
5. создание канала пользователя
6. список чатов, каналов
7. поиск по списку

- Ключевой функционал: Передача сообщений между пользователями

- Ключевые продуктовые решения:
1. Для мессенджера был создан протокол MTProto(написан на C++), предполагающий использование нескольких протоколов шифрования
2. Секретные чаты - режим реализует сквозное шифрование(пакет данных шифруется независимо от предыдущих пакетов) с применением алгоритма AES-256. Переписка шифруется на устройстве отправителя, а расшифровывается на устройстве собеседника. Нет никаких промежуточных остановок в виде сервера

Источники:
1. https://www.demandsage.com/telegram-statistics/
2. https://worldpopulationreview.com/country-rankings/telegram-users-by-country
3. https://tlgrm.ru/docs/mtproto
