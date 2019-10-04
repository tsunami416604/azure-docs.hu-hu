---
title: Elektromos járművek útválasztása Azure Notebooks használatával (Python) | Microsoft Docs
description: EV-útválasztás Azure Maps útválasztási API-k és Azure Notebooks használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c4b46bc952782fc7c9b56d6f0c049fe17b63d0f2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836357"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Elektromos járművek útválasztása Azure Notebooks használatával (Python)

A Azure Maps a térinformatikai szolgáltatási API-k natívan integrált portfoliója, amely lehetővé teszi a fejlesztők, a vállalatok és az ISV-k számára, hogy helyet IoT, mobilitási, logisztikai és Asset Tracking-megoldásokat hozzanak létre. A Azure Maps REST API-kat olyan nyelvekről hívhatjuk, mint például a Python és az R a térinformatikai adatok elemzéséhez és a gépi tanulási forgatókönyvekhez. Azure Maps az [útválasztási API](https://docs.microsoft.com/rest/api/maps/route) -k robusztus készletét kínálja, amely lehetővé teszi, hogy a felhasználók különböző feltételek (például járműtípus vagy elérhető terület) alapján számítsanak ki útvonalakat több adatpont között. Ebben az oktatóanyagban egy olyan forgatókönyvet ismertetünk, amely segít egy olyan elektromos jármű-illesztőprogram létrehozásában, amelynek a járművei alacsony töltöttségi szinten vannak, hogy megtalálják a lehető legközelebb töltő állomást a meghajtó időpontjára vonatkozóan.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Jupyter Notebook létrehozása és futtatása a felhőben [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)
> * Azure Maps REST API-k hívása Pythonban
> * Keressen egy elérhető tartományt az elektromos jármű használati modellje alapján.
> * Keressen az elektromos járművek díjszabási állomásait a rendelkezésre álló tartományon belül (vagy isochrone).
> * A elérhető tartomány határának és a díjszabási állomások leképezése
> * Megkeresheti és megjelenítheti az útvonalat a legközelebbi elektromos jármű töltőállomásokon az idő függvényében.


## <a name="prerequisites"></a>Előfeltételek 

Az oktatóanyag lépéseinek elvégzéséhez először létre kell hoznia egy Azure Maps fiókot, és le kell kérnie az elsődleges kulcsot (előfizetési kulcs). Kövesse a [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) Azure Maps fiók előfizetése S1 árképzési szinten való létrehozásához című témakör útmutatását, és kövesse az [elsődleges kulcs beolvasása](./tutorial-search-location.md#getkey) a fiókhoz tartozó elsődleges előfizetési kulcs beszerzéséhez című témakör lépéseit.

## <a name="create-an-azure-notebook"></a>Azure-beli jegyzetfüzet létrehozása

Ahhoz, hogy követni tudja az oktatóanyagot, létre kell hoznia egy Azure notebook-projektet, és le kell töltenie és futtatnia kell a Jupyter notebook-fájlt. A jegyzetfüzet-fájl Python-kódot tartalmaz, amely megvalósítja a forgatókönyvet ebben az oktatóanyagban. Az alábbi lépéseket követve hozzon létre egy Azure notebook-projektet, és töltse fel a Jupyter notebook-dokumentumot.

1. Lépjen a [Azure notebookok](https://notebooks.azure.com) , és jelentkezzen be. [További információ: gyors útmutató.](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)
2. A nyilvános profil oldalon válassza a **saját projektek** lehetőséget az oldal tetején.

    ![saját projekt](./media/tutorial-ev-routing/myproject.png)

3. A **saját projektek** lapon válassza az **új projekt**lehetőséget.
 
   ![Új projekt](./media/tutorial-ev-routing/create-project.png)

4. A megjelenő **új projekt létrehozása** előugró ablakban adja meg a következő információkat, majd kattintson a **Létrehozás**gombra:
    * Projekt neve
    * Projektazonosító
 
    ![Projekt létrehozása](./media/tutorial-ev-routing/create-project-window.png)

5. A projekt létrehozása után töltse le a [Jupyter notebook Document fájlt](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) a [Azure Maps Jupyter notebook adattárból](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Válassza ki a projektet a **saját projektek** oldalon a projektek listából, és kattintson a **feltöltés** gombra a Jupyter notebook dokumentum fájljának feltöltéséhez. Töltse fel a fájlt a számítógépről, és kattintson a **kész**gombra.

    ![Jegyzetfüzet feltöltése](./media/tutorial-ev-routing/upload-notebook.png)

7. A sikeres feltöltés után a fájl megjelenik a projekt oldalán. Kattintson a jegyzetfüzet-fájlra, hogy megnyissa Jupyter Notebookként.

A notebook-fájlban megvalósított funkciók jobb megismerése érdekében javasoljuk, hogy egyszerre egy cellában futtassa a kódot a jegyzetfüzetben. A kódot minden cellában futtathatja, ha a **Futtatás** gombra kattint a notebook alkalmazás tetején.

  ![Futtatás](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Projekt szolgáltatásiszint-csomagok telepítése

A kód a jegyzetfüzetben való futtatásához telepítenie kell a csomagokat a projekt szintjén. A szükséges csomagok telepítéséhez kövesse az alábbi lépéseket:

1. Töltse le a ["követelmények. txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) fájlt a [Azure Maps Jupyter notebook adattárból](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) , és töltse fel a projektbe.
2. Válassza ki a projekt irányítópultján **Projektbeállítások**. 
3. A megjelenő előugró ablakban válassza a **környezet lapot**, majd kattintson a **Hozzáadás**gombra.
4. A **környezet beállítása lépésekben** 
    * Az első legördülő listában válassza a **követelmények. txt**elemet.
    * A második legördülő menüben válassza ki a "követelmények. txt" fájlt.
    * A harmadik legördülő menüben válassza a Python-verzió 3,6-as verzióját Python-verzióként.
7. Kattintson a **Mentés** gombra.

    ![Csomagok telepítése](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>A szükséges modulok és keretrendszerek betöltése

Futtassa az alábbi szkriptet a szükséges modulok és keretrendszerek betöltéséhez.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Elérhető tartomány határának kérése

Ebben a példában egy Package Delivery Company rendelkezik néhány elektromos járművel a flottájában. A nap folyamán az elektromos járműveket újra kell számlázni anélkül, hogy vissza kellene térnie a tárházba. Minden alkalommal, amikor az elektromos jármű aktuális hátralévő díja egy óránál kevesebb időt vesz igénybe (az elektromos jármű alacsony töltöttségű), meg kell keresni a rendelkezésre álló tartományon belüli díjszabási állomások készletét, és le kell kérni az adott tartományhoz tartozó határokra vonatkozó információkat. Mivel a vállalat inkább a gazdaság és a sebesség alapján kiegyensúlyozott útvonalakat használ, a kért routeType "Eco". A következő parancsfájl meghívja a Azure Maps útválasztási szolgáltatáshoz tartozó paraméterek [lekérése API](https://docs.microsoft.com/rest/api/maps/route/getrouterange) -t a jármű felhasználási modelljéhez, és elemzi a választ, hogy az autó maximálisan elérhető tartományát jelképező geojson-formátumból hozzon létre egy sokszög objektumot. .

Futtassa a szkriptet az alábbi cellában az elektromos jármű elérhető tartományához tartozó korlátok beszerzéséhez.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get bounds for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Az elektromos járművek díjszabási állomásának keresése elérhető tartományon belül

Ha rendelkezésre áll a rendelkezésre álló tartomány (isochrone) az elektromos járművön, megkereshetjük a díjszabási állomásokat az adott tartományban. A következő szkript meghívja a Azure Maps [utáni keresést a geometriai API](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) -n belül, hogy az autó maximálisan elérhető tartományának határain belüli, az elektromos járművek díjszabási állomásait keressen, majd a válaszait a rendelkezésre álló helyekről elemezze.

Futtassa az alábbi szkriptet a rendelkezésre álló tartományon belüli elektromos járművek díjszabásának kereséséhez.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Elérhető tartomány és töltési pontok feltöltése Azure Maps adatszolgáltatásba

Annak érdekében, hogy megjelenítse a díjszabási állomásokat és a határt az elektromos jármű maximálisan elérhető tartománya számára a térképen, fel kell töltenie a határokat és az állomások adatait geojson objektumként a Azure Maps [Adatfeltöltő API használatával. ](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Futtassa a következő két cellát a határ és a töltési pont adatok Azure Maps adatszolgáltatásba való feltöltéséhez.

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload range data to Azure Maps data service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload EV charging stations data to Azure Maps data service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Díjszabási állomások és elérhető tartomány leképezése

Miután feltöltötte az adatok az adatszolgáltatásba való feltöltését, most a következő szkriptet fogjuk futtatni a Azure Maps [lekérési rendszerkép-szolgáltatás](https://docs.microsoft.com/rest/api/maps/render/getmapimage) meghívásához, hogy a kitöltési pontok és a maximálisan elérhető határ legyen a statikus térképi képen.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![hely tartománya](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Az optimális töltési állomás megkeresése

Miután az összes lehetséges díjszabási állomás elérhető a rendelkezésre álló tartományon belül, szeretnénk tudni, hogy melyik állomás érhető el a minimálisan megengedettnél. A következő szkript meghívja a Azure Maps [Matrix Routing API](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) -t, amely az adott jármű helyére az utazási időt és a távolságot adja vissza az egyes díjszabási állomások helyétől. A következő cellában lévő parancsfájl elemzi a legközelebbi elérhető töltőállomás elérési helyére vonatkozó választ, az idő tekintetében.

Futtassa a következő cellát a legközelebbi elérhető töltőállomás megkereséséhez, amely a minimális időtartamon belül elérhető.

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Útvonal kiszámítása a legközelebbi kitöltési állomáshoz

Most, hogy megtalálta a legközelebbi kitöltési állomást, a következő lépésben meghívjuk a [Get Route Directions API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) -t, hogy a részletes útvonalat kérje az elektromos jármű aktuális helyéről a díjszabási állomásra.

Futtassa a szkriptet a következő cellában az útvonal lekéréséhez, és elemezze a választ az útvonalat jelképező geojson-objektum létrehozásához.

```python
# Get route from current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Az útvonal megjelenítése

Az útvonal megjelenítéséhez először fel kell töltenie az geojson objektumként a Azure Maps adatszolgáltatásba a Azure Maps [Adatfeltöltő API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)használatával. Ezután hívja meg a render szolgáltatást, és a [Térkép képapi](https://docs.microsoft.com/rest/api/maps/render/getmapimage) -val jelenítse meg az útvonalat a térképen, és jelenítse meg.

Futtassa a következő szkriptet a megjelenített útvonalhoz tartozó rendszerkép lekéréséhez a térképen.

```python
# Upload route data to Azure data service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![útvonal](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan hívhatja közvetlenül a Azure Maps REST API-kat, és hogyan jelenítheti meg Azure Maps-adatok a Python használatával.

Az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [Útvonal tartományának beolvasása](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Keresés közzététele a geometrián belül](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Adatok feltöltése](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render-Térkép képe](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Útvonal-mátrix küldése](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Útvonal irányának beolvasása](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

A Azure Maps REST API-k teljes listájáért lásd:

* [Azure Maps REST API-k](https://docs.microsoft.com/azure/azure-maps/#reference)

A Azure Notebooksról további információt a következő témakörben talál:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)