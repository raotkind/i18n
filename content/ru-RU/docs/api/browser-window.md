# BrowserWindow

> Создавайте окна браузера и управляйте ими.

Процесс: [Основной](../glossary.md#main-process)

```javascript
// В основном процессе.
const { BrowserWindow } = require('electron')

// Или используйте 'remote' в графическом процессе.
// const { BrowserWindow } = require('electron').remote

let win = new BrowserWindow({ width: 800, height: 600 })
win.on('closed', () => {
  win = null
})

// Загрузить удаленный URL
win.loadURL('https://github.com')

// Или загрузить локальный HTML-файл
win.loadURL(`file://${__dirname}/app/index.html`)
```

## Окно без рамки

Для создания окна без хрома или прозрачного окна произвольной формы, можно использовать API [окна без рамки](frameless-window.md).

## Изящный показ окон

Когда страница загружается в окно напрямую, пользователи могут видеть ступенчатую загрузку страницы, что является дурным тоном для нативного приложения. Для создания окна без ступенчатой загрузки существует два решения, которые можно использовать в различных ситуациях.

### Использование `ready-to-show` события

При загрузке страницы, после отрисовки страницы будет происходить событие `ready-to-show`, которое будет происходить первый раз, если окно до этого еще не было показано. Окно, показанное после этого события, не будет иметь визуальной ступенчатой подгрузки:

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow({ show: false })
win.once('ready-to-show', () => {
  win.show()
})
```

Обычно это событие происходит после события `did-finish-load`. Однако, страницы, включающие в себя удаленные ресурсы, могут продолжать подгружаться после происхождения события `did-finish-load`.

### Настройка `backgroundColor`

Для больших приложений событие `ready-to-show` может вызываться слишком поздно, что может замедлить приложение. В этом случае рекомендуется показать окно немедленно, и использовать `backgroundColor`, задающий цвет фона Вашего приложения:

```javascript
const { BrowserWindow } = require('electron')

let win = new BrowserWindow({ backgroundColor: '#2e2c29' })
win.loadURL('https://github.com')
```

Обратите внимание, что даже для приложений, использующих `ready-to-show` события, по-прежнему рекомендуется установить `backgroundColor`, чтобы сделать приложение более нативным.

## Родительские и дочерние окна

С помощью параметра `parent`, Вы можете создавать дочерние окна:

```javascript
const { BrowserWindow } = require('electron')

let top = new BrowserWindow()
let child = new BrowserWindow({ parent: top })
child.show()
top.show()
```

Окно `child` будет всегда показано поверх окна `top`.

### Модальные окна

Модальное окно - дочернее окно, которое делает недоступным родительское окно. Чтобы создать модальное окно, Вы должны установить два параметра `parent` и `modal`:

```javascript
conts { BrowserWindow } = require('electron')

let child = new BrowserWindow({ parent: top, modal: true, show: false })
child.loadURL('https://github.com')
child.once('ready-to-show', () = > {
  child.show()
})
```

### Видимость страниц

[API видимости страниц](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API) работает следующим образом:

* На всех платформах состояние видимости отслеживает скрыто/уменьшено окно или нет.
* Кроме того, на macOS, состояние видимости также отслеживает состояние перекрытия окна. Если окно перекрыто (т.е. полностью покрыто) другим окном, состояние видимости будет `hidden`. На других платформах состояние видимости будет `hidden`, только когда окно уменьшено или явно скрыто при помощи `win.hide()`.
* Если `BrowserWindow` создано с `show: false`, первоначальное состояние видимости будет `visible`, несмотря на фактически скрытое окно.
* Если `backgroundThrottling` отключено, состояние видимости останется `visible`, даже если окно уменьшено, закрыто или скрыто.

Рекомендуется приостановить дорогостоящие операции, когда состояние видимости `hidden`, для того чтобы свести к минимуму потребление энергии.

### Платформа заметок

* На macOS модальные окна будут отображены в виде страниц, прикрепленных к родительскому окну.
* На macOS дочерние окна будут находиться относительно родительского окна, во время передвижения родительского окна, тем временем на Windows и Linux дочерние окна не будут двигаться.
* На Linux тип модального окна будет поменян на `dialog`.
* На Linux многие среды рабочего стола не поддерживают скрытие модального окна.

## Класс: BrowserWindow

> Создавайте окна браузера и управляйте ими.

Процесс: [Основной](../glossary.md#main-process)

`BrowserWindow` это [EventEmitter](https://nodejs.org/api/events.html#events_class_events_eventemitter).

Так создается новый экземпляр `BrowserWindow` с нативными свойствами, установленными в `options`.

### `new BrowserWindow([options])`

* `options` Object (опционально) 
  * `width` Integer (опционально) - ширина окна в пикселях. По умолчанию - `800`.
  * `height` Integer (опционально) - высота окна в пикселях. По умолчанию - `600`.
  * `x` Integer (опционально) (**обязателен**, если используется y) - отступ окна слева от экрана. Значение по умолчанию центрирует окно.
  * `y` Integer (опционально) (**обязателен**, если используется x) - отступ окна сверху от экрана. Значение по умолчанию центрирует окно.
  * `useContentSize` Boolean (опционально) - `width` и `height` могут использоваться как размеры веб-страницы, это значит, что актуальный размер окна будет включать размер фрейма и будет немного крупнее. По умолчанию - `false`.
  * `center` Boolean (опционально) - показывает окно в центре экрана.
  * `minWidth` Integer (опционально) - минимальная ширина окна. По умолчанию - `0`.
  * `minHeight` Integer (опционально) - минимальная высота окна. По умолчанию - `0`.
  * `maxWidth` Integer (опционально) - максимальная ширина окна. По умолчанию - без ограничений.
  * `maxHeight` Integer (опционально) - максимальная высота окна. По умолчанию - без ограничений.
  * `resizable` Boolean (опционально) - будет ли окно изменять размеры. По умолчанию - `true`.
  * `movable` Boolean (опционально) - будет ли окно перемещаться. Не реализовано на Linux. По умолчанию - `true`.
  * `minimizable` Boolean (опционально) - будет ли окно сворачиваться. Не реализовано на Linux. По умолчанию - `true`.
  * `maximazable` Boolean (опционально) - будет ли окно разворачиваться. Не реализовано на Linux. По умолчанию - `true`.
  * `closable` Boolean (опционально) - возможность закрывать окно. Не реализовано на Linux. По умолчанию - `true`.
  * `focusable` Boolean (опционально) - может ли быть окно в фокусе. По умолчанию - `true`. На Windows настройка `focusable: false` также подразумевает настройку `skipTaskbar: true`. На Linux настройка `focusable: false` прекращает взаимодействие окна с оконным менеджером, на Windows же всегда остается поверх всех рабочих областей.
  * `alwaysOnTop` Boolean (опционально) - будет ли окно всегда оставаться поверх других окон. По умолчанию - `false`.
  * `fullscreen` Boolean (опционально) - будет ли окно показываться во весь экран. Когда явно установлено `false`, на macOS кнопка полноэкранного режима будет скрыта или отключена. По умолчанию - `false`.
  * `fullscreenable` Boolean (опционально) - может ли окно быть в полноэкранном режиме. На macOS также кнопка увеличить/зумировать должна переключить в полноэкранный режим или увеличить окно. По умолчанию - `true`.
  * `simpleFullscreen` Boolean (опционально) - использовать полноэкранный режим на macOS в представленном до версии Lion варианте реализации. По умолчанию - `false`.
  * `skipTaskbar` Boolean (опционально) - будет ли показано окно в панели задач. По умолчанию - `false`.
  * `kiosk` Boolean (опционально) - режим киоска. По умолчанию - `false`.
  * `title` String (опционально) - заголовок окна по умолчанию. По умолчанию `"Electron"`. Если HTML-тег `<title>` определен в HTML-файле, загруженном с помощью `loadURL()`, то это свойство будет игнорироваться.
  * `icon` ([NativeImage](native-image.md) | String) (опционально) - иконка окна. На Windows рекомендуется использовать иконки `ICO`, чтобы получить лучший визуальный эффект, Вы также можете оставить неопределенным, чтобы был использован значок исполняемого файла.
  * `show` Boolean (опционально) - будет ли показано окно, когда будет создано. По умолчанию - `true`.
  * `frame` Boolean (опционально) - установите `false`, чтобы создать [окно без рамки](frameless-window.md). По умолчанию - `true`.
  * `parent` BrowserWindow (опционально) - устанавливает родительское окно. По умолчанию - `null`.
  * `modal` Boolean (опционально) - будет ли окно модальным. Работает только, когда окно является дочерним окном. По умолчанию - `false`.
  * `acceptFirstMouse` Boolean (опционально) - будет ли веб-окно принимать событие одиночного нажатия мыши, которое сразу активирует окно. По умолчанию - `false`.
  * `disableAutoHideCursor` Boolean (опционально) - будет ли спрятан курсор, во время печатания. По умолчанию - `false`.
  * `autoHideMenuBar` Boolean (опционально) - автоматическое убирание полоски меню, пока клавиша `Alt` не будет нажата. По умолчанию - `false`.
  * `enableLargerThanScreen` Boolean (опционально) - позволяет окну изменять размер больше, чем экран. По умолчанию - `false`.
  * `backgroundColor` String (опционально) - фоновый цвет окна, в виде шестнадцатиричной системы счисления, например, `#66CD00`, `#FFF` или `#80FFFFFF` (альфа в формате #AARRGGBB поддерживается, если `transparent` установлено `true`). По умолчанию `#FFF` (белый).
  * `hasShadow` Boolean (опционально) - будет ли окно иметь тень. Реализовано только на macOS. По умолчанию - `true`.
  * `opacity` Number (опционально) - установить начальную прозрачность окна, между 0.0 (полная прозрачность) и 1.0 (полная видимость). Это реализовано только на Windows и macOS.
  * `darkTheme` Boolean (опционально) - заставляет использовать темную тему для окна, работает только на некоторых GTK+3 окружениях рабочего стола. По умолчанию - `false`.
  * `transparent` Boolean (опционально) - делает окно [прозрачным](frameless-window.md). По умолчанию - `false`.
  * `type` String (опционально) - тип окна, по умолчанию - обычное окно. См. больше об этом ниже.
  * `titleBarStyle` String (опционально) - стиль полосы заголовка окна. По умолчанию - `default`. Возможные значения: 
    * `default` - в результате стандартный, серый, непрозрачный Mac заголовок.
    * `hidden` - в результате скрытый заголовок и содержимое во все окно, но заголовок по-прежнему имеет стандартное окно контроля ("светофоры") сверху слева.
    * `hiddenInset` - в результате скрытый заголовок с альтернативным видом, где кнопки контролирования немного больше отступает от края окна.
    * `customButtonsOnHover` Boolean (опционально) - отобразить пользовательские кнопки закрыть и свернуть на macOS окнах без рамки. Эти кнопки будут отображены только при наведении на верхний левый угол окна. Эти пользовательские кнопки предотвращают проблемы с событиями мыши, которые случаются со стандартными кнопками панели инструментов. **Примечание:** Этот параметр в настоящее время экспериментален.
  * `fullscreenWindowTitle` Boolean (опционально) - показывает название в заголовке в полноэкранном режиме на macOS для всех вариантов `titleBarStyle`. По умолчанию `false`.
  * `thickFrame` Boolenan (опционально) - использовать стиль `WS_THICKFRAME` на окнах без рамок на Windows, добавляющий стандартные рамки окна. Установив значение `false`, тень окна и анимация окна будут удалены. По умолчанию - `true`.
  * `vibrancy` String (опционально) - добавить тип эффекта вибрации к окну, только на macOS. Может быть `appearance-based`, `light`, `dark`, `titlebar`, `selection`, `menu`, `popover`, `sidebar`, `medium-light`, или `ultra-dark`. Обратите внимание, что использование `frame: false` в комбинации с меняющимся значением, требует так же указывать `titleBarStyle`.
  * `zoomToPageWidth` Boolean (опционально) - управляет поведением на macOS, при нажатии на зеленую кнопку на панели инструментов или при нажатии на пункт меню Окно > Увеличить. Если `true`, окно будет увеличиваться до предпочтительной ширины веб-страницы при увеличении, `false` приведет к увеличению масштаба до ширины экрана. Это также повлияет на поведение при вызове `maximize()` напрямую. По умолчанию - `false`.
  * `tabbingIdentifier` String (опционально) - название группы вкладок, позволяет открывать окно как нативную вкладку в macOS 10.12+. Окна с одинаковым идентификатором вкладки будут сгруппированы вместе. Это также добавляет новую нативную кнопку вкладки в панель вкладок вашего окна и позволяет Вашему `приложению` и окну получать событие `new-window-for-tab`.
  * `webPreferences` Object (опционально) - настройки веб-страниц. 
    * `devTools` Boolean (опционально) - включает инструменты разработчика. Если значение `false`, нельзя будет использовать `BrowserWindow.webContents.openDevTools()`, чтобы открыть инструменты разработчика. По умолчанию - `true`.
    * `nodeIntegration` Boolean (опционально) - включает интеграцию NodeJS. По умолчанию - `false`.
    * `nodeIntegrationInWorker` Boolean (опционально) - включает интеграцию NodeJS в веб-воркерах. По умолчанию - `false`. Больше об этом можно найти в [многопоточности](../tutorial/multithreading.md).
    * `nodeIntegrationInSubFrames` Boolean (опционально) - экспериментальная опция для включения поддержки NodeJS в подфреймах, таких как iframes. Все Ваши предварительные загрузки будут загружены для каждого iframe, Вы можете использовать `process.isMainFrame`, чтобы определить в главном фрейме Вы или нет.
    * `preload` String (опционально) - определяет скрипт, который будет загружен до того, как остальные скрипты запустятся на странице. Этот скрипт будет всегда иметь доступ к API NodeJS, вне зависимости включена или выключена интеграция NodeJS. Значение должно быть абсолютным путем к файлу скрипта. Когда интеграция NodeJS отключена, предварительно загруженный скрипт может повторно ввести глобальные символы NodeJS в глобальную область. Смотрите пример [здесь](process.md#event-loaded).
    * `sandbox` Boolean (опционально) - если установлено true, то в окне будет запущена песочница, что делает ее совместимой с песочницей Chromium на уровне операционной системы, и отключает движок NodeJS. Это не тоже самое, что параметр `nodeIntegration`, доступные API для предзагруженных скриптов более ограничены. Узнать больше об этой опции можно [здесь](sandbox-option.md). **Примечание:** Эта опция в настоящее время экспериментальная и может поменяться или быть удалена в будущих Electron релизах.
    * `enableRemoteModule` Boolean (опционально) - включает модуль [`remote`](remote.md). По умолчанию - `true`.
    * `session` [Session](session.md#class-session) (опционально) - устанавливает сессию, которая используется страницей. Вместо передачи экземпляр Session напрямую, вместо этого Вы можете также выбрать использование опции `partition`, которая принимает строку раздела. Когда оба `session` и `partition` определены, `session` будет предпочтительней. По умолчанию используется сессия по умолчанию.
    * `partition` String (опционально) - устанавливает сессию, используемую на странице в соответствии со строкой раздела сессии. Если `partition` начинается с `persist:`, страница будет использовать постоянную сессию, которая доступна всем страницам в приложении с тем же `разделом`. Если нет префикса `persist:`, страница будет использовать сессию в памяти. При присваивании одинакового `раздела`, разные страницы могут иметь одинаковую сессию. По умолчанию используется сессия по умолчанию.
    * `affinity` String (опционально) - когда определено, веб-страницы с одинаковым `родством` будут работать в том же графическом процессе. Обратите внимание, что из-за повторного использования графического процесса, некоторые параметры `webPreferences` также будут доступны между веб-страницами, даже если Вы указали для них разные значения, включая, но не ограничиваясь, `preload`, `sandbox` и `nodeIntegration`. Поэтому рекомендуется использовать те же `webPreferences` для веб-страниц с одинаковым `родством`. *Это экспериментальное свойство*
    * `zoomFactor` Number (опционально) - коэффициент масштабирования на странице, `3.0` означает `300%`. По умолчанию `1.0`.
    * `javascript` Boolean (опционально) - включает поддержку JavaScript. По умолчанию `true`.
    * `webSecurity` Boolean (опционально) - когда `false`, отключается политика same-origin (обычно используется при тестировании веб-сайтов людьми), и устанавливается `allowRunningInsecureContent` в `true`, если параметр не был установлен пользователем. По умолчанию - `true`.
    * `allowRunningInsecureContent` Boolean (опционально) - позволяет https-страницам запускать JavaScript, CSS или плагины из URL с протоколом http. По умолчанию `false`.
    * `images` Boolean (опционально) - включает поддержку изображений. По умолчанию `true`.
    * `textAreasAreResizable` Boolean (опционально) - позволяет изменять размер у элементов TextArea. По умолчанию `true`.
    * `webgl` Boolean (опционально) - включает поддержку WebGL. По умолчанию `true`.
    * `webaudio` Boolean (optional) - включает поддержку WebAudio. По умолчанию `true`.
    * `plugins` Boolean (опционально) - включает поддержку плагинов. По умолчанию `false`.
    * `experimentalFeatures` Boolean (опционально) - включает экспериментальные возможности Chromium. По умолчанию `false`.
    * `scrollBounce` Boolean (опционально) - включает эффект отскока при прокрутке на macOS. По умолчанию `false`.
    * `enableBlinkFeatures` String (optional) - список возможностей, разделенных `запятой`, которые нужно включить, например `CSSVariables,KeyboardEventKey`. Полный список поддерживаемых возможностей можно найти в файле [RuntimeEnabledFeatures.json5](https://cs.chromium.org/chromium/src/third_party/blink/renderer/platform/runtime_enabled_features.json5?l=70).
    * `disableBlinkFeatures` String (опционально) - список возможностей, разделенных `запятой`, которые нужно выключить, например `CSSVariables,KeyboardEventKey`. Полный список поддерживаемых возможностей можно найти в файле [RuntimeEnabledFeatures.json5](https://cs.chromium.org/chromium/src/third_party/blink/renderer/platform/runtime_enabled_features.json5?l=70).
    * `defaultFontFamily` Object (опционально) - устанавливает шрифт по умолчанию для семейства шрифтов. 
      * `standard` String (опционально) - по умолчанию `Times New Roman`.
      * `serif` String (опционально) - по умолчанию `Times New Roman`.
      * `sansSerif` String (опционально) - по умолчанию `Arial`.
      * `monospace` String (опционально) - по умолчанию `Courier New`.
      * `cursive` String (опционально) - по умолчанию `Script`.
      * `fantasy` String (опционально) - по умолчанию `Impact`.
    * `defaultFontSize` Integer (опционально) - по умолчанию `16`.
    * `defaultMonospaceFontSize` Integer (опционально) - по умолчанию `13`.
    * `minimumFontSize` Integer (опционально) - по умолчанию `0`.
    * `defaultEncoding` String (опционально) - по умолчанию `ISO-8859-1`.
    * `backgroundThrottling` Boolean (опционально) - предотвратить анимации и таймеры, когда страница становится фоновой. Это также влияет на [API видимости страницы](#page-visibility). По умолчанию `true`.
    * `offscreen` Boolean (опционально) - включает отрисовку окна браузера вне экрана. По умолчанию `false`. Смотрите [руководство отрисовки вне экрана](../tutorial/offscreen-rendering.md) для подробной информации.
    * `contextIsolation` Boolean (опционально) - запускать или нет API Electron и определенный скрипт `предварительной загрузки` в отдельном JavaScript-контексте. По умолчанию `false`. Контекст, в котором скрипт `предварительной загрузки` будет запускаться, имеет полный доступ к глобальным объектам `document` и `window`, но будет использовать собственный набор JavaScript-типов (`Array`, `Object`, `JSON`, и т.д.) и будет изолирован от любых изменений, сделанных в глобальной среде, загруженной страницы. API Electron будет доступен только в скрипте `предварительной загрузки` и не будет доступен загруженной странице. Эта опция должна быть использована, когда загружается потенциально ненадежный удаленный контент, чтобы убедиться, что загруженный контент не сможет изменить скрипт `предварительной загрузки` и любой API Electron, который используется. Эта опция использует ту же самую технику, что используется в [контенте скриптов Chrome](https://developer.chrome.com/extensions/content_scripts#execution-environment). Вы можете получить доступ к этому контексту в инструментах разработчика, при выборе пункта 'Изолированный Контекст Elctron' в списке на верхней части консольной вкладки.
    * `nativeWindowOpen` Boolean (опционально) - использовать нативную функцию `window.open()`. По умолчанию `false`. У дочерних окон всегда будет отключена интеграция NodeJS. **Примечание:** Эта опция в настоящее время экспериментальная.
    * `webviewTag` Boolean (опционально) - включает [`<webview>`-тег](webview-tag.md). По умолчанию `false`. **Примечание:** Cкрипт `предварительной загрузки`, настроенный для `<webview>`, будет иметь интеграцию NodeJS, когда будет запущен, так что Вы должны убедиться, что удаленный/непроверенный контент не может создавать тег `<webview>` с возможно вредоносным скриптом `предварительной загрузки`. Вы можете использовать событие `will-attach-webview` на [webContents](web-contents.md), чтобы снять скрипт `предварительной загрузки` и проверить или изменить начальные настройки `<webview>`.
    * `additionalArguments` String[] (опционально) - список строк, которые будут добавлены в `process.argv` в графическом процессе этого приложения. Полезно, для передачи небольших битов данных в скрипты предварительной загрузки графического процесса.
    * `safeDialogs` Boolean (опционально) - включает режим последовательной защиты диалогов. По умолчанию - `false`.
    * `safeDialogsMessage` String (опционально) - сообщение, которое будет отображено, когда сработает последовательная защита диалогов. Если не определено, будет использовано сообщение по умолчанию, обратите внимание, что текущее сообщение по умолчанию на английском и не переведено.
    * `navigateOnDragDrop` Boolean (опционально) - вызывает навигацию, когда файл или ссылка перетаскивается на страницу. По умолчанию - `false`.
    * `autoplayPolicy` String (опционально) - политика автовоспроизведения для применения к содержимому в окне, может быть `no-user-gesture-required`, `user-gesture-required` или `document-user-activation-required`. По умолчанию `no-user-gesture-required`.

Когда установлен минимальный или максимальный размер окна, при помощи `minWidth`/`maxWidth`/`minHeight`/`maxHeight`, это ограничивает только пользователей. Это не позволит Вам установить размер, который не будет следовать ограничениям размера, в `setBounds`/`setSize` или в конструкторе `BrowserWindow`.

Возможные значения и поведения опции `type` зависят от платформы. Возможные значения:

* На Linux возможны типы `desktop`, `dock`, `toolbar`, `splash`, `notification`.
* На macOS возможны типы `desktop`, `textured`. 
  * Тип `textured` добавляет вид металлического градиента (`NSTexturedBackgroundWindowMask`).
  * Тип `desktop` размещает окно на уровень фонового окна рабочего стола (`kCGDesktopWindowLevel - 1`). Обратите внимание, что окно рабочего стола не будет получить события фокуса, клавиатуры или мыши, но Вы можете использовать `globalShortcut`, чтобы получать ввод.
* На Windows возможен тип `toolbar`.

### События экземпляра

Объекты созданные с помощью `new BrowserWindow` имеют следующие события:

**Примечание:** Некоторые методы доступны только в определенных операционных системах и помечены как таковые.

#### Событие: 'page-title-updated'

Возвращает:

* `event` Event
* `title` String
* `explicitSet` Boolean

Происходит, когда документ меняет свой заголовок, вызов `event.preventDefault()` предотвратит изменение заголовка нативного окна. `explicitSet` является false, когда заголовок синтезирован из url файла.

#### Событие: 'close'

Возвращает:

* `event` Event

Происходит при закрытии окна. Оно происходит перед событиями `beforeunload` и `unload` в DOM. Вызов `event.preventDefault()` предотвратит закрытие.

Скорее всего, Вы захотите использовать обработчик `beforeunload`, чтобы решить, когда окно должно быть закрыто, который также будет вызываться, когда окно перезагружается. В Electron возврат любого значения, отличного от `undefined`, предотвратит закрытие. Например:

```javascript
window.onbeforeunload = (e) => {
  console.log('Я не хочу быть закрыт')

  // В отличие от браузеров, пользователю будет показано окно с сообщением.
  // Возврат любого значения незаметно отменит закрытие.
  // Рекомендуется использовать dialog API, чтобы дать пользователям
  // возможность подтвердить закрытие приложения.
  e.returnValue = false // идентично `return false`, но в использовании не рекомендуется
}
```

***Примечание**: Существует тонкая разница между поведением `window.onbeforeunload = handler` и `window.addEventListener('beforeunload', handler)`. Рекомендуется всегда устанавливать `event.returnValue` явно, вместо того, чтобы просто возвращать значение, поскольку первое работает более последовательно в Electron.*

#### Событие: 'closed'

Происходит, когда окно закрыто. После того, как Вы получили это событие, Вы должны удалить ссылку на окно и больше не использовать ее.

#### Событие: 'session-end' *Windows*

Происходит, когда сеанс окна заканчивается из-за выключения, перезагрузки компьютера или отключения сеанса.

#### Событие: 'unresponsive'

Происходит, когда страница "не отвечает".

#### Событие: 'responsive'

Происходит, когда страница, которая "не отвечала", снова реагирует.

#### Событие: 'blur'

Происходит, когда окно теряет фокус.

#### Событие: 'focus'

Происходит, когда на окне фокусируются.

#### Событие: 'show'

Происходит, когда отображается окно.

#### Событие: 'hide'

Происходит, когда окно спрятано.

#### Событие: 'ready-to-show'

Происходит, когда веб-страница была отрисована (пока не отображена) и окно может быть отображено без визуального мерцания.

#### Событие: 'maximize'

Происходит, когда окно увеличивается до предела.

#### Событие: 'unmaximize'

Происходит, когда окно выходит из увеличенного состояния.

#### Событие: 'minimize'

Происходит, когда окно было свернуто.

#### Событие: 'restore'

Происходит, когда окно восстанавливается из свернутого состояния.

#### Событие: 'will-resize' *macOS* *Windows*

Возвращает:

* `event` Event
* `newBounds` [`Rectangle`](structures/rectangle.md) - размер окна, на который будет изменено.

Происходит перед изменением размера окна. Вызов `event.preventDefault()` предотвратит изменение размера окна.

Обратите внимание, что это происходит, только когда размер окна будет изменяться вручную. Изменение размера окна с помощью `setBounds`/`setSize` не вызовет это событие.

#### Событие: 'resize'

Происходит после того, как изменился размер окна.

#### Событие: 'will-move' *Windows*

Возвращает:

* `event` Event
* `newBounds` [`Rectangle`](structures/rectangle.md) - расположение, куда окно будет перемещено.

Происходит перед перемещением окна. Вызов `event.preventDefault()` предотвратит перемещение окна.

Обратите внимание, что это происходит, только когда размер окна будет изменяться вручную. Изменение размера окна с помощью `setBounds`/`setSize` не вызовет это событие.

#### Событие: 'move'

Происходит, когда окно перемещено на новое место.

**Примечание**: На macOS это событие является псевдонимом `moved`.

#### Событие: 'moved' *macOS*

Происходит единожды, когда окно перемещается в новое положение.

#### Событие: 'enter-full-screen'

Происходит, когда окно переходит в полноэкранный режим.

#### Событие: 'leave-full-screen'

Происходит, когда окно выходит из полноэкранного режима.

#### Событие: 'enter-html-full-screen'

Происходит, когда окно входит в полноэкранный режим с помощью HTML API.

#### Событие: 'leave-html-full-screen'

Происходит, когда окно выходит из полноэкранного режима с помощью HTML API.

#### Событие: 'always-on-top-changed' *macOS*

Возвращает:

* `event` Event
* `isAlwaysOnTop` Boolean

Происходит, когда окно переключает режим отображения поверх всех окон.

#### Событие: 'app-command' *Windows* *Linux*

Возвращает:

* `event` Event
* `command` String

Происходит, когда вызывается [команда приложения](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx). Обычно это касается клавиатурных медиа-клавиш или команд браузера, а также кнопки "Назад", встроенной в некоторые мыши на Windows.

Команды в нижнем регистре, подчеркивание заменено на дефисы, а префикс `APPCOMMAND_` обрезан. например `APPCOMMAND_BROWSER_BACKWARD` происходит как `browser-backward`.

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow()
win.on('app-command', (e, cmd) => {
  // Возврат на предыдущий экран, когда пользователь мышкой нажимает кнопку "назад"
  if (cmd === 'browser-backward' && win.webContents.canGoBack()) {
    win.webContents.goBack()
  }
})
```

Следующие команды приложения явно поддерживаются на Linux:

* `browser-backward`
* `browser-forward`

#### Событие: 'scroll-touch-begin' *macOS*

Происходит, когда начинается прокрутка колеса.

#### Событие: 'scroll-touch-end' *macOS*

Происходит, когда заканчивается прокрутка колеса.

#### Событие: 'scroll-touch-edge' *macOS*

Происходит, когда прокрутка колеса достигает края элемента.

#### Событие: 'swipe' *macOS*

Возвращает:

* `event` Event
* `direction` String

Происходит при свайпе тремя пальцами. Возможные направления: `up`, `right`, `down` и `left`.

#### Событие: 'sheet-begin' *macOS*

Происходит, когда окно открывает лист.

#### Событие: 'sheet-end' *macOS*

Происходит, когда окно закрыло лист.

#### Событие: 'new-window-for-tab' *macOS*

Происходит, когда нажимается нативная кнопка новой вкладки.

### Статические методы

The `BrowserWindow` class has the following static methods:

#### `BrowserWindow.getAllWindows()`

Returns `BrowserWindow[]` - An array of all opened browser windows.

#### `BrowserWindow.getFocusedWindow()`

Returns `BrowserWindow | null` - The window that is focused in this application, otherwise returns `null`.

#### `BrowserWindow.fromWebContents(webContents)`

* `webContents` [WebContents](web-contents.md)

Возвращает `BrowserWindow` - окно, которое владеет указанным `webContents`.

#### `BrowserWindow.fromBrowserView(browserView)`

* `browserView` [BrowserView](browser-view.md)

Returns `BrowserWindow | null` - The window that owns the given `browserView`. If the given view is not attached to any window, returns `null`.

#### `BrowserWindow.fromId(id)`

* `id` Integer

Возвращает `BrowserWindow` - окно с указанным `id`.

#### `BrowserWindow.addExtension(path)`

* `path` String

Adds Chrome extension located at `path`, and returns extension's name.

The method will also not return if the extension's manifest is missing or incomplete.

**Примечание:** Этот метод должен вызываться только после события `ready` модуля `app`.

#### `BrowserWindow.removeExtension(name)`

* `name` String

Удаляет расширение Chrome с указанным именем.

**Примечание:** Этот метод должен вызываться только после события `ready` модуля `app`.

#### `BrowserWindow.getExtensions()`

Returns `Object` - The keys are the extension names and each value is an Object containing `name` and `version` properties.

**Примечание:** Этот метод должен вызываться только после события `ready` модуля `app`.

#### `BrowserWindow.addDevToolsExtension(path)`

* `path` String

Adds DevTools extension located at `path`, and returns extension's name.

The extension will be remembered so you only need to call this API once, this API is not for programming use. If you try to add an extension that has already been loaded, this method will not return and instead log a warning to the console.

The method will also not return if the extension's manifest is missing or incomplete.

**Примечание:** Этот метод должен вызываться только после события `ready` модуля `app`.

#### `BrowserWindow.removeDevToolsExtension(name)`

* `name` String

Удаляет расширение DevTools с указанным именем.

**Примечание:** Этот метод должен вызываться только после события `ready` модуля `app`.

#### `BrowserWindow.getDevToolsExtensions()`

Returns `Object` - The keys are the extension names and each value is an Object containing `name` and `version` properties.

To check if a DevTools extension is installed you can run the following:

```javascript
const { BrowserWindow } = require('electron')

let installed = BrowserWindow.getDevToolsExtensions().hasOwnProperty('devtron')
console.log(installed)
```

**Примечание:** Этот метод должен вызываться только после события `ready` модуля `app`.

### Свойства экземпляра

Objects created with `new BrowserWindow` have the following properties:

```javascript
const { BrowserWindow } = require('electron')
// In this example `win` is our instance
let win = new BrowserWindow({ width: 800, height: 600 })
win.loadURL('https://github.com')
```

#### `win.webContents`

A `WebContents` object this window owns. All web page related events and operations will be done via it.

See the [`webContents` documentation](web-contents.md) for its methods and events.

#### `win.id`

A `Integer` representing the unique ID of the window.

### Методы экземпляра

Objects created with `new BrowserWindow` have the following instance methods:

**Примечание:** Некоторые методы доступны только в определенных операционных системах и помечены как таковые.

#### `win.destroy()`

Force closing the window, the `unload` and `beforeunload` event won't be emitted for the web page, and `close` event will also not be emitted for this window, but it guarantees the `closed` event will be emitted.

#### `win.close()`

Try to close the window. This has the same effect as a user manually clicking the close button of the window. The web page may cancel the close though. See the [close event](#event-close).

#### `win.focus()`

Focuses on the window.

#### `win.blur()`

Removes focus from the window.

#### `win.isFocused()`

Returns `Boolean` - Whether the window is focused.

#### `win.isDestroyed()`

Returns `Boolean` - Whether the window is destroyed.

#### `win.show()`

Shows and gives focus to the window.

#### `win.showInactive()`

Shows the window but doesn't focus on it.

#### `win.hide()`

Скрывает окно.

#### `win.isVisible()`

Returns `Boolean` - Whether the window is visible to the user.

#### `win.isModal()`

Returns `Boolean` - Whether current window is a modal window.

#### `win.maximize()`

Maximizes the window. This will also show (but not focus) the window if it isn't being displayed already.

#### `win.unmaximize()`

Unmaximizes the window.

#### `win.isMaximized()`

Возвращает `Boolean` - если окно развернуто.

#### `win.minimize()`

Minimizes the window. On some platforms the minimized window will be shown in the Dock.

#### `win.restore()`

Restores the window from minimized state to its previous state.

#### `win.isMinimized()`

Возвращает `Boolean` - если окно свернуто.

#### `win.setFullScreen(flag)`

* `flag` Boolean

Sets whether the window should be in fullscreen mode.

#### `win.isFullScreen()`

Returns `Boolean` - Whether the window is in fullscreen mode.

#### `win.setSimpleFullScreen(flag)` *macOS*

* `flag` Boolean

Enters or leaves simple fullscreen mode.

Simple fullscreen mode emulates the native fullscreen behavior found in versions of Mac OS X prior to Lion (10.7).

#### `win.isSimpleFullScreen()` *macOS*

Returns `Boolean` - Whether the window is in simple (pre-Lion) fullscreen mode.

#### `win.isNormal()`

Returns `Boolean` - Whether the window is in normal state (not maximized, not minimized, not in fullscreen mode).

#### `win.setAspectRatio(aspectRatio[, extraSize])` *macOS*

* `aspectRatio` Float - The aspect ratio to maintain for some portion of the content view.
* `extraSize` [Size](structures/size.md) - The extra size not to be included while maintaining the aspect ratio.

This will make a window maintain an aspect ratio. The extra size allows a developer to have space, specified in pixels, not included within the aspect ratio calculations. This API already takes into account the difference between a window's size and its content size.

Consider a normal window with an HD video player and associated controls. Perhaps there are 15 pixels of controls on the left edge, 25 pixels of controls on the right edge and 50 pixels of controls below the player. In order to maintain a 16:9 aspect ratio (standard aspect ratio for HD @1920x1080) within the player itself we would call this function with arguments of 16/9 and [ 40, 50 ]. The second argument doesn't care where the extra width and height are within the content view--only that they exist. Sum any extra width and height areas you have within the overall content view.

Calling this function with a value of `0` will remove any previously set aspect ratios.

#### `win.setBackgroundColor(backgroundColor)`

* `backgroundColor` String - Window's background color as a hexadecimal value, like `#66CD00` or `#FFF` or `#80FFFFFF` (alpha is supported if `transparent` is `true`). Default is `#FFF` (white).

Sets the background color of the window. See [Setting `backgroundColor`](#setting-backgroundcolor).

#### `win.previewFile(path[, displayName])` *macOS*

* `path` String - The absolute path to the file to preview with QuickLook. This is important as Quick Look uses the file name and file extension on the path to determine the content type of the file to open.
* `displayName` String (optional) - The name of the file to display on the Quick Look modal view. This is purely visual and does not affect the content type of the file. Defaults to `path`.

Uses [Quick Look](https://en.wikipedia.org/wiki/Quick_Look) to preview a file at a given path.

#### `win.closeFilePreview()` *macOS*

Closes the currently open [Quick Look](https://en.wikipedia.org/wiki/Quick_Look) panel.

#### `win.setBounds(bounds[, animate])`

* `bounds` [Rectangle](structures/rectangle.md)
* `animate` Boolean (необязательно) *macOS*

Resizes and moves the window to the supplied bounds. Any properties that are not supplied will default to their current values.

```javascript
const { BrowserWindow } = require('electron')
const win = new BrowserWindow()

// set all bounds properties
win.setBounds({ x: 440, y: 225, width: 800, height: 600 })

// set a single bounds property
win.setBounds({ width: 100 })

// { x: 440, y: 225, width: 100, height: 600 }
console.log(win.getBounds())
```

#### `win.getBounds()`

Возвращает [`Rectangle`](structures/rectangle.md)

#### `win.setContentBounds(bounds[, animate])`

* `bounds` [Rectangle](structures/rectangle.md)
* `animate` Boolean (необязательно) *macOS*

Resizes and moves the window's client area (e.g. the web page) to the supplied bounds.

#### `win.getContentBounds()`

Возвращает [`Rectangle`](structures/rectangle.md)

#### `win.getNormalBounds()`

Returns [`Rectangle`](structures/rectangle.md) - Contains the window bounds of the normal state

**Note:** whatever the current state of the window : maximized, minimized or in fullscreen, this function always returns the position and size of the window in normal state. In normal state, getBounds and getNormalBounds returns the same [`Rectangle`](structures/rectangle.md).

#### `win.setEnabled(enable)`

* `enable` Boolean

Включает или выключает окно.

#### `win.setSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (необязательно) *macOS*

Resizes the window to `width` and `height`. If `width` or `height` are below any set minimum size constraints the window will snap to its minimum size.

#### `win.getSize()`

Возвращает `Integer[]` - Содержит высоту и ширину окна.

#### `win.setContentSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (необязательно) *macOS*

Resizes the window's client area (e.g. the web page) to `width` and `height`.

#### `win.getContentSize()`

Returns `Integer[]` - Contains the window's client area's width and height.

#### `win.setMinimumSize(width, height)`

* `width` Integer
* `height` Integer

Sets the minimum size of window to `width` and `height`.

#### `win.getMinimumSize()`

Returns `Integer[]` - Contains the window's minimum width and height.

#### `win.setMaximumSize(width, height)`

* `width` Integer
* `height` Integer

Sets the maximum size of window to `width` and `height`.

#### `win.getMaximumSize()`

Returns `Integer[]` - Contains the window's maximum width and height.

#### `win.setResizable(resizable)`

* `resizable` Boolean

Устанавливает, может ли пользователь вручную изменять размер окна.

#### `win.isResizable()`

Возвращает `Boolean` - Когда окно может быть изменено пользователем вручную.

#### `win.setMovable(movable)` *macOS* *Windows*

* `movable` Boolean

Sets whether the window can be moved by user. On Linux does nothing.

#### `win.isMovable()` *macOS* *Windows*

Returns `Boolean` - Whether the window can be moved by user.

На Linux всегда возвращает `true`.

#### `win.setMinimizable(minimizable)` *macOS* *Windows*

* `minimizable` Boolean

Sets whether the window can be manually minimized by user. On Linux does nothing.

#### `win.isMinimizable()` *macOS* *Windows*

Returns `Boolean` - Whether the window can be manually minimized by user

На Linux всегда возвращает `true`.

#### `win.setMaximizable(maximizable)` *macOS* *Windows*

* `maximizable` Boolean

Sets whether the window can be manually maximized by user. On Linux does nothing.

#### `win.isMaximizable()` *macOS* *Windows*

Returns `Boolean` - Whether the window can be manually maximized by user.

На Linux всегда возвращает `true`.

#### `win.setFullScreenable(fullscreenable)`

* `fullscreenable` Boolean

Sets whether the maximize/zoom window button toggles fullscreen mode or maximizes the window.

#### `win.isFullScreenable()`

Returns `Boolean` - Whether the maximize/zoom window button toggles fullscreen mode or maximizes the window.

#### `win.setClosable(closable)` *macOS* *Windows*

* `closable` Boolean

Sets whether the window can be manually closed by user. On Linux does nothing.

#### `win.isClosable()` *macOS* *Windows*

Returns `Boolean` - Whether the window can be manually closed by user.

На Linux всегда возвращает `true`.

#### `win.setAlwaysOnTop(flag[, level][, relativeLevel])`

* `flag` Boolean
* `level` String (необязательно) *macOS* - Значения включают `normal`, `floating`, `torn-off-menu`, `modal-panel`, `main-menu`, `status`, `pop-up-menu`, `screen-saver`, and ~~`dock`~~ (Устарело). По-умолчанию `floating`. Смотри [Документацию macOS](https://developer.apple.com/documentation/appkit/nswindow/level) для подробностей.
* `relativeLevel` Integer (optional) *macOS* - The number of layers higher to set this window relative to the given `level`. The default is `0`. Note that Apple discourages setting levels higher than 1 above `screen-saver`.

Sets whether the window should show always on top of other windows. After setting this, the window is still a normal window, not a toolbox window which can not be focused on.

#### `win.isAlwaysOnTop()`

Returns `Boolean` - Whether the window is always on top of other windows.

#### `win.moveTop()` *macOS* *Windows*

Moves window to top(z-order) regardless of focus

#### `win.center()`

Moves window to the center of the screen.

#### `win.setPosition(x, y[, animate])`

* `x` Integer
* `y` Integer
* `animate` Boolean (необязательно) *macOS*

Moves window to `x` and `y`.

#### `win.getPosition()`

Returns `Integer[]` - Contains the window's current position.

#### `win.setTitle(title)`

* `title` String

Changes the title of native window to `title`.

#### `win.getTitle()`

Returns `String` - The title of the native window.

**Note:** The title of the web page can be different from the title of the native window.

#### `win.setSheetOffset(offsetY[, offsetX])` *macOS*

* `offsetY` Float
* `offsetX` Float (optional)

Changes the attachment point for sheets on macOS. By default, sheets are attached just below the window frame, but you may want to display them beneath a HTML-rendered toolbar. For example:

```javascript
const { BrowserWindow } = require('electron')
let win = new BrowserWindow()

let toolbarRect = document.getElementById('toolbar').getBoundingClientRect()
win.setSheetOffset(toolbarRect.height)
```

#### `win.flashFrame(flag)`

* `flag` Boolean

Starts or stops flashing the window to attract user's attention.

#### `win.setSkipTaskbar(skip)`

* `skip` Boolean

Makes the window not show in the taskbar.

#### `win.setKiosk(flag)`

* `flag` Boolean

Enters or leaves the kiosk mode.

#### `win.isKiosk()`

Returns `Boolean` - Whether the window is in kiosk mode.

#### `win.getNativeWindowHandle()`

Returns `Buffer` - The platform-specific handle of the window.

The native type of the handle is `HWND` on Windows, `NSView*` on macOS, and `Window` (`unsigned long`) on Linux.

#### `win.hookWindowMessage(message, callback)` *Windows*

* `message` Integer
* `callback` Function

Hooks a windows message. The `callback` is called when the message is received in the WndProc.

#### `win.isWindowMessageHooked(message)` *Windows*

* `message` Integer

Returns `Boolean` - `true` or `false` depending on whether the message is hooked.

#### `win.unhookWindowMessage(message)` *Windows*

* `message` Integer

Unhook the window message.

#### `win.unhookAllWindowMessages()` *Windows*

Unhooks all of the window messages.

#### `win.setRepresentedFilename(filename)` *macOS*

* `filename` String

Sets the pathname of the file the window represents, and the icon of the file will show in window's title bar.

#### `win.getRepresentedFilename()` *macOS*

Returns `String` - The pathname of the file the window represents.

#### `win.setDocumentEdited(edited)` *macOS*

* `edited` Boolean

Specifies whether the window’s document has been edited, and the icon in title bar will become gray when set to `true`.

#### `win.isDocumentEdited()` *macOS*

Returns `Boolean` - Whether the window's document has been edited.

#### `win.focusOnWebView()`

#### `win.blurWebView()`

#### `win.capturePage([rect, ]callback)`

* `rect` [Rectangle](structures/rectangle.md) (optional) - The bounds to capture
* `callback` Function 
  * `image` [NativeImage](native-image.md)

Captures a snapshot of the page within `rect`. Upon completion `callback` will be called with `callback(image)`. The `image` is an instance of [NativeImage](native-image.md) that stores data of the snapshot. Omitting `rect` will capture the whole visible page.

**[Скоро устареет](promisification.md)**

#### `win.capturePage([rect])`

* `rect` [Rectangle](structures/rectangle.md) (optional) - The bounds to capture

* Returns `Promise<NativeImage>` - Resolves with a [NativeImage](native-image.md)

Captures a snapshot of the page within `rect`. Omitting `rect` will capture the whole visible page.

#### `win.loadURL(url[, options])`

* `url` String
* `options` Object (опционально) 
  * `httpReferrer` (String | [Referrer](structures/referrer.md)) (optional) - An HTTP Referrer url.
  * `userAgent` String (optional) - A user agent originating the request.
  * `extraHeaders` String (optional) - Extra headers separated by "\n"
  * `postData` ([UploadRawData[]](structures/upload-raw-data.md) | [UploadFile[]](structures/upload-file.md) | [UploadBlob[]](structures/upload-blob.md)) (optional)
  * `baseURLForDataURL` String (optional) - Base url (with trailing path separator) for files to be loaded by the data url. This is needed only if the specified `url` is a data url and needs to load other files.

Returns `Promise<void>` - the promise will resolve when the page has finished loading (see [`did-finish-load`](web-contents.md#event-did-finish-load)), and rejects if the page fails to load (see [`did-fail-load`](web-contents.md#event-did-fail-load)).

Same as [`webContents.loadURL(url[, options])`](web-contents.md#contentsloadurlurl-options).

The `url` can be a remote address (e.g. `http://`) or a path to a local HTML file using the `file://` protocol.

To ensure that file URLs are properly formatted, it is recommended to use Node's [`url.format`](https://nodejs.org/api/url.html#url_url_format_urlobject) method:

```javascript
let url = require('url').format({
  protocol: 'file',
  slashes: true,
  pathname: require('path').join(__dirname, 'index.html')
})

win.loadURL(url)
```

You can load a URL using a `POST` request with URL-encoded data by doing the following:

```javascript
win.loadURL('http://localhost:8000/post', {
  postData: [{
    type: 'rawData',
    bytes: Buffer.from('hello=world')
  }],
  extraHeaders: 'Content-Type: application/x-www-form-urlencoded'
})
```

#### `win.loadFile(filePath[, options])`

* `filePath` String
* `options` Object (опционально) 
  * `query` Object (optional) - Passed to `url.format()`.
  * `search` String (optional) - Passed to `url.format()`.
  * `hash` String (optional) - Passed to `url.format()`.

Returns `Promise<void>` - the promise will resolve when the page has finished loading (see [`did-finish-load`](web-contents.md#event-did-finish-load)), and rejects if the page fails to load (see [`did-fail-load`](web-contents.md#event-did-fail-load)).

Same as `webContents.loadFile`, `filePath` should be a path to an HTML file relative to the root of your application. See the `webContents` docs for more information.

#### `win.reload()`

Same as `webContents.reload`.

#### `win.setMenu(menu)` *Linux* *Windows*

* `menu` Menu | null

Sets the `menu` as the window's menu bar.

#### `win.removeMenu()` *Linux* *Windows*

Remove the window's menu bar.

#### `win.setProgressBar(progress[, options])`

* `progress` Double
* `options` Object (опционально) 
  * `mode` String *Windows* - Mode for the progress bar. Can be `none`, `normal`, `indeterminate`, `error` or `paused`.

Sets progress value in progress bar. Valid range is [0, 1.0].

Remove progress bar when progress < 0; Change to indeterminate mode when progress > 1.

On Linux platform, only supports Unity desktop environment, you need to specify the `*.desktop` file name to `desktopName` field in `package.json`. By default, it will assume `app.getName().desktop`.

On Windows, a mode can be passed. Accepted values are `none`, `normal`, `indeterminate`, `error`, and `paused`. If you call `setProgressBar` without a mode set (but with a value within the valid range), `normal` will be assumed.

#### `win.setOverlayIcon(overlay, description)` *Windows*

* `overlay` [NativeImage](native-image.md) | null - the icon to display on the bottom right corner of the taskbar icon. If this parameter is `null`, the overlay is cleared
* `description` String - a description that will be provided to Accessibility screen readers

Sets a 16 x 16 pixel overlay onto the current taskbar icon, usually used to convey some sort of application status or to passively notify the user.

#### `win.setHasShadow(hasShadow)` *macOS*

* `hasShadow` Boolean

Sets whether the window should have a shadow. On Windows and Linux does nothing.

#### `win.hasShadow()` *macOS*

Возвращает `Boolean` - был ли вызов успешным.

В Windows и Linux всегда возвращает `true`.

#### `win.setOpacity(opacity)` *Windows* *macOS*

* `opacity` Number - between 0.0 (fully transparent) and 1.0 (fully opaque)

Sets the opacity of the window. On Linux does nothing.

#### `win.getOpacity()` *Windows* *macOS*

Returns `Number` - between 0.0 (fully transparent) and 1.0 (fully opaque)

#### `win.setShape(rects)` *Windows* *Linux* *Experimental*

* `rects` [Rectangle[]](structures/rectangle.md) - Sets a shape on the window. Passing an empty list reverts the window to being rectangular.

Setting a window shape determines the area within the window where the system permits drawing and user interaction. Outside of the given region, no pixels will be drawn and no mouse events will be registered. Mouse events outside of the region will not be received by that window, but will fall through to whatever is behind the window.

#### `win.setThumbarButtons(buttons)` *Windows*

* `buttons` [ThumbarButton[]](structures/thumbar-button.md)

Возвращает `Boolean` - успешно ли добавлены кнопки

Add a thumbnail toolbar with a specified set of buttons to the thumbnail image of a window in a taskbar button layout. Returns a `Boolean` object indicates whether the thumbnail has been added successfully.

The number of buttons in thumbnail toolbar should be no greater than 7 due to the limited room. Once you setup the thumbnail toolbar, the toolbar cannot be removed due to the platform's limitation. But you can call the API with an empty array to clean the buttons.

`buttons` является массивом объектов `Button`:

* `Button` Object 
  * `icon` [NativeImage](native-image.md) - значок, отображаемый на панели инструментов эскизов.
  * `click` Function
  * `tooltip` String (опционально) - текст всплывающей подсказки на кнопке.
  * `flags` String[] (опционально) - контроль определенных состояний и поведений кнопки. По умолчанию `['enabled']`.

`flags` — это массив, который может включать следующие `строки`:

* `enabled` - кнопка активна и доступна пользователю.
* `disabled` - кнопка отключена. Она присутствует, но имеет неактивное визуальное состояние и не будет реагировать на действия пользователя.
* `dismissonclick` - когда кнопка нажата, окно миниатюры закрывается немедленно.
* `nobackground` - не рисует границы кнопок, использует только изображение.
* `hidden` - кнопка не отображается пользователю.
* `noninteractive` - кнопка включена, но не интерактивная; рисуется не нажимаемое состояние кнопки. Это значение предназначено для экземпляров, где кнопка используется в уведомлении.

#### `win.setThumbnailClip(region)` *Windows*

* `region` [Rectangle](structures/rectangle.md) - Область окна

Sets the region of the window to show as the thumbnail image displayed when hovering over the window in the taskbar. You can reset the thumbnail to be the entire window by specifying an empty region: `{ x: 0, y: 0, width: 0, height: 0 }`.

#### `win.setThumbnailToolTip(toolTip)` *Windows*

* `toolTip` String

Sets the toolTip that is displayed when hovering over the window thumbnail in the taskbar.

#### `win.setAppDetails(options)` *Windows*

* `options` Object 
  * `appId` String (optional) - Window's [App User Model ID](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391569(v=vs.85).aspx). It has to be set, otherwise the other options will have no effect.
  * `appIconPath` String (optional) - Window's [Relaunch Icon](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391573(v=vs.85).aspx).
  * `appIconIndex` Integer (optional) - Index of the icon in `appIconPath`. Ignored when `appIconPath` is not set. Default is `0`.
  * `relaunchCommand` String (optional) - Window's [Relaunch Command](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391571(v=vs.85).aspx).
  * `relaunchDisplayName` String (optional) - Window's [Relaunch Display Name](https://msdn.microsoft.com/en-us/library/windows/desktop/dd391572(v=vs.85).aspx).

Sets the properties for the window's taskbar button.

**Note:** `relaunchCommand` and `relaunchDisplayName` must always be set together. If one of those properties is not set, then neither will be used.

#### `win.showDefinitionForSelection()` *macOS*

Same as `webContents.showDefinitionForSelection()`.

#### `win.setIcon(icon)` *Windows* *Linux*

* `icon` [NativeImage](native-image.md)

Changes window icon.

#### `win.setWindowButtonVisibility(visible)` *macOS*

* `visible` Boolean

Sets whether the window traffic light buttons should be visible.

This cannot be called when `titleBarStyle` is set to `customButtonsOnHover`.

#### `win.setAutoHideMenuBar(hide)`

* `hide` Boolean

Sets whether the window menu bar should hide itself automatically. Once set the menu bar will only show when users press the single `Alt` key.

If the menu bar is already visible, calling `setAutoHideMenuBar(true)` won't hide it immediately.

#### `win.isMenuBarAutoHide()`

Returns `Boolean` - Whether menu bar automatically hides itself.

#### `win.setMenuBarVisibility(visible)` *Windows* *Linux*

* `visible` Boolean

Sets whether the menu bar should be visible. If the menu bar is auto-hide, users can still bring up the menu bar by pressing the single `Alt` key.

#### `win.isMenuBarVisible()`

Returns `Boolean` - Whether the menu bar is visible.

#### `win.setVisibleOnAllWorkspaces(visible[, options])`

* `visible` Boolean
* `options` Object (опционально) 
  * `visibleOnFullScreen` Boolean (optional) *macOS* - Sets whether the window should be visible above fullscreen windows

Sets whether the window should be visible on all workspaces.

**Note:** This API does nothing on Windows.

#### `win.isVisibleOnAllWorkspaces()`

Returns `Boolean` - Whether the window is visible on all workspaces.

**Примечание:** Данный API всегда возвращает false в Windows.

#### `win.setIgnoreMouseEvents(ignore[, options])`

* `ignore` Логическое значение
* `options` Object (опционально) 
  * `forward` Boolean (optional) *macOS* *Windows* - If true, forwards mouse move messages to Chromium, enabling mouse related events such as `mouseleave`. Only used when `ignore` is true. If `ignore` is false, forwarding is always disabled regardless of this value.

Включает для окна игнорирование событий от мыши.

All mouse events happened in this window will be passed to the window below this window, but if this window has focus, it will still receive keyboard events.

#### `win.setContentProtection(enable)` *macOS* *Windows*

* `enable` Boolean

Предотвращает захват содержимого окна другими приложениями.

On macOS it sets the NSWindow's sharingType to NSWindowSharingNone. On Windows it calls SetWindowDisplayAffinity with `WDA_MONITOR`.

#### `win.setFocusable(focusable)` *Windows*

* `focusable` Boolean

Changes whether the window can be focused.

#### `win.setParentWindow(parent)`

* `parent` BrowserWindow

Sets `parent` as current window's parent window, passing `null` will turn current window into a top-level window.

#### `win.getParentWindow()`

Возвращает `BrowserWindow` - родительское окно.

#### `win.getChildWindows()`

Возвращает `BrowserWindow[]` - все дочерние окна.

#### `win.setAutoHideCursor(autoHide)` *macOS*

* `autoHide` Boolean

Controls whether to hide cursor when typing.

#### `win.selectPreviousTab()` *macOS*

Selects the previous tab when native tabs are enabled and there are other tabs in the window.

#### `win.selectNextTab()` *macOS*

Selects the next tab when native tabs are enabled and there are other tabs in the window.

#### `win.mergeAllWindows()` *macOS*

Merges all windows into one window with multiple tabs when native tabs are enabled and there is more than one open window.

#### `win.moveTabToNewWindow()` *macOS*

Moves the current tab into a new window if native tabs are enabled and there is more than one tab in the current window.

#### `win.toggleTabBar()` *macOS*

Toggles the visibility of the tab bar if native tabs are enabled and there is only one tab in the current window.

#### `win.addTabbedWindow(browserWindow)` *macOS*

* `browserWindow` BrowserWindow

Adds a window as a tab on this window, after the tab for the window instance.

#### `win.setVibrancy(type)` *macOS*

* `type` String - Can be `appearance-based`, `light`, `dark`, `titlebar`, `selection`, `menu`, `popover`, `sidebar`, `medium-light` or `ultra-dark`. See the [macOS documentation](https://developer.apple.com/documentation/appkit/nsvisualeffectview?preferredLanguage=objc) for more details.

Adds a vibrancy effect to the browser window. Passing `null` or an empty string will remove the vibrancy effect on the window.

#### `win.setTouchBar(touchBar)` *macOS* *Experimental*

* `touchBar` TouchBar

Sets the touchBar layout for the current window. Specifying `null` or `undefined` clears the touch bar. This method only has an effect if the machine has a touch bar and is running on macOS 10.12.1+.

**Примечание:** TouchBar API в настоящее время является экспериментальным и может быть изменен или удален в будущих версиях Electron.

#### `win.setBrowserView(browserView)` *Экспериментально*

* `browserView` [BrowserView](browser-view.md). Attach browserView to win. If there is some other browserViews was attached they will be removed from this window.

#### `win.getBrowserView()` *Experimental*

Returns `BrowserView | null` - an BrowserView what is attached. Returns `null` if none is attached. Throw error if multiple BrowserViews is attached.

#### `win.addBrowserView(browserView)` *Экспериментально*

* `browserView` [BrowserView](browser-view.md)

Replacement API for setBrowserView supporting work with multi browser views.

#### `win.removeBrowserView(browserView)` *Экспериментально*

* `browserView` [BrowserView](browser-view.md)

#### `win.getBrowserViews()` *Experimental*

Returns array of `BrowserView` what was an attached with addBrowserView or setBrowserView.

**Примечание:** BrowserView API в настоящее время экспериментально и может измениться или быть удалено в будущих релизах Electron.