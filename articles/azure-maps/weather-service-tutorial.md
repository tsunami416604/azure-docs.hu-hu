---
title: 'Oktatóanyag: az érzékelő adataihoz való csatlakozás időjárás-előrejelzési adataival Azure Notebooks (Python) használatával | Microsoft Docs'
description: 'Oktatóanyag: ez az oktatóanyag bemutatja, hogyan csatlakoztathatja az érzékelő adatait Azure Maps időjárási szolgáltatásból származó időjárás-előrejelzési adatokkal Azure Notebooks (Python) használatával.'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 809c188dc37aba64de27e89e38acd8692c7de032
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613567"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Oktatóanyag: az érzékelő adataihoz való csatlakozás időjárás-előrejelzési adataival Azure Notebooks (Python) használatával

A szélenergia egy alternatív energiaforrás a fosszilis tüzelőanyagok számára a klímaváltozás elleni küzdelem érdekében. Mivel a szél nem konzisztens a természettel, a szélenergia-kezelőknek ML (gépi tanulási) modelleket kell kiépíteniük a szélenergia-kapacitás megbecsléséhez, hogy megfeleljenek a villamosenergia-igényeknek, és biztosítsuk a hálózat stabilitását. Ebben az oktatóanyagban bemutatjuk, hogyan lehet a Azure Maps időjárás-előrejelzési adatmennyiséget kombinálni az érzékelő helyeinek az időjárási beolvasással rendelkező bemutató-adatkészletével. A Azure Maps időjárási szolgáltatás meghívásával az időjárás-előrejelzési adatkérést kéri a rendszer.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * A felhőben [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) adatfájlokkal dolgozhat.
> * Betöltheti a bemutató adatait a fájlból.
> * Azure Maps REST API-k hívása a Pythonban.
> * Helyadatok leképezése a térképen.
> * Azure Maps [napi előrejelzési](https://aka.ms/AzureMapsWeatherDailyForecast) időjárási adattal gazdagíthatja a bemutatókat.
> * Előrejelzési adatdiagramok ábrázolása.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez először a következőket kell tennie:

1. Hozzon létre egy Azure Maps fiók-előfizetést a S0 díjszabási szinten a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps)című témakör utasításait követve.
2. Szerezze be a fiókjához tartozó elsődleges előfizetési kulcsot, kövesse az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account)című témakör utasításait.


A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

Az Azure-jegyzetfüzetek megismeréséhez és az első lépésekhez kövesse az [Azure notebook létrehozása](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)című témakör utasításait.

> [!Note]
> A projekthez tartozó Jupyter notebook-fájl az [időjárási térképek Jupyter notebook-tárházból](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)tölthető le.

## <a name="load-the-required-modules-and-frameworks"></a>A szükséges modulok és keretrendszerek betöltése

Az összes szükséges modul és keretrendszer betöltéséhez futtassa a következő parancsfájlt:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Időjárási adatimportálás

Ennek az oktatóanyagnak az elvégzése érdekében a négy különböző szélturbinák esetében telepített érzékelőkből származó időjárási adatok beolvasását fogjuk használni. A mintaadatok 30 napos időjárási adatokból állnak, és az egyes turbina-helyek közelében az időjárási adatközpontokból gyűjtött adatok. A bemutatóban szereplő adatolvasások a hőmérséklet, a szélsebesség és a irányában szerepelnek. A bemutató [adatait innen töltheti le.](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data) Az alábbi szkript a bemutatót az Azure jegyzetfüzetbe importálja.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Napi előrejelzési adatgyűjtés kérése

Példánkban az egyes érzékelők helyszínei esetében napi előrejelzést szeretnénk igényelni. Az alábbi szkript meghívja a Azure Maps időjárási szolgáltatás [napi előrejelzési API](https://aka.ms/AzureMapsWeatherDailyForecast) -ját, amely az aktuális dátumtól számított 15 napon belül napi időjárási előrejelzést biztosít az egyes szélturbinák esetében.


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

Az alábbi parancsfájl a turbina-helyet a térképen jeleníti meg a Azure Maps [Térkép rendszerkép-szolgáltatásának](https://docs.microsoft.com/rest/api/maps/render/getmapimage)meghívásával.

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

![Turbina-helyszínek](./media/weather-service-tutorial/location-map.png)


Ahhoz, hogy a bemutatót az előrejelzési adattal bővítse, az előrejelzési adatközpontot az állomás azonosítója alapján csoportosítjuk a bemutatóba.

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

A következő táblázat az egyik turbina-helyszín összesített előzmény-és előrejelzési adatait jeleníti meg.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![csoportosított adat](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Előrejelzési adatgyűjtés ábrázolása

Ha szeretné megtekinteni, hogy a szél sebessége és iránya hogyan változik a következő 15 nap során, az előre jelzett értékeket azon napokra fogjuk kimutatni, amelyeknek az előrejelzését elvártuk.

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

Az alábbi grafikonok az adatok kérésének napjától számított 15 napon belül megjelenítik a szél sebességének (bal gráf) és irányának (jobb gráf) változásának előrejelzési adatait.

<center>

![szél sebessége](./media/weather-service-tutorial/speed-date-plot.png) ![szél irányának ábrázolása](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hívhat meg Azure Maps REST API-kat az időjárási előrejelzési adatok beszerzéséhez és a diagramokon tárolt adatok megjelenítéséhez.

Ha többet szeretne megtudni arról, hogyan hívhat Azure Maps REST API-kat Azure Notebookson belül, tekintse meg az [EV-útválasztást a Azure Notebooks használatával](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Az oktatóanyagban használt Azure Maps API-k megismeréséhez tekintse meg a következőt:

* [Napi előrejelzés](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render-Térkép képe](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Azure Maps REST API-k teljes listájáért lásd: [Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/consumption-model)-k.

További információ a Azure Notebooksről: [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
