
# WeatherPy

### Observed Trends
* Temperature increases closer to equator.
* City latitude has little effect on humidity, except at the poles.
* Winds speeds tend to be greater further away from the equator. 


```python
#import dependencies
import pandas as pd
import random
import requests
from citipy import citipy
import matplotlib.pyplot as plt
from pprint import pprint

#import OpenWeatherMap api key
from config import api_key
```


```python
#create database to hold information from OpenWeatherMap
data = pd.DataFrame(columns =["Lat", "Long", "City", "Temperature", "Humidity",
                              "Clouds", "Wind Speed"])

#empty lists to append data to
lat = []
long = []

#for loop to generate random latitude and longitude cooridinates
for x in range(0,500):
    
    lat.append(random.uniform(-90,90))
    long.append(random.uniform(-180,180))
    
data["Lat"] = lat
data['Long'] = long

data.head()
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
      <th>Long</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>36.840030</td>
      <td>-138.326655</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19.417284</td>
      <td>-168.866576</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-37.299821</td>
      <td>-18.049731</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>79.278860</td>
      <td>-83.418560</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>62.560630</td>
      <td>147.222472</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#use CitiPy library to find city near randomly generated cooridinates

cities = []

#use a for loop to iterate through the rows of the Data dataframe
for index, row in data.iterrows():
    
    city = citipy.nearest_city(row["Lat"], row["Long"])
    cities.append(city.city_name)
    
data["City"] = cities

data.head()
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
      <th>Long</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>36.840030</td>
      <td>-138.326655</td>
      <td>fortuna</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19.417284</td>
      <td>-168.866576</td>
      <td>kapaa</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-37.299821</td>
      <td>-18.049731</td>
      <td>jamestown</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>79.278860</td>
      <td>-83.418560</td>
      <td>qaanaaq</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>62.560630</td>
      <td>147.222472</td>
      <td>myaundzha</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
#figure out how to remove and replace duplicates?
```


