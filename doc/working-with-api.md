# card2b — общие принципы работы с API

## Обращение

Обращение к API происходит просто по GET и POST методам. Мы решили не завязываться на более экзотические PUT/PATCH и др., чтобы было проще интегрироваться большинству внешних систем.

POST-данные могут быть отправлены либо в json-виде (Content-Type='application/json'), либо в urlencoded-виде (Content-Type='application/x-www-form-urlencoded'). Если заголовок Content-Type отсутствует, мы парсим запрос как json.

**Формат ответа — всегда json.**

В каждом запросе (неважно, GET или POST) вы должны **передать ?api_token в GET-параметрах**. Токен вы можете найти в личном кабинете, на вкладке "API" любого шаблона. *Опять-таки, api_token в get-параметрах, а не заголовоках, для более простой интеграции*.

Пример:

```
GET https://{domain}/api/card/456?api_token=789ec0ce45ceef4a7c90a96e9d472b3f
```

## Объекты

Ниже — список объектов, встречающихся в ответах по API.

#### Card  

Объект **выпущенная карточка** представляет собой следующее:

```json
{
  "card_id": 10148,
  "url": "https://{domain}/c/10148/ffbc60f02b1ae137",
  "data": {
    "bonus": "10.00",
    "status": "Синий",
    "client_id": "12540",
    "owner_name": "-"
  },
  "create_time": "2018-03-11T11:58:00.000Z",
  "update_time": "2018-03-12T12:53:00.000Z",
  "last_request_time": "2018-03-12T12:53:09.143Z",
  "n_installed": 1,
  "deactivated": false,
  "versions": [
    {
      "v_num": 1,
      "valid_from": "2018-03-11T11:58:00.000Z"
    },
    {
      "v_num": 2,
      "valid_from": "2018-03-12T12:53:00.000Z"
    }
  ]
}
```

