---
title: Az Azure Maps Services modul használata | Microsoft Azure Maps
description: Ebben a cikkben megtudhatja, hogyan használhatja a Microsoft Azure Maps REST-szolgáltatásokat az Azure Maps-szolgáltatások modul használatával.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988736"
---
# <a name="use-the-azure-maps-services-module"></a>Az Azure Maps-szolgáltatások modul használata

Az Azure Maps Web SDK *szolgáltatási modult*biztosít. Ez a modul egy segítő könyvtár, amely megkönnyíti az Azure Maps REST-szolgáltatások használatát a web- vagy node.js alkalmazásokban JavaScript vagy TypeScript használatával.

## <a name="use-the-services-module-in-a-webpage"></a>A szolgáltatások modul használata egy weblapon

1. Hozzon létre egy új HTML-fájlt.
1. Töltse be az Azure Maps-szolgáltatások modult. Kétféleképpen töltheti be:
    - Használja az Azure Maps-szolgáltatások modul globálisan üzemeltetett, Azure Content Delivery Network verzióját. Parancsfájl-hivatkozás hozzáadása `<head>` a fájl eleméhez:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Másik lehetőségként töltse be az Azure Maps Web SDK forráskód szolgáltatásmodulját helyileg az [azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) npm csomag használatával, majd az alkalmazással való üzemeltetése. Ez a csomag TypeScript-definíciókat is tartalmaz. Használja ezt a parancsot:
    
        > **npm install azure-maps-rest**
    
        Ezután adjon hozzá egy `<head>` parancsfájlhivatkozást a fájl eleméhez:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Hozzon létre egy hitelesítési folyamatot. A folyamatot létre kell hozni a szolgáltatás URL-ügyfélvégpontjának inicializálása előtt. Saját Azure Maps-fiókkulcs vagy Az Azure Active Directory (Azure AD) hitelesítő adataival hitelesítheti az Azure Maps Search szolgáltatásügyfél. Ebben a példában létrejön a keresési szolgáltatás URL-ügyfele. 

    Ha előfizetési kulcsot használ a hitelesítéshez:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Ha az Azure AD-t használja a hitelesítéshez:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    További információ: [Authentication with Azure Maps](azure-maps-authentication.md).

1. A következő kód az újonnan létrehozott Azure Maps Search szolgáltatás URL-ügyfelet használja egy cím geokódolásához: "1 Microsoft Way, Redmond, WA". A kód `searchAddress` a függvényt használja, és az eredményeket táblázatként jeleníti meg az oldal törzsében.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Itt a teljes, futó kód minta:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A szolgáltatási modul használata" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Toll használata a<a href='https://codepen.io/azuremaps'>@azuremaps</a>szolgáltatások <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>modul</a> az Azure Maps ( ) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Az Azure Government felhőtámogatása

Az Azure Maps Web SDK támogatja az Azure Government felhő. Az Azure Maps Web SDK eléréséhez használt összes JavaScript- és CSS-URL-cím változatlan marad, azonban a következő feladatokat kell elvégezni az Azure Maps platform Azure Government felhőalapú verziójához való csatlakozáshoz.

Az interaktív térképvezérlő használatakor adja hozzá a következő kódsort az `Map` osztály egy példányának létrehozása előtt. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Ügyeljen arra, hogy az Azure Maps hitelesítési adatait az Azure Government felhőplatform hitelesítése során a térkép és a szolgáltatások.

A szolgáltatási modul használatakor a szolgáltatások tartományát be kell állítani egy API-URL-végpont egy példányának létrehozásakor. Például a következő kód létrehoz `SearchURL` egy példányt az osztály, és rámutat a tartomány az Azure Government-felhő.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ha közvetlenül hozzáfér az Azure Maps REST-szolgáltatásokhoz, módosítsa az URL-tartományt a.-ra. `atlas.azure.us` Ha például a search API-szolgáltatást használja, `https://atlas.microsoft.com/search/` `https://atlas.azure.us/search/`módosítsa az URL-tartományt a-ra.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL (URL)](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

A szolgáltatási modult használó további kódmintákat az alábbi cikkekben láthatja:

> [!div class="nextstepaction"]
> [Keresési eredmények megjelenítése a térképen](./map-search-location.md)

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése A-ból B-be](./map-route.md)
