* note that the existing code is written in js and im required to try the same thing with python 
## FOLIUM :

* i found a framework called FOLIUM 
* what folium does is, it basically visualizes the leaflet map but the data is written in python
* the best part is that folium has integration with STREAMLIT 
* you can look at `https://folium.streamlit.app/` 
* also took a look at folium docs 
* folium is beautiful asf, you can legit write all your leaflet code in python
getting a map on ur screen using folium is as simple as 
``` python
import folium
m = folium.Map(location=(45.5236, -122.6750))
```
the default tile layer (idk what that is ) is also set to same as current tile layer in js 

NOTE: the only advantage of using folium is integration with streamlit 
you can do things like creating a [dashboard using streamlit and folium](https://www.youtube.com/watch?v=uXj76K9Lnqc)
easier for frontend and much more organized front end because streamlit of course 
but i dont know if streamlit has a voice input feature.
## QUESTIONABLE SHI :
1. if we plan on making the ai things python, we cant. LMAO coz the tensorflow speech command library that we're using has in built voice commands that we can easily map to functions and there is no tension
2. however if we want to use something else like pytorch we need to take in data, use voice to text, train model on text and then map functions to it
3. in my opinion its better to stick with js 
## POSSIBLE CHANGES :
1. handling data : currently we're only fixed to blr's lat and longitude, need to make that flexible. I suggest we use a csv loader with lat and long of places, and then fetch required details, but again our voice commands are limited.
2.  fixing that switch case. because why not ?. 
---

