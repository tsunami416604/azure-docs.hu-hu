---
title: 'Oktatóanyag: Az érzékelőadatok összehívása az időjárás-előrejelzési adatokkal az Azure Notebooks(Python) használatával | Microsoft Azure Maps'
description: Ez az oktatóanyag bemutatja, hogyan illesztheti össze az érzékelőadatokat a Microsoft Azure Maps időjárás-előrejelzési adataival az Azure Notebooks(Python) használatával.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e5292f5166e739264e9cf969480b70f415fcc75a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333496"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Oktatóanyag: Az érzékelőadatok összekapcsolódása az időjárás-előrejelzési adatokkal az Azure Notebooks (Python) használatával

Szélenergia egyik alternatív energiaforrás a fosszilis tüzelőanyagok elleni küzdelem az éghajlatváltozás. Mivel a szél természeténél fogva nem konzisztens, a szélenergia-üzemeltetőknek gépi tanulási (ML) modelleket kell készíteniük a szélenergia-kapacitás előrejelzéséhez. Ez az előrejelzés a villamosenergia-kereslet kielégítéséhez és a hálózat stabilitásának biztosításához szükséges. Ebben az oktatóanyagban bemutatjuk, hogyan kapcsolódik az Azure Maps időjárás-előrejelzési adatai az időjárási értékek bemutatóadataihoz. Az időjárás-előrejelzési adatokat az Azure Maps Időjárás szolgáltatás ának hívása kéri.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adatfájlok at [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) a felhőben.
> * Demóadatok betöltése fájlból.
> * Hívja meg az Azure Maps REST API-kat a Pythonban.
> * Helyadatok renderelése a térképen.
> * Gazdagítsa a bemutató adatokat az Azure Maps [napi előrejelzési](https://aka.ms/AzureMapsWeatherDailyForecast) időjárási adataival.
> * Előrejelzési adatok nyomtatása grafikonokon.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez először a következőket kell elvégeznie:

1. Hozzon létre egy Azure Maps-fiók-előfizetést az S0-díjszabási csomagban a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps)című útmutató utasításainak követésével.
2. A fiók elsődleges előfizetési kulcsának beszerezése, kövesse az [elsődleges kulcs bekésezése](quick-demo-map-app.md#get-the-primary-key-for-your-account)című útmutatóutasításait.


Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.

Az Azure-jegyzetfüzetek megismeréséhez és az első lépésekhez kövesse az [Azure-jegyzetfüzet létrehozása](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)című utasításokat.

> [!Note]
> A Jupyter notebook fájl ehhez a projekthez letölthető a [Weather Maps Jupyter notebook repository](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Töltse be a szükséges modulokat és keretrendszereket

Az összes szükséges modul és keretrendszer betöltéséhez futtassa a következő parancsfájlt:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Időjárási adatok importálása

A bemutató kedvéért négy különböző szélturbinára telepített érzékelők időjárási adatleolvasásait fogjuk használni. A mintaadatok 30 napos időjárási értékekből állnak. Ezek az értékek az egyes turbina-helyeken található időjárási adatközpontokból kerülnek összegyűjtésre. A demó adatok a hőmérséklet, a szélsebesség és az irány adatleolvasását tartalmazzák. Letöltheti a demo adatokat [innen](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). Az alábbi parancsfájl importálja a bemutató adatokat az Azure Notebook.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Napi előrejelzési adatok kérése

A mi forgatókönyvünkben napi előrejelzést szeretnénk kérni az egyes érzékelők helyéről. A következő parancsfájl meghívja az Azure [Maps időjárás-szolgáltatás napi előrejelzési API-ját.](https://aka.ms/AzureMapsWeatherDailyForecast) Ez az API az aktuális dátumtól számított 15 napra vonatkozóan az egyes szélturbinák időjárás-előrejelzését adja vissza.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Az alábbi parancsfájl az Azure Maps Get Map Image szolgáltatás felhívásával jeleníti meg a turbinahelyeket a [térképen.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Turbina helyszínek](./media/weather-service-tutorial/location-map.png)


Az előrejelzési adatokat az állomásazonosító alapján csoportosítjuk a bemutató adatokkal. Az állomás azonosítója az időjárási adatközponthoz kell tart. Ez a csoportosítás növeli a bemutató adatokat az előrejelzési adatokkal.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

Az alábbi táblázat az egyik turbinahely összesített előzmény- és előrejelzési adatait jeleníti meg.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Csoportosított adatok](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Előrejelzési adatok nyomtatása

Az előre jelzett értékeket az előre jelzett napokhoz megyünk. Ez a telek lehetővé teszi számunkra, hogy a sebesség és irányváltozás a szél a következő 15 napban.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Az alábbi grafikonok megjelenítik az előrejelzési adatokat. A változás a szél sebessége, lásd a bal oldali grafikonon. A szél irányának megváltoztatásához lásd a megfelelő grafikont. Ezek az adatok az adatok kérésének napjától a következő 15 napra vonatkozó előrejelzést kapnak.

<center>

![Szélsebesség](./media/weather-service-tutorial/speed-date-plot.png) ![telek Szél iránya telek](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hívhatja meg az Azure Maps REST API-kat az időjárás-előrejelzési adatok lehívásához. Azt is megtanulta, hogyan jelenítheti meg az adatokat a grafikonokon.

Ha többet szeretne tudni arról, hogyan hívhatja meg az Azure Maps REST API-kat az Azure-jegyzetfüzeteken belül, olvassa el [az Azure Notebookok használatával történő ev-útválasztást.](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing)

Az ebben az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [Napi előrejelzés](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Renderelés – Térképkép bekése](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Az Azure Maps REST API-k teljes listáját az [Azure Maps REST API-k ban.](https://docs.microsoft.com/azure/azure-maps/consumption-model)

Az Azure-jegyzetfüzetekről az [Azure-jegyzetfüzetek](https://docs.microsoft.com/azure/notebooks)ről olvashat bővebben.
