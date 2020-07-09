---
title: Bevezetés a web Map vezérlőelem használatába | Microsoft Azure térképek
description: Ismerje meg, hogyan használhatja a Maps és a Embedded Azure Maps funkciókat a webes vagy mobil alkalmazásba a Microsoft Azure Maps Map Control ügyféloldali JavaScript-kódtár használatával.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335244"
---
# <a name="use-the-azure-maps-map-control"></a>Az Azure Maps-térképvezérlő használata

A térképkezelés ügyféloldali JavaScript-kódtár lehetővé teszi a Maps és a Embedded Azure Maps funkcióinak megjelenítését a webes vagy mobil alkalmazásban.

## <a name="create-a-new-map-in-a-web-page"></a>Új Térkép létrehozása egy weblapon

A weblapokon a térképkezelés ügyféloldali JavaScript-kódtár segítségével ágyazhat be térképet.

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be a Azure Maps web SDK-t. Két lehetőség közül választhat:

    * Használja a Azure Maps web SDK globálisan üzemeltetett CDN-verzióját úgy, hogy a `<head>` HTML-fájl elemében a JavaScriptre és a stíluslapra mutató hivatkozásokat ad hozzá:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Töltse be az Azure Maps web SDK forráskódját helyileg az [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM csomag használatával, és működtesse azt az alkalmazással. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz.

        > **NPM telepítése Azure-Maps-Control**

       Ezután vegyen fel hivatkozásokat az Azure Maps stíluslapra és a parancsfájl forrására mutató hivatkozásokra a `<head>` fájl elemére:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Az írógéppel definiált definíciók importálhatók az alkalmazásba a következő kód hozzáadásával:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Ha a térképet úgy szeretné megjeleníteni, hogy az kitöltse az oldal teljes törzsét, adja hozzá a következő `<style>` elemet a `<head>` elemhez.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. A lap törzsében adjon hozzá egy elemet, `<div>` és adjon neki egy `id` **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. A Térkép vezérlőelem inicializálásához adjon meg egy új parancsfájl-címkét a HTML-szövegtörzsben. Adja át a `id` térképet `<div>` vagy egy `HTMLElement` (például `document.getElementById('myMap')` ) az első paraméterként az osztály egy példányának létrehozásakor `Map` . Használja a saját Azure Maps fiók kulcsát vagy a Azure Active Directory (HRE) hitelesítő adatait a leképezés [hitelesítésére a hitelesítési beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)használatával. 

   Ha létre kell hoznia egy fiókot, vagy meg kell keresnie a kulcsot, kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) és az [elsődleges kulcs lekérése](quick-demo-map-app.md#get-the-primary-key-for-your-account) című témakör utasításait. 

   A **Language (nyelv** ) beállítás határozza meg a címkék és vezérlőelemek leképezéséhez használandó nyelvet. További információ a támogatott nyelvekről: [támogatott nyelvek](supported-languages.md). Ha előfizetés-kulcsot használ a hitelesítéshez, használja a következőt:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   Ha Azure Active Directoryt (HRE) használ a hitelesítéshez, használja a következőt:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   [Itt](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)találhat olyan mintákat, amelyek bemutatják, hogyan integrálható Azure Active Directory (HRE) a Azure maps. 
    
   További információkért tekintse meg a [hitelesítés Azure Maps](azure-maps-authentication.md) dokumentummal és a [Azure Maps Azure ad-hitelesítési példákat](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)is.

6. Szükség esetén a következő meta kódelem-elemek hozzáadását is megtalálhatja az oldal Head eleméhez:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. A HTML-fájl összevonásával a következő kódhoz hasonlóan kell kinéznie:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Nyissa meg a fájlt a böngészőben, és tekintse meg a megjelenített térképet. A következő képhez hasonlóan kell kinéznie:

   ![Megjelenített eredményt mutató Térkép képe](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>A Térkép honosítása

A Azure Maps két különböző módszert biztosít a megjelenített Térkép nyelvének és regionális nézetének beállítására. Az első lehetőség, hogy hozzáadja ezt az információt a globális `atlas` névtérhez, ami az alkalmazás összes leképezés-vezérlési példányát az alapértelmezett beállítások alapján fogja eredményezni. A következő nyelvre állítja be a franciát ("fr-FR") és a regionális nézetet az "Auto" értékre:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A második lehetőség az, hogy ezeket az információkat a térképi beállításokba adja át, amikor a következőhöz hasonló leképezést tölt be:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> A web SDK-val több térképes példányt is betölthet ugyanazon az oldalon különböző nyelvi és területi beállításokkal. Emellett ezek a beállítások a Térkép betöltése után is frissíthetők `setStyle` . 

Itt látható egy példa arra, hogy a "fr-FR", a regionális nézet pedig az "Auto" értékre van beállítva Azure Maps.

![Térkép ábrázolása a feliratokról francia nyelven](./media/how-to-use-map-control/websdk-localization.png)

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="azure-government-cloud-support"></a>Felhő-támogatás Azure Government

A Azure Maps web SDK támogatja a Azure Government-felhőt. A Azure Maps web SDK eléréséhez használt összes JavaScript és CSS URL-cím változatlan marad. A Azure Maps platform Azure Government Cloud-verziójához való kapcsolódáshoz a következő feladatokat kell elvégezni.

Az interaktív térkép vezérlőelem használatakor adja hozzá a következő kódrészletet az osztály egy példányának létrehozása előtt `Map` . 

```javascript
atlas.setDomain('atlas.azure.us');
```

Ügyeljen arra, hogy a Térkép és a szolgáltatások hitelesítése során a Azure Government Cloud platformon Azure Maps hitelesítés részleteit használja.

A szolgáltatások modul használatakor a szolgáltatás tartományát be kell állítani az API URL-végpontok példányának létrehozásakor. A következő kód például létrehozza a osztály egy példányát, `SearchURL` és a tartományt a Azure Government felhőre mutat.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ha közvetlenül fér hozzá a Azure Maps REST-szolgáltatásokhoz, módosítsa az URL-tartományt a következőre: `atlas.azure.us` . Ha például a Search API szolgáltatást használja, módosítsa az URL-tartományt a következőre: `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre és kezelhet egy térképet:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

A Térkép stílusa:

> [!div class="nextstepaction"]
> [Térképstílus kiválasztása](choose-map-style.md)

További információ hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

A Azure Active Directory (HRE) Azure Maps használatával történő integrálását bemutató minták listáját a következő témakörben tekintheti meg:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
