# API-challenge


## ANALYZE WEATHER CONDITIONS FOR 500+ RANDOM CITIES WITH RESPECT TO IT'S LATITUDE AS COMPARED TO EQUATOR

### GET RANDOM CO-ORDINATES


```python
# Dependencies
import json
import numpy as np
import pandas as pd
from pprint import pprint
import requests as req
import openweathermapy as owm
import config
import time
from citipy import citipy
import matplotlib.pyplot as plt
import seaborn as sns
```


```python
# Generate random latitudes and longitudes
random_lat = pd.Series(np.random.randint(-90,90,size=(5500)))
random_lon = pd.Series(np.random.randint(-180,180,size=(5500)))

random_coord = pd.DataFrame({"Lat":random_lat, "Lon":random_lon}).astype('float')
random_coord.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lon</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-19.0</td>
      <td>51.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>84.0</td>
      <td>-89.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>16.0</td>
      <td>-78.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>29.0</td>
      <td>-163.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>86.0</td>
      <td>45.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#### TEST
len(random_coord)
```




    5500



### GET CITIES FOR RANDOM CO-ORDINATES


```python
# Get cities, countries from citipy for lat and lon
city_list = []
country_list = []
for index, row in random_coord.iterrows():
    coord_obj = citipy.nearest_city(row['Lat'], row['Lon'])
    city_name = coord_obj.city_name
    country_code = coord_obj.country_code
    city_list.append(city_name)
    country_list.append(country_code)
    
#len(city_list)
len(country_list)
```




    5500




```python
# Add cities and countries to DF
random_coord['City']=city_list
random_coord['Country']=country_list
random_coord.head()
len(random_coord)

#### TEST
#x=random_coord['City'].value_counts()
#print(x)
x=random_coord['City'].unique()
print(len(x))
#print(x)
```

    1537



```python
# Drop duplicate cities
random_coord.drop_duplicates('City', inplace=True)
```


```python
#### TEST
if citipy.nearest_city(random_coord['Lat'][3], random_coord['Lon'][3]).city_name == random_coord['City'][3]:
    print("The data collected from citipy is {} matches the city in df which is {}".format(citipy.nearest_city(random_coord['Lat'][3], random_coord['Lon'][3]).city_name,random_coord['City'][3]))
else:
    print("NOT MATCHING!!!!!!!!")
```

    The data collected from citipy is kapaa matches the city in df which is kapaa



```python
# Set labels for columns to add WEATHER data later
random_coord['Max Temp']=''
random_coord['Humidity']=''
random_coord['Cloudiness']=''
random_coord['Wind Speed']=''
random_coord=random_coord.reset_index(drop=True)
random_coord.head()
#TEST
len(random_coord)
random_coord
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Lat</th>
      <th>Lon</th>
      <th>City</th>
      <th>Country</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-19.0</td>
      <td>51.0</td>
      <td>toamasina</td>
      <td>mg</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>84.0</td>
      <td>-89.0</td>
      <td>qaanaaq</td>
      <td>gl</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>16.0</td>
      <td>-78.0</td>
      <td>bull savanna</td>
      <td>jm</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>29.0</td>
      <td>-163.0</td>
      <td>kapaa</td>
      <td>us</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>86.0</td>
      <td>45.0</td>
      <td>belushya guba</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.0</td>
      <td>-40.0</td>
      <td>acarau</td>
      <td>br</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>6</th>
      <td>-74.0</td>
      <td>157.0</td>
      <td>bluff</td>
      <td>nz</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>7</th>
      <td>-37.0</td>
      <td>140.0</td>
      <td>mount gambier</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>8</th>
      <td>-40.0</td>
      <td>39.0</td>
      <td>margate</td>
      <td>za</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>9</th>
      <td>62.0</td>
      <td>-40.0</td>
      <td>tasiilaq</td>
      <td>gl</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>10</th>
      <td>-34.0</td>
      <td>141.0</td>
      <td>mildura</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>11</th>
      <td>-80.0</td>
      <td>-128.0</td>
      <td>rikitea</td>
      <td>pf</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>12</th>
      <td>78.0</td>
      <td>74.0</td>
      <td>dikson</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>13</th>
      <td>-73.0</td>
      <td>40.0</td>
      <td>port alfred</td>
      <td>za</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>14</th>
      <td>-55.0</td>
      <td>73.0</td>
      <td>souillac</td>
      <td>mu</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>15</th>
      <td>35.0</td>
      <td>10.0</td>
      <td>sidi bu zayd</td>
      <td>tn</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>16</th>
      <td>56.0</td>
      <td>-127.0</td>
      <td>smithers</td>
      <td>ca</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>17</th>
      <td>0.0</td>
      <td>62.0</td>
      <td>victoria</td>
      <td>sc</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>18</th>
      <td>-54.0</td>
      <td>-54.0</td>
      <td>ushuaia</td>
      <td>ar</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>19</th>
      <td>15.0</td>
      <td>-72.0</td>
      <td>pedernales</td>
      <td>do</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>20</th>
      <td>53.0</td>
      <td>142.0</td>
      <td>lazarev</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>21</th>
      <td>13.0</td>
      <td>-63.0</td>
      <td>gouyave</td>
      <td>gd</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>22</th>
      <td>-56.0</td>
      <td>102.0</td>
      <td>busselton</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>23</th>
      <td>84.0</td>
      <td>-59.0</td>
      <td>upernavik</td>
      <td>gl</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>24</th>
      <td>-73.0</td>
      <td>-93.0</td>
      <td>punta arenas</td>
      <td>cl</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>25</th>
      <td>48.0</td>
      <td>-90.0</td>
      <td>thunder bay</td>
      <td>ca</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>26</th>
      <td>-49.0</td>
      <td>128.0</td>
      <td>port lincoln</td>
      <td>au</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>27</th>
      <td>53.0</td>
      <td>80.0</td>
      <td>blagoveshchenka</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>28</th>
      <td>15.0</td>
      <td>-119.0</td>
      <td>cabo san lucas</td>
      <td>mx</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>29</th>
      <td>-57.0</td>
      <td>-150.0</td>
      <td>mataura</td>
      <td>pf</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1507</th>
      <td>-27.0</td>
      <td>-73.0</td>
      <td>copiapo</td>
      <td>cl</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1508</th>
      <td>46.0</td>
      <td>-7.0</td>
      <td>cervo</td>
      <td>es</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1509</th>
      <td>-27.0</td>
      <td>-48.0</td>
      <td>porto belo</td>
      <td>br</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1510</th>
      <td>41.0</td>
      <td>-104.0</td>
      <td>fort morgan</td>
      <td>us</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1511</th>
      <td>49.0</td>
      <td>0.0</td>
      <td>argentan</td>
      <td>fr</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1512</th>
      <td>26.0</td>
      <td>13.0</td>
      <td>awbari</td>
      <td>ly</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1513</th>
      <td>29.0</td>
      <td>51.0</td>
      <td>bushehr</td>
      <td>ir</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1514</th>
      <td>38.0</td>
      <td>37.0</td>
      <td>afsin</td>
      <td>tr</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1515</th>
      <td>-23.0</td>
      <td>49.0</td>
      <td>farafangana</td>
      <td>mg</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1516</th>
      <td>40.0</td>
      <td>-72.0</td>
      <td>mastic beach</td>
      <td>us</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1517</th>
      <td>22.0</td>
      <td>-64.0</td>
      <td>road town</td>
      <td>vg</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1518</th>
      <td>2.0</td>
      <td>23.0</td>
      <td>bumba</td>
      <td>cd</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1519</th>
      <td>63.0</td>
      <td>116.0</td>
      <td>almaznyy</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1520</th>
      <td>9.0</td>
      <td>-81.0</td>
      <td>santa fe</td>
      <td>pa</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1521</th>
      <td>-16.0</td>
      <td>-47.0</td>
      <td>unai</td>
      <td>br</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1522</th>
      <td>6.0</td>
      <td>5.0</td>
      <td>okitipupa</td>
      <td>ng</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1523</th>
      <td>48.0</td>
      <td>-100.0</td>
      <td>devils lake</td>
      <td>us</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1524</th>
      <td>36.0</td>
      <td>28.0</td>
      <td>lardos</td>
      <td>gr</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1525</th>
      <td>36.0</td>
      <td>33.0</td>
      <td>anamur</td>
      <td>tr</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1526</th>
      <td>38.0</td>
      <td>118.0</td>
      <td>binzhou</td>
      <td>cn</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1527</th>
      <td>51.0</td>
      <td>-1.0</td>
      <td>waterlooville</td>
      <td>gb</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1528</th>
      <td>23.0</td>
      <td>-105.0</td>
      <td>pueblo nuevo</td>
      <td>mx</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1529</th>
      <td>-9.0</td>
      <td>33.0</td>
      <td>mlowo</td>
      <td>tz</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1530</th>
      <td>0.0</td>
      <td>16.0</td>
      <td>owando</td>
      <td>cg</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1531</th>
      <td>55.0</td>
      <td>-69.0</td>
      <td>port-cartier</td>
      <td>ca</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1532</th>
      <td>48.0</td>
      <td>-106.0</td>
      <td>glendive</td>
      <td>us</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1533</th>
      <td>-39.0</td>
      <td>-62.0</td>
      <td>punta alta</td>
      <td>ar</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1534</th>
      <td>36.0</td>
      <td>-73.0</td>
      <td>virginia beach</td>
      <td>us</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1535</th>
      <td>4.0</td>
      <td>-85.0</td>
      <td>burica</td>
      <td>pa</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>1536</th>
      <td>58.0</td>
      <td>161.0</td>
      <td>palana</td>
      <td>ru</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
<p>1537 rows × 8 columns</p>
</div>



### CALL API AND RETRIEVE REQUIRED WEATHER INFORMATION


```python
# Save config information
base_url = 'http://api.openweathermap.org/data/2.5/weather'
api_key = config.owm_key
```


```python
# Create bins for set of 50 cities
bins = list(range(-1,1501,50))

#Create the names for the four bins
set_number = ['Set 1', 'Set 2', 'Set 3', 'Set 4', 'Set 5', 
              'Set 6', 'Set 7', 'Set 8', 'Set 9', 'Set 10', 
              'Set 11', 'Set 12', 'Set 13', 'Set 14', 'Set 15',
              'Set 16', 'Set 17', 'Set 18', 'Set 19', 'Set 20', 
              'Set 21', 'Set 22', 'Set 23', 'Set 24', 'Set 25',
              'Set 26', 'Set 27', 'Set 28', 'Set 29', 'Set 30']
# Cut index and place the ranges into bins
random_coord['Set'] = pd.cut(random_coord.index, bins, labels=set_number)
random_coord.head()
len(random_coord)
```




    1537




```python
#### TEST
city = random_coord['City'][0]
print(city)

params = {'appid': api_key,
          'q': city,
          'units': 'metric'}

response = req.get(base_url, params=params).json()
response['main']['temp_max']
```

    toamasina





    25




```python
# Iterate through lats and longs on open weather maps to get random cities
print(len(random_coord))
for index, row in random_coord.iterrows():
    time.sleep(0.5)
    
    city = row['City']
    city_set = row['Set'] 
    params = {'appid': api_key,
          'q': city,
          'units': 'imperial'}
    
    print(f"Retrieving Cities for {index} of {city_set} | {city}")
    print(f"{base_url}?appid={api_key}&q={city}&units={params['units']}")
    
    try: 
        response = req.get(base_url, params=params).json()

        random_coord.set_value(index, 'Max Temp', response['main']['temp_max'])
        random_coord.set_value(index, 'Humidity', response['main']['humidity'])
        random_coord.set_value(index, 'Cloudiness', response['clouds']['all'])
        random_coord.set_value(index, 'Wind Speed', response['wind']['speed'])
    
    except KeyError:
        print("Not Found!")

