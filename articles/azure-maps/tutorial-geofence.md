---
title: 'Oktatóanyag: geokerítésen létrehozása és eszközök nyomon követése Microsoft Azure térképen'
description: Ismerje meg, hogyan állíthat be geokerítésen. Tekintse meg, hogyan követheti nyomon az eszközöket a geokerítésen viszonyítva a Azure Maps térbeli szolgáltatás használatával.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/11/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3ea9923dd98a49b1533defa3e95616655b7ea78d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299303"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Oktatóanyag: Geokerítés beállítása az Azure Maps használatával

Ez az oktatóanyag végigvezeti a Azure Maps Geokerítésen-szolgáltatások létrehozásának és használatának alapjain az alábbi forgatókönyv kontextusában:

*Az építkezési Site Manager meg kell követnie a berendezéseket, ahogy belép, és elhagyja az építkezés területeit. Amikor egy berendezés kilép vagy beírja ezeket a kerületeket, a rendszer e-mailben értesítést küld a Operations Manager.*

A Azure Maps számos szolgáltatást biztosít a fenti forgatókönyvben az építkezési terület bevezetését és onnan való kilépését támogató berendezések nyomon követésének támogatásához. Ebben az oktatóanyagban a következőket ismertetjük:

> [!div class="checklist"]
> * Töltse fel az [Geokerítések GeoJSON-adatok](geofence-geojson.md) körét, amelyek meghatározzák a figyelni kívánt építkezési területeket. Az [Adatfeltöltő API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) -val a geofences-t sokszög-koordinátákként kell feltölteni a Azure Maps-fiókjába.
> * Állítson be két [logikai alkalmazást](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) , amely a indításakor elküldi az e-mailes értesítéseket az építkezési helynek Operations Manager amikor a berendezés belép és kilép a geokerítésen területről.
> * A [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) előfizethet Azure Maps geokerítésen-be és-kilépési eseményekre. Beállítunk két webhook esemény-előfizetést, amely meghívja a két Logic Appsban definiált HTTP-végpontokat. A Logic Apps ezután elküldi a szükséges, a geokerítésen meghaladó vagy oda áthelyezett berendezések megfelelő e-mail-értesítéseit.
> * A [Search Geokerítésen Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) -val értesítéseket kaphat, amikor egy berendezés kilép, és belép a geokerítésen területekre.

## <a name="prerequisites"></a>Előfeltételek

