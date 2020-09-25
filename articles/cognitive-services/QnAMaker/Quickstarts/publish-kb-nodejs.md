---
title: 'Gyors útmutató: QnA Maker REST API-kkal Node.js'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a Node.js QnA Maker REST API-jait. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-js
ms.topic: how-to
ms.openlocfilehash: 67c4711d74bdeb970a706eabf795e8f9bdcbbded
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324603"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Gyors útmutató: QnA Maker REST API-k Node.js

Ismerkedjen meg a Node.js QnA Maker REST API-kkal. Az alábbi lépéseket követve próbálja ki az alapszintű feladatokhoz tartozó példa kódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.

Használja a QnA Maker REST API-kat a Node.jshoz:

* Tudásbázis létrehozása
* Tudásbázis cseréje
* Tudásbázis közzététele
* Tudásbázis törlése
* Tudásbázis letöltése
* Művelet állapotának beolvasása

[Dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Node.js minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)
* A [Node.js](https://nodejs.org)aktuális verziója.
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást QnA Maker a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával.

Miután beolvasott egy kulcsot az erőforrásból, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) és nevű erőforráshoz `QNAMAKER_RESOURCE_KEY` `QNAMAKER_AUTHORING_ENDPOINT` . Használja az erőforrás rövid **útmutató lapján található** kulcs-és végpont-értékeket a Azure Portal.

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init -y` parancsot a Node-fájl létrehozásához `package.json` .

```console
npm init -y
```

Adja hozzá a `reqeuestretry` és a `request` NPM csomagokat:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Node.js QnA Maker REST API-kkal:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis cseréje](#replace-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Tudásbázis letöltése](#download-the-knowledge-base)
* [Művelet állapotának beolvasása](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Hozzon létre egy nevű fájlt `rest-apis.js` , és adja hozzá az alábbi függőségeket.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="dependencies":::

## <a name="add-utility-functions"></a>Segédprogram-függvények hozzáadása

Adja hozzá a következő segédprogram-függvényeket.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="utility":::

## <a name="add-azure-resource-information"></a>Azure-erőforrás adatainak hozzáadása

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt.

Állítsa be a következő környezeti értékeket:

* `QNAMAKER_RESOURCE_KEY` – A **kulcs** egy 32 karakterből álló karakterlánc, amely a Azure Portal QnA Maker erőforrásban, a **gyors üzembe helyezés** lapon érhető el. Ez nem ugyanaz, mint az előrejelzési végpont kulcsa.
* `QNAMAKER_AUTHORING_ENDPOINT` – A szerzői végpont a formátumban `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` tartalmazza az **erőforrás nevét**. Ez nem ugyanaz az URL-cím, amely az előrejelzési végpont lekérdezésére szolgál.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="authorization":::

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A Tudásbázis a következő JSON-objektumból létrehozott kérdés-válasz párokat tárolja:

* **Szerkesztői tartalom**.
* **Fájlok** – nem szükséges engedélyekkel rendelkező helyi fájlok.
* **URL-címek** – nyilvánosan elérhető URL-címek.

[Hozzon létre egy tudásbázist a REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)használatával.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="createKb":::

## <a name="replace-a-knowledge-base"></a>Tudásbázis cseréje

A [Tudásbázis cseréjéhez](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)használja a REST API.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="replaceKb":::

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist. Ez a folyamat teszi elérhetővé a tudásbázist egy HTTP-lekérdezés előrejelzési végpontja alapján.

A [Tudásbázis közzétételéhez](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)használja a REST API.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="publishKb":::

## <a name="download-the-knowledge-base"></a>A Tudásbázis letöltése

A [Tudásbázis letöltéséhez](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)használja a REST API.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="downloadKb":::

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Ha elkészült a Tudásbázis használatával, törölje azt.

A [Tudásbázis törléséhez](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)használja a REST API.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="deleteKb":::

## <a name="get-status-of-an-operation"></a>Művelet állapotának beolvasása

A hosszú ideig futó folyamatok, például a létrehozási folyamat visszaad egy műveleti azonosítót, amelyet külön REST API hívással kell ellenőrizni. Ez a függvény a létrehozási válasz törzsét veszi át. A fontos kulcs a `operationState` , amely meghatározza, hogy folytatni kell-e a lekérdezést.

A [REST API használatával figyelheti a műveleteket a Tudásbázisban](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails).

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="operationDetails":::

## <a name="add-main-method"></a>Main metódus hozzáadása

Adja hozzá a következő `main` metódust.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/QnAMaker/rest/create-publish-kb.js" id="main":::

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node rest-apis.js` paranccsal az alkalmazás könyvtárából.

```console
node rest-apis.js
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
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js)található.
