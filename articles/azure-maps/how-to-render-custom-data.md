---
title: Egyéni adatok renderelése raszteres térképen | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan jelenítheti meg az egyéni adatokat egy raszteres térképen a Microsoft Azure Maps statikus lemezkép-szolgáltatás használatával.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335520"
---
# <a name="render-custom-data-on-a-raster-map"></a>Egyéni adatok renderelése raszteres térképen

Ez a cikk bemutatja, hogyan használható a [statikus képszolgáltatás](https://docs.microsoft.com/rest/api/maps/render/getmapimage)képkompozíciós funkcióval, hogy átfedéseket engedélyezze egy raszteres térkép tetején. A képkompozíció lehetővé teszi a rasztermozaik visszaszerzését további adatokkal, például egyéni nyomótűkkel, címkékkel és geometriai átfedésekkel.

Egyéni nyomótűk, címkék és geometriai átfedések megjelenítéséhez használhatja a Postman alkalmazást. Az Azure Maps [Data Service API-k](https://docs.microsoft.com/rest/api/maps/data) segítségével tárolhatja és renderelheti az átfedéseket.

> [!Tip]
> Gyakran sokkal költséghatékonyabb az Azure Maps Web SDK használatával egy egyszerű térkép megjelenítése egy weboldalon, mint a statikus lemezképszolgáltatás használata. A webes SDK térképcsempéket használ, és ha csak a felhasználó pásztáz és nagyítja a térképet, gyakran csak a tranzakció töredékét generálja térképterhelésenként. Vegye figyelembe, hogy az Azure Maps webes SDK-ban lehetőség van a pásztázás és a nagyítás letiltására. Emellett az Azure Maps webes SDK gazdagabb adatvizualizációs lehetőségeket biztosít, mint egy statikus térkép webszolgáltatás nem.  

## <a name="prerequisites"></a>Előfeltételek

### <a name="create-an-azure-maps-account"></a>Azure Maps-fiók létrehozása

A cikkben szereplő eljárások végrehajtásához először létre kell hoznia egy Azure Maps-fiókot, és be kell szereznie a térképfiók kulcsát. Kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) című útmutató utasításait az Azure Maps-fiók-előfizetés létrehozásához, és kövesse az elsődleges kulcs [beszerezése](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseit a fiók elsődleges kulcsának lekérnie. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Nyomtok renderelése címkékkel és egyéni képpel

> [!Note]
> Az ebben a szakaszban szereplő eljárás hoz egy Azure Maps-fiókot az S0 vagy S1 tarifacsomagban.

Az Azure Maps-fiók S0 szint támogatja `pins` csak egy példányát a paraméter. Ez lehetővé teszi, hogy legfeljebb öt pushpins, az URL-kérelemben megadott, egy egyéni kép.

Ha címkékkel és egyéni képpel szeretné megjeleníteni a nyomóbélyegeket, hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy gyűjteményt, amelyben a kérelmeket tárolni. A Postman alkalmazásban válassza az **Új**lehetőséget. Az **Új létrehozása** ablakban válassza a **Gyűjtemény**lehetőséget. Nevezze el a gyűjteményt, és válassza a **Létrehozás** gombot. 

2. A kérelem létrehozásához válassza ismét az **Új** lehetőséget. Az **Új létrehozása** ablakban válassza a **Kérés lehetőséget.** Adja meg a **nyomótűk kérelemnevét.** Válassza ki az előző lépésben létrehozott gyűjteményt a kérelem mentéséhez. Ezután válassza a **Mentés gombot.**
    
    ![Kérelem létrehozása a Postman ben](./media/how-to-render-custom-data/postman-new.png)

3. Válassza a GET HTTP metódust a szerkesztő lapon, és írja be a következő URL-címet a GET-kérelem létrehozásához.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Itt az eredményül kapott kép:

    ![Egyedi nyomótű címkével](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Adatok bekéselése az Azure Maps adattárából

> [!Note]
> Az ebben a szakaszban szereplő eljáráshoz egy Azure Maps-fiók szükséges az S1 tarifacsomagban.

Az elérési utat és a rögzítési helyadatokat az [Adatfeltöltés API segítségével](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)is beszerezheti. Az alábbi lépéseket követve töltse fel az elérési utat és a pineket.

1. A Postman alkalmazásban nyisson meg egy új lapot az előző szakaszban létrehozott gyűjteményben. Válassza a POST HTTP metódust a szerkesztő lapon, és írja be a következő URL-címet a POST kéréshez:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. A **Params** lapon adja meg a következő kulcs-/értékpárokat, amelyek a POST kérés URL-címéhez használatosak. Cserélje `subscription-key` le az értéket az Azure Maps-előfizetési kulcsra.
    
    ![Kulcs/érték params a Postmanben](./media/how-to-render-custom-data/postman-key-vals.png)

3. A **Törzs** lapon válassza ki a nyers beviteli formátumot, és válassza a JSON-t a legördülő listából. Adja meg ezt a JSON-t feltöltendő adatként:
    
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

4. Válassza **a Küldés** és a válaszfejléc áttekintése lehetőséget. Sikeres kérés esetén a Hely fejléce tartalmazza az állapotURI-t a feltöltési kérelem aktuális állapotának ellenőrzéséhez. Az állapotURI a következő formátumú lesz.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Másolja az állapotURI-t, és fűzze hozzá az előfizetési kulcs paramétert az Azure Maps-fiók előfizetési kulcsának értékével. Használja ugyanazt a fiók-előfizetési kulcsot, amelyet az adatok feltöltéséhez használt. Az állapotURI-formátumnak az alábbihoz hasonlóan kell kinéznie:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Az udId megkapásához nyisson meg egy új lapot a Postman alkalmazásban. Válassza a GET HTTP metódus t a szerkesztő lapon. Ha az adatok feltöltése sikeres volt, egy udId-ot fog kapni a választörzsben. Másolja az azonosítót.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Használja `udId` az adatfeltöltési API-ból kapott értéket a térképen lévő funkciók megjelenítéséhez. Ehhez nyisson meg egy új lapot az előző szakaszban létrehozott gyűjteményben. Válassza a GET HTTP metódust a szerkesztő lapon, cserélje le a (subscription-key} és a ({udId} elemet az értékekre) helyett, és adja meg ezt az URL-címet a GET kéréshez:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Itt a válasz kép:

    ![Adatok bekéselése az Azure Maps adattárából](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Sokszög renderelése színnel és opacitussal

> [!Note]
> Az ebben a szakaszban szereplő eljáráshoz egy Azure Maps-fiók szükséges az S1 tarifacsomagban.


A sokszög megjelenését a görbeparaméterstílus-módosítók segítségével [path parameter](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)módosíthatja.

1. A Postman alkalmazásban nyisson meg egy új lapot a korábban létrehozott gyűjteményben. Válassza a GET HTTP metódust a szerkesztő lapon, és írja be a következő URL-címet a GET kérés konfigurálásához a színnel és opacitussal rendelkező sokszög megjelenítéséhez:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Itt a válasz kép:

    ![Átlátszatlan sokszög renderelése](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Kör és nyomótű renderelése egyéni címkékkel

> [!Note]
> Az ebben a szakaszban szereplő eljáráshoz egy Azure Maps-fiók szükséges az S1 tarifacsomagban.


A tűk megjelenését stílusmódosítók hozzáadásával módosíthatja. Ha például a nyomótűket és azok feliratait nagyobbá vagy kisebbé szeretné tenni, használja a `sc` "méretezési stílus" módosítót. Ez a módosító nullánál nagyobb értéket vesz fel. Az 1 érték a szabványos skála. Az 1-nél nagyobb értékek a tűket nagyobbá, az 1-nél kisebb értékek pedig kisebbé teszik őket. A stílusmódosítókról a [statikus képszolgáltatás elérési útjának paraméterei](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)című témakörben talál további információt.


A kör és a nyomótű egyéni címkékkel való megjelenítéséhez kövesse az alábbi lépéseket:

1. A Postman alkalmazásban nyisson meg egy új lapot a korábban létrehozott gyűjteményben. Válassza a GET HTTP metódust a szerkesztő lapon, és adja meg ezt az URL-címet a GET kéréshez:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Itt a válasz kép:

    ![Kör renderelése egyéni nyomótűkkel](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Ha az utolsó lépésből szeretné módosítani a nyomótűk színét, módosítsa a "co" stílusmódosítót. Nézd `pins=default|la15+50|al0.66|lc003C62|co002D62|`meg, az aktuális szín lenne megadva #002D62 CSS. Tegyük fel, hogy #41d42a szeretné változtatni. Írja be az új színértéket a "co" `pins=default|la15+50|al0.66|lc003C62|co41D42A|`megadó után, így: . Új GET-kérelem benyújtása:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Itt van a válasz kép megváltoztatása után a színek a csapok:

    ![Kör renderelése frissített nyomótűkkel](./media/how-to-render-custom-data/circle-updated-pins.png)

Hasonlóképpen módosíthatja, hozzáadhatja és eltávolíthatja a többi stílusmódosítót.

## <a name="next-steps"></a>További lépések


* Fedezze fel az [Azure Maps Get Map Image API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dokumentációját.
* Ha többet szeretne megtudni az Azure Maps Data Service szolgáltatásról, olvassa el a [szolgáltatás dokumentációját.](https://docs.microsoft.com/rest/api/maps/data)