```python
#set up API request
url = "https://api.openweathermap.org/data/2.5/weather?"
key = api_key
units = "imperial"

#lists to append to
temp = []
humidity = []
clouds = []
wind = []

counter = 0

#iterate through the rows to pull data from OpenWeatherMap

for index, row in data.iterrows():
    counter += 1
    city = row["City"]
    target_url = url + "lat=" + str(row["Lat"]) + "&lon=" + str(row["Long"]) +\
    "&appid="+ key + "&units=" + units
    print("This is request number "+ str(counter))
    print("The name of the city is "+ (row["City"]).upper())
    print(target_url)
    print("---------------------------------------")
    response = requests.get(target_url).json()
    temp.append(response["main"]["temp"])
    humidity.append(response["main"]["humidity"])
    clouds.append(response["clouds"]["all"])
    wind.append(response["wind"]["speed"])
       

#assign the values from the arrays to the data frame
data["Temperature"] = temp
data["Humidity"] = humidity
data["Clouds"] = clouds
data["Wind Speed"] = wind
```

    This is request number 1
    The name of the city is FORTUNA
    https://api.openweathermap.org/data/2.5/weather?lat=36.84003020228995&lon=-138.32665499512603&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 2
    The name of the city is KAPAA
    https://api.openweathermap.org/data/2.5/weather?lat=19.417283807877823&lon=-168.86657590883217&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 3
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-37.299820801278116&lon=-18.049730955520886&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 4
    The name of the city is QAANAAQ
    https://api.openweathermap.org/data/2.5/weather?lat=79.27886035558458&lon=-83.41855985328722&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 5
    The name of the city is MYAUNDZHA
    https://api.openweathermap.org/data/2.5/weather?lat=62.56062971937183&lon=147.22247218400827&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 6
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-76.18311295976412&lon=92.29386515779225&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 7
    The name of the city is NORMAN WELLS
    https://api.openweathermap.org/data/2.5/weather?lat=75.65548238920292&lon=-118.72180832102211&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 8
    The name of the city is LUCAPA
    https://api.openweathermap.org/data/2.5/weather?lat=-8.6176557744527&lon=19.53060152775734&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 9
    The name of the city is HUSAVIK
    https://api.openweathermap.org/data/2.5/weather?lat=76.93171396456873&lon=-7.759768403514414&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 10
    The name of the city is BAMBOUS VIRIEUX
    https://api.openweathermap.org/data/2.5/weather?lat=-28.76803086121238&lon=69.45212127997826&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 11
    The name of the city is BREDASDORP
    https://api.openweathermap.org/data/2.5/weather?lat=-54.92101163071939&lon=23.233077415769003&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 12
    The name of the city is COUTANCES
    https://api.openweathermap.org/data/2.5/weather?lat=49.01984197418875&lon=-1.5327517318170294&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 13
    The name of the city is CHLORAKAS
    https://api.openweathermap.org/data/2.5/weather?lat=34.59436848134993&lon=31.153581604523225&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 14
    The name of the city is LOMPOC
    https://api.openweathermap.org/data/2.5/weather?lat=23.304192293757765&lon=-133.91521612519614&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 15
    The name of the city is LUDERITZ
    https://api.openweathermap.org/data/2.5/weather?lat=-28.018325437171768&lon=11.078889406607402&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 16
    The name of the city is PUERTO AYORA
    https://api.openweathermap.org/data/2.5/weather?lat=-28.260700643349963&lon=-103.51707029087135&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 17
    The name of the city is GERALDTON
    https://api.openweathermap.org/data/2.5/weather?lat=-30.98752248490247&lon=103.4368297415275&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 18
    The name of the city is BELUSHYA GUBA
    https://api.openweathermap.org/data/2.5/weather?lat=86.48974649061361&lon=54.792871032530115&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 19
    The name of the city is TAOUDENNI
    https://api.openweathermap.org/data/2.5/weather?lat=24.596892946012616&lon=-2.2856443366765973&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 20
    The name of the city is GAT
    https://api.openweathermap.org/data/2.5/weather?lat=26.513871642388878&lon=6.277506115817488&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 21
    The name of the city is NIKOLSKOYE
    https://api.openweathermap.org/data/2.5/weather?lat=44.277630050116926&lon=170.77424038390063&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 22
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-54.443125495215334&lon=86.33535982167984&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 23
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-85.5487563209264&lon=-135.59345938656116&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 24
    The name of the city is NEW NORFOLK
    https://api.openweathermap.org/data/2.5/weather?lat=-86.70634284810436&lon=128.87896149443156&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 25
    The name of the city is BUNDABERG
    https://api.openweathermap.org/data/2.5/weather?lat=-23.978225198622283&lon=152.64218236025903&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 26
    The name of the city is SHITANJING
    https://api.openweathermap.org/data/2.5/weather?lat=41.059426760422355&lon=104.08756580900626&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 27
    The name of the city is CHOKURDAKH
    https://api.openweathermap.org/data/2.5/weather?lat=79.19036235712252&lon=147.66858489684859&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 28
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-87.33932517416177&lon=-45.43446009515489&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 29
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-46.22884379605774&lon=-129.242873069639&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 30
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-53.05449471915493&lon=151.13494612844312&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 31
    The name of the city is TASIILAQ
    https://api.openweathermap.org/data/2.5/weather?lat=79.83964137360263&lon=-35.33377800490109&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 32
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-38.282391037483706&lon=-122.34210377175395&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 33
    The name of the city is VILA FRANCA DO CAMPO
    https://api.openweathermap.org/data/2.5/weather?lat=40.85810589043783&lon=-18.525453864591697&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 34
    The name of the city is LEBU
    https://api.openweathermap.org/data/2.5/weather?lat=-33.13536035809833&lon=-85.94794072323893&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 35
    The name of the city is KJOPSVIK
    https://api.openweathermap.org/data/2.5/weather?lat=67.77873865611775&lon=16.788501207462133&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 36
    The name of the city is CHAGDA
    https://api.openweathermap.org/data/2.5/weather?lat=56.69551674752461&lon=128.51148372823678&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 37
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-89.77976372580186&lon=-74.43587864796861&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 38
    The name of the city is MYS SHMIDTA
    https://api.openweathermap.org/data/2.5/weather?lat=77.07817635796653&lon=-175.68906680792477&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 39
    The name of the city is VASTERVIK
    https://api.openweathermap.org/data/2.5/weather?lat=57.45891042273803&lon=17.308355577685006&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 40
    The name of the city is PLETTENBERG BAY
    https://api.openweathermap.org/data/2.5/weather?lat=-46.93690449567615&lon=24.268981184749123&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 41
    The name of the city is DIKSON
    https://api.openweathermap.org/data/2.5/weather?lat=81.40781569694545&lon=83.35007136837885&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 42
    The name of the city is WAGAR
    https://api.openweathermap.org/data/2.5/weather?lat=16.953063290156862&lon=36.785449830517905&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 43
    The name of the city is ESPERANCE
    https://api.openweathermap.org/data/2.5/weather?lat=-46.50356819506595&lon=128.08933649550454&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 44
    The name of the city is ILULISSAT
    https://api.openweathermap.org/data/2.5/weather?lat=76.1369955875613&lon=-47.65777964981541&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 45
    The name of the city is PEMANGKAT
    https://api.openweathermap.org/data/2.5/weather?lat=1.70043926401776&lon=109.3131254815417&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 46
    The name of the city is HAVRE-SAINT-PIERRE
    https://api.openweathermap.org/data/2.5/weather?lat=55.51698270513896&lon=-62.58046337757449&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 47
    The name of the city is CARNARVON
    https://api.openweathermap.org/data/2.5/weather?lat=-32.30270547272512&lon=87.3198719601072&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 48
    The name of the city is BARA
    https://api.openweathermap.org/data/2.5/weather?lat=14.756784786278303&lon=30.483809242004952&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 49
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-84.93321150952752&lon=-31.070578260186892&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 50
    The name of the city is BUTARITARI
    https://api.openweathermap.org/data/2.5/weather?lat=6.196827003978285&lon=163.2781440574588&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 51
    The name of the city is MANDALGOVI
    https://api.openweathermap.org/data/2.5/weather?lat=46.62407590333737&lon=106.82193440096819&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 52
    The name of the city is NISHIHARA
    https://api.openweathermap.org/data/2.5/weather?lat=19.914717684721396&lon=132.35234502914722&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 53
    The name of the city is QAANAAQ
    https://api.openweathermap.org/data/2.5/weather?lat=77.76665160540884&lon=-77.76618155885326&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 54
    The name of the city is AMAPA
    https://api.openweathermap.org/data/2.5/weather?lat=4.696615992611584&lon=-46.0623133948161&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 55
    The name of the city is DINGLE
    https://api.openweathermap.org/data/2.5/weather?lat=52.475097773164464&lon=-12.401614997109476&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 56
    The name of the city is SAN VICENTE
    https://api.openweathermap.org/data/2.5/weather?lat=19.02924981066876&lon=124.81192606164518&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 57
    The name of the city is ILLOQQORTOORMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=87.48809144872146&lon=-30.604055235824376&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 58
    The name of the city is NGUKURR
    https://api.openweathermap.org/data/2.5/weather?lat=-16.534999778191462&lon=134.47321614498134&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 59
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-70.27131499245326&lon=117.1722718603769&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 60
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-54.879872146784365&lon=-121.71289622435249&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 61
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-35.049169189774105&lon=95.72177586088668&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 62
    The name of the city is FAANUI
    https://api.openweathermap.org/data/2.5/weather?lat=-12.34339165168808&lon=-156.41677705199163&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 63
    The name of the city is ATUONA
    https://api.openweathermap.org/data/2.5/weather?lat=-8.020007877507751&lon=-145.91775917038143&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 64
    The name of the city is FUKUE
    https://api.openweathermap.org/data/2.5/weather?lat=32.83528534084935&lon=126.83157105958219&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 65
    The name of the city is PUNAKHA
    https://api.openweathermap.org/data/2.5/weather?lat=27.780660662648472&lon=89.8182329363512&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 66
    The name of the city is VAINI
    https://api.openweathermap.org/data/2.5/weather?lat=-42.46088805684039&lon=-170.08618778315278&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 67
    The name of the city is DUNEDIN
    https://api.openweathermap.org/data/2.5/weather?lat=-56.036731313135405&lon=177.15448963874007&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 68
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-71.85713330977832&lon=103.50125618020206&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 69
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-84.441426912647&lon=142.97757838198117&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 70
    The name of the city is VAINI
    https://api.openweathermap.org/data/2.5/weather?lat=-37.090525351859995&lon=-171.49749423001867&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 71
    The name of the city is HASAKI
    https://api.openweathermap.org/data/2.5/weather?lat=27.126916848092364&lon=160.14588503199417&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 72
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-54.495672723029834&lon=114.12202017711206&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 73
    The name of the city is AHIPARA
    https://api.openweathermap.org/data/2.5/weather?lat=-33.89080819831959&lon=172.65273973509267&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 74
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-84.34002052777727&lon=133.00230643317235&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 75
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-56.97054341522917&lon=165.3832741273888&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 76
    The name of the city is KAVIENG
    https://api.openweathermap.org/data/2.5/weather?lat=16.5243164722759&lon=152.38434442882385&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 77
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-50.48291655196005&lon=167.06213162101056&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 78
    The name of the city is TURA
    https://api.openweathermap.org/data/2.5/weather?lat=62.65842443843016&lon=101.45405405660654&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 79
    The name of the city is KAILUA
    https://api.openweathermap.org/data/2.5/weather?lat=31.17294292163895&lon=-153.23143791881745&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 80
    The name of the city is KLAKSVIK
    https://api.openweathermap.org/data/2.5/weather?lat=79.17885296243512&lon=-3.8030352660092603&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 81
    The name of the city is CHUMPHON
    https://api.openweathermap.org/data/2.5/weather?lat=10.637605246916166&lon=99.67086193517622&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 82
    The name of the city is OSTERSUND
    https://api.openweathermap.org/data/2.5/weather?lat=62.770497457640914&lon=13.425268739378168&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 83
    The name of the city is CASTRO
    https://api.openweathermap.org/data/2.5/weather?lat=-43.62842869892386&lon=-99.05593968177057&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 84
    The name of the city is STARYY NADYM
    https://api.openweathermap.org/data/2.5/weather?lat=67.44477631004034&lon=72.69021792840746&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 85
    The name of the city is ODESSKOYE
    https://api.openweathermap.org/data/2.5/weather?lat=53.87950694631553&lon=72.8604696740721&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 86
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-56.31703966857922&lon=164.64227894624207&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 87
    The name of the city is BARROW
    https://api.openweathermap.org/data/2.5/weather?lat=84.18037156069536&lon=-161.0833719020192&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 88
    The name of the city is MISRATAH
    https://api.openweathermap.org/data/2.5/weather?lat=32.05805199340023&lon=15.686710353531879&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 89
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-80.99841075810762&lon=161.73273705905711&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 90
    The name of the city is SAMUSU
    https://api.openweathermap.org/data/2.5/weather?lat=-1.236273980649571&lon=-161.2732010299586&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 91
    The name of the city is VASTERHANINGE
    https://api.openweathermap.org/data/2.5/weather?lat=58.615435479611705&lon=18.85567510581788&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 92
    The name of the city is MUROS
    https://api.openweathermap.org/data/2.5/weather?lat=44.415412662874985&lon=-11.284748938711601&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 93
    The name of the city is ARRECIFE
    https://api.openweathermap.org/data/2.5/weather?lat=28.341886207518044&lon=-11.778623199626082&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 94
    The name of the city is FORT MYERS
    https://api.openweathermap.org/data/2.5/weather?lat=26.626290180661172&lon=-81.93222341308062&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 95
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-74.15058627200337&lon=94.75867836962266&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 96
    The name of the city is MAR DEL PLATA
    https://api.openweathermap.org/data/2.5/weather?lat=-48.877272980738994&lon=-45.401721732527164&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 97
    The name of the city is TALTAL
    https://api.openweathermap.org/data/2.5/weather?lat=-25.188416654733516&lon=-80.2959149262704&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 98
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-74.0476053927267&lon=79.90690857982958&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 99
    The name of the city is SITKA
    https://api.openweathermap.org/data/2.5/weather?lat=50.564547464431996&lon=-141.10735623818195&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 100
    The name of the city is PERTH
    https://api.openweathermap.org/data/2.5/weather?lat=-30.83180846953576&lon=114.1682132556765&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 101
    The name of the city is PORT ALFRED
    https://api.openweathermap.org/data/2.5/weather?lat=-51.00045499914438&lon=33.145434205678924&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 102
    The name of the city is PAAMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=55.62582565990763&lon=-50.676463233959765&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 103
    The name of the city is BUTARITARI
    https://api.openweathermap.org/data/2.5/weather?lat=24.60048207859913&lon=164.84259620554093&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 104
    The name of the city is VAINI
    https://api.openweathermap.org/data/2.5/weather?lat=-74.97869874728188&lon=-174.92703703072118&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 105
    The name of the city is KASHAN
    https://api.openweathermap.org/data/2.5/weather?lat=33.6570859972324&lon=51.22100789857774&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 106
    The name of the city is CHANIKA
    https://api.openweathermap.org/data/2.5/weather?lat=-5.622351561021858&lon=37.82290037477239&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 107
    The name of the city is MILDURA
    https://api.openweathermap.org/data/2.5/weather?lat=-33.77692332245213&lon=142.84043918892633&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 108
    The name of the city is VAITUPU
    https://api.openweathermap.org/data/2.5/weather?lat=-8.519223745155472&lon=-179.29674711149084&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 109
    The name of the city is SEVERO-KURILSK
    https://api.openweathermap.org/data/2.5/weather?lat=41.5587920752354&lon=158.3110241177494&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 110
    The name of the city is BUTARITARI
    https://api.openweathermap.org/data/2.5/weather?lat=18.013593681193612&lon=160.90041369150595&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 111
    The name of the city is ISABELA
    https://api.openweathermap.org/data/2.5/weather?lat=20.953664099522697&lon=-66.98451260235205&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 112
    The name of the city is CARAVELAS
    https://api.openweathermap.org/data/2.5/weather?lat=-20.04588772334968&lon=-28.884420424679092&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 113
    The name of the city is SAMBAVA
    https://api.openweathermap.org/data/2.5/weather?lat=-11.115586160346453&lon=54.53104334772081&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 114
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-48.691137172082975&lon=81.78428962093506&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 115
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-76.26229555987378&lon=-41.391551548825305&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 116
    The name of the city is TABIAUEA
    https://api.openweathermap.org/data/2.5/weather?lat=0.19723303743168685&lon=173.66481581863752&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 117
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-77.18442060369274&lon=-65.0724241327994&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 118
    The name of the city is MARGHERITA
    https://api.openweathermap.org/data/2.5/weather?lat=27.28002678040947&lon=95.57865667499323&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 119
    The name of the city is PONTA DO SOL
    https://api.openweathermap.org/data/2.5/weather?lat=25.906289741402375&lon=-36.563074293414104&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 120
    The name of the city is PANGNIRTUNG
    https://api.openweathermap.org/data/2.5/weather?lat=67.55181931625762&lon=-67.38786135587888&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 121
    The name of the city is EAST LONDON
    https://api.openweathermap.org/data/2.5/weather?lat=-64.48101601903633&lon=47.55143917906983&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 122
    The name of the city is PORT-CARTIER
    https://api.openweathermap.org/data/2.5/weather?lat=52.26773202903587&lon=-67.88915804608568&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 123
    The name of the city is GRAND GAUBE
    https://api.openweathermap.org/data/2.5/weather?lat=-15.940798444957522&lon=61.70874025861636&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 124
    The name of the city is MATAURA
    https://api.openweathermap.org/data/2.5/weather?lat=-54.877026775747694&lon=-160.6083384197082&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 125
    The name of the city is MEULABOH
    https://api.openweathermap.org/data/2.5/weather?lat=3.2021952088872325&lon=92.943377971394&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 126
    The name of the city is BAHERDEN
    https://api.openweathermap.org/data/2.5/weather?lat=38.00691502336667&lon=57.70848110066626&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 127
    The name of the city is PORT-GENTIL
    https://api.openweathermap.org/data/2.5/weather?lat=-0.9066325450697406&lon=6.391916132284024&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 128
    The name of the city is NEW NORFOLK
    https://api.openweathermap.org/data/2.5/weather?lat=-54.22060503227679&lon=138.95367625669184&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 129
    The name of the city is CAPE TOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-52.178690753042346&lon=-12.049742614565048&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 130
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-38.00503792283085&lon=-135.5572841425123&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 131
    The name of the city is SAINT GEORGE
    https://api.openweathermap.org/data/2.5/weather?lat=34.13093092414141&lon=-62.73915440355688&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 132
    The name of the city is HARNOSAND
    https://api.openweathermap.org/data/2.5/weather?lat=62.05240251373468&lon=18.557543846983407&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 133
    The name of the city is PIRGOS
    https://api.openweathermap.org/data/2.5/weather?lat=33.65712469864336&lon=25.233236225611478&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 134
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-31.114468907259955&lon=-113.07950164913154&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 135
    The name of the city is HERMANUS
    https://api.openweathermap.org/data/2.5/weather?lat=-88.95063298458882&lon=5.8133013349726355&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 136
    The name of the city is MISRATAH
    https://api.openweathermap.org/data/2.5/weather?lat=33.54785053922916&lon=15.894214805708032&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 137
    The name of the city is DUJUMA
    https://api.openweathermap.org/data/2.5/weather?lat=1.5396635850198237&lon=42.75256671265663&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 138
    The name of the city is CASTRO
    https://api.openweathermap.org/data/2.5/weather?lat=-48.523593895647856&lon=-94.1045737089507&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 139
    The name of the city is PORT SHEPSTONE
    https://api.openweathermap.org/data/2.5/weather?lat=-32.64656519299441&lon=33.532790664518274&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 140
    The name of the city is KYSYL-SYR
    https://api.openweathermap.org/data/2.5/weather?lat=64.61575554820865&lon=124.41036872903999&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 141
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-28.99894694981461&lon=-3.5309929159206206&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 142
    The name of the city is SASKYLAKH
    https://api.openweathermap.org/data/2.5/weather?lat=76.35645531038676&lon=118.0062444471584&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 143
    The name of the city is SASKYLAKH
    https://api.openweathermap.org/data/2.5/weather?lat=75.3364335495217&lon=108.72909470996967&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 144
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-76.65125004302695&lon=90.09067178247233&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 145
    The name of the city is HERMANUS
    https://api.openweathermap.org/data/2.5/weather?lat=-65.21439338735999&lon=4.530887216359076&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 146
    The name of the city is DONGZHEN
    https://api.openweathermap.org/data/2.5/weather?lat=22.143589037749237&lon=111.00514382601864&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 147
    The name of the city is CAPACI
    https://api.openweathermap.org/data/2.5/weather?lat=39.322470125483676&lon=13.597121994040606&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 148
    The name of the city is INDERBORSKIY
    https://api.openweathermap.org/data/2.5/weather?lat=48.7021191422273&lon=52.10754807596933&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 149
    The name of the city is EGVEKINOT
    https://api.openweathermap.org/data/2.5/weather?lat=67.78541214838228&lon=-176.19777746859208&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 150
    The name of the city is EAST LONDON
    https://api.openweathermap.org/data/2.5/weather?lat=-68.66198948835606&lon=50.9264283774329&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 151
    The name of the city is KRUISFONTEIN
    https://api.openweathermap.org/data/2.5/weather?lat=-62.282757678376385&lon=27.731807251152304&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 152
    The name of the city is HITHADHOO
    https://api.openweathermap.org/data/2.5/weather?lat=-3.5716148559172467&lon=76.09034680056038&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 153
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-65.22635013791749&lon=-136.99874199112526&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 154
    The name of the city is EGVEKINOT
    https://api.openweathermap.org/data/2.5/weather?lat=66.28363463537491&lon=-177.0688007623081&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 155
    The name of the city is BETHEL
    https://api.openweathermap.org/data/2.5/weather?lat=61.161657810854024&lon=-162.3820514907962&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 156
    The name of the city is PARABEL
    https://api.openweathermap.org/data/2.5/weather?lat=58.32719083503852&lon=81.83867634578041&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 157
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-19.780171687128345&lon=-136.79533885762726&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 158
    The name of the city is MYS SHMIDTA
    https://api.openweathermap.org/data/2.5/weather?lat=84.91424128207183&lon=-178.1941008928589&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 159
    The name of the city is MATTRU
    https://api.openweathermap.org/data/2.5/weather?lat=1.4149111630384255&lon=-16.23770535785266&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 160
    The name of the city is AVARUA
    https://api.openweathermap.org/data/2.5/weather?lat=-44.41836717684387&lon=-159.7178509127965&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 161
    The name of the city is SAMARAI
    https://api.openweathermap.org/data/2.5/weather?lat=-15.38850227691465&lon=152.68633901087873&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 162
    The name of the city is TUTOIA
    https://api.openweathermap.org/data/2.5/weather?lat=-0.7973442751881237&lon=-42.0906409044143&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 163
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-46.57126997885909&lon=60.85898058049622&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 164
    The name of the city is CAJATI
    https://api.openweathermap.org/data/2.5/weather?lat=-24.59484473037378&lon=-48.11093194734832&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 165
    The name of the city is BELUSHYA GUBA
    https://api.openweathermap.org/data/2.5/weather?lat=80.66895821060731&lon=46.13937376290298&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 166
    The name of the city is DJIBO
    https://api.openweathermap.org/data/2.5/weather?lat=13.871859291788866&lon=-1.7529687618357457&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 167
    The name of the city is ILLOQQORTOORMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=87.70250784571343&lon=-18.98662920859931&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 168
    The name of the city is PORT ELIZABETH
    https://api.openweathermap.org/data/2.5/weather?lat=-67.77071096949791&lon=34.07243374807018&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 169
    The name of the city is LAGOA
    https://api.openweathermap.org/data/2.5/weather?lat=46.897529158241355&lon=-26.209926784335437&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 170
    The name of the city is BANSWARA
    https://api.openweathermap.org/data/2.5/weather?lat=23.673364940524735&lon=74.41615879746297&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 171
    The name of the city is TASIILAQ
    https://api.openweathermap.org/data/2.5/weather?lat=61.669040918461576&lon=-37.73192551683118&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 172
    The name of the city is PUERTO AYORA
    https://api.openweathermap.org/data/2.5/weather?lat=-5.091305342681636&lon=-108.94885917326728&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 173
    The name of the city is RIBEIRA GRANDE
    https://api.openweathermap.org/data/2.5/weather?lat=35.733707926077756&lon=-41.45464998341987&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 174
    The name of the city is SAINT GEORGE
    https://api.openweathermap.org/data/2.5/weather?lat=29.09288889548104&lon=-57.05832782079557&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 175
    The name of the city is BUTARITARI
    https://api.openweathermap.org/data/2.5/weather?lat=26.538376688194646&lon=169.60856628771262&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 176
    The name of the city is BURAYDAH
    https://api.openweathermap.org/data/2.5/weather?lat=26.60430258442348&lon=44.921514636671986&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 177
    The name of the city is THINADHOO
    https://api.openweathermap.org/data/2.5/weather?lat=1.208318135505337&lon=70.26992293857572&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 178
    The name of the city is RIO GALLEGOS
    https://api.openweathermap.org/data/2.5/weather?lat=-51.30785092441332&lon=-65.31040643793183&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 179
    The name of the city is PORT ELIZABETH
    https://api.openweathermap.org/data/2.5/weather?lat=-82.68795825386859&lon=31.412213759827182&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 180
    The name of the city is VILA FRANCA DO CAMPO
    https://api.openweathermap.org/data/2.5/weather?lat=35.42980243529796&lon=-24.708904094527753&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 181
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-32.32149914627766&lon=-109.24108380359634&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 182
    The name of the city is SVETLOGORSK
    https://api.openweathermap.org/data/2.5/weather?lat=66.53360179253065&lon=92.86010853933976&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 183
    The name of the city is CABO SAN LUCAS
    https://api.openweathermap.org/data/2.5/weather?lat=17.453183605679413&lon=-118.5198832380371&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 184
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-36.358549317385716&lon=-13.567649567167479&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 185
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-46.274035017722554&lon=-118.49035242091628&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 186
    The name of the city is PORT ELIZABETH
    https://api.openweathermap.org/data/2.5/weather?lat=-52.24070532723658&lon=29.85567249192522&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 187
    The name of the city is THOMPSON
    https://api.openweathermap.org/data/2.5/weather?lat=70.45187550532242&lon=-91.73811733496406&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 188
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-81.084006905666&lon=114.64695496257963&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 189
    The name of the city is MUROS
    https://api.openweathermap.org/data/2.5/weather?lat=45.966052065632994&lon=-18.243109476158992&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 190
    The name of the city is TASIILAQ
    https://api.openweathermap.org/data/2.5/weather?lat=83.17735204429007&lon=-39.57118859566518&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 191
    The name of the city is PORT ELIZABETH
    https://api.openweathermap.org/data/2.5/weather?lat=-73.94122550773179&lon=31.620021034470312&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 192
    The name of the city is KAPAA
    https://api.openweathermap.org/data/2.5/weather?lat=13.410906813815046&lon=-172.50876585431251&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 193
    The name of the city is NOME
    https://api.openweathermap.org/data/2.5/weather?lat=65.62825892637855&lon=-163.007895597449&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 194
    The name of the city is CARUTAPERA
    https://api.openweathermap.org/data/2.5/weather?lat=9.58278188935303&lon=-40.895669162404204&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 195
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-83.19467803680747&lon=-58.31223144431728&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 196
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-30.91533454574504&lon=48.570954664430246&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 197
    The name of the city is SAINT-PIERRE
    https://api.openweathermap.org/data/2.5/weather?lat=45.47313392804705&lon=-55.16007379487442&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 198
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-81.01263486631058&lon=65.62902602981328&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 199
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-86.51098885031881&lon=175.6746218340328&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 200
    The name of the city is MPANDA
    https://api.openweathermap.org/data/2.5/weather?lat=-6.0602426426393095&lon=31.68045532385881&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 201
    The name of the city is GRAND RIVER SOUTH EAST
    https://api.openweathermap.org/data/2.5/weather?lat=-23.189543921183017&lon=79.0165246371447&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 202
    The name of the city is OLAFSVIK
    https://api.openweathermap.org/data/2.5/weather?lat=62.2703341281229&lon=-27.10020492576035&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 203
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-23.60444576635642&lon=-124.4264287364276&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 204
    The name of the city is BREDASDORP
    https://api.openweathermap.org/data/2.5/weather?lat=-78.59005610474112&lon=14.830952300054179&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 205
    The name of the city is FORTUNA
    https://api.openweathermap.org/data/2.5/weather?lat=33.658794300889085&lon=-135.96678962351498&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 206
    The name of the city is CARNARVON
    https://api.openweathermap.org/data/2.5/weather?lat=-28.0733351807389&lon=107.66431444848229&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 207
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-47.86030808232684&lon=154.56458871389788&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 208
    The name of the city is CHOKURDAKH
    https://api.openweathermap.org/data/2.5/weather?lat=76.90075509189018&lon=154.45976632562923&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 209
    The name of the city is CLYDE RIVER
    https://api.openweathermap.org/data/2.5/weather?lat=72.89210110291384&lon=-65.15015970607516&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 210
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-86.5322867627498&lon=-87.39197931049236&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 211
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-79.97954935281578&lon=138.5630067737294&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 212
    The name of the city is PEVEK
    https://api.openweathermap.org/data/2.5/weather?lat=86.85939781667486&lon=169.05821086027464&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 213
    The name of the city is CLARENCE TOWN
    https://api.openweathermap.org/data/2.5/weather?lat=23.04463944370947&lon=-74.50300672625714&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 214
    The name of the city is MEHAMN
    https://api.openweathermap.org/data/2.5/weather?lat=82.90499627569719&lon=27.4626187898873&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 215
    The name of the city is MAHEBOURG
    https://api.openweathermap.org/data/2.5/weather?lat=-52.85050105174598&lon=77.01585050401093&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 216
    The name of the city is AKLAVIK
    https://api.openweathermap.org/data/2.5/weather?lat=70.11277491539602&lon=-136.3221006698458&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 217
    The name of the city is BASOKO
    https://api.openweathermap.org/data/2.5/weather?lat=0.24720347892730388&lon=23.423544345555086&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 218
    The name of the city is BATHSHEBA
    https://api.openweathermap.org/data/2.5/weather?lat=23.252148735055485&lon=-44.208365415826876&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 219
    The name of the city is ILLOQQORTOORMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=71.88785686955043&lon=-18.707046001731385&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 220
    The name of the city is NEW NORFOLK
    https://api.openweathermap.org/data/2.5/weather?lat=-52.345542154155524&lon=139.92218925395525&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 221
    The name of the city is BEREDA
    https://api.openweathermap.org/data/2.5/weather?lat=11.961145675481973&lon=51.538653222854066&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 222
    The name of the city is MATAURA
    https://api.openweathermap.org/data/2.5/weather?lat=-66.35668941540904&lon=-153.24871121371325&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 223
    The name of the city is SAINT-PHILIPPE
    https://api.openweathermap.org/data/2.5/weather?lat=-73.38447501444284&lon=75.37714926196364&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 224
    The name of the city is AVARUA
    https://api.openweathermap.org/data/2.5/weather?lat=-44.431597634598916&lon=-166.37635575970162&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 225
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-66.97237174634779&lon=-61.962472604108854&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 226
    The name of the city is GIZO
    https://api.openweathermap.org/data/2.5/weather?lat=-13.268791932013258&lon=155.49808154874074&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 227
    The name of the city is BUTARITARI
    https://api.openweathermap.org/data/2.5/weather?lat=12.588807577470476&lon=175.88019105500746&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 228
    The name of the city is DZAOUDZI
    https://api.openweathermap.org/data/2.5/weather?lat=-11.605986018984893&lon=46.693849306408225&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 229
    The name of the city is PANGNIRTUNG
    https://api.openweathermap.org/data/2.5/weather?lat=65.45394086281149&lon=-60.63838754098863&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 230
    The name of the city is HONNINGSVAG
    https://api.openweathermap.org/data/2.5/weather?lat=76.94246430514895&lon=26.354472852384788&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 231
    The name of the city is MILLINOCKET
    https://api.openweathermap.org/data/2.5/weather?lat=45.74551554886864&lon=-68.49071158242519&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 232
    The name of the city is UPTAR
    https://api.openweathermap.org/data/2.5/weather?lat=59.77734442658141&lon=150.9384421281456&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 233
    The name of the city is JIZAN
    https://api.openweathermap.org/data/2.5/weather?lat=15.722958420179253&lon=41.836069147308535&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 234
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-88.91967140554523&lon=86.19290889341158&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 235
    The name of the city is HALF MOON BAY
    https://api.openweathermap.org/data/2.5/weather?lat=31.417513736806782&lon=-131.76673038734464&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 236
    The name of the city is SANTA MARIA
    https://api.openweathermap.org/data/2.5/weather?lat=20.178010094113986&lon=-21.176978087352865&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 237
    The name of the city is KHATANGA
    https://api.openweathermap.org/data/2.5/weather?lat=89.35138584906906&lon=102.49802148337812&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 238
    The name of the city is KLAKSVIK
    https://api.openweathermap.org/data/2.5/weather?lat=73.44483539774069&lon=-5.881664683345235&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 239
    The name of the city is HIRARA
    https://api.openweathermap.org/data/2.5/weather?lat=23.06257209251966&lon=125.35403256196247&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 240
    The name of the city is HERMANUS
    https://api.openweathermap.org/data/2.5/weather?lat=-71.03535703953875&lon=-2.959740260032646&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 241
    The name of the city is SITKA
    https://api.openweathermap.org/data/2.5/weather?lat=45.88504679429175&lon=-142.51469650992107&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 242
    The name of the city is RICHARDS BAY
    https://api.openweathermap.org/data/2.5/weather?lat=-33.59435683069561&lon=36.279045755498856&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 243
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-86.20808766497407&lon=154.33957299618083&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 244
    The name of the city is WADDAN
    https://api.openweathermap.org/data/2.5/weather?lat=29.526529543492956&lon=16.484330257993633&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 245
    The name of the city is CHKALOVSKOYE
    https://api.openweathermap.org/data/2.5/weather?lat=44.786573608174336&lon=133.1579378338031&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 246
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-76.90495876565663&lon=63.92881836725016&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 247
    The name of the city is TORBAY
    https://api.openweathermap.org/data/2.5/weather?lat=47.82113022684601&lon=-45.83025404794253&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 248
    The name of the city is MIRI
    https://api.openweathermap.org/data/2.5/weather?lat=5.624737668661226&lon=111.89931169300229&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 249
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-70.74959463517195&lon=93.69675637868545&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 250
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-81.97029397447402&lon=157.36077184464114&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 251
    The name of the city is DARHAN
    https://api.openweathermap.org/data/2.5/weather?lat=45.783827813789344&lon=110.51733123674626&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 252
    The name of the city is DUNDEE
    https://api.openweathermap.org/data/2.5/weather?lat=-28.175939333057826&lon=30.31530798031264&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 253
    The name of the city is HEARST
    https://api.openweathermap.org/data/2.5/weather?lat=50.84731389975502&lon=-84.1634216589881&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 254
    The name of the city is SOYO
    https://api.openweathermap.org/data/2.5/weather?lat=-9.917910900283147&lon=5.145763068927494&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 255
    The name of the city is SAINT-PHILIPPE
    https://api.openweathermap.org/data/2.5/weather?lat=-45.51386714151016&lon=67.1302050601011&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 256
    The name of the city is UPINGTON
    https://api.openweathermap.org/data/2.5/weather?lat=-28.796501513086&lon=20.325655752478525&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 257
    The name of the city is PUNTA ARENAS
    https://api.openweathermap.org/data/2.5/weather?lat=-52.067544546541654&lon=-76.94682755020082&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 258
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-75.39308951384415&lon=115.48909651216388&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 259
    The name of the city is TSIHOMBE
    https://api.openweathermap.org/data/2.5/weather?lat=-43.351885467143255&lon=46.14590284310523&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 260
    The name of the city is BETHEL
    https://api.openweathermap.org/data/2.5/weather?lat=52.914068100223574&lon=-162.08410259819587&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 261
    The name of the city is VAITUPU
    https://api.openweathermap.org/data/2.5/weather?lat=-5.018623915666325&lon=-174.9345203955893&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 262
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-84.65245961101668&lon=-46.17404726065496&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 263
    The name of the city is GAT
    https://api.openweathermap.org/data/2.5/weather?lat=26.669282235183417&lon=10.6704957194967&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 264
    The name of the city is EAST LONDON
    https://api.openweathermap.org/data/2.5/weather?lat=-63.27532107515222&lon=51.14811552746542&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 265
    The name of the city is OMSUKCHAN
    https://api.openweathermap.org/data/2.5/weather?lat=61.29009315999707&lon=156.5682978443533&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 266
    The name of the city is KEMIJARVI
    https://api.openweathermap.org/data/2.5/weather?lat=66.29123904268457&lon=28.048476513662735&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 267
    The name of the city is HOLLABRUNN
    https://api.openweathermap.org/data/2.5/weather?lat=48.52041915298591&lon=16.13402783216256&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 268
    The name of the city is GEORGETOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-3.549297105555908&lon=-14.717706466535276&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 269
    The name of the city is ABU SAMRAH
    https://api.openweathermap.org/data/2.5/weather?lat=19.903043170736453&lon=49.48452838212509&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 270
    The name of the city is KLOULKLUBED
    https://api.openweathermap.org/data/2.5/weather?lat=7.3327478454055495&lon=133.20821612389676&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 271
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-33.43729289188785&lon=113.23638192174701&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 272
    The name of the city is BUD
    https://api.openweathermap.org/data/2.5/weather?lat=63.37642565256499&lon=7.039981490439828&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 273
    The name of the city is TURUKHANSK
    https://api.openweathermap.org/data/2.5/weather?lat=64.15330052878932&lon=91.30058786476462&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 274
    The name of the city is SALDANHA
    https://api.openweathermap.org/data/2.5/weather?lat=-39.4872109290762&lon=-1.4726739639397124&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 275
    The name of the city is TAZMALT
    https://api.openweathermap.org/data/2.5/weather?lat=35.08309232000036&lon=5.299671646063388&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 276
    The name of the city is SAO FILIPE
    https://api.openweathermap.org/data/2.5/weather?lat=10.545674687824402&lon=-31.15841299924128&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 277
    The name of the city is ILULISSAT
    https://api.openweathermap.org/data/2.5/weather?lat=71.1458528931683&lon=-50.44383544246054&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 278
    The name of the city is LOS LLANOS DE ARIDANE
    https://api.openweathermap.org/data/2.5/weather?lat=25.04190529475089&lon=-24.32070361241091&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 279
    The name of the city is MAUNABO
    https://api.openweathermap.org/data/2.5/weather?lat=17.666601378905654&lon=-65.66961155872661&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 280
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-80.51227484560856&lon=115.39417781943826&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 281
    The name of the city is ATUONA
    https://api.openweathermap.org/data/2.5/weather?lat=-13.390569712229365&lon=-136.1845795821109&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 282
    The name of the city is VAITUPU
    https://api.openweathermap.org/data/2.5/weather?lat=7.182559762629197&lon=-178.91340075993443&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 283
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-88.38014842519124&lon=-25.96103330257634&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 284
    The name of the city is YULARA
    https://api.openweathermap.org/data/2.5/weather?lat=-27.970819449928534&lon=130.650480692764&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 285
    The name of the city is OZERNOVSKIY
    https://api.openweathermap.org/data/2.5/weather?lat=51.6910473631028&lon=156.504797145527&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 286
    The name of the city is BELUSHYA GUBA
    https://api.openweathermap.org/data/2.5/weather?lat=73.88905175669547&lon=47.08500471737025&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 287
    The name of the city is SOUILLAC
    https://api.openweathermap.org/data/2.5/weather?lat=-36.047834294802925&lon=64.94525648661516&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 288
    The name of the city is KHATANGA
    https://api.openweathermap.org/data/2.5/weather?lat=84.43332895213774&lon=97.52817415856708&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 289
    The name of the city is FAIRBANKS
    https://api.openweathermap.org/data/2.5/weather?lat=70.90975178004524&lon=-145.32757432716227&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 290
    The name of the city is YELLOWKNIFE
    https://api.openweathermap.org/data/2.5/weather?lat=69.55695745531617&lon=-108.40312658440426&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 291
    The name of the city is MOCUBA
    https://api.openweathermap.org/data/2.5/weather?lat=-15.830036429523247&lon=38.45447886714865&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 292
    The name of the city is KRUISFONTEIN
    https://api.openweathermap.org/data/2.5/weather?lat=-64.61481585119569&lon=28.216789534442057&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 293
    The name of the city is VARDO
    https://api.openweathermap.org/data/2.5/weather?lat=86.19355584370993&lon=38.65131792217204&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 294
    The name of the city is TRES PICOS
    https://api.openweathermap.org/data/2.5/weather?lat=14.218971972760755&lon=-94.35937968875922&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 295
    The name of the city is PUNTA ARENAS
    https://api.openweathermap.org/data/2.5/weather?lat=-81.33364467873304&lon=-118.3897737536968&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 296
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-76.44024884650185&lon=65.13218008826146&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 297
    The name of the city is UPERNAVIK
    https://api.openweathermap.org/data/2.5/weather?lat=84.48858949670674&lon=-57.03828364939382&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 298
    The name of the city is MOOSE FACTORY
    https://api.openweathermap.org/data/2.5/weather?lat=58.33875337093431&lon=-75.76169547261861&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 299
    The name of the city is BOLUNGARVIK
    https://api.openweathermap.org/data/2.5/weather?lat=66.78934077757089&lon=-22.088514839205203&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 300
    The name of the city is BENGKULU
    https://api.openweathermap.org/data/2.5/weather?lat=-13.138957832674336&lon=94.66836536598078&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 301
    The name of the city is SAINT-PHILIPPE
    https://api.openweathermap.org/data/2.5/weather?lat=-58.63733111098808&lon=69.22068364101276&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 302
    The name of the city is PORT ALFRED
    https://api.openweathermap.org/data/2.5/weather?lat=-83.2555595294912&lon=48.3391899040256&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 303
    The name of the city is MARIDI
    https://api.openweathermap.org/data/2.5/weather?lat=4.5947953572120355&lon=30.02532059514425&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 304
    The name of the city is COAHUAYANA
    https://api.openweathermap.org/data/2.5/weather?lat=10.393338981380666&lon=-107.51999955643113&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 305
    The name of the city is CASTRO
    https://api.openweathermap.org/data/2.5/weather?lat=-46.010379558919674&lon=-106.54656813757782&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 306
    The name of the city is MATAURA
    https://api.openweathermap.org/data/2.5/weather?lat=-42.139337376764246&lon=-152.3432733297011&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 307
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-79.92895067754479&lon=165.1949758377084&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 308
    The name of the city is MALWAN
    https://api.openweathermap.org/data/2.5/weather?lat=15.033294515378344&lon=72.08097421571574&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 309
    The name of the city is MATANE
    https://api.openweathermap.org/data/2.5/weather?lat=49.41505800570178&lon=-67.46248306096662&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 310
    The name of the city is MAR DEL PLATA
    https://api.openweathermap.org/data/2.5/weather?lat=-46.69121650410289&lon=-50.07195752277312&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 311
    The name of the city is VAINI
    https://api.openweathermap.org/data/2.5/weather?lat=-72.60337950461732&lon=-178.84981928442483&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 312
    The name of the city is MATAURA
    https://api.openweathermap.org/data/2.5/weather?lat=-71.19779508126393&lon=-153.22446372583664&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 313
    The name of the city is VACARIA
    https://api.openweathermap.org/data/2.5/weather?lat=-28.331290214011915&lon=-50.651491906891636&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 314
    The name of the city is KARGASOK
    https://api.openweathermap.org/data/2.5/weather?lat=60.512635839700124&lon=80.91146664387566&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 315
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-33.59507240867666&lon=55.629491355569115&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 316
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-58.49086961781079&lon=56.50113539838304&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 317
    The name of the city is PASTAVY
    https://api.openweathermap.org/data/2.5/weather?lat=55.2960911251277&lon=26.98331388219438&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 318
    The name of the city is PUERTO AYACUCHO
    https://api.openweathermap.org/data/2.5/weather?lat=5.149375058222702&lon=-67.98919376003941&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 319
    The name of the city is PUNTA ARENAS
    https://api.openweathermap.org/data/2.5/weather?lat=-63.14597173820645&lon=-83.49694809475739&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 320
    The name of the city is PUNTA ARENAS
    https://api.openweathermap.org/data/2.5/weather?lat=-65.40747181171761&lon=-86.75944304961335&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 321
    The name of the city is ITAITUBA
    https://api.openweathermap.org/data/2.5/weather?lat=-5.187884257380659&lon=-54.16837759291144&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 322
    The name of the city is KIETA
    https://api.openweathermap.org/data/2.5/weather?lat=-1.9894388464438464&lon=157.98777567830132&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 323
    The name of the city is PUERTO AYORA
    https://api.openweathermap.org/data/2.5/weather?lat=4.497965529721839&lon=-94.43986439037769&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 324
    The name of the city is GRAVDAL
    https://api.openweathermap.org/data/2.5/weather?lat=69.7977501179324&lon=12.091759667160574&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 325
    The name of the city is PORT ALFRED
    https://api.openweathermap.org/data/2.5/weather?lat=-71.13096089044836&lon=41.824428923084895&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 326
    The name of the city is BOZEMAN
    https://api.openweathermap.org/data/2.5/weather?lat=44.825167301531394&lon=-111.56981850141607&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 327
    The name of the city is PUNTA ARENAS
    https://api.openweathermap.org/data/2.5/weather?lat=-85.35672558672803&lon=-117.133322053552&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 328
    The name of the city is BUARIKI
    https://api.openweathermap.org/data/2.5/weather?lat=1.9857625302185795&lon=170.0586842759394&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 329
    The name of the city is WARMBAD
    https://api.openweathermap.org/data/2.5/weather?lat=-28.729389318930494&lon=17.885656343671116&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 330
    The name of the city is MAGHAMA
    https://api.openweathermap.org/data/2.5/weather?lat=16.30259821258477&lon=-11.340976127610446&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 331
    The name of the city is NOVYY URENGOY
    https://api.openweathermap.org/data/2.5/weather?lat=65.405200956102&lon=75.96529126591761&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 332
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-20.77062739107383&lon=-1.410334320606296&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 333
    The name of the city is ZEYA
    https://api.openweathermap.org/data/2.5/weather?lat=54.21326581351477&lon=128.58002903741232&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 334
    The name of the city is YELLOWKNIFE
    https://api.openweathermap.org/data/2.5/weather?lat=81.96340751112692&lon=-112.91905054676289&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 335
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-79.11039838184765&lon=110.14601978462252&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 336
    The name of the city is VICTORIA
    https://api.openweathermap.org/data/2.5/weather?lat=-0.5607009281819444&lon=57.63359060328406&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 337
    The name of the city is UMZIMVUBU
    https://api.openweathermap.org/data/2.5/weather?lat=-37.91751303172858&lon=33.72681877085728&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 338
    The name of the city is KAMENKA
    https://api.openweathermap.org/data/2.5/weather?lat=69.98858673634655&lon=45.48598086578917&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 339
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-70.40573401909049&lon=-52.8533436234071&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 340
    The name of the city is KAPAA
    https://api.openweathermap.org/data/2.5/weather?lat=34.04774736259698&lon=-159.94905283264987&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 341
    The name of the city is ILLOQQORTOORMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=85.99207381848325&lon=-24.002961389430425&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 342
    The name of the city is CARNARVON
    https://api.openweathermap.org/data/2.5/weather?lat=-29.838940428116274&lon=98.72532204499669&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 343
    The name of the city is SOUILLAC
    https://api.openweathermap.org/data/2.5/weather?lat=-61.39398117056216&lon=77.86622413962255&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 344
    The name of the city is PORT ALFRED
    https://api.openweathermap.org/data/2.5/weather?lat=-74.78087582165006&lon=46.67049297282571&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 345
    The name of the city is OTRADNOYE
    https://api.openweathermap.org/data/2.5/weather?lat=44.53977661361566&lon=145.27633252760216&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 346
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-65.56154130558546&lon=124.90502199819514&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 347
    The name of the city is LAGOA
    https://api.openweathermap.org/data/2.5/weather?lat=44.239894994255366&lon=-28.697073531308405&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 348
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-78.52293887964747&lon=174.09674925347122&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 349
    The name of the city is PANIPAT
    https://api.openweathermap.org/data/2.5/weather?lat=29.321118660465956&lon=76.92520864354123&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 350
    The name of the city is MARKOVA
    https://api.openweathermap.org/data/2.5/weather?lat=56.7720880746528&lon=106.64493000107393&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 351
    The name of the city is YELLOWKNIFE
    https://api.openweathermap.org/data/2.5/weather?lat=89.33488826873358&lon=-105.65177999900317&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 352
    The name of the city is HEMSEDAL
    https://api.openweathermap.org/data/2.5/weather?lat=61.020521699367066&lon=8.555149679069302&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 353
    The name of the city is HILO
    https://api.openweathermap.org/data/2.5/weather?lat=21.244419346610186&lon=-142.63490359078332&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 354
    The name of the city is PAAMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=62.00444563775039&lon=-47.730411971439395&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 355
    The name of the city is NIKOLSKOYE
    https://api.openweathermap.org/data/2.5/weather?lat=30.705111514897027&lon=174.86770975073085&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 356
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-55.903211732838585&lon=-137.644759062695&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 357
    The name of the city is AKLAVIK
    https://api.openweathermap.org/data/2.5/weather?lat=68.02683740889665&lon=-134.75482488902864&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 358
    The name of the city is BARROW
    https://api.openweathermap.org/data/2.5/weather?lat=72.44889186791866&lon=-164.0265813629&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 359
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-50.16542576400993&lon=-13.253840745151393&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 360
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-73.53200194488241&lon=85.8857581579897&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 361
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-13.568418801509694&lon=-7.361548110838811&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 362
    The name of the city is MYS SHMIDTA
    https://api.openweathermap.org/data/2.5/weather?lat=88.77178681610235&lon=-176.84089738842033&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 363
    The name of the city is MZIMBA
    https://api.openweathermap.org/data/2.5/weather?lat=-12.321654028954597&lon=33.79843264272699&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 364
    The name of the city is SHURUGWI
    https://api.openweathermap.org/data/2.5/weather?lat=-19.962049028677583&lon=29.718647640933284&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 365
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-41.04313424719352&lon=105.80794275526432&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 366
    The name of the city is LAGOA
    https://api.openweathermap.org/data/2.5/weather?lat=44.010880883909124&lon=-28.55790447518777&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 367
    The name of the city is MATAURA
    https://api.openweathermap.org/data/2.5/weather?lat=-61.8467016984166&lon=-151.79463934290612&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 368
    The name of the city is ERENHOT
    https://api.openweathermap.org/data/2.5/weather?lat=43.11883468334955&lon=109.4436972546315&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 369
    The name of the city is KAHULUI
    https://api.openweathermap.org/data/2.5/weather?lat=32.26624637818348&lon=-147.02231184258216&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 370
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-29.930013012581405&lon=-1.1906647281266203&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 371
    The name of the city is FORTUNA
    https://api.openweathermap.org/data/2.5/weather?lat=35.416608656218074&lon=-132.02649255260144&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 372
    The name of the city is HARWICH
    https://api.openweathermap.org/data/2.5/weather?lat=41.615730560547235&lon=-68.47925662744412&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 373
    The name of the city is HILO
    https://api.openweathermap.org/data/2.5/weather?lat=12.729020105167933&lon=-136.0931460514696&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 374
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-35.508557782999254&lon=-11.251314848283641&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 375
    The name of the city is KAHULUI
    https://api.openweathermap.org/data/2.5/weather?lat=25.901269301699074&lon=-152.67109060668145&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 376
    The name of the city is THOMPSON
    https://api.openweathermap.org/data/2.5/weather?lat=56.411654101309125&lon=-96.454653474668&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 377
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-53.07271020134088&lon=121.47909830823471&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 378
    The name of the city is HOQUIAM
    https://api.openweathermap.org/data/2.5/weather?lat=46.831337705772285&lon=-125.36019730517027&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 379
    The name of the city is ESPERANCE
    https://api.openweathermap.org/data/2.5/weather?lat=-31.48638804620203&lon=123.67766002429073&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 380
    The name of the city is BONOUA
    https://api.openweathermap.org/data/2.5/weather?lat=5.056930190669675&lon=-3.5394920702961485&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 381
    The name of the city is LOUISBOURG
    https://api.openweathermap.org/data/2.5/weather?lat=38.380957213651755&lon=-58.51121086467418&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 382
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-51.76611340133496&lon=-115.66855313787552&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 383
    The name of the city is RUSSELL
    https://api.openweathermap.org/data/2.5/weather?lat=-27.776285720094116&lon=179.74807499756702&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 384
    The name of the city is HAY RIVER
    https://api.openweathermap.org/data/2.5/weather?lat=58.73313038412758&lon=-112.3777947874116&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 385
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-50.7383043310975&lon=102.38890386200336&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 386
    The name of the city is PROVIDENIYA
    https://api.openweathermap.org/data/2.5/weather?lat=57.3454687735113&lon=-169.5189518926326&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 387
    The name of the city is AHIPARA
    https://api.openweathermap.org/data/2.5/weather?lat=-32.91096767551597&lon=168.34400330768494&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 388
    The name of the city is ESPERANCE
    https://api.openweathermap.org/data/2.5/weather?lat=-40.89098871215908&lon=124.51874050241167&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 389
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-80.6532620231417&lon=135.5315825218293&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 390
    The name of the city is GRAND RIVER SOUTH EAST
    https://api.openweathermap.org/data/2.5/weather?lat=-19.839077345114106&lon=63.18026157216076&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 391
    The name of the city is SUR
    https://api.openweathermap.org/data/2.5/weather?lat=21.709964624400527&lon=59.56873936371696&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 392
    The name of the city is KODIAK
    https://api.openweathermap.org/data/2.5/weather?lat=41.065114088259236&lon=-158.3343836625897&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 393
    The name of the city is SAINT-PHILIPPE
    https://api.openweathermap.org/data/2.5/weather?lat=-72.88251806567669&lon=72.28318362538727&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 394
    The name of the city is ANEHO
    https://api.openweathermap.org/data/2.5/weather?lat=5.571299332477295&lon=1.6493722430279831&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 395
    The name of the city is NIZHNEYANSK
    https://api.openweathermap.org/data/2.5/weather?lat=81.07898441376253&lon=133.18299150640894&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 396
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-86.4933026246671&lon=-81.8377898211361&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 397
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-65.54422210804951&lon=103.75858022209269&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 398
    The name of the city is LITHAKIA
    https://api.openweathermap.org/data/2.5/weather?lat=36.41049943646938&lon=19.863035329077093&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 399
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-75.08246018382123&lon=-45.66947597431039&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 400
    The name of the city is HAVOYSUND
    https://api.openweathermap.org/data/2.5/weather?lat=72.84411760694567&lon=23.75248847882645&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 401
    The name of the city is HERMANUS
    https://api.openweathermap.org/data/2.5/weather?lat=-73.89208600477374&lon=-1.5206219984247298&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 402
    The name of the city is NORMAN WELLS
    https://api.openweathermap.org/data/2.5/weather?lat=62.85258205650371&lon=-128.51366914410374&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 403
    The name of the city is YUMEN
    https://api.openweathermap.org/data/2.5/weather?lat=38.30573422350926&lon=94.68529330973041&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 404
    The name of the city is CHERSKIY
    https://api.openweathermap.org/data/2.5/weather?lat=77.78875871178556&lon=159.38382733760147&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 405
    The name of the city is RIKITEA
    https://api.openweathermap.org/data/2.5/weather?lat=-46.63574756902742&lon=-124.44401491904543&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 406
    The name of the city is TASIILAQ
    https://api.openweathermap.org/data/2.5/weather?lat=79.15316950091574&lon=-35.13775933389584&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 407
    The name of the city is OSTROVNOY
    https://api.openweathermap.org/data/2.5/weather?lat=77.93660175497445&lon=43.509298261053914&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 408
    The name of the city is NHULUNBUY
    https://api.openweathermap.org/data/2.5/weather?lat=-11.929637812404223&lon=136.84524671747653&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 409
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-70.73854048012684&lon=114.50970320450887&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 410
    The name of the city is DONGSHENG
    https://api.openweathermap.org/data/2.5/weather?lat=38.99806832227182&lon=109.06533292190272&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 411
    The name of the city is MACAMIC
    https://api.openweathermap.org/data/2.5/weather?lat=48.24118305034614&lon=-79.10973770795296&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 412
    The name of the city is MEHAMN
    https://api.openweathermap.org/data/2.5/weather?lat=82.22308769938417&lon=29.664525303214646&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 413
    The name of the city is CLYDE RIVER
    https://api.openweathermap.org/data/2.5/weather?lat=71.0360264897854&lon=-79.38226913768548&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 414
    The name of the city is SEVERNOYE
    https://api.openweathermap.org/data/2.5/weather?lat=56.65141722030049&lon=77.82892227627258&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 415
    The name of the city is CHICAMA
    https://api.openweathermap.org/data/2.5/weather?lat=-8.83529158161474&lon=-82.00858136275454&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 416
    The name of the city is SAMUSU
    https://api.openweathermap.org/data/2.5/weather?lat=-1.7477064051406757&lon=-162.04821481733137&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 417
    The name of the city is GEORGETOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-2.751059784341294&lon=-17.706813840757235&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 418
    The name of the city is BELOHA
    https://api.openweathermap.org/data/2.5/weather?lat=-32.3408335407497&lon=41.09463484926482&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 419
    The name of the city is EUREKA
    https://api.openweathermap.org/data/2.5/weather?lat=40.75438709124097&lon=-137.7198942982891&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 420
    The name of the city is YELLOWKNIFE
    https://api.openweathermap.org/data/2.5/weather?lat=86.74636559270968&lon=-115.28030187125866&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 421
    The name of the city is LABUHAN
    https://api.openweathermap.org/data/2.5/weather?lat=-10.96707044802244&lon=101.12928062987748&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 422
    The name of the city is TABIAUEA
    https://api.openweathermap.org/data/2.5/weather?lat=-1.8923813447318025&lon=168.4317359569585&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 423
    The name of the city is VILA FRANCA DO CAMPO
    https://api.openweathermap.org/data/2.5/weather?lat=37.47380287248856&lon=-21.347165536962905&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 424
    The name of the city is VICTORIA
    https://api.openweathermap.org/data/2.5/weather?lat=-8.400247362638325&lon=56.48708159038284&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 425
    The name of the city is LES ESCOUMINS
    https://api.openweathermap.org/data/2.5/weather?lat=48.86861553099283&lon=-69.76369655880475&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 426
    The name of the city is VESTMANNAEYJAR
    https://api.openweathermap.org/data/2.5/weather?lat=57.025097790207894&lon=-18.828647140206385&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 427
    The name of the city is ASAU
    https://api.openweathermap.org/data/2.5/weather?lat=-14.284851246152101&lon=179.05126864139845&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 428
    The name of the city is BUALA
    https://api.openweathermap.org/data/2.5/weather?lat=-2.9425551245341666&lon=161.53663309978424&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 429
    The name of the city is MAR DEL PLATA
    https://api.openweathermap.org/data/2.5/weather?lat=-68.5559164300776&lon=-25.411126258180275&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 430
    The name of the city is NIZHNEYANSK
    https://api.openweathermap.org/data/2.5/weather?lat=86.7797208600299&lon=138.34504978723663&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 431
    The name of the city is KRUISFONTEIN
    https://api.openweathermap.org/data/2.5/weather?lat=-86.63428225281078&lon=30.306852431299404&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 432
    The name of the city is NIKOLSKOYE
    https://api.openweathermap.org/data/2.5/weather?lat=49.29842904617823&lon=164.57333685814598&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 433
    The name of the city is KAPUTA
    https://api.openweathermap.org/data/2.5/weather?lat=-8.156523797367413&lon=29.268604678549167&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 434
    The name of the city is AGUIMES
    https://api.openweathermap.org/data/2.5/weather?lat=25.086596569165025&lon=-14.785354900878815&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 435
    The name of the city is CARNARVON
    https://api.openweathermap.org/data/2.5/weather?lat=-32.884566059328634&lon=88.16053353509449&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 436
    The name of the city is BLUFF
    https://api.openweathermap.org/data/2.5/weather?lat=-58.42602552634884&lon=166.66961769272194&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 437
    The name of the city is ANDENES
    https://api.openweathermap.org/data/2.5/weather?lat=71.93557131305218&lon=13.887642641800113&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 438
    The name of the city is ELAT
    https://api.openweathermap.org/data/2.5/weather?lat=29.212830484132184&lon=34.126479624668775&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 439
    The name of the city is VAINI
    https://api.openweathermap.org/data/2.5/weather?lat=-86.57098544721947&lon=-175.5857737105819&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 440
    The name of the city is TASIILAQ
    https://api.openweathermap.org/data/2.5/weather?lat=87.10803879351138&lon=-36.67761936157541&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 441
    The name of the city is OSTROVNOY
    https://api.openweathermap.org/data/2.5/weather?lat=75.8777421312125&lon=39.98207124722066&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 442
    The name of the city is MATAURA
    https://api.openweathermap.org/data/2.5/weather?lat=-80.29707196720993&lon=-159.34843295836947&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 443
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-87.49544272373997&lon=63.43365644884764&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 444
    The name of the city is TASIILAQ
    https://api.openweathermap.org/data/2.5/weather?lat=65.6140229111032&lon=-38.13281848027631&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 445
    The name of the city is UGOOFAARU
    https://api.openweathermap.org/data/2.5/weather?lat=5.114056201565006&lon=63.172374040914235&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 446
    The name of the city is PAAMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=64.25306517809892&lon=-48.703617980376066&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 447
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-89.40978203277975&lon=-68.45927570124516&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 448
    The name of the city is BENGKULU
    https://api.openweathermap.org/data/2.5/weather?lat=-8.609491882709193&lon=92.29645928210778&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 449
    The name of the city is SAINT GEORGE
    https://api.openweathermap.org/data/2.5/weather?lat=36.15270954671912&lon=-64.54554983077797&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 450
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-8.193015936920744&lon=-1.8095429828725287&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 451
    The name of the city is ALBANY
    https://api.openweathermap.org/data/2.5/weather?lat=-88.87127229458137&lon=93.01787842763673&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 452
    The name of the city is CHUY
    https://api.openweathermap.org/data/2.5/weather?lat=-35.41030510314841&lon=-51.37668316490456&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 453
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-65.46864112326186&lon=63.167812291894506&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 454
    The name of the city is IBB
    https://api.openweathermap.org/data/2.5/weather?lat=13.870240439118646&lon=44.57723254048622&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 455
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-69.84941314826017&lon=-44.46882209492193&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 456
    The name of the city is PUNTA ARENAS
    https://api.openweathermap.org/data/2.5/weather?lat=-70.28366474133037&lon=-80.82563097924253&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 457
    The name of the city is VANIMO
    https://api.openweathermap.org/data/2.5/weather?lat=3.1612247901262407&lon=143.09771587609993&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 458
    The name of the city is MYS SHMIDTA
    https://api.openweathermap.org/data/2.5/weather?lat=78.26095073853207&lon=-178.827579604176&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 459
    The name of the city is AIRAI
    https://api.openweathermap.org/data/2.5/weather?lat=13.570755439907913&lon=146.76956281825187&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 460
    The name of the city is KAPAA
    https://api.openweathermap.org/data/2.5/weather?lat=38.37221142615019&lon=-173.93661293575468&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 461
    The name of the city is TAOLANARO
    https://api.openweathermap.org/data/2.5/weather?lat=-57.066941354618365&lon=55.6454411295056&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 462
    The name of the city is WARQLA
    https://api.openweathermap.org/data/2.5/weather?lat=31.09105348614824&lon=6.4300328399638715&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 463
    The name of the city is TSENTRALNYY
    https://api.openweathermap.org/data/2.5/weather?lat=55.40147745437784&lon=87.81897086062327&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 464
    The name of the city is PONTA DO SOL
    https://api.openweathermap.org/data/2.5/weather?lat=27.047454010857365&lon=-28.731485377701176&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 465
    The name of the city is YENAGOA
    https://api.openweathermap.org/data/2.5/weather?lat=3.162785882300369&lon=4.9473183971904575&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 466
    The name of the city is HILO
    https://api.openweathermap.org/data/2.5/weather?lat=3.304367461991461&lon=-163.86696220856078&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 467
    The name of the city is MARQUETTE
    https://api.openweathermap.org/data/2.5/weather?lat=46.16587687106343&lon=-88.28289341438341&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 468
    The name of the city is VAINI
    https://api.openweathermap.org/data/2.5/weather?lat=-35.42136953177959&lon=-172.2402243448948&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 469
    The name of the city is NYERI
    https://api.openweathermap.org/data/2.5/weather?lat=-0.45851480243851483&lon=37.05798016048686&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 470
    The name of the city is BALLINA
    https://api.openweathermap.org/data/2.5/weather?lat=56.066234201260215&lon=-10.290282585037062&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 471
    The name of the city is LEXINGTON
    https://api.openweathermap.org/data/2.5/weather?lat=40.34399003775428&lon=-100.24719421813352&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 472
    The name of the city is AVARUA
    https://api.openweathermap.org/data/2.5/weather?lat=-56.46267994664945&lon=-163.17985642544755&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 473
    The name of the city is PORTLAND
    https://api.openweathermap.org/data/2.5/weather?lat=-53.22031035791668&lon=133.116195829815&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 474
    The name of the city is XAM NUA
    https://api.openweathermap.org/data/2.5/weather?lat=20.919217711523757&lon=104.31621949993018&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 475
    The name of the city is LOMPOC
    https://api.openweathermap.org/data/2.5/weather?lat=30.372272678047537&lon=-126.43948113416538&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 476
    The name of the city is ODWEYNE
    https://api.openweathermap.org/data/2.5/weather?lat=9.497218661579765&lon=44.86330198007042&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 477
    The name of the city is BUSSELTON
    https://api.openweathermap.org/data/2.5/weather?lat=-64.05520286909022&lon=91.35709998636264&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 478
    The name of the city is WADDAN
    https://api.openweathermap.org/data/2.5/weather?lat=27.91408924321256&lon=18.15564486639647&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 479
    The name of the city is VILA FRANCA DO CAMPO
    https://api.openweathermap.org/data/2.5/weather?lat=35.21139684790842&lon=-23.81043810962757&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 480
    The name of the city is PORT LINCOLN
    https://api.openweathermap.org/data/2.5/weather?lat=-38.07018889813178&lon=133.42645791223606&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 481
    The name of the city is HERMANUS
    https://api.openweathermap.org/data/2.5/weather?lat=-61.315789839375554&lon=10.676393108239921&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 482
    The name of the city is SHIMODA
    https://api.openweathermap.org/data/2.5/weather?lat=31.762318448825013&lon=140.02926922346364&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 483
    The name of the city is OLDS
    https://api.openweathermap.org/data/2.5/weather?lat=51.83493382105274&lon=-114.04029397575847&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 484
    The name of the city is THOMPSON
    https://api.openweathermap.org/data/2.5/weather?lat=67.68754769219163&lon=-97.41040884230571&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 485
    The name of the city is USHUAIA
    https://api.openweathermap.org/data/2.5/weather?lat=-61.61307490042368&lon=-51.18707190025512&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 486
    The name of the city is JAMESTOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-12.01601864515385&lon=-0.5652830357119853&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 487
    The name of the city is ILLOQQORTOORMIUT
    https://api.openweathermap.org/data/2.5/weather?lat=83.57396648412251&lon=-10.336805383252226&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 488
    The name of the city is HAVRE-SAINT-PIERRE
    https://api.openweathermap.org/data/2.5/weather?lat=50.167102789829954&lon=-62.48867483094715&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 489
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-64.81359463784287&lon=143.68751255314004&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 490
    The name of the city is FAANUI
    https://api.openweathermap.org/data/2.5/weather?lat=-7.4252676045823165&lon=-149.33800893466193&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 491
    The name of the city is ATTAWAPISKAT
    https://api.openweathermap.org/data/2.5/weather?lat=57.58632117691613&lon=-77.73307828776568&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 492
    The name of the city is HOBART
    https://api.openweathermap.org/data/2.5/weather?lat=-44.99893096790557&lon=146.82276530472205&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 493
    The name of the city is MATAGAMI
    https://api.openweathermap.org/data/2.5/weather?lat=52.29311504364264&lon=-78.14155594682846&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 494
    The name of the city is VICTORIA
    https://api.openweathermap.org/data/2.5/weather?lat=-2.5608919218079222&lon=56.79622092110242&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 495
    The name of the city is DANGRIGA
    https://api.openweathermap.org/data/2.5/weather?lat=16.85556227990419&lon=-87.48496597619146&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 496
    The name of the city is CAPE TOWN
    https://api.openweathermap.org/data/2.5/weather?lat=-48.74796374927064&lon=-5.578683972622144&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 497
    The name of the city is VARDO
    https://api.openweathermap.org/data/2.5/weather?lat=86.59766652437256&lon=35.37428122849326&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 498
    The name of the city is JUMLA
    https://api.openweathermap.org/data/2.5/weather?lat=31.06732804617647&lon=83.13326977063349&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 499
    The name of the city is TSIHOMBE
    https://api.openweathermap.org/data/2.5/weather?lat=-33.013911201329876&lon=44.0456674239351&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------
    This is request number 500
    The name of the city is SAINT-PHILIPPE
    https://api.openweathermap.org/data/2.5/weather?lat=-40.46832527024079&lon=64.53734725480973&appid=dc420b4243492d00f3e5909765aa462e&units=imperial
    ---------------------------------------



