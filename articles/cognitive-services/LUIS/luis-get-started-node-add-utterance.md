---
title: 'Rövid útmutató: Modell módosítása és LUIS-alkalmazás betanítása Node.js használatával – Azure Cognitive Services | Microsoft Docs'
description: Ebben a rövid Node.js-útmutatóban kimondott példaszövegeket ad egy otthonautomatizálási alkalmazáshoz, és betanítja az alkalmazást. A kimondott példaszövegek szándékhoz rendelt beszélgetésre szánt felhasználói szövegek. A szándékok kimondott példaszövegeinek megadásával megtanítja a LUIS-t arra, hogy milyen típusú felhasználói szöveg mely szándékhoz tartozik.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: diberry
ms.openlocfilehash: c6ad30340e0800e475af9a9fefa0af7df1769f6c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43770042"
---
# <a name="quickstart-change-model-using-nodejs"></a>Rövid útmutató: Modell módosítása a Node.js használatával

[!include[Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Előfeltételek

[!include[Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* A [**Node.js**](https://nodejs.org/en/download/) NPM-et tartalmazó legújabb verziója.
* Az oktatóanya elvégzéséhez szükséges NPM-függőségek: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* [Visual Studio Code](https://code.visualstudio.com/).

[!include[Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!include[Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>A rövid útmutató kódjának létrehozása 

Adja hozzá az NPM-függőségeket az `add-utterances.js` nevű fájlhoz.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=8-11 "NPM Dependencies")]

Adja hozzá a LUIS-állandókat a fájlhoz. Másolja ki az alábbi kódot, és módosítsa a szerzői kulcsnak, az alkalmazásazonosítónak és a verzióazonosítónak megfelelően.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=13-22 "LUIS key and IDs")]

Adja hozzá a kimondott szövegeket tartalmazó feltöltendő fájl nevét és helyét. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=24-26 "Add upload file")]

Adjon hozzá metódust és objektumot az `addUtterance` függvényhez.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=28-67 "Add configuration information for adding utterance")]

Adjon hozzá metódust és objektumot az `train` függvényhez.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=69-101 "Add configuration information for training LUIS")]

Adja hozzá a HTTP-hívások küldésére és fogadására szolgáló `sendUtteranceToApi` függvényt. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=103-119 "Send the HTTP request")]

Adja hozzá a műveletet kiválasztó **fő** kódot.

   [!code-javascript[Main function](~/samples-luis/documentation-samples/quickstarts/change-model/node/add-utterances.js?range=121-143 "Main function")]

### <a name="install-dependencies"></a>Függőségek telepítése

Hozzon létre egy `package.json` fájlt a következő szöveggel:

   [!code-json[Package.json](~/samples-luis/documentation-samples/quickstarts/change-model/node/package.json "Package.json")]

A parancssorban a package.json fájlt tartalmazó könyvtárból telepítsen függőségeket az NPM használatával: `npm install`.

## <a name="run-code"></a>Kód futtatása

Futtassa az alkalmazást a parancssorból a Node.js nyelv használatával.

Az `npm start` hívása hozzáadja a kimondott szövegeket, betanítást végez és lekéri a betanítás állapotát.

```CMD
> npm start 
```

A parancssor megjeleníti a kimondott szövegek hozzáadása API meghívásának eredményét. 

[!include[Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a rövid útmutatóval, távolítsa el a rövid útmutatóban létrehozott összes fájlt. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [LUIS-alkalmazások felépítése programozott módon](luis-tutorial-node-import-utterances-csv.md)