print("-------------------------")
print(" DATA RETRIEVAL COMPLETE ")
print("-------------------------")
```

    1537
    Retrieving Cities for 0 of Set 1 | toamasina
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=toamasina&units=imperial
    Retrieving Cities for 1 of Set 1 | qaanaaq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=qaanaaq&units=imperial
    Retrieving Cities for 2 of Set 1 | bull savanna
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bull savanna&units=imperial
    Retrieving Cities for 3 of Set 1 | kapaa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kapaa&units=imperial
    Retrieving Cities for 4 of Set 1 | belushya guba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=belushya guba&units=imperial
    Not Found!
    Retrieving Cities for 5 of Set 1 | acarau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=acarau&units=imperial
    Not Found!
    Retrieving Cities for 6 of Set 1 | bluff
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bluff&units=imperial
    Retrieving Cities for 7 of Set 1 | mount gambier
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mount gambier&units=imperial
    Retrieving Cities for 8 of Set 1 | margate
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=margate&units=imperial
    Retrieving Cities for 9 of Set 1 | tasiilaq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tasiilaq&units=imperial
    Retrieving Cities for 10 of Set 1 | mildura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mildura&units=imperial
    Retrieving Cities for 11 of Set 1 | rikitea
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rikitea&units=imperial
    Retrieving Cities for 12 of Set 1 | dikson
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dikson&units=imperial
    Retrieving Cities for 13 of Set 1 | port alfred
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port alfred&units=imperial
    Retrieving Cities for 14 of Set 1 | souillac
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=souillac&units=imperial
    Retrieving Cities for 15 of Set 1 | sidi bu zayd
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sidi bu zayd&units=imperial
    Not Found!
    Retrieving Cities for 16 of Set 1 | smithers
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=smithers&units=imperial
    Retrieving Cities for 17 of Set 1 | victoria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=victoria&units=imperial
    Retrieving Cities for 18 of Set 1 | ushuaia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ushuaia&units=imperial
    Retrieving Cities for 19 of Set 1 | pedernales
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pedernales&units=imperial
    Retrieving Cities for 20 of Set 1 | lazarev
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lazarev&units=imperial
    Retrieving Cities for 21 of Set 1 | gouyave
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gouyave&units=imperial
    Retrieving Cities for 22 of Set 1 | busselton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=busselton&units=imperial
    Retrieving Cities for 23 of Set 1 | upernavik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=upernavik&units=imperial
    Retrieving Cities for 24 of Set 1 | punta arenas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=punta arenas&units=imperial
    Retrieving Cities for 25 of Set 1 | thunder bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=thunder bay&units=imperial
    Retrieving Cities for 26 of Set 1 | port lincoln
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port lincoln&units=imperial
    Retrieving Cities for 27 of Set 1 | blagoveshchenka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=blagoveshchenka&units=imperial
    Retrieving Cities for 28 of Set 1 | cabo san lucas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cabo san lucas&units=imperial
    Retrieving Cities for 29 of Set 1 | mataura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mataura&units=imperial
    Retrieving Cities for 30 of Set 1 | vaitupu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vaitupu&units=imperial
    Not Found!
    Retrieving Cities for 31 of Set 1 | port hedland
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port hedland&units=imperial
    Retrieving Cities for 32 of Set 1 | chalmette
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chalmette&units=imperial
    Retrieving Cities for 33 of Set 1 | kruisfontein
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kruisfontein&units=imperial
    Retrieving Cities for 34 of Set 1 | lagoa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lagoa&units=imperial
    Retrieving Cities for 35 of Set 1 | vinh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vinh&units=imperial
    Retrieving Cities for 36 of Set 1 | beloha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=beloha&units=imperial
    Retrieving Cities for 37 of Set 1 | sao filipe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sao filipe&units=imperial
    Retrieving Cities for 38 of Set 1 | vaini
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vaini&units=imperial
    Retrieving Cities for 39 of Set 1 | aran
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aran&units=imperial
    Retrieving Cities for 40 of Set 1 | sentyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sentyabrskiy&units=imperial
    Not Found!
    Retrieving Cities for 41 of Set 1 | illoqqortoormiut
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=illoqqortoormiut&units=imperial
    Not Found!
    Retrieving Cities for 42 of Set 1 | padang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=padang&units=imperial
    Retrieving Cities for 43 of Set 1 | honiara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=honiara&units=imperial
    Retrieving Cities for 44 of Set 1 | kodiak
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kodiak&units=imperial
    Retrieving Cities for 45 of Set 1 | umzimvubu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=umzimvubu&units=imperial
    Not Found!
    Retrieving Cities for 46 of Set 1 | hammerfest
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hammerfest&units=imperial
    Retrieving Cities for 47 of Set 1 | bonfim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bonfim&units=imperial
    Retrieving Cities for 48 of Set 1 | sault sainte marie
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sault sainte marie&units=imperial
    Retrieving Cities for 49 of Set 1 | isangel
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=isangel&units=imperial
    Retrieving Cities for 50 of Set 2 | chokurdakh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chokurdakh&units=imperial
    Retrieving Cities for 51 of Set 2 | pauini
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pauini&units=imperial
    Retrieving Cities for 52 of Set 2 | atambua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=atambua&units=imperial
    Retrieving Cities for 53 of Set 2 | khatanga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=khatanga&units=imperial
    Retrieving Cities for 54 of Set 2 | bandarbeyla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bandarbeyla&units=imperial
    Retrieving Cities for 55 of Set 2 | chuy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chuy&units=imperial
    Retrieving Cities for 56 of Set 2 | chocaman
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chocaman&units=imperial
    Retrieving Cities for 57 of Set 2 | arraial do cabo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arraial do cabo&units=imperial
    Retrieving Cities for 58 of Set 2 | cidreira
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cidreira&units=imperial
    Retrieving Cities for 59 of Set 2 | ossora
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ossora&units=imperial
    Retrieving Cities for 60 of Set 2 | dhidhdhoo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dhidhdhoo&units=imperial
    Retrieving Cities for 61 of Set 2 | nanortalik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nanortalik&units=imperial
    Retrieving Cities for 62 of Set 2 | narsaq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=narsaq&units=imperial
    Retrieving Cities for 63 of Set 2 | little current
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=little current&units=imperial
    Retrieving Cities for 64 of Set 2 | ribeira grande
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ribeira grande&units=imperial
    Retrieving Cities for 65 of Set 2 | lamar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lamar&units=imperial
    Retrieving Cities for 66 of Set 2 | nome
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nome&units=imperial
    Retrieving Cities for 67 of Set 2 | fortuna
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fortuna&units=imperial
    Retrieving Cities for 68 of Set 2 | vredendal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vredendal&units=imperial
    Retrieving Cities for 69 of Set 2 | saskylakh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saskylakh&units=imperial
    Retrieving Cities for 70 of Set 2 | castro daire
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=castro daire&units=imperial
    Retrieving Cities for 71 of Set 2 | manokwari
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manokwari&units=imperial
    Retrieving Cities for 72 of Set 2 | salumbar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=salumbar&units=imperial
    Retrieving Cities for 73 of Set 2 | komsomolskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=komsomolskiy&units=imperial
    Retrieving Cities for 74 of Set 2 | caravelas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=caravelas&units=imperial
    Retrieving Cities for 75 of Set 2 | hithadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hithadhoo&units=imperial
    Retrieving Cities for 76 of Set 2 | lucea
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lucea&units=imperial
    Retrieving Cities for 77 of Set 2 | yellowknife
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yellowknife&units=imperial
    Retrieving Cities for 78 of Set 2 | esperance
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=esperance&units=imperial
    Retrieving Cities for 79 of Set 2 | imbituba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=imbituba&units=imperial
    Retrieving Cities for 80 of Set 2 | santo antonio do leverger
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santo antonio do leverger&units=imperial
    Retrieving Cities for 81 of Set 2 | kishi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kishi&units=imperial
    Retrieving Cities for 82 of Set 2 | klaksvik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=klaksvik&units=imperial
    Retrieving Cities for 83 of Set 2 | sambava
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sambava&units=imperial
    Retrieving Cities for 84 of Set 2 | port elizabeth
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port elizabeth&units=imperial
    Retrieving Cities for 85 of Set 2 | taolanaro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=taolanaro&units=imperial
    Not Found!
    Retrieving Cities for 86 of Set 2 | casablanca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=casablanca&units=imperial
    Retrieving Cities for 87 of Set 2 | ponta delgada
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ponta delgada&units=imperial
    Retrieving Cities for 88 of Set 2 | rocha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rocha&units=imperial
    Retrieving Cities for 89 of Set 2 | hilo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hilo&units=imperial
    Retrieving Cities for 90 of Set 2 | nikolskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nikolskoye&units=imperial
    Retrieving Cities for 91 of Set 2 | avarua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=avarua&units=imperial
    Retrieving Cities for 92 of Set 2 | puerto ayora
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto ayora&units=imperial
    Retrieving Cities for 93 of Set 2 | senanga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=senanga&units=imperial
    Retrieving Cities for 94 of Set 2 | lorengau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lorengau&units=imperial
    Retrieving Cities for 95 of Set 2 | bambous virieux
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bambous virieux&units=imperial
    Retrieving Cities for 96 of Set 2 | chumikan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chumikan&units=imperial
    Retrieving Cities for 97 of Set 2 | linxia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=linxia&units=imperial
    Retrieving Cities for 98 of Set 2 | san quintin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san quintin&units=imperial
    Retrieving Cities for 99 of Set 2 | hermanus
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hermanus&units=imperial
    Retrieving Cities for 100 of Set 3 | lucapa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lucapa&units=imperial
    Retrieving Cities for 101 of Set 3 | jamestown
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jamestown&units=imperial
    Retrieving Cities for 102 of Set 3 | albany
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=albany&units=imperial
    Retrieving Cities for 103 of Set 3 | barrow
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barrow&units=imperial
    Retrieving Cities for 104 of Set 3 | lavrentiya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lavrentiya&units=imperial
    Retrieving Cities for 105 of Set 3 | stornoway
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=stornoway&units=imperial
    Not Found!
    Retrieving Cities for 106 of Set 3 | lamont
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lamont&units=imperial
    Retrieving Cities for 107 of Set 3 | santa marta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa marta&units=imperial
    Retrieving Cities for 108 of Set 3 | tuatapere
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tuatapere&units=imperial
    Retrieving Cities for 109 of Set 3 | trofors
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=trofors&units=imperial
    Retrieving Cities for 110 of Set 3 | ondjiva
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ondjiva&units=imperial
    Retrieving Cities for 111 of Set 3 | guerrero negro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=guerrero negro&units=imperial
    Retrieving Cities for 112 of Set 3 | puerto del rosario
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto del rosario&units=imperial
    Retrieving Cities for 113 of Set 3 | vardo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vardo&units=imperial
    Retrieving Cities for 114 of Set 3 | cumbernauld
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cumbernauld&units=imperial
    Retrieving Cities for 115 of Set 3 | gashua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gashua&units=imperial
    Retrieving Cities for 116 of Set 3 | salalah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=salalah&units=imperial
    Retrieving Cities for 117 of Set 3 | carnarvon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=carnarvon&units=imperial
    Retrieving Cities for 118 of Set 3 | guantanamo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=guantanamo&units=imperial
    Retrieving Cities for 119 of Set 3 | rabo de peixe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rabo de peixe&units=imperial
    Retrieving Cities for 120 of Set 3 | burns lake
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=burns lake&units=imperial
    Retrieving Cities for 121 of Set 3 | pala
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pala&units=imperial
    Retrieving Cities for 122 of Set 3 | kedrovyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kedrovyy&units=imperial
    Retrieving Cities for 123 of Set 3 | faanui
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=faanui&units=imperial
    Retrieving Cities for 124 of Set 3 | sokoni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sokoni&units=imperial
    Retrieving Cities for 125 of Set 3 | khani
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=khani&units=imperial
    Retrieving Cities for 126 of Set 3 | veraval
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=veraval&units=imperial
    Retrieving Cities for 127 of Set 3 | mys shmidta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mys shmidta&units=imperial
    Not Found!
    Retrieving Cities for 128 of Set 3 | yendi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yendi&units=imperial
    Retrieving Cities for 129 of Set 3 | talnakh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=talnakh&units=imperial
    Retrieving Cities for 130 of Set 3 | aparecida do taboado
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aparecida do taboado&units=imperial
    Retrieving Cities for 131 of Set 3 | attawapiskat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=attawapiskat&units=imperial
    Not Found!
    Retrieving Cities for 132 of Set 3 | leningradskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=leningradskiy&units=imperial
    Retrieving Cities for 133 of Set 3 | vadso
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vadso&units=imperial
    Retrieving Cities for 134 of Set 3 | pevek
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pevek&units=imperial
    Retrieving Cities for 135 of Set 3 | moron
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=moron&units=imperial
    Retrieving Cities for 136 of Set 3 | norman wells
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=norman wells&units=imperial
    Retrieving Cities for 137 of Set 3 | sovetskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sovetskiy&units=imperial
    Retrieving Cities for 138 of Set 3 | kahului
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kahului&units=imperial
    Retrieving Cities for 139 of Set 3 | wanlaweyn
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wanlaweyn&units=imperial
    Retrieving Cities for 140 of Set 3 | hasaki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hasaki&units=imperial
    Retrieving Cities for 141 of Set 3 | cape town
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cape town&units=imperial
    Retrieving Cities for 142 of Set 3 | san miguel
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san miguel&units=imperial
    Retrieving Cities for 143 of Set 3 | kargil
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kargil&units=imperial
    Retrieving Cities for 144 of Set 3 | chinique
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chinique&units=imperial
    Retrieving Cities for 145 of Set 3 | puerto carreno
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto carreno&units=imperial
    Retrieving Cities for 146 of Set 3 | hervey bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hervey bay&units=imperial
    Retrieving Cities for 147 of Set 3 | samusu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=samusu&units=imperial
    Not Found!
    Retrieving Cities for 148 of Set 3 | olafsvik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=olafsvik&units=imperial
    Not Found!
    Retrieving Cities for 149 of Set 3 | kudahuvadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kudahuvadhoo&units=imperial
    Retrieving Cities for 150 of Set 4 | dingle
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dingle&units=imperial
    Retrieving Cities for 151 of Set 4 | bredasdorp
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bredasdorp&units=imperial
    Retrieving Cities for 152 of Set 4 | necochea
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=necochea&units=imperial
    Retrieving Cities for 153 of Set 4 | davila
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=davila&units=imperial
    Retrieving Cities for 154 of Set 4 | meadow lake
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=meadow lake&units=imperial
    Retrieving Cities for 155 of Set 4 | butaritari
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=butaritari&units=imperial
    Retrieving Cities for 156 of Set 4 | ravar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ravar&units=imperial
    Retrieving Cities for 157 of Set 4 | mount isa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mount isa&units=imperial
    Retrieving Cities for 158 of Set 4 | yining
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yining&units=imperial
    Retrieving Cities for 159 of Set 4 | barentsburg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barentsburg&units=imperial
    Not Found!
    Retrieving Cities for 160 of Set 4 | vanimo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vanimo&units=imperial
    Retrieving Cities for 161 of Set 4 | hobart
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hobart&units=imperial
    Retrieving Cities for 162 of Set 4 | cockburn town
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cockburn town&units=imperial
    Retrieving Cities for 163 of Set 4 | saint george
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint george&units=imperial
    Retrieving Cities for 164 of Set 4 | tarakan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tarakan&units=imperial
    Retrieving Cities for 165 of Set 4 | kaabong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kaabong&units=imperial
    Retrieving Cities for 166 of Set 4 | honningsvag
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=honningsvag&units=imperial
    Retrieving Cities for 167 of Set 4 | umm bab
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=umm bab&units=imperial
    Retrieving Cities for 168 of Set 4 | gimli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gimli&units=imperial
    Retrieving Cities for 169 of Set 4 | saint-georges
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint-georges&units=imperial
    Retrieving Cities for 170 of Set 4 | georgetown
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=georgetown&units=imperial
    Retrieving Cities for 171 of Set 4 | morehead
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=morehead&units=imperial
    Retrieving Cities for 172 of Set 4 | nong khai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nong khai&units=imperial
    Retrieving Cities for 173 of Set 4 | port hawkesbury
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port hawkesbury&units=imperial
    Retrieving Cities for 174 of Set 4 | alta floresta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=alta floresta&units=imperial
    Retrieving Cities for 175 of Set 4 | lazaro cardenas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lazaro cardenas&units=imperial
    Retrieving Cities for 176 of Set 4 | vila velha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vila velha&units=imperial
    Retrieving Cities for 177 of Set 4 | havelock
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=havelock&units=imperial
    Retrieving Cities for 178 of Set 4 | pisco
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pisco&units=imperial
    Retrieving Cities for 179 of Set 4 | micheweni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=micheweni&units=imperial
    Retrieving Cities for 180 of Set 4 | eskasem
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=eskasem&units=imperial
    Not Found!
    Retrieving Cities for 181 of Set 4 | tura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tura&units=imperial
    Retrieving Cities for 182 of Set 4 | camacha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=camacha&units=imperial
    Retrieving Cities for 183 of Set 4 | prince albert
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=prince albert&units=imperial
    Retrieving Cities for 184 of Set 4 | bethel
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bethel&units=imperial
    Retrieving Cities for 185 of Set 4 | makokou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=makokou&units=imperial
    Retrieving Cities for 186 of Set 4 | boralday
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boralday&units=imperial
    Retrieving Cities for 187 of Set 4 | kamenka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kamenka&units=imperial
    Retrieving Cities for 188 of Set 4 | louisbourg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=louisbourg&units=imperial
    Not Found!
    Retrieving Cities for 189 of Set 4 | fortuna foothills
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fortuna foothills&units=imperial
    Retrieving Cities for 190 of Set 4 | opuwo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=opuwo&units=imperial
    Retrieving Cities for 191 of Set 4 | clinton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=clinton&units=imperial
    Retrieving Cities for 192 of Set 4 | izhma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=izhma&units=imperial
    Retrieving Cities for 193 of Set 4 | karasburg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=karasburg&units=imperial
    Retrieving Cities for 194 of Set 4 | asau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=asau&units=imperial
    Not Found!
    Retrieving Cities for 195 of Set 4 | thompson
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=thompson&units=imperial
    Retrieving Cities for 196 of Set 4 | vila franca do campo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vila franca do campo&units=imperial
    Retrieving Cities for 197 of Set 4 | general roca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=general roca&units=imperial
    Retrieving Cities for 198 of Set 4 | inderborskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=inderborskiy&units=imperial
    Not Found!
    Retrieving Cities for 199 of Set 4 | castro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=castro&units=imperial
    Retrieving Cities for 200 of Set 5 | tiksi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tiksi&units=imperial
    Retrieving Cities for 201 of Set 5 | bonthe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bonthe&units=imperial
    Retrieving Cities for 202 of Set 5 | saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint-pierre&units=imperial
    Retrieving Cities for 203 of Set 5 | severo-kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=severo-kurilsk&units=imperial
    Retrieving Cities for 204 of Set 5 | iskateley
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=iskateley&units=imperial
    Retrieving Cities for 205 of Set 5 | grand gaube
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grand gaube&units=imperial
    Retrieving Cities for 206 of Set 5 | mackay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mackay&units=imperial
    Retrieving Cities for 207 of Set 5 | tougan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tougan&units=imperial
    Retrieving Cities for 208 of Set 5 | myre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=myre&units=imperial
    Retrieving Cities for 209 of Set 5 | straumen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=straumen&units=imperial
    Retrieving Cities for 210 of Set 5 | codrington
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=codrington&units=imperial
    Retrieving Cities for 211 of Set 5 | atuona
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=atuona&units=imperial
    Retrieving Cities for 212 of Set 5 | dickinson
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dickinson&units=imperial
    Retrieving Cities for 213 of Set 5 | coffs harbour
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coffs harbour&units=imperial
    Retrieving Cities for 214 of Set 5 | fukue
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fukue&units=imperial
    Retrieving Cities for 215 of Set 5 | lebu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lebu&units=imperial
    Retrieving Cities for 216 of Set 5 | seguela
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=seguela&units=imperial
    Retrieving Cities for 217 of Set 5 | kuldiga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kuldiga&units=imperial
    Retrieving Cities for 218 of Set 5 | richards bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=richards bay&units=imperial
    Retrieving Cities for 219 of Set 5 | kimbe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kimbe&units=imperial
    Retrieving Cities for 220 of Set 5 | longyearbyen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=longyearbyen&units=imperial
    Retrieving Cities for 221 of Set 5 | saint-philippe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint-philippe&units=imperial
    Retrieving Cities for 222 of Set 5 | airai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=airai&units=imperial
    Retrieving Cities for 223 of Set 5 | mareeba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mareeba&units=imperial
    Retrieving Cities for 224 of Set 5 | tumannyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tumannyy&units=imperial
    Not Found!
    Retrieving Cities for 225 of Set 5 | erzin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=erzin&units=imperial
    Retrieving Cities for 226 of Set 5 | byron bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=byron bay&units=imperial
    Retrieving Cities for 227 of Set 5 | yanan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yanan&units=imperial
    Not Found!
    Retrieving Cities for 228 of Set 5 | ponta do sol
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ponta do sol&units=imperial
    Retrieving Cities for 229 of Set 5 | pangnirtung
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pangnirtung&units=imperial
    Retrieving Cities for 230 of Set 5 | ahipara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ahipara&units=imperial
    Retrieving Cities for 231 of Set 5 | zhigansk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zhigansk&units=imperial
    Retrieving Cities for 232 of Set 5 | banda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=banda&units=imperial
    Retrieving Cities for 233 of Set 5 | agadir
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=agadir&units=imperial
    Retrieving Cities for 234 of Set 5 | rjukan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rjukan&units=imperial
    Retrieving Cities for 235 of Set 5 | galle
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=galle&units=imperial
    Retrieving Cities for 236 of Set 5 | saint-louis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint-louis&units=imperial
    Retrieving Cities for 237 of Set 5 | toliary
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=toliary&units=imperial
    Not Found!
    Retrieving Cities for 238 of Set 5 | puerto escondido
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto escondido&units=imperial
    Retrieving Cities for 239 of Set 5 | lobito
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lobito&units=imperial
    Retrieving Cities for 240 of Set 5 | bengkulu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bengkulu&units=imperial
    Not Found!
    Retrieving Cities for 241 of Set 5 | puqi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puqi&units=imperial
    Retrieving Cities for 242 of Set 5 | alice springs
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=alice springs&units=imperial
    Retrieving Cities for 243 of Set 5 | cairns
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cairns&units=imperial
    Retrieving Cities for 244 of Set 5 | iquique
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=iquique&units=imperial
    Retrieving Cities for 245 of Set 5 | iqaluit
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=iqaluit&units=imperial
    Retrieving Cities for 246 of Set 5 | altamira
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=altamira&units=imperial
    Retrieving Cities for 247 of Set 5 | new norfolk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=new norfolk&units=imperial
    Retrieving Cities for 248 of Set 5 | la ronge
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=la ronge&units=imperial
    Retrieving Cities for 249 of Set 5 | canmore
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=canmore&units=imperial
    Retrieving Cities for 250 of Set 6 | inhambane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=inhambane&units=imperial
    Retrieving Cities for 251 of Set 6 | mahebourg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mahebourg&units=imperial
    Retrieving Cities for 252 of Set 6 | kupang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kupang&units=imperial
    Retrieving Cities for 253 of Set 6 | nizhneyansk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nizhneyansk&units=imperial
    Not Found!
    Retrieving Cities for 254 of Set 6 | ostrogozhsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ostrogozhsk&units=imperial
    Retrieving Cities for 255 of Set 6 | fuyang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fuyang&units=imperial
    Retrieving Cities for 256 of Set 6 | paradwip
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=paradwip&units=imperial
    Not Found!
    Retrieving Cities for 257 of Set 6 | kaitangata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kaitangata&units=imperial
    Retrieving Cities for 258 of Set 6 | antigonish
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=antigonish&units=imperial
    Retrieving Cities for 259 of Set 6 | rochelle
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rochelle&units=imperial
    Retrieving Cities for 260 of Set 6 | tsihombe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tsihombe&units=imperial
    Not Found!
    Retrieving Cities for 261 of Set 6 | praya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=praya&units=imperial
    Retrieving Cities for 262 of Set 6 | kavieng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kavieng&units=imperial
    Retrieving Cities for 263 of Set 6 | tres lagoas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tres lagoas&units=imperial
    Not Found!
    Retrieving Cities for 264 of Set 6 | grand river south east
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grand river south east&units=imperial
    Not Found!
    Retrieving Cities for 265 of Set 6 | doctor pedro p. pena
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=doctor pedro p. pena&units=imperial
    Not Found!
    Retrieving Cities for 266 of Set 6 | palmas bellas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=palmas bellas&units=imperial
    Retrieving Cities for 267 of Set 6 | yumen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yumen&units=imperial
    Retrieving Cities for 268 of Set 6 | warqla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=warqla&units=imperial
    Not Found!
    Retrieving Cities for 269 of Set 6 | dong xoai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dong xoai&units=imperial
    Retrieving Cities for 270 of Set 6 | torbay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=torbay&units=imperial
    Retrieving Cities for 271 of Set 6 | pasighat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pasighat&units=imperial
    Retrieving Cities for 272 of Set 6 | nyurba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nyurba&units=imperial
    Retrieving Cities for 273 of Set 6 | cherskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cherskiy&units=imperial
    Retrieving Cities for 274 of Set 6 | mutsamudu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mutsamudu&units=imperial
    Not Found!
    Retrieving Cities for 275 of Set 6 | provideniya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=provideniya&units=imperial
    Retrieving Cities for 276 of Set 6 | simao
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=simao&units=imperial
    Retrieving Cities for 277 of Set 6 | saldanha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saldanha&units=imperial
    Retrieving Cities for 278 of Set 6 | wagar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wagar&units=imperial
    Retrieving Cities for 279 of Set 6 | dakoro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dakoro&units=imperial
    Retrieving Cities for 280 of Set 6 | safranbolu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=safranbolu&units=imperial
    Retrieving Cities for 281 of Set 6 | marabba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marabba&units=imperial
    Retrieving Cities for 282 of Set 6 | requena
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=requena&units=imperial
    Retrieving Cities for 283 of Set 6 | normandin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=normandin&units=imperial
    Retrieving Cities for 284 of Set 6 | merke
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=merke&units=imperial
    Retrieving Cities for 285 of Set 6 | saint-joseph
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint-joseph&units=imperial
    Retrieving Cities for 286 of Set 6 | sinkat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sinkat&units=imperial
    Not Found!
    Retrieving Cities for 287 of Set 6 | moerai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=moerai&units=imperial
    Retrieving Cities for 288 of Set 6 | henties bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=henties bay&units=imperial
    Retrieving Cities for 289 of Set 6 | ostrovnoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ostrovnoy&units=imperial
    Retrieving Cities for 290 of Set 6 | yangambi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yangambi&units=imperial
    Retrieving Cities for 291 of Set 6 | benjamin constant
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=benjamin constant&units=imperial
    Retrieving Cities for 292 of Set 6 | pinawa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pinawa&units=imperial
    Retrieving Cities for 293 of Set 6 | clyde river
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=clyde river&units=imperial
    Retrieving Cities for 294 of Set 6 | juba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=juba&units=imperial
    Retrieving Cities for 295 of Set 6 | tshela
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tshela&units=imperial
    Retrieving Cities for 296 of Set 6 | hamilton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hamilton&units=imperial
    Retrieving Cities for 297 of Set 6 | novobureyskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=novobureyskiy&units=imperial
    Retrieving Cities for 298 of Set 6 | nagai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nagai&units=imperial
    Retrieving Cities for 299 of Set 6 | sitka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sitka&units=imperial
    Retrieving Cities for 300 of Set 7 | tabiauea
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tabiauea&units=imperial
    Not Found!
    Retrieving Cities for 301 of Set 7 | sungaipenuh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sungaipenuh&units=imperial
    Retrieving Cities for 302 of Set 7 | kamuli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kamuli&units=imperial
    Retrieving Cities for 303 of Set 7 | adrar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=adrar&units=imperial
    Retrieving Cities for 304 of Set 7 | wahran
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wahran&units=imperial
    Not Found!
    Retrieving Cities for 305 of Set 7 | tual
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tual&units=imperial
    Retrieving Cities for 306 of Set 7 | puerto suarez
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto suarez&units=imperial
    Retrieving Cities for 307 of Set 7 | chicama
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chicama&units=imperial
    Retrieving Cities for 308 of Set 7 | mana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mana&units=imperial
    Retrieving Cities for 309 of Set 7 | praia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=praia&units=imperial
    Retrieving Cities for 310 of Set 7 | primo tapia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=primo tapia&units=imperial
    Retrieving Cities for 311 of Set 7 | cukai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cukai&units=imperial
    Retrieving Cities for 312 of Set 7 | tabulbah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tabulbah&units=imperial
    Not Found!
    Retrieving Cities for 313 of Set 7 | seymchan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=seymchan&units=imperial
    Retrieving Cities for 314 of Set 7 | amderma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amderma&units=imperial
    Not Found!
    Retrieving Cities for 315 of Set 7 | katsuura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=katsuura&units=imperial
    Retrieving Cities for 316 of Set 7 | porbandar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=porbandar&units=imperial
    Retrieving Cities for 317 of Set 7 | barreirinha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barreirinha&units=imperial
    Retrieving Cities for 318 of Set 7 | chagda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chagda&units=imperial
    Not Found!
    Retrieving Cities for 319 of Set 7 | kibala
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kibala&units=imperial
    Retrieving Cities for 320 of Set 7 | belmonte
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=belmonte&units=imperial
    Retrieving Cities for 321 of Set 7 | corowa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=corowa&units=imperial
    Retrieving Cities for 322 of Set 7 | bubaque
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bubaque&units=imperial
    Retrieving Cities for 323 of Set 7 | boa vista
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boa vista&units=imperial
    Retrieving Cities for 324 of Set 7 | varias
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=varias&units=imperial
    Retrieving Cities for 325 of Set 7 | marzuq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marzuq&units=imperial
    Retrieving Cities for 326 of Set 7 | tautira
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tautira&units=imperial
    Retrieving Cities for 327 of Set 7 | isaka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=isaka&units=imperial
    Retrieving Cities for 328 of Set 7 | yorosso
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yorosso&units=imperial
    Retrieving Cities for 329 of Set 7 | geraldton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=geraldton&units=imperial
    Retrieving Cities for 330 of Set 7 | kavaratti
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kavaratti&units=imperial
    Retrieving Cities for 331 of Set 7 | sur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sur&units=imperial
    Retrieving Cities for 332 of Set 7 | iranshahr
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=iranshahr&units=imperial
    Retrieving Cities for 333 of Set 7 | ambilobe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ambilobe&units=imperial
    Retrieving Cities for 334 of Set 7 | brody
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=brody&units=imperial
    Retrieving Cities for 335 of Set 7 | southbridge
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=southbridge&units=imperial
    Retrieving Cities for 336 of Set 7 | nuuk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nuuk&units=imperial
    Retrieving Cities for 337 of Set 7 | loikaw
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=loikaw&units=imperial
    Retrieving Cities for 338 of Set 7 | mehamn
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mehamn&units=imperial
    Retrieving Cities for 339 of Set 7 | malwan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malwan&units=imperial
    Not Found!
    Retrieving Cities for 340 of Set 7 | olinda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=olinda&units=imperial
    Retrieving Cities for 341 of Set 7 | namibe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=namibe&units=imperial
    Retrieving Cities for 342 of Set 7 | bathsheba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bathsheba&units=imperial
    Retrieving Cities for 343 of Set 7 | rio branco
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rio branco&units=imperial
    Retrieving Cities for 344 of Set 7 | colares
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=colares&units=imperial
    Retrieving Cities for 345 of Set 7 | manggar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manggar&units=imperial
    Retrieving Cities for 346 of Set 7 | shaunavon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shaunavon&units=imperial
    Retrieving Cities for 347 of Set 7 | college
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=college&units=imperial
    Retrieving Cities for 348 of Set 7 | antofagasta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=antofagasta&units=imperial
    Retrieving Cities for 349 of Set 7 | amilly
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amilly&units=imperial
    Retrieving Cities for 350 of Set 8 | kati
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kati&units=imperial
    Retrieving Cities for 351 of Set 8 | rochegda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rochegda&units=imperial
    Retrieving Cities for 352 of Set 8 | maturin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=maturin&units=imperial
    Retrieving Cities for 353 of Set 8 | mikkeli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mikkeli&units=imperial
    Retrieving Cities for 354 of Set 8 | urucara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=urucara&units=imperial
    Retrieving Cities for 355 of Set 8 | zachagansk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zachagansk&units=imperial
    Not Found!
    Retrieving Cities for 356 of Set 8 | lake charles
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lake charles&units=imperial
    Retrieving Cities for 357 of Set 8 | topchikha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=topchikha&units=imperial
    Retrieving Cities for 358 of Set 8 | ancud
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ancud&units=imperial
    Retrieving Cities for 359 of Set 8 | champerico
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=champerico&units=imperial
    Retrieving Cities for 360 of Set 8 | portland
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=portland&units=imperial
    Retrieving Cities for 361 of Set 8 | malakal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malakal&units=imperial
    Not Found!
    Retrieving Cities for 362 of Set 8 | meyungs
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=meyungs&units=imperial
    Not Found!
    Retrieving Cities for 363 of Set 8 | hofn
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hofn&units=imperial
    Retrieving Cities for 364 of Set 8 | paamiut
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=paamiut&units=imperial
    Retrieving Cities for 365 of Set 8 | kurilsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kurilsk&units=imperial
    Retrieving Cities for 366 of Set 8 | riyadh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=riyadh&units=imperial
    Retrieving Cities for 367 of Set 8 | dicabisagan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dicabisagan&units=imperial
    Retrieving Cities for 368 of Set 8 | tuktoyaktuk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tuktoyaktuk&units=imperial
    Retrieving Cities for 369 of Set 8 | seymour
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=seymour&units=imperial
    Retrieving Cities for 370 of Set 8 | viedma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=viedma&units=imperial
    Retrieving Cities for 371 of Set 8 | rungata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rungata&units=imperial
    Not Found!
    Retrieving Cities for 372 of Set 8 | the valley
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=the valley&units=imperial
    Retrieving Cities for 373 of Set 8 | pata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pata&units=imperial
    Retrieving Cities for 374 of Set 8 | seoul
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=seoul&units=imperial
    Retrieving Cities for 375 of Set 8 | darhan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=darhan&units=imperial
    Retrieving Cities for 376 of Set 8 | marienburg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marienburg&units=imperial
    Retrieving Cities for 377 of Set 8 | juneau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=juneau&units=imperial
    Retrieving Cities for 378 of Set 8 | forest acres
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=forest acres&units=imperial
    Retrieving Cities for 379 of Set 8 | taltal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=taltal&units=imperial
    Retrieving Cities for 380 of Set 8 | high level
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=high level&units=imperial
    Retrieving Cities for 381 of Set 8 | lac du bonnet
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lac du bonnet&units=imperial
    Retrieving Cities for 382 of Set 8 | lyuban
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lyuban&units=imperial
    Retrieving Cities for 383 of Set 8 | tazovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tazovskiy&units=imperial
    Retrieving Cities for 384 of Set 8 | denpasar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=denpasar&units=imperial
    Retrieving Cities for 385 of Set 8 | tuzha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tuzha&units=imperial
    Retrieving Cities for 386 of Set 8 | temiscaming
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=temiscaming&units=imperial
    Retrieving Cities for 387 of Set 8 | vyazma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vyazma&units=imperial
    Retrieving Cities for 388 of Set 8 | sabathu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sabathu&units=imperial
    Retrieving Cities for 389 of Set 8 | fairbanks
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fairbanks&units=imperial
    Retrieving Cities for 390 of Set 8 | goba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=goba&units=imperial
    Retrieving Cities for 391 of Set 8 | conakry
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=conakry&units=imperial
    Retrieving Cities for 392 of Set 8 | shu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shu&units=imperial
    Retrieving Cities for 393 of Set 8 | norrtalje
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=norrtalje&units=imperial
    Retrieving Cities for 394 of Set 8 | flinders
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=flinders&units=imperial
    Retrieving Cities for 395 of Set 8 | zeya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zeya&units=imperial
    Retrieving Cities for 396 of Set 8 | jahrom
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jahrom&units=imperial
    Not Found!
    Retrieving Cities for 397 of Set 8 | ngaoundere
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ngaoundere&units=imperial
    Retrieving Cities for 398 of Set 8 | pacific grove
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pacific grove&units=imperial
    Retrieving Cities for 399 of Set 8 | matara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=matara&units=imperial
    Retrieving Cities for 400 of Set 9 | andujar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=andujar&units=imperial
    Retrieving Cities for 401 of Set 9 | husavik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=husavik&units=imperial
    Retrieving Cities for 402 of Set 9 | waipawa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=waipawa&units=imperial
    Retrieving Cities for 403 of Set 9 | mecca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mecca&units=imperial
    Retrieving Cities for 404 of Set 9 | vyazemskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vyazemskiy&units=imperial
    Retrieving Cities for 405 of Set 9 | xinyang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=xinyang&units=imperial
    Retrieving Cities for 406 of Set 9 | bima
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bima&units=imperial
    Retrieving Cities for 407 of Set 9 | berlevag
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=berlevag&units=imperial
    Retrieving Cities for 408 of Set 9 | rehoboth
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rehoboth&units=imperial
    Retrieving Cities for 409 of Set 9 | angermunde
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=angermunde&units=imperial
    Retrieving Cities for 410 of Set 9 | yalchiki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yalchiki&units=imperial
    Not Found!
    Retrieving Cities for 411 of Set 9 | altay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=altay&units=imperial
    Retrieving Cities for 412 of Set 9 | dire dawa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dire dawa&units=imperial
    Retrieving Cities for 413 of Set 9 | qianan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=qianan&units=imperial
    Retrieving Cities for 414 of Set 9 | ilulissat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ilulissat&units=imperial
    Retrieving Cities for 415 of Set 9 | port moresby
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port moresby&units=imperial
    Retrieving Cities for 416 of Set 9 | malinyi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malinyi&units=imperial
    Retrieving Cities for 417 of Set 9 | nguiu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nguiu&units=imperial
    Not Found!
    Retrieving Cities for 418 of Set 9 | east london
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=east london&units=imperial
    Retrieving Cities for 419 of Set 9 | katangli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=katangli&units=imperial
    Retrieving Cities for 420 of Set 9 | kyzyl-suu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kyzyl-suu&units=imperial
    Retrieving Cities for 421 of Set 9 | mattru
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mattru&units=imperial
    Retrieving Cities for 422 of Set 9 | kangasala
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kangasala&units=imperial
    Retrieving Cities for 423 of Set 9 | gaoua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gaoua&units=imperial
    Retrieving Cities for 424 of Set 9 | cayenne
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cayenne&units=imperial
    Retrieving Cities for 425 of Set 9 | dembi dolo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dembi dolo&units=imperial
    Retrieving Cities for 426 of Set 9 | lazo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lazo&units=imperial
    Retrieving Cities for 427 of Set 9 | orangeburg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=orangeburg&units=imperial
    Retrieving Cities for 428 of Set 9 | samana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=samana&units=imperial
    Retrieving Cities for 429 of Set 9 | harlingen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=harlingen&units=imperial
    Retrieving Cities for 430 of Set 9 | luderitz
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=luderitz&units=imperial
    Retrieving Cities for 431 of Set 9 | broome
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=broome&units=imperial
    Retrieving Cities for 432 of Set 9 | tonantins
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tonantins&units=imperial
    Retrieving Cities for 433 of Set 9 | rengo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rengo&units=imperial
    Retrieving Cities for 434 of Set 9 | penzance
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=penzance&units=imperial
    Retrieving Cities for 435 of Set 9 | mar del plata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mar del plata&units=imperial
    Retrieving Cities for 436 of Set 9 | tunxi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tunxi&units=imperial
    Not Found!
    Retrieving Cities for 437 of Set 9 | los llanos de aridane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=los llanos de aridane&units=imperial
    Retrieving Cities for 438 of Set 9 | sapucaia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sapucaia&units=imperial
    Retrieving Cities for 439 of Set 9 | linhares
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=linhares&units=imperial
    Retrieving Cities for 440 of Set 9 | severodvinsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=severodvinsk&units=imperial
    Retrieving Cities for 441 of Set 9 | riga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=riga&units=imperial
    Retrieving Cities for 442 of Set 9 | mitsamiouli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mitsamiouli&units=imperial
    Retrieving Cities for 443 of Set 9 | kusk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kusk&units=imperial
    Not Found!
    Retrieving Cities for 444 of Set 9 | sept-iles
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sept-iles&units=imperial
    Retrieving Cities for 445 of Set 9 | kaz
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kaz&units=imperial
    Retrieving Cities for 446 of Set 9 | san cristobal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san cristobal&units=imperial
    Retrieving Cities for 447 of Set 9 | gayny
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gayny&units=imperial
    Retrieving Cities for 448 of Set 9 | batagay-alyta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=batagay-alyta&units=imperial
    Retrieving Cities for 449 of Set 9 | jalu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jalu&units=imperial
    Retrieving Cities for 450 of Set 10 | cagayan de tawi-tawi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cagayan de tawi-tawi&units=imperial
    Not Found!
    Retrieving Cities for 451 of Set 10 | merauke
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=merauke&units=imperial
    Retrieving Cities for 452 of Set 10 | uwayl
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=uwayl&units=imperial
    Not Found!
    Retrieving Cities for 453 of Set 10 | arlit
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arlit&units=imperial
    Retrieving Cities for 454 of Set 10 | uthal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=uthal&units=imperial
    Retrieving Cities for 455 of Set 10 | mamaku
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mamaku&units=imperial
    Retrieving Cities for 456 of Set 10 | collie
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=collie&units=imperial
    Retrieving Cities for 457 of Set 10 | mitu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mitu&units=imperial
    Retrieving Cities for 458 of Set 10 | pandamatenga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pandamatenga&units=imperial
    Retrieving Cities for 459 of Set 10 | mirnyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mirnyy&units=imperial
    Retrieving Cities for 460 of Set 10 | beitbridge
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=beitbridge&units=imperial
    Retrieving Cities for 461 of Set 10 | yakshur-bodya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yakshur-bodya&units=imperial
    Not Found!
    Retrieving Cities for 462 of Set 10 | chateaubelair
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chateaubelair&units=imperial
    Retrieving Cities for 463 of Set 10 | coos bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coos bay&units=imperial
    Retrieving Cities for 464 of Set 10 | townsville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=townsville&units=imperial
    Retrieving Cities for 465 of Set 10 | muisne
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=muisne&units=imperial
    Retrieving Cities for 466 of Set 10 | uribia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=uribia&units=imperial
    Retrieving Cities for 467 of Set 10 | indi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=indi&units=imperial
    Retrieving Cities for 468 of Set 10 | pangai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pangai&units=imperial
    Retrieving Cities for 469 of Set 10 | vangaindrano
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vangaindrano&units=imperial
    Retrieving Cities for 470 of Set 10 | port hardy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port hardy&units=imperial
    Retrieving Cities for 471 of Set 10 | sarkand
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sarkand&units=imperial
    Retrieving Cities for 472 of Set 10 | labutta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=labutta&units=imperial
    Not Found!
    Retrieving Cities for 473 of Set 10 | vikulovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vikulovo&units=imperial
    Retrieving Cities for 474 of Set 10 | nokaneng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nokaneng&units=imperial
    Retrieving Cities for 475 of Set 10 | aracati
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aracati&units=imperial
    Retrieving Cities for 476 of Set 10 | matadi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=matadi&units=imperial
    Retrieving Cities for 477 of Set 10 | kalmunai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kalmunai&units=imperial
    Retrieving Cities for 478 of Set 10 | khislavichi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=khislavichi&units=imperial
    Retrieving Cities for 479 of Set 10 | saurimo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saurimo&units=imperial
    Retrieving Cities for 480 of Set 10 | boddam
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boddam&units=imperial
    Retrieving Cities for 481 of Set 10 | teya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=teya&units=imperial
    Retrieving Cities for 482 of Set 10 | jibuti
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jibuti&units=imperial
    Not Found!
    Retrieving Cities for 483 of Set 10 | basco
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=basco&units=imperial
    Retrieving Cities for 484 of Set 10 | mangai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mangai&units=imperial
    Retrieving Cities for 485 of Set 10 | auki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=auki&units=imperial
    Retrieving Cities for 486 of Set 10 | accra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=accra&units=imperial
    Retrieving Cities for 487 of Set 10 | tateyama
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tateyama&units=imperial
    Retrieving Cities for 488 of Set 10 | owensboro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=owensboro&units=imperial
    Retrieving Cities for 489 of Set 10 | west bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=west bay&units=imperial
    Retrieving Cities for 490 of Set 10 | babanusah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=babanusah&units=imperial
    Not Found!
    Retrieving Cities for 491 of Set 10 | sorland
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sorland&units=imperial
    Retrieving Cities for 492 of Set 10 | san patricio
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san patricio&units=imperial
    Retrieving Cities for 493 of Set 10 | santa eulalia del rio
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa eulalia del rio&units=imperial
    Not Found!
    Retrieving Cities for 494 of Set 10 | rosarito
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rosarito&units=imperial
    Retrieving Cities for 495 of Set 10 | zelenoborsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zelenoborsk&units=imperial
    Retrieving Cities for 496 of Set 10 | tawkar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tawkar&units=imperial
    Not Found!
    Retrieving Cities for 497 of Set 10 | san andres
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san andres&units=imperial
    Retrieving Cities for 498 of Set 10 | tarko-sale
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tarko-sale&units=imperial
    Retrieving Cities for 499 of Set 10 | half moon bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=half moon bay&units=imperial
    Retrieving Cities for 500 of Set 11 | kampong chhnang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kampong chhnang&units=imperial
    Retrieving Cities for 501 of Set 11 | shchelyayur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shchelyayur&units=imperial
    Not Found!
    Retrieving Cities for 502 of Set 11 | zaraza
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zaraza&units=imperial
    Retrieving Cities for 503 of Set 11 | mangrol
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mangrol&units=imperial
    Retrieving Cities for 504 of Set 11 | powell
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=powell&units=imperial
    Retrieving Cities for 505 of Set 11 | zykovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zykovo&units=imperial
    Retrieving Cities for 506 of Set 11 | matamoros
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=matamoros&units=imperial
    Retrieving Cities for 507 of Set 11 | tambopata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tambopata&units=imperial
    Not Found!
    Retrieving Cities for 508 of Set 11 | daru
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=daru&units=imperial
    Retrieving Cities for 509 of Set 11 | prudentopolis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=prudentopolis&units=imperial
    Retrieving Cities for 510 of Set 11 | srednekolymsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=srednekolymsk&units=imperial
    Retrieving Cities for 511 of Set 11 | gunjur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gunjur&units=imperial
    Retrieving Cities for 512 of Set 11 | beaupre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=beaupre&units=imperial
    Retrieving Cities for 513 of Set 11 | burkhala
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=burkhala&units=imperial
    Not Found!
    Retrieving Cities for 514 of Set 11 | kanigoro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kanigoro&units=imperial
    Retrieving Cities for 515 of Set 11 | turukhansk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=turukhansk&units=imperial
    Retrieving Cities for 516 of Set 11 | doembang nangbuat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=doembang nangbuat&units=imperial
    Retrieving Cities for 517 of Set 11 | saleaula
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saleaula&units=imperial
    Not Found!
    Retrieving Cities for 518 of Set 11 | kenora
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kenora&units=imperial
    Retrieving Cities for 519 of Set 11 | krasnoarmeysk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=krasnoarmeysk&units=imperial
    Retrieving Cities for 520 of Set 11 | krasnoselkup
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=krasnoselkup&units=imperial
    Not Found!
    Retrieving Cities for 521 of Set 11 | touros
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=touros&units=imperial
    Retrieving Cities for 522 of Set 11 | novyy urengoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=novyy urengoy&units=imperial
    Retrieving Cities for 523 of Set 11 | utinga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=utinga&units=imperial
    Retrieving Cities for 524 of Set 11 | ola
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ola&units=imperial
    Retrieving Cities for 525 of Set 11 | karratha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=karratha&units=imperial
    Retrieving Cities for 526 of Set 11 | orlik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=orlik&units=imperial
    Retrieving Cities for 527 of Set 11 | phan thiet
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=phan thiet&units=imperial
    Retrieving Cities for 528 of Set 11 | ulladulla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ulladulla&units=imperial
    Retrieving Cities for 529 of Set 11 | timbangan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=timbangan&units=imperial
    Not Found!
    Retrieving Cities for 530 of Set 11 | quzhou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=quzhou&units=imperial
    Retrieving Cities for 531 of Set 11 | mayumba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mayumba&units=imperial
    Retrieving Cities for 532 of Set 11 | oranjemund
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oranjemund&units=imperial
    Retrieving Cities for 533 of Set 11 | bam
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bam&units=imperial
    Retrieving Cities for 534 of Set 11 | naze
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=naze&units=imperial
    Retrieving Cities for 535 of Set 11 | sisimiut
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sisimiut&units=imperial
    Retrieving Cities for 536 of Set 11 | pingliang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pingliang&units=imperial
    Retrieving Cities for 537 of Set 11 | beyneu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=beyneu&units=imperial
    Retrieving Cities for 538 of Set 11 | rurrenabaque
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rurrenabaque&units=imperial
    Retrieving Cities for 539 of Set 11 | ambon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ambon&units=imperial
    Retrieving Cities for 540 of Set 11 | akhmeta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=akhmeta&units=imperial
    Retrieving Cities for 541 of Set 11 | massakory
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=massakory&units=imperial
    Retrieving Cities for 542 of Set 11 | vao
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vao&units=imperial
    Retrieving Cities for 543 of Set 11 | simpang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=simpang&units=imperial
    Retrieving Cities for 544 of Set 11 | northam
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=northam&units=imperial
    Retrieving Cities for 545 of Set 11 | nador
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nador&units=imperial
    Retrieving Cities for 546 of Set 11 | greenville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=greenville&units=imperial
    Retrieving Cities for 547 of Set 11 | barcelos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barcelos&units=imperial
    Retrieving Cities for 548 of Set 11 | murray bridge
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=murray bridge&units=imperial
    Retrieving Cities for 549 of Set 11 | sakata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sakata&units=imperial
    Retrieving Cities for 550 of Set 12 | petauke
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=petauke&units=imperial
    Retrieving Cities for 551 of Set 12 | oum hadjer
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oum hadjer&units=imperial
    Retrieving Cities for 552 of Set 12 | krymsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=krymsk&units=imperial
    Retrieving Cities for 553 of Set 12 | mandalgovi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mandalgovi&units=imperial
    Retrieving Cities for 554 of Set 12 | atar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=atar&units=imperial
    Retrieving Cities for 555 of Set 12 | ye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ye&units=imperial
    Not Found!
    Retrieving Cities for 556 of Set 12 | la rosa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=la rosa&units=imperial
    Retrieving Cities for 557 of Set 12 | sioux lookout
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sioux lookout&units=imperial
    Retrieving Cities for 558 of Set 12 | banda aceh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=banda aceh&units=imperial
    Retrieving Cities for 559 of Set 12 | am timan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=am timan&units=imperial
    Retrieving Cities for 560 of Set 12 | grajau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grajau&units=imperial
    Not Found!
    Retrieving Cities for 561 of Set 12 | lasa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lasa&units=imperial
    Retrieving Cities for 562 of Set 12 | cortez
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cortez&units=imperial
    Retrieving Cities for 563 of Set 12 | sharjah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sharjah&units=imperial
    Retrieving Cities for 564 of Set 12 | abu samrah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abu samrah&units=imperial
    Retrieving Cities for 565 of Set 12 | darab
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=darab&units=imperial
    Retrieving Cities for 566 of Set 12 | ust-kut
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-kut&units=imperial
    Retrieving Cities for 567 of Set 12 | sechura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sechura&units=imperial
    Retrieving Cities for 568 of Set 12 | daoukro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=daoukro&units=imperial
    Retrieving Cities for 569 of Set 12 | nalut
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nalut&units=imperial
    Retrieving Cities for 570 of Set 12 | rosario
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rosario&units=imperial
    Retrieving Cities for 571 of Set 12 | hami
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hami&units=imperial
    Retrieving Cities for 572 of Set 12 | halifax
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=halifax&units=imperial
    Retrieving Cities for 573 of Set 12 | vanavara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vanavara&units=imperial
    Retrieving Cities for 574 of Set 12 | coihaique
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coihaique&units=imperial
    Retrieving Cities for 575 of Set 12 | mujiayingzi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mujiayingzi&units=imperial
    Retrieving Cities for 576 of Set 12 | cochrane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cochrane&units=imperial
    Not Found!
    Retrieving Cities for 577 of Set 12 | magistralnyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=magistralnyy&units=imperial
    Retrieving Cities for 578 of Set 12 | lolua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lolua&units=imperial
    Not Found!
    Retrieving Cities for 579 of Set 12 | pleshanovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pleshanovo&units=imperial
    Retrieving Cities for 580 of Set 12 | camana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=camana&units=imperial
    Not Found!
    Retrieving Cities for 581 of Set 12 | knin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=knin&units=imperial
    Retrieving Cities for 582 of Set 12 | hualmay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hualmay&units=imperial
    Retrieving Cities for 583 of Set 12 | okhotsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=okhotsk&units=imperial
    Retrieving Cities for 584 of Set 12 | azimur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=azimur&units=imperial
    Not Found!
    Retrieving Cities for 585 of Set 12 | alofi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=alofi&units=imperial
    Retrieving Cities for 586 of Set 12 | nishihara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nishihara&units=imperial
    Retrieving Cities for 587 of Set 12 | tongren
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tongren&units=imperial
    Retrieving Cities for 588 of Set 12 | poum
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=poum&units=imperial
    Retrieving Cities for 589 of Set 12 | sao felix do xingu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sao felix do xingu&units=imperial
    Retrieving Cities for 590 of Set 12 | ekibastuz
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ekibastuz&units=imperial
    Retrieving Cities for 591 of Set 12 | itaocara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=itaocara&units=imperial
    Retrieving Cities for 592 of Set 12 | marawi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marawi&units=imperial
    Retrieving Cities for 593 of Set 12 | najran
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=najran&units=imperial
    Retrieving Cities for 594 of Set 12 | surt
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=surt&units=imperial
    Retrieving Cities for 595 of Set 12 | columbus
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=columbus&units=imperial
    Retrieving Cities for 596 of Set 12 | taoudenni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=taoudenni&units=imperial
    Retrieving Cities for 597 of Set 12 | narasannapeta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=narasannapeta&units=imperial
    Retrieving Cities for 598 of Set 12 | qitaihe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=qitaihe&units=imperial
    Retrieving Cities for 599 of Set 12 | baghmara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=baghmara&units=imperial
    Retrieving Cities for 600 of Set 13 | quang ngai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=quang ngai&units=imperial
    Retrieving Cities for 601 of Set 13 | peniche
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=peniche&units=imperial
    Retrieving Cities for 602 of Set 13 | kutum
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kutum&units=imperial
    Retrieving Cities for 603 of Set 13 | kununurra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kununurra&units=imperial
    Retrieving Cities for 604 of Set 13 | kadungora
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kadungora&units=imperial
    Retrieving Cities for 605 of Set 13 | vagur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vagur&units=imperial
    Retrieving Cities for 606 of Set 13 | korla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=korla&units=imperial
    Not Found!
    Retrieving Cities for 607 of Set 13 | mayskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mayskiy&units=imperial
    Retrieving Cities for 608 of Set 13 | tucupita
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tucupita&units=imperial
    Retrieving Cities for 609 of Set 13 | pokrovsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pokrovsk&units=imperial
    Retrieving Cities for 610 of Set 13 | helong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=helong&units=imperial
    Retrieving Cities for 611 of Set 13 | nichinan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nichinan&units=imperial
    Retrieving Cities for 612 of Set 13 | lompoc
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lompoc&units=imperial
    Retrieving Cities for 613 of Set 13 | savonlinna
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=savonlinna&units=imperial
    Retrieving Cities for 614 of Set 13 | lata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lata&units=imperial
    Retrieving Cities for 615 of Set 13 | teguldet
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=teguldet&units=imperial
    Retrieving Cities for 616 of Set 13 | ahtopol
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ahtopol&units=imperial
    Retrieving Cities for 617 of Set 13 | chebsara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chebsara&units=imperial
    Retrieving Cities for 618 of Set 13 | mayor pablo lagerenza
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mayor pablo lagerenza&units=imperial
    Retrieving Cities for 619 of Set 13 | fulton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fulton&units=imperial
    Retrieving Cities for 620 of Set 13 | khash
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=khash&units=imperial
    Retrieving Cities for 621 of Set 13 | ust-kamchatsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-kamchatsk&units=imperial
    Not Found!
    Retrieving Cities for 622 of Set 13 | conde
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=conde&units=imperial
    Retrieving Cities for 623 of Set 13 | manta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manta&units=imperial
    Retrieving Cities for 624 of Set 13 | havre-saint-pierre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=havre-saint-pierre&units=imperial
    Retrieving Cities for 625 of Set 13 | barbar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barbar&units=imperial
    Not Found!
    Retrieving Cities for 626 of Set 13 | kobayashi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kobayashi&units=imperial
    Retrieving Cities for 627 of Set 13 | hay river
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hay river&units=imperial
    Retrieving Cities for 628 of Set 13 | novobirilyussy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=novobirilyussy&units=imperial
    Retrieving Cities for 629 of Set 13 | pringsewu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pringsewu&units=imperial
    Retrieving Cities for 630 of Set 13 | abu kamal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abu kamal&units=imperial
    Retrieving Cities for 631 of Set 13 | vengerovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vengerovo&units=imperial
    Retrieving Cities for 632 of Set 13 | victor harbor
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=victor harbor&units=imperial
    Retrieving Cities for 633 of Set 13 | kirakira
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kirakira&units=imperial
    Retrieving Cities for 634 of Set 13 | dong hoi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dong hoi&units=imperial
    Retrieving Cities for 635 of Set 13 | sibu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sibu&units=imperial
    Retrieving Cities for 636 of Set 13 | kiama
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kiama&units=imperial
    Retrieving Cities for 637 of Set 13 | samalaeulu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=samalaeulu&units=imperial
    Not Found!
    Retrieving Cities for 638 of Set 13 | hays
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hays&units=imperial
    Retrieving Cities for 639 of Set 13 | sakakah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sakakah&units=imperial
    Not Found!
    Retrieving Cities for 640 of Set 13 | tecpan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tecpan&units=imperial
    Retrieving Cities for 641 of Set 13 | santa rosa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa rosa&units=imperial
    Retrieving Cities for 642 of Set 13 | wenchi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wenchi&units=imperial
    Retrieving Cities for 643 of Set 13 | santa cruz de la palma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa cruz de la palma&units=imperial
    Retrieving Cities for 644 of Set 13 | suicheng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=suicheng&units=imperial
    Retrieving Cities for 645 of Set 13 | labuhan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=labuhan&units=imperial
    Retrieving Cities for 646 of Set 13 | urdzhar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=urdzhar&units=imperial
    Not Found!
    Retrieving Cities for 647 of Set 13 | dandong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dandong&units=imperial
    Retrieving Cities for 648 of Set 13 | buriti alegre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=buriti alegre&units=imperial
    Retrieving Cities for 649 of Set 13 | mapiripan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mapiripan&units=imperial
    Retrieving Cities for 650 of Set 14 | kandrian
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kandrian&units=imperial
    Retrieving Cities for 651 of Set 14 | kingsville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kingsville&units=imperial
    Retrieving Cities for 652 of Set 14 | trencin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=trencin&units=imperial
    Retrieving Cities for 653 of Set 14 | tarancon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tarancon&units=imperial
    Retrieving Cities for 654 of Set 14 | athens
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=athens&units=imperial
    Retrieving Cities for 655 of Set 14 | zheleznodorozhnyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zheleznodorozhnyy&units=imperial
    Retrieving Cities for 656 of Set 14 | yamada
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yamada&units=imperial
    Retrieving Cities for 657 of Set 14 | san carlos de bariloche
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san carlos de bariloche&units=imperial
    Retrieving Cities for 658 of Set 14 | masjed-e soleyman
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=masjed-e soleyman&units=imperial
    Not Found!
    Retrieving Cities for 659 of Set 14 | santa cruz de rosales
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa cruz de rosales&units=imperial
    Not Found!
    Retrieving Cities for 660 of Set 14 | baruun-urt
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=baruun-urt&units=imperial
    Retrieving Cities for 661 of Set 14 | arua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arua&units=imperial
    Retrieving Cities for 662 of Set 14 | ambodifototra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ambodifototra&units=imperial
    Not Found!
    Retrieving Cities for 663 of Set 14 | aklavik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aklavik&units=imperial
    Retrieving Cities for 664 of Set 14 | hobyo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hobyo&units=imperial
    Retrieving Cities for 665 of Set 14 | kalabo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kalabo&units=imperial
    Retrieving Cities for 666 of Set 14 | maragogi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=maragogi&units=imperial
    Retrieving Cities for 667 of Set 14 | dmytrivka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dmytrivka&units=imperial
    Retrieving Cities for 668 of Set 14 | yinchuan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yinchuan&units=imperial
    Retrieving Cities for 669 of Set 14 | katherine
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=katherine&units=imperial
    Retrieving Cities for 670 of Set 14 | kuala terengganu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kuala terengganu&units=imperial
    Retrieving Cities for 671 of Set 14 | pozo colorado
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pozo colorado&units=imperial
    Retrieving Cities for 672 of Set 14 | oskemen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oskemen&units=imperial
    Retrieving Cities for 673 of Set 14 | yarmouth
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yarmouth&units=imperial
    Retrieving Cities for 674 of Set 14 | tiznit
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tiznit&units=imperial
    Retrieving Cities for 675 of Set 14 | chake chake
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chake chake&units=imperial
    Retrieving Cities for 676 of Set 14 | san vicente
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san vicente&units=imperial
    Retrieving Cities for 677 of Set 14 | metro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=metro&units=imperial
    Retrieving Cities for 678 of Set 14 | koeru
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=koeru&units=imperial
    Retrieving Cities for 679 of Set 14 | takoradi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=takoradi&units=imperial
    Retrieving Cities for 680 of Set 14 | skjervoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=skjervoy&units=imperial
    Retrieving Cities for 681 of Set 14 | volot
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=volot&units=imperial
    Retrieving Cities for 682 of Set 14 | cap malheureux
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cap malheureux&units=imperial
    Retrieving Cities for 683 of Set 14 | tarnow
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tarnow&units=imperial
    Retrieving Cities for 684 of Set 14 | bogalusa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bogalusa&units=imperial
    Retrieving Cities for 685 of Set 14 | ixtapa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ixtapa&units=imperial
    Retrieving Cities for 686 of Set 14 | nhulunbuy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nhulunbuy&units=imperial
    Retrieving Cities for 687 of Set 14 | naron
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=naron&units=imperial
    Retrieving Cities for 688 of Set 14 | lethbridge
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lethbridge&units=imperial
    Retrieving Cities for 689 of Set 14 | hanyang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hanyang&units=imperial
    Retrieving Cities for 690 of Set 14 | el triunfo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=el triunfo&units=imperial
    Retrieving Cities for 691 of Set 14 | itarema
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=itarema&units=imperial
    Retrieving Cities for 692 of Set 14 | mandera
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mandera&units=imperial
    Retrieving Cities for 693 of Set 14 | quelimane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=quelimane&units=imperial
    Retrieving Cities for 694 of Set 14 | ibipeba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ibipeba&units=imperial
    Retrieving Cities for 695 of Set 14 | saravan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saravan&units=imperial
    Retrieving Cities for 696 of Set 14 | roebourne
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=roebourne&units=imperial
    Retrieving Cities for 697 of Set 14 | chimbote
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chimbote&units=imperial
    Retrieving Cities for 698 of Set 14 | preobrazheniye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=preobrazheniye&units=imperial
    Retrieving Cities for 699 of Set 14 | gorontalo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gorontalo&units=imperial
    Retrieving Cities for 700 of Set 15 | north auburn
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=north auburn&units=imperial
    Retrieving Cities for 701 of Set 15 | arman
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arman&units=imperial
    Retrieving Cities for 702 of Set 15 | gobabis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gobabis&units=imperial
    Retrieving Cities for 703 of Set 15 | egvekinot
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=egvekinot&units=imperial
    Retrieving Cities for 704 of Set 15 | shakawe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shakawe&units=imperial
    Retrieving Cities for 705 of Set 15 | kyra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kyra&units=imperial
    Not Found!
    Retrieving Cities for 706 of Set 15 | beira
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=beira&units=imperial
    Retrieving Cities for 707 of Set 15 | potsdam
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=potsdam&units=imperial
    Retrieving Cities for 708 of Set 15 | zuwarah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zuwarah&units=imperial
    Retrieving Cities for 709 of Set 15 | potma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=potma&units=imperial
    Not Found!
    Retrieving Cities for 710 of Set 15 | saint-augustin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint-augustin&units=imperial
    Retrieving Cities for 711 of Set 15 | voyvozh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=voyvozh&units=imperial
    Retrieving Cities for 712 of Set 15 | kloulklubed
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kloulklubed&units=imperial
    Retrieving Cities for 713 of Set 15 | bereda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bereda&units=imperial
    Retrieving Cities for 714 of Set 15 | la romana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=la romana&units=imperial
    Retrieving Cities for 715 of Set 15 | san giorgio del sannio
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san giorgio del sannio&units=imperial
    Retrieving Cities for 716 of Set 15 | moyo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=moyo&units=imperial
    Not Found!
    Retrieving Cities for 717 of Set 15 | salinas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=salinas&units=imperial
    Retrieving Cities for 718 of Set 15 | kempsey
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kempsey&units=imperial
    Retrieving Cities for 719 of Set 15 | begun
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=begun&units=imperial
    Retrieving Cities for 720 of Set 15 | rincon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rincon&units=imperial
    Retrieving Cities for 721 of Set 15 | baykit
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=baykit&units=imperial
    Retrieving Cities for 722 of Set 15 | tooele
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tooele&units=imperial
    Retrieving Cities for 723 of Set 15 | verkh-usugli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=verkh-usugli&units=imperial
    Retrieving Cities for 724 of Set 15 | old road
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=old road&units=imperial
    Not Found!
    Retrieving Cities for 725 of Set 15 | finschhafen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=finschhafen&units=imperial
    Retrieving Cities for 726 of Set 15 | pondicherry
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pondicherry&units=imperial
    Retrieving Cities for 727 of Set 15 | marhaura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marhaura&units=imperial
    Retrieving Cities for 728 of Set 15 | ongandjera
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ongandjera&units=imperial
    Retrieving Cities for 729 of Set 15 | coquimbo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coquimbo&units=imperial
    Retrieving Cities for 730 of Set 15 | san policarpo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san policarpo&units=imperial
    Retrieving Cities for 731 of Set 15 | hoyanger
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hoyanger&units=imperial
    Retrieving Cities for 732 of Set 15 | galesong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=galesong&units=imperial
    Retrieving Cities for 733 of Set 15 | nahrin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nahrin&units=imperial
    Retrieving Cities for 734 of Set 15 | aflu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aflu&units=imperial
    Not Found!
    Retrieving Cities for 735 of Set 15 | cazones
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cazones&units=imperial
    Retrieving Cities for 736 of Set 15 | beringovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=beringovskiy&units=imperial
    Retrieving Cities for 737 of Set 15 | patnos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=patnos&units=imperial
    Retrieving Cities for 738 of Set 15 | constitucion
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=constitucion&units=imperial
    Retrieving Cities for 739 of Set 15 | buluang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=buluang&units=imperial
    Retrieving Cities for 740 of Set 15 | avera
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=avera&units=imperial
    Retrieving Cities for 741 of Set 15 | roura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=roura&units=imperial
    Retrieving Cities for 742 of Set 15 | wadena
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wadena&units=imperial
    Retrieving Cities for 743 of Set 15 | hambantota
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hambantota&units=imperial
    Retrieving Cities for 744 of Set 15 | kerouane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kerouane&units=imperial
    Retrieving Cities for 745 of Set 15 | mikhaylovka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mikhaylovka&units=imperial
    Retrieving Cities for 746 of Set 15 | port blair
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port blair&units=imperial
    Retrieving Cities for 747 of Set 15 | mabaruma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mabaruma&units=imperial
    Retrieving Cities for 748 of Set 15 | rawson
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rawson&units=imperial
    Retrieving Cities for 749 of Set 15 | magadan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=magadan&units=imperial
    Retrieving Cities for 750 of Set 16 | dwarka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dwarka&units=imperial
    Retrieving Cities for 751 of Set 16 | visnes
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=visnes&units=imperial
    Retrieving Cities for 752 of Set 16 | dunedin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dunedin&units=imperial
    Retrieving Cities for 753 of Set 16 | buraydah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=buraydah&units=imperial
    Retrieving Cities for 754 of Set 16 | jinchang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jinchang&units=imperial
    Retrieving Cities for 755 of Set 16 | halalo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=halalo&units=imperial
    Not Found!
    Retrieving Cities for 756 of Set 16 | irara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=irara&units=imperial
    Retrieving Cities for 757 of Set 16 | kinsale
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kinsale&units=imperial
    Retrieving Cities for 758 of Set 16 | talaya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=talaya&units=imperial
    Retrieving Cities for 759 of Set 16 | sri aman
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sri aman&units=imperial
    Retrieving Cities for 760 of Set 16 | irece
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=irece&units=imperial
    Retrieving Cities for 761 of Set 16 | xinxiang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=xinxiang&units=imperial
    Retrieving Cities for 762 of Set 16 | wanning
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wanning&units=imperial
    Retrieving Cities for 763 of Set 16 | boueni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boueni&units=imperial
    Retrieving Cities for 764 of Set 16 | shimoda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shimoda&units=imperial
    Retrieving Cities for 765 of Set 16 | quthing
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=quthing&units=imperial
    Retrieving Cities for 766 of Set 16 | koungou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=koungou&units=imperial
    Not Found!
    Retrieving Cities for 767 of Set 16 | yulara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yulara&units=imperial
    Retrieving Cities for 768 of Set 16 | alappuzha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=alappuzha&units=imperial
    Not Found!
    Retrieving Cities for 769 of Set 16 | laguna
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=laguna&units=imperial
    Retrieving Cities for 770 of Set 16 | winston
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=winston&units=imperial
    Retrieving Cities for 771 of Set 16 | saint anthony
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint anthony&units=imperial
    Retrieving Cities for 772 of Set 16 | manavalakurichi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manavalakurichi&units=imperial
    Retrieving Cities for 773 of Set 16 | laredo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=laredo&units=imperial
    Retrieving Cities for 774 of Set 16 | kulhudhuffushi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kulhudhuffushi&units=imperial
    Retrieving Cities for 775 of Set 16 | pella
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pella&units=imperial
    Retrieving Cities for 776 of Set 16 | mogadishu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mogadishu&units=imperial
    Retrieving Cities for 777 of Set 16 | karachi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=karachi&units=imperial
    Retrieving Cities for 778 of Set 16 | yongan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yongan&units=imperial
    Retrieving Cities for 779 of Set 16 | biltine
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=biltine&units=imperial
    Retrieving Cities for 780 of Set 16 | gravelbourg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gravelbourg&units=imperial
    Retrieving Cities for 781 of Set 16 | biak
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=biak&units=imperial
    Retrieving Cities for 782 of Set 16 | monteagudo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=monteagudo&units=imperial
    Retrieving Cities for 783 of Set 16 | salekhard
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=salekhard&units=imperial
    Retrieving Cities for 784 of Set 16 | lima
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lima&units=imperial
    Retrieving Cities for 785 of Set 16 | mathbaria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mathbaria&units=imperial
    Retrieving Cities for 786 of Set 16 | palabuhanratu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=palabuhanratu&units=imperial
    Not Found!
    Retrieving Cities for 787 of Set 16 | hovd
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hovd&units=imperial
    Retrieving Cities for 788 of Set 16 | hermon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hermon&units=imperial
    Retrieving Cities for 789 of Set 16 | emirdag
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=emirdag&units=imperial
    Retrieving Cities for 790 of Set 16 | yerbogachen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yerbogachen&units=imperial
    Retrieving Cities for 791 of Set 16 | lethem
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lethem&units=imperial
    Retrieving Cities for 792 of Set 16 | hayesville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hayesville&units=imperial
    Retrieving Cities for 793 of Set 16 | gorno-chuyskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gorno-chuyskiy&units=imperial
    Not Found!
    Retrieving Cities for 794 of Set 16 | paita
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=paita&units=imperial
    Retrieving Cities for 795 of Set 16 | puerto gaitan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto gaitan&units=imperial
    Not Found!
    Retrieving Cities for 796 of Set 16 | udachnyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=udachnyy&units=imperial
    Retrieving Cities for 797 of Set 16 | luba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=luba&units=imperial
    Retrieving Cities for 798 of Set 16 | severnoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=severnoye&units=imperial
    Retrieving Cities for 799 of Set 16 | tashtagol
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tashtagol&units=imperial
    Retrieving Cities for 800 of Set 17 | sabla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sabla&units=imperial
    Retrieving Cities for 801 of Set 17 | dori
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dori&units=imperial
    Retrieving Cities for 802 of Set 17 | sataua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sataua&units=imperial
    Not Found!
    Retrieving Cities for 803 of Set 17 | nouadhibou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nouadhibou&units=imperial
    Retrieving Cities for 804 of Set 17 | guanica
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=guanica&units=imperial
    Retrieving Cities for 805 of Set 17 | caluquembe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=caluquembe&units=imperial
    Retrieving Cities for 806 of Set 17 | steinbach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=steinbach&units=imperial
    Retrieving Cities for 807 of Set 17 | millville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=millville&units=imperial
    Retrieving Cities for 808 of Set 17 | san pedro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san pedro&units=imperial
    Retrieving Cities for 809 of Set 17 | ekimchan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ekimchan&units=imperial
    Retrieving Cities for 810 of Set 17 | ferme-neuve
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ferme-neuve&units=imperial
    Retrieving Cities for 811 of Set 17 | harper
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=harper&units=imperial
    Retrieving Cities for 812 of Set 17 | zalantun
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zalantun&units=imperial
    Retrieving Cities for 813 of Set 17 | langsa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=langsa&units=imperial
    Retrieving Cities for 814 of Set 17 | jacareacanga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jacareacanga&units=imperial
    Retrieving Cities for 815 of Set 17 | haapu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=haapu&units=imperial
    Not Found!
    Retrieving Cities for 816 of Set 17 | litovko
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=litovko&units=imperial
    Retrieving Cities for 817 of Set 17 | sertanopolis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sertanopolis&units=imperial
    Retrieving Cities for 818 of Set 17 | ranong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ranong&units=imperial
    Retrieving Cities for 819 of Set 17 | priargunsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=priargunsk&units=imperial
    Retrieving Cities for 820 of Set 17 | waverly
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=waverly&units=imperial
    Retrieving Cities for 821 of Set 17 | saleilua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saleilua&units=imperial
    Not Found!
    Retrieving Cities for 822 of Set 17 | inuvik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=inuvik&units=imperial
    Retrieving Cities for 823 of Set 17 | dzilam gonzalez
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dzilam gonzalez&units=imperial
    Retrieving Cities for 824 of Set 17 | quatre cocos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=quatre cocos&units=imperial
    Retrieving Cities for 825 of Set 17 | aykhal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aykhal&units=imperial
    Retrieving Cities for 826 of Set 17 | dibaya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dibaya&units=imperial
    Not Found!
    Retrieving Cities for 827 of Set 17 | ciudad bolivar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ciudad bolivar&units=imperial
    Retrieving Cities for 828 of Set 17 | vila praia de ancora
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vila praia de ancora&units=imperial
    Retrieving Cities for 829 of Set 17 | bhag
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bhag&units=imperial
    Retrieving Cities for 830 of Set 17 | sturgis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sturgis&units=imperial
    Retrieving Cities for 831 of Set 17 | anuradhapura
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=anuradhapura&units=imperial
    Retrieving Cities for 832 of Set 17 | vostok
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vostok&units=imperial
    Retrieving Cities for 833 of Set 17 | araguacu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=araguacu&units=imperial
    Not Found!
    Retrieving Cities for 834 of Set 17 | prince rupert
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=prince rupert&units=imperial
    Retrieving Cities for 835 of Set 17 | bhairab bazar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bhairab bazar&units=imperial
    Retrieving Cities for 836 of Set 17 | vestmannaeyjar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vestmannaeyjar&units=imperial
    Retrieving Cities for 837 of Set 17 | kumeny
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kumeny&units=imperial
    Retrieving Cities for 838 of Set 17 | nizwa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nizwa&units=imperial
    Retrieving Cities for 839 of Set 17 | avenal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=avenal&units=imperial
    Retrieving Cities for 840 of Set 17 | manaus
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manaus&units=imperial
    Retrieving Cities for 841 of Set 17 | vondrozo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vondrozo&units=imperial
    Retrieving Cities for 842 of Set 17 | kautokeino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kautokeino&units=imperial
    Retrieving Cities for 843 of Set 17 | twentynine palms
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=twentynine palms&units=imperial
    Retrieving Cities for 844 of Set 17 | longyan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=longyan&units=imperial
    Retrieving Cities for 845 of Set 17 | temirtau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=temirtau&units=imperial
    Retrieving Cities for 846 of Set 17 | poya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=poya&units=imperial
    Retrieving Cities for 847 of Set 17 | mackenzie
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mackenzie&units=imperial
    Retrieving Cities for 848 of Set 17 | perth
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=perth&units=imperial
    Retrieving Cities for 849 of Set 17 | safford
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=safford&units=imperial
    Retrieving Cities for 850 of Set 18 | ushumun
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ushumun&units=imperial
    Retrieving Cities for 851 of Set 18 | batemans bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=batemans bay&units=imperial
    Retrieving Cities for 852 of Set 18 | omboue
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=omboue&units=imperial
    Retrieving Cities for 853 of Set 18 | abilene
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abilene&units=imperial
    Retrieving Cities for 854 of Set 18 | kaeo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kaeo&units=imperial
    Retrieving Cities for 855 of Set 18 | carlton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=carlton&units=imperial
    Retrieving Cities for 856 of Set 18 | burgeo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=burgeo&units=imperial
    Retrieving Cities for 857 of Set 18 | nanakuli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nanakuli&units=imperial
    Retrieving Cities for 858 of Set 18 | bara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bara&units=imperial
    Retrieving Cities for 859 of Set 18 | safwah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=safwah&units=imperial
    Not Found!
    Retrieving Cities for 860 of Set 18 | huejuquilla el alto
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=huejuquilla el alto&units=imperial
    Retrieving Cities for 861 of Set 18 | colomi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=colomi&units=imperial
    Retrieving Cities for 862 of Set 18 | gamba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gamba&units=imperial
    Retrieving Cities for 863 of Set 18 | santa margherita ligure
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa margherita ligure&units=imperial
    Retrieving Cities for 864 of Set 18 | makakilo city
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=makakilo city&units=imperial
    Retrieving Cities for 865 of Set 18 | brenham
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=brenham&units=imperial
    Retrieving Cities for 866 of Set 18 | miri
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=miri&units=imperial
    Retrieving Cities for 867 of Set 18 | leninskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=leninskoye&units=imperial
    Retrieving Cities for 868 of Set 18 | kayes
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kayes&units=imperial
    Retrieving Cities for 869 of Set 18 | iquitos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=iquitos&units=imperial
    Retrieving Cities for 870 of Set 18 | liberal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=liberal&units=imperial
    Retrieving Cities for 871 of Set 18 | coatesville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coatesville&units=imperial
    Retrieving Cities for 872 of Set 18 | teresina
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=teresina&units=imperial
    Retrieving Cities for 873 of Set 18 | hovorany
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hovorany&units=imperial
    Retrieving Cities for 874 of Set 18 | dossor
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dossor&units=imperial
    Retrieving Cities for 875 of Set 18 | weligama
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=weligama&units=imperial
    Retrieving Cities for 876 of Set 18 | oskarshamn
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oskarshamn&units=imperial
    Retrieving Cities for 877 of Set 18 | neyshabur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=neyshabur&units=imperial
    Retrieving Cities for 878 of Set 18 | ketchikan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ketchikan&units=imperial
    Retrieving Cities for 879 of Set 18 | brigantine
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=brigantine&units=imperial
    Retrieving Cities for 880 of Set 18 | bossembele
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bossembele&units=imperial
    Not Found!
    Retrieving Cities for 881 of Set 18 | kargasok
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kargasok&units=imperial
    Retrieving Cities for 882 of Set 18 | senneterre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=senneterre&units=imperial
    Retrieving Cities for 883 of Set 18 | luwuk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=luwuk&units=imperial
    Retrieving Cities for 884 of Set 18 | grand centre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grand centre&units=imperial
    Not Found!
    Retrieving Cities for 885 of Set 18 | taitung
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=taitung&units=imperial
    Retrieving Cities for 886 of Set 18 | khorixas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=khorixas&units=imperial
    Retrieving Cities for 887 of Set 18 | coruripe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coruripe&units=imperial
    Retrieving Cities for 888 of Set 18 | antalaha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=antalaha&units=imperial
    Retrieving Cities for 889 of Set 18 | isla vista
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=isla vista&units=imperial
    Retrieving Cities for 890 of Set 18 | bukama
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bukama&units=imperial
    Retrieving Cities for 891 of Set 18 | borogontsy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=borogontsy&units=imperial
    Retrieving Cities for 892 of Set 18 | arona
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arona&units=imperial
    Retrieving Cities for 893 of Set 18 | plainview
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=plainview&units=imperial
    Retrieving Cities for 894 of Set 18 | dvinskoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dvinskoy&units=imperial
    Retrieving Cities for 895 of Set 18 | shushenskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shushenskoye&units=imperial
    Retrieving Cities for 896 of Set 18 | esso
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=esso&units=imperial
    Retrieving Cities for 897 of Set 18 | ardabil
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ardabil&units=imperial
    Retrieving Cities for 898 of Set 18 | kunigal
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kunigal&units=imperial
    Retrieving Cities for 899 of Set 18 | guymon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=guymon&units=imperial
    Retrieving Cities for 900 of Set 19 | amga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amga&units=imperial
    Retrieving Cities for 901 of Set 19 | emba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=emba&units=imperial
    Retrieving Cities for 902 of Set 19 | banyo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=banyo&units=imperial
    Retrieving Cities for 903 of Set 19 | girona
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=girona&units=imperial
    Retrieving Cities for 904 of Set 19 | pochutla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pochutla&units=imperial
    Retrieving Cities for 905 of Set 19 | tandlianwala
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tandlianwala&units=imperial
    Retrieving Cities for 906 of Set 19 | hongjiang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hongjiang&units=imperial
    Retrieving Cities for 907 of Set 19 | evensk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=evensk&units=imperial
    Retrieving Cities for 908 of Set 19 | sukhothai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sukhothai&units=imperial
    Retrieving Cities for 909 of Set 19 | tilichiki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tilichiki&units=imperial
    Retrieving Cities for 910 of Set 19 | inongo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=inongo&units=imperial
    Retrieving Cities for 911 of Set 19 | sinnamary
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sinnamary&units=imperial
    Retrieving Cities for 912 of Set 19 | khandyga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=khandyga&units=imperial
    Retrieving Cities for 913 of Set 19 | marcona
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marcona&units=imperial
    Not Found!
    Retrieving Cities for 914 of Set 19 | dolbeau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dolbeau&units=imperial
    Not Found!
    Retrieving Cities for 915 of Set 19 | andenes
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=andenes&units=imperial
    Not Found!
    Retrieving Cities for 916 of Set 19 | brae
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=brae&units=imperial
    Retrieving Cities for 917 of Set 19 | cerinza
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cerinza&units=imperial
    Retrieving Cities for 918 of Set 19 | letlhakane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=letlhakane&units=imperial
    Retrieving Cities for 919 of Set 19 | utiroa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=utiroa&units=imperial
    Not Found!
    Retrieving Cities for 920 of Set 19 | gunnedah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gunnedah&units=imperial
    Retrieving Cities for 921 of Set 19 | palmerston
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=palmerston&units=imperial
    Retrieving Cities for 922 of Set 19 | marv dasht
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marv dasht&units=imperial
    Not Found!
    Retrieving Cities for 923 of Set 19 | igarka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=igarka&units=imperial
    Retrieving Cities for 924 of Set 19 | visby
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=visby&units=imperial
    Retrieving Cities for 925 of Set 19 | gat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gat&units=imperial
    Retrieving Cities for 926 of Set 19 | sabang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sabang&units=imperial
    Retrieving Cities for 927 of Set 19 | shingu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shingu&units=imperial
    Retrieving Cities for 928 of Set 19 | newport
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=newport&units=imperial
    Retrieving Cities for 929 of Set 19 | forestville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=forestville&units=imperial
    Retrieving Cities for 930 of Set 19 | addanki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=addanki&units=imperial
    Retrieving Cities for 931 of Set 19 | igrim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=igrim&units=imperial
    Retrieving Cities for 932 of Set 19 | darovskoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=darovskoy&units=imperial
    Retrieving Cities for 933 of Set 19 | sinegorye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sinegorye&units=imperial
    Retrieving Cities for 934 of Set 19 | kavalerovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kavalerovo&units=imperial
    Retrieving Cities for 935 of Set 19 | nanao
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nanao&units=imperial
    Retrieving Cities for 936 of Set 19 | nara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nara&units=imperial
    Retrieving Cities for 937 of Set 19 | knoxville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=knoxville&units=imperial
    Retrieving Cities for 938 of Set 19 | soria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=soria&units=imperial
    Retrieving Cities for 939 of Set 19 | pombas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pombas&units=imperial
    Retrieving Cities for 940 of Set 19 | changli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=changli&units=imperial
    Retrieving Cities for 941 of Set 19 | havoysund
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=havoysund&units=imperial
    Retrieving Cities for 942 of Set 19 | neuquen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=neuquen&units=imperial
    Retrieving Cities for 943 of Set 19 | quincy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=quincy&units=imperial
    Retrieving Cities for 944 of Set 19 | kushmurun
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kushmurun&units=imperial
    Not Found!
    Retrieving Cities for 945 of Set 19 | manuk mangkaw
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manuk mangkaw&units=imperial
    Retrieving Cities for 946 of Set 19 | grenfell
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grenfell&units=imperial
    Retrieving Cities for 947 of Set 19 | vitim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vitim&units=imperial
    Retrieving Cities for 948 of Set 19 | dukat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dukat&units=imperial
    Retrieving Cities for 949 of Set 19 | bilma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bilma&units=imperial
    Retrieving Cities for 950 of Set 20 | maneadero
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=maneadero&units=imperial
    Not Found!
    Retrieving Cities for 951 of Set 20 | chapais
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chapais&units=imperial
    Retrieving Cities for 952 of Set 20 | mantua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mantua&units=imperial
    Retrieving Cities for 953 of Set 20 | idanre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=idanre&units=imperial
    Retrieving Cities for 954 of Set 20 | puli
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puli&units=imperial
    Retrieving Cities for 955 of Set 20 | sola
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sola&units=imperial
    Retrieving Cities for 956 of Set 20 | maldonado
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=maldonado&units=imperial
    Retrieving Cities for 957 of Set 20 | ust-maya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-maya&units=imperial
    Retrieving Cities for 958 of Set 20 | tamiahua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tamiahua&units=imperial
    Retrieving Cities for 959 of Set 20 | sahuaripa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sahuaripa&units=imperial
    Retrieving Cities for 960 of Set 20 | zlatoustovsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zlatoustovsk&units=imperial
    Not Found!
    Retrieving Cities for 961 of Set 20 | dongsheng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dongsheng&units=imperial
    Retrieving Cities for 962 of Set 20 | fort saint james
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fort saint james&units=imperial
    Retrieving Cities for 963 of Set 20 | abu jubayhah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abu jubayhah&units=imperial
    Not Found!
    Retrieving Cities for 964 of Set 20 | nam tha
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nam tha&units=imperial
    Not Found!
    Retrieving Cities for 965 of Set 20 | uarini
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=uarini&units=imperial
    Retrieving Cities for 966 of Set 20 | xichang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=xichang&units=imperial
    Retrieving Cities for 967 of Set 20 | westport
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=westport&units=imperial
    Retrieving Cities for 968 of Set 20 | lisakovsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lisakovsk&units=imperial
    Retrieving Cities for 969 of Set 20 | duz
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=duz&units=imperial
    Not Found!
    Retrieving Cities for 970 of Set 20 | lakshmipur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lakshmipur&units=imperial
    Retrieving Cities for 971 of Set 20 | umm lajj
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=umm lajj&units=imperial
    Retrieving Cities for 972 of Set 20 | murray
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=murray&units=imperial
    Retrieving Cities for 973 of Set 20 | veseloyarsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=veseloyarsk&units=imperial
    Retrieving Cities for 974 of Set 20 | tuggurt
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tuggurt&units=imperial
    Not Found!
    Retrieving Cities for 975 of Set 20 | launceston
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=launceston&units=imperial
    Retrieving Cities for 976 of Set 20 | havre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=havre&units=imperial
    Retrieving Cities for 977 of Set 20 | tra vinh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tra vinh&units=imperial
    Retrieving Cities for 978 of Set 20 | obo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=obo&units=imperial
    Retrieving Cities for 979 of Set 20 | manaure
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manaure&units=imperial
    Retrieving Cities for 980 of Set 20 | meulaboh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=meulaboh&units=imperial
    Retrieving Cities for 981 of Set 20 | storforshei
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=storforshei&units=imperial
    Retrieving Cities for 982 of Set 20 | lajas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lajas&units=imperial
    Retrieving Cities for 983 of Set 20 | camalu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=camalu&units=imperial
    Retrieving Cities for 984 of Set 20 | sao joao da barra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sao joao da barra&units=imperial
    Retrieving Cities for 985 of Set 20 | shenjiamen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shenjiamen&units=imperial
    Retrieving Cities for 986 of Set 20 | dauphin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dauphin&units=imperial
    Retrieving Cities for 987 of Set 20 | bermeo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bermeo&units=imperial
    Retrieving Cities for 988 of Set 20 | andevoranto
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=andevoranto&units=imperial
    Not Found!
    Retrieving Cities for 989 of Set 20 | tshikapa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tshikapa&units=imperial
    Retrieving Cities for 990 of Set 20 | nanhai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nanhai&units=imperial
    Retrieving Cities for 991 of Set 20 | kenai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kenai&units=imperial
    Retrieving Cities for 992 of Set 20 | garissa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=garissa&units=imperial
    Retrieving Cities for 993 of Set 20 | channel-port aux basques
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=channel-port aux basques&units=imperial
    Retrieving Cities for 994 of Set 20 | chenzhou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chenzhou&units=imperial
    Retrieving Cities for 995 of Set 20 | teguise
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=teguise&units=imperial
    Retrieving Cities for 996 of Set 20 | shache
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shache&units=imperial
    Retrieving Cities for 997 of Set 20 | suez
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=suez&units=imperial
    Retrieving Cities for 998 of Set 20 | george
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=george&units=imperial
    Retrieving Cities for 999 of Set 20 | sikasso
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sikasso&units=imperial
    Retrieving Cities for 1000 of Set 21 | shetpe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shetpe&units=imperial
    Retrieving Cities for 1001 of Set 21 | faya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=faya&units=imperial
    Retrieving Cities for 1002 of Set 21 | cheremkhovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cheremkhovo&units=imperial
    Retrieving Cities for 1003 of Set 21 | chor
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chor&units=imperial
    Retrieving Cities for 1004 of Set 21 | loandjili
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=loandjili&units=imperial
    Retrieving Cities for 1005 of Set 21 | anqing
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=anqing&units=imperial
    Retrieving Cities for 1006 of Set 21 | panaba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=panaba&units=imperial
    Retrieving Cities for 1007 of Set 21 | darnah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=darnah&units=imperial
    Retrieving Cities for 1008 of Set 21 | balikpapan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=balikpapan&units=imperial
    Retrieving Cities for 1009 of Set 21 | wanaka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wanaka&units=imperial
    Retrieving Cities for 1010 of Set 21 | waki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=waki&units=imperial
    Retrieving Cities for 1011 of Set 21 | broken hill
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=broken hill&units=imperial
    Retrieving Cities for 1012 of Set 21 | inirida
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=inirida&units=imperial
    Retrieving Cities for 1013 of Set 21 | yar-sale
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yar-sale&units=imperial
    Retrieving Cities for 1014 of Set 21 | bonavista
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bonavista&units=imperial
    Retrieving Cities for 1015 of Set 21 | rajsamand
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rajsamand&units=imperial
    Retrieving Cities for 1016 of Set 21 | russkiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=russkiy&units=imperial
    Retrieving Cities for 1017 of Set 21 | mkushi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mkushi&units=imperial
    Retrieving Cities for 1018 of Set 21 | pemangkat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pemangkat&units=imperial
    Not Found!
    Retrieving Cities for 1019 of Set 21 | messina
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=messina&units=imperial
    Retrieving Cities for 1020 of Set 21 | qurayyat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=qurayyat&units=imperial
    Not Found!
    Retrieving Cities for 1021 of Set 21 | clarence town
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=clarence town&units=imperial
    Retrieving Cities for 1022 of Set 21 | talcahuano
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=talcahuano&units=imperial
    Retrieving Cities for 1023 of Set 21 | boguchany
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boguchany&units=imperial
    Retrieving Cities for 1024 of Set 21 | jiuquan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jiuquan&units=imperial
    Retrieving Cities for 1025 of Set 21 | araouane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=araouane&units=imperial
    Retrieving Cities for 1026 of Set 21 | taicheng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=taicheng&units=imperial
    Retrieving Cities for 1027 of Set 21 | port pirie
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port pirie&units=imperial
    Retrieving Cities for 1028 of Set 21 | parrita
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=parrita&units=imperial
    Retrieving Cities for 1029 of Set 21 | werda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=werda&units=imperial
    Retrieving Cities for 1030 of Set 21 | yerofey pavlovich
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yerofey pavlovich&units=imperial
    Retrieving Cities for 1031 of Set 21 | sao gabriel da cachoeira
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sao gabriel da cachoeira&units=imperial
    Retrieving Cities for 1032 of Set 21 | mandsaur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mandsaur&units=imperial
    Retrieving Cities for 1033 of Set 21 | kizhinga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kizhinga&units=imperial
    Retrieving Cities for 1034 of Set 21 | lyantonde
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lyantonde&units=imperial
    Retrieving Cities for 1035 of Set 21 | peterhead
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=peterhead&units=imperial
    Retrieving Cities for 1036 of Set 21 | tobol
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tobol&units=imperial
    Retrieving Cities for 1037 of Set 21 | bacuit
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bacuit&units=imperial
    Not Found!
    Retrieving Cities for 1038 of Set 21 | orcopampa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=orcopampa&units=imperial
    Retrieving Cities for 1039 of Set 21 | vagay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vagay&units=imperial
    Retrieving Cities for 1040 of Set 21 | ushibuka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ushibuka&units=imperial
    Retrieving Cities for 1041 of Set 21 | whitehorse
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=whitehorse&units=imperial
    Retrieving Cities for 1042 of Set 21 | te anau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=te anau&units=imperial
    Retrieving Cities for 1043 of Set 21 | lagos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lagos&units=imperial
    Retrieving Cities for 1044 of Set 21 | wahpeton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wahpeton&units=imperial
    Retrieving Cities for 1045 of Set 21 | mineros
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mineros&units=imperial
    Retrieving Cities for 1046 of Set 21 | mangaratiba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mangaratiba&units=imperial
    Retrieving Cities for 1047 of Set 21 | tevaitoa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tevaitoa&units=imperial
    Retrieving Cities for 1048 of Set 21 | kita
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kita&units=imperial
    Retrieving Cities for 1049 of Set 21 | florianopolis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=florianopolis&units=imperial
    Retrieving Cities for 1050 of Set 22 | peace river
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=peace river&units=imperial
    Retrieving Cities for 1051 of Set 22 | sikeston
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sikeston&units=imperial
    Retrieving Cities for 1052 of Set 22 | padre bernardo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=padre bernardo&units=imperial
    Not Found!
    Retrieving Cities for 1053 of Set 22 | sovetskaya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sovetskaya&units=imperial
    Retrieving Cities for 1054 of Set 22 | tambovka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tambovka&units=imperial
    Retrieving Cities for 1055 of Set 22 | duku
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=duku&units=imperial
    Retrieving Cities for 1056 of Set 22 | fagernes
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fagernes&units=imperial
    Retrieving Cities for 1057 of Set 22 | shelburne
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shelburne&units=imperial
    Retrieving Cities for 1058 of Set 22 | rawah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rawah&units=imperial
    Not Found!
    Retrieving Cities for 1059 of Set 22 | otane
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=otane&units=imperial
    Retrieving Cities for 1060 of Set 22 | tawnat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tawnat&units=imperial
    Not Found!
    Retrieving Cities for 1061 of Set 22 | lahat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lahat&units=imperial
    Retrieving Cities for 1062 of Set 22 | zolotinka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zolotinka&units=imperial
    Not Found!
    Retrieving Cities for 1063 of Set 22 | cartagena
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cartagena&units=imperial
    Retrieving Cities for 1064 of Set 22 | bilibino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bilibino&units=imperial
    Retrieving Cities for 1065 of Set 22 | taywarah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=taywarah&units=imperial
    Retrieving Cities for 1066 of Set 22 | anadyr
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=anadyr&units=imperial
    Retrieving Cities for 1067 of Set 22 | maniitsoq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=maniitsoq&units=imperial
    Retrieving Cities for 1068 of Set 22 | russkaya polyana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=russkaya polyana&units=imperial
    Retrieving Cities for 1069 of Set 22 | jizan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jizan&units=imperial
    Retrieving Cities for 1070 of Set 22 | nakskov
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nakskov&units=imperial
    Retrieving Cities for 1071 of Set 22 | moa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=moa&units=imperial
    Retrieving Cities for 1072 of Set 22 | zyryanka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zyryanka&units=imperial
    Retrieving Cities for 1073 of Set 22 | ahuimanu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ahuimanu&units=imperial
    Retrieving Cities for 1074 of Set 22 | mtwango
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mtwango&units=imperial
    Retrieving Cities for 1075 of Set 22 | malindi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malindi&units=imperial
    Retrieving Cities for 1076 of Set 22 | lerwick
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lerwick&units=imperial
    Retrieving Cities for 1077 of Set 22 | macamic
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=macamic&units=imperial
    Retrieving Cities for 1078 of Set 22 | kasongo-lunda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kasongo-lunda&units=imperial
    Retrieving Cities for 1079 of Set 22 | anloga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=anloga&units=imperial
    Retrieving Cities for 1080 of Set 22 | wajima
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wajima&units=imperial
    Retrieving Cities for 1081 of Set 22 | ksenyevka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ksenyevka&units=imperial
    Not Found!
    Retrieving Cities for 1082 of Set 22 | grand-lahou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grand-lahou&units=imperial
    Retrieving Cities for 1083 of Set 22 | ramotswa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ramotswa&units=imperial
    Retrieving Cities for 1084 of Set 22 | hare bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hare bay&units=imperial
    Retrieving Cities for 1085 of Set 22 | methoni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=methoni&units=imperial
    Retrieving Cities for 1086 of Set 22 | hokitika
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hokitika&units=imperial
    Retrieving Cities for 1087 of Set 22 | takapau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=takapau&units=imperial
    Retrieving Cities for 1088 of Set 22 | ko samui
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ko samui&units=imperial
    Retrieving Cities for 1089 of Set 22 | clearwater
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=clearwater&units=imperial
    Retrieving Cities for 1090 of Set 22 | wembley
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wembley&units=imperial
    Retrieving Cities for 1091 of Set 22 | pangody
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pangody&units=imperial
    Retrieving Cities for 1092 of Set 22 | cocobeach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cocobeach&units=imperial
    Retrieving Cities for 1093 of Set 22 | dhola
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dhola&units=imperial
    Retrieving Cities for 1094 of Set 22 | claresholm
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=claresholm&units=imperial
    Retrieving Cities for 1095 of Set 22 | falealupo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=falealupo&units=imperial
    Not Found!
    Retrieving Cities for 1096 of Set 22 | gamboula
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gamboula&units=imperial
    Retrieving Cities for 1097 of Set 22 | bedford
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bedford&units=imperial
    Retrieving Cities for 1098 of Set 22 | emerald
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=emerald&units=imperial
    Retrieving Cities for 1099 of Set 22 | hirara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hirara&units=imperial
    Retrieving Cities for 1100 of Set 23 | ust-barguzin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-barguzin&units=imperial
    Retrieving Cities for 1101 of Set 23 | kamaishi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kamaishi&units=imperial
    Retrieving Cities for 1102 of Set 23 | pueblo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pueblo&units=imperial
    Retrieving Cities for 1103 of Set 23 | marang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marang&units=imperial
    Not Found!
    Retrieving Cities for 1104 of Set 23 | sciacca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sciacca&units=imperial
    Retrieving Cities for 1105 of Set 23 | keningau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=keningau&units=imperial
    Retrieving Cities for 1106 of Set 23 | phan rang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=phan rang&units=imperial
    Not Found!
    Retrieving Cities for 1107 of Set 23 | kangaatsiaq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kangaatsiaq&units=imperial
    Retrieving Cities for 1108 of Set 23 | bolungarvik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bolungarvik&units=imperial
    Not Found!
    Retrieving Cities for 1109 of Set 23 | dafeng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dafeng&units=imperial
    Retrieving Cities for 1110 of Set 23 | bayanday
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bayanday&units=imperial
    Retrieving Cities for 1111 of Set 23 | ibirite
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ibirite&units=imperial
    Retrieving Cities for 1112 of Set 23 | epernay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=epernay&units=imperial
    Retrieving Cities for 1113 of Set 23 | cody
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cody&units=imperial
    Retrieving Cities for 1114 of Set 23 | zabid
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zabid&units=imperial
    Retrieving Cities for 1115 of Set 23 | aginskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aginskoye&units=imperial
    Retrieving Cities for 1116 of Set 23 | eyl
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=eyl&units=imperial
    Retrieving Cities for 1117 of Set 23 | morro bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=morro bay&units=imperial
    Retrieving Cities for 1118 of Set 23 | carutapera
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=carutapera&units=imperial
    Retrieving Cities for 1119 of Set 23 | petropavlovka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=petropavlovka&units=imperial
    Retrieving Cities for 1120 of Set 23 | banatski karlovac
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=banatski karlovac&units=imperial
    Retrieving Cities for 1121 of Set 23 | tasbuget
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tasbuget&units=imperial
    Not Found!
    Retrieving Cities for 1122 of Set 23 | agadez
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=agadez&units=imperial
    Retrieving Cities for 1123 of Set 23 | pavino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pavino&units=imperial
    Retrieving Cities for 1124 of Set 23 | isla mujeres
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=isla mujeres&units=imperial
    Retrieving Cities for 1125 of Set 23 | tulungagung
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tulungagung&units=imperial
    Retrieving Cities for 1126 of Set 23 | umm kaddadah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=umm kaddadah&units=imperial
    Retrieving Cities for 1127 of Set 23 | rapid valley
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rapid valley&units=imperial
    Retrieving Cities for 1128 of Set 23 | bardiyah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bardiyah&units=imperial
    Not Found!
    Retrieving Cities for 1129 of Set 23 | ginir
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ginir&units=imperial
    Retrieving Cities for 1130 of Set 23 | serra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=serra&units=imperial
    Retrieving Cities for 1131 of Set 23 | campoverde
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=campoverde&units=imperial
    Retrieving Cities for 1132 of Set 23 | fare
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fare&units=imperial
    Retrieving Cities for 1133 of Set 23 | zinder
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zinder&units=imperial
    Retrieving Cities for 1134 of Set 23 | maghama
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=maghama&units=imperial
    Not Found!
    Retrieving Cities for 1135 of Set 23 | catamarca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=catamarca&units=imperial
    Not Found!
    Retrieving Cities for 1136 of Set 23 | leshukonskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=leshukonskoye&units=imperial
    Retrieving Cities for 1137 of Set 23 | zavyalovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zavyalovo&units=imperial
    Retrieving Cities for 1138 of Set 23 | bolshiye klyuchishchi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bolshiye klyuchishchi&units=imperial
    Not Found!
    Retrieving Cities for 1139 of Set 23 | vila
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vila&units=imperial
    Retrieving Cities for 1140 of Set 23 | gboko
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gboko&units=imperial
    Retrieving Cities for 1141 of Set 23 | samarai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=samarai&units=imperial
    Retrieving Cities for 1142 of Set 23 | acapulco
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=acapulco&units=imperial
    Retrieving Cities for 1143 of Set 23 | zig
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zig&units=imperial
    Retrieving Cities for 1144 of Set 23 | aden
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aden&units=imperial
    Retrieving Cities for 1145 of Set 23 | deputatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=deputatskiy&units=imperial
    Retrieving Cities for 1146 of Set 23 | trelew
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=trelew&units=imperial
    Retrieving Cities for 1147 of Set 23 | keti bandar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=keti bandar&units=imperial
    Retrieving Cities for 1148 of Set 23 | batagay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=batagay&units=imperial
    Retrieving Cities for 1149 of Set 23 | soyo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=soyo&units=imperial
    Retrieving Cities for 1150 of Set 24 | milledgeville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=milledgeville&units=imperial
    Retrieving Cities for 1151 of Set 24 | pandan niog
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pandan niog&units=imperial
    Retrieving Cities for 1152 of Set 24 | salinopolis
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=salinopolis&units=imperial
    Retrieving Cities for 1153 of Set 24 | concordia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=concordia&units=imperial
    Retrieving Cities for 1154 of Set 24 | north myrtle beach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=north myrtle beach&units=imperial
    Retrieving Cities for 1155 of Set 24 | yian
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yian&units=imperial
    Not Found!
    Retrieving Cities for 1156 of Set 24 | nemuro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nemuro&units=imperial
    Retrieving Cities for 1157 of Set 24 | boca do acre
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boca do acre&units=imperial
    Retrieving Cities for 1158 of Set 24 | verkhoyansk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=verkhoyansk&units=imperial
    Retrieving Cities for 1159 of Set 24 | plast
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=plast&units=imperial
    Retrieving Cities for 1160 of Set 24 | amalfi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amalfi&units=imperial
    Retrieving Cities for 1161 of Set 24 | mazyr
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mazyr&units=imperial
    Retrieving Cities for 1162 of Set 24 | wonthaggi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wonthaggi&units=imperial
    Retrieving Cities for 1163 of Set 24 | kuche
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kuche&units=imperial
    Not Found!
    Retrieving Cities for 1164 of Set 24 | qaqortoq
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=qaqortoq&units=imperial
    Retrieving Cities for 1165 of Set 24 | male
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=male&units=imperial
    Retrieving Cities for 1166 of Set 24 | ust-kuyga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-kuyga&units=imperial
    Retrieving Cities for 1167 of Set 24 | malatya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malatya&units=imperial
    Not Found!
    Retrieving Cities for 1168 of Set 24 | yellamanchili
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yellamanchili&units=imperial
    Not Found!
    Retrieving Cities for 1169 of Set 24 | aljezur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aljezur&units=imperial
    Retrieving Cities for 1170 of Set 24 | borovskoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=borovskoy&units=imperial
    Retrieving Cities for 1171 of Set 24 | mahon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mahon&units=imperial
    Retrieving Cities for 1172 of Set 24 | dali
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dali&units=imperial
    Retrieving Cities for 1173 of Set 24 | nouna
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nouna&units=imperial
    Retrieving Cities for 1174 of Set 24 | kapoeta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kapoeta&units=imperial
    Not Found!
    Retrieving Cities for 1175 of Set 24 | lashio
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lashio&units=imperial
    Retrieving Cities for 1176 of Set 24 | salamiyah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=salamiyah&units=imperial
    Retrieving Cities for 1177 of Set 24 | leon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=leon&units=imperial
    Retrieving Cities for 1178 of Set 24 | dalmau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dalmau&units=imperial
    Retrieving Cities for 1179 of Set 24 | goure
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=goure&units=imperial
    Retrieving Cities for 1180 of Set 24 | harrison
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=harrison&units=imperial
    Retrieving Cities for 1181 of Set 24 | victoria point
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=victoria point&units=imperial
    Retrieving Cities for 1182 of Set 24 | aberdeen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aberdeen&units=imperial
    Retrieving Cities for 1183 of Set 24 | whitianga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=whitianga&units=imperial
    Retrieving Cities for 1184 of Set 24 | riaba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=riaba&units=imperial
    Not Found!
    Retrieving Cities for 1185 of Set 24 | duldurga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=duldurga&units=imperial
    Retrieving Cities for 1186 of Set 24 | latung
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=latung&units=imperial
    Retrieving Cities for 1187 of Set 24 | coahuayana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=coahuayana&units=imperial
    Retrieving Cities for 1188 of Set 24 | stepnogorsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=stepnogorsk&units=imperial
    Retrieving Cities for 1189 of Set 24 | grand forks
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grand forks&units=imperial
    Retrieving Cities for 1190 of Set 24 | praia da vitoria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=praia da vitoria&units=imperial
    Retrieving Cities for 1191 of Set 24 | barawe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barawe&units=imperial
    Not Found!
    Retrieving Cities for 1192 of Set 24 | roseburg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=roseburg&units=imperial
    Retrieving Cities for 1193 of Set 24 | jasper
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jasper&units=imperial
    Retrieving Cities for 1194 of Set 24 | moroni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=moroni&units=imperial
    Retrieving Cities for 1195 of Set 24 | raudeberg
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=raudeberg&units=imperial
    Retrieving Cities for 1196 of Set 24 | aracaju
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aracaju&units=imperial
    Retrieving Cities for 1197 of Set 24 | carroll
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=carroll&units=imperial
    Retrieving Cities for 1198 of Set 24 | ingersoll
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ingersoll&units=imperial
    Retrieving Cities for 1199 of Set 24 | toungoo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=toungoo&units=imperial
    Not Found!
    Retrieving Cities for 1200 of Set 25 | ojinaga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ojinaga&units=imperial
    Retrieving Cities for 1201 of Set 25 | aleppo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aleppo&units=imperial
    Retrieving Cities for 1202 of Set 25 | shorapani
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shorapani&units=imperial
    Retrieving Cities for 1203 of Set 25 | waddan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=waddan&units=imperial
    Retrieving Cities for 1204 of Set 25 | gornopravdinsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gornopravdinsk&units=imperial
    Retrieving Cities for 1205 of Set 25 | yacuiba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yacuiba&units=imperial
    Retrieving Cities for 1206 of Set 25 | kisangani
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kisangani&units=imperial
    Retrieving Cities for 1207 of Set 25 | pachino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pachino&units=imperial
    Retrieving Cities for 1208 of Set 25 | petropavlovsk-kamchatskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=petropavlovsk-kamchatskiy&units=imperial
    Retrieving Cities for 1209 of Set 25 | saryozek
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saryozek&units=imperial
    Retrieving Cities for 1210 of Set 25 | ewa beach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ewa beach&units=imperial
    Retrieving Cities for 1211 of Set 25 | namatanai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=namatanai&units=imperial
    Retrieving Cities for 1212 of Set 25 | san ignacio
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san ignacio&units=imperial
    Retrieving Cities for 1213 of Set 25 | jishu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jishu&units=imperial
    Retrieving Cities for 1214 of Set 25 | general bravo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=general bravo&units=imperial
    Retrieving Cities for 1215 of Set 25 | valparaiso
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=valparaiso&units=imperial
    Retrieving Cities for 1216 of Set 25 | yara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yara&units=imperial
    Retrieving Cities for 1217 of Set 25 | wenling
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wenling&units=imperial
    Retrieving Cities for 1218 of Set 25 | haines junction
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=haines junction&units=imperial
    Retrieving Cities for 1219 of Set 25 | mocuba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mocuba&units=imperial
    Retrieving Cities for 1220 of Set 25 | milos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=milos&units=imperial
    Retrieving Cities for 1221 of Set 25 | boden
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=boden&units=imperial
    Retrieving Cities for 1222 of Set 25 | mukhen
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mukhen&units=imperial
    Retrieving Cities for 1223 of Set 25 | bada
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bada&units=imperial
    Retrieving Cities for 1224 of Set 25 | la palma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=la palma&units=imperial
    Retrieving Cities for 1225 of Set 25 | porterville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=porterville&units=imperial
    Retrieving Cities for 1226 of Set 25 | sembe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sembe&units=imperial
    Not Found!
    Retrieving Cities for 1227 of Set 25 | tomatlan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tomatlan&units=imperial
    Retrieving Cities for 1228 of Set 25 | lingao
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lingao&units=imperial
    Retrieving Cities for 1229 of Set 25 | borlange
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=borlange&units=imperial
    Not Found!
    Retrieving Cities for 1230 of Set 25 | rongcheng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rongcheng&units=imperial
    Retrieving Cities for 1231 of Set 25 | roald
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=roald&units=imperial
    Retrieving Cities for 1232 of Set 25 | tessalit
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tessalit&units=imperial
    Retrieving Cities for 1233 of Set 25 | buala
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=buala&units=imperial
    Retrieving Cities for 1234 of Set 25 | seybaplaya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=seybaplaya&units=imperial
    Retrieving Cities for 1235 of Set 25 | kamenskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kamenskoye&units=imperial
    Not Found!
    Retrieving Cities for 1236 of Set 25 | kant
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kant&units=imperial
    Retrieving Cities for 1237 of Set 25 | dubbo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dubbo&units=imperial
    Retrieving Cities for 1238 of Set 25 | ust-karsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-karsk&units=imperial
    Retrieving Cities for 1239 of Set 25 | trinidad
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=trinidad&units=imperial
    Retrieving Cities for 1240 of Set 25 | camocim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=camocim&units=imperial
    Retrieving Cities for 1241 of Set 25 | griffith
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=griffith&units=imperial
    Retrieving Cities for 1242 of Set 25 | bolshiye uki
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bolshiye uki&units=imperial
    Not Found!
    Retrieving Cities for 1243 of Set 25 | magdagachi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=magdagachi&units=imperial
    Retrieving Cities for 1244 of Set 25 | domoni
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=domoni&units=imperial
    Not Found!
    Retrieving Cities for 1245 of Set 25 | bay roberts
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bay roberts&units=imperial
    Retrieving Cities for 1246 of Set 25 | sorong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sorong&units=imperial
    Retrieving Cities for 1247 of Set 25 | bang lamung
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bang lamung&units=imperial
    Retrieving Cities for 1248 of Set 25 | malibu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malibu&units=imperial
    Retrieving Cities for 1249 of Set 25 | timmins
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=timmins&units=imperial
    Retrieving Cities for 1250 of Set 26 | dondo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dondo&units=imperial
    Retrieving Cities for 1251 of Set 26 | karaul
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=karaul&units=imperial
    Not Found!
    Retrieving Cities for 1252 of Set 26 | santa cruz
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa cruz&units=imperial
    Retrieving Cities for 1253 of Set 26 | baoro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=baoro&units=imperial
    Retrieving Cities for 1254 of Set 26 | berdigestyakh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=berdigestyakh&units=imperial
    Retrieving Cities for 1255 of Set 26 | karamea
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=karamea&units=imperial
    Not Found!
    Retrieving Cities for 1256 of Set 26 | sitrah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sitrah&units=imperial
    Retrieving Cities for 1257 of Set 26 | birao
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=birao&units=imperial
    Retrieving Cities for 1258 of Set 26 | harer
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=harer&units=imperial
    Retrieving Cities for 1259 of Set 26 | cubara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cubara&units=imperial
    Retrieving Cities for 1260 of Set 26 | baghdad
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=baghdad&units=imperial
    Retrieving Cities for 1261 of Set 26 | is
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=is&units=imperial
    Not Found!
    Retrieving Cities for 1262 of Set 26 | sokolo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sokolo&units=imperial
    Retrieving Cities for 1263 of Set 26 | bursa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bursa&units=imperial
    Retrieving Cities for 1264 of Set 26 | verkhnyaya inta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=verkhnyaya inta&units=imperial
    Retrieving Cities for 1265 of Set 26 | zhigalovo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zhigalovo&units=imperial
    Retrieving Cities for 1266 of Set 26 | sirvintos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sirvintos&units=imperial
    Retrieving Cities for 1267 of Set 26 | pahrump
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pahrump&units=imperial
    Retrieving Cities for 1268 of Set 26 | marsa matruh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marsa matruh&units=imperial
    Retrieving Cities for 1269 of Set 26 | kilmez
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kilmez&units=imperial
    Not Found!
    Retrieving Cities for 1270 of Set 26 | pokhara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pokhara&units=imperial
    Retrieving Cities for 1271 of Set 26 | katete
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=katete&units=imperial
    Retrieving Cities for 1272 of Set 26 | amazar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amazar&units=imperial
    Retrieving Cities for 1273 of Set 26 | port keats
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port keats&units=imperial
    Retrieving Cities for 1274 of Set 26 | akcakoca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=akcakoca&units=imperial
    Retrieving Cities for 1275 of Set 26 | fourmies
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fourmies&units=imperial
    Retrieving Cities for 1276 of Set 26 | bria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bria&units=imperial
    Retrieving Cities for 1277 of Set 26 | cockburn harbour
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cockburn harbour&units=imperial
    Not Found!
    Retrieving Cities for 1278 of Set 26 | tibati
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tibati&units=imperial
    Retrieving Cities for 1279 of Set 26 | leona vicario
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=leona vicario&units=imperial
    Retrieving Cities for 1280 of Set 26 | formoso do araguaia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=formoso do araguaia&units=imperial
    Not Found!
    Retrieving Cities for 1281 of Set 26 | abai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abai&units=imperial
    Retrieving Cities for 1282 of Set 26 | calvia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=calvia&units=imperial
    Retrieving Cities for 1283 of Set 26 | bjelovar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bjelovar&units=imperial
    Retrieving Cities for 1284 of Set 26 | palu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=palu&units=imperial
    Retrieving Cities for 1285 of Set 26 | xiaoweizhai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=xiaoweizhai&units=imperial
    Retrieving Cities for 1286 of Set 26 | geisenheim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=geisenheim&units=imperial
    Retrieving Cities for 1287 of Set 26 | zilair
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zilair&units=imperial
    Retrieving Cities for 1288 of Set 26 | kadykchan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kadykchan&units=imperial
    Not Found!
    Retrieving Cities for 1289 of Set 26 | belyy yar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=belyy yar&units=imperial
    Retrieving Cities for 1290 of Set 26 | saint andrews
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saint andrews&units=imperial
    Retrieving Cities for 1291 of Set 26 | genhe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=genhe&units=imperial
    Retrieving Cities for 1292 of Set 26 | gurupa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gurupa&units=imperial
    Not Found!
    Retrieving Cities for 1293 of Set 26 | iracoubo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=iracoubo&units=imperial
    Retrieving Cities for 1294 of Set 26 | manicaragua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manicaragua&units=imperial
    Retrieving Cities for 1295 of Set 26 | aasiaat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aasiaat&units=imperial
    Retrieving Cities for 1296 of Set 26 | oktyabrskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oktyabrskoye&units=imperial
    Retrieving Cities for 1297 of Set 26 | cravo norte
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cravo norte&units=imperial
    Retrieving Cities for 1298 of Set 26 | ziro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ziro&units=imperial
    Retrieving Cities for 1299 of Set 26 | amapa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amapa&units=imperial
    Retrieving Cities for 1300 of Set 27 | port augusta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port augusta&units=imperial
    Retrieving Cities for 1301 of Set 27 | arcata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arcata&units=imperial
    Retrieving Cities for 1302 of Set 27 | barabai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barabai&units=imperial
    Retrieving Cities for 1303 of Set 27 | rock sound
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rock sound&units=imperial
    Retrieving Cities for 1304 of Set 27 | baneh
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=baneh&units=imperial
    Retrieving Cities for 1305 of Set 27 | axim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=axim&units=imperial
    Retrieving Cities for 1306 of Set 27 | vilhena
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vilhena&units=imperial
    Retrieving Cities for 1307 of Set 27 | birjand
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=birjand&units=imperial
    Retrieving Cities for 1308 of Set 27 | lieksa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lieksa&units=imperial
    Retrieving Cities for 1309 of Set 27 | rawlins
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rawlins&units=imperial
    Retrieving Cities for 1310 of Set 27 | chivilcoy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chivilcoy&units=imperial
    Retrieving Cities for 1311 of Set 27 | kirillov
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kirillov&units=imperial
    Retrieving Cities for 1312 of Set 27 | ratisbon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ratisbon&units=imperial
    Not Found!
    Retrieving Cities for 1313 of Set 27 | viligili
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=viligili&units=imperial
    Not Found!
    Retrieving Cities for 1314 of Set 27 | valvedditturai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=valvedditturai&units=imperial
    Retrieving Cities for 1315 of Set 27 | lluta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lluta&units=imperial
    Retrieving Cities for 1316 of Set 27 | mocambique
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mocambique&units=imperial
    Not Found!
    Retrieving Cities for 1317 of Set 27 | chipata
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chipata&units=imperial
    Retrieving Cities for 1318 of Set 27 | ivanteyevka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ivanteyevka&units=imperial
    Retrieving Cities for 1319 of Set 27 | kumba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kumba&units=imperial
    Retrieving Cities for 1320 of Set 27 | srandakan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=srandakan&units=imperial
    Retrieving Cities for 1321 of Set 27 | campbellton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=campbellton&units=imperial
    Retrieving Cities for 1322 of Set 27 | balaguer
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=balaguer&units=imperial
    Retrieving Cities for 1323 of Set 27 | christchurch
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=christchurch&units=imperial
    Retrieving Cities for 1324 of Set 27 | rudbar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rudbar&units=imperial
    Not Found!
    Retrieving Cities for 1325 of Set 27 | ngukurr
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ngukurr&units=imperial
    Not Found!
    Retrieving Cities for 1326 of Set 27 | cumaribo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cumaribo&units=imperial
    Not Found!
    Retrieving Cities for 1327 of Set 27 | hibbing
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hibbing&units=imperial
    Retrieving Cities for 1328 of Set 27 | jacmel
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jacmel&units=imperial
    Retrieving Cities for 1329 of Set 27 | middlebury
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=middlebury&units=imperial
    Retrieving Cities for 1330 of Set 27 | aswan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aswan&units=imperial
    Retrieving Cities for 1331 of Set 27 | soe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=soe&units=imperial
    Retrieving Cities for 1332 of Set 27 | ilebo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ilebo&units=imperial
    Retrieving Cities for 1333 of Set 27 | chara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chara&units=imperial
    Retrieving Cities for 1334 of Set 27 | shunyi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shunyi&units=imperial
    Retrieving Cities for 1335 of Set 27 | almazar
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=almazar&units=imperial
    Retrieving Cities for 1336 of Set 27 | tyup
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tyup&units=imperial
    Retrieving Cities for 1337 of Set 27 | ostersund
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ostersund&units=imperial
    Retrieving Cities for 1338 of Set 27 | aksarka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aksarka&units=imperial
    Retrieving Cities for 1339 of Set 27 | venice
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=venice&units=imperial
    Retrieving Cities for 1340 of Set 27 | makung
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=makung&units=imperial
    Not Found!
    Retrieving Cities for 1341 of Set 27 | terney
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=terney&units=imperial
    Retrieving Cities for 1342 of Set 27 | haapiti
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=haapiti&units=imperial
    Retrieving Cities for 1343 of Set 27 | berkak
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=berkak&units=imperial
    Retrieving Cities for 1344 of Set 27 | kanjiza
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kanjiza&units=imperial
    Retrieving Cities for 1345 of Set 27 | kropotkin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kropotkin&units=imperial
    Retrieving Cities for 1346 of Set 27 | mahadday weyne
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mahadday weyne&units=imperial
    Not Found!
    Retrieving Cities for 1347 of Set 27 | santa maria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa maria&units=imperial
    Retrieving Cities for 1348 of Set 27 | verkhnevilyuysk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=verkhnevilyuysk&units=imperial
    Retrieving Cities for 1349 of Set 27 | namie
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=namie&units=imperial
    Retrieving Cities for 1350 of Set 28 | basi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=basi&units=imperial
    Retrieving Cities for 1351 of Set 28 | armacao dos buzios
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=armacao dos buzios&units=imperial
    Not Found!
    Retrieving Cities for 1352 of Set 28 | malanje
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=malanje&units=imperial
    Retrieving Cities for 1353 of Set 28 | shizunai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=shizunai&units=imperial
    Retrieving Cities for 1354 of Set 28 | ust-koksa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-koksa&units=imperial
    Retrieving Cities for 1355 of Set 28 | anaconda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=anaconda&units=imperial
    Retrieving Cities for 1356 of Set 28 | semnan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=semnan&units=imperial
    Retrieving Cities for 1357 of Set 28 | angoram
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=angoram&units=imperial
    Retrieving Cities for 1358 of Set 28 | zhangye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zhangye&units=imperial
    Retrieving Cities for 1359 of Set 28 | fort myers beach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fort myers beach&units=imperial
    Retrieving Cities for 1360 of Set 28 | vilyuysk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vilyuysk&units=imperial
    Retrieving Cities for 1361 of Set 28 | amuntai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=amuntai&units=imperial
    Retrieving Cities for 1362 of Set 28 | saryshagan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=saryshagan&units=imperial
    Not Found!
    Retrieving Cities for 1363 of Set 28 | gorodishche
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gorodishche&units=imperial
    Retrieving Cities for 1364 of Set 28 | elat
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=elat&units=imperial
    Retrieving Cities for 1365 of Set 28 | ankang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ankang&units=imperial
    Retrieving Cities for 1366 of Set 28 | lakes entrance
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lakes entrance&units=imperial
    Retrieving Cities for 1367 of Set 28 | vaxjo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vaxjo&units=imperial
    Not Found!
    Retrieving Cities for 1368 of Set 28 | sao miguel
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sao miguel&units=imperial
    Retrieving Cities for 1369 of Set 28 | tete
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tete&units=imperial
    Retrieving Cities for 1370 of Set 28 | santiago del estero
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santiago del estero&units=imperial
    Retrieving Cities for 1371 of Set 28 | ambikapur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ambikapur&units=imperial
    Retrieving Cities for 1372 of Set 28 | mayo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mayo&units=imperial
    Retrieving Cities for 1373 of Set 28 | alotau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=alotau&units=imperial
    Not Found!
    Retrieving Cities for 1374 of Set 28 | jati
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jati&units=imperial
    Retrieving Cities for 1375 of Set 28 | arkansas city
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=arkansas city&units=imperial
    Retrieving Cities for 1376 of Set 28 | zelenoborskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zelenoborskiy&units=imperial
    Retrieving Cities for 1377 of Set 28 | creston
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=creston&units=imperial
    Retrieving Cities for 1378 of Set 28 | bahia blanca
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bahia blanca&units=imperial
    Retrieving Cities for 1379 of Set 28 | oktyabrskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oktyabrskiy&units=imperial
    Retrieving Cities for 1380 of Set 28 | buchanan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=buchanan&units=imperial
    Retrieving Cities for 1381 of Set 28 | rio gallegos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rio gallegos&units=imperial
    Retrieving Cities for 1382 of Set 28 | villa carlos paz
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=villa carlos paz&units=imperial
    Retrieving Cities for 1383 of Set 28 | ukiah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ukiah&units=imperial
    Retrieving Cities for 1384 of Set 28 | koumac
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=koumac&units=imperial
    Retrieving Cities for 1385 of Set 28 | caxito
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=caxito&units=imperial
    Retrieving Cities for 1386 of Set 28 | osakarovka
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=osakarovka&units=imperial
    Retrieving Cities for 1387 of Set 28 | grindavik
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=grindavik&units=imperial
    Retrieving Cities for 1388 of Set 28 | skovorodino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=skovorodino&units=imperial
    Retrieving Cities for 1389 of Set 28 | skagastrond
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=skagastrond&units=imperial
    Not Found!
    Retrieving Cities for 1390 of Set 28 | huarmey
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=huarmey&units=imperial
    Retrieving Cities for 1391 of Set 28 | ceyhan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ceyhan&units=imperial
    Retrieving Cities for 1392 of Set 28 | sabaudia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sabaudia&units=imperial
    Retrieving Cities for 1393 of Set 28 | charyshskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=charyshskoye&units=imperial
    Retrieving Cities for 1394 of Set 28 | exeter
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=exeter&units=imperial
    Retrieving Cities for 1395 of Set 28 | itaituba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=itaituba&units=imperial
    Retrieving Cities for 1396 of Set 28 | jiddah
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jiddah&units=imperial
    Not Found!
    Retrieving Cities for 1397 of Set 28 | assiniboia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=assiniboia&units=imperial
    Retrieving Cities for 1398 of Set 28 | barnstaple
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=barnstaple&units=imperial
    Retrieving Cities for 1399 of Set 28 | jenks
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=jenks&units=imperial
    Retrieving Cities for 1400 of Set 29 | chardara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chardara&units=imperial
    Not Found!
    Retrieving Cities for 1401 of Set 29 | san isidro
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=san isidro&units=imperial
    Retrieving Cities for 1402 of Set 29 | paraiso
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=paraiso&units=imperial
    Retrieving Cities for 1403 of Set 29 | ayan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ayan&units=imperial
    Retrieving Cities for 1404 of Set 29 | tanshui
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tanshui&units=imperial
    Not Found!
    Retrieving Cities for 1405 of Set 29 | xuddur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=xuddur&units=imperial
    Retrieving Cities for 1406 of Set 29 | de-kastri
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=de-kastri&units=imperial
    Retrieving Cities for 1407 of Set 29 | itarantim
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=itarantim&units=imperial
    Retrieving Cities for 1408 of Set 29 | libourne
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=libourne&units=imperial
    Retrieving Cities for 1409 of Set 29 | tessaoua
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tessaoua&units=imperial
    Retrieving Cities for 1410 of Set 29 | kungurtug
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kungurtug&units=imperial
    Retrieving Cities for 1411 of Set 29 | nkoteng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nkoteng&units=imperial
    Retrieving Cities for 1412 of Set 29 | navahrudak
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=navahrudak&units=imperial
    Retrieving Cities for 1413 of Set 29 | bom jesus
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bom jesus&units=imperial
    Retrieving Cities for 1414 of Set 29 | kabalo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kabalo&units=imperial
    Retrieving Cities for 1415 of Set 29 | ruteng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ruteng&units=imperial
    Retrieving Cities for 1416 of Set 29 | edd
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=edd&units=imperial
    Retrieving Cities for 1417 of Set 29 | abnub
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abnub&units=imperial
    Retrieving Cities for 1418 of Set 29 | oxapampa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=oxapampa&units=imperial
    Retrieving Cities for 1419 of Set 29 | sibolga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=sibolga&units=imperial
    Retrieving Cities for 1420 of Set 29 | ugoofaaru
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ugoofaaru&units=imperial
    Retrieving Cities for 1421 of Set 29 | nabire
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nabire&units=imperial
    Retrieving Cities for 1422 of Set 29 | bida
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bida&units=imperial
    Retrieving Cities for 1423 of Set 29 | comodoro rivadavia
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=comodoro rivadavia&units=imperial
    Retrieving Cities for 1424 of Set 29 | ruatoria
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ruatoria&units=imperial
    Not Found!
    Retrieving Cities for 1425 of Set 29 | walvis bay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=walvis bay&units=imperial
    Retrieving Cities for 1426 of Set 29 | hengyang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=hengyang&units=imperial
    Retrieving Cities for 1427 of Set 29 | port antonio
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port antonio&units=imperial
    Retrieving Cities for 1428 of Set 29 | carberry
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=carberry&units=imperial
    Retrieving Cities for 1429 of Set 29 | puerto madero
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=puerto madero&units=imperial
    Retrieving Cities for 1430 of Set 29 | ubinskoye
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ubinskoye&units=imperial
    Retrieving Cities for 1431 of Set 29 | phonhong
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=phonhong&units=imperial
    Retrieving Cities for 1432 of Set 29 | spring hill
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=spring hill&units=imperial
    Retrieving Cities for 1433 of Set 29 | vytegra
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vytegra&units=imperial
    Retrieving Cities for 1434 of Set 29 | vung tau
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vung tau&units=imperial
    Retrieving Cities for 1435 of Set 29 | port-gentil
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port-gentil&units=imperial
    Retrieving Cities for 1436 of Set 29 | vieux-habitants
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=vieux-habitants&units=imperial
    Retrieving Cities for 1437 of Set 29 | charters towers
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=charters towers&units=imperial
    Retrieving Cities for 1438 of Set 29 | nha trang
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nha trang&units=imperial
    Retrieving Cities for 1439 of Set 29 | berga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=berga&units=imperial
    Retrieving Cities for 1440 of Set 29 | joshimath
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=joshimath&units=imperial
    Retrieving Cities for 1441 of Set 29 | north bend
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=north bend&units=imperial
    Retrieving Cities for 1442 of Set 29 | yarada
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yarada&units=imperial
    Retrieving Cities for 1443 of Set 29 | zhoucheng
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=zhoucheng&units=imperial
    Retrieving Cities for 1444 of Set 29 | kualakapuas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kualakapuas&units=imperial
    Retrieving Cities for 1445 of Set 29 | dilla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=dilla&units=imperial
    Retrieving Cities for 1446 of Set 29 | kortkeros
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kortkeros&units=imperial
    Retrieving Cities for 1447 of Set 29 | yulin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yulin&units=imperial
    Retrieving Cities for 1448 of Set 29 | lere
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lere&units=imperial
    Retrieving Cities for 1449 of Set 29 | abu zabad
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=abu zabad&units=imperial
    Retrieving Cities for 1450 of Set 30 | codo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=codo&units=imperial
    Retrieving Cities for 1451 of Set 30 | cozumel
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cozumel&units=imperial
    Not Found!
    Retrieving Cities for 1452 of Set 30 | ikalamavony
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ikalamavony&units=imperial
    Retrieving Cities for 1453 of Set 30 | rodrigues alves
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=rodrigues alves&units=imperial
    Retrieving Cities for 1454 of Set 30 | mrirt
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mrirt&units=imperial
    Not Found!
    Retrieving Cities for 1455 of Set 30 | wiarton
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=wiarton&units=imperial
    Retrieving Cities for 1456 of Set 30 | porosozero
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=porosozero&units=imperial
    Retrieving Cities for 1457 of Set 30 | kieta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kieta&units=imperial
    Retrieving Cities for 1458 of Set 30 | acajutla
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=acajutla&units=imperial
    Retrieving Cities for 1459 of Set 30 | porto novo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=porto novo&units=imperial
    Retrieving Cities for 1460 of Set 30 | kodino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kodino&units=imperial
    Retrieving Cities for 1461 of Set 30 | ushtobe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ushtobe&units=imperial
    Retrieving Cities for 1462 of Set 30 | ampanihy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ampanihy&units=imperial
    Retrieving Cities for 1463 of Set 30 | nuzvid
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=nuzvid&units=imperial
    Retrieving Cities for 1464 of Set 30 | horta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=horta&units=imperial
    Retrieving Cities for 1465 of Set 30 | levelek
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=levelek&units=imperial
    Retrieving Cities for 1466 of Set 30 | votkinsk
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=votkinsk&units=imperial
    Retrieving Cities for 1467 of Set 30 | marfino
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=marfino&units=imperial
    Retrieving Cities for 1468 of Set 30 | tucumcari
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tucumcari&units=imperial
    Retrieving Cities for 1469 of Set 30 | reconquista
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=reconquista&units=imperial
    Retrieving Cities for 1470 of Set 30 | koslan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=koslan&units=imperial
    Retrieving Cities for 1471 of Set 30 | huambo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=huambo&units=imperial
    Retrieving Cities for 1472 of Set 30 | chlorakas
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=chlorakas&units=imperial
    Not Found!
    Retrieving Cities for 1473 of Set 30 | play cu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=play cu&units=imperial
    Not Found!
    Retrieving Cities for 1474 of Set 30 | lufilufi
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lufilufi&units=imperial
    Retrieving Cities for 1475 of Set 30 | villazon
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=villazon&units=imperial
    Not Found!
    Retrieving Cities for 1476 of Set 30 | andros town
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=andros town&units=imperial
    Retrieving Cities for 1477 of Set 30 | aras
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=aras&units=imperial
    Retrieving Cities for 1478 of Set 30 | mingshui
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mingshui&units=imperial
    Retrieving Cities for 1479 of Set 30 | gberia fotombu
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=gberia fotombu&units=imperial
    Retrieving Cities for 1480 of Set 30 | bend
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bend&units=imperial
    Retrieving Cities for 1481 of Set 30 | soto la marina
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=soto la marina&units=imperial
    Retrieving Cities for 1482 of Set 30 | matay
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=matay&units=imperial
    Retrieving Cities for 1483 of Set 30 | linares
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=linares&units=imperial
    Retrieving Cities for 1484 of Set 30 | samara
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=samara&units=imperial
    Retrieving Cities for 1485 of Set 30 | manicore
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manicore&units=imperial
    Retrieving Cities for 1486 of Set 30 | manjacaze
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=manjacaze&units=imperial
    Retrieving Cities for 1487 of Set 30 | waitati
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=waitati&units=imperial
    Retrieving Cities for 1488 of Set 30 | goundam
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=goundam&units=imperial
    Retrieving Cities for 1489 of Set 30 | pafos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pafos&units=imperial
    Not Found!
    Retrieving Cities for 1490 of Set 30 | luanda
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=luanda&units=imperial
    Retrieving Cities for 1491 of Set 30 | galiwinku
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=galiwinku&units=imperial
    Not Found!
    Retrieving Cities for 1492 of Set 30 | ila
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ila&units=imperial
    Retrieving Cities for 1493 of Set 30 | yurga
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=yurga&units=imperial
    Retrieving Cities for 1494 of Set 30 | messini
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=messini&units=imperial
    Retrieving Cities for 1495 of Set 30 | somotillo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=somotillo&units=imperial
    Retrieving Cities for 1496 of Set 30 | goksun
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=goksun&units=imperial
    Retrieving Cities for 1497 of Set 30 | goderich
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=goderich&units=imperial
    Retrieving Cities for 1498 of Set 30 | thinadhoo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=thinadhoo&units=imperial
    Retrieving Cities for 1499 of Set 30 | tecoanapa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=tecoanapa&units=imperial
    Retrieving Cities for 1500 of nan | mossendjo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mossendjo&units=imperial
    Retrieving Cities for 1501 of nan | una
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=una&units=imperial
    Retrieving Cities for 1502 of nan | ust-tsilma
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=ust-tsilma&units=imperial
    Retrieving Cities for 1503 of nan | kroya
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=kroya&units=imperial
    Retrieving Cities for 1504 of nan | porciuncula
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=porciuncula&units=imperial
    Retrieving Cities for 1505 of nan | koltubanovskiy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=koltubanovskiy&units=imperial
    Retrieving Cities for 1506 of nan | red bluff
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=red bluff&units=imperial
    Retrieving Cities for 1507 of nan | copiapo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=copiapo&units=imperial
    Retrieving Cities for 1508 of nan | cervo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=cervo&units=imperial
    Retrieving Cities for 1509 of nan | porto belo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=porto belo&units=imperial
    Retrieving Cities for 1510 of nan | fort morgan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=fort morgan&units=imperial
    Retrieving Cities for 1511 of nan | argentan
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=argentan&units=imperial
    Retrieving Cities for 1512 of nan | awbari
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=awbari&units=imperial
    Retrieving Cities for 1513 of nan | bushehr
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bushehr&units=imperial
    Retrieving Cities for 1514 of nan | afsin
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=afsin&units=imperial
    Retrieving Cities for 1515 of nan | farafangana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=farafangana&units=imperial
    Retrieving Cities for 1516 of nan | mastic beach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mastic beach&units=imperial
    Retrieving Cities for 1517 of nan | road town
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=road town&units=imperial
    Retrieving Cities for 1518 of nan | bumba
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=bumba&units=imperial
    Retrieving Cities for 1519 of nan | almaznyy
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=almaznyy&units=imperial
    Retrieving Cities for 1520 of nan | santa fe
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=santa fe&units=imperial
    Retrieving Cities for 1521 of nan | unai
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=unai&units=imperial
    Retrieving Cities for 1522 of nan | okitipupa
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=okitipupa&units=imperial
    Retrieving Cities for 1523 of nan | devils lake
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=devils lake&units=imperial
    Retrieving Cities for 1524 of nan | lardos
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=lardos&units=imperial
    Retrieving Cities for 1525 of nan | anamur
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=anamur&units=imperial
    Retrieving Cities for 1526 of nan | binzhou
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=binzhou&units=imperial
    Retrieving Cities for 1527 of nan | waterlooville
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=waterlooville&units=imperial
    Retrieving Cities for 1528 of nan | pueblo nuevo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=pueblo nuevo&units=imperial
    Retrieving Cities for 1529 of nan | mlowo
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=mlowo&units=imperial
    Retrieving Cities for 1530 of nan | owando
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=owando&units=imperial
    Retrieving Cities for 1531 of nan | port-cartier
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=port-cartier&units=imperial
    Retrieving Cities for 1532 of nan | glendive
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=glendive&units=imperial
    Retrieving Cities for 1533 of nan | punta alta
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=punta alta&units=imperial
    Retrieving Cities for 1534 of nan | virginia beach
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=virginia beach&units=imperial
    Retrieving Cities for 1535 of nan | burica
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=burica&units=imperial
    Not Found!
    Retrieving Cities for 1536 of nan | palana
    http://api.openweathermap.org/data/2.5/weather?appid=22db07e0d09190f7ddde59ef47f401cc&q=palana&units=imperial
    -------------------------
     DATA RETRIEVAL COMPLETE 
    -------------------------



```python
# Save the DATA into csv file to run analysis on it later
random_coord.to_csv('City_Weather_Data.csv')
```


```python
# Save the DATA into json file to run analysis on it later
random_coord.to_json('City_Weather_Data.json')
```

### READ THE SAVED WEATHER DATA AND RUN ANALYSIS


```python
# Read the City_Weather_Data.json file and make new DF
file = 'City_Weather_Data.json'
city_weather_data_df = pd.read_json(file)
```


```python
# display DF
print(len(city_weather_data_df))
city_weather_data_df.head()
```

    1537





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>City</th>
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lon</th>
      <th>Max Temp</th>
      <th>Set</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>toamasina</td>
      <td>40</td>
      <td>mg</td>
      <td>94</td>
      <td>-19</td>
      <td>51</td>
      <td>77</td>
      <td>Set 1</td>
      <td>2.24</td>
    </tr>
    <tr>
      <th>1</th>
      <td>qaanaaq</td>
      <td>0</td>
      <td>gl</td>
      <td>88</td>
      <td>84</td>
      <td>-89</td>
      <td>-15.19</td>
      <td>Set 1</td>
      <td>8.41</td>
    </tr>
    <tr>
      <th>10</th>
      <td>mildura</td>
      <td>0</td>
      <td>au</td>
      <td>66</td>
      <td>-34</td>
      <td>141</td>
      <td>52.41</td>
      <td>Set 1</td>
      <td>2.82</td>
    </tr>
    <tr>
      <th>100</th>
      <td>lucapa</td>
      <td>12</td>
      <td>ao</td>
      <td>100</td>
      <td>-8</td>
      <td>19</td>
      <td>68.61</td>
      <td>Set 3</td>
      <td>6.73</td>
    </tr>
    <tr>
      <th>1000</th>
      <td>shetpe</td>
      <td>0</td>
      <td>kz</td>
      <td>77</td>
      <td>44</td>
      <td>52</td>
      <td>46.47</td>
      <td>Set 21</td>
      <td>13.11</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Rearrange columns and reset index
city_weather_data_df = city_weather_data_df[['Lat','Lon','City','Country',
                                             'Max Temp','Humidity','Cloudiness','Wind Speed']]
city_weather_data_df = city_weather_data_df.reset_index()
print(len(city_weather_data_df))
city_weather_data_df.head()
```

    1537





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>Lat</th>
      <th>Lon</th>
      <th>City</th>
      <th>Country</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>-19</td>
      <td>51</td>
      <td>toamasina</td>
      <td>mg</td>
      <td>77</td>
      <td>94</td>
      <td>40</td>
      <td>2.24</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>84</td>
      <td>-89</td>
      <td>qaanaaq</td>
      <td>gl</td>
      <td>-15.19</td>
      <td>88</td>
      <td>0</td>
      <td>8.41</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10</td>
      <td>-34</td>
      <td>141</td>
      <td>mildura</td>
      <td>au</td>
      <td>52.41</td>
      <td>66</td>
      <td>0</td>
      <td>2.82</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100</td>
      <td>-8</td>
      <td>19</td>
      <td>lucapa</td>
      <td>ao</td>
      <td>68.61</td>
      <td>100</td>
      <td>12</td>
      <td>6.73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1000</td>
      <td>44</td>
      <td>52</td>
      <td>shetpe</td>
      <td>kz</td>
      <td>46.47</td>
      <td>77</td>
      <td>0</td>
      <td>13.11</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Check for empty cells