1. [Azure Maps fiók létrehozása](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Szerezzen be egy elsődleges előfizetési kulcsot](quick-demo-map-app.md#get-the-primary-key-for-your-account), más néven az elsődleges kulcsot vagy az előfizetési kulcsot.

Ez az oktatóanyag a [Poster](https://www.postman.com/) alkalmazást használja, de más API-fejlesztési környezetet is választhat.

## <a name="upload-geofencing-geojson-data"></a>Geokerítések GeoJSON-adatok feltöltése

Ebben az oktatóanyagban feltöltjük a Geokerítések GeoJSON-adatok listáját, amely tartalmazza a következőt: `FeatureCollection` . A `FeatureCollection` két geofences tartalmaz, amelyek az építkezési helyen lévő sokszögeket határozzák meg. Az első geokerítésen nem rendelkezik időbeli lejárattal vagy korlátozásokkal. A másodikat csak munkaidőn belül lehet lekérdezni (9-5 óra PST), és a továbbiakban nem lesz érvényes a 2022 január 1. után. A GeoJSON formátumával kapcsolatos további információkért lásd: [Geokerítések GeoJSON-adatok](geofence-geojson.md).

>[!TIP]
>Bármikor frissítheti a Geokerítések adatait. Az adatok frissítésével kapcsolatos további információkért lásd az [adatfeltöltés API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) -t ismertető témakört.

1. Nyissa meg a Poster alkalmazást. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény**elemet.  Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot.

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget. Adja meg a kérelem **nevét** . Válassza ki az előző lépésben létrehozott gyűjteményt, majd kattintson a **Mentés**gombra.

3. Válassza a http **post** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a geokerítések-adatok Azure Maps szolgáltatásba való feltöltéséhez. Ehhez a kérelemhez és a cikkben említett egyéb kérelmekhez cserélje le az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulcsát.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Az URL-cím _geojson_ paramétere a feltöltött adatformátumot jelöli.

4. Kattintson a **törzs** fülre. Válassza a **RAW**lehetőséget, majd a **JSON** -t bemeneti formátumként. Másolja ki és illessze be az alábbi GeoJSON-beállításokat a **szövegtörzs** területére:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Kattintson a kék **Küldés** gombra, és várjon, amíg a rendszer feldolgozza a kérést. A kérés befejeződése után lépjen a válasz **fejlécek** lapjára. Másolja a **hely** kulcsának értékét, amely a `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Az API-hívás állapotának megtekintéséhez hozzon létre egy **Get** http-kérelmet a on `status URL` . A hitelesítéshez hozzá kell fűzni az elsődleges előfizetési kulcsot az URL-címhez. A **Get** kérelemnek a következő URL-címnek kell megjelennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Ha a **Get** HTTP-kérelem sikeresen befejeződik, a visszaadja a-t `resourceLocation` . A `resourceLocation` tartalmazza a `udid` feltöltött tartalom egyedi tartalmát. Ennek az oktatóanyagnak az utolsó szakaszában le kell mentenie ezt a `udid` Get GEOKERÍTÉSEN API lekérdezéséhez. A `resourceLocation` következő lépésben használhatja az URL-címet is az erőforrás metaadatainak lekéréséhez.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. A tartalom metaadatainak beolvasásához hozzon létre egy **Get** HTTP-kérést a `resourceLocation` 7. lépésben lekért URL-címen. Ügyeljen arra, hogy az elsődleges előfizetési kulcsot az URL-címhez fűzze a hitelesítéshez. A **Get** kérelemnek a következő URL-címnek kell megjelennie:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. Ha a HTTP-kérés **beolvasása** sikeresen befejeződött, a válasz törzse tartalmazza a `udid` `resourceLocation` 7. lépésben megadott helyet, a tartalom elérésének és letöltésének helyét, valamint a tartalommal kapcsolatos egyéb metaadatokat, például a létrehozott/frissített dátumot, a méretet stb. Az általános válasz példája:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-logic-app-workflows"></a>Logikai alkalmazás-munkafolyamatok létrehozása

Ebben a szakaszban két [logikai alkalmazás](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) -végpontot hozunk létre, amelyek e-mailes értesítést küldenek. Bemutatjuk, hogyan hozhatja létre az első triggert, amely e-mail-értesítéseket küld, amikor a végpontja meghívásra kerül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

2. Kattintson az [Azure Portal](https://portal.azure.com) bal felső sarkában az **Erőforrás létrehozása** gombra.

3. A *Keresés a piactéren* mezőbe írja be a **Logic app**kifejezést.

4. Az *eredmények*közül válassza a **logikai alkalmazás**lehetőséget. Kattintson a **Létrehozás** gombra.

5. A **logikai alkalmazás** lapon adja meg a következő értékeket:
    * Az ehhez a logikai alkalmazáshoz használni kívánt *előfizetés* .
    * Az *erőforráscsoport* neve ehhez a logikai alkalmazáshoz. Választhat, hogy *létrehoz egy új erőforráscsoportot*, vagy egy *meglévő erőforráscsoportot használ*.
    * A logikai alkalmazás *logikai alkalmazásának neve* . Ebben az esetben `Equipment-Enter` a nevet használjuk.

    Ebben az oktatóanyagban tartsa meg a többi értéket az alapértelmezett beállításokon.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Logikai alkalmazás létrehozása":::

6. Kattintson a **felülvizsgálat + létrehozás** gombra. Tekintse át a beállításokat, és kattintson a **Létrehozás** gombra a telepítés elküldéséhez. Ha a telepítés sikeresen befejeződött, kattintson az **Ugrás az erőforrásra**elemre. A **Logic app Designer**

7. Most kiválasztunk egy trigger típust. Görgessen le egy kicsit a *kezdéshez egy közös trigger** szakasz használatával. Kattintson a **http-kérelem fogadása**lehetőségre.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Logikai alkalmazás HTTP-triggerének létrehozása":::

8. Kattintson a tervező jobb felső sarkában található **Mentés** gombra. A rendszer automatikusan létrehozza a **http post URL-címet** . Mentse az URL-címet, mert szüksége lesz rá a következő szakaszban egy esemény-végpont létrehozásához.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Logic app HTTP-kérelem URL-címe és JSON":::

9. Válassza az **+ új lépés**lehetőséget. Most választjuk ki a műveletet. Írja be `outlook.com email` a keresőmezőbe a kifejezést. A **műveletek** listában görgessen lefelé, és kattintson az **E-mail küldése (v2)** elemre.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Logic app Designer létrehozása":::

10. Jelentkezzen be a Outlook.com-fiókjába. Ügyeljen arra, hogy az **Igen** gombra kattintva engedélyezze a logikai alkalmazás számára a fiók elérését. Adja meg az e-mailek küldésére szolgáló mezőket.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Logikai alkalmazás létrehozása – e-mail küldése lépés":::

    >[!TIP]
    > Az e-mail-értesítések GeoJSON beolvashatja a `geometryId` `deviceId` logikai alkalmazás konfigurálásával a Event Grid által küldött információk olvasásához. További információ a logikai alkalmazások használatáról és az események e-mailes értesítésekre való továbbításáról [: oktatóanyag: e-mailes értesítések küldése az Azure IoT hub eseményekről Event Grid és Logic Apps használatával](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps).

11. Kattintson a **Save (Mentés** ) gombra a Logic apps Designer bal felső sarkában.

12. Ismételje meg a 3-11 lépést egy második logikai alkalmazás létrehozásához, amely értesíti a kezelőt, ha a berendezés kilép az építkezési helyről. Nevezze el a logikai alkalmazást `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Azure Maps esemény-előfizetések létrehozása

A Azure Maps három eseménytípus használatát támogatja. Tekintse meg a Azure Maps támogatott eseménytípus [itt](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps).  Két különböző esemény-előfizetést kell létrehoznia: az egyiket a geokerítésen adja meg, egyet pedig a geokerítésen kilépési eseményeihez.

Az alábbi lépéseket követve hozzon létre egy esemény-előfizetést a geokerítésen események beviteléhez. A geokerítésen kilépési eseményekre való előfizetéshez hasonló módon ismételje meg a lépéseket.

1. Navigáljon a Azure Maps-fiókjához. Az irányítópulton válassza az **előfizetések**lehetőséget. Kattintson az előfizetés nevére, és válassza az **események** lehetőséget a beállítások menüben.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Azure Maps-fiók eseményeinek megkeresése":::

2. Esemény-előfizetés létrehozásához válassza az események lap **+ esemény előfizetése** elemét.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Azure Maps esemény-előfizetés létrehozása":::

3. Az **esemény-előfizetés létrehozása** lapon adja meg a következő értékeket:
    * Az esemény-előfizetés *neve* .
    * Az *esemény sémájának* *Event Grid sémának*kell lennie.
    * A *Rendszertéma neve* ehhez az esemény-előfizetéshez. Ebben az esetben a következőt fogjuk használni: `Contoso-Construction` .
    * A *szűrés az események típusainál*válassza `Geofence Entered` az esemény típusa lehetőséget.
    * A *végpont típusa*beállításnál válassza a lehetőséget `Web Hook` .
    * A *végpont*esetében másolja a logikai alkalmazáshoz az előző szakaszban létrehozott végponthoz tartozó http-post URL-címet. Ha elfelejtette menteni, visszatérhet a Logic app Designerbe, és átmásolhatja a HTTP-trigger lépésből.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Azure Maps események előfizetés részletei":::

4. Kattintson a **Létrehozás** lehetőségre.

5. Ismételje meg a 1-4. lépést az előző szakaszban létrehozott Logic app kilépő végponthoz. A 3. lépésnél ügyeljen arra, hogy válassza ki `Geofence Exited` az esemény típusát.

## <a name="use-spatial-geofence-get-api"></a>Térbeli Geokerítésen Get API használata

Most a [térbeli Geokerítésen Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) -t fogjuk használni e-mail-értesítések küldéséhez a Operations Managerba, amikor egy berendezés belép vagy kilép a geofences.

Minden berendezéshez tartozik egy `deviceId` . Ebben az oktatóanyagban egy olyan berendezést fogunk nyomon követni, amelynek egyedi azonosítója a következő: `device_1` .

Az érthetőség érdekében a következő ábrán a berendezés öt helye látható a *kezdő* helyétől kezdve, ami valahol a geofences kívül esik. Ebben az oktatóanyagban a *kezdő* hely nem definiált, mert az adott helyen nem kérdezi le az eszközt.

Amikor lekérdezi a [térbeli Geokerítésen Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) -t egy olyan berendezési hellyel, amely a kezdeti geokerítésen-bevitelt vagy-kilépést jelzi, a Event Grid meghívja a megfelelő logikai alkalmazás-végpontot, hogy e-mail értesítést küldjön a Operations Managernak.

A következő részekben a HTTP GET Geokerítések API-kérelmek a berendezés öt különböző helyének koordinátáit használják.

![Geokerítésen-leképezés Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Berendezések helye 1 (47.638237,-122,132483)

1. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget.  Adja meg a kérelem **nevét** . A nevet, az 1. *helyet*fogjuk használni. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés**lehetőséget.

2. Jelölje be a http **lekérése** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet, és ügyeljen rá, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal és `{udid}` a `udid` [geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentett értékre cserélje.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON fog megjelenni a válasz ablakban.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

4. A fenti GeoJSON-válaszban a fő hely geokerítésen negatív távolsága azt jelenti, hogy a berendezés a geokerítésen belül található. A alhelyről geokerítésen pozitív távolság azt jelenti, hogy a berendezés kívül esik a alhelyen geokerítésen. Mivel ez az eszköz első alkalommal a fő hely geokerítésen belül található, a paraméter értéke, `isEventPublished` `true` és a Operations Manager e-mailben értesítést kapott arról, hogy a berendezés belépett a geokerítésen.

### <a name="location-2-4763800-122132531"></a>2. hely (47.63800,-122,132531)

1. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget.  Adja meg a kérelem **nevét** . A nevet, a 2. *helyet*fogjuk használni. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés**lehetőséget.

2. Jelölje be a http **lekérése** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet, és ügyeljen rá, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal és `{udid}` a `udid` [geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentett értékre cserélje.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON fog megjelenni a válasz ablakban:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. A fenti GeoJSON-válaszban a berendezés a fő hely geokerítésen maradt, és nem adta meg a geokerítésen alhelyét. Ennek eredményeképpen a `isEventPublished` paraméter be van állítva, `false` és a Operations Manager nem kap e-mail-értesítéseket.

### <a name="location-3-4763810783315048-12213336020708084"></a>3. hely (47.63810783315048,-122.13336020708084)

1. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget.  Adja meg a kérelem **nevét** . A nevet, a 3. *helyet*fogjuk használni. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés**lehetőséget.

2. Jelölje be a http **lekérése** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet, és ügyeljen rá, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal és `{udid}` a `udid` [geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentett értékre cserélje.

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON fog megjelenni a válasz ablakban:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. A fenti GeoJSON-válaszban a berendezés megmaradt a fő hely geokerítésen, de megadta a geokerítésen alhelyét. Ennek eredményeképpen a paraméter értéke, `isEventPublished` `true` a Operations Manager pedig egy e-mail-értesítést kap, amely jelzi, hogy a berendezés geokerítésen adott meg.

    >[!NOTE]
    >Ha a berendezés munkaidő után áthelyezte az alhelyre, egyetlen eseményt sem tesz közzé, és a Operations Manager nem kap értesítést.  

### <a name="location-4-47637988-1221338344"></a>4. hely (47.637988,-122,1338344)

1. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget.  Adja meg a kérelem **nevét** . A nevet, a 4. *helyet*fogjuk használni. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés**lehetőséget.

2. Jelölje be a http **lekérése** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet, és ügyeljen rá, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal és `{udid}`  a `udid` [geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentett értékre cserélje.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON fog megjelenni a válasz ablakban:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

4. A fenti GeoJSON-válaszban a berendezés a fő hely geokerítésen maradt, de kilépett a geokerítésen alhelyről. Ha azonban észreveszi, az `userTime` érték a `expiredTime` geokerítésen-adatként megadott módon történik. Ennek eredményeképpen a `isEventPublished` paraméter be van állítva, `false` és a Operations Manager nem kap e-mail-értesítést.

### <a name="location-547637988-1221338344"></a>5. hely (47.637988,-122,1338344)

1. A Poster alkalmazás teteje közelében válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget.  Adja meg a kérelem **nevét** . A nevet, a 4. *helyet*fogjuk használni. Válassza ki a gyűjteményt, amelyet a [Geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)hozott létre, majd válassza a **Mentés**lehetőséget.

2. Jelölje be a http **lekérése** metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet, és ügyeljen rá, hogy az `{Azure-Maps-Primary-Subscription-key}` elsődleges előfizetési kulccsal és `{udid}` a `udid` [geokerítések GeoJSON-adatok feltöltése szakaszban](#upload-geofencing-geojson-data)mentett értékre cserélje.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Kattintson a **Küldés** gombra. A következő GeoJSON fog megjelenni a válasz ablakban:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

4. A fenti GeoJSON-válaszban a berendezés kilépett a fő hely geokerítésen. Ennek eredményeképpen a paraméter a (z `isEventPublished` ) értékre van állítva, `true` és a Operations Manager e-mailben értesítést kap arról, hogy a berendezés kilépett egy geokerítésen.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartalomtípusok kezelése Azure Logic Appsban](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)

> [!div class="nextstepaction"]
> [E-mail-értesítések küldése Event Grid és Logic Apps használatával](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)

> [!div class="nextstepaction"]
> [A Event Grid támogatott események kezelői](https://docs.microsoft.com/azure/event-grid/event-handlers).