```python
#display DataFrame with data gathered from OpenWeatherMap
data.head()
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
      <th>Long</th>
      <th>City</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Clouds</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>36.840030</td>
      <td>-138.326655</td>
      <td>fortuna</td>
      <td>54.40</td>
      <td>100</td>
      <td>92</td>
      <td>22.73</td>
    </tr>
    <tr>
      <th>1</th>
      <td>19.417284</td>
      <td>-168.866576</td>
      <td>kapaa</td>
      <td>76.45</td>
      <td>100</td>
      <td>56</td>
      <td>18.81</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-37.299821</td>
      <td>-18.049731</td>
      <td>jamestown</td>
      <td>69.07</td>
      <td>98</td>
      <td>92</td>
      <td>7.18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>79.278860</td>
      <td>-83.418560</td>
      <td>qaanaaq</td>
      <td>-30.20</td>
      <td>49</td>
      <td>24</td>
      <td>2.15</td>
    </tr>
    <tr>
      <th>4</th>
      <td>62.560630</td>
      <td>147.222472</td>
      <td>myaundzha</td>
      <td>-7.07</td>
      <td>68</td>
      <td>64</td>
      <td>3.15</td>
    </tr>
  </tbody>
</table>
</div>



### DataFrame Ouput


```python
# Save the DataFrame as a csv
data.to_csv("output_analysis/city_weather_data.csv", encoding="utf-8", index=False)
```

### Latitude vs Temperature Plot


```python
plt.scatter(data["Lat"], data["Temperature"], marker = "o")
plt.title("City Latitude vs Max Temperature (7 Mar 2018)")
plt.xlabel("Latitude")
plt.ylabel("Temperature (F)")
plt.grid()
plt.xlim(-99, 99 )
plt.ylim(min(data["Temperature"]), max(data["Temperature"]))

