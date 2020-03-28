---
title: 'Oktatóanyag: Hozzon létre egy geokerítést, és kövesse nyomon az eszközöket a térképen | Microsoft Azure Maps'
description: Ismerje meg, hogyan állíthat be geokerítést, és hogyan követheti nyomon a geokerítéshez viszonyított eszközöket a Microsoft Azure Maps térbeli szolgáltatás használatával.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 126829f12d71e40511c26e781cb191988c1d031e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333862"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Oktatóanyag: Geokerítés beállítása az Azure Maps használatával

Ez az oktatóanyag végigvezeti a geokerítés azure Maps használatával történő beállításának alaplépéseit. Vegyük ezt a forgatókönyvet, egy építési Site Manager figyelemmel kell kísérnie a potenciális veszélyes berendezések. Az igazgatónak gondoskodnia kell arról, hogy a berendezés a kiválasztott teljes építési területeken maradjon. Ez a teljes építési terület egy kemény paraméter. A szabályzatok megkövetelik, hogy a berendezések e paraméteren belül maradjanak, és a szabálysértéseket jelenteni kell az Operations Managernek.  

Az adatok feltöltése API-t használunk egy geokerítés tárolására, és a Geofence API-t használjuk a berendezés helyének a geokerítéshez viszonyított ellenőrzéséhez. Az Adatfeltöltés API és a Geofence API egyaránt az Azure Mapsből származik. Az Azure Event Grid segítségével is streameljük a geokerítés eredményeit, és a geokerítés-eredmények alapján értesítést állítunk be. Az Event Grid ről az [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview)ben olvashat bővebben.

Ebben a tutorial azt, hogyan:

> [!div class="checklist"]
> * Geokerítés-terület feltöltése az Azure Maps adatszolgáltatásban az Adatfeltöltés API használatával.
> *   Állítson be egy eseményrácsot a geokerítés-események kezelésére.
> *   Geokerítés eseménykezelő beállítása.
> *   Állítsa be a riasztásokat a Logic Apps használatával történő geokerítés-eseményekre válaszul.
> *   Az Azure Maps geokerítés szolgáltatás API-jaisegítségével nyomon követheti, hogy egy építési eszköz az építkezésen belül van-e vagy sem.


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) című, Azure Maps-fiók-előfizetés s1-es tarifacsomaggal való létrehozásához kövesse az utasításokat. Az [elsődleges kulcs beolvasása](quick-demo-map-app.md#get-the-primary-key-for-your-account) című lépés bemutatja, hogyan szerezheti be a fiók elsődleges kulcsát. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.

## <a name="upload-geofences"></a>Geokerítések feltöltése

Feltételezzük, hogy a fő geokerítés alwebhely1, amely egy meghatározott lejárati idő. Több beágyazott geokerítést hozhat létre a követelményeknek megfelelően. Ezek a kerítések lehet használni, hogy nyomon kövesse a különböző építési területek a teljes építési területen. Az 1. aloldal 2 lehet, ahol a munka zajlik a héten 5-7. Minden ilyen kerítés ek lehet betölteni, mint egy adatkészlet a projekt elején. Ezek a kerítések az idő és a tér alapján történő szabályok nyomon követésére szolgálnak. 

Az építkezés geokerítésének feltöltéséhez az Adatfeltöltés API-val a postás alkalmazást használjuk. Telepítse a [postás alkalmazást,](https://www.getpostman.com/) és készítsen egy ingyenes fiókot. 

A Postman alkalmazás telepítése után kövesse az alábbi lépéseket az építési terület geokerítésének feltöltéséhez az Azure Maps adatfeltöltési API használatával.

1. A Postman alkalmazás megnyitása és az új | Hozzon létre újat, és válassza a Kérés lehetőséget. Adja meg a Geokerítés-adatok feltöltése kérelem nevét, jelölje ki azt a gyűjteményt vagy mappát, amelybe menteni szeretné őket, majd kattintson a Mentés gombra.

    ![Geokerítések feltöltése postás használatával](./media/tutorial-geofence/postman-new.png)

2. Válassza a POST HTTP metódus lehetőséget a szerkesztő lapon, és írja be a következő URL-címet a POST kéréshez.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Az URL-elérési út GEOJSON paramétere a feltöltendő adatok adatformátumát jelöli.

3. Kattintson a **Params**gombra, és adja meg a következő kulcs/érték párokat, amelyeket a POST kérés URL-címéhez használni kell. Cserélje le a {subscription-key} alkalmazást az Azure Maps-előfizetési kulcsra, más néven elsődleges kulcsra.
   
    ![A feltöltési adatok paraméterei (geokerítés) a Postmanben](./media/tutorial-geofence/postman-key-vals.png)

4. Kattintson a **Törzs** gombra, majd válassza ki a nyers beviteli formátumot, és válassza a JSON-t a legördülő listából. Adja meg a következő JSON-t a feltöltendő adatokként:

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

5. Kattintson a Küldés gombra, és tekintse át a válaszfejlécet. Sikeres kérés esetén a **Hely** fejléce tartalmazza az állapotURI-t. Az állapotURI formátuma a következő. A uploadStatusId érték nem { }. Általános gyakorlat, hogy a { } használatával olyan értékeket jelenít meg, amelyeket a felhasználónak meg kell adnia, vagy olyan értékeket, amelyek különbözőek a különböző felhasználók számára.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Másolja az állapotURI-t, és fűzze hozzá az előfizetési kulcsot. Az állapotURI formátumnak az alábbihoz hasonlónak kell lennie. Figyelje meg, hogy az alábbi formátumban módosítania kell a {előfizetés-kulcs}, nem tartalmazza a { }, az előfizetési kulcs.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. A beszerezni a `udId`, nyisson meg egy új lapot a Postman alkalmazásban, és válassza a HTTP-módszer beírása lehetőséget a szerkesztő lapon. Ha az adatok feltöltése sikeres volt, megkapja az udId a választörzsben. Másolja az udId-et későbbi használatra.

   ```JSON
   {
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udId}?api-version=1.0"
   }
   ```

## <a name="set-up-an-event-handler"></a>Eseménykezelő beállítása

Ebben a szakaszban létrehozunk egy eseménykezelőt, amely értesítéseket kap. Ennek az eseménykezelőnek értesítenie kell az Operations Managert a berendezések be- és kilépési eseményeiről.

Két [Logic Apps-szolgáltatást](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) készítünk a belépési és kilépési események kezeléséhez. Amikor az események a Logic Apps eseményindító, további események sorrendben aktiválódnak. Az ötlet az, hogy küldjön riasztásokat, ebben az esetben e-maileket, az Operations Manager. Az alábbi ábra egy logikai alkalmazás létrehozását mutatja be a geokerítés beírási eseményhez. Hasonlóképpen létrehozhat egy másikat a kilépési eseményhez. További információkért tekintse meg az összes [támogatott eseménykezelőt.](https://docs.microsoft.com/azure/event-grid/event-handlers)

1. Hozzon létre egy logikai alkalmazást az Azure Portalon. Válassza ki a logikai alkalmazást az Azure Marketplace-en. Ezután válassza a **Létrehozás** gombot.

   ![Azure Logic-alkalmazások létrehozása a geokerítés-események kezeléséhez](./media/tutorial-geofence/logic-app.png)

2. A Logikai alkalmazás beállítások menüjében keresse meg a **Logic App Designer**

3. Jelöljön ki egy HTTP-kérelem-eseményindítót, majd válassza az "Új lépés" lehetőséget. Az Outlook-összekötőben válassza az "e-mail küldése" lehetőséget műveletként
  
   ![Logikai alkalmazások sémája](./media/tutorial-geofence/logic-app-schema.png)

4. Töltse ki az e-mail küldéséhez tartozó mezőket. Hagyja el a HTTP URL-t, akkor automatikusan generál, miután rákattint a "mentés"

   ![Logikai alkalmazások végpontjának létrehozása](./media/tutorial-geofence/logic-app-endpoint.png)

5. Mentse a logikai alkalmazást a HTTP URL-végpont létrehozásához és a HTTP URL másolásához.

## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps-esemény-előfizetés létrehozása

Az Azure Maps három eseménytípust támogat. Az Azure Maps által támogatott [eseménytípusokat itt](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)olvashatja el. Két különböző esemény-előfizetésre van szükségünk, az egyik az enter eseményre, a másik a kilépési eseményekre.

Az alábbi lépéseket követve hozzon létre egy esemény-előfizetést a geokerítés események beírásához. A geokerítés kilépési eseményeire is hasonló módon előfizethet.

1. Nyissa meg az Azure Maps-fiókját. Az irányítópulton válassza az Előfizetések lehetőséget. Kattintson az előfizetés nevére, és válassza ki az **eseményeket** a beállítások menüből.

   ![Navigálás az Azure Maps-fiók eseményeire](./media/tutorial-geofence/events-tab.png)

2. Esemény-előfizetés létrehozásához válassza az Esemény-előfizetés lehetőséget az események lapon.

   ![Azure Maps-esemény-előfizetés létrehozása](./media/tutorial-geofence/create-event-subscription.png)

3. Nevezze el az esemény-előfizetést, és iratkozzon fel az Enter eseménytípusra. Most válassza a Web Hook lehetőséget "Végponttípusként". Kattintson a "Végpont kiválasztása" gombra, és másolja a Logic App HTTP URL-végpontját a "{Endpoint}" fájlba.

   ![Az Azure Maps esemény-előfizetésrészletei](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geokerítés API használata

A Geofence API segítségével ellenőrizheti, hogy egy **eszköz**, ebben az esetben a berendezés, belül vagy kívül geokerítés. Lehetővé teszi a Geofence GET API lekérdezését különböző helyekre, ahol egy adott berendezés az idő múlásával költözött. Az alábbi ábra öt helyszínt mutat be öt építőipari berendezéssel. 

> [!Note]
> A forgatókönyv és a viselkedés ugyanazon az **eszközazonosítón** alapul, így az az alábbi ábrán látható öt különböző helyet tükrözi.

A "deviceId" egy egyedi azonosító, amelyet a GET-kérelemben ad meg az eszközszámára, amikor a helyét kérdezi. Amikor aszinkron kérést ad a **keresési geokerítés - GET API,** a "deviceId" segít az adott eszköz geokerítés-eseményeinek közzétételében, a megadott geokerítéshez képest. Ebben az oktatóanyagban egyedi "deviceId" azonosítóval aszinkron kéréseket intéztünk az API-hoz. A kérelmek a bemutató történik időrendi sorrendben, mint az ábrán. A válaszban az "isEventPublished" tulajdonság közzétételre kerül, amikor egy eszköz belép vagy kilép a geokerítésből. Nem kell regisztrálni a készüléket, hogy kövesse ezt az oktatóanyagot.

Nézzünk vissza a diagramra. Mind az öt hely a geokerítés be- és kilépési állapotának a kerítéshez való változásának értékelésére szolgál. Ha állapotváltozás történik, a geokerítés szolgáltatás eseményindító, amely az Eseményrács által küldött logikai alkalmazás. Ennek eredményeképpen a művelet vezetője e-mailben kapja meg a megfelelő belépési vagy kilépési értesítést.

![Geokerítés-térkép az Azure Mapsben](./media/tutorial-geofence/geofence.png)

A Postman alkalmazásban nyisson meg egy új lapot a fent létrehozott gyűjteményben. A szerkesztő lapon válassza a HTTP get metódus lehetőséget:

Az alábbiakban öt HTTP GET Geofencing API-kérelmek, különböző helykoordinátáia a berendezés. A koordináták időrendi sorrendben vannak megfigyelhetők. Minden kérelmet a választörzs követ.
 
1. 1. helyszín:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geokerítés-lekérdezés 1](./media/tutorial-geofence/geofence-query1.png)

   A fenti válaszban a fő geokerítéstől való negatív távolság azt jelenti, hogy a berendezés a geokerítésen belül van. Az alterület geokerítésétől való pozitív távolság azt jelenti, hogy a berendezés az altelephely geokerítésén kívül van. 

2. 2. helyszín: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geokerítés-lekérdezés 2](./media/tutorial-geofence/geofence-query2.png)

   Ha megnézed az előző JSON választ gondosan a berendezés kívül van az aloldalon, de ez belül a fő kerítés. Nem esemény aktiválódik, és nem küld e-mailt.

3. Helyszín 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geokerítés-lekérdezés 3](./media/tutorial-geofence/geofence-query3.png)

   Állapotváltozás történt, és most a berendezés a fő- és altelephelyi geokerítéseken belül van. Ez a módosítás egy esemény közzétételét eredményezi, és egy értesítő e-mailt küld az Operations Managernek.

4. 4. helyszín: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geokerítés-lekérdezés 4](./media/tutorial-geofence/geofence-query4.png)

   Ha gondosan megfigyeljük a megfelelő választ, megfigyelheti, hogy itt egyetlen esemény sem kerül közzétételre annak ellenére, hogy a berendezés kilépett az alwebhely geokerítéséből. Ha megnézi a felhasználó megadott idejét a GET kérelemben, láthatja, hogy az alwebhely geokerítése ez úttal lejárt. A berendezés még mindig a fő geokerítésen van. A `expiredGeofenceGeometryId` választörzsalatt az alwebhely geokerítésének geometriaazonosítóját is láthatja.


5. 5. helyszín:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geokerítés-lekérdezés 5](./media/tutorial-geofence/geofence-query5.png)

   Láthatjuk, hogy a berendezés elhagyta a fő építési terület geofence. Egy esemény közzé, és egy figyelmeztető e-mailt küld az Operations Manager.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta: hogyan állíthatja be a geokerítést az Azure Maps és az adatok szolgáltatásban az Adatfeltöltés API használatával való feltöltésével. Azt is megtanulta, hogyan használhatja az Azure Maps Events Grid et a geokerítés-eseményekre való feliratkozáshoz és azok kezeléséhez. 

* [Tekintse meg a tartalomtípusok kezelése az Azure Logic Apps,](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)megtudhatja, hogyan használhatja a Logic Apps a JSON elemzésével összetettebb logika létrehozásához.
* Ha többet szeretne tudni az Eseményrács eseménykezelőiről, olvassa el [a Támogatott eseménykezelők az Eseményrácsban című témakört.](https://docs.microsoft.com/azure/event-grid/event-handlers)