print("Empty cells: " + str(len(city_weather_data_df[city_weather_data_df.Humidity == ''])))
print("city_weather_data_df: " + str(len(city_weather_data_df)))
```

    Empty cells: 170
    city_weather_data_df: 1537



```python
# Replace empty cells with nan value
city_weather_data_df['Humidity'] = city_weather_data_df['Humidity'].replace('', np.nan)
city_weather_data_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>Lat</th>
      <th>Lon</th>
      <th>City</th>
      <th>Country</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>-19</td>
      <td>51</td>
      <td>toamasina</td>
      <td>mg</td>
      <td>77</td>
      <td>94.0</td>
      <td>40</td>
      <td>2.24</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>84</td>
      <td>-89</td>
      <td>qaanaaq</td>
      <td>gl</td>
      <td>-15.19</td>
      <td>88.0</td>
      <td>0</td>
      <td>8.41</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10</td>
      <td>-34</td>
      <td>141</td>
      <td>mildura</td>
      <td>au</td>
      <td>52.41</td>
      <td>66.0</td>
      <td>0</td>
      <td>2.82</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100</td>
      <td>-8</td>
      <td>19</td>
      <td>lucapa</td>
      <td>ao</td>
      <td>68.61</td>
      <td>100.0</td>
      <td>12</td>
      <td>6.73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1000</td>
      <td>44</td>
      <td>52</td>
      <td>shetpe</td>
      <td>kz</td>
      <td>46.47</td>
      <td>77.0</td>
      <td>0</td>
      <td>13.11</td>
    </tr>
  </tbody>