plt.savefig("output_analysis/latitdue_temperature_plot.png")

plt.show()
```


![png](output_10_0.png)


### Latitude vs Humidity Plot


```python
plt.scatter(data["Lat"], data["Humidity"], marker = "o")
plt.title("City Latitude vs Humidity (7 Mar 2018)")
plt.xlabel("Latitude")
plt.ylabel("Humidity (&)")
plt.grid()
plt.xlim(-99, 99 )
plt.ylim(-10, 110)

plt.savefig("output_analysis/latitdue_humidity_plot.png")

plt.show()
```


![png](output_12_0.png)


### Latitude vs Cloudiness Plot


```python
plt.scatter(data["Lat"], data["Clouds"], marker = "o")
plt.title("City Latitude vs Cloudiness (7 Mar 2018)")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.grid()
plt.xlim(-99, 99 )
plt.ylim(-10, 110)

plt.savefig("output_analysis/latitdue_cloudiness_plot.png")

plt.show()
```


![png](output_14_0.png)


### Latitude vs Wind Speed Plot


```python
plt.scatter(data["Lat"], data["Wind Speed"], marker = "o")
plt.title("City Latitude vs Wind Speed (7 Mar 2018)")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")
plt.grid()
plt.xlim(-99, 99 )
plt.ylim(-3, max(data["Wind Speed"]))

plt.savefig("output_analysis/latitdue_wind_plot.png")

plt.show()
```


![png](output_16_0.png)

