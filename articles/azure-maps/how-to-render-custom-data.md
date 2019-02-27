---
title: Egyéni adatok az Azure Maps-rasztertérkép renderelése |} A Microsoft Docs
description: Az Azure Maps-rasztertérkép egyéni adatok jelennek meg.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ddc051eb5f9638f7afec34db41c0e9d6e6d9d57d
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890062"
---
# <a name="render-custom-data-on-raster-map"></a>Egyéni adatok rasztertérkép megjelenítése

Ez a cikk bemutatja, hogyan használható [statikus kép szolgáltatás](https://docs.microsoft.com/rest/api/maps/render/getmapimage) a lemezkép összeállítás funkcióival a átfedések egy rasztertérkép felett. Kép a tartalomtípusból teszi vissza olyan egyéni gombostűk, a címkék és a geometriai lefedi a további adatok Raszter csempe lekéréséhez. Egyéni gombostűk, a címkék és a geometriai renderelni, a postman alkalmazást használjuk. Nyissa meg a Postman alkalmazást, kattintson az új |} Hozzon létre új válassza ki és a egy gyűjtemény vagy mappába mentéséhez, és kattintson a Mentés gombra.

Használjuk az Azure Maps [adatok Service API-k](https://docs.microsoft.com/rest/api/maps/data) tárolására és hangátfedések jelennek meg. 


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása 

Kövesse a jelen útmutató lépéseit, először szüksége [fiók és kulcsok kezelése](how-to-manage-account-keys.md) hozhat létre és kezelhet a fiók előfizetés S1 tarifacsomagot.

## <a name="render-pushpins-with-labels-and-custom-image"></a>A címkék és egyéni rendszerkép gombostűk megjelenítése

> [!Note]
> Ebben a példában egy S0 vagy S1 szint díjszabása az Azure Maps-fiók szükséges. 

Az Azure Maps-fiók S0 Termékváltozat csak egyetlen példányát támogatja a `pins` paramétert, így a felhasználók legfeljebb 5 gombostűk az URL-cím kérésére, egyéni rendszerkép megadott jelennek meg.

PIN-kódok a címkék és egyéni rendszerkép leküldéses megjelenítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Postman alkalmazást, és kattintson új |} Új létrehozása, és válassza ki a kérelmet. Renderelési gombostűk kérés nevét adja meg, válasszon ki egy gyűjtemény vagy mappába mentéséhez, és kattintson a Mentés gombra.
    
    ![Töltse fel a geokerítések Postman használatával](./media/tutorial-geofence/postman-new.png)

2. GET HTTP metódus a jelentéskészítő lapon válassza ki, és adja meg a következő URL-címet a GET-kérés.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Következő a válasz képet kap.

    ![egyéni gombostűk, a címkék megjelenítése](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Adatok beolvasása az Azure Maps adattárolás

> [!Note]
> Ebben a példában az S1 szint díjszabása az Azure Maps-fiók szükséges.

Az elérési út és a PIN-kód helyére vonatkozó információt is beszerezhető keresztül [adatok feltöltése API](https://docs.microsoft.com/rest/api/maps/mapdata/upload). Az elérési út és a PIN-kódok adatokat feltölteni az alábbi lépésekkel.

1. A Postman alkalmazást a fent létrehozott ugyanabban a gyűjteményben új lap megnyitásához. POST HTTP metódus a jelentéskészítő lapon válassza ki, és írja be a következő URL-cím a POST-kérés:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Kattintson a paraméterei, és adja meg a következő kulcs-érték párokat a POST kérelem URL-cím használható. Előfizetés-kulcs értékét cserélje le az Azure Maps előfizetési kulcs.
    
    ![Kulcs-érték paraméterek Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Kattintson a **törzs** majd válassza ki a bemeneti fájlt nyers formátumban, és válassza ki a JSON a bemeneti formátum, a legördülő listából. Adja meg a következő JSON fel kell tölteni adatokkal:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Kattintson a Küldés gombra, és tekintse át a válaszfejlécben. A location fejlécet tartalmazza az URI-t eléréséhez, vagy töltse le az adatokat későbbi használatra. Emellett tartalmaz egy egyedi `udId` a feltöltött adatok.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. Használja a értékét `udid` kapott az adatok feltöltése API-t a térképen funkciók jelennek meg. Ehhez nyissa meg egy új lapot a fent létrehozott ugyanabba a gyűjteménybe. GET HTTP metódus a jelentéskészítő lapon válassza ki, és írja be a következő URL-címet a GET-kérés:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. A válasz kép a következőhöz hasonlóan kell kinéznie:

    ![feltöltött adatok megjelenítése](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>A szín és átlátszatlanság sokszög megjelenítése

> [!Note]
> Ebben a példában az S1 szint díjszabása az Azure Maps-fiók szükséges.

Sokszög megjelenését a stílus dostupnosti használatával módosíthatja a [elérésiút-paraméterre](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage).

1. A Postman alkalmazást a fent létrehozott ugyanabban a gyűjteményben új lap megnyitásához. GET HTTP metódus a jelentéskészítő lapon válassza ki, és adja meg, hogy egy GET kéréssel, színét és átlátszatlanság sokszög előállítására a következő URL:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

A válasz kép a következőhöz hasonlóan kell kinéznie:

![leképezési átlátszatlan sokszög](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>Sokszög kör és leküldéses PIN-kód, az egyéni feliratok megjelenítése

> [!Note]
> Ebben a példában az S1 szint díjszabása az Azure Maps-fiók szükséges.

Meghatározhat gombostűk és azok a címkék nagyobb vagy kisebb az "sc" méretezési stílusú modifikátor használatával. Ez az egy nullánál nagyobb értéket. Az 1 érték, a standard méretezési csoport. 1-nél nagyobb érték lesz teheti a PIN-kódok nagyobb, és 1-nél kisebb érték fogja őket kisebb. További információ a stílus dostupnosti:, [statikus kép szolgáltatás elérési út paramétereihez](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

Sokszög kör és leküldéses PIN-kód, az egyéni címkék jelennek meg az alábbi lépésekkel:

1. A Postman alkalmazást a fent létrehozott ugyanabban a gyűjteményben új lap megnyitásához. GET HTTP metódus a jelentéskészítő lapon válassza ki, és írja be a következő URL-címet a GET-kérés:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

A válasz kép a következőhöz hasonlóan kell kinéznie:

![kör egyéni PIN-kódok a megjelenítése](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>További lépések

* Fedezze fel a [Azure Maps első térkép Image API](https://docs.microsoft.com/rest/api/maps/search) dokumentációját.
* Azure Maps adatszolgáltatás funkciókkal kapcsolatos további információkért lásd: [service dokumentációja](https://docs.microsoft.com/rest/api/maps/data).
