---
title: Geokerítésen létrehozása a Azure Maps használatával | Microsoft Docs
description: Geokerítésen beállítása Azure Maps használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 176cde77810a1c75cc18c351969a128fa78348af
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694924"
---
# <a name="set-up-a-geofence-by-using-azure-maps"></a>Geokerítésen beállítása Azure Maps használatával

Ez az oktatóanyag végigvezeti a geokerítésen a Azure Maps használatával történő beállításához szükséges alapismereteken. Az ebben az oktatóanyagban ismertetett forgatókönyv segít az építkezési vezetők számára a kijelölt építési területeken túlmutató lehetséges veszélyes berendezések figyelésében. Az építkezések költséges berendezéseket és előírásokat foglalnak magukban. Általában megköveteli, hogy a berendezés az építkezési helyen maradjon, és ne hagyjon engedély nélkül.

Azure Maps adatfeltöltő API-t fogjuk használni egy geokerítésen tárolásához, és az Azure Maps Geokerítésen API-t használva ellenőrizhetjük a berendezések helyét a geokerítésen viszonyítva. A geokerítésen eredményeinek továbbítására és a geokerítésen eredmények alapján történő bejelentésére a Azure Event Grid fogjuk használni.
További információ a Event Gridről: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Az adatfeltöltő API használatával töltse fel a geokerítésen területét a Azure Mapsba.
> *   Event Grid beállítása a geokerítésen-események kezelésére.
> *   Állítsa be a geokerítésen Events kezelőjét.
> *   Riasztások beállítása a geokerítésen eseményekre adott válaszként Logic Apps használatával.
> *   A Azure Maps geokerítésen szolgáltatás API-jai segítségével nyomon követheti, hogy egy építési eszköz az építkezési helyen belül van-e, vagy sem.


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Az oktatóanyag lépéseinek elvégzéséhez kövesse a [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) Azure Maps fiók előfizetése S1 árképzési szinten való létrehozásához című témakör útmutatását, és kövesse az [elsődleges kulcs beolvasása](./tutorial-search-location.md#getkey) a fiókhoz tartozó elsődleges előfizetési kulcs beszerzéséhez című témakör lépéseit.

## <a name="upload-geofences"></a>Geofences feltöltése

Az adatfeltöltő API-val az építkezési hely geokerítésen feltöltéséhez a Poster alkalmazást fogjuk használni. Ebben az oktatóanyagban feltételezzük, hogy van egy általános építkezési terület, amely egy olyan rögzített paraméter, amelyet az építőipari berendezés nem sért. A kerítés megsértése súlyos támadás, és a Operations Managernek jelentett. További kerítések optimalizált készlete használható, amely a teljes építkezési terület különböző építési területeit nyomon követheti ütemterv szerint. Tegyük fel, hogy a fő geokerítésen rendelkezik egy subsite1, amely egy beállított lejárati idővel rendelkezik, és ez idő után lejár. Igény szerint több beágyazott geofences is létrehozhat. Előfordulhat például, hogy a subsite1 az 1. és a 2. Alhely 4. hetében történik, ahol a munka az 5 – 7. héten zajlik. Az ilyen kerítések a projekt elején egyetlen adatkészletbe tölthetők be, és a szabályok az idő és a tér alapján követhetők nyomon. A geokerítésen adatformátumával kapcsolatos további információkért lásd: [Geokerítésen GeoJSON-adatok](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Az adatok Azure Maps szolgáltatásba való feltöltésével kapcsolatos további információkért lásd az [Adatfeltöltő API dokumentációját](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Nyissa meg a Poster alkalmazást, és kövesse az alábbi lépéseket az építkezési hely geokerítésen feltöltéséhez a Azure Maps, az adatfeltöltő API használatával.

1. Nyissa meg a Poster alkalmazást, és kattintson az új elemre | Hozzon létre egy újat, és válassza a kérelem lehetőséget. Adja meg a geokerítésen-adatok feltöltésére vonatkozó kérelem nevét, válassza ki azt a gyűjteményt vagy mappát, ahová menteni szeretné, majd kattintson a Mentés gombra.

    ![Geofences feltöltése a Poster használatával](./media/tutorial-geofence/postman-new.png)

2. Válassza a HTTP POST metódus lehetőséget a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a POST-kérelem létrehozásához.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Az URL-cím GEOJSON paramétere a feltöltött adatformátumot jelöli.

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket a post kérelem URL-címéhez kíván használni. Cserélje le az előfizetés-kulcs értékét a Azure Maps elsődleges előfizetési kulcsára.
   
    ![Kulcs-érték paraméterek Poster](./media/tutorial-geofence/postman-key-vals.png)

4. Kattintson a **törzs** elemre, majd válassza a nyers bemeneti formátum lehetőséget, majd a legördülő listából válassza a JSON lehetőséget. Adja meg a következő JSON-t a feltöltött adatként:

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

5. Kattintson a Küldés gombra, és tekintse át a válasz fejlécét. Sikeres kérelem esetén a **hely** fejléce tartalmazza az állapot URI-ját a feltöltési kérelem aktuális állapotának vizsgálatához. Az állapot URI-ja a következő formátumú lesz. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Másolja az állapot-URI-t `subscription-key` , és fűzze hozzá a paramétert a Azure Maps fiókja előfizetési kulcsának értékéhez. Az állapot URI-formátumának az alábbihoz hasonlónak kell lennie:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. A létrehozásához `udId` nyisson meg egy új fület a Poster alkalmazásban, majd válassza a http-módszer beolvasása lehetőséget a Builder (szerkesztő) lapon, és tegyen egy Get-kérést az állapot URI-ja Ha az adatok feltöltése sikeres volt, egy udId fog kapni a válasz törzsében. Másolja a udId későbbi használatra.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Eseménykezelő beállítása

A beléptetési és kilépési eseményekkel kapcsolatos Operations Manager értesítéséhez létre kell hozni egy eseménykezelőt, amely fogadja az értesítéseket.

Két [Logic apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) szolgáltatást hozunk létre az események kezeléséhez, beírásához és kilépéséhez. A Logic Apps az események által aktivált eseményindítókat is létrehozunk. Az ötlet a riasztások küldése, ebben az esetben a Operations Manager e-mail-címe, amikor a berendezések belépnek vagy kilépnek az építkezési helyről. Az alábbi ábra bemutatja, hogyan kell létrehozni egy logikai alkalmazást a geokerítésen esemény megadásához. Hasonlóképpen, létrehozhat egy másikat a kilépési eseményhez.
További információért tekintse meg az összes [támogatott eseménykezelőt](https://docs.microsoft.com/azure/event-grid/event-handlers) .

1. Logikai alkalmazás létrehozása Azure Portal

   ![Logic Apps létrehozása](./media/tutorial-geofence/logic-app.png)

2. Válasszon egy HTTP-kérelem triggert, majd válassza az "e-mail küldése" műveletet az Outlook-összekötőben
  
   ![Logic Apps séma](./media/tutorial-geofence/logic-app-schema.png)

3. Mentse a logikai alkalmazást a HTTP URL-cím végpontjának létrehozásához és a HTTP URL-cím másolásához.

   ![Logic Apps végpont](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps esemény-előfizetés létrehozása

A Azure Maps három eseménytípus használatát támogatja. Tekintse meg a Azure Maps támogatott eseménytípus [itt](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps). Két különböző előfizetést hozunk létre, egyet a bevitelhez és a másikat a kilépési eseményekhez.

Az alábbi lépéseket követve hozzon létre egy esemény-előfizetést a geokerítésen események beviteléhez. A geokerítésen kilépési eseményeihez hasonló módon fizethet elő.

1. Navigáljon a Azure Maps-fiókjához [ezen a portálon](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) keresztül, és válassza az események lapot.

   ![Események Azure Maps](./media/tutorial-geofence/events-tab.png)

2. Esemény-előfizetés létrehozásához válassza az események lap esemény-előfizetés elemét.

   ![Azure Maps esemény-előfizetés](./media/tutorial-geofence/create-event-subscription.png)

3. Nevezze el az események előfizetését, és fizessen elő az ENTER eseménytípus típusra. Most válassza ki a "végpont típusa" nevű webhookot, és másolja a logikai alkalmazás HTTP URL-végpontját a "Endpoint" értékre.

   ![Események előfizetése](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>A Geokerítésen API használata

A Geokerítésen API-val ellenőrizhető, hogy egy **eszköz** (a berendezés az állapot részét képezi-e) egy geokerítésen belül vagy kívül van-e. A geokerítésen GET API jobb megismeréséhez. Azt a különböző helyekről kérdezjük le, ahol egy adott berendezés az idő múlásával mozgott. Az alábbi ábra egy adott építőipari berendezés öt helyét mutatja be, amely egy egyedi **azonosítójú eszköz** , amely időrendi sorrendben van megfigyelve. A rendszer az alábbi öt helyet használja a geokerítésen megadására és a kilépési állapot változására a kerítésen. Ha az állapot módosul, a geokerítésen szolgáltatás elindít egy eseményt, amelyet a Event Grid a logikai alkalmazásnak továbbít. Ennek eredményeképpen a művelet kezelőjének e-mailben kell megkapnia a megfelelő Enter-vagy kilépési értesítést.

> [!Note]
> A fenti forgatókönyv és viselkedés ugyanazon az **eszköz-azonosítón** alapul, hogy az az alábbi ábrán látható módon tükrözze az öt különböző helyet.

![Geokerítésen Térkép](./media/tutorial-geofence/geofence.png)

A Poster alkalmazásban nyisson meg egy új fület a fent létrehozott gyűjteményben. Válassza a HTTP-módszer beolvasása lehetőséget a szerkesztő lapon:

A következő öt HTTP GET Geokerítések API-kérést, a berendezés eltérő hely koordinátáit pedig időrendi sorrendben megfigyelt módon. Minden kérelmet a válasz törzse követ.
 
1. 1\. hely:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![1\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query1.png)

   Ha megtekinti a fenti választ, a fő geokerítésen származó negatív távolság azt jelenti, hogy a berendezés a geokerítésen belül helyezkedik el, és az alhelyről geokerítésen pozitív érték azt jelenti, hogy az Alhely geokerítésen kívül esik. 

2. 2\. hely: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![2\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query2.png)

   Ha megtekinti az előző JSON-választ, a berendezés kívül esik az alhelyen, de a fő kerítésen belül van. Nem indít el eseményt, és nem küld e-mailt.

3. 3\. hely: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![3\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query3.png)

   Állapot-változás történt, és most a berendezés a fő-és Alhely geofences belül van. Ez egy eseményt tesz közzé, és egy értesítő e-mailt küld a Operations Managernak.

4. 4\. hely: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![4\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query4.png)

   A megfelelő válasz körültekintő betartásával azt is megfigyelheti, hogy egyetlen esemény sem jelenik meg itt annak ellenére, hogy a berendezés kilépett a alhelyhez tartozó geokerítésen. Ha megtekinti a felhasználó megadott időpontját a GET kérelemben, láthatja, hogy az alwebhely geokerítésen lejárt, és a berendezés még mindig a fő geokerítésen van. A válasz törzsében a alhelyhez tartozó geokerítésen `expiredGeofenceGeometryId` geometriájának azonosítóját is megtekintheti.


5. 5\. hely:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![5\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query5.png)

   Láthatja, hogy a berendezés elhagyta a fő építkezési hely geokerítésen. Egy eseményt tesz közzé, súlyos szabálysértést jelent, és kritikus riasztási e-mailt küld a Operations Managernak.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan állíthatja be a geokerítésen úgy, hogy feltölti a Azure Mapsba az adatfeltöltő API használatával. Azt is megtanulta, hogyan használhatja a Azure Maps Events Gridt a geokerítésen-események előfizetéséhez és kezeléséhez. 

* Tekintse meg a [Azure Logic apps a tartalomtípusok kezelése](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)című témakört, amelyből megtudhatja, hogyan elemezheti a JSON-t egy összetettebb logika létrehozásához Logic Apps használatával.
* Ha többet szeretne megtudni a Event Grid-eseménykezelőről, tekintse meg a [Event Grid támogatott események kezelői](https://docs.microsoft.com/azure/event-grid/event-handlers)című témakört.
