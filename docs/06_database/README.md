# 8 Требования к данным интеграции

## 8.1 Физическая модель
Оригинал модели представлен в [файле](/docs/technical-design/database/ER-model.drawio)
![](/images/diagrams/database/ER-model.drawio.png)

## 8.2 Словарь данных

| Элемент данных | Описание | Тип данных | Длина | Значение |
| ----- | ----- | ----- | ----- | ----- |
| **User (Пользователь)** | физическое лицо, достигшее 18 лет, имеющее доступ к Платформе посредством сети Интернет и размещающее предложения о Сборе через создаваемые События | PK: user\_id bitrix\_id first\_name last\_name phone birth\_date status created\_at registration\_confirmed\_at updated\_at |  |  |
| user\_id | Уникальный идентификатор пользователя | UUID | 36 |  |
| bitrix\_id | Идентификатор пользователя в системе Битрикс | VARCHAR | 50 |  |
| first\_name | Имя пользователя | VARCHAR | 100 |  |
| last\_name | Фамилия пользователя | VARCHAR | 100 |  |
| phone | Номер телефона | VARCHAR | 20 |  |
| birth\_date | Дата рождения пользователя | DATE | 10 | Не может быть раньше текущей даты минус 18 лет |
| status | Статус аккаунта | ENUM |  | active inactive deleted |
| created\_at | Дата и время создания аккаунта | TIMESTAMP | 19 | Не может быть раньше текущей даты |
| registration\_confirmed\_at | Дата подтверждения email | TIMESTAMP | 19 | Не может быть раньше created\_at |
| updated\_at | Дата и время последнего обновления | TIMESTAMP | 19 | Не может быть раньше текущей даты |
| **Donor (Благотворитель)** | физическое лицо, которое может отправлять платежи через Платформу на выбранные им События | PK: donor\_id FK: user\_id FK: email\_id first\_name last\_name is\_registered\_user total\_donations donations\_count first\_donation\_at has\_subscription status created\_at updated\_at |  |  |
| donor\_id | Уникальный идентификатор Благотворителя | UUID | 36 |  |
| email\_id | E-mail Благотворителя | UUID | 36 | Должно содержать символ @ |
| user\_id | Ссылка на User (если Благотворитель зарегистрирован) | UUID | 36 |  |
| first\_name | Имя Благотворителя | VARCHAR | 100 |  |
| last\_name | Фамилия Благотворителя | VARCHAR | 100 |  |
| is\_registered\_user | Является ли зарегистрированным пользователем | BOOLEAN | 1 |  |
| total\_donations | Общая сумма всех пожертвований (в рублях) | INTEGER | 10 |  |
| donations\_count | Количество совершённых пожертвований | INTEGER | 10 |  |
| first\_donation\_at | Дата первого пожертвования | TIMESTAMP | 19 |  |
| has\_subscription | Есть ли активная рекуррентная подписка | VARCHAR | 1 |  |
| status | Статус | ENUM | \- | active inactive blocked |
| created\_at | Дата первого пожертвования | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **Event (Событие/Сбор)** | сбор средств или ресурсов для организации или проекта в рамках благотворительных мероприятий на Платформе посредством использования Событий | PK: event\_id FK: user\_id event\_name event\_description target\_amount current\_amount currency status event\_date\_from event\_date\_to images\_count donors\_count created\_at moderation\_submitted\_at published\_at rejected\_at closed\_at rejection\_reason |  |  |
| event\_id | Уникальный идентификатор события | UUID | 36 |  |
| user\_id | Ссылка на создателя События | UUID | 36 |  |
| event\_name | Название События | VARCHAR | 255 |  |
| event\_description | Описание События | VARCHAR | 2000 | Нужно уточнить длину описания |
| target\_amount | Целевая сумма Сбора | INTEGER | 10 |  |
| current\_amount | Текущая собранная сумма Сбора | INTEGER | 10 |  |
| currency | Валюта (RUB) | VARCHAR | 3 |  |
| status | Статус события | ENUM | \- | pending\_moderation published rejected closed |
| event\_date\_from | Дата начала События | DATE | 10 |  |
| event\_date\_to | Дата завершения События | DATE | 10 |  |
| images\_count | Количество изображений в Событии | INTEGER | 5 |  |
| donors\_count | Количество уникальных Благотворителей | INTEGER | 10 |  |
| created\_at | Дата создания События | TIMESTAMP | 19 |  |
| published\_at | Дата публикации | TIMESTAMP | 19 |  |
| rejected\_at | Дата отклонения | TIMESTAMP | 19 |  |
| closed\_at | Дата закрытия События | TIMESTAMP | 19 |  |
| rejection\_reason | Причина (вердикт) отклонения События при модерации | VARCHAR | 1000 |  |
| **Donation (Пожертвование)** | дарение денежных средств Благотворителем через Платформу посредством выбранного События | PK: donation\_id FK: payment\_id FK: event\_id FK: donor\_id amount currency donation\_type is\_first\_donation is\_large\_donation status |  |  |
| donation\_id | Уникальный идентификатор пожертвования | UUID | 36 |  |
| payment\_id | Ссылка на платёж | UUID | 36 |  |
| event\_id | Ссылка на События | UUID | 36 |  |
| donor\_id | Ссылка на Благотворителя | UUID | 36 |  |
| amount | Сумма пожертвования | INTEGER | 10 |  |
| currency | Валюта | VARCHAR | 3 |  |
| donation\_type | Тип пожертвования | ENUM | \- | single subscription\_charge |
| is\_first\_donation | Является ли пожертвование первым на Платформе? | BOOLEAN | 1 |  |
| is\_large\_donation | Сумма пожертвования \>= 50000 рублей? | BOOLEAN | 1 |  |
| status | Статус пожертвования | ENUM | \- | pending completed failed |
| created\_at | Дата создания записи о пожертвовании | TIMESTAMP | 19 |  |
| confirmed\_at | Дата подтверждения платежа | TIMESTAMP | 19 |  |
| **Payment (Платёж)** | Единоразовый платёж при внесении денежных средств в ходе Сбора | PK: payment\_id cloudpayments\_transaction\_id idempotency\_key amount currency payment\_method status error\_code error\_reason retry\_count max\_retries next\_retry\_at created\_at completed\_at updated\_at |  |  |
| payment\_id | Уникальный идентификатор платежа | UUID | 36 |  |
| cloudpayments\_transaction\_id | Идентификатор транзакции в CloudPayments	 | VARCHAR | 50 |  |
| idempotency\_key | Ключ идемпотентности | UUID | 36 |  |
| amount | Сумма платежа | INTEGER | 10 |  |
| currency | Валюта (RUB) | VARCHAR | 3 |  |
| payment\_method | Способ платежа	 | ENUM | \- | card bank\_transfer |
| status | Статус платежа | ENUM | \- | pending completed declined failed |
| error\_code | Код ошибки от CloudPayments | VARCHAR | 50 |  |
| error\_reason | Описание причины ошибки | VARCHAR | 500 |  |
| retry\_count | Количество попыток повтора | INTEGER | 1 |  |
| max\_retries | Максимальное количество повторов | INTEGER | 1 |  |
| next\_retry\_at | Дата и время следующей попытки | TIMESTAMP | 19 |  |
| created\_at | Дата создания платежа	 | TIMESTAMP | 19 |  |
| completed\_at | Дата завершения платежа | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **Subscription  (Рекуррентный платёж/подписка)** | возможность выполнять регулярные списания денег с банковской карты покупателя без повторного ввода реквизитов карты и без участия благотворителя для инициации очередного платежа | PK: subscription\_id FK: donor\_id FK: event\_id cloudpayments\_subscription\_id amount currency frequency status failure\_count start\_date next\_charge\_date last\_charge\_date total\_charged charges\_count annual\_anniversary\_date created\_at cancelled\_at updated\_at |  |  |
| subscription\_id | Уникальный идентификатор подписки | UUID | 36 |  |
| donor\_id | Ссылка на Благотворителя | UUID | 36 |  |
| event\_id | Ссылка на События | UUID | 36 |  |
| cloudpayments\_subscription\_id | Идентификатор подписки в CloudPayments | VARCHAR | 50 |  |
| amount | Сумма пожертвования | INTEGER | 10 |  |
| currency | Валюта | VARCHAR | 3 |  |
| frequency | Периодичность | ENUM | \- |  |
| status | Статус подписки | ENUM | \- | active paused cancelled |
| failure\_count | Количество неудачных попыток списания | INTEGER | 5 |  |
| start\_date | Дата первого списания | DATE | 10 |  |
| next\_charge\_date | Дата следующего планируемого списания | DATE | 10 |  |
| last\_charge\_date | Дата последнего успешного списания  | DATE | 10 |  |
| total\_charged | Общая сумма по подписке | INTEGER | 10 |  |
| charges\_count | Количество успешных списаний | INTEGER | 10 |  |
| annual\_anniversary\_date | Дата годовщины подписки | DATE | 10 |  |
| created\_at | Дата создания подписки | TIMESTAMP | 19 |  |
| cancelled\_at | Дата отмены подписки | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **SubscriptionCharge  (Списание по подписке)** |  | PK: subscription\_charge\_id FK: payment\_id FK: subscription\_id charge\_number amount status scheduled\_at attempted\_at completed\_at error\_reason attempt\_number created\_at updated\_at |  |  |
| subscription\_charge\_id | Уникальный идентификатор попытки | UUID | 36 |  |
| payment\_id | Ссылка на платёж | UUID | 36 |  |
| subscription\_id | Ссылка на подписку | UUID | 36 |  |
| charge\_number | Порядковый номер списания | INTEGER | 5 | 1,2,3,...12, … |
| amount | Сумма списания | INTEGER | 10 |  |
| status | Статус | ENUM | \- | pending completed failed |
| scheduled\_at | Дата, на которую было запланировано списание | DATE | 10 |  |
| attempted\_at | Дата и время первой попытки | TIMESTAMP | 19 |  |
| completed\_at | Дата и время завершения | TIMESTAMP | 19 |  |
| error\_reason | Причина ошибки | VARCHAR | 500 | если status \= failed |
| attempt\_number | Номер текущей попытки | INTEGER | 5 | 1,2,3,...12, … |
| created\_at | Дата создания записи | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **EmailSubscription  (Подписка на рассылку)** |  | PK: email\_subscription\_id FK: email\_id FK: user\_id FK: donor\_id unisender\_list\_id unisender\_contact\_id source is\_active created\_at updated\_at |  |  |
| email\_subscription\_id | Уникальный идентификатор подписки | UUID | 36 |  |
| email\_id | Идентифкатор email | UUID | 36 |  |
| user\_id | Ссылка на Пользователя | UUID | 36 |  |
| donor\_id | Ссылка на Благодарителя | UUID | 36 |  |
| unisender\_list\_id | Идентификатор списка в Unisender | VARCHAR | 50 |  |
| unisender\_contact\_id | Идентификатор контакта в Unisender | VARCHAR | 100 |  |
| source | Источник подписки | ENUM | \- | registration donation subscription |
| is\_active | Тип рассылки | ENUM | \- | пока неизвестно |
| created\_at | Дата создания подписки | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **EmailNotification  (Письма / Уведомления)** |  | PK: email\_notification\_id template\_id FK: email\_id subject status delivery\_status sent\_at delivered\_at opened\_at clicked\_at bounce\_type bounce\_reason retry\_count max\_retries next\_retry\_at idempotency\_key related\_entity\_type related\_entity\_id created\_at updated\_at |  |  |
| email\_notification\_id | Уникальный идентификатор письма | UUID | 36 |  |
| template\_id | Идентификатор шаблона письма | UUID | 36 |  |
| email\_id | Ссылка на адрес получателя | UUID | 36 |  |
| subject | Тема письма | VARCHAR | 255 |  |
| status | Статус письма | ENUM | \- | sent queued failed bounced |
| delivery\_status | Статус доставки | ENUM | \- |  |
| sent\_at | Дата и время отправки | TIMESTAMP | 19 |  |
| delivered\_at | Дата и время доставки | TIMESTAMP | 19 |  |
| opened\_at | Дата и время открытия письма | TIMESTAMP | 19 |  |
| clicked\_at | Дата и время первого клика | TIMESTAMP | 19 |  |
| bounce\_type | Тип отскока | ENUM | \- | hard soft |
| bounce\_reason | Причина отскока | VARCHAR | 255 |  |
| retry\_count | Количество попыток переотправки | INTEGER | 5 |  |
| max\_retries | Максимальное количество повторов | INTEGER | 5 |  |
| next\_retry\_at | Дата и время следующей попытки | TIMESTAMP | 19 |  |
| idempotency\_key | Ключ идемпотентности | UUID | 36 |  |
| related\_entity\_type | Тип связанной сущности | VARCHAR | 50 |  |
| related\_entity\_id | Идентификатор связанной сущности для отслеживания контакта | UUID | 36 |  |
| created\_at | Дата создания записи | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **EmailAddress (Email адрес)** |  | PK: email\_id email email\_verified validation\_status created\_at updated\_at |  |  |
| email\_id | Уникальный идентификатор email | UUID | 36 |  |
| email | Email адрес | VARCHAR | 255 |  |
| email\_verified | Дата и время подтверждения | TIMESTAMP | 19 |  |
| validation\_status | Статус валидации | ENUM | \- | valid invalid soft\_bounce hard\_bounce |
| created\_at | Дата добавления email | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **EmailTemplate (Шаблон письма)** |  | PK: template\_id template\_code subject body\_html body\_text variables description is\_active version created\_at updated\_at |  |  |
| template\_id | Уникальный идентификатор шаблона | UUID | 36 |  |
| template\_code | Код шаблона | VARCHAR | 10 | ФТ-001, ФТ-026, … |
| subject | Тема письма | VARCHAR | 255 |  |
| body\_html | HTML-версия тела письма | TEXT | \- |  |
| body\_text | Текстовая версия тела письма | TEXT | \- |  |
| variables | JSON со списком переменных для подстановки | JSON | \- |  |
| description | Описание шаблона для администратор | VARCHAR | 500 |  |
| is\_active | Активен ли шаблон? | BOOLEAN | 1 |  |
| version | Версия шаблона | INTEGER | 5 |  |
| created\_at | Дата создания шаблона | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **EmailTemplateHistory (История изменения шаблонов)** |  | PK: history\_id template\_id version subject body\_html body\_text created\_at updated\_at changed\_by\_admin\_id |  |  |
| history\_id | Уникальный идентификатор записи истории | UUID | 36 |  |
| template\_id | Идентификатор шаблона | UUID | 36 |  |
| version | Номер версии | INTEGER | 5 |  |
| subject | Тема письма (версия) | VARCHAR | 255 |  |
| body\_html | HTML версия (версия) | TEXT | \- |  |
| body\_text | Текстовая версия (версия) | TEXT | \- |  |
| created\_at | Дата создания этой версии | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| changed\_by\_admin\_id | Идентификатор Администратора, внёсшего изменение | UUID | 36 |  |
| **Admin (Администратор)** |  | PK: admin\_id email\_id username status last\_login\_at created\_at updated\_at |  |  |
| admin\_id | Уникальный идентификатор Администратора | UUID | 36 |  |
| email\_id | Ссылка на email Администратора | UUID | 36 |  |
| username | Уникальное имя Администратора | VARCHAR | 100 |  |
| status | Статус | ENUM | \- | active inactive blocked |
| last\_login\_at | Дата и время последнего входа | TIMESTAMP | 19 |  |
| created\_at | Дата создания аккаунта Администратора | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **Statistics (Статистика писем)** |  | PK: statistics\_id date template\_id total\_sent total\_delivered total\_opened total\_clicked total\_bounced unsubscribed delivery\_rate\_pct open\_rate\_pct ctr\_rate\_pct ctor\_rate\_pct calculated\_at updated\_at |  |  |
| statistics\_id | Уникальный идентификатор записи | UUID | 36 |  |
| date | Дата, для которой считается статистика | DATE | 10 |  |
| template\_id | Идентификатор шаблона письма | UUID | 36 |  |
| total\_sent | Всего писем отправлено | INTEGER | 10 |  |
| total\_delivered | Всего писем доставлено | INTEGER | 10 |  |
| total\_opened | Всего писем открыто | INTEGER | 10 |  |
| total\_clicked | Всего кликов по ссылкам | INTEGER | 10 |  |
| total\_bounced | Всего отскоков | INTEGER | 10 |  |
| unsubscribed | Количество отписок | INTEGER | 10 |  |
| delivery\_rate\_pct | Процент доставки (DR) | DECIMAL | 6,4 |  |
| open\_rate\_pct | Процент открытий (OR) | DECIMAL | 6,4 |  |
| ctr\_rate\_pct | Процент кликов (CTR) | DECIMAL | 6,4 |  |
| ctor\_rate\_pct | Процент CTOR | DECIMAL | 6,4 |  |
| calculated\_at | Дата и время расчёта статистики | TIMESTAMP | 19 |  |
| updated\_at | Дата последнего обновления | TIMESTAMP | 19 |  |
| **AuditLog (Журнал аудита)** |  | PK: audit\_log\_id entity\_type entity\_id action actor\_type actor\_id user\_agent timestamp retention\_until |  |  |
| audit\_log\_id | Уникальный идентификатор записи | UUID | 36 |  |
| entity\_type | Тип сущности | VARCHAR | 50 |  |
| entity\_id | Идентификатор сущности | UUID | 36 |  |
| action | Тип действия | ENUM | \- | CRUD |
| actor\_type | Кто выполнил действие | ENUM | \- | system admin api\_client |
| actor\_id | Идентификатор Администратора или системы | UUID | 36 |  |
| user\_agent | User-Agent браузера/клиента | TEXT | \- |  |
| timestamp | Дата и время действия | TIMESTAMP | 19 |  |
| retention\_until | Дата удаления записи | DATE | 10 | 3 года по НФТ-3.5 |
