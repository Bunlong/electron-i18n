# Integración del entorno de escritorio

Diferentes sistemas operativos proporcionan diferentes funciones para integrar al escritorio aplicaciones en sus entornos. Por ejemplo, en Windows, las aplicaciones pueden poner accesos directos en JumpList de la barra de tareas, y en Mac, las aplicaciones pueden poner un menú personalizado en el menú de base.

Esta guía explica cómo integrar su aplicación en los entornos de esas computadoras de escritorio con las APIs de Electron.

## Notificaciones

Ver [Notificaciones](notifications.md)

## Documentos recientes (Windows & macOS)

Windows y macOS brindan fácil acceso a una lista de documentos recientes abiertos por la aplicación a través de JumpList o menú de base, respectivamente.

**JumpList:**

![JumpList Recent Files](https://cloud.githubusercontent.com/assets/2289/23446924/11a27b98-fdfc-11e6-8485-cc3b1e86b80a.png)

**Application dock menu:**

<img src="https://cloud.githubusercontent.com/assets/639601/5069610/2aa80758-6e97-11e4-8cfb-c1a414a10774.png" height="353" width="428" />

Para añadir archivos a documento recientes, puede usar el API [app.addRecentDocument](../api/app.md#appaddrecentdocumentpath-macos-windows):

```javascript
const {app} = require('electron')
app.addRecentDocument('/Users/USERNAME/Desktop/work.type')
```

Y puede usar la API [app.clearRecentDocuments](../api/app.md#appclearrecentdocuments-macos-windows) para vaciar la lista de documentos recientes:

```javascript
const {app} = require('electron')
app.clearRecentDocuments()
```

### Notas de Windows

Para poder usar esta característica en Windows, su aplicación debe ser registrado como un controlador del tipo de archivo del documento; de lo contrario, el archivo no aparecerá en JumpList incluso después de que usted lo haya agregado. Puedes encontrar todo al registrar su aplicación en [Registro de solicitud](https://msdn.microsoft.com/en-us/library/windows/desktop/ee872121(v=vs.85).aspx).

Cuando un usuario hace clic en un archivo de JumpList, una nueva instancia de su aplicación se iniciará con la ruta del archivo agregado como un argumento de línea de comando.

### notas de macOS

Cuando se solicita un archivo del menú de documentos recientes, el evento `archivo abierto` de `aplicación` se emitirá para él.

## Menú de base personalizada (macOS)

macOS permite a los desarrolladores especificar un menú personalizado para el dock, que generalmente contiene algunos accesos directos para las funciones de uso común de su aplicación:

**Menu de Dock de Terminal.app:**

<img src="https://cloud.githubusercontent.com/assets/639601/5069962/6032658a-6e9c-11e4-9953-aa84006bdfff.png" height="354" width="341" />

Para configurar su menú de base personalizado, puede usar la API `app.dock.setMenu`, que sólo está disponible en macOS:

```javascript
const {app, Menu} = require('electron')

const dockMenu = Menu.buildFromTemplate([
  {label: 'New Window', click () { console.log('New Window') }},
  {label: 'New Window with Settings',
    submenu: [
      {label: 'Basic'},
      {label: 'Pro'}
    ]
  },
  {label: 'New Command...'}
])
app.dock.setMenu(dockMenu)
```

## Tareas de usuario (Windows)

En Windows, puede especificar acciones personalizadas en la categoría `Tareas` de JumpList, como se cita en MSDN:

> Las aplicaciones definen tareas basadas tanto en las características del programa como en las cosas claves que se espera que haga un usuario con ellas. Las tareas deben estar libres de contexto, en que la aplicación no necesita ejecutarse para que funcione. También deberían ser las acciones estadísticamente más comunes que un usuario normal llevaría a cabo en una aplicación, como redactar un mensaje de correo electrónico o abrir el calendario en un programa de correo, crear un nuevo documento en un procesador de texto, iniciar una aplicación en un determinado modo, o ejecuta uno de sus subcomandos. Una aplicación no debe saturar el menú con características avanzadas que los usuarios estándar no necesitarán o acciones únicas como el registro. No utilice tareas para artículos promocionales como actualizaciones u ofertas especiales.
> 
> Se recomienda encarecidamente que la lista de tareas sea estática. Debería permanecer igual independientemente del estatus o estado de la aplicación. Si bien es posible variar la lista de forma dinámicamente, debe considerar que esto podría confundir al usuario que no espera que esa parte de la lista de destinos cambie.

**Tareas de Internet Explorer:**

![IE](http://i.msdn.microsoft.com/dynimg/IC420539.png)

A diferencia del menú Dock en macOS, que es un menú real, las tareas de usuario en Windows funcionan como accesos directos a aplicaciones, de modo que cuando el usuario hace clic en una tarea, se ejecuta un programa con argumentos especificados.

Para usar tareas para su aplicación, puede usar la API [app.setUserTasks](../api/app.md#appsetusertaskstasks-windows):

```javascript
const {app} = require('electron')
app.setUserTasks([
  {
    program: process.execPath,
    arguments: '--new-window',
    iconPath: process.execPath,
    iconIndex: 0,
    title: 'New Window',
    description: 'Create a new window'
  }
])
```

Para limpiar su lista de tareas, solo llame `app.setUserTasks` con un arreglo vacío:

```javascript
const {app} = require('electron')
app.setUserTasks([])
```

Las tareas del usuario aún se mostrará después de que la aplicación se cierre, de esta manera el ícono y ruta del programa especificado para su tarea exista aún así la aplicación haya sido desinstalada.

## Barra de tareas de Thumbnail

En Windoes usted puede añadir una barra de tareas thumbnail con botones específicos en un diseñador de barras de tareas de una ventana de un aplicación. Le da a los usuarios una manera de acceder a una comando de ventana en particular sin tener que restaurar o activar la ventana.

Para MSDN, está ilustrado:

> Esta barra de herramientas es simplemente la barra de tareas estándar de control común. Tiene un máximo de siete botones. Cada botón de identificación, imagen, descripción y estado está definido en una estructura, la cual es pasada a la barra de tareas. La aplicación puede mostrar, habilitar, deshabilitar u ocultar botones de la barra de herramientas si es requerido por su estado actual.
> 
> Por ejemplo, el reproductor de Windows ofrece regularmente controles de transporte como play, pausa, silenciar y detener.

**Barra de herramientas del reproductor de media de Windows:**

![intérprete](https://i-msdn.sec.s-msft.com/dynimg/IC420540.png)

Usted puede usar [BrowserWindow.setThumbarButtons](../api/browser-window.md#winsetthumbarbuttonsbuttons-windows) para configurar la barra de herramientas en miniatura en su aplicación:

```javascript
const {BrowserWindow} = require('electron')
const path = require('path')

let win = new BrowserWindow({
  width: 800,
  height: 600
})

win.setThumbarButtons([
  {
    tooltip: 'button1',
    icon: path.join(__dirname, 'button1.png'),
    click () { console.log('button1 clicked') }
  },
  {
    tooltip: 'button2',
    icon: path.join(__dirname, 'button2.png'),
    flags: ['enabled', 'dismissonclick'],
    click () { console.log('button2 clicked.') }
  }
])
```

Para limpiar los botones de su barra de herramientas en miniatura, llame `BrowserWindow.setThumbarButtons` con un arreglo vacío:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setThumbarButtons([])
```

## Acceso directos unidad Laucher (Linux)

En unity, usted puede añadir entradas personalizadas a su Launcher modificando el archivo `.escritorio`, vea [Añadiendo accesos directos al luncher](https://help.ubuntu.com/community/UnityLaunchersAndDesktopFiles#Adding_shortcuts_to_a_launcher).

**Accesos directos del Launcher de Audacious:**

![audaz](https://help.ubuntu.com/community/UnityLaunchersAndDesktopFiles?action=AttachFile&do=get&target=shortcuts.png)

## Barra de progreso en la barra de tareas (Windows, macOS, Unity)

En Windows una botón en la barra de tareas puede ser usado para mostrar una barra de progreso. Esto habilita a windows de informar sobre el progreso del proceso al usuario sin que este tenga que cambiar a la ventana misma.

En macOS la barra de progreso será mostrada como una parte del ícono del dock.

El Unity DE también tiene una función similar que le permite especificar la barra de progreso en el laucher.

**Barra de progreso en el botón de la barra de tareas:**

![Barra de progreso de la barra de tareas](https://cloud.githubusercontent.com/assets/639601/5081682/16691fda-6f0e-11e4-9676-49b6418f1264.png)

Para configurar la barra de progreso para una ventana, puede usar la API [BrowserWindow.setProgressBar](../api/browser-window.md#winsetprogressbarprogress):

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setProgressBar(0.5)
```

## Encubrimiento de íconos en la barra de tareas (Windows)

En Windows un botón de la barra de tareas puede usar un pequeño encubrimiento para mostrar el estatus de la aplicación, como es mencionado por la MSDN:

> El encubrimiento de íconos sirve como una notificación contextual del estatus, y tiene la intención de negar la necesidad de separar el ícono de la notificación del estatus del ícono para comunicar la información al usuario. Por ejemplo, el nuevo estatus de correos en Microsoft Outlook, actualmente se muestra en el área de notificaciones, ahora puede ser indicado a través de un encubrimiento en el botón de la barra de tareas. De nuevo, debes decidir durante su ciclo de desarrollo cual método es el mejor para su aplicación. El encubrimiento de íconos tiene la intención de dar información importante, estatus de larga duración o notificaciones así como estado de la red o de mensajería, o nuevos correos. El usuario no debe ser presentado con cambios constantes en el encubrimiento o animaciones.

**Encubrimiento en botones de la barra de tareas:**

![Encubrimiento en botones de la barra de tareas](https://i-msdn.sec.s-msft.com/dynimg/IC420441.png)

Para configurar el encubrimiento de un ícono para una ventana tiene que usar el API [BrowserWindow.setOverlayIcon](../api/browser-window.md#winsetoverlayiconoverlay-description-windows):

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setOverlayIcon('path/to/overlay.png', 'Description for overlay')
```

## Marco de flash (Windows)

En Windows puede resaltar el botón de barras de tarea para obtener la atención del usuario. Esto es similar al ícono de dock que rebota en macOS. Desde la documentación de referencia de MSDN:

> Por lo general, una ventana está iluminada para informar al usuario que la ventana requiere atención pero este no está concentrado en el teclado en el momento.

Para iluminar el botón de la barra de tareas del buscador windows, usted puede usar el API [BrowserWindow.flashFrame](../api/browser-window.md#winflashframeflag):

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.once('focus', () => win.flashFrame(false))
win.flashFrame(true)
```

No olvide llamar el método `flashFrame` con `false` a apagar la iluminación. En el ejemplo anterior, se llama cuando la ventana entra en foco, pero es posible que use un tiempo de espera u otro evento para deshabilitarlo.

## Archivo Representado de Window (macOS)

En macOS, una ventana puede establecer su archivo representado, de modo que el icono del archivo se muestre en la barra de título y cuando los usuarios hagan clic con el botón Comando o haga clic con el botón Control en el título, se muestre una ventana emergente de ruta.

También puede establecer el estado editado de una ventana para que el ícono del archivo pueda indicar si el documento en esta ventana ha sido modificado.

**Menú emergente de archivo representado:**

<img src="https://cloud.githubusercontent.com/assets/639601/5082061/670a949a-6f14-11e4-987a-9aaa04b23c1d.png" height="232" width="663" />

Para configurar el archivo representado de la ventana, puede utilizarse las APIs [BrowserWindow.setRepresentedFilename](../api/browser-window.md#winsetrepresentedfilenamefilename-macos) y [BrowserWindow.setDocumentEdited](../api/browser-window.md#winsetdocumenteditededited-macos) APIs:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.setRepresentedFilename('/etc/passwd')
win.setDocumentEdited(true)
```

## Arrastrando archivos fuera de la ventana

Para ciertos tipos de aplicaciones que manipulan archivos, es importante poder arrastrar archivos de Electron a otras aplicaciones. To implement this feature in your app, you need to call `webContents.startDrag(item)` API on `ondragstart` event.

En página web:

```html
<a href="#" id="drag">item</a>
<script type="text/javascript" charset="utf-8">
  document.getElementById('drag').ondragstart = (event) => {
    event.preventDefault()
    ipcRenderer.send('ondragstart', '/path/to/item')
  }
</script>
```

En el proceso principal:

```javascript
const {ipcMain} = require('electron')
ipcMain.on('ondragstart', (event, filePath) => {
  event.sender.startDrag({
    file: filePath,
    icon: '/path/to/icon.png'
  })
})
```