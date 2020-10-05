---
title: Bing Web Search JavaScript ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 20eaf6e6a9f0eee15e6ad6a5bd8f23bf8531545c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376435"
---
Az Bing Web Search ügyféloldali kódtár megkönnyíti a Bing Web Search integrálását a Node.js alkalmazásba. Ebben a rövid útmutatóban elsajátíthatja az ügyfél-példányosítás, a kérésküldés és a válaszmegjelenítés módját.

Szeretné most rögtön megtekinteni a kódot? A [javascripthez készült Bing Search ügyféloldali kódtárak](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) mintái a githubon érhetők el.

## <a name="prerequisites"></a>Előfeltételek
Az alábbi dolgokra szüksége lesz a rövid útmutató futtatásához:

* A [Node.js 6-os](https://nodejs.org/en/download/) vagy újabb verziója
* Egy előfizetői azonosító  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Kezdjük azzal, hogy beállítjuk a fejlesztési környezetet a Node.js-projektünkhöz.

1. Hozzon létre egy új könyvtárat a projekthez:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Hozzon létre egy új csomagfájlt:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Most telepítsen néhány Azure-modult, és adja hozzá őket a következőhöz `package.json` :

    ```console
    npm install --save @azure/cognitiveservices-websearch
    npm install --save @azure/ms-rest-azure-js
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Projekt létrehozása és a szükséges modulok deklarálása

Hozzon létre egy új Node.js-projektet a kedvenc IDE-környezete vagy szerkesztője segítségével ugyanabban a könyvtárban, ahol a `package.json` is található. Például: `sample.js`.

Ezután másolja ezt a kódot a projektbe. Ez betölti az előző szakaszban telepített modulokat.

```javascript
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const WebSearchAPIClient = require('@azure/cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Az ügyfél példányosítása

Ez a kód az `@azure/cognitiveservices-websearch` modul segítségével példányosítja az ügyfelet. Folytatás előtt győződjön meg arról, hogy érvényes előfizetői azonosítót adott meg az Azure-fiókjához.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Kérés indítása és az eredmények megjelenítése

Küldjön egy keresési lekérdezést a Bing Web Searchnek az ügyfél segítségével. Ha a válaszban szerepel a `properties` tömb bármelyik eleméhez tartozó eredmény, a `result.value` megjelenik a konzolon.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>A program futtatása

Az utolsó lépés a program futtatása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a projekttel, ne felejtse el eltávolítani az előfizetői azonosítót a program kódjából.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Cognitive Services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Lásd még

* [Azure Node SDK-referencia](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
