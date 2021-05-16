# Overview
With Map2Geo, you can define your own format for the transmission intent of geographic information (Ver 4.00 or later).
With the definition file "intents.ini", it is possible to add its original support to applications other than the transmission target that Map2Geo supports as standard.

# intents.ini
User's own additional format definition file.
* ＜Internal shared storage＞/map2geo/intents.ini
* Text file
* Line feed code :CR \| LF \| CRLF
* Encoding :UTF-8 is recommended

## Structure of file contents
* It consists of a list of entries of key = value.
* Divide the definition of each application by [section name].
* Spaces and tabs at the beginning and end of a line are ignored.
* Lines beginning with ";" (semicolon) are ignored as comment lines.
    * It is not regarded as a comment except at the beginning of a line.

```
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
* Providing a section for each transmission target application.
    * Entries written before the first section are treated as entries of section name "" (empty string).
    * If section names overlap, they are combined as the same section.
* The section name is an any string.
* The key is a predefined string (see below).
* The value is an any string.

## Example
```
[0]
;Activate Google Maps with always zoom level 20 and place name as "Example".
data=geo:{lat},{lng}?q={lat},{lng}(Example)&z=20
package=com.google.android.apps.maps
```
You can try this by copying to a text file and placing it as ＜Internal shared storage＞/map2geo/intents.ini.

# Process of file parse
Parsed by the following rule:
* Parsed in the order of appearance of sections.
* The presence of an application that accepts the URI described in the "data" entry for each section is checked, and if it exists, it is regarded as an transmission target application..
    * Each entry of identifier, package, and class is evaluated as a secondary condition.
* All sections are parsed sequentially regardless of whether or not the transmission target application is detected.
* Based on each entry of data, identifier, package, class, the existence of the transmission target application is listed up.
* Applications already listed are not listed duplicately.
    * If it is the same package and the content of the "class", "iconactivity" entry used for detection is the same, it is determined to be duplicated.
* Map2Geo standard target application definition is parsed after this intents.ini parsing.
    * This means that you can override the standard behavior with this file.

# Key
* Capital and lowercase letters are distinguished.
* Do not put spaces between Key, =, Value.
* Do not enclose the value with double quotes.

The list of keys is as follows:

|Key|Meaning|Default value|Example|
|---|---|---|---|
|data|URI template of intent to be sent|Empty string|geo:{lat},{lng}|
|package|Package name of destination app|Empty string|com.google.android.apps.maps|
|class|Activity name of destination app|Empty string|com.google.android.maps.MapsActivity|
|identifier|URI template for existence confirmation of destination|Empty string|google.navigation:?ll={lat},{lng}|
|iconactivity|Activity name of app with icon and label to display as destination|Empty string|com.google.android.maps.driveabout.app.DestinationActivity|
|action|Intent's action string|android.intent.action.VIEW|android.intent.action.SEND|
|label|Display name of the destination app|App Name|Google Maps on Web|

## data
* Applying coordinate values etc. to the URI template defined here is sent to the destination application.
* Also, based on this value, applications that can be transmitted are searched.
    * If package is specified, it will be given priority.

The syntax details are described below.

## package
* To specify the transmission target application, specify the package name with this entry.
* In the absence of this entry, applications responding to URI based on data (or identifier) are listed as transmission targets.

## class
* To specify the activity of the application to be sent, specify the class name of the activity in this entry.
* Ignored if no package is specified.

## identifier
* Template of URI used for searching transmission target applications.
* Used when want to search target application with URI different from the actually transmitted data.
    * Specifically, it is used when data transmission is performed with android.intent.action.SEND (= transmission data is not URI).

## iconactivity
* Used when want to use icon and label of specific activity  as a destination list icon. 
    * The activity must be included in the package of the destination application.
    * If activity does not exist, default icon and label are used.

## action
* Specify this when want to apply intent actions other than standard (android.intent.action.VIEW).
    * Specifically, it is used when data transmission is performed with android.intent.action.SEND (text transmission).

## label
* Specify the display name of the destination app.
    * The destination with a different label is regarded as a different destination even if it is the same app.
    * For example, if you want to set up multiple destinations such as opening a web page in Chrome, you can do so by giving each a different label.

# URI Template Syntax
The syntax of the data, identifier entry of intents.ini is as follows:

## Basic syntax
* It consists of concatenation of strings, values, conditional phrases.
* Conditional phrases can be nested.
* It must be completed in one line. Can not divide into multiple lines.
    * However, since {\ n} will be replaced by a line feed, it is possible to obtain multiline format results.

## Element
The elements of the template are as follows:

|Element|Use|
|---|---|
|String|ICopied to the URI as it is|
|Value|Replaced with the coordinate value etc. and copied to the URI|
|Conditional phrase|Control copying to the URI with the existence of a value, etc.|

Example:
```
geo:{lat},{lng}[zoom:&z={zoom}]
```

|Element|Type|
|---|---|
|geo:    |String|
|{lat}   |Value(Latitude)|
|,       |String|
|{lng}   |Value(Longitude)|
|[zoom:  |Start of conditional phrase(Valid if zoom level exists)|
|&z=     |String|
|{zoom}  |Value(Zoom level)|
|]       |End of conditional phrase|

## Value
The list of values is as follows:

|Value|Meaning|Remarks|
|---|---|---|
|{lat}     |Latitude(WGS)||
|{lng}     |Longitude(WGS)||
|{lon}     |Longitude(WGS)||
|{latms}   |Latitude(WGS Millisecond format)||
|{lngms}   |Longitude(WGS Millisecond format)||
|{lonms}   |Longitude(WGS Millisecond format)||
|{latjp}   |Latitude(Japan geodetic system)||
|{lngjp}   |Longitude(Japan geodetic system)||
|{lonjp}   |Longitude(Japan geodetic system)||
|{latmsjp} |Latitude(Japan geodetic system Millisecond format)||
|{lngmsjp} |Longitude(Japan geodetic system Millisecond format)||
|{lonmsjp} |Longitude(Japan geodetic system Millisecond format)||
|{zoom}    |Zoom level(real number)|Default value 18.0 is applied when the zoom level does not exist in the geographic information|
|{zoomint} |Zoom level(integer)|Default value 18 is applied when the zoom level does not exist in the geographic information|
|{label}   |Place name(URI encoded)|Empty string is applied when the place name does not exist in the geographic information|
|{rawlabel}|Place name(Not encoded)|Empty string is applied when the place name does not exist in the geographic information|
|{\n}      |Line feed| |

## Conditional phrase
Substring that begins with "[Condition:" or "[Condition!" and ends with"]".
Do not put spaces between "[", Condition, ":" "!".

* The conditional phrase becomes valid when the condition evaluation is true, and the condition phrase is discarded when it is false.
* The condition value can be reversed with the letter at the end of the conditional phrase start token:
    * ":" Colon: True if the condition is true.
    * "!" Exclamation: True if the condition is false.
* Condition phrase can include string, value, and can contain another conditional phrase with nesting.

Example:
`[zoom:zoom exists]` :If the zoom level exists, evaluated to the string "zoom exists", otherwise evaluated to the empty string.
`[zoom!zoom not exists]` :If the zoom level not exists, evaluated to the string "zoom not exists", otherwise evaluated to the empty string.
`[zoom:[label:zoom and label exist]]` If the zoom level and label exist, evaluated to the string "zoom and label exist", otherwise evaluated to the empty string.

The list of conditions is as follows:

|Condition|Meaning|
|---|---|
|zoom|Exists zoom level.|
|label|Exists place name.|
|latlng|Exists position coordinate.|
|latlon|Exists position coordinate.|
|version=Integer|Version code of the destination application is equal to an integer value.|
|version<Integer|Version code of the destination application is smaller than an integer value.|
|version>Integer|Version code of the destination application is larger than an integer value.|

## Example
```
geo:{lat},{lng}
```
>    `geo:35.660411,139.729265` , expanded as.

```
geo:{lat},{lng}[zoom:?z=zoom]
```
>    `geo:35.660411,139.729265`  or `geo:35.660411,139.729265?z=18.0` , expanded as.

```
geo:{lat},{lng}[label:?q={lat},{lng}({label})[zoom:&z={zoom}]][label![zoom:?z={zoom}]]
```
 >   It is expanded as follows:
 >    `geo:35.660411,139.729265` 
 >    `geo:35.660411,139.729265?q=35.660411,139.729265(google)` 
 >    `geo:35.660411,139.729265?q=35.660411,139.729265(google)&z=18.0` 
 >    `geo:35.660411,139.729265?z=18.0` 

# Remarks
Route transfer feature of Map2Geo can not be handled with this definition file.
This definition file is not referred to in route transfer processing.

# Tips
## Standard geo URI format transmitted by Map2Geo
It is as follows (at Ver4.00):
```
geo:{lat},{lng}[?z={zoomint}]
```
* "z = ..." is not given in Ver3.12 and below.
* It may be changed depending on the transmission destination application.

## Issuing intents other than geo intent
Intent that can be issued is not limited to geo intent.

### Launch browser
For example, Google Maps for the browser opens with the following definition:
```
data=https://www.google.com/maps/@{lat},{lng},{zoom}z
```
You can try this by making this line a text file and putting it as ＜Internal shared storage＞/map2geo/intents.ini.
Section description is not required (parsed as a section of the empty name).

### Share text
By specifying action=android.intent.action.SEND it is also possible to transfer text to memo apps etc.

In the case of action=android.intent.action.SEND, Map2Geo packs the string generated by data into Extra of Intent.EXTRA_TEXT, and sends it by generating an intent with type="text/plain".
```
data={label}{\n}https://www.google.com/maps/@{lat},{lng},{zoom}z
action=android.intent.action.SEND
```
With the above definition,
>Google
>https://www.google.com/maps/@35.660411,139.729265,16.0z

Text such as above will be shared with mailers and others.

However, in this example all the android.intent.action.SEND compatible applications are listed, and the sections after this that use android.intent.action.SEND are masked.
It is advisable to specify a specific application by appending description like `package=com.google.android.gm `.