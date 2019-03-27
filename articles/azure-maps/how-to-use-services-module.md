---
title: A szolgáltatások modul – az Azure Maps segítségével |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Maps szolgáltatások modul.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 335e114fc6a4afa83a6b82509148cf258a9e8347
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501151"
---
# <a name="using-the-azure-maps-services-module"></a>Az Azure Maps Services modullal

Az Azure Maps Web SDK biztosít egy services modult, amely egy segédkódtárba helyezni, amely megkönnyíti a web- vagy JavaScript- vagy TypeScript használatával Node.js-alkalmazások az Azure Maps REST szolgáltatások.

## <a name="using-the-services-module-in-a-web-page"></a>Egy weblap services modullal

1. Hozzon létre egy új HTML-fájlt.
2. Töltse be az Azure Maps Services modulban. Ezt megteheti egy két lehetőség közül választhat;

    a. Globálisan üzemeltetett CDN verzióját használja, az Azure Maps services modul egy szkriptreferenciájának hozzáadásával a <head> elem a fájl:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
    b. Azt is megteheti, betöltése az Azure Maps Web SDK forráskódját használatával helyben a [azure-térképek – rest](https://www.npmjs.com/package/azure-maps-rest) NPM csomag, és tárolja azt az alkalmazást. Ez a csomag is TypeScript definíciókat tartalmazza.
    
    > az npm telepítése azure-térképek – rest
    
    Majd adja hozzá a parancsfájl hivatkozik a `<head>` elem a fájl:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Szolgáltatásvégpont URL-cím ügyfél inicializálása, először létre kell hoznia egy hitelesítési folyamatot. A saját Azure Maps-fiók kulcsára vagy Azure Active Directory (AAD) hitelesítő adatokat használja a keresési szolgáltatás ügyfelek hitelesítéséhez. Ebben a példában a search szolgáltatás URL-cím ügyfél létrejön. Ha egy előfizetési kulcsot használ a hitelesítéshez:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Az Azure Active Directory (AAD) használata a hitelesítéshez:

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
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
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
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
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    További információkért lásd: [hitelesítés az Azure Maps](azure-maps-authentication.md).

4. A következő kódot használja a geocode egy címet, az újonnan létrehozott search szolgáltatás URL-cím ügyfél "1 Microsoft módja, Redmond, WA" használatával az `searchAddress` funkciót, és megjeleníti az eredményeket az oldal törzsében táblázatként. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
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
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Itt látható a teljes körűen futó kódminta:

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

Tekintse meg a szolgáltatások a modul további Kódminták az alábbi cikkeket:

> [!div class="nextstepaction"]
> [A térképen a keresési eredmények megjelenítése](./map-search-location.md)

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a-b](./map-route.md)