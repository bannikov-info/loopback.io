---
title: "Creating components"
lang: en
layout: page
keywords: LoopBack
tags:
sidebar: lb2_sidebar
permalink: /doc/en/lb2/Creating-components.html
summary:
---

## Overview

LoopBack _components_ are predefined packages that extend a basic LoopBack application.
Fundamentally, a component is related code bundled together as a unit to enable LoopBack applications to easily reuse it.
They are registered to the app using 
[`component-config.json`](/pages/createpage.action?spaceKey=APIC&title=component-config.json&linkCreation=true&fromPageId=9634257).

Components can be official [StrongLoop npm packages](/doc/en/lb2/LoopBack-components.html) or custom components. This page is about custom components.

## How to create components

A LoopBack component is basically a node module which accepts a LoopBack application instance and an accompanying configuration object.
Using the available LoopBack application instance and the configuration object, the functionality of the component can be programmed according to the requirements.

{% include note.html content="

If a component is not published on npm, the convention is to put the component file in the `server/components` directory.

" %}

The following are some examples to help you understand the components API.

In this example, the component mounts a middleware on the path specified in the `path`,
property of the `options` object, which prints a message "Your Component", when a request is made to the path.

**server/components/my-component.js**

```javascript
module.exports = function (loopbackApplication, options) {
  loopbackApplication.use(options.path, function (req, res, next) {
    res.send('Your Component');
  });
};
```

In this example, the component mounts a middleware which prints the `options` object to the console.

**server/components/show-options.js**

```javascript
module.exports = function (loopbackApplication, options) {
  loopbackApplication.use(function (req, res, next) {
    console.log(options);
    next();
  });
};
```

{% include note.html content="

To better understand how routing works in LoopBack and how it affects components, refer [Routing](/doc/en/lb2/Routing.html).

" %}

While a lot of components mount a middleware of some kind, all components need not do so.
For example, this component prints the `loopback` version on initialization, and does nothing more than that.

**server/components/version.js**

```javascript
module.exports = function (loopbackApplication) {
  var version = loopbackApplication.loopback.version;
  console.log('LoopBack v%s', version);
};
```

The `options` parameter passed to the component function is the value specified in the
[`component-config.json`](pages/createpage.action?spaceKey=APIC&title=component-config.json&linkCreation=true&fromPageId=9634257) file for the component.
The value can be any valid JavaScript data type.
The next section explains how to register components using the
[`component-config.json`](pages/createpage.action?spaceKey=APIC&title=component-config.json&linkCreation=true&fromPageId=9634257) file, and specify its `options` property.

## Registering components

An application will load all components that have an entry in the
[component-config.json](pages/createpage.action?spaceKey=APIC&title=component-config.json&linkCreation=true&fromPageId=9634257) that does not evaluate to false.
If the component is installed using `npm`, use its package name as the key; if the component is a local component, specify its relative path from the `server` directory.

Here is an example of a `component-config.json` file with entries for a component installed via `npm`, and the three local components described earlier.

```javascript
{
  "loopback-component-explorer": {
    "mountPath": "/explorer"
  },
  "./components/my-component": {
    "path": "/my-component"
  },
  "./components/show-options": {},
  "./components/version": true
}
```