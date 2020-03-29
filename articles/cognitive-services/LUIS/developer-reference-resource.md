---
title: Fejlesztői források - Nyelvtudás
description: SDK-k, REST API-k, CLI, segítségével fejlesztheti a nyelvtudás (LUIS) alkalmazásokat a programozási nyelven. Kezelje az Azure-erőforrásokat és a LUIS-előrejelzéseket.
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 5e375157cef4789bc2980f6154ea8d59e765ff3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457984"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK-, REST- és CLI-fejlesztői erőforrások a nyelvismertetéshez (LUIS)

SDK-k, REST API-k, CLI, segítségével fejlesztheti a nyelvtudás (LUIS) alkalmazásokat a programozási nyelven. Kezelje az Azure-erőforrásokat és a LUIS-előrejelzéseket.

## <a name="azure-resource-management"></a>Azure erőforrás-kezelés

Az Azure Cognitive Services Felügyeleti réteg használatával hozza létre, szerkessze, listázhatja és törölje a nyelvi megértés vagy a Cognitive Service erőforrást.

Az eszköz alapján találreferencia-dokumentációt:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Nyelvismertetési kérelmek készítése és előrejelzése

A Language Understanding szolgáltatás egy Azure-erőforrásból érhető el, amelyet létre kell hoznia. Két forrás létezik:

* A **szerzői** erőforrás segítségével hozzon létre, szerkeszt, betanítása és közzététele.
* Használja a futásidejű **előrejelzést** a felhasználó szövegének elküldéséhez és egy előrejelzés fogadásához.

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

A [Cognitive Services mintakódjával](https://github.com/Azure-Samples/cognitive-services-quickstart-code) megismerése és használata a leggyakoribb feladatok.

### <a name="rest-specifications"></a>REST specifikációk

A [LUIS REST-specifikációk,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)valamint az összes [Azure REST-specifikáció nyilvánosan](https://github.com/Azure/azure-rest-api-specs)elérhető a GitHubon.

### <a name="rest-apis"></a>REST API-k

Mind a szerzői, mind az előrejelzési végpontAPI-k a REST API-kból érhetők el:

|Típus|Verzió|
|--|--|
|Tartalomkészítés|[2. verzió](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[előnézet V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Előrejelzés|[2. verzió](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-végpontok

A LUIS jelenleg kétféle végponttal rendelkezik:

* szerzői a betanítási végponton
* lekérdezés-előrejelzést a futásidejű végponton.

|Cél|URL-cím|
|--|--|
|A betanítási végpont szerkesztése|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 futásidejű – a futásidejű végpont összes előrejelzése|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3-as futásidejű – verziók előrejelzése a futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 futásidejű – slot-előrejelzés a futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Az alábbi táblázat az előző táblázatban a `{}`kapcsos zárójelekkel jelölt paramétereket ismerteti.

|Paraméter|Cél|
|--|--|
|`your-resource-name`|Az Azure-erőforrás neve|
|`q` vagy `query`|utterance szöveget küldött kliens alkalmazás, mint a chat bot|
|`version`|10 karakteres verziónév|
|`slot`| `production` vagy `staging`|

### <a name="language-based-sdks"></a>Nyelvalapú SDK-k

|Nyelv |Referenciadokumentáció|Csomag|Példák|Rövid útmutatók|
|--|--|--|--|--|
|C#|[Tartalomkészítés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Előrejelzés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet szerzői](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet előrejelzés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Alkalmazás létrehozása és kezelése](sdk-authoring.md?pivots=programming-language-csharp)<br>[Előrejelzési végpont lekérdezése](sdk-query-prediction-endpoint.md)|
|Indítás|[Szerzői és előrejelzés](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Szerkesztés és előrejelzés rest használatával](luis-get-started-get-intent-from-rest.md)|
|Java|[Szerzői és előrejelzés](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven szerzői](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven előrejelzés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Szerzői és előrejelzés](luis-get-started-get-intent-from-rest.md)
|Node.js|[Tartalomkészítés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Előrejelzés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM-szerzői jog](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM előrejelzés](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Szerkesztés és előrejelzés rest használatával](luis-get-started-get-intent-from-rest.md)|
|Python|[Szerzői és előrejelzés](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tartalomkészítés](sdk-authoring.md?pivots=programming-language-python)<br>[Előrejelzés rest használatával](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

A Language Understanding (LUIS) egy [tárolót](luis-container-howto.md) biztosít az alkalmazás helyszíni és tartalmazott verzióinak biztosításához.

### <a name="export-and-import-formats"></a>Formátumok exportálása és importálása

A Language Understanding lehetővé teszi az alkalmazás és modelljeijének JSON-formátumban, a `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) formátumban történő kezelését, valamint a Nyelvközt évközit tartalmazó tároló tömörített csomagjának kezelését.

Ezek a formátumok importálása és exportálása az API-kból és a LUIS portálról érhető el. A portál az Alkalmazások és verziók lista részeként biztosítja az importálást és az exportálást.

## <a name="other-tools-and-sdks"></a>Egyéb eszközök és SDK-k

A robotkeretrendszer [SDK-ként](https://github.com/Microsoft/botframework) érhető el különböző nyelveken és az [Azure Bot Service](https://dev.botframework.com/)szolgáltatásként.

A Bot framework [számos eszközt](https://github.com/microsoft/botbuilder-tools) kínál a nyelvismertetéshez, többek között a következőket:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – A LUIS nyelvi megértési modelljeinek létrehozása markdown-fájlok használatával
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – A LUIS.ai alkalmazások létrehozása és kezelése
* [Feladás](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– szülő- és gyermekalkalmazások kezelése
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – Automatikus biztonsági mentési C#/Typescript osztályok létrehozása a LUIS-leképezések és entitások.
* [Bot Framework emulátor](https://github.com/Microsoft/BotFramework-Emulator/releases) - egy asztali alkalmazás, amely lehetővé teszi a bot fejlesztők számára, hogy teszteljék és hibakeresés botok segítségével épített Bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) - integrált fejlesztési eszköz a fejlesztők és a multidiszciplináris csapatok építeni botok és társalgási tapasztalatok a Microsoft Bot Framework
* [microsoft/NLU. DevOps](https://github.com/microsoft/NLU.DevOps) – Az NLU-szolgáltatások folyamatos integrációját és üzembe helyezését támogató eszközök.

## <a name="next-steps"></a>További lépések

* További információ a [http-hibakódokról](luis-reference-response-codes.md)
* [Referenciadokumentáció](https://docs.microsoft.com/azure/index) az összes API-hoz és SDK-hoz
* [Bot keretrendszer](https://github.com/Microsoft/botbuilder-dotnet) és [az Azure Bot Service](https://dev.botframework.com/)
* [LUDown (LUDown)](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitív tárolók](../cognitive-services-container-support.md)