</table>
</div>




```python
#### TEST
len(city_weather_data_df)
```




    1537




```python
# Drop cells with nan values
city_weather_data_df = city_weather_data_df.dropna(how='any')
city_weather_data_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>index</th>
      <th>Lat</th>
      <th>Lon</th>
      <th>City</th>
      <th>Country</th>
      <th>Max Temp</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>-19</td>
      <td>51</td>
      <td>toamasina</td>
      <td>mg</td>
      <td>77</td>
      <td>94.0</td>
      <td>40</td>
      <td>2.24</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>84</td>
      <td>-89</td>
      <td>qaanaaq</td>
      <td>gl</td>
      <td>-15.19</td>
      <td>88.0</td>
      <td>0</td>
      <td>8.41</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10</td>
      <td>-34</td>
      <td>141</td>
      <td>mildura</td>
      <td>au</td>
      <td>52.41</td>
      <td>66.0</td>
      <td>0</td>
      <td>2.82</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100</td>
      <td>-8</td>
      <td>19</td>
      <td>lucapa</td>
      <td>ao</td>
      <td>68.61</td>
      <td>100.0</td>
      <td>12</td>
      <td>6.73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1000</td>
      <td>44</td>
      <td>52</td>
      <td>shetpe</td>
      <td>kz</td>
      <td>46.47</td>
      <td>77.0</td>
      <td>0</td>
      <td>13.11</td>
    </tr>
  </tbody>
</table>
</div>




```python
#### TEST
print("city_weather_data_df: " + str(len(city_weather_data_df)))
```

    city_weather_data_df: 1367



```python
city_weather_data_df.head()
city_weather_data_df.dtypes
```




    index           int64
    Lat             int64
    Lon             int64
    City           object
    Country        object
    Max Temp       object
    Humidity      float64
    Cloudiness     object
    Wind Speed     object
    dtype: object




```python
city_weather_data_df['Max Temp']=city_weather_data_df['Max Temp'].astype(int)
city_weather_data_df['Humidity']=city_weather_data_df['Humidity'].astype(int)
city_weather_data_df['Cloudiness']=city_weather_data_df['Cloudiness'].astype(int)
city_weather_data_df['Wind Speed']=city_weather_data_df['Wind Speed'].astype(int)
city_weather_data_df.dtypes
```




    index          int64
    Lat            int64
    Lon            int64
    City          object
    Country       object
    Max Temp       int64
    Humidity       int64
    Cloudiness     int64
    Wind Speed     int64
    dtype: object



### Take sample of 500+ random cities


```python
sample_df = city_weather_data_df.sample(n=600)
sample_df.head()
print(len(sample_df))
```

    600



```python
# Use seaborn to make the TEMPERATURE scatter plot
ax = sns.regplot(x='Lat', 
           y='Max Temp', 
           data=sample_df, 
           fit_reg=False,
           scatter_kws={"color":"darkblue","alpha":1,"s":25}) 

