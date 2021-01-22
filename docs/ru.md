Vueditor
===

[![vueditor](https://img.shields.io/npm/v/vueditor.svg)](https://www.npmjs.com/package/vueditor)
[![vueditor](https://img.shields.io/npm/l/vueditor.svg)](https://www.npmjs.com/package/vueditor)
[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

[中文文档](./docs/chinese.md)

[RU](./docs/ru.md)

Для редактора wysiwyg, написанного на Vue.js и Vuex.js, требуются Vue.js 2.0.0, Vuex.js 2.0.0 и выше.

Совместимость с браузером: Chrome, Firefox, Safari, IE 9+.

В сети [DEMO](http://hifarer.github.io/vueditor/)

## Скриншот

![vueditor](../vueditor.gif)

## Особенности

- Настраиваемый
- Лёгкий вес, очень мало зависимостей
- Поддержка плагинов

## Установка
```javascript
npm install vueditor
```

Если вы предпочитаете использовать его с помощью тега сценария, загрузите последний пакет выпуска и добавьте на свою страницу `vueditor.min.js`,` vueditor.min.css`. 

## Применение

### Vue.use(Vueditor, config)

Используйте его в следующих случаях:

1. Требуется только один редактор
2. Требуется несколько редакторов, но используется одна и та же конфигурация

```javascript
import Vue from 'vue'
import Vuex from 'vuex'
import Vueditor from 'vueditor'

import 'vueditor/dist/style/vueditor.min.css'

// ваша конфигурация здесь
let config = {
  toolbar: [
    'removeFormat', 'undo', '|', 'elements', 'fontName', 'fontSize', 'foreColor', 'backColor'
  ],
  fontName: [
    {val: 'arial black'}, 
    {val: 'times new roman'}, 
    {val: 'Courier New'}
  ],
  fontSize: ['12px', '14px', '16px', '18px', '0.8rem', '1.0rem', '1.2rem', '1.5rem', '2.0rem'],
  uploadUrl: ''
};

Vue.use(Vuex);
Vue.use(Vueditor, config);
// создать корневой экземпляр
new Vue({
  el: '#editorContainer'
});
```

Затем в вашем шаблоне vue где-нибудь:
```html
<template>
  <div>
    ...
    <Vueditor></Vueditor>
  </div>
</template>
```

Чтобы получить и установить контент, вам нужно приобрести компонент Vueditor, вы можете использовать для этого `$children [index]` или `ref`. 

```javascript
let parent = new Vue({
  el: '#editor1'
});
let editor = parent.$children[0];
editor.setContent('ваш контент здесь');
editor.getContent();
```

### createEditor(selector, config)

Вызовите `createEditor` и передайте конкретную конфигурацию в качестве параметра соответственно для нескольких редакторов на странице. 

```javascript

  import Vue from 'vue'
  import Vuex from 'vuex'
  import { createEditor } from 'vueditor'

  import 'vueditor/dist/style/vueditor.min.css'
  
  Vue.use(Vuex);

  createEditor('#editorContainer', {
    toolbar: [
      'removeFormat', 'undo', '|', 'elements', 'fontName', 'fontSize', 'foreColor', 'backColor', 
    ],
    uploadUrl: '',
    id: '',
    classList: []
  });
```

В этом случае инициализированный элемент будет заменен, вы можете добавить classList или id в конфигурацию для добавления стилей, визуализированный элемент будет иметь эти атрибуты. `createEditor` возвращает экземпляр vueditor, где вы можете установить и получить содержимое с его помощью:

```javascript
let inst = createEditor(...);
inst.setContent('ваш контент здесь');
inst.getContent();
```

#### Загрузка файла 

Вы можете установить атрибут `uploadUrl` в конфигурации при инициализации редактора, все загружаемые материалы будут обрабатываться автоматически. Если вы хотите сделать это самостоятельно или вам нужно выполнить некоторую авторизацию перед загрузкой, просто добавьте функцию `upload` к экземпляру, возвращаемому `createEditor`. Когда запускается действие загрузки, vueditor вызывает эту функцию вместо встроенной функции. Функция загрузки имеет два аргумента: `obj` относится к элементу ввода файла,` callback` требует URL загруженного файла в качестве аргумента для вставки содержимого в редактор. См. Пример ниже: 
```javascript
editor.upload = function (obj, callback) {
  let formData = new FormData();
  let xhr = new XMLHttpRequest();
  formData.append('fieldName', obj.files[0]);
  xhr.open('POST', 'upload/url');
  xhr.send(formData);
  xhr.onload = function () {
    callback(xhr.responseText);
  };
  xhr.onerror = function (err) {
    console.log(err);
  }
}
```

### Настройка языка

Язык редактора по умолчанию - английский, чтобы установить другой язык, вам нужно будет перевести на свой собственный.
Внутри папки `dist/language` есть полный пример. Добавьте тег скрипта или используйте `import`, `require` для ввода языкового объекта, а затем сделайте его атрибутом конфигурации для инициализации. См. Пример ниже:
```javascript
Vue.use(Vueditor, {
  ...
  lang: languageObject,
});
```

## Варианты конфигурации:

|          Name         |    Type    |                                                         Description                                                         |
| --------------------- | ---------- | --------------------------------------------------------------------------------------------------------------------------- |
| spellcheck            | `Boolean`  | Проверять орфографию или нет, по умолчанию - false |
| lang                  | `Object`   | Язык интерфейса, по умолчанию английский |
| toolbar               | `Array`   | Кнопки на панели инструментов, используйте `|` или `divider` в качестве разделителя для группировки |
| fontName              | `Array`   | Параметры выбора семейства шрифтов, `val` относятся к фактическому значению css, `abbr` относятся к тексту параметра, `abbr` является необязательным, когда равно `val` |
| fontSize              | `Array`    | Параметры выбора размера шрифта |
| uploadUrl         | `String`   | URL-адрес загрузки файла, возвращаемый результат должен быть строкой, относящейся к URL-адресу загруженного файла, оставив его пустым, вы получите локальный предварительный просмотр |
| id                    | `String`   | id для визуализированного элемента редактора |
| classList             | `Array`    | className для визуализированного элемента редактора |
| plugins             | `Array`    | плагины для редактора |


Значение по умолчанию для вышеуказанных полей:

```javascript
{
  toolbar: [
    'removeFormat', 'undo', '|', 'elements', 'fontName', 'fontSize', 'foreColor', 'backColor', 'divider',
    'bold', 'italic', 'underline', 'strikeThrough', 'links', 'divider', 'subscript', 'superscript',
    'divider', 'justifyLeft', 'justifyCenter', 'justifyRight', 'justifyFull', '|', 'indent', 'outdent',
    'insertOrderedList', 'insertUnorderedList', '|', 'picture', 'tables', '|', 'switchView'
  ],
  fontName: [
    {val: 'arial black'}, 
    {val: 'times new roman'}, 
    {val: 'Courier New'}
  ],
  fontSize: [
    '12px', '14px', '16px', '18px', '20px', '24px', '28px', '32px', '36px'
  ],
  uploadUrl: ''
  id: '',
  classList: []
};
```

## Журнал изменений

См. [Журнал изменений](./docs/changelog.md)

## Подтверждение ошибки 

## TODO

- [x] Поддержка Markdown
- [x] Полноэкранные и фиксированные функции панели инструментов
- [x] Автоматическая настройка положения всплывающего меню
- [ ] Расширенные параметры стола
- [ ] Выделение кода
- [ ] Поддержка плагинов
- [ ] XSS профилактика
- [ ] Тест

## Лицензия

[MIT](http://opensource.org/licenses/MIT)

Copyright (c) 2016 hifarer
