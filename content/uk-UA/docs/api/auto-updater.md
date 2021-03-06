# autoUpdater

> Дозволяє застосункам автоматично оновлювати себе.

Процес: [Main](../glossary.md#main-process)

**You can find a detailed guide about how to implement updates into your application [here](../tutorial/updates.md).**

## Platform Notices

Currently, only macOS and Windows are supported. There is no built-in support for auto-updater on Linux, so it is recommended to use the distribution's package manager to update your app.

In addition, there are some subtle differences on each platform:

### macOS

На macOS, модуль `autoUpdater` вбудований в [Squirrel.Mac](https://github.com/Squirrel/Squirrel.Mac), мається на увазі, що не потрібно додаткових налаштувань для його роботи. Щоб дізнатися потреби серверної частини, можете прочитати [Підтримку Серверу](https://github.com/Squirrel/Squirrel.Mac#server-support). Зауважте що [App Transport Security](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) (ATS) застосовується для всіх запитів зроблених при процесі оновлення. Застосунки, яким потрібно вимкнути ATS можуть додати ключ `NSAllowsArbitraryLoads` до свого plist.

**Примітка:** Ваш застосунок має бути підписаний на автоматичні оновлення на macOS. Це вимога `Squirrel.Mac`.

### Windows

На Windows, ви маєте встановлювати свій застосунок на машину користувача перед тим, як зможете використовувати `autoUpdater`, тому рекомедовано використовувати [electron-winstaller](https://github.com/electron/windows-installer), [electron-forge](https://github.com/electron-userland/electron-forge) чи [grunt-electron-installer](https://github.com/electron/grunt-electron-installer) пакети для генерації встановника Windows.

При використанні [electron-winstaller](https://github.com/electron/windows-installer) чи [electron-forge](https://github.com/electron-userland/electron-forge) переконайтеся що ви не намагаєтеся оновити застосунок [при першому запуску](https://github.com/electron/windows-installer#handling-squirrel-events) (Дивіться також [ці випадки для детільної інформації](https://github.com/electron/electron/issues/7155)). Також рекомендується використовувати [electron-squirrel-startup](https://github.com/mongodb-js/electron-squirrel-startup) для отримання піктограм вашого застосунку на робочому столі.

Встановник згенерований Squirrel створить піктограму з [Application User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx) у форматі `com.squirrel.PACKAGE_ID.YOUR_EXE_WITHOUT_DOT_EXE`, наприклад, `com.squirrel.slack.Slack` чи `com.squirrel.code.Code`. Ви маєте використовувати однакове ID для вашого застосунку з `app.setAppUserModelId` API, в іншому випадку Windows не зможе правильно закріпити ваш застосунок в панелі завдань.

На відміну від Squirrel.Mac, Windows може тримати оновлення на S3 чи будь-якому іншому файловому сховищі. Ви можете прочитати документацію [Squirrel.Windows](https://github.com/Squirrel/Squirrel.Windows) для детальнішої інформації як Squirrel.Windows працює.

## Події (Events)

Об'єкт `autoUpdater` викликає наступні події:

### Подія: 'error'

Повертає:

* `error` Error

Відбувається коли виникає помилка при оновленні.

### Подія: 'checking-for-update'

Відбувається при перевірці чи стартувало оновлення.

### Подія: 'update-available'

Відбуваєтсья коли доступне оновлення. Воно завантажується автоматично.

### Подія: 'update-not-available'

Відбувається коли нема доступних оновлень.

### Подія: 'update-downloaded'

Повертає:

* `event` Event
* `releaseNotes` String
* `releaseName` String
* `releaseDate` Date
* `updateURL` String

Вібдувається коли оновлення завантажено.

На Windows доступне тільки `releaseName`.

## Методи

Об'єкт `autoUpdater` має наступні методи:

### `autoUpdater.setFeedURL(url[, requestHeaders])`

* `url` String
* `requestHeaders` Object *macOS* (опціонально) - хедери HTTP запиту.

Встановлює `url` та ініціалізує автоновлення.

### `autoUpdater.getFeedURL()`

Повертає `String` - Поточна URL для оновлення.

### `autoUpdater.checkForUpdates()`

Запитує сервер чи доступні оновлення. Потрібно викликати `setFeedURL` перед використанням цього API.

### `autoUpdater.quitAndInstall()`

Перезавантажує застосунок та встановлює оновлення після їх завантаження. Має викликатися тільки після події `update-downloaded`.

**Примітка:** `autoUpdater.quitAndInstall()` закриє всі вікна застосунку і викличе тільки подію `before-quit`. Це відмінність від нормальної послідовності подій виходу.