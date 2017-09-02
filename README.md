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
service        | ((string)) | A "well-known service" с которым Nodemailer знает как взаимодействовать (см. [this list of services](https://github.com/andris9/nodemailer-wellknown/blob/v0.1.5/README.md#supported-services))
auth | ((object)) | объект аутентификации вида `{user:"...", pass:"..."}`
transporter | ((object)) | Custom transporter passed directly to nodemailer.createTransport (overrides service/auth) (see [Available Transports](https://github.com/andris9/Nodemailer/blob/v1.3.4/README.md#available-transports))
templateDir | ((string)) | Path to view templates relative to `sails.config.appPath` (defaults to `views/emailTemplates`)
from | ((string)) | Default `from` email address
testMode | ((boolean)) | Flag indicating whether the hook is in "test mode".  In test mode, email options and contents are written to a `.tmp/email.txt` file instead of being actually sent.  Defaults to `true`.
alwaysSendTo | ((string)) | If set, all emails will be sent to this address regardless of the `to` option specified.  Good for testing live emails without worrying about accidentally spamming people.

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

Templates are generated using your configured Sails [View Engine](http://sailsjs.org/#!/documentation/concepts/Views/ViewEngines.html), allowing for multiple template engines and layouts.  If Sails Views are disabled, will fallback to EJS templates. To define a new email template, create a new folder with the template name inside your `templateDir` directory, and add an **html.ejs** file inside the folder (substituting .ejs for your template engine).  You may also add an optional `text.ejs` file; if none is provided, Nodemailer will attempt to create a text version of the email based on the html version.

### Пример

Given the following **html.ejs** file contained in the folder **views/emailTemplates/testEmail**:

```
<p>Дорогой <%=recipientName%>,</p>
<br/>
<p><em>Спасибо</em> за то, что ты друг.</p>
<p>С Любовью,<br/><%=senderName%></p>
```

executing the following command (after [configuring for your email service](https://github.com/balderdashy/sails-hook-email/#configuration) and turning off test mode) :

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

will result in the following email being sent to `joe@example.com`

> Dear Joe,
>
> *Thank you* for being a friend.
>
> Love,
>
> Sue

with an error being printed to the console if one occurred, otherwise "It worked!".
