---
layout: page_toc
title: "External decoder reference"
---

# Overview
For Map2Geo Ver 6.00 or later, users can program their own coordinates analysis process when sharing from map app etc. to Map2Geo.

By placing a file describing JavaScript (hereinafter referred to as a script file) on the Internal shared storage of device, it is possible to decode the data shared from the map app etc. with the JavaScript and obtain coordinates to transfer to other apps .

# Outline of behavior
The data shared from the map app usually consists of a multi-line character string.
As usual, each line contains the name of the place, the URL for opening the place in the browser etc, and these are passed to the function `decode()` in the script file.

The received function gets the place name and coordinates from each of those lines and passes it to the function `setResult()` prepared by the app side to complete or stop the decoding.

If the coordinate value is written in the URL, you can get the coordinates from it, but if not, you will get the contents of the page indicated by the URL and analyze it to obtain the coordinates.

# File specification

| | |
| --- | --- |
| File name | &lt;any name>.html |
| File location | &lt;Internal shared storage>/map2geo/decoder/ |
| File format | html |
| Character encoding | UTF-8 recommended |

The JavaScript part must be surrounded by `<script>...</script>` according to the format of html.
All tags relating to the description of documents, such as `<html>, <head>, <body>`, etc. are unnecessary, but even if they are described it will work.

# Rules for applying files
## Application sequence with built-in decoder
Decoding by script file is done first.
And if it is not subject to decoding for all script files, Map2Geo will process with built-in decoder.

## Multiple script files
Multiple script files are available.
It is possible to operate such that one script file is for one sender app.

## Order of application of script file
* It is sequentially applied in ascending order of file names.
* If data that is not processed by a script file is passed, it is decoded sequentially by the next script file.
* If neither script file is processed, it will be processed by Map2Geo built-in decoder.

### Change the order of application
A specific script file can be applied first by preparing a text file describing the file name

| | |
|---|---|
| File name | order.cfg |
| File location | &lt;Internal shared storage\>/map2geo/decoder/ |
| Character encoding | UTF-8 recommended |
| Format | One file name on one line |

The files listed in order.cfg are applied in order from the top.

The script file in the folder
```
1.html
2.html
3.html
4.html
```
And order.cfg
```
4.html
3.html
```
The result is
```
4.html
3.html
1.html
2.html
```

# Script specification
The framework is the process of parsing the text passed in `decode()` and returning the result with `locationDecoder.setResult()`.

## Required function in script
Implementation of the following function is required:
`function decode(text)`
* **Arguments "text"** :String sent by sharing. Can contain line breaks.
* **Return value** :None

## Objects provided from Map2Geo
The object `locationDecoder` can be referenced from JavaScript:

Member function of `locationDecoder` object :
* `setResult(result, latlng, zoom, title, url)` ... Return a place
* `setResult(result, waypoints, zoom, title, url)` ... Return a route

either one call is required.
By calling these, the decoding process in that script is completed or aborted.

The following member functions are provided:

### setResult(result, latlng, zoom, title, url)
Returns a place information of the decoding result to Map2Geo.

**Return value** :None

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| result | integer | Constant indicating decode result: 0=success, 1=not to be decoded, -1=decode failure |
| latlng | string | String representing the point in "latitude,longitude".
| zoom | number | Map zoom level. unspecified if -1. |
| title | string | Place name (may be null). |
| url | string | URL for displaying the place in the browser extracted from the data passed in decode() (may be null). |

latlng, zoom, title, url are invalid if result is not 0(success) and will not be referred. In that case, each value can be null.

The argument url is used to opening the URL with a browser etc. when using Map2Geo URL Injector.

### setResult(result, waypoints, zoom, title, url)
Returns a route information of the decoding result to Map2Geo.

**Return value** :None

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| result | integer | Constant indicating decode result: 0=success, 1=not to be decoded, -1=decode failure |
| waypoints | string array | Array of strings of "latitude,longitude,placename" to indicate route starting point, waypoint, destination.<br>placename is optional. <br>If the array element is null or does not indicate latitude and longitude, that element is treated as the current location.
| title | string | Title of the route (may be null).
| url | string | URL for displaying the route in the browser extracted from the data passed in decode() (may be null). |

waypoints, title, url are invalid if result is not 0(success) and will not be referred. In that case, each value can be null.

The argument url is used to opening the URL with a browser etc. when using Map2Geo URL Injector.

### getContent(url)
Synchronously gets the content of the page specified by url.
It will not return from the function until processing is complete.

**Return value** :String. Page content indicated by url.

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| url | string | page URL |

### getContent(url, ua)
Synchronously gets the content of the page specified by url.
It will not return from the function until processing is complete.

