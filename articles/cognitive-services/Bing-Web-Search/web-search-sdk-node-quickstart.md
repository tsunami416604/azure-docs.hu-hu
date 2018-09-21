---
title: 'Rövid útmutató: A Node.js-hez készült Bing Web Search SDK használata'
titleSuffix: Azure Cognitive Services
description: A Bing Web Search SDK megkönnyíti a Bing Web Search integrálását a Node.js-alkalmazásába. Ebben a rövid útmutatóban elsajátíthatja az ügyfél-példányosítás, a kérésküldés és a válaszmegjelenítés módját.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: b27c11a69c7d16f38c2448d380b611940078c501
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121920"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Rövid útmutató: A Node.js-hez készült Bing Web Search SDK használata

A Bing Web Search SDK megkönnyíti a Bing Web Search integrálását a Node.js-alkalmazásába. Ebben a rövid útmutatóban elsajátíthatja az ügyfél-példányosítás, a kérésküldés és a válaszmegjelenítés módját.

Szeretné most rögtön megtekinteni a kódot? A [Node.js-hez készült Bing Web Search SDK-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) megtekinthetők a GitHubon.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Előfeltételek

Az alábbi dolgokra szüksége lesz a rövid útmutató futtatásához:

* A [Node.js 6-os](https://nodejs.org/en/download/) vagy újabb verziója
* Egy előfizetői azonosító  

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Kezdjük azzal, hogy beállítjuk a fejlesztési környezetet a Node.js-projektünkhöz.

1. Hozzon létre egy új könyvtárat a projekthez:

    ```console
    mkdir YOUR_PROJECT
    ```

2. Hozzon létre egy új csomagfájlt:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

3. Most telepítsünk néhány Azure-modellt, és adjuk őket hozzá a `package.json` fájlhoz:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Projekt létrehozása és a szükséges modulok deklarálása

Hozzon létre egy új Node.js-projektet a kedvenc IDE-környezete vagy szerkesztője segítségével ugyanabban a könyvtárban, ahol a `package.json` is található. Például: `sample.js`.

Ezután másolja ezt a kódot a projektbe. Ez betölti az előző szakaszban telepített modulokat.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Az ügyfél példányosítása

Ez a kód az `azure-cognitiveservices-websearch` modul segítségével példányosítja az ügyfelet. Folytatás előtt győződjön meg arról, hogy érvényes előfizetői azonosítót adott meg az Azure-fiókjához.

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

* [Azure Node SDK-referencia](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-websearch/)
