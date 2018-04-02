

```python
#Import dependencies
import requests
import random
import json
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import urllib3
import openweathermapy.core as owm
from citipy import citipy

from config import api_key
```


```python
#Create range for lat values 
lat_range= np.arange(-90,90.01,.01)
#Create range for long
long_range= np.arange(-180,180.01,.01)
type(lat_range)
```




    numpy.ndarray




```python
#Generate random sample from long_range (1000 values)
long_samples = np.random.choice(long_range, 1000)
#Generate random sample from lat_range (1000 values)
lat_samples = np.random.choice(lat_range, 1000)
```


```python
#set samples to tuple list of (lat,long)
rand_coord = zip(lat_samples, long_samples)
```


```python
#set up list to hold response info
cities = []

#loop through rand_lat_long to find closest cities
for coord in rand_coord:
    city = citipy.nearest_city(coord[0], coord[1])
    city_name = city.city_name
    cities.append(city_name)
```


```python
#create setting dictionary with informatin we're interested in 
settings = {"units": "metric", "appid": api_key}

#Make empty lists for call data:
#Temp(%), humidity(%), cloudiness(%), wind speed (mph), lat, long
temp = []
hum = []
cl = []
ws = []
c_lat = []
c_long = []
c_name = []
```


```python
# Get current weather for rand_coord
for city in cities:
    try: 
        results = owm.get_current(city, **settings)
        temp.append(results['main']['temp']) 
        c_lat.append(results['coord']['lat'])
        c_long.append(results['coord']['lon'])
        hum.append(results['main']['humidity'])
        cl.append(results['clouds']['all'])
        ws.append(results['wind']['speed'])
        c_name.append(results['name'])
    except:
        #print(f"Couldn't find city {city}")
        pass
```


```python
len(c_lat)
```




    907




```python
#append lists into data frame
#Temp(%), humidity(%), cloudiness(%), wind speed (mph), (lat/long?)
weather_df = pd.DataFrame({'City':c_name, 'Latitude': c_lat, 'Longitude': c_long,'Wind Speed (mph)':ws,'Humidity(%)':hum,'Cloudiness(%)':cl,'Temperature(%)':temp,})
```


```python
#Make scatter plot Lat v. Temp
lat_temp_plot = weather_df.plot(kind='scatter', x='Latitude', y='Temperature(%)', title='Latitude v. Temperature(%)')
```


![png](output_9_0.png)



```python
#Make scatter plot Lat v. Cloudiness
lat_cloud_plot = weather_df.plot(kind='scatter', x='Latitude', y='Cloudiness(%)', title="Latitude v. Cloudiness(%)")
```


![png](output_10_0.png)



```python
#Make scatterplot Lat. v. Wind Speed
lat_wind_plot = weather_df.plot(kind='scatter', x='Latitude', y='Wind Speed (mph)', title="Latitude v. Wind (mph)")
```


![png](output_11_0.png)



```python
# Make Scatterplot Lat. v. Humidity
lat_hum_plot = weather_df.plot(kind='scatter', x='Latitude', y='Humidity(%)', title="Latitude v. Humidity")
```


![png](output_12_0.png)

