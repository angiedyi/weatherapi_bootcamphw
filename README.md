# weatherapi_bootcamphw

 ```python
 #pip install citipy
import csv
import os
from random import uniform
from random import random
import matplotlib.pyplot as plt
import seaborn as sb
import requests as req
import pandas as pd
import json
from citipy import citipy as cp
import math
```
```python

#Weather API
api_key = "62262785d73674ea77c4cb358e1f421e"
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "IMPERIAL"

#Looking up a city nearest to the equator - test
#city = cp.nearest_city(0, 0)
#print(city)             
#print(city.city_name)
#print(city.country_code)

cities = []
#random number generator for latitude, longitude; set limits -60 lat, 80 lat
def randompoint():
   return uniform(-60, 80), uniform(-90, 90)

points = (randompoint() for x in range(100))
for point in points:
        xlat = point[0]
        ylon = point[1]
        city = cp.nearest_city(xlat, ylon)
        
        if city.city_name not in cities:
            cities.append(city.city_name)

new_cities = cities
```
```python
# Build partial query URL
query_url = url + "appid=" + api_key + "&units=" + units + "&q="
weather_data = [req.get(query_url + city).json() for city in new_cities]
print(json.dumps(weather_data, indent=2))
```
```python
csv_data = []

for data in weather_data:
    cities_name = data.get("name")
    cloud_data = data.get("clouds", {}).get("all", 0)
    country_data = data.get("sys", {}).get("country", "Unknown")
    date_data = [data.get("dt", 0) for data in weather_data]
    lat_data = data.get("coord", {}).get("lat", 0)
    lon_data = data.get("coord", {}).get("lon", 0)
    humidity_data = data.get("main", {}).get("humidity", 0)
    maxtemp_data = data.get("main", {}).get("temp_max", 0)

    single_row = [cities_name,cloud_data,country_data,date_data,lat_data,lon_data,humidity_data,maxtemp_data]
    csv_data.append(single_row)
    
root_path = os.path.join(os.getcwd())
data_path = os.path.join(root_path)
csvpath = os.path.join(root_path, "weather_data.csv")
with open(csvpath, "w", newline="") as csvfile:
    csvwriter = csv.writer(csvfile, delimiter=",")
    csvwriter.writerow(["City Name", "Cloudiness", "Country", "Date", "Latitude","Longitude","Humidity","Max Temp"])
    csvwriter.writerow(csv_data)
```
```python
cloud_data = [data.get("clouds", {}).get("all", 0) for data in weather_data]
country_data = [data.get("sys", {}).get("country", "Unknown") for data in weather_data]
date_data = [data.get("dt", 0) for data in weather_data]
lat_data = [data.get("coord").get("lat") for data in weather_data]
lon_data = [data.get("coord").get("lon") for data in weather_data]
humidity_data = [data.get("main", {}).get("humidity", 0) for data in weather_data]
maxtemp_data = [data.get("main", {}).get("temp_max", 0) for data in weather_data]
windspeed_data = [data.get("wind", {}).get("speed", 0) for data in weather_data]
```
```python
print(len(new_cities))
print(len(cloud_data))
print(len(country_data))
print(len(date_data))
print(len(lat_data))
```
```python
weather_data = pd.DataFrame(
    {
        "City": new_cities,
        "Cloudiness": cloud_data,
        "Country": country_data,
        "Date": date_data,
        "Humidity": humidity_data,
        "Lat": lat_data,
        "Lon": lon_data,
        "Max Temp": maxtemp_data,
        "Wind Speed": windspeed_data
    }
)

weather_data.head()
```
```python
plt.scatter(weather_data["Lat"], weather_data["Max Temp"], marker="o")

plt.title("City Latitude vs. Max Temperature (08/22/17)")
plt.ylabel("Max Temperature")
plt.xlabel("Latitude")
plt.grid(True)

plt.show()
```
![alt text](https://github.com/angiedyi/weatherapi_bootcamphw/blob/master/latvstemp.png)

```python
:
plt.scatter(weather_data["Lat"], weather_data["Humidity"], marker="o")

plt.title("City Latitude vs. Humidity (08/22/17)")
plt.ylabel("Humidity")
plt.xlabel("Latitude")
plt.grid(True)

plt.show()
```

![alt text](https://github.com/angiedyi/weatherapi_bootcamphw/blob/master/latvshumid.png)

```python
plt.scatter(weather_data["Lat"], weather_data["Cloudiness"], marker="o")

plt.title("City Latitude vs. Cloudiness (08/22/17)")
plt.ylabel("Cloudiness")
plt.xlabel("Latitude")
plt.grid(True)

plt.show()
```

![alt text](https://github.com/angiedyi/weatherapi_bootcamphw/blob/master/latvscloud.png)

```python
plt.scatter(weather_data["Lat"], weather_data["Wind Speed"], marker="o")

plt.title("City Latitude vs. Wind Speed (08/22/17)")
plt.ylabel("Cloudiness")
plt.xlabel("Wind Speed")
plt.grid(True)

plt.show()
```
![alt text](https://github.com/angiedyi/weatherapi_bootcamphw/blob/master/latvswind.png)
