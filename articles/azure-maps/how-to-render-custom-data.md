---
title: Egyéni adathalmazok renderelése raszteres térképeken a Azure Mapsban | Microsoft Docs
description: Egyéni adatértékek renderelése Azure Maps raszteres térképén.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6619fd842f225a6d362a4b308dde6e35b43677c9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915762"
---
# <a name="render-custom-data-on-a-raster-map"></a>Egyéni adathalmazok renderelése egy raszteres térképen

Ez a cikk azt ismerteti, hogyan használható a [statikus képszolgáltatás](https://docs.microsoft.com/rest/api/maps/render/getmapimage) a képösszeállítási funkcióval a raszteres térképeken található átfedések engedélyezéséhez. A képösszeállítás lehetővé teszi a raszteres csempék visszaszerzését, további adatokkal (például egyéni pushpins, címkékkel és geometriai átfedésekkel).

Az egyéni pushpins, címkék és geometriai átfedések megjelenítéséhez használhatja a Poster alkalmazást. Azure Maps [adatszolgáltatási API-kat](https://docs.microsoft.com/rest/api/maps/data) használhat az átfedések tárolására és megjelenítésére.


## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

A cikkben ismertetett eljárások elvégzéséhez először létre kell hoznia egy Azure Maps fiókot a [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) részben leírt utasítások alapján, és kövesse az [elsődleges kulcs beolvasása](./tutorial-search-location.md#getkey) a fiókhoz elsődleges előfizetési kulcs lekéréséhez című témakör lépéseit.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Pushpins megjelenítése címkékkel és egyéni képpel

> [!Note]
> Az ebben a szakaszban szereplő eljáráshoz Azure Maps-fiók szükséges a S0 vagy S1 díjszabási szinten.

A Azure Maps fiók S0 szintje csak a `pins` paraméter egyetlen példányát támogatja. Ez lehetővé teszi, hogy legfeljebb öt pushpins jelenítse meg az URL-kérelemben megadott egyéni rendszerkép használatával.

A pushpins címkékkel és egyéni képpel történő megjelenítéséhez hajtsa végre a következő lépéseket:

1. Hozzon létre egy gyűjteményt, amelyben tárolni szeretné a kérelmeket. A Poster alkalmazásban válassza az **új**lehetőséget. Az **új létrehozása** ablakban válassza a **gyűjtemény**elemet. Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot. 

2. A kérelem létrehozásához válassza az **új** újra lehetőséget. Az **új létrehozása** ablakban válassza a **kérelem**lehetőséget. Adja meg a pushpins **nevét** , válassza ki az előző lépésben létrehozott gyűjteményt a kérelem mentéséhez használt helyként, majd kattintson a **Mentés**gombra.
    
    ![Kérelem létrehozása a Poster-ban](./media/how-to-render-custom-data/postman-new.png)

3. Válassza a HTTP beolvasása metódust a Builder (szerkesztő) lapon, majd a GET kérelem létrehozásához adja meg a következő URL-címet.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Az eredményül kapott rendszerkép:

    ![Egy címkével rendelkező egyéni gombostű](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Adatok beolvasása Azure Maps adattárolóból

> [!Note]
> Az ebben a szakaszban ismertetett eljáráshoz egy Azure Maps fiókra van szükség az S1 díjszabási szinten.

Az elérési utat és a rögzítési hely adatait az [Adatfeltöltő API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)használatával is beszerezheti. Az elérési út és a PIN-kód adatok feltöltéséhez kövesse az alábbi lépéseket.

1. A Poster alkalmazásban nyisson meg egy új lapot az előző szakaszban létrehozott gyűjteményben. Válassza a HTTP POST metódust a Builder (szerkesztő) lapon, és adja meg a következő URL-címet a POST-kérelem elvégzéséhez:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. A **Paraméterek** lapon adja meg a következő kulcs/érték párokat, amelyek a post kérelem URL-címéhez használatosak. Cserélje le `subscription-key` az értéket a Azure Maps előfizetési kulcsára.
    
    ![A Poster kulcs/érték paraméterei](./media/how-to-render-custom-data/postman-key-vals.png)

3. A **törzs** lapon válassza ki a nyers bemeneti formátumot, és a legördülő listából válassza a JSON lehetőséget. Adja meg ezt a JSON-t a feltöltött adatként:
    
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

4. Válassza a **Küldés** lehetőséget, és tekintse át a válasz fejlécét. Sikeres kérelem esetén a hely fejléce tartalmazza az állapot URI-JÁT a feltöltési kérelem aktuális állapotának vizsgálatához. Az állapot URI-ja a következő formátumú lesz:.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Másolja ki az állapot-URI-t, és fűzze hozzá az előfizetés-kulcs paramétert annak értékéhez, hogy a Azure Maps fiókjának előfizetési kulcsa, amelyet az adatok feltöltéséhez használt. Az állapot URI-formátumának az alábbihoz hasonlóan kell kinéznie:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. A udId megnyitásához nyisson meg egy új fület a Poster alkalmazásban, majd válassza a HTTP-módszer beolvasása lehetőséget a Builder lapon, és hozzon igénybe egy GET kérelmet az állapot URI-n. Ha az adatok feltöltése sikeres volt, egy udId fog kapni a válasz törzsében. Másolja a udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Használja az adatfeltöltő API által fogadott értéketafunkciókmegjelenítéséhezatérképen.`udId` Ehhez nyisson meg egy új fület az előző szakaszban létrehozott gyűjteményben. Válassza a HTTP beolvasása metódust a szerkesztő lapon, és írja be ezt az URL-címet a GET kérelem elvégzéséhez:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    A válasz képe:

    ![Adatok beolvasása Azure Maps adattárolóból](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Sokszög megjelenítése színnel és opacitással

> [!Note]
> Az ebben a szakaszban ismertetett eljáráshoz egy Azure Maps fiókra van szükség az S1 díjszabási szinten.


A sokszög megjelenését a [path paraméterrel](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)módosíthatja a stílus-módosítók használatával.

1. A Poster alkalmazásban nyisson meg egy új fület a korábban létrehozott gyűjteményben. Jelölje be a HTTP lekérése metódust a szerkesztő lapon, és adja meg a következő URL-címet a GET kérelem konfigurálásához a sokszög színének és opacitásának megjelenítéséhez:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    A válasz képe:

    ![Átlátszatlan sokszög megjelenítése](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Kör-és pushpins megjelenítése egyéni címkékkel

> [!Note]
> Az ebben a szakaszban ismertetett eljáráshoz egy Azure Maps fiókra van szükség az S1 díjszabási szinten.


A pushpins és azok címkéit nagyobb vagy kisebb méretűre teheti `sc` a méretezési stílus módosító használatával. Ez a módosító egy nullánál nagyobb értéket vesz fel. Az 1 érték a standard skála. Az 1 értéknél nagyobb értékek esetén a PIN-kód nagyobb lesz, és az 1-nél kisebb értékek kisebbek lesznek. A stílus-módosítókkal kapcsolatos további információkért lásd a [statikus képszolgáltatás elérési útja paramétereit](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


A kör-és pushpins egyéni címkékkel történő megjelenítéséhez kövesse az alábbi lépéseket:

1. A Poster alkalmazásban nyisson meg egy új fület a korábban létrehozott gyűjteményben. Válassza a HTTP beolvasása metódust a szerkesztő lapon, és írja be ezt az URL-címet a GET kérelem elvégzéséhez:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    A válasz képe:

    ![Kör megjelenítése egyéni pushpins](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>További lépések


* Ismerkedjen meg a [Azure Maps Map RENDSZERKÉP API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dokumentációjának használatával.
* Ha többet szeretne megtudni a Azure Maps adatszolgáltatásról, tekintse meg a [szolgáltatás dokumentációját](https://docs.microsoft.com/rest/api/maps/data).

