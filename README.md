# LabVIEW-HttpServer
HTTP Server written in LabVIEW, inspired by Flask's Web Server Gateway Interface (WSGI)

## Getting Started

Run the **Demo HttpServer.vi**, then in a web browser navigate to **http://localhost:5234/** to see the hosted website.

![HttpServer-Home](/docs/imgs/HttpServer-Home.png)

![HttpServer-Log](/docs/imgs/HttpServer-Log.png)

The HTTP Server LabVIEW library contains the minimal amount of embedded web files neccessary to host a website via LabVIEW. The server includes a `/favicon.png`, `/w3.css`, `/index.html`, error `404: Not Found` and `405: Method Not Supported` templates.

![DemoHttpServer](/docs/imgs/DemoHttpServer.png)

## Overview

This HTTP Server, written in LabVIEW, is based on the inner workings of Python's Flask Web Server Gateway Interface (WSGI) framework. Similar to Flask, this library uses functional global VIs to pass data between the WSGI and HTML rendering engine. The TCP layer abstracts the Http Request and Response similar to how WSGI is defined and a minimal HTML templating engine was built to render different webpage parts similar to how Flask's Jinja operates.

***Note: This library is meant as a proof of concept and is not ready for any type of production environment, obviously.***

![DemoWSGI](/docs/imgs/DemoWSGI.png)

## Add a webpage
Use the **Add Page.vi** to add a endpoint (i.e. webpage) and routes (i.e. paths) to the HTTP Server. The page is added to the site's Navbar automatically. Specify which HTML template (default: `__html__`) to render the page content, similar to how Flask's Jinja renders.

![DemoAddPage](/docs/imgs/DemoAddPage.png)

![HttpServer-TestPage](/docs/imgs/HttpServer-TestPage.png)

## Add a Endpoint
Use the **Add Endpoint.vi** to add custom html templates `__<template>__`, static files or custom content when a response is rendered. Like Jinja, the HTML rendering engine uses double curley bracket to define a partial `{{%__<partial>__%}}`, variables `{{<var>}}` or statements `{{for:<var>}}..{{endfor:<var>}}`. Unlike Jinja, to avoid nested blocks each end statement repeats the opening statement to avoid recursion checks (i.e. faster performance). 

The router maps the endpoints for specific HTTP Request paths. For instance `/`, `/index.html` and `/index.htm` would all be routed to the  `index` endpoint, similar to how Flask uses method decoractor:

```python
@app.route('/', methods=['GET'])
def index()
    pass
```

### Templates `__html__`

The built in HTML template:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>{{%__title__%}}</title>
<link href="/favicon.png" rel="icon" type="image/png" sizes="48x48"/>
<link href="/w3.css" rel="stylesheet">
{{%__head__%}}
</head>
<body>
{{%__navbar__%}}
<div style="margin-top:46px">
{{%__debug__%}}
{{%__content__%}}
{{%__footer__%}}
{{%__foot__%}}
</div>
</body>
</html>
```

### Partials `{{%__partial__%}}`

Partials are HTML sub-templates that are replaced when an HTML template is rendered. Partials are only rendered one time and are typically used to enhance components within the HTML templates.

```html
<!-- Partials use double curley brackets with % symbols -->
<div id='content'>
  {{%__content__%}}
</div>`
<!-- is rendered with content -->
<div id='content'>
  <p>This is some example content</p>
</div>
```

### Variables `{{<var[.key]>}}`

Variables are literal values to replaced in the templates and partials.

```html
<!-- Variables are enclosed in double curley brackets -->
<p>User requested {{request.method}} {{request.path}}</p>
```

### For Loops `{{for:<var.key>}} .. {{endfor:<var>`

For loops allow for 1D array looping to repeat the HTML rendering.

```html
<!-- Print out all request variables -->
{{for:query.__keys__}}
  <p>{{query.__key__}} : {{query:__value__}}</p>
{{endfor:query.__keys__}}
```

### If Statements `{{if:<var.key>}} .. {{endif:<var.key>}}`

```html
<!-- Conditionaly render HTML -->
{{if:debug}}
  <p>DEBUG MODE</p>
{{endif:debug}}
```

## Static Files

Static files are embedded in the endpoint's functional global. To add static files, call the **Add Endpoint.vi** without a **Template**, specify the **Content** (i.e. Static Files Binary Data), **Content-Type** mime-type (i.e. `image/png`, `application/json`, etc.) and **Paths** (i.e. `/myimage.png`).

When a file is requested, the router maps to the embedded file's endpoint and returns the raw file with the appropriate **Content-Type** and **Content-Length** response headers.

*Note: Embeddeding static files was a security design decision; and for easier real-time target embedded support.*

*More documentation to come...*