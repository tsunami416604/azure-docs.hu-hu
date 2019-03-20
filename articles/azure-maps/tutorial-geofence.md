---
title: Hozzon létre egy Azure mapsszel geokerítésen |} A Microsoft Docs
description: A telepítő a geokerítésen az Azure Maps használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 112d0bd4b6802179692d0d177775027e552d1170
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085320"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Az Azure Maps segítségével egy geokerítésen beállítása

Ez az oktatóanyag végigvezeti azokon az alapvető lépéseket geokerítésen beállítása az Azure Maps használatával. A forgatókönyvet, hogy oldja meg az oktatóanyag segítséget konstrukció hely kezelők figyelő lehetséges veszélyes berendezések a kijelölt építési területeken túl. Építkezés magában foglalja a költséges eszközöket és az előírásoknak. Általában van szükség, hogy a készülék az építkezés belül marad, és nem kerülnek engedélye nélkül.

Használjuk az Azure Maps adatok feltöltése API a geokerítésen tárolására, és használja az Azure maps Geokerítésen API-t, hogy ellenőrizze a készülék helyét a geokerítésen viszonyítva. Azure Event Grid használjuk a geokerítésen eredmények streamelése és beállíthat egy értesítést a geokerítésen eredményei alapján.
Az Event Griddel kapcsolatos további információkért lásd: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
Ebben az oktatóanyagban azt tekintjük át hogyan lehet:

> [!div class="checklist"]
> * Töltse fel az Azure Maps, az adatok feltöltése API-val adatszolgáltatás geokerítésen területet.
> *   Állítsa be az Event Grid geokerítésen események kezeléséhez.
> *   A telepítő geokerítésen eseményeket kezelő.
> *   Riasztásokat állíthat be a Logic Apps használatával geokerítésen eseményeihez.
> *   Az Azure Maps geokerítésen szolgáltatás API-k segítségével nyomon követheti, akár a konstrukció eszköz a konstrukció helyen belül van, akár nem.


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Ebben az oktatóanyagban a lépések elvégzéséhez szüksége lesz megtekintéséhez [fiók és kulcsok kezelése](how-to-manage-account-keys.md) hozhat létre és kezelhet a fiók előfizetés S1 tarifacsomagot.

## <a name="upload-geofences"></a>Töltse fel a geokerítések

Szeretne feltölteni a geokerítésen a konstrukció hely, az adatok feltöltése API-val, a postman alkalmazást használjuk. Az ebben az oktatóanyagban feltételezzük, van egy általános konstrukció hely területet, amely egy rögzített paraméter, amely a konstrukció berendezés kell sérti. Az időkorlát megsértésének súlyos küzdelemben, és az Operations Manager jelentett. Egy további kerítések optimalizált készlet használható, amelyek nyomon követik a különböző szerkezeti területek belül az összes konstrukció terület ütemezés alapján. Feltételezzük, hogy rendelkezik-e a fő geokerítésen alwebhely1, amely rendelkezik egy készlet lejárati időt, és idő után jár le. Több beágyazott geokerítések igény szerint hozhat létre. Alwebhely1 lehet például, ha munkahelyi lefolyása 1 – 4 héten az ütemezés és alwebhely 2, ahol munka történik hétre esik-e 5 – 7. Az összes ilyen kerítések a projekt elején egyetlen adatkészletként betölthetők és szabályokat időt és hely alapján nyomon követésére szolgál. További információ a geokerítésen adatformátum: [Geokerítésen GeoJSON adatok](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). További információ az adatok feltöltése az Azure Maps szolgáltatásban: [adatok feltöltése API-dokumentáció](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Nyissa meg a Postman alkalmazást, és kövesse az alábbi lépések végrehajtásával töltse fel a konstrukció hely geokerítésen az Azure Maps, az adatok feltöltése API használatával.

1. Nyissa meg a Postman alkalmazást, és kattintson új |} Új létrehozása, és válassza ki a kérelmet. Adja meg a kérelem feltöltési geokerítésen adatok nevét, válassza ki a gyűjtemény vagy a mappát, mentse, majd kattintson a mentés.

    ![Töltse fel a geokerítések Postman használatával](./media/tutorial-geofence/postman-new.png)

