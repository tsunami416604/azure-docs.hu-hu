---
title: Webes térképvezérlés – első lépések | Microsoft Azure Maps
description: Megtudhatja, hogy a Microsoft Azure Maps leképezési rendszerű ügyféloldali JavaScript-kódtár használatával hogyan jelenítheti meg a térképeket és a beágyazott Azure Maps-funkciókat a webes vagy mobilalkalmazásban.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335244"
---
# <a name="use-the-azure-maps-map-control"></a>Az Azure Maps térképvezérlő használata

A Map Control ügyféloldali JavaScript-kódtár lehetővé teszi a térképek és a beágyazott Azure Maps-funkciók megjelenítését a webes vagy mobilalkalmazásban.

## <a name="create-a-new-map-in-a-web-page"></a>Új térkép létrehozása weblapon

A Térképvezérlés ügyféloldali JavaScript-könyvtárával beágyazhat egy térképet egy weblapba.

1. Hozzon létre egy új HTML-fájlt.

2. Töltse be az Azure Maps Web SDK-ban. Két lehetőség közül választhat;

    * Használja az Azure Maps Web SDK globálisan üzemeltetett CDN-verzióját a JavaScriptre és a stíluslapra mutató hivatkozások hozzáadásával a `<head>` HTML-fájl elemében:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Töltse be az Azure Maps Web SDK forráskódját helyileg az [azure-maps-control NPM-csomag](https://www.npmjs.com/package/azure-maps-control) használatával, és az alkalmazással együtt üzemeltetje azt. Ez a csomag TypeScript-definíciókat is tartalmaz.

        > **npm telepítés azúrkék térképvezérlése**

       Ezután adjon hozzá hivatkozásokat az Azure Maps stíluslapjára és a parancsfájl forráshivatkozásaira a `<head>` fájl eleméhez:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > A typescript-definíciók a következő kód hozzáadásával importálhatók az alkalmazásba:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Ha úgy szeretné megjeleníteni a térképet, hogy az kitöltse az oldal teljes törzsét, adja hozzá a következő `<style>` elemet az `<head>` elemhez.

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

4. A törzs az oldal, `<div>` adjunk hozzá egy `id` elemet, és adja meg a **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. A térképvezérlő inicializálásához definiáljon egy új parancsfájlcímkét a html törzsében. Adja át `id` a `<div>` térkép `HTMLElement` vagy egy `document.getElementById('myMap')`(például ) az első paraméter `Map` létrehozásakor egy példányát az osztály. Saját Azure Maps-fiókkulcsa vagy Az Azure Active Directory (AAD) hitelesítő adataival hitelesítheti a térképet [hitelesítési beállításokkal.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions) 

   Ha létre kell hoznia egy fiókot, vagy meg kell találnia a kulcsot, kövesse a [Fiók létrehozása című](quick-demo-map-app.md#create-an-account-with-azure-maps) részben található utasításokat, és kapja meg az elsődleges [kulcsot.](quick-demo-map-app.md#get-the-primary-key-for-your-account) 

   A **nyelvi** beállítás határozza meg a térképcímkék és -vezérlők nyelvét. A támogatott nyelvekről a támogatott nyelvekről további információt a [támogatott nyelvek című témakörben](supported-languages.md)talál. Ha előfizetési kulcsot használ a hitelesítéshez, használja az alábbiakat:

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

   Ha az Azure Active Directoryt (AAD) használja a hitelesítéshez, használja a következőket:

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

   Az Azure Active Directory (AAD) Azure Maps szolgáltatással való integrálását bemutató minták listája [itt](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)található. 
    
   További információt az [Azure Maps hitelesítése](azure-maps-authentication.md) dokumentumban, valamint az [Azure Maps Azure AD-hitelesítési mintákban](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)talál.

6. Szükség esetén hasznos lehet a következő metacímke-elemek hozzáadása az oldal fejéhez:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Elhelyezés ez minden együtt a HTML fájlt kell kinéznie, mint a következő kódot:

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

8. Nyissa meg a fájlt a webböngészőben, és tekintse meg a megjelenített térképet. Meg kell kinéznie az alábbi képen:

   ![A megjelenített eredményt megjelenítő térképkép](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>A térkép honosítása

Az Azure Maps két különböző módon állítja be a leképezés nyelvi és regionális nézetét. Az első lehetőség az, hogy `atlas` ezt az információt hozzáadja a globális névtérhez, ami azt eredményezi, hogy az alkalmazás összes térképvezérlő példánya alapértelmezés szerint ezeket a beállításokat tartalmazza. A következő nyelv francia ("fr-FR") és a regionális nézet "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

A második lehetőség az, hogy adja át ezt az információt a térkép en, amikor betölti a térképet, mint ez:

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
> A Web SDK segítségével több térképpéldány is betölthető ugyanazon az oldalon különböző nyelvi és területi beállításokkal. Ezenkívül ezek a beállítások a térkép betöltése után frissíthetők a térkép `setStyle` funkciójának használatával. 

Íme egy példa az Azure Maps a nyelv beállítása "fr-FR" és a regionális nézet beállítása "Auto".

![Térképkép, amelyen francia címkék láthatók](./media/how-to-use-map-control/websdk-localization.png)

A támogatott nyelvek és a regionális nézetek teljes listáját [itt](supported-languages.md)olvashatja.

## <a name="azure-government-cloud-support"></a>Az Azure Government felhőtámogatása

Az Azure Maps Web SDK támogatja az Azure Government felhő. Az Azure Maps Web SDK eléréséhez használt javascript- és CSS-URL-ek változatlanok maradnak. A következő feladatokat kell elvégezni az Azure Maps platform Azure Government felhőalapú verziójához való csatlakozáshoz.

Az interaktív térképvezérlő használatakor adja hozzá a következő kódsort az `Map` osztály egy példányának létrehozása előtt. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Ügyeljen arra, hogy az Azure Maps hitelesítési adatait az Azure Government felhőplatform, a térkép és a szolgáltatások hitelesítésekénte.

A szolgáltatási modul használatakor a szolgáltatások tartományát be kell állítani egy API-URL-végpont egy példányának létrehozásakor. Például a következő kód létrehoz `SearchURL` egy példányt az osztály, és rámutat a tartomány az Azure Government-felhő.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ha közvetlenül hozzáfér az Azure Maps REST-szolgáltatásokhoz, módosítsa az URL-tartományt a.-ra. `atlas.azure.us` Ha például a search API-szolgáltatást használja, `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`módosítsa az URL-tartományt a-ra.

## <a name="next-steps"></a>További lépések

További információ a térkép létrehozásáról és az azokkal való interakcióról:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

Ismerje meg, hogyan stílus egy térkép:

> [!div class="nextstepaction"]
> [Térképstílus kiválasztása](choose-map-style.md)

További adatok hozzáadása a térképhez:

> [!div class="nextstepaction"]
> [Térkép létrehozása](map-create.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Az Azure Active Directory (AAD) Azure Maps szolgáltatással való integrálását bemutató minták listáját a következő témakörben található:

> [!div class="nextstepaction"]
> [Az Azure AD hitelesítési minták](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