#Make the grid, set x-limit and y-limit
plt.grid()
plt.xlim(-100,100)
plt.ylim(-75,125)

#Make x-axis, y-axis & title labels
ax.axes.set_title('City Latitude vs. Maximum Temperature', fontsize=15,color="black",alpha=1)
ax.set_xlabel("Latitude",size = 11,color="black",alpha=1)
ax.set_ylabel("Max. Temprature (F)",size = 11,color="black",alpha=1)

ax.figure.set_size_inches(10,7)

sns.set_style("dark")

plt.show()
```


![png](output_31_0.png)



```python
# Use seaborn to make the HUMIDITY scatter plot
ax = sns.regplot(x='Lat', 
           y='Humidity', 
           data=sample_df, 
           fit_reg=False,
           scatter_kws={"color":"darkblue","alpha":1,"s":25}) 

#Make the grid, set x-limit and y-limit
plt.grid()
plt.xlim(-100,100)
plt.ylim(-75,125)

#Make x-axis, y-axis & title labels
ax.axes.set_title('City Latitude vs. Humidity', fontsize=15,color="black",alpha=1)
ax.set_xlabel("Latitude",size = 11,color="black",alpha=1)
ax.set_ylabel("Humidity (%)",size = 11,color="black",alpha=1)

ax.figure.set_size_inches(10,7)

