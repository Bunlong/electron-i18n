# autoUpdater

> Uygulamaları kendiliğinden güncelleme yapmak için etkinleştirin.

İşlem: [Ana](../glossary.md#main-process)

**You can find a detailed guide about how to implement updates into your application [here](../tutorial/updates.md).**

## Platform Notices

Currently, only macOS and Windows are supported. There is no built-in support for auto-updater on Linux, so it is recommended to use the distribution's package manager to update your app.

In addition, there are some subtle differences on each platform:

### macOS

MacOS'ta, `autoUpdater` modulü [Squirrel](https://github.com/Squirrel/Squirrel.Mac) üzerine kurulmuştur. Mac'in anlamı onu çalışabilir yapmak için herhangi bir özel kuruma ihtiyacınız yok demektir. Sunucu tarafı gereksinimleri için [Sunucu desteği](https://github.com/Squirrel/Squirrel.Mac#server-support) okuyabilirsiniz. [Uygulama Aktarım Katmanı Güvenliği](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)'nin (ATS) güncelleştirme işleminin bir parçası olarak yapılan tüm istekler için geçerli olduğunu unutmayın. Uygulamalar plist'lerine `NSAllowsArbitraryLoads` anahtarını ekleyerek ATS'nin devre dışı kalmasını sağlamalıdır.

**Not:** macOS'ta uygulamanız otomatik güncelleştirmeler için onaylı olmalıdır. Bu `Squirrel.Mac`'nin bir gereksinimidir.

### Windows

Windows'ta `autoUpdater`'ı kullanmadan önce uygulamanızı bir kullanıcının makinesine yüklemeniz gerekir. Bu nedenle Windows yükleyici paketi oluşturmak için [electron-winstaller](https://github.com/electron/windows-installer), [electron-forge](https://github.com/electron-userland/electron-forge) veya [grunt-electron-installer](https://github.com/electron/grunt-electron-installer) kullanılması önerilir.

[electron-winstaller](https://github.com/electron/windows-installer) veya [electron-forge](https://github.com/electron-userland/electron-forge) kullanırken uygulamanız [ilk çalıştığında ](https://github.com/electron/windows-installer#handling-squirrel-events)güncellenmeye çalışmadığından emin olun ([Ayrıca daha fazla bilgi için buna bakın](https://github.com/electron/electron/issues/7155)). Ayrıca, uygulamanız için masaüstü kısayolları almak için [electron-squirrel-startup](https://github.com/mongodb-js/electron-squirrel-startup) 'ı kullanmanız önerilir.

Yükleyici Squirrel ile [Application User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx) şeklinde bir kısayol ikonu üretecektir ve bunun formatı `com.squirrel.PACKAGE_ID.YOUR_EXE_WITHOUT_DOT_EXE` şeklinde olacaktır. `com.squirrel.slack.Slack` ve `com.squirrel.code.Code` bunun örnekleridir. Uygulamanız için `app.setAppUserModelId` API ile aynı ID'yi kullanmanız gerekiyor, aksi halde Windows görev çubuğunda uygulamanızı doğru bir şekilde sabitleyemeyecektir.

Squirrel.Mac'ten farklı olarak, Windows güncelleştirmeleri S3'te veya diğer herhangi bir statik dosya ana sisteminde tutabilir. Squirrel.Windows'un nasıl çalıştığı hakkında daha fazla bilgi almak için [Squirrel.Windows](https://github.com/Squirrel/Squirrel.Windows) belgelerini okuyabilirsiniz.

## Events

`autoUpdater` nesnesi aşağıdaki olaylarla ortaya çıkarır:

### Olay: 'error'

Dönüşler:

* `error` Error

Güncelleştirilirken bir hata olduğunda ortaya çıkan.

### Event: 'checking-for-update'

Bir güncellemenin başlatılıp başlatılmadığını kontrol ederken ortaya çıkan.

### Event: 'update-available'

Kullanılabilir bir güncelleştirme olduğunda ortaya çıkan. Güncelleştirme otomatik olarak karşıdan yüklenir.

### Olay: 'update-not-available'

Mevcut bir güncelleme yokken ortaya çıkan.

### Event: 'update-downloaded'

Dönüşler:

* `olay` Olay
* `releaseNotes` String
* `releaseName` String
* `releaseDate` Date
* `updateURL` String

Bir güncelleme indirildiğinde ortaya çıkan.

Windows üzerinde yalnızca `releaseName` kullanılabilir.

## Metodlar

`autoUpdater` nesnesi aşağıdaki yöntemleri içerir:

### `autoUpdater.setFeedURL(url[, requestHeaders])`

* `url` Dize
* `requestHeaders` nesnesi *macOS* (isteğe bağlı) - HTTP başıkları ister.

`url`'i belirler ve otomatik güncelleyici başlar.

### `autoUpdater.getFeedURL()`

`String`'i geri döndürür - Geçerli olan akış URL'ini günceller.

### `autoUpdater.checkForUpdates()`

Sunucuya bir güncelleştirme olup olmadığını sorar. Bu API'yi kullanmadan önce `setFeedURL`'i çağırmalısınız.

### `autoUpdater.quitAndInstall()`

Uygulamayı yeniden başlatır ve indirmeler bittikten sonra güncellemeyi yükler. O yalnızca `update-downloaded` ortaya çıktıktan sonra çağırılmış olmalıdır.

**Not:** `autoUpdater.quitAndInstall()` ilk olarak tüm uygulama pencerelerini kapatacak ve bundan sonra `uygulama` üzerinde sadece `before-quit` event'i ortaya çıkacak. Bu normal çıkış event sırasından farklıdır.