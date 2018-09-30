# override_data — переопределение данных карточки

В методах выпуска карточки [/api/card/issue](./cards.md#api_card_issue) и изменения данных [/api/card/update](./cards.md#api_card_update)
используется **override_data**. 

- если значение null, то ключ возвращается к дефолтным данным шаблона
- если не null, то перезатирается
- если отсутствует, то не меняется
- также есть ключи со специальными названиями, независимо от шаблона

### Специальные ключи в override_data

Пример:

```json
{
  "bonus": 10,                        // перезатираем default_data шаблона
  "backgroundColor": "#fff",          // спецключ: белый цвет фона
  "thumbnailImage": "1:vai/v3li2:3"   // спецключ: картинка-миниатюра
}
```

Вот какие есть специальные ключи в **override_data**:

Ключ | Тип | Подробно
-----|-----|---------
logoImage<br>thumbnailImage<br>backgroundImage<br>footerImage<br>stripImage | string | Чтобы менять индивидуально у конкретных карточек картинки: логотип, миниатюру, фон, колонтитул, фон-полоску. См. [загрузка изображений](./working-with-api.md#загрузка-изображений)
locations | {latitude: number,<br>longitude: number,<br>altitude?: number,<br>relevantText?: string} | Поставить у конкретной карточки геолокацию, отличающуюся от шаблонной; подробнее см. [документацию Apple](https://developer.apple.com/library/content/documentation/UserExperience/Reference/PassKit_Bundle/Chapters/LowerLevel.html#//apple_ref/doc/uid/TP40012026-CH3-SW2)
transitType | string | Для посадочных талонов: иконка между primary field'ами; подробнее см. [документацию Apple](https://developer.apple.com/library/content/documentation/UserExperience/Reference/PassKit_Bundle/Chapters/LowerLevel.html#//apple_ref/doc/uid/TP40012026-CH3-SW1)
relevantDate | W3C date string | *Рекомендуется для типов "событие" и "посадочный талон"*: точное время, когда карточка становится актуальной для клиента; подробнее см. [документацию Apple](https://developer.apple.com/library/content/documentation/UserExperience/Reference/PassKit_Bundle/Chapters/TopLevel.html#//apple_ref/doc/uid/TP40012026-CH2-SW2)
expirationDate | W3C date string | Когда срок действия карточки истекает; подробнее см. [документацию Apple](https://developer.apple.com/library/content/documentation/UserExperience/Reference/PassKit_Bundle/Chapters/TopLevel.html#//apple_ref/doc/uid/TP40012026-CH2-SW2)
labelColor<br>backgroundColor<br>foregroundColor | string | Сменить у конкретной карточки цвет заголовков, фона и значений; должен быть в hex-формате '#aabbcc' или rgb-формате 'rgb(rr, gg, bb)'.  

**Чаще всего использовать эти ключи не понадобится**
 
* самое актуальное — это смена изображений; например, поместить фотографию клиента в **thumbnailImage**
* также распространённое — это при использовании одного шаблона типа "событие" выпускать карточки с разным временем, и задавать **relevantDate**/**expirationDate** (хотя логичнее использовать по шаблону на событие, и задавать это на уровне шаблона, а не от карточки к карточке)
* остальные кейсы, когда нужно менять так детально, намного более редкие     

