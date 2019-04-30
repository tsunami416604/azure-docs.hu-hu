---
title: A szolgáltatások modul – az Azure Maps használata |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Maps szolgáltatások modul.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: f3650d4db06a763308939e9fb1a98fddb0eaa04a
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738773"
---
# <a name="use-the-azure-maps-services-module"></a>Az Azure Maps services modullal

Az Azure Maps Web SDK biztosít egy *szolgáltatások modul*. Ez a modul egy segédkódtárba helyezni, amely megkönnyíti az Azure Maps REST szolgáltatások web- vagy Node.js-alkalmazások használata JavaScript- vagy TypeScript használatával.

## <a name="use-the-services-module-in-a-webpage"></a>Egy weblap services modullal

1. Hozzon létre egy új HTML-fájlt.
1. Az Azure Maps szolgáltatások modul betöltése. Betöltheti azokat a két módszer egyikével:
    - Az Azure Maps szolgáltatások modul globálisan üzemeltetett Azure Content Delivery Network verzióját használja. Vegyen fel egy parancsfájl hivatkozást a `<head>` elem a fájl:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
        ```

    - Azt is megteheti, betöltheti az Azure Maps Web SDK forráskódját helyileg a a [azure maps-inaktív](https://www.npmjs.com/package/azure-maps-rest) npm csomagot, majd ezután üzemeltetni az alkalmazást. Ez a csomag is TypeScript definíciókat tartalmazza. Használja ezt a parancsot:
    
        > **az npm telepítése azure-térképek – rest**
    
        Ezután vegyen fel egy parancsfájl hivatkozást a `<head>` elem a fájl:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Hozzon létre egy hitelesítési folyamatot. Akkor is Szolgáltatásvégpont URL-cím ügyfél inicializálása előtt létre kell hoznia a folyamatot. A saját Azure Maps-fiók kulcsára vagy Azure Active Directory (Azure AD) hitelesítő adatok használatával az Azure Maps Search szolgáltatás ügyfelek hitelesítéséhez. Ebben a példában a Search szolgáltatás URL-cím ügyfél létrejön. 

    Ha egy előfizetési kulcsot használ a hitelesítéshez:

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

    Az Azure AD használatakor a hitelesítéshez:

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

    További információkért lásd: [hitelesítés az Azure Maps](azure-maps-authentication.md).

1. A következő kódot az újonnan létrehozott Azure Search szolgáltatás URL-cím ügyfél a geocode-címet használja: "1 Microsoft Way, Redmond, WA". A kód a `searchAddress` funkciót, és megjeleníti az eredményeket az oldal törzsében táblázatként.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
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

    Itt látható a teljes kódmintát fut:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A szolgáltatások modul használatával" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>az Services modullal</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

A szolgáltatások a modul további Kódminták tanulmányozza a következő cikkeket:

> [!div class="nextstepaction"]
> [A térképen a keresési eredmények megjelenítése](./map-search-location.md)

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a-b](./map-route.md)