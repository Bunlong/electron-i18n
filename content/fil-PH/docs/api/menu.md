## Class: Menu

> Lumikha ng natural na mga menu ng aplikasyon at mga menu ng konteksto.

Proseso:[Main](../glossary.md#main-process)

### `bagong Menu()`

Lumilikha ng isang bagong menu.

### Mga statik na pamamaraan

Ang klase ng `menu` na mayroon ng mga sumusunod na mga istatikong pamamaraan:

#### `Menu.setApplicationMenu(menu)`

* `menu` Menu | null

Nagtatakda ng `menu` bilang ang aplikasyon ng menu sa macOS. Sa Windows at Linux, ang `menu` ay magtatakda sa ibabaw ng menu sa bawat window.

Ang pagpasa sa `null` ay aalisin ang bar ng menu sa Windows at sa Linux ngunit walang epekto sa macOS.

**Note:** Ang API na ito ay dapat tawagin pagkatapos ng `ready` sa event ng modyul ng `app`.

#### `Menu.getApplicationMenu()`

Returns `Menu | null` - The application menu, if set, or `null`, if not set.

**Note:** Ang nagbalik na instansya ng `Menu` ay hindi suportado ang dinamikong pagdadagdag o pagtatanggal ng mga aytem ng menu. [mga katangian ng pagkakataon ](#instance-properties) na maaring dynamic na binago.

#### `Menu.sendActionToFirstResponder(action)` *macOS*

* `action` String

Ipadala ang mga `Pagkilos` Sa mga unang responder ng aplikasyon. Ito ay ginagamit para sa pagtulad sa default macOS menu ng pag-uugali. Karaniwan mong ginagamit ang [`papel`](menu-item.md#roles)pag-aari ng isang[`Menultem`](menu-item.md).

Tingnan ang [mascOS Cocoa kaganapan sa pag-aasikso ng paggabay](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/EventArchitecture.html#//apple_ref/doc/uid/10000060i-CH3-SW7) Para sa mga karagdagang impormasyon sa mascOS' katutubong pagkilos.

#### `Menu.buildFromTemplate(template)`

* `ang template` [MenuItemConstructorOptions]

Nagbabalik ang `Menu`

Sa pangkalahatan, ang `template` ay lamang ng isang hanay ng mga `pagpipilian` para sa pagpapagawa ng isang [MenuItem](menu-item.md). Ang paggamit ay maaaring binanggit sa itaas.

Makakabitan mo din ang iba pang mga patlang sa mga elemento ng mga `template` at sila ay magiging mga katangian ng mga constructed menu item.

### Halimbawa ng mga pamamaraan

Ang `Menu`Na object ay ang mga sumusunod na pamamaraan ng pagkakataon:

#### `ang menu.popup([browserWindow, options])`

* `browserWindow` BrowserWindow (opsyonal) - default ang nakatuong window.
* `mga opsyon` Mga bagay (opsyonal) 
  * `x` Number (opsyonal) - Ang default ay ang kasalukuyang posisyon ng cursor ng mouse. Dapat ipahayag kung ang `y` ay naipahayag na.
  * `y` (opsyonal) - bilang Default ay ang kasalukuyang posisyon ng cursor ng mouse. Dapat ipahayag kung `x` ay ipinahayag.
  * `async` Boolean (opsyonal) - Itinakda sa `true` upang maibalik agad ang pamamaraang ito ay tinawag, `false` upang maibalik pagkatapos na ang menu ay mapili o maisara. Ang default na `mali`.
  * `positioningItem` Number (opsyonal) *macOS* - Ang indise ng mga aytem ng menu upang maging nakaposisyon sa ilalim ng cursor ng mouse sa tinukoy na pagkakatugma. Ang default ay -1.

Pasulputin ang menu na ito bilang isang menu ng konteksto sa `browserWindow`.

#### `menu.closePopup([browserWindow])`

* `browserWindow` BrowserWindow (opsyonal) - default ang nakatuong window.

Isinasara ang konteksto ng menu sa `browserWindow`.

#### `menu.append(menuItem)`

* `menuItem`MenuItem

Idinagdag ang `menuItem` sa menu.

#### `menu.getMenuItemById(id)`

* `id` String

Returns `MenuItem` the item with the specified `id`

#### `menu.insert(pos, menuItem)`

* `pos` Integer
* `menuItem` Ang MenuItem

Ipasok sa`menuItem`papunta sa posisyon ng`pos`ng menu.

### Mga Katangian ng Instansya

Ang mga bagay sa `menu` ay mayroon ding mga sumusunod na katangian:

#### `menu.items`

Ang hanay ng `MenuItem[]` na naglalaman ng mag aytem ng menu.

Bawat `Menu` ay binubuo ng maramihang [`MenuItem`](menu-item.md) at bawat `MenuItem` ay mayroong isang submenu.

## Halimbawa

Ang klase ng `Menu` ay magagamit lamang sa pangunahing proseso, ngunit maaari mo rin itong magamit sa prosesong tagabigay sa pamamagitan ng modyul ng [`remote`](remote.md).

### Pangunahing proseso

Isang halimbawa ng paglikha ng aplikasyon ng menu sa pangunahing proseso ay sa simpleng template ng API:

```javascript
const {app, Menu} = require('electron')

const template = [
  {
    label: 'Edit',
    submenu: [
      {role: 'undo'},
      {role: 'redo'},
      {type: 'separator'},
      {role: 'cut'},
      {role: 'copy'},
      {role: 'paste'},
      {role: 'pasteandmatchstyle'},
      {role: 'delete'},
      {role: 'selectall'}
    ]
  },
  {
    label: 'View',
    submenu: [
      {role: 'reload'},
      {role: 'forcereload'},
      {role: 'toggledevtools'},
      {type: 'separator'},
      {role: 'resetzoom'},
      {role: 'zoomin'},
      {role: 'zoomout'},
      {type: 'separator'},
      {role: 'togglefullscreen'}
    ]
  },
  {
    role: 'window',
    submenu: [
      {role: 'minimize'},
      {role: 'close'}
    ]
  },
  {
    role: 'help',
    submenu: [
      {
        label: 'Learn More',
        click () { require('electron').shell.openExternal('https://electronjs.org') }
      }
    ]
  }
]

if (process.platform === 'darwin') {
  template.unshift({
    label: app.getName(),
    submenu: [
      {role: 'about'},
      {type: 'separator'},
      {role: 'services', submenu: []},
      {type: 'separator'},
      {role: 'hide'},
      {role: 'hideothers'},
      {role: 'unhide'},
      {type: 'separator'},
      {role: 'quit'}
    ]
  })

  // Edit menu
  template[1].submenu.push(
    {type: 'separator'},
    {
      label: 'Speech',
      submenu: [
        {role: 'startspeaking'},
        {role: 'stopspeaking'}
      ]
    }
  )

  // Window menu
  template[3].submenu = [
    {role: 'close'},
    {role: 'minimize'},
    {role: 'zoom'},
    {type: 'separator'},
    {role: 'front'}
  ]
}

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```

### Prosesong tagabigay

Ang nasa ibaba ay isang halimbawa ng paglikha ng isang dinamikong menu sa isang pahina ng web (prosesong tagabigay) sa pamamagitan ng paggamit ng modyul ng [`remote`](remote.md), at ipinapakita ito kapag ang user ay nira-right click ang pahina:

```html
<!-- index.html -->
<script>
const {remote} = require('electron')
const {Menu, MenuItem} = remote

const menu = new Menu()
menu.append(new MenuItem({label: 'MenuItem1', click() { console.log('item 1 clicked') }}))
menu.append(new MenuItem({type: 'separator'}))
menu.append(new MenuItem({label: 'MenuItem2', type: 'checkbox', checked: true}))

window.addEventListener('contextmenu', (e) => {
  e.preventDefault()
  menu.popup(remote.getCurrentWindow())
}, false)
</script>
```

## Ang mga tala sa Menu ng Aplikasyon sa macOS

ang macOS ay may kompletong naiibang istilo ng aplikasyon ng menu mula saWindows at Linux. Narito ang ilang mga tala kung paanong ang menu ng iyong app ay maging mas natural.

### Mga pamantayan ng Menu

Sa macOS ay maraming tukoy na sistema ng standard na menu, tulad ng `Services` at mga menu ng `Window`. Para gawin ang iyong menu na standard na menu, dapat mong i-set ang iyong menu sa `role` sa isa sa mga sumusunod at ang Electron ay makikilala sila at gagawin silang mga standard na menu:

* `ang window`
* `tulong`
* `mga serbisyo`

### Mga Aksyon ng Standard Menu

ang macOS ay nagbigay ng standard na mga aksyon para sa ilang mga item ng menu, katulad ng `About xxx`, `Hide xxx`, at ` Hide Others`. Para itakda ang aksyon ng isang item ng menu sa isang standard na aksyon, dapat mong itakda ang katangian ng `role` ng item ng menu.

### Pangalan ng Pangunahing Menu

Sa macOS ang tatak ng unang item ng aplikasyon ng menu ay laging ang pangalan ng iyong app, hindi mahalaga kung anong tatak ang iyong itakda. Para baguhin ito, baguhin ang bungkos ng file ng iyong app sa `info.plist`. Tingnan ang [About Information Property List Files](https://developer.apple.com/library/ios/documentation/general/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html) para sa karagdagang impormasyon.

## Itinatakda ang Menu para sa Specific Browser Window ng (*Linux* *Windows*)

Ang [`setMenu` method](https://github.com/electron/electron/blob/master/docs/api/browser-window.md#winsetmenumenu-linux-windows) ng browser windows ay kayang itakda ang menu ng tiyak na browser windows.

## Posisyon ng Item ng Menu

Maaari kang gumamit ng `position` at `id` para makontrol kung paano ilalagay ang mga item kapag bumubuo ng isang menu sa pamamagitan ng `Menu.buildFromTemplate`.

Ang katangian ng `position` ng `MenuItem` ay may anyo na `[placement]=[id]`, kung saan `placement` ay isa sa `before`, `after`, o `endof` at `id` ay ang natatanging ID ng isang umiiral na item sa menu:

* `before` - Isisingit ang item na ito bago ang id ng isinangguning item. Kung ang isinangguning item ay hindi umiiral ang item ay ilalagay sa hulihan ng menu.
* `after` - Isisingit ang item na ito pagkatapos ng id ng isinangguning item, Kung ang isinangguning item ay hindi umiiral ang item ay ilalagay sa hulihan ng menu.
* `endof` - Isisingit ang item na ito sa hulihan ng lohikal na grupo na naglalaman ng id ng isinangguning item (ang mga grupo ay ginawa nang taga-hiwalay ng mga item). Kung ang isinangguning aytem ay hindi umiiral, isang bagong grupo ng taga-hiwalay ay lilikhain kasama ang ibinigay na id at ang aytem na ito ay ilalagay pagkatapos nang taga-hiwalay.

Kapag ang aytem ay naka-posisyon na, lahat ng hindi naka-posisyon na mga aytem ay ilalagay pagkatapos nito hanggang ang isang bagong aytem ay nai-posisyon na. Kaya kung gusto mong i-posisyon ang isang grupo ng mga aytem ng menu sa kaparehas na lokasyon kailangan mo lang tukuyin ang posisyon para sa unang aytem.

### Mga halimbawa

Ang Template:

```javascript
[
  {label: '4', id: '4'},
  {label: '5', id: '5'},
  {label: '1', id: '1', posisyon: 'before=4'},
  {label: '2', id: '2'},
  {label: '3', id: '3'}
]
```

Ang Menu:

```sh
<br />- 1
- 2
- 3
- 4
- 5
 
Context | Request Context
```

Ang Template:

```javascript
[
  {label: 'a', position: 'endof=letters'},
  {label: '1', position: 'endof=numbers'},
  {label: 'b', position: 'endof=letters'},
  {label: '2', position: 'endof=numbers'},
  {label: 'c', position: 'endof=letters'},
  {label: '3', position: 'endof=numbers'}
]
```

Ang Menu:

```sh
<br />- ---
- a
- b
- c
- ---
- 1
- 2
- 3
```