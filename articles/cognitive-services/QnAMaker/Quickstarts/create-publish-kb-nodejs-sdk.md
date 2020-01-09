---
title: 'Rövid útmutató: a Node. js-hez készült ügyféloldali kódtár QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató bemutatja, hogyan kezdheti meg a Node. js-hez készült QnA Maker ügyféloldali kódtárat. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: a605b5145e393352e8cd6fe18ac97ea749caf4ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447588"
---
# <a name="quickstart-qna-maker-client-library-for-nodejs"></a>Rövid útmutató: a Node. js-hez készült ügyféloldali kódtár QnA Maker

Ismerkedés a Node. js-hez készült QnA Maker ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.

A Node. js-hez készült QnA Maker ügyféloldali kódtár a következőhöz használható:

* Tudásbázis létrehozása
* Tudásbázis kezelése
* Tudásbázis közzététele

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-qnamaker) | [Node. js-minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást QnA Maker a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával.

Miután beolvasott egy kulcsot az erőforrásból, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az erőforráshoz, `QNAMAKER_SUBSCRIPTION_KEY` és `QNAMAKER_HOST`névvel. Használja a Azure Portal **erőforrás kulcsainak** és **áttekintő** lapjain található kulcs-és végpont-értékeket.

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

A `npm init -y` parancs futtatásával hozzon létre egy csomópont-alkalmazást egy `package.json`-fájllal.

```console
npm init -y
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `ms-rest-azure` és `azure-cognitiveservices-qnamaker` NPM csomagokat:

```console
npm install azure-cognitiveservices--qnamaker ms-rest-azure --save
```

Az alkalmazás `package.json` fájlja a függőségekkel frissül.


## <a name="object-model"></a>Objektummodell

A QnA Maker ügyfél egy [QnAMakerClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest) objektum, amely a kulcsot tartalmazó ServiceClientCredentials használatával hitelesíti az Azure-t.

Az ügyfél létrehozása után használja a tudásbázist [a Tudásbázis](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#knowledgebase) létrehozásához, kezeléséhez és közzétételéhez.

A tudásbázist egy JSON-objektum küldésével kezelheti. Az azonnali műveletekhez a metódus általában egy JSON-objektumot ad vissza, amely az állapotot jelzi. A hosszú ideig futó műveletek esetében a válasz a művelet azonosítója. Hívja meg az [ügyfelet. Operations. getDetails](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest#getdetails-string--servicecallback-operation--) metódus a műveleti azonosítóval a [kérelem állapotának](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operationstatetype?view=azure-node-latest)meghatározásához.


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node. js-hez készült QnA Maker ügyféloldali kódtár használatával:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis frissítése](#update-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Hozzon létre egy `index.js` nevű fájlt. Adja hozzá a QnA Maker könyvtárat és az Azure REST-függvénytárat a fájlhoz.

[!code-javascript[Require statements](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=dependencies)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

|Környezeti változó|Node. js-változó|Példa|
|--|--|--|
|`QNAMAKER_SUBSCRIPTION_KEY`|`subscription_key`|A kulcs egy 32 karakterből álló karakterlánc, amely a Azure Portal QnA Maker erőforrásának gyors üzembe helyezés lapján érhető el. Ez nem ugyanaz, mint az előrejelzési végpont kulcsa.|
|`QNAMAKER_HOST`|`endpoint`| A szerzői végpont `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formátuma tartalmazza az **erőforrás nevét**. Ez nem ugyanaz az URL-cím, amely az előrejelzési végpont lekérdezésére szolgál.|
||||

[!code-javascript[Azure resource variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ezután hozzon létre egy ServiceClientCredentials objektumot a kulccsal, és használja a végpontján egy [QnAMakerClient](https://docs.microsoft.com/javascript/api/%40azure/cognitiveservices-qnamaker/qnamakerclient?view=azure-node-latest#qnamakerclient-serviceclientcredentials--string--msrest-serviceclientoptions-) objektum létrehozásához. Egy [Tudásbázis](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest) -objektum beszerzéséhez használja az Client objektumot.


[!code-javascript[Authorization to resource key](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a [CreateKbDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/createkbdto?view=azure-node-latest) objektumra vonatkozó kérdés-és válasz párokat három forrásból tárolja:

* A **szerkesztői tartalomhoz**használja a [QnADTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/qnadto?view=azure-node-latest) objektumot.
* **Fájlok**esetében használja a [FileDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/filedto?view=azure-node-latest) objektumot.
* **URL-címek**esetén használja a karakterláncok listáját.

Hívja meg a [create](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#create-createkbdto--servicecallback-operation--) metódust, majd adja át a visszaadott művelet azonosítóját a [Operations. getDetails](#get-status-of-an-operation) metódusnak az állapot lekérdezéséhez.

[!code-javascript[Create a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=createkb&highlight=15)]

A Tudásbázis sikeres létrehozásához ellenőrizze, hogy a fenti kódban hivatkozott [`wait_for_operation`](#get-status-of-an-operation) függvény szerepeljen-e.

## <a name="update-a-knowledge-base"></a>Tudásbázis frissítése

A tudásbázist a Tudásbázis-azonosító és egy olyan [UpdateKbOperationDTO](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest) használatával frissítheti, amely a DTO objektumok [hozzáadását](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#add), [frissítését](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#update)és [törlését](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/updatekboperationdto?view=azure-node-latest#deleteproperty) tartalmazza a [frissítési](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#update-string--updatekboperationdto--msrest-requestoptionsbase-) metódushoz. A [Operation. getDetail](#get-status-of-an-operation) metódus használatával állapítsa meg, hogy a frissítés sikeres volt-e.

[!code-javascript[Update a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=updatekb&highlight=19)]

A Tudásbázis sikeres frissítéséhez ellenőrizze, hogy a fenti kódban hivatkozott [`wait_for_operation`](#get-status-of-an-operation) függvény szerepeljen-e.

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist a [közzétételi](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#publish-string--msrest-requestoptionsbase-) metódus használatával. Ez az aktuálisan mentett és betanított modellt veszi alapul, amelyet a Tudásbázis-azonosító hivatkozik, és egy végponton teszi közzé.

[!code-javascript[Publish a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=publishkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Törölje a tudásbázist a [delete](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/knowledgebase?view=azure-node-latest#deletemethod-string--msrest-requestoptionsbase-) metódus használatával a TUDÁSBÁZIS-azonosító paraméterével.

[!code-javascript[Delete a knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=deletekbs&highlight=2)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

Bizonyos metódusok, például a létrehozás és a frissítés, elegendő időt vehetnek igénybe, hogy a folyamat befejeződésére való várakozás helyett egy [műveletet](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operations?view=azure-node-latest) adjon vissza. Az eredeti metódus állapotának meghatározásához használja a művelet [azonosítóját](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-qnamaker/operation?view=azure-node-latest#operationid) a lekérdezésben (az újrapróbálkozási logikával).

A következő _setTimeout_ hívásával szimulálható az aszinkron kód. Cserélje le ezt az újrapróbálkozási logikával.

[!code-javascript[Monitor an operation](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js?name=monitorOperation&highlight=2,17)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást `node index.js` paranccsal az alkalmazás könyvtárából.


A cikkben szereplő kódrészletek mindegyike [elérhető](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.js) , és egyetlen fájlként is futtatható.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
>[Oktatóanyag: KB létrehozása és megválaszolása](../tutorials/create-publish-query-in-portal.md)

* [Mi a QnA Maker API?](../Overview/overview.md)
* [Tudásbázis szerkesztése](../how-to/edit-knowledge-base.md)
* [Használati elemzések beolvasása](../how-to/get-analytics-knowledge-base.md)