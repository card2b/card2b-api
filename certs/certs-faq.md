# FAQ по Apple-сертификатам и pkpass


### Q: Что такое pkpass-файл, и зачем вообще нужен сертификат?

Apple Wallet карты — это специальные файлы с расширением .pkpass. 
Пока ещё все привыкли носить в кошельке кучу дисконтных пластиковых карт, но электронные Wallet-карты приходят им на смену.
Их можно устанавливать на iPhone и на Android.

Сертификат — это то, чем подписываются (шифруются) эти файлы, чтоб удостоверить подлинность. 
Таким образом Apple обеспечивает безопасность пользователей: он не позволит открыть карточку, которая подписана чужим сертификатом. 
Это как приватный ключ, известный только Вам и никому другому.


### Q: Зачем мне нужен собственный сертификат?

По умолчанию мы используем свои собственные сертификаты для наших пользователей. 
Т.е. Ваши клиенты получают карточки, подписанные нашими сертификатами.

> Однако, мы настоятельно рекомендуем, если вы серьёзно подходите к делу, загрузить к нам собственный сертификат, и тогда мы будем подписывать им. Вот почему:

- это юридическая и психологическая гарантия для Вас; вы всегда можете доказать своё право на выпущенные карточки
- если Вы решите больше не пользоваться нашим сервисом, то Вы легко продолжите выпускать и менять существующие карты самостоятельно
- iPhone обладает противным свойством: он кеширует и группирует по pass_type_id (id сертификата); если у клиента на iPhone окажутся 2 карточки — одна Ваша, другая другого пользователя нашего сервиса (который тоже без своего сертификата) — эти 2 карточки сгруппируются, что введёт клиента в заблуждение 


### Q: Как создать собственный сертификат?

[Вот здесь подробная инструкция](./cert-creation.md). Сначала нужно создать сертификат в Apple-аккаунте, потом загрузить его на Card2b и привязать к шаблону.


### Q: Это платно?

Увы, да. Apple Developer Account, который позволяет выпускать сертификаты, стоит 99$ для физических лиц и 299$ в год для организаций. 

> Возможно, у Вашей огранизации уже есть Apple account — например, если у Вас есть iOS-приложение. В таком случае дополнительно ничего не нужно, сертификаты можно делать там.  


### Q: Почему всё так сложно?

Это криптография, с ней всегда так сложно. Зато эти сертификаты никаким образом нельзя подделать.

Это Ваша безопасность и безопасность Ваших пользователей. 


### Q: Можно ли создать несколько сертификатов?

Да, на один и тот же Apple-аккаунт можно создать несколько десятков. Их можно потом все загрузить в Card2b.

Более того: **лучше на каждый шаблон создать свой сертификат**, особенно если они действительно разноплановые. Это позволит избежать группировки по pass_type_id и проблемы с уведомлениями, о чём написано выше. 


### Q: Я уже начал выпускать карты, есть установки. Если я загружу сертификат, что станет с существующими?

Ранее выпущенные карты останутся со старым сертификатом, новые будут с новым. Сменить его не получится, но **всё будет работать**. Все карты будут также обновляться и прочее. Т.е. поле "сертификат" у шаблона — действует на новые карты, за ранее выпущенными он уже зафиксирован.

> Поэтому, если Вы потестировали сервис и решили использовать его на реальных клиентах, очень советуем загрузить собственный сертификат с самого начала. 