* **url** — ссылка на скачивание (установку)
* **data** — наложение данных, переопределяемых карточкой, на дефолтные данные шаблона (см. [концепция: шаблоны и данные](./basic-concepts.md))
* **create_time** — время создания карточки
* **update_time** — время последнего обновления (изменения); совпадает с valid_from последней версии
* **last_request_time** — время последнего скачивания карточки на устройство клиента (null, если карта не была ни разу установлена); если last_request_time < update_time, то карточка на устройстве клиента не обновлена до последней версии
* **n_installed** — количество установок (карточка может быть установлена на несколько устройств одновременно); уменьшается, когда клиент удаляет карточку с устройства
* **deactivated** — если вы вручную или [по api](./cards.md#api_card_deactivate) деактивировали карточку; тогда её нельзя скачать, и она недостуна для новых установок
* **versions** — полный список всех версий карточки; при [изменении данных](./cards.md#api_card_update) создаётся новая версия; при [изменении шаблона](./templates.md#api_template_update) также создается новая версия у всех его карточек

Такой объект возвращается, например, при [/api/card/get](./cards.md#api_card_get). При массовых запросах, когда возвращаются много карточек (например, [/api/template/:id/cards](./templates.md#api_template_cards)), они возвращаются без списка версий.    

#### Card version

Методом [/api/card/get](./cards.md#api_card_get_vnum) можно получить **состояние (версию) карточки** на любой момент времени, либо по номеру версии:

```json
{
  "card_id": 10148,
  "valid_from": "2018-03-12T01:58:00.000Z",
  "valid_to": "2018-03-12T02:53:00.000Z",
  "data": {
    "bonus": "0.00",
    "status": "Синий",
    "client_id": "12540",
    "owner_name": "-"
  },
  "template_settings": {
    "...": "..."
  },
  "pass_type": "storeCard"
}
``` 

* **valid_from** — с какого момента эта версия была актуальна
* **valid_to** — до какого момента эта версия была актуальна, а если она актуальна и сейчас, то конец 2999-го года
* **data** — наложение данных, которые были на тот период времени, на дефолтные данные шаблона, который был актуален на тот период времени
* **template_settings** — полные настройки шаблона на период \[valid_from..valid_to]
* **pass_type** — тип карты на период \[valid_from..valid_to] (generic, boardingPass, coupon, eventTicket, storeCard)   

Изменение данных карточки приводит к новой версии карточки. Изменение дефолтных данных/настроек шаблона приводит к новым версиям всех выпущенных по нему карточек.

#### Template

Объект **шаблон** представляет собой следующее:

```json
{
  "template_id": 64,
  "pass_type": "storeCard",
  "title": "Пивная карта",
  "description": "Универсальная карта клиентов нашего бара MyBeerProject",
  "template_settings": {
    "...": "..."
  },
  "default_data": {
    "bonus": "0.00",
    "status": "Синий",
    "client_id": "00000",
    "owner_name": "-"
  },
  "create_time": "2017-09-18T12:33:45.620Z",
  "update_time": "2018-03-15T18:09:06.737Z",
  "deactivated": false,
  "cards_issued": 569,
  "cards_installed": 301,
  "versions": [
    {
      "v_num": 1,
      "valid_from": "2017-09-18T12:33:45.620Z"
    },
    {
      "v_num": 2,
      "valid_from": "2018-03-13T16:48:13.642Z"
    }
  ]
}
```

* **pass_type** — тип карты (generic, boardingPass, coupon, eventTicket, storeCard)
* **title** — название шаблона 
* **description** — подробное описание
* **template_settings** — полные настройки шаблона 
* **default_data** — данные по умолчанию для создаваемых карточек (см. [концепция: шаблоны и данные](./basic-concepts.md))
* **create_time** — время создания шаблона
* **update_time** — время последнего изменения данных или настроек, что повлекло к новой версии шаблона и всех выпущенных карточек; совпадает с valid_from последней версии
* **deactivated** — если вы вручную или [по api](./templates.md#api_template_deactivate) деактивируете шаблон, то у него нельзя будет выпустить новую карточку; фактически, деактивация — это предохранитель
* **cards_issued** — количество выпущенных карточек (в том числе деактивированных)
* **cards_installed** — количество установленных (и ещё не удалённых) карточек
* **versions** - полный список всех версий шаблона; при [изменении шаблона](./templates.md#api_template_update) создаётся самого шаблона, а также всех его карточек    

#### Template version

Методом [/api/template/get](./templates.md#api_template_get_vnum) можно получить **состояние (версию) шаблона** на любой момент времени, либо по номеру версии:

```json
{
  "template_id": 64,
  "valid_from": "2017-09-18T12:33:45.620Z",
  "valid_to": "2018-03-13T16:48:13.642Z",
  "pass_type": "storeCard",
  "title": "Пивная карта",
  "description": "Универсальная карта клиентов нашего бара MyBeerProject",
  "template_settings": {
    "...": "..."
  },
  "default_data": {
    "bonus": "0.00",
    "status": "Синий",
    "client_id": "00000",
    "owner_name": "-"
  }
}
```

* **valid_from** — с какого момента эта версия была актуальна
* **valid_to** — до какого момента эта версия была актуальна, а если она актуальна и сейчас, то конец 2999-го года
* **pass_type** — тип карты на тот период времени (generic, boardingPass, coupon, eventTicket, storeCard)
* **title** — название шаблона (не версионируется, совпадает с тем, что сейчас)
* **description** — описание шаблона (тоже не версионируется)
* **template_settings** — полные настройки шаблона на период \[valid_from..valid_to]
* **default_data** — дефолтные данные на период \[valid_from..valid_to] 

## Ошибки

В случае ошибки исполнения api-запроса мы отдадим statusCode 4xx и ошибку в виде

```json
{ 
  "errorCode": 5, 
  "message": "..."
}
```

Возможные значения errorCode:

* 1 — неверный запрос (не распознаны POST-данные, некорректный url и пр.)
* 2 — внутренняя ошибка сервера, повторите действие позже
* 3 — отсутствует api_token
* 4 — неверный или истекший api_token
* 5 — некорректные параметры (нечисловая строка вместо числа, неверный формат цвета, отсутствует required значение и пр.)
* 6 — карта не найдена
* 7 — шаблон не найден 
* 8 — превышен лимит на аккаунт (выпущено слишком много карточек и пр.)

## Загрузка изображений

Предположим, вы хотите для каждого клиента (для каждой карточки) отображать фотографию клиента в thumbnailImage карточки.
В таком случае, перед выпуском карты через [/api/card/issue](./cards.md#api_card_issue) вам нужно загрузить изображение к нам на сервер и получить его хеш, который подставить в override_data::thumbnailImage.

Загрузка изображений происходит следующим образом. 

1. Вы делаете GET */api/get-upload-url?image_type={image_type}*, где image_type одно из: iconImage, logoImage, thumbnailImage, backgroundImage, footerImage, stripImage 
2. В качестве ответа мы возвращаем `{"upload_url": "https://...."}`
3. На этот *upload_url* вы POST'ом загружаете файл (как обычный form-файл, Content-Type='multipart/form-data') 
4. В качестве ответа мы возвращаем нечто вроде `{"image_filehash": "1:vai/v3li2:3"}`
5. Вот эту строку *image_filehash* и нужно указать как thumbnailImage в override_data при */api/card/issue* или */api/card/update*. 

## Callback API 

Мы можем уведомлять ваш сервер при любых действиях, связанных с карточками. Это полезно, например, когда часть действий вы проводите через браузер, а часть по api: на заданный url будут приходить все события. 

Укажите callback url на вкладке "API" шаблона, и мы будем присылать туда POST в формате

```json
{
  "template_id": 123,
  "card_id": 456,
  "action_type": "some_action"
}
``` 

В настоящий момент мы уведомляем о следующих событиях *action_type*:

* **card_issue** — карточка выпущена
* **card_update** — данные карточки изменены
* **card_deactivate** — карточка деактивирована (и не сможет быть больше установлена)
* **card_activate** — деактивация карточки убрана