**Return value** :String. Page content indicated by url.

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| url | string | page URL |
| ua | string | user agent |

### getRedirect(url)
Synchronously acquires the redirect destination of url.
It will not return from the function until processing is complete.
Access as much as possible with the HEAD method (method selection depends on Android version).

**Return value** :String. The URL of the redirect destination of url.

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| url | string | page URL |

### getRedirect(url, ua)
Synchronously acquires the redirect destination of url.
It will not return from the function until processing is complete.
Access as much as possible with the HEAD method (method selection depends on Android version).

**Return value** :String. The URL of the redirect destination of url.

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| url | string | page URL |
| ua | string | user agent |

### getRedirect(url, ua, useGet)
Synchronously acquires the redirect destination of url.
It will not return from the function until processing is complete.

**Return value** :String. The URL of the redirect destination of url.

**Argument**

| Argument | Type | Meaning |
|---|---|---|
| url | string | page URL |
| ua | string | user agent |
| useGet | boolean | If true, access uses GET method instead of HEAD. |

# Tips
* The `alert(), confirm(), prompt()` dialogs are available in JavaScript.
    * However, you can not change the title of the dialog.
* External js file can be used by writing `<script src="..."></script>` in script file.
    * If the specified file does not exist, it is ignored without causing an error. This behavior is convenient for debugging.
* The synchronous request by `XMLHttpRequest#open()` on the main thread of the script is not available.
    * The script execution will be terminated.
    * If you want to acquire content synchronously, you can use `locationDecoder.getContent()`. There is no problem as it becomes asynchronous process by app.

# Examples
You can try behaviors by making these contents as text files and placing them on the device as `<Internal shared storage>/map2geo/decoder/example.html`.

## Example of returning the same place regardless of what you had shared
```
<script>
	function decode(text) {
		locationDecoder.setResult(0,"35.6604083,139.7292602",20,"Google-Tokyo","http://www.google.com/");
	}
</script>
```

## Example of returning the route from the current location to the same place regardless of what you had shared
```
<script>
	function decode(text) {
		locationDecoder.setResult(0,[null,"35.6604083,139.7292602,Google-Tokyo"],"Route sample","http://www.google.com/");
	}
</script>
```

## Example of passing through without processing anything
As a result, analysis processing is handed over to another decoder.
```
<script>
	function decode(text) {
		locationDecoder.setResult(1,null,null,null,null);
	}
</script>
```

## Examples that do not process even after sharing anything and do not decode afterward
As a result, acquisition of coordinates always fails.
```
<script>
	function decode(text) {
		locationDecoder.setResult(-1,null,null,null,null);
	}
</script>
```

## Decode sharing from MAPS.ME
This is a practical implementation.
This script extracts the title and URL from the shared text, obtains the content indicated by the URL and extracts the coordinates.

If you do not have MAPS.ME installed, you can try this by selecting and sharing the following text on your device's browser:

> Check out
> Roppongi Hills Mori Tower
> Building
> ge0://03g2betT_T/Roppongi_Hills_Mori_Tower
> http://ge0.me/03g2betT_T/Roppongi_Hills_Mori_Tower

```
<script type="text/javascript">
var SUCCEEDED=0;
var FAILED=-1;
var UNMATCHED=1;
var NOZOOM=-1;
var regexp_url = /https?:\/\/[^\s　]+/g;

var url;
var title;

function extractUrl(string) {
	return string.match(regexp_url);
}

function decode(text) {
	var lines=text.split("\n");
	for(var i=0;i<lines.length;i++) {
		var isUrl=false;
		if(url==null) {
			var urls=extractUrl(lines[i]);
			if(urls!=null) {
				url=urls[0];
				if(url.search(/^https?:\/\/ge0.me\/.*/)<0) url=null;
			}
		}
		if(!isUrl && i==1) title=lines[i];
	}
	
	if(url==null) {
		locationDecoder.setResult(UNMATCHED,null,null,null);
		return;
	}

	if(title==null) {
		//extract <title> from http://ge0.me/xxxx/<title>
		var parts=url.match(/(?:.+ge0\.me\/[^/]+\/)(.*)/);
		title=parts[1].replace("_"," ");
	}

	//get contents
	var content=locationDecoder.getContent(url);
	parseContent(content);
}

function parseContent(content) {
	if(content==null) {
		locationDecoder.setResult(FAILED,null,null,null,null);
		return;
	}
	
	var latlng;
	var matchResult=content.match(/var\s+coord\s+=\s+\[(.+)\]/);
	if(matchResult!=null) latlng=matchResult[1];
	
	var result=(latlng!=null) ? SUCCEEDED:FAILED;
	title="SAMPLE:"+title;
	locationDecoder.setResult(result,latlng,NOZOOM,title,url);
}
</script>
```