sns.set_style("dark")

plt.show()
```


![png](output_32_0.png)



```python
# Use seaborn to make the CLOUDINESS scatter plot
ax = sns.regplot(x='Lat', 
           y='Cloudiness', 
           data=sample_df, 
           fit_reg=False,
           scatter_kws={"color":"darkblue","alpha":1,"s":25}) 

#Make the grid, set x-limit and y-limit
plt.grid()
plt.xlim(-100,100)
plt.ylim(-20,120)

#Make x-axis, y-axis & title labels
ax.axes.set_title('City Latitude vs. Cloudiness', fontsize=15,color="black",alpha=1)
ax.set_xlabel("Latitude",size = 11,color="black",alpha=1)
ax.set_ylabel("Cloudiness (%)",size = 11,color="black",alpha=1)

ax.figure.set_size_inches(10,7)

sns.set_style("dark")

plt.show()
```


![png](output_33_0.png)



```python
# Use seaborn to make the WIND SPEED scatter plot
ax = sns.regplot(x='Lat', 
           y='Wind Speed', 
           data=sample_df, 
           fit_reg=False,
           scatter_kws={"color":"darkblue","alpha":1,"s":25}) 

#Make the grid, set x-limit and y-limit
plt.grid()
plt.xlim(-100,100)
plt.ylim(-5,40)

#Make x-axis, y-axis & title labels
ax.axes.set_title('City Latitude vs. Wind Speed', fontsize=15,color="black",alpha=1)
ax.set_xlabel("Latitude",size = 11,color="black",alpha=1)
ax.set_ylabel("Wind Speed (mph)",size = 11,color="black",alpha=1)

ax.figure.set_size_inches(10,7)

sns.set_style("dark")

plt.show()
```


![png](output_34_0.png)



```python
# Use seaborn to make the WIND SPEED scatter plot
ax = sns.regplot(x='Cloudiness', 
           y='Humidity', 
           data=sample_df, 
           fit_reg=False,
           scatter_kws={"color":"darkblue","alpha":1,"s":25}) 

#Make the grid, set x-limit and y-limit
plt.grid()
plt.xlim(-20,120)
plt.ylim(-75,125)

#Make x-axis, y-axis & title labels
ax.axes.set_title('Cloudiness vs. Humidity', fontsize=15,color="black",alpha=1)
ax.set_xlabel("Cloudiness",size = 11,color="black",alpha=1)
ax.set_ylabel("Humidity",size = 11,color="black",alpha=1)

ax.figure.set_size_inches(10,7)

sns.set_style("dark")

plt.show()
```


![png](output_35_0.png)


