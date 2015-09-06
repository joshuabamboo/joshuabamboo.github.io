---
layout: post
title: "We're Programmers, Dammit"
date: 2015-09-06 14:17:55 -0400
comments: true
categories: flatiron school
---

This is a phrase I borrowed from [programmer extraordinaire Steven Nunez](http://hostiledeveloper.com/). I love this sentiment. I love the idea that we aren't limited to consuming the Internet. We can be an active participant.

In this edition of "We're Programmers, Dammit", I'll walk through how I used a Chrome Extension to change the functionality of an unwanted feature of a website. I will walk through the basic steps for getting a Chrome Extension up and running. The project lives at [this Github repository](https://github.com/joshuabamboo/fias).

#### 1. Create a `manifest.json` file
Inside a directory that will contain extension, create a `manifest.json`. This file is what Chrome will look for first. It supplies Chrome with all the metadata about the extension (e.g. `manifest_version`, `name`, `version`). These are the only things *required* to make a chrome extention, so let's do the basics and come back later.

```json
  {
    "manifest_version": 2,
    "name": "extenion_name",
    "version": "myExtensionVersion"
  } 
```

#### 2. Connect your local directory to Chrome
Now we have the following directory:
```
  extension_name/ 
  └── manifest.json
```

To get the directory into Chrome, we must first go to `chrome://extensions/` in your Chrome browser. Next, click the `Developer Mode` checkbox at the top right. It should look like this: 
<img src="{{ root_url }}/images/chrome-ext.png" />

Next, locate the directory in your finder, and drag the entire folder into the browser opened to `chrome://extensions/`. Chrome allows you to drop the entire folder to load it into Chrome. It's as simple as that. You'll only have to do this step once. Any changes you make to the files will now be available in Chrome one you reload (`cmd + r`) the page. Click the `Enable` checkbox, and you've got yourself a Chrome Extension.

<img src="{{ root_url }}/images/chrome-drag.png" />

Protip: If you get an error at this phase, more than likely you have a syntax error. Check your json formatting (check your commas) and try again.

#### 3. Now make it do something
Congrats! You officially have a Chrome Extension. The downside is, it doesn't do anything. Let's fix that. In my case, I wanted to remove a social feature on [Learn.co](http://learn.co). The site looks like this. I'm feeling particularly antisocial today, so I want to get rid of everything in the red box.

<img src="{{ root_url }}/images/chrome-ext-learn-before.png" />

Back to `manifest.json`. We'll need to add some `content_scripts` to the manifest.

```json
  {
    "manifest_version": 2,
    "name": "extenion_name",
    "version": "myExtensionVersion"

    "content_scripts": [
      {
        "matches": ["https://learn.co/*"],
        "css": ["fias.css"]
      }
    ]
  } 
```

`matches` refers to the URLs we want to the Chrome Extension to change. In our case, we want it to work on every URL that starts with `https://learn.co/`.

`css` is where we link the css files in the directory. Go ahead and create the css file at the root of the directory.

```
  extension_name/ 
  └── manifest.json
  └── fias.css
```

`fias.css` is where we will add the css to remove the elements on the page that we do not want to see. That is as simple as adding `display: none` to the id of the divs. In our case it looks like this:

 ```css
  #students-working-container {
    display: none;
  }
```

<img src="{{ root_url }}/images/chrome-ext-learn-after.png" />

It's as simple as that. With a few lines of code we've completely removed an unwanted feature because we're programmers, dammit!



