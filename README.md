Fancier Settings 1.0
==================

Based on Fancy Settings 1.2.

*Create fancy, chrome-look-alike settings for your Chrome or Safari extension in minutes!*

Used by:

* [`FluentTyper`](https://chrome.google.com/webstore/detail/fluenttyper-autocomplete/mbjlobpodpimgbkmlmjiblnmfgajmebm) 

Rationale
---------

The goal of this project is to provide a simple way to generate native-chrome-like settings pages for use in projects like 
chrome extensions. Settings are defined entirely using a javascript object, the "manifest," and event binding can be 
easily customized via javascript.

Ideally, this framework contains enough variety of [setting types](#setting-types) that one need only to edit [the "Manifest" 
(`/source/manifest.js`)](#the-manifest) and the [settings initialization script (`/source/settings.js`)](#settings-initialization) 
to populate the settings page with the right controls.


How It Works
---------------

#### Project Structure
```
├── css ─────────────────────────├─( framework css; if you're extending the framework you should add to these )
│   ├── bulma.min.css────────────├─( Bulma.io - for main layout )
│   └── bulma-switch.min.css ────├─( Bulma.io extension for CheckBox )
│   └── bulma-slider.min.css ────├─( Bulma.io extension for Slider )
│   └── bulma-divider.min.css ───├─( Bulma.io extension for Tab groups )
├── i18n.js ─────────────────────├─( your internationalization data )
├── icon.png ────────────────────├─( favicon shown on the settings tab in chrome )
├── index.html ──────────────────├─( index page; loads all javascript and establishes main layout )
├── js ──────────────────────────├─( framework javascript; if you're extending the framework you should add to these )
│   ├── classes ─────────────────├─( mootools-backed framework classes )
│   │   ├── fancier-settings.js    ├─( main entry point; contains `FancierSettingsWithManifest` function, used to 
│   │   │                        │       build all settings and add them to the DOM )
│   │   ├── search.js ───────────├─( provides management interface for the search index )
│   │   ├── setting.js ──────────├─( classes for all setting types (e.g. ListBox, Button, etc.) and the Setting class
│   │   │                        │       itself; includes DOM creation and event logic )
│   │   └── tab.js ──────────────├─( class for `Tab`; includes DOM creation and tab switching )
│   └── i18n.js ─────────────────├─( internationalization interface; retrieves i18n values registered in `/source/i18n.js` )
├── lib ─────────────────────────├─( dependencies
│   └── store.js ────────────────├─( localStorage interface )
├── manifest.js ─────────────────├─( your settings manifest; see [The "Manifest"](#the--manifest)
└── settings.js ─────────────────└─( your settings pre-initialization; `FancierSettingsWithManifest` is called here 
                                         after any prerequisite async event (e.g. domready, retrieving values from 
                                         `chrome.storage`, etc.); see [Settings Initialization](#settings-initialization) )
```

#### The "Manifest"
The "Manifest" (`/source/manifest.js`) is a simple javascript file which registers a global object: `manifest`. This 
object contains the following properties:

* `name`: Name of the manifest
* `icon`: Filename of the favicon to show for the options tab in chrome
* `settings`:

  An array containing a "flattened" list of settings.  Each element in this array describes one setting. All setting objects, 
  regardless of type, have the following properties:
  
  * `tab`: The name (and text) of the tab where the setting will be shown; settings with the same `tab` value will be rendered on the same tab
  * `name`: The name of this setting; this name will be used to reference it later via javascript, usually as the key of an object
  * `type`: The type of setting, see [setting types](#setting-types) below
  * `label` _(optional)_: The text of a `<label>` element which will be rendered before the setting element
  * `group` _(optional)_: The name (and text) of the group (a section within a tab) where the setting will be shown; settings with the same `group` value will be rendered in the same group

#### Events
###### **(WIP)**

#### Setting Types

| Type | Description | Additional Properties | Events |
|------|-------------|-----------------------|--------|
| `description` | renders a `<p>` element containing a block of text | <ul><li>`text`: the text content of the `<p>` element</li></ul> | |
| `button` | renders a `<button>` element | <ul><li>`text`: the text of the `<button>` element</li></ul> | <ul><li>`action`: fires on button `click`</li></ul> |
| `text` | renders an `<input>` element with a `type` attribute of either `text` (default) or `password` | <ul><li>`text`: the value of the `placeholder` attribute of the `<input>` element</li><li>`masked`: a boolean property; if true, sets the `type` attribute of the `<input>` element to `password`</li></ul> | |
| `textarea` | renders an `<input type='textarea'>` element | <ul><li>`text`: _not sure what this does yet_</li><li>`value`: _not sure what this does yet_</li></ul> | `action`: fires on textarea `change` & `keyup` |
| `checkbox` | renders an `<input type='checkbox'>` element | _HINT: use `label` with this setting type_ | <ul><li>`action`: fires on checkbox `change`</li></ul> |
| `slider` | renders an `<input type='range'>` element | <ul><li>`min`: sets the `min` attribute</li><li>`max`: sets the `max` attribute</li><li>`step`: sets the `step` attribute</li><li>`display`: a boolean property; if true (default), renders the current value of the slider beside it (unless modified by `displayModifier`)</li><li>`displayModifier`: a function which receives the value of the range and whose return value is rendered beside the range if `display` is true</li></ul> | <ul><li>`action`: fires on range `change`</li></ul> |
| `popupButton` | a bit of a misnomer; render's a `<select>` element with `<option>` childred corresponding to the `options` array | <ul><li>`options`: an object with `groups` and `values` properties<ul><li>`groups`: an array of strings; each renders an `<optgroup>` element whose `label` attribute is the value of the string</li><li>`values`: array of objects which correspond to the `<option>` elements to go inside the `<optgroup>`s; each object should have a `text` property which renders as the text node inside of the `<option>`, a `value` property which is the value of the `value` attribute on the `<option>` element, and a `group` property which detmines which `<optgroup>` a given `<option>` is placed</li></ul></li></ul> | `action`: fires on select `change` |
| `listbox` | renders a `<select>` element in listBox mode with `<option>` elements corresponding to the `options` array | <ul><li>`multiple`: adds the `multiple` attribute, allowing for multiple options to be selected simultaneously</li><li>`options`: an array of objects which correspond to the `<option>` elements to go inside the `<select>`; each object should have a `text` property which renders as the text node inside of the `<option>` and a `value` property which is the value of the `value` attribute on the `<option>` element<\li><\ul> | <ul><li>`action`: fires on select `change`</li></ul> |
| `radioButtons` | renders a set of `<input type='radio'>` elements corresponding to the `options` array | <ul><li>`options`: an array of objects which correspond to the `<input type='radio'>`; each object should have a `text` property which renders as the text node inside of the `<input type='radio'>` and a `value` property which is the value of the `value` attribute on the `<input type='radio'>` element</li></ul> | <ul><li>`action`: fires on select `change`</li></ul> |
| `modalButton` | renders a button which, when clicked, opens a modal over the current settings tab, containing nested settings | <ul><li>`text`: the text of the `<button>` element</li><li>`modal`: an object with `title` and `contents` properties<ul><li>`title`: the text which is rendered in an `<h2>` at the top of the modal</li><li>`contents`: an array of nested [settings](#setting-types) which will be rendered inside the modal</li></ul></li></ul> | <ul><li>`action`: fires on button `click`</li><li>`modal_done`: fires on "done" button `click` (inside modal)</li></ul> |
| `fileButton` | **WIP** | | |


#### Settings Initialization
###### **(WIP)**

#### Using Settings Values

All values in the settings page are automatically persisted via [`chrome.storage`](https://developer.chrome.com/extensions/storage) if it is available or `localStorage` otherwise. By default `chrome.storage.sync` interface is used, this can be changed in (`source/lib/store.js`), by setting `useLocalBackend` to `false`. All keys have the prefix of `store.settings.` (e.g. `store.settings.myButton`). You can retrieve the values via javascript, operate on them and ultimately store your chrome extension settings via `Store` object(`new Store("settings")`) or [`chrome.storage`](https://developer.chrome.com/extensions/storage) for use in your extension.

In the sample code of this repo, this logic resides in the [`settings`](#settings-initialization) file as well but could 
just as easily be factored out.

How To Use **(WIP)**
----------

1. `npm i --save or something`...
1. add things to your build process...
1. customize [`manifest.js`](#the-manifest)
1. customize [`settings.js`](#settings-initialization)
1. ...