2. POST HTTP metódus a jelentéskészítő lapon válassza ki, és adja meg a következő URL-cím a POST-kérés.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    A GEOJSON paramétert az URL-címet jelöli az adatok feltöltése folyamatban adatok formátumát.

3. Kattintson a **paraméterei**, és adja meg a következő kulcs-érték párokat a POST kérelem URL-cím használható. Előfizetés-kulcs értékét cserélje le az Azure Maps előfizetési kulcs.
   
    ![Kulcs-érték paraméterek Postman](./media/tutorial-geofence/postman-key-vals.png)

4. Kattintson a **törzs** majd válassza ki a bemeneti fájlt nyers formátumban, és válassza ki a JSON a bemeneti formátum, a legördülő listából. Adja meg a következő JSON fel kell tölteni adatokkal:

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
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
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

5. Kattintson a Küldés gombra, és tekintse át a válaszfejlécben. A location fejlécet tartalmazza az URI-t eléréséhez, vagy töltse le az adatokat későbbi használatra. Emellett tartalmaz egy egyedi `udId` a feltöltött adatok.

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

## <a name="set-up-an-event-handler"></a>Állítsa be az eseménykezelő

Az Operations Manager enter és kilépési eseményekről értesíti, hogy hozzon létre egy eseménykezelő, amely fogadja az értesítéseket.

