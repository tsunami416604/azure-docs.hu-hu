---
title: 'Oktatóanyag: Elektromos járművek irányítása az Azure Notebooks (Python) használatával | Microsoft Azure Maps'
description: Elektromos járművek irányítsa a Microsoft Azure Maps útválasztási API-k és az Azure Notebookok használatával.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333839"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Oktatóanyag: Elektromos járművek irányítása az Azure Notebooks (Python) használatával

Az Azure Maps az Azure-ba natívmódon integrált térinformatikai szolgáltatás API-k portfóliója. Ezek az API-k lehetővé teszik a fejlesztők, a vállalatok és a független szoftverszállítók számára, hogy helyalapú alkalmazásokat, IoT-t, mobilitást, logisztikát és eszközkövetési megoldásokat fejlesszenek ki. 

Az Azure Maps REST API-k at lehet hívni a nyelvek, például a Python és az R, hogy a térinformatikai adatok elemzése és a gépi tanulási forgatókönyvek. Az Azure Maps az [útválasztási API-k](https://docs.microsoft.com/rest/api/maps/route) robusztus készletét kínálja, amelyek lehetővé teszik a felhasználók számára, hogy több adatpont közötti útvonalakat számítsanak ki. A számítások különböző feltételeken alapulnak, mint például a járműtípus vagy az elérhető terület. 

Ebben a bemutatóban, séta segít a vezető, akinek elektromos jármű akkumulátor akkad. A vezetőnek meg kell találnia a lehető legközelebbi töltőállomást a jármű helyéről.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre és futtasson egy Jupyter-jegyzetfüzetet a felhőben lévő [Azure-jegyzetfüzetekben.](https://docs.microsoft.com/azure/notebooks)
> * Hívja meg az Azure Maps REST API-kat a Pythonban.
> * Keressen egy elérhető tartományt az elektromos jármű fogyasztási modellje alapján.
> * Keressen elektromos jármű töltőállomásokat az elérhető tartományon belül, vagy isochrone.
> * Renderelje az elérhető tartományhatárt és a töltőállomásokat a térképen.
> * Keresse meg és vizualizálja az útvonalat a legközelebbi elektromos jármű töltőállomáshoz a menetidő alapján.


## <a name="prerequisites"></a>Előfeltételek 

Az oktatóanyag befejezéséhez először létre kell hoznia egy Azure Maps-fiókot, és be kell szereznie az elsődleges kulcsot (előfizetési kulcs). 

Azure Maps-fiók-előfizetés létrehozásához kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps)című útmutató utasításait. Szüksége van egy Azure Maps-fiók-előfizetésre az S1 árcsomaggal. 

A fiók elsődleges előfizetési kulcsának lekérni, kövesse az [elsődleges kulcs bekésezése](quick-demo-map-app.md#get-the-primary-key-for-your-account)című útmutató utasításait.

Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.

## <a name="create-an-azure-notebook"></a>Azure-jegyzetfüzet létrehozása

Az oktatóanyag követéséhez létre kell hoznia egy Azure notebook-projektet, és le kell töltenie és futtatnia kell a Jupyter-jegyzetfüzetfájlt. A jegyzetfüzetfájl Python-kódot tartalmaz, amely megvalósítja a forgatókönyvet ebben az oktatóanyagban. Azure-jegyzetfüzet-projekt létrehozásához és a Jupyter-jegyzetfüzet-dokumentum feltöltéséhez tegye a következő lépéseket:

1. Nyissa meg az [Azure-jegyzetfüzeteket,](https://notebooks.azure.com) és jelentkezzen be. További információt a Rövid útmutató: Bejelentkezés és felhasználói azonosító beállítása című [témakörben talál.](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)
1. A nyilvános profillap tetején válassza a **Saját projektek**lehetőséget.

    ![A Saját projektek gomb](./media/tutorial-ev-routing/myproject.png)

1. A **Saját projektek** lapon válassza az **Új projekt**lehetőséget.
 
   ![Az Új projekt gomb](./media/tutorial-ev-routing/create-project.png)

1. Az **Új projekt létrehozása** ablaktáblán adja meg a projekt nevét és a projektazonosítót.
 
    ![Az Új projekt létrehozása ablaktábla](./media/tutorial-ev-routing/create-project-window.png)

1. Kattintson a **Létrehozás** gombra.

1. A projekt létrehozása után töltse le ezt a [Jupyter notebook dokumentumfájlt](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) az [Azure Maps Jupyter jegyzetfüzettárból.](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)

1. A **Projektek** lap Projektek listájában jelölje ki a projektet, majd a **Feltöltés gombra** a Jupyter-jegyzetfüzet dokumentumfájljának feltöltéséhez. 

    ![jegyzetfüzet feltöltése](./media/tutorial-ev-routing/upload-notebook.png)

1. Töltse fel a fájlt a számítógépről, majd válassza a **Kész gombot.**

1. A feltöltés sikeres befejezése után a fájl megjelenik a projekt oldalon. Kattintson duplán a fájlra, hogy megnyissa jupyter notebookként.

Próbálja meg megérteni a jegyzetfüzetfájlban megvalósított funkciókat. Futtassa a kódot a jegyzetfüzetfájlban, egyszerre egy cellával. A kódot az egyes cellákban futtathatja a jegyzetfüzetalkalmazás tetején található **Futtatás** gombra kattintva.

  ![A Futtatás gomb](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Projektszintű csomagok telepítése

A kód jegyzetfüzetben való futtatásához telepítse a csomagokat a projekt szintjén az alábbi lépésekkel:

1. Töltse le a [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) fájlt az [Azure Maps Jupyter jegyzetfüzettárból,](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)majd töltse fel a projektbe.
1. A projekt irányítópultján válassza a **Projektbeállítások lehetőséget.** 
1. A **Projekt beállításai** ablaktáblán válassza a **Környezet** lapot, majd a **Hozzáadás gombot.**
1. A **Környezet beállítási lépései csoportban**tegye a következőket:   
    a. Az első legördülő listában válassza a **Requirements.txt**lehetőséget.  
    b. A második legördülő listában válassza ki a *requirements.txt* fájlt.  
    c. A harmadik legördülő listában válassza a **Python 3.6-os verziója** a saját verziója.
1. Kattintson a **Mentés** gombra.

    ![Csomagok telepítése](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Töltse be a szükséges modulokat és keretrendszereket

Az összes szükséges modul és keretrendszer betöltéséhez futtassa a következő parancsfájlt.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Az elérhető tartományhatár kérése

Egy csomagszállító cég flottájában van néhány elektromos jármű. A nap folyamán az elektromos járműveket újra kell tölteni anélkül, hogy vissza kellene térniük a raktárba. Minden alkalommal, amikor a fennmaradó töltés kevesebb, mint egy órára csökken, olyan töltőállomásokat keres, amelyek elérhető tartományon belül vannak. Lényegében akkor keres töltőállomást, amikor az akkumulátor töltöttsége alacsony. És megkapja a határinformációkat az adott töltőállomástartományhoz. 

Mivel a vállalat előnyben részesíti a gazdaságosság és a sebesség egyensúlyát igénylő útvonalak használatát, a kért routeType *az eco*. A következő parancsfájl meghívja az Azure Maps útválasztási szolgáltatás [útvonaltartomány API-ját.](https://docs.microsoft.com/rest/api/maps/route/getrouterange) A jármű fogyasztási modelljének paramétereit használja. A parancsfájl ezután elemzi a választ, hogy hozzon létre egy sokszög objektum a geojson formátumban, amely az autó maximális elérhető tartományban.

Az elektromos jármű elérhető hatótávolságának határainak meghatározásához futtassa a szkriptet a következő cellában:

```python
subscriptionKey = "Your Azure Maps key"
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


# Get boundaries for the electric vehicle's reachable range.
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

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Elektromos járművek töltőállomásának keresése az elérhető hatótávolságon belül

Miután meghatározta az elektromos jármű elérhető hatótávolságát (isochrone), megkeresheti a töltőállomásokat az adott tartományon belül. 

A következő parancsfájl meghívja az Azure Maps [Post Search Inside Geometry API-t.](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) Az autó legnagyobb hatósugarának határain belül keresi az elektromos járművek töltőállomásait. Ezután a parancsfájl egy elérhető helyek tömbjének elemzi a választ.

Ha az elektromos járművek töltőállomásait az elérhető tartományon belül szeretné keresni, futtassa a következő parancsfájlt:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Töltse fel az elérhető tartományt és a töltőpontokat az Azure Maps adatszolgáltatásba

A térképen érdemes megvizelni a töltőállomásokat és az elektromos jármű maximális ható tartományának határát. Ehhez töltse fel a határadatokat és a töltőállomások adatait geojson-objektumként az Azure Maps Data Service szolgáltatásba. Használja az [Adatfeltöltés API-t.](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) 

A határ- és töltésipont-adatok Feltöltéséhez futtassa a következő két cellát:

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

# Upload the range data to Azure Maps Data Service.
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

# Upload the electric vehicle charging station data to Azure Maps Data Service.
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

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>A töltőállomások és az elérhető hatótávolság renderelése a térképen

Miután feltöltötte az adatokat az adatszolgáltatásba, hívja meg az Azure Maps [Térképkép szolgáltatását.](https://docs.microsoft.com/rest/api/maps/render/getmapimage) Ez a szolgáltatás a töltőpontok és a maximális elérhető határ a statikus térképképen a következő parancsfájl futtatásával:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
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

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![A helytartományt megjelenítő térkép](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Találja meg az optimális töltőállomást

Először is meg szeretné határozni az összes lehetséges töltőállomást az elérhető tartományon belül. Ezután szeretné tudni, hogy melyikük érhető el minimális idő alatt. 

A következő parancsfájl meghívja az Azure Maps [Matrix Routing API-t.](https://docs.microsoft.com/rest/api/maps/route/postroutematrix) Visszaadja a jármű adott helyét, az utazási időt és az egyes töltőállomásoktávolságát. A következő cellában lévő parancsfájl elemzi a választ, hogy megtalálja a legközelebbi elérhető töltőállomást az időhöz képest.

A legközelebbi elérhető töltőállomás megkereséséhez a legkevesebb idő alatt érhető el, futtassa a parancsfájlt a következő cellában:

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

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Számítsa ki a legközelebbi töltőállomáshoz vezető útvonalat

Most, hogy megtalálta a legközelebbi töltőállomást, felhívhatja a [Get Route Directions API-t,](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) hogy részletes útvonalat kérjen az elektromos jármű aktuális helyétől a töltőállomásig.

A töltőállomásra vezető útvonal lehívásához és az útvonalat jelölő geojson objektum létrehozására adott válasz elemzéséhez futtassa a parancsfájlt a következő cellában:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
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

Az útvonal megjelenítéséhez először töltse fel az útvonaladatokat geojson objektumként az Azure Maps Data Service szolgáltatásba. Ehhez használja az Azure Maps [Data Upload API-t.](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) Ezután hívja meg a renderelési szolgáltatást, a [Get Map Image API-t,](https://docs.microsoft.com/rest/api/maps/render/getmapimage)hogy megjelenítse az útvonalat a térképen, és vizualizálja azt.

Ha képet szeretne kapni a renderelt útvonalról a térképen, futtassa a következő parancsfájlt:

```python
# Upload the route data to Azure Maps Data Service.
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


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Az útvonalat megjelenítő térkép](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hívhatja meg közvetlenül az Azure Maps REST API-kat, és hogyan jelenítheti meg az Azure Maps-adatokat a Python használatával.

Az ebben az oktatóanyagban használt Azure Maps API-k megismeréséhez lásd:

* [Útvonaltartomány beszerezése](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Keresés a geometrián belül](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Adatok feltöltése](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Renderelés – Térképkép bekése](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Útvonalmátrix feladása](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Útvonalútvonal-utasítások beszereznie](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Az Azure Maps REST API-k teljes listáját az [Azure Maps REST API-k ban.](https://docs.microsoft.com/azure/azure-maps/consumption-model)

Az Azure-jegyzetfüzetekről az [Azure-jegyzetfüzetek](https://docs.microsoft.com/azure/notebooks)ről olvashat bővebben.
