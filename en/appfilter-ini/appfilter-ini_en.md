---
layout: page_toc
title: "appfilter.ini reference"
---

# Overview
In Map2Geo, you can display a list of apps to which geographic information is transferred except for unnecessary apps (Ver5.01 or later).
With the definition file "appfilter.ini", it is possible to specify the apps to exclude from the list.

You can improve situations that cause inconvenience as apps that are never used are displayed every time.

# appfilter.ini
Definition file of the app to be excluded from the transfer destination app list.
* ＜Internal shared storage＞/map2geo/appfilter.ini
* Text file
* Line feed code: CR \| LF \| CRLF
* Encoding: UTF-8 is recommended

## Structure of file contents
* It consists of a list of entries of key = value.
* Define a section with [section name].
    * If the section names overlap, they are combined as the same section.
* The lines before the first section is handled as a description to the global section (section name "" (empty string)).
* Spaces and tabs at the beginning and end of a line are ignored.
* Lines beginning with ";" (semicolon) are ignored as comment lines
    * It is not regarded as a comment except at the beginning of the line

```
;global section
key=value
 :
 :
[section1]
;comment
key=value
 :
 :
[section2]
;comment
key=value
 :
 :
```
## Section
The following three kinds of sections are available:

| Section name | Applicable target |
| ---- | ---- |
| Global section | Entire app list |
| [place] | Place transfer destination app list |
| [route] | Route transfer destination app list |

## Key
The following two kinds of keys are available:

| Key name | Use |
| ---- | ---- |
| reject | Hide app with package name described in value |
| accept | Display app with package name described in value |

## Value
In the value, describe the package name of the app.
Wildcard description is possible.

| Wild card | Meaning |
| ---- | ---- |
| * (Asterisk) | Any length any character string (empty string also matches) |
|? | 1 character |

※ It is not a regular expression

# Display judgment rules
* Display / non-display determination is performed for each display candidate app.
* It is judged in order from the top of the entries.
* If there is a matching entry, terminate the judgment of the app there.
* Apps that did not match any entry are determined to be displayed.
* The [place] or [route] section is judged first and the global section is further judged for the result.

# Example
```
;Hide non-google apps
accept=com.google.*
reject=*
```

```
;Display only Google Maps and Waze in "open the place" app
[place]
accept=com.google.android.apps.maps
accept=com.waze
reject=*
```

You can try this by copying it to a text file and placing it as ＜Internal shared storage＞/map2geo/appfilter.ini.

# How to find the package name of the app
## With Map2Geo
By tapping the icon of the app on the title editing screen at creating the shortcut of the app, the package name of the app is displayed.

## From the GooglePlay URL
Open the pages of the target app in GooglePlay of the browser version, it is possible to know the package name from the URL.
The id parameter in the URL is the package name.

If it is Map2Geo, the URL is
`https://play.google.com/store/apps/details?id=catfish.android.map2geo`
and the package name
`catfish.android.map2geo`
can be obtained.