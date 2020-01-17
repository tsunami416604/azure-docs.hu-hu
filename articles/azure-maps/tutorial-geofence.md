---
title: 'Oktatóanyag: geokerítésen létrehozása és eszközök nyomon követése térképeken | Microsoft Azure térképek'
description: Megtudhatja, hogyan állíthat be geokerítésen, és hogyan követheti nyomon az eszközöket a geokerítésen képest a Microsoft Azure Maps térbeli szolgáltatás használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a88f03adab3beaea75ec2fa9a1c6f59b09739025
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76153138"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Oktatóanyag: geokerítésen beállítása Azure Maps használatával

Ez az oktatóanyag végigvezeti a geokerítésen a Azure Maps használatával történő beállításához szükséges alapismereteken. Gondolja át ezt az esetet, ha egy építési Site Manager a lehetséges veszélyes berendezések figyelésére van lehetőség. A kezelőnek biztosítania kell, hogy a berendezés a kiválasztott általános építési területeken maradjon. Ez az általános felépítési munkaterület egy rögzített paraméter. A szabályozások megkövetelik, hogy a berendezések ezen a paraméteren belül maradjanak, és a rendszer a Operations Manager.  

Az adatfeltöltő API-val tárolunk egy geokerítésen, és a Geokerítésen API használatával ellenőrizhetjük a berendezések helyét a geokerítésen viszonyítva. Az adatfeltöltő API és a Geokerítésen API is a Azure Maps. A geokerítésen eredményeinek továbbítására és a geokerítésen eredmények alapján történő bejelentésére is a Azure Event Grid használjuk. További információ a Event Gridről: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).

Ebben az oktatóanyagban a következőket ismertetjük:

> [!div class="checklist"]
> * Az adatfeltöltő API használatával töltse fel a geokerítésen területét a Azure Mapsba.
> *   Event Grid beállítása a geokerítésen-események kezelésére.
> *   Állítsa be a geokerítésen Events kezelőjét.
> *   Riasztások beállítása a geokerítésen eseményekre adott válaszként Logic Apps használatával.
> *   A Azure Maps geokerítésen szolgáltatás API-jai segítségével nyomon követheti, hogy egy építési eszköz az építkezési helyen belül van-e, vagy sem.


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) Azure Maps fiók előfizetésének S1 árképzési szinten való létrehozásához című témakör utasításait. Az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakörben bemutatjuk, hogyan kérheti le a fiók elsődleges kulcsát. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

## <a name="upload-geofences"></a>Geofences feltöltése

Feltételezzük, hogy a fő geokerítésen a subsite1, amely a beállított lejárati idővel rendelkezik. Igény szerint több beágyazott geofences is létrehozhat. Ezek a kerítések a teljes építkezési terület különböző építési területeinek nyomon követésére használhatók. Előfordulhat például, hogy a subsite1 az ütemterv 1. és 4. hetében zajlik. a subsite2 lehet, hogy a munka az 5. és 7. hét között zajlik. Az ilyen kerítések a projekt elején egyetlen adatkészletbe tölthetők be. Ezek a kerítések a szabályok idő és hely alapján történő nyomon követésére szolgálnak. 

