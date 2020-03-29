---
title: 'Rövid útmutató: QnA Maker REST API-kkal a Node.js'
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a QnA Maker REST API-k at Node.js. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: ecc3fb144fb4b4e27182567925199f841b1c4357
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851673"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>Rövid útmutató: QnA Maker REST API-k a Node.js-hez

Ismerkedés a QnA Maker REST API-k node.js. Az alábbi lépésekkel próbálja ki az alapfeladatok példakódját.  A QnA Maker lehetővé teszi egy kérdés-válasz szolgáltatás működtetését olyan félig strukturált tartalomból, mint a GYIK-dokumentumok, URL-címek és termékútmutatók.

A Node.js qna maker REST API-jainak használatával:

* Tudásbázis létrehozása
* Tudásbázis cseréje
* Tudásbázis közzététele
* Tudásbázis törlése
* Tudásbázis letöltése
* Művelet állapotának beszereznie

[Referenciadokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js Minták](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org)jelenlegi verziója .
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs és a végpont (amely tartalmazza az erőforrás nevét) lekéréséhez válassza az erőforrás **gyorsindítását** az Azure Portalon.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a QnA Maker számára az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI-n](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) keresztül a helyi gépen.

Miután beszerzett egy kulcsot az erőforrásból, [hozzon létre környezeti változókat](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) az erőforráshoz, amelynek neve és. `QNAMAKER_RESOURCE_KEY` `QNAMAKER_AUTHORING_ENDPOINT` Használja a kulcs és a végpont értékek et az erőforrás **gyorsindítási** lapján az Azure Portalon.

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt.

```console
mkdir myapp && cd myapp
```

Csomópontfájl `npm init -y` létrehozásához futtassa a parancsot. `package.json`

```console
npm init -y
```

Adja `reqeuestretry` hozzá `request` a és az NPM csomagokat:

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a QnA Maker REST API-kkal a Node.js-hez:

* [Tudásbázis létrehozása](#create-a-knowledge-base)
* [Tudásbázis cseréje](#replace-a-knowledge-base)
* [Tudásbázis közzététele](#publish-a-knowledge-base)
* [Tudásbázis törlése](#delete-a-knowledge-base)
* [Tudásbázis letöltése](#download-the-knowledge-base)
* [Művelet állapotának beszereznie](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

Hozzon létre `rest-apis.js` egy nevű fájlt, és adja hozzá a következő _szükséges_ utasítást, hogy HTTP-kérelmeket.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Azure-erőforrásadatok hozzáadása

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. Ha az alkalmazás elindítása után hozta létre a környezeti változót, be kell zárnia, majd újra meg kell nyitnia a változót futtató szerkesztőt, IDE-t vagy rendszerhéjat.

Állítsa be a következő környezeti értékeket:

* `QNAMAKER_RESOURCE_KEY`- A **kulcs** egy 32 karakterből álló karakterlánc, és elérhető az Azure Portalon, a QnA Maker erőforrás, a **Gyorsindítás** oldalon. Ez nem ugyanaz, mint az előrejelzési végpont kulcs.
* `QNAMAKER_AUTHORING_ENDPOINT`- A szerzői végpont, a `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formátumban , tartalmazza az **erőforrás nevét**. Ez nem ugyanaz az URL-cím, amelyet az előrejelzési végpont lekérdezéséhez használnak.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

A tudásbázis a következő JSON-objektumból létrehozott kérdés- és válaszpárokat tárolja:

* **Szerkesztői tartalom**.
* **Fájlok** – olyan helyi fájlok, amelyek hez nincs szükség engedélyre.
* **URL-címek** - nyilvánosan elérhető URL-ek.

A [REST API segítségével hozzon létre egy tudásbázist.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>Tudásbázis cseréje

A [REST API-val helyettesítsen egy tudásbázist.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Tegye közzé a tudásbázist. Ez a folyamat elérhetővé teszi a tudásbázist egy HTTP-lekérdezés-előrejelzési végpontról.

A [REST API segítségével tegye közzé a tudásbázist.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>A tudásbázis letöltése

A [REST API segítségével töltsön le egy tudásbázist.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>Tudásbázis törlése

Ha végzett a tudásbázis használatával, törölje azt.

A [REST API segítségével töröljön egy tudásbázist.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>Művelet állapotának beszereznie

Hosszú ideig futó folyamatok, például a létrehozási folyamat egy műveletazonosítót ad vissza, amelyet egy külön REST API-hívással kell ellenőrizni. Ez a függvény a create válasz törzsét veszi fel. A fontos kulcs `operationState`a , amely meghatározza, hogy folytatnia kell-e a lekérdezést.

A [REST API segítségével figyelheti a tudásbázis műveleteit.](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa `node rest-apis.js` az alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Oktatóanyag: Kb. létrehozása és megválaszolása](../tutorials/create-publish-query-in-portal.md)

* [Mi a QnA Maker API?](../Overview/overview.md)
* [Tudásbázis szerkesztése](../how-to/edit-knowledge-base.md)
* [Használati elemzések beszereznie](../how-to/get-analytics-knowledge-base.md)
* A minta forráskódja megtalálható a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js)