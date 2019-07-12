# FileUploader

this is one of those things i got tired of writing from scratch each time (&& didn't want to use a library or larger framework for) so i wrote a little vanilla JS class to handle that. info/examples below. if u find a bug please open an issue :) PRs welcome.

[![JavaScript Style Guide](https://cdn.rawgit.com/standard/standard/master/badge.svg)](https://github.com/standard/standard)

## install

Just download the `FileUploader.js` file and include client-side as a script tag, or alternatively u could install with npm, but keep in mind, this relies on the DOM and WebAPIs so it can't be used outside of a browser. i just added the package.json to make things a little easier for myself (avoid all that manual copy+paste) && instead just do:
```
$ npm install git+https://github.com/nbriz/FileUploader.git
```
```HTML
<script src="node_modules/FileUploader/FileUploader.js"></script>
```

## config options

here's all the options it takes:
```js
new FileUploader({
  maxSize: 1000,                        // limit max file size in kb
  types: ['image/jpeg','audio/mpeg3'],  // limit allowed file mime types
  filter: callback,                     // or alternative callback filter
  click: '#button',                     // selector for clickable element
  drop: '#background',                  // selector for drag&drop element
  dropping: callback,                   // runs when file is dragged over
  dropped: callback,                    // runs when file has been dropped
  ready: callback,                      // runs when data is ready
  error: callback,                      // runs when there's an error
})
```

## example usage

but u're never gonna actually use all of 'em at the same time. instead it be something like this: say for example u've got an HTML element like:
```html
  <button id="my-btn">click to upload</button>
```
then in ur JS:
```js
const fu = new FileUploader({
  maxSize: 1000,
  types: ['image/jpeg', 'image/png'],
  click: '#my-btn',
  ready: (file) => {
    console.log(`the data for the ${file.type} file called ${file.name} is ready`)
    console.log(file.data)
  },
  error: (err) => {
    console.error(err)
  }
})
```

the above example attached the file uploader to the button, when the button is clicked a finder window will pop up for the user to choose a file, if they choose something other than a PNG or JPG under 1000kb in size the error callback will fire with an error message. otherwise, when the file is ready the ready callback will fire.

another example usage could look like this:
```html
  <button id="my-btn">click to upload</button>
  <section id="main">
    <!-- some other HTML stuff here -->
  </section>
```
then in ur JS:
```js
const fu = new FileUploader({
  click: '#my-btn',
  drop: '#main',
  filter: (type) => {
    let audio = [
      'audio/wav', 'audio/mpeg3', 'audio/mp4', 'audio/aac',
      'audio/aacp', 'audio/ogg', 'audio/webm', 'audio/ogg',
      'audio/webm', 'audio/mpeg']
    let types = [ ...audio, 'text/plain' ]
    if (type.indexOf('image') > -1) return true
    else if (types.indexOf(type) > -1) return true
    else return false
  },
  dropping: (e) => { e.style.opacity = 0.5 },
  dropped: (e) => { e.style.opacity = 1 },
  ready: (file) => {
    console.log(`the data for the ${file.type} file called ${file.name} is ready`)
    console.log(file.data)
  },
  error: (err) => {
    console.error(err)
  }
})
```

in this example we have two options for uploading a file, either by clicking the `button#my-btn` or by dragging and dropping the file onto the `section#main`. here, rather than limiting the file size and specifying what types of files are allowed, i'm using the filter callback, this will check the type of the uploaded file and let u define ur own logic for determining whether or not to allow the upload (in this case i'm allowing for any image file type, plain text and a specific set of audio types). additionally, i'm using the dropping and dropped callbacks, these will fire when a file is dragged over the `section#main` (ie. dropping) and then when the file is released over that element (ie. dropped). in this example i'm using those callbacks to provide a little visual feedback (fading the section element out a bit while u're hovering over it w/a file).
