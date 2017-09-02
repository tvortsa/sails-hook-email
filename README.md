# sails-hook-email

[![Dependency Status](https://david-dm.org/balderdashy/sails-hook-email.svg)](https://david-dm.org/balderdashy/sails-hook-email)

Email hook для [Sails JS](http://sailsjs.org), использует [Nodemailer](https://github.com/andris9/Nodemailer/blob/v1.3.4/README.md)

*Note: требует Sails v0.10.6+.*

### Установка

`npm install sails-hook-email`

### Использование

`sails.hooks.email.send(template, data, options, cb)`

Параметр       | Тип                 | Детали
-------------- | ------------------- |:---------------------------------
template       | ((string))          | Относительный путь из `templateDir` (см "Configuration" below) к папке содержащей email шаблоны.
data           | ((object))          | Данные, используемые для замены токенов шаблонов
options        | ((object))          | опции отправки Email (см. [Nodemailer docs](https://github.com/andris9/Nodemailer/blob/v1.3.4/README.md#e-mail-message-fields))
cb             | ((function))        | Callback запускаемый после отправки имэйл (или при возникновении ошибки).

### Конфигурация

ПО умолчанию, конфигурация находится здесь: `sails.config.email`.  The configuration key (`email`) можно изменить настройкой `sails.config.hooks['sails-hook-email'].configKey`.

Parameter      | Type                | Details
-------------- | ------------------- |:---------------------------------
service        | ((string)) | "общеизвестные сервисы" с которым Nodemailer знает как взаимодействовать (см. [список сервисов](https://github.com/andris9/nodemailer-wellknown/blob/v0.1.5/README.md#supported-services))
auth | ((object)) | объект аутентификации вида `{user:"...", pass:"..."}`
transporter | ((object)) | Custom transporter passed directly to nodemailer.createTransport (overrides service/auth) (see [Available Transports](https://github.com/andris9/Nodemailer/blob/v1.3.4/README.md#available-transports))
templateDir | ((string)) | путь к view шаблонам относительноo `sails.config.appPath` (по умолчанию `views/emailTemplates`)
from | ((string)) | Дефолтный `from` email адрес
testMode | ((boolean)) | Flag показывающий ято hook в режиме "test mode".  В test режиме, email опции и содержимое записываются в файл `.tmp/email.txt` вместо того чтобы действительно отправляться.  По умолчанию `true`.
alwaysSendTo | ((string)) | Если установлено, все emails будут отсылаться на этот абресс независимо от опции `to` .  Это удобно для тестирования реальных emails без опасения заспамить случайно людей.

#### Пример

```javascript
// [your-sails-app]/config/email.js
module.exports.email = {
  service: 'Gmail',
  auth: {user: 'foobar@gmail.com', pass: 'emailpassword'},
  testMode: true
};

```


### Шаблоны

Шаблоны создаются с использованием сконфигурированнjuj Sails [View Engine](http://sailsjs.org/#!/documentation/concepts/Views/ViewEngines.html), что позволяет использовать несколько шаблонных движков и макетов.  Если Sails Views отключен, вернется к EJS templates. Для создания новогоw email шаблона, создайте новую папку с именем вашего шаблона в папке `templateDir`, и добавьте в нее файл **html.ejs** (substituting .ejs for your template engine).   файлВы также можете добавить необязательный файл `text.ejs`; если ни один не предоставлен, Nodemailer попытается создать текстовую версию электронной почты на основе html-версии.

### Пример

Со следующим файлом **html.ejs** в папке **views/emailTemplates/testEmail**:

```
<p>Дорогой <%=recipientName%>,</p>
<br/>
<p><em>Спасибо</em> за то, что ты друг.</p>
<p>С Любовью,<br/><%=senderName%></p>
```

выполнение следующей команды (после [конфигурирования вашего email сервиса](https://github.com/емщкеыф/sails-hook-email/edit/tvr_russian/#configuration) и отключения test режима) :

```
sails.hooks.email.send(
  "testEmail",
  {
    recipientName: "Joe",
    senderName: "Sue"
  },
  {
    to: "joe@example.com",
    subject: "Hi there"
  },
  function(err) {console.log(err || "It worked!");}
)
```

результатом будет следующий email отправленный по адресу `joe@example.com`

> Дорогой Joe,
>
> *Спасибоu* что ты друг.
>
> С любовью,
>
> Sue

с ошибкой, печатаемой на консоли, если произошла ошибка, или "It worked!".
