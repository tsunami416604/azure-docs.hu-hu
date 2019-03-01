---
title: Egyéni adatok az Azure Maps-egy rasztertérkép renderelése |} A Microsoft Docs
description: Az Azure Maps-egy rasztertérkép egyéni adatok jelennek meg.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 982a8b6ed67b955fcb68006072b67f7c59f29688
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010157"
---
# <a name="render-custom-data-on-a-raster-map"></a>Egyéni adatok egy rasztertérkép megjelenítése

Ez a cikk azt ismerteti, hogyan használható a [statikus kép szolgáltatás](https://docs.microsoft.com/rest/api/maps/render/getmapimage) a lemezkép összeállítás funkcióival a átfedések egy rasztertérkép felett. Kép a tartalomtípusból lehetővé teszi olyan egyéni gombostűk, a címkék és a geometriai lefedi a további adatok vissza Raszter csempe lekéréséhez.

Egyéni gombostűk, a címkék és a geometriai átfedések renderelni, használhatja a Postman alkalmazást. Használhatja az Azure Maps [adatok Service API-k](https://docs.microsoft.com/rest/api/maps/data) tárolására és hangátfedések jelennek meg.


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

Hajtsa végre az ebben a cikkben ismertetett, szüksége lesz a [létrehozása az Azure Maps-fiók](how-to-manage-account-keys.md) az S1 tarifacsomagot.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>A címkék és a egy egyéni rendszerkép gombostűk megjelenítése

> [!Note]
> Ebben a szakaszban az eljárást egy S0 vagy az S1 tarifacsomagot az Azure Maps-fiók szükséges.

Az Azure Maps-fiók S0 csomag támogatja a csak egy példány lehet a `pins` paraméter. Ez lehetővé teszi, hogy jelennek meg legfeljebb öt gombostűk URL-címe a kérelemben, egyéni rendszerképpel.

A címkék és a egy egyéni rendszerkép gombostűk jelennek meg, hajtsa végre ezeket a lépéseket:

1. Hozzon létre egy gyűjteményt, amely tárolja a kérelmeket. Válassza ki a Postman alkalmazást **új**. Az a **hozzon létre új** ablakban válassza **gyűjtemény**. A gyűjtemény neve, és válassza ki a **létrehozás** gombra. 

2. A kérelem létrehozásához válassza **új** újra. Az a **hozzon létre új** ablakban válassza **kérelem**. Adjon meg egy **kérelem neve** számára a gombostűk válassza ki a gyűjteményt, mint a helyet, ahol a mentse a kérést, majd válassza az előző lépésben létrehozott **mentése**.
    
    ![A Postmanben a kérelem létrehozása](./media/tutorial-geofence/postman-new.png)

3. Az első HTTP-metódus a jelentéskészítő lapon válassza ki, és adja meg a hozzon létre egy GET kérelmet a következő URL-CÍMRE.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Íme a képernyőképet:

    ![Egy egyéni Rajzszög címkével](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Adatok beolvasása az Azure Maps adattárolás

> [!Note]
> Ebben a szakaszban az eljárás igényli az Azure Maps-fiók az S1 tarifacsomagot.

Az elérési út és a PIN-kód helyére vonatkozó információt is beszerezhető használatával a [adatok feltöltése API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Az elérési út és a PIN-kódok adatokat feltölteni az alábbi lépésekkel.

1. A Postman alkalmazást nyissa meg a gyűjteményben az előző szakaszban létrehozott egy új lap. A POST HTTP metódus builder lapon válassza ki, és adja meg a következő URL-cím a POST-kérés:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Az a **paraméterei** lapra, adja meg a következő kulcs-érték párok, a POST-kérelem URL-Címében használt. Cserélje le a `subscription-key` az Azure Maps előfizetési kulcs-érték.
    
    ![A Postmanben kulcs/érték paraméterei](./media/how-to-render-custom-data/postman-key-vals.png)

3. Az a **törzs** lapon válassza ki a bemeneti fájlt nyers formátumban, majd válassza a JSON a bemeneti formátum, a legördülő listából. Adja meg ezt a JSON-adatként tölthető fel:
    
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

4. Válassza ki **küldése** , és tekintse át a válaszfejlécben. A location fejlécet tartalmazza az URI-t való hozzáférés, vagy töltse le az adatokat későbbi használatra. Emellett tartalmaz egy egyedi `udId` a feltöltött adatok.  

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```

5. Használja a `udId` érték érkezett az adatok feltöltése API-t a térképen funkciók jelennek meg. Ehhez nyissa meg a gyűjteményt az előző szakaszban létrehozott egy új lap. Az első HTTP-metódus a jelentéskészítő lapon válassza ki, és adja meg az URL-címet a GET-kérés:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Íme a képernyőképet:

    ![Adatok beolvasása az Azure Maps adattárolás](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Szín- és átlátszatlanság sokszög megjelenítése

> [!Note]
> Ebben a szakaszban az eljárás igényli az Azure Maps-fiók az S1 tarifacsomagot.


Sokszög megjelenését a stílus dostupnosti használatával módosíthatja a [elérésiút-paraméterre](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. A Postman alkalmazást nyissa meg a korábban létrehozott gyűjteményt egy új lapon. Az első HTTP-metódus a jelentéskészítő lapon válassza ki, és adja meg a konfigurálása egy GET kéréssel, színét és átlátszatlanság sokszög előállítására a következő URL:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Íme a képernyőképet:

![Egy nem átlátszó sokszög megjelenítése](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Kör és egyéni címkével rendelkező gombostűk megjelenítéséhez

> [!Note]
> Ebben a szakaszban az eljárás igényli az Azure Maps-fiók az S1 tarifacsomagot.


Meghatározhat gombostűk és azok a címkék kisebb vagy nagyobb használatával a `sc` méretezési stílusú modifikátor. Ez a módosító, amely csak nullánál nagyobb értéket vesz fel. Az 1 érték, a standard méretezési csoport. 1-nél nagyobb érték lesz teheti a PIN-kódok nagyobb, és 1-nél kisebb érték fogja őket kisebb. Stílus dostupnosti kapcsolatos további információkért lásd: [statikus kép szolgáltatás elérési út paramétereihez](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Kövesse az alábbi lépéseket egy kör és egyéni címkével rendelkező gombostűk megjelenítéséhez:

1. A Postman alkalmazást nyissa meg a korábban létrehozott gyűjteményt egy új lapon. Az első HTTP-metódus a jelentéskészítő lapon válassza ki, és adja meg az URL-címet a GET-kérés:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Íme a képernyőképet:

![Az egyéni gombostűk kör megjelenítése](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>További lépések


* Fedezze fel a [Azure Maps első térkép Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dokumentációját.
* Azure Maps adatszolgáltatás kapcsolatos további információkért tekintse meg a [service dokumentációja](https://docs.microsoft.com/rest/api/maps/data).