Az adatfeltöltő API-val az építkezési hely geokerítésen feltöltéséhez használjuk a Poster alkalmazást. Telepítse a [Poster alkalmazást](https://www.getpostman.com/) , és hozzon ki egy ingyenes fiókot. 

Miután telepítette a Poster alkalmazást, kövesse az alábbi lépéseket az építkezési hely geokerítésen feltöltéséhez a Azure Maps, az adatfeltöltő API használatával.

1. Nyissa meg a Poster alkalmazást, és kattintson az új elemre | Hozzon létre egy újat, és válassza a kérelem lehetőséget. Adja meg a geokerítésen-adatok feltöltésére vonatkozó kérelem nevét, válassza ki azt a gyűjteményt vagy mappát, ahová menteni szeretné, majd kattintson a Mentés gombra.

    ![Geofences feltöltése a Poster használatával](./media/tutorial-geofence/postman-new.png)

2. Válassza a HTTP POST metódus lehetőséget a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a POST-kérelem létrehozásához.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Az URL-cím GEOJSON paramétere a feltöltött adatformátumot jelöli.

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket a post kérelem URL-címéhez kíván használni. Cserélje le az {előfizetés-Key}-t a Azure Maps előfizetési kulcsára, más néven az elsődleges kulcsra.
   
    ![Az adatok feltöltésének paraméterei (geokerítésen) a Poster-ben](./media/tutorial-geofence/postman-key-vals.png)

4. Kattintson a **törzs** elemre, majd válassza ki a nyers bemeneti formátumot, és a legördülő listából válassza a JSON lehetőséget. Adja meg a következő JSON-t a feltöltött adatként:

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

5. Kattintson a Küldés gombra, és tekintse át a válasz fejlécét. Sikeres kérelem esetén a **Location** fejléc tartalmazza az állapot URI-ját. Az állapot URI-ja a következő formátumú. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Másolja ki az állapot-URI-t, és fűzze hozzá az előfizetés-kulcsot. Az állapot URI-formátumának az alábbihoz hasonlónak kell lennie. Figyelje meg, hogy az alábbi formátumban megváltoztathatja a {előfizetés-Key}, beleértve a {} előfizetési kulcsot is.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. A `udId`beszerzéséhez nyisson meg egy új fület a Poster alkalmazásban, majd válassza a HTTP-módszer beolvasása lehetőséget a Builder (szerkesztő) lapon. a GET kérelem az előző lépésben megadott status URI-n keresztül végezhető el. Ha az adatok feltöltése sikeres volt, a udId fog megjelenni a válasz törzsében. Másolja a udId későbbi használatra.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>Eseménykezelő beállítása

Ebben a szakaszban egy, az értesítéseket fogadó eseménykezelőt hozunk létre. Az eseménykezelőnek értesítenie kell a Operations Manager az egyes berendezések belépési és kilépési eseményeiről.

Két [Logic apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) -szolgáltatást teszünk elérhetővé az események kezelésére, beírására és kilépésére. A Logic Apps trigger eseményeinél több esemény is aktiválódik a sorozatban. Az a gondolat, hogy a riasztásokat, ebben az esetben e-maileket küld a Operations Manager. Az alábbi ábra bemutatja, hogyan kell létrehozni egy logikai alkalmazást a geokerítésen esemény megadásához. Hasonlóképpen, létrehozhat egy másikat a kilépési eseményhez. További információért tekintse meg az összes [támogatott eseménykezelőt](https://docs.microsoft.com/azure/event-grid/event-handlers) .

1. Logikai alkalmazás létrehozása Azure Portal

   ![Azure Logic Apps létrehozása a geokerítésen-események kezeléséhez](./media/tutorial-geofence/logic-app.png)

2. A logikai alkalmazás beállítások menüjében navigáljon a **Logic app Designer alkalmazáshoz** .

3. Válasszon ki egy HTTP-kérelem triggert, majd válassza az "új lépés" lehetőséget. Az Outlook-összekötőben válassza az "e-mail küldése" műveletet
  
   ![Logic Apps séma](./media/tutorial-geofence/logic-app-schema.png)

4. Adja meg az e-mailek küldésére szolgáló mezőket. Hagyja meg a HTTP URL-címet, a rendszer automatikusan létrehozza a Mentés gombra kattintás után.

   ![Logic Apps végpont létrehozása](./media/tutorial-geofence/logic-app-endpoint.png)

5. Mentse a logikai alkalmazást a HTTP URL-cím végpontjának létrehozásához és a HTTP URL-cím másolásához.

## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps esemény-előfizetés létrehozása

A Azure Maps három eseménytípus használatát támogatja. Tekintse meg a Azure Maps támogatott eseménytípus [itt] (https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps. Két különböző esemény-előfizetésre van szükségünk, egyet az ENTER eseményhez és egyet a kilépési eseményekhez.

Az alábbi lépéseket követve hozzon létre egy esemény-előfizetést a geokerítésen események beviteléhez. A geokerítésen kilépési eseményeihez hasonló módon fizethet elő.

1. Navigáljon a Azure Maps-fiókjához. Az irányítópulton válassza az előfizetések lehetőséget. Kattintson az előfizetés nevére, és válassza az **események** lehetőséget a beállítások menüben.

   ![Azure Maps-fiók eseményeinek megkeresése](./media/tutorial-geofence/events-tab.png)

2. Esemény-előfizetés létrehozásához válassza az események lap esemény-előfizetés elemét.

   ![Azure Maps esemény-előfizetés létrehozása](./media/tutorial-geofence/create-event-subscription.png)

3. Nevezze el az események előfizetését, és fizessen elő az ENTER eseménytípus típusra. Most válassza a webhook lehetőséget a "végpont típusa" elemre. Kattintson a "végpont kiválasztása" elemre, és másolja a logikai alkalmazás HTTP URL-végpontját a következőre: {Endpoint}

   ![Azure Maps események előfizetés részletei](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>A Geokerítésen API használata

A Geokerítésen API-val ellenőrizhető, hogy egy **eszköz**(ebben az esetben a berendezés) egy geokerítésen belül vagy kívül van-e. Lehetővé teszi, hogy lekérdezze a Geokerítésen GET API-t a különböző helyekről, ahol egy adott berendezés az idő múlásával mozgott. Az alábbi ábrán öt olyan helyet mutatunk be, amelyek öt építőipari berendezéssel rendelkeznek. 

> [!Note]
> A forgatókönyv és a viselkedés ugyanazon az eszköz- **azonosítón** alapul, hogy az az alábbi ábrán látható módon tükrözze az öt különböző helyet.

A "deviceId" egy egyedi azonosító, amelyet a GET kérelemben az eszköz számára biztosít a helyének lekérdezése során. Amikor aszinkron kérelmet küld a **Search GEOKERÍTÉSEN API-** hoz, a "deviceId" segít az eszköz geokerítésen-eseményeinek a megadott geokerítésen viszonyított közzétételében. Ebben az oktatóanyagban aszinkron kéréseket készítettünk az API-hoz egy egyedi "deviceId" használatával. Az oktatóanyagban szereplő kérelmek kronológiai sorrendben történnek, ahogy az ábrán is látható. A válasz "isEventPublished" tulajdonsága közzé lesz téve, amikor egy eszköz belép vagy kilép a geokerítésen. Ehhez az oktatóanyaghoz nem kell regisztrálnia az eszközt.

Lehetővé teszi, hogy visszatekintse a diagramot. A rendszer az alábbi öt helyet használja a geokerítésen megadására és a kilépési állapot változására a kerítésen. Ha az állapot módosul, a geokerítésen szolgáltatás elindít egy eseményt, amelyet a Event Grid a logikai alkalmazásnak továbbít. Ennek eredményeképpen a művelet kezelője e-mailben megkapja a megfelelő Enter-vagy kilépési értesítést.

![Geokerítésen-leképezés Azure Maps](./media/tutorial-geofence/geofence.png)

A Poster alkalmazásban nyisson meg egy új fület a fent létrehozott gyűjteményben. Válassza a HTTP-módszer beolvasása lehetőséget a szerkesztő lapon:

A következő öt HTTP GET Geokerítések API-kérés a berendezés különböző földrajzi koordinátáival. A koordinátákat időrendi sorrendben kell megfigyelni. Minden kérelmet a válasz törzse követ.
 
1. 1\. hely:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![1\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query1.png)

   A fenti válaszban a fő geokerítésen származó negatív távolság azt jelenti, hogy a berendezés a geokerítésen belül van. A alhelyről geokerítésen pozitív távolság azt jelenti, hogy a berendezés kívül esik a alhelyen geokerítésen. 

2. 2\. hely: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![2\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query2.png)

   Ha megtekinti az előző JSON-választ, a berendezés kívül esik az alhelyen, de a fő kerítésen belül van. A rendszer nem indít el eseményt, és nem küld e-mailt.

3. 3\. hely: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![3\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query3.png)

   Állapot-változás történt, és most a berendezés a fő-és Alhely geofences belül van. Ez a változás egy esemény közzétételét eredményezi, és egy értesítő e-mailt küld a rendszer a Operations Managernak.

4. 4\. hely: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![4\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query4.png)

   A megfelelő válasz körültekintő betartásával azt is megfigyelheti, hogy egyetlen esemény sem jelenik meg itt annak ellenére, hogy a berendezés kilépett a alhelyhez tartozó geokerítésen. Ha megtekinti a felhasználó megadott időpontját a GET kérelemben, láthatja, hogy a webhely geokerítésen érvényessége lejárt. A berendezés még mindig a fő geokerítésen van. A válasz törzsében a `expiredGeofenceGeometryId` alatt található Alhely geokerítésen geometriájának AZONOSÍTÓját is megtekintheti.


5. 5\. hely:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![5\. geokerítésen-lekérdezés](./media/tutorial-geofence/geofence-query5.png)

   Láthatja, hogy a berendezés elhagyta a fő építkezési hely geokerítésen. Egy esemény kerül közzétételre, és a rendszer értesítő e-mailt küld a Operations Managernak.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan állíthatja be a geokerítésen úgy, hogy az adatfeltöltő API használatával feltölti azt a Azure Maps és az adatszolgáltatásba. Azt is megtanulta, hogyan használhatja a Azure Maps Events Gridt a geokerítésen-események előfizetéséhez és kezeléséhez. 

* Tekintse meg a [Azure Logic apps a tartalomtípusok kezelése](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)című témakört, amelyből megtudhatja, hogyan elemezheti a JSON-t egy összetettebb logika létrehozásához Logic Apps használatával.
* Ha többet szeretne megtudni a Event Grid-eseménykezelőről, tekintse meg a [Event Grid támogatott események kezelői](https://docs.microsoft.com/azure/event-grid/event-handlers)című témakört.