Hozunk létre két [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) kezeljék, adja meg, és lépjen ki az eseményeket. Hozunk létre eseményindítókat a Logic Apps, hogy ezek az események által kiváltott lekérése is. A cél, hogy küld riasztást, ebben az esetben e-mailt küld az Operations Manager, amikor a berendezések lép vagy kilép az építkezés. A következő ábra illusztrálja a Logic Apps-alkalmazás létrehozása a geokerítésen belépésének eseményét. Hasonlóképpen egy másik kilépési esemény is létrehozhat.
Láthatja az összes [eseménykezelők támogatott](https://docs.microsoft.com/azure/event-grid/event-handlers) további információ.

1. Logikai alkalmazás létrehozása az Azure Portalon

   ![Logikai alkalmazások létrehozása](./media/tutorial-geofence/logic-app.png)

2. Jelölje be a HTTP-kérelem típusú trigger és válassza az "e-mail küldése" műveletet az outlook-összekötőben
  
   ![Logic Apps-séma](./media/tutorial-geofence/logic-app-schema.png)

3. Mentse a logikai alkalmazás a HTTP URL-végpontot létrehozni, és másolja a HTTP URL-címet.

   ![Logic Apps-végpont](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps esemény-előfizetés létrehozása

Az Azure Maps három eseménytípusok támogatja. Használhat egy pillantást az Azure Maps támogatott eseménytípusok [Itt](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Hozunk létre két különböző előfizetésben, adjon meg egy, a másik kilépési eseményeket.

Egy esemény-előfizetés létrehozásához a geokerítésen események adja meg az alábbi lépésekkel. Hasonló módon geokerítésen kilépési események fizethet.

1. Keresse meg az Azure Maps-fiók keresztül [a portál hivatkozás](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) , és válassza ki az események fülre.

   ![Azure Maps-események](./media/tutorial-geofence/events-tab.png)

2. Egy esemény-előfizetés létrehozásához válassza ki az esemény-előfizetés az események lapról.

   ![Azure Maps esemény-előfizetés](./media/tutorial-geofence/create-event-subscription.png)

3. Az esemény-előfizetés neve, és iratkozzon fel a Enter eseménytípus. Most válassza ki a Webhook "Végpont típusa", és másolja a logikai alkalmazás HTTP URL-végpontjának "Végpont"

   ![Esemény-előfizetés](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>A Geokerítésen API használata

A Geokerítésen API segítségével ellenőrizze, hogy egy **eszköz** (a készülék állapota részét képezi) belül vagy kívül a geokerítésen történik. Jobb megértése érdekében a geokerítésen API beolvasása. Hogy lekérdezése különböző helyeken, amelybe át lett helyezve egy adott berendezés idővel ellen. Az alábbi ábra mutatja be egy adott szerkezeti berendezés egyedi öt helyek **eszközazonosító** megfigyelt időrendi sorrendben. Mindhárom öt segítségével felmérheti a geokerítésen állapotának változása szemben az korlátját kilép, és adja meg. Állapotváltozás esetén a geokerítésen szolgáltatás elindít egy eseményt, amely a logikai alkalmazás az Event Grid által küldött. Ennek eredményeképpen az Operations manager a megfelelő enter és fogad értesítési kilép egy e-mailen keresztül.

> [!Note]
> A fenti forgatókönyv és viselkedése alapján azonos **eszközazonosító** úgy, hogy tükrözze a öt különböző helyeken az alábbi ábrán látható módon.

![Geokerítésen térkép](./media/tutorial-geofence/geofence.png)

A Postman alkalmazást a fent létrehozott ugyanabban a gyűjteményben új lap megnyitásához. Válassza ki a lapon jelentéskészítő GET HTTP metódus:

Az alábbiakban öt HTTP GET Geokerítések API-kérelem a különböző megfelelő hely koordinátái a készülék megfigyelt időrendi sorrendben. Minden kérelmet a válasz törzse követ.
 
1. 1. hely:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![1 Geokerítésen lekérdezés](./media/tutorial-geofence/geofence-query1.png)

   A fenti válasz tekinti meg, ha negatív közötti távolságot a fő geokerítésen azt jelenti, hogy a berendezés a geokerítésen belül van, és a pozitív a alwebhely geokerítésen az azt jelenti, hogy a alwebhely geokerítésen kívül. 

2. 2. hely: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geokerítésen lekérdezés 2](./media/tutorial-geofence/geofence-query2.png)

   Ha megtekinti a fenti JSON-válasz gondosan a készülék kívül esik a alwebhely, de a fő zárójel található. Ne indítsa el egy eseményt, és nem e-mailt küld.

3. 3. hely: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![3 Geokerítésen lekérdezés](./media/tutorial-geofence/geofence-query3.png)

   Állapotváltozás történt, és most már a készülék belül két fő és alwebhely geokerítések. Ez közzétesz egy eseményt, és a egy értesítést e-mailt küld az Operations Manager.

4. 4. hely: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![4 Geokerítésen lekérdezés](./media/tutorial-geofence/geofence-query4.png)

   Gondosan figyelje a megfelelő választ, megtalálja, hogy nincs esemény lekérdezi az itt közzétett annak ellenére, hogy a berendezés kilépett a alwebhely geokerítésen. Ha a GET kérelem a felhasználó megadott időpontban, láthatja, hogy a alwebhely geokerítésen képest ez idő lejárt, és a készülék még mindig a fő geokerítésen. Emellett megtekintheti a alwebhely geokerítésen alatt geometriai azonosítója `expiredGeofenceGeometryId` a válasz törzsében.


5. 5. hely:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![5 Geokerítésen lekérdezés](./media/tutorial-geofence/geofence-query5.png)

   Láthatja, hogy a berendezés elhagyta a fő szerkezeti hely geokerítésen. Azt közzétesz egy eseményt, súlyos megsértést, és a egy kritikus riasztási e-mailt küld az Operations Manager.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan állítható be a geokerítésen, fel kell töltenie azt az Azure Maps, az adatok feltöltése API-val Data service. Azt is megtanulta használata az Azure Maps események rács iratkozzon fel, és a geokerítésen események kezelésére. 

* Lásd: [tartalomtípusok kezelése az Azure Logic Appsben](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type), megtudhatja, hogyan hozhat létre egy összetettebb logika JSON elemzése a Logic Apps használatával.
* Többet is megtudni az Event Gridben eseménykezelőket, lásd: [események kezelők támogat az Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers).
