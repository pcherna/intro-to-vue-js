# intro-to-vue-js modified to also work in OnSignTV

This is the intro-to-vue-js project from <https://www.vuemastery.com/courses/intro-to-vue-js/>

The initial commit is the completed project.

## Use, in OnSignTV

OnSignTV has a number of requirements:

1. HTML files are run through the Jinja templating engine
1. All assets need to be in the root folder
1. All assets need to be accessed through a Jinja expansion {{ media.basefile.url }}, where _basefile_ is the asset filename, lowercased, with all punctuation converted to underscores, and the extension dropped
1. Interactivity has to be requested
1. Other app configuration can be added

### Handling Jinja

Because Jinja template delimiters {{ and }} conflict with those of Vue, we need to switch Vue to something else, at least for the Main Vue app if it has any expansions in the HTML. Here is an example showing how to switch Vue to use double square-brackets as delimiters:

```javascript
var app = new Vue({
    el: '#app',
    ...
    delimiters: ['[[', ']]'],
    ...
```

### Handling asset folders

When building the OnSignTV app zip file, ```zip -jrD```  will gather files from the subfolders into the root of the ZIP file.

### Handling the media expansion

When pulling in JavaScript or Style Sheets, you can just write it both ways, and ignore the error caused by the "wrong one", e.g.:

```html
    <script src="main.js"></script>
    <script src="{{ media.main.url }}"></script>
```

If your JavaScript needs access to files, you can define them in your HTML using global JavaScript variables, and a helper function to discriminate between the OnSignTV and the Browser case, thus:

```html
        <script>
            function inOnSignTV() {
                // If inside OnSignTV, then we're processed by Jinja and this evaluates to "0"
                // If inside browser, then this evaluates to "{{0}}" so we can discern by length
                let jinja_str = "{{0}}"
                return jinja_str.length == 1
            }
            if (inOnSignTV()) {
                foo = "{{ media.file_1.url }}"
                bar = "{{ media.another_file.url }}"
            }
            else {
                foo = './assets/file-1.jpg'
                bar = './assets/another-file.jpg'
            }
        </script>
```

## Handling Interactivity

You need to inform OnSignTV to allow interactivity, by adding:

```html
    <meta name="allow-interaction" content="yes">
```

inside of ```<head>```.
