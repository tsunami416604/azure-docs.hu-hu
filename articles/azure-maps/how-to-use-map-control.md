---
title: Bevezetés a web Map Control használatába a Azure Mapsban | Microsoft Docs
description: Ismerje meg, hogyan használhatja a Azure Maps Map Control ügyféloldali JavaScript-függvénytárat.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ff183261f67ff76f56fc034d8102e3aa3a4838a8
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480526"
---
# <a name="use-the-azure-maps-map-control"></a>A Azure Maps Térkép vezérlőelem használata

A térképkezelés ügyféloldali JavaScript-kódtár lehetővé teszi a Maps és a Embedded Azure Maps funkcióinak megjelenítését a webes vagy mobil alkalmazásban.

## <a name="create-a-new-map-in-a-web-page"></a>Új Térkép létrehozása egy weblapon

A weblapokon a térképkezelés ügyféloldali JavaScript-kódtár segítségével ágyazhat be térképet.

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be a Azure Maps web SDK-t. Ezt két lehetőség egyikének használatával teheti meg.

    a. Használja az Azure Maps web SDK globálisan üzemeltetett CDN-verzióját úgy, hogy hozzáadja az URL-végpontokat a stíluslaphoz és a parancsfájl-hivatkozásokhoz a fájl `<head>` elemében:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Azt is megteheti, hogy helyileg betölti a Azure Maps web SDK forráskódját az [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) NPM csomag használatával, és üzemelteti azt az alkalmazással. Ez a csomag írógéppel kapcsolatos definíciókat is tartalmaz.

    > NPM telepítése Azure-Maps-Control

    Ezután vegyen fel hivatkozásokat a Azure Maps stíluslapra és a parancsfájl forrására mutató hivatkozásokra a fájl `<head>` elemére:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
    ```

    >[!Note]
    > Az írógéppel definiált definíciók a hozzáadásával importálhatók az alkalmazásba:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Ha úgy szeretné megjeleníteni a térképet, hogy az kitöltse az oldal teljes törzsét, adja hozzá a következő `<style>` elemet a `<head>` elemhez.

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

4. A lap törzsében adjon hozzá egy `<div>` elemet, és adjon neki egy `id` a **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. A Térkép vezérlőelem inicializálásához adjon meg egy új szakaszt a HTML-szövegtörzsben, és hozzon létre egy parancsfájlt. Adja át a Térkép `<div>` vagy egy `HTMLElement` (például `document.getElementById('myMap')`) `id` az első paraméterként a `Map` osztály egy példányának létrehozásakor. Használja a saját Azure Maps fiók kulcsát vagy a Azure Active Directory (HRE) hitelesítő adatait a leképezés [hitelesítésére a hitelesítési beállítások](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)használatával. Ha létre kell hoznia egy fiókot, vagy meg kell találnia a kulcsot, tekintse meg a [Azure Maps-fiók és-kulcsok kezelése](how-to-manage-account-keys.md)című témakört. A **Language (nyelv** ) beállítás határozza meg a címkék és vezérlőelemek leképezéséhez használandó nyelvet. További információ a támogatott nyelvekről: [támogatott nyelvek](supported-languages.md). Ha előfizetés-kulcsot használ a hitelesítéshez.

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

    Ha Azure Active Directoryt (HRE) használ a hitelesítéshez:

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

8. Nyissa meg a fájlt a böngészőben, és tekintse meg a megjelenített térképet. A következő kódhoz hasonlóan kell kinéznie:

    <iframe height="700" style="width: 100%;" scrolling="no" title="A Térkép vezérlőelem használata" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>a Térkép vezérlőelem használata</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>A Térkép honosítása

Azure Maps két különböző módszert biztosít a Térkép nyelvének és regionális nézetének beállításához. Az első lehetőség az, hogy hozzáadja ezt az információt a globális `atlas` névtérhez, ami az alkalmazás összes leképezés-vezérlési példányát az alapértelmezett beállítások alapján fogja eredményezni. A következő nyelvre állítja be a franciát ("fr-FR") és a regionális nézetet az "Auto" értékre:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A második lehetőség az, hogy ezeket az információkat a térképi beállításokba adja át a Térkép betöltése során, például:

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
> A web SDK-val több térképes példányt is betölthet ugyanazon az oldalon különböző nyelvi és területi beállításokkal. Ezeket a beállításokat a Térkép `setStyle` funkciójának használatával is frissítheti, ha a térképet már betöltötte. 

Itt látható egy példa arra, hogy a "fr-FR", a regionális nézet pedig az "Auto" értékre van beállítva Azure Maps.

![Térkép ábrázolása a feliratokról francia nyelven](./media/how-to-use-map-control/websdk-localization.png)

A támogatott nyelvek és regionális nézetek teljes listáját [itt](supported-languages.md)dokumentáljuk.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre és kezelhet egy térképet:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

A Térkép stílusa:

> [!div class="nextstepaction"]
> [Válasszon egy leképezési stílust](choose-map-style.md)

További információ hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

A Azure Active Directory (HRE) Azure Maps használatával történő integrálását bemutató minták listáját a következő témakörben tekintheti meg:

> [!div class="nextstepaction"]
> [Azure AD-hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)