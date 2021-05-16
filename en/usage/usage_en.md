# What is this
This is an app to transfer the place selected on the map app such as Google Maps to other apps.
Transfer is done via Android's "Sharing" function.

![][whats]

# Basic usage
Select the place you want to transfer on the map app

![][basis01]

----

Perform sharing

![][basis02]

----

Select this app as sharing destination

![][basis03]

----

A list of transfer target apps will be displayed

![][basis04]

----

The place will be transferred to the selected app

![][basis05]

# Route transfering
Routes created by Google Maps etc. can also be transferred.

Perform route sharing

![][route01]

----

Select this app as sharing destination

![][route02]

----

A list of transfer target apps will be displayed

![][route03]

----

The route will be transferred to the selected app

![][route04]

## Points to note about route transfer
### Path
In the route transfer, the departure place, waypoints and destination are transferred, but the path connecting them is selected by the transfer target app.
Therefore, different routes may be presented between the transfer source and the transfer target.

### Waypoint
Because the upper limit of the number of waypoints that can be included in the route differs depending on the app, if the number of waypoints of the transferred route is too large, the transfer may fail or only a part of the waypoint may be transferred.

### Conditions
Conditions such as transportation and toll road availability are not transferred.

# Create Shortcut
You can create a shortcut on the Home screen to open the place / route with the map app.

![][shortcut01]![][shortcut02]

## Place shortcut
Create it with the following procedure:
* Long tap the icon of the app
* Edit shortcut name
* A shortcut will be created on the home screen

![][shortcut03]

## Route shortcut
Route shortcuts can be created in the same procedure as the place.

![][shortcut04]

## Shortcut to open the app selection screen
By long tapping the Map2Geo icon on the app selection screen, you can create shortcut for open the app selection screen.

![][shortcut05]

![][shortcut06]

## Caution on creating shortcut
### Handling of current location
When transferring the route including the current location, handling of the current location differs depending on the transferring source app.

There are two specifications:
* Preserve current location at the time of sharing operation
* Apply current location at the time of referring to shared results each time

When creating shortcut of "Route including current location" transferred from the app of the former specification, the current location on the route is fixed at the terminal location at the time of sharing operation.

# Display location / route information
By short tapping the Map2Geo icon on the app selection screen, information on the place / route point will be displayed.

![][placeinfo01]

Tap an item to switch the display format of coordinates.

# Cooperation with URL injector
If Map2Geo URL injector is installed, any Map2Geo supported map app can be launched from the link for displaying the map.

![][icon_injector] Map2Geo URL injector:
https://play.google.com/store/apps/details?id=catfish.android.map2geo.urlinjector

When opening a link to a map displayed on a browser etc., if the URL injector supports it, can acquire the coordinates and transfer it to the map app.

![][injector01]


[icon]:./images/ic_launcher.png
[icon_injector]:./images/map2geo_urlinjector.png

[whats]:./images/whats.png

[basis01]:./images/basis01.png
[basis02]:./images/basis02.png
[basis03]:./images/basis03.png
[basis04]:./images/basis04.png
[basis05]:./images/basis05.png

[route01]:./images/route01.png
[route02]:./images/route02.png
[route03]:./images/route03.png
[route04]:./images/route04.png

[shortcut01]:./images/shortcut01.png
[shortcut02]:./images/shortcut02.png
[shortcut03]:./images/shortcut03.png
[shortcut04]:./images/shortcut04.png
[shortcut05]:./images/shortcut05.png
[shortcut06]:./images/shortcut06.png

[placeinfo01]:./images/placeinfo01.png

[injector01]:./images/injector01.png
