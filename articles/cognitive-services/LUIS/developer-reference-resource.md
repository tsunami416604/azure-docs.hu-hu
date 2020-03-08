---
title: Fejlesztői erőforrások – Language Understanding
description: Az SDK-k, a REST API-k, a CLI, a programozási nyelvben Language Understanding (LUIS) alkalmazások fejlesztését segítik. Kezelheti Azure-erőforrásait és a LUIS-előrejelzéseket.
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: f9d531f800ba0729a1f679c0bf3b11c8958c78ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391952"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>SDK, REST és CLI fejlesztői erőforrások a Language Understandinghoz (LUIS)

Az SDK-k, a REST API-k, a CLI, a programozási nyelvben Language Understanding (LUIS) alkalmazások fejlesztését segítik. Kezelheti Azure-erőforrásait és a LUIS-előrejelzéseket.

## <a name="azure-resource-management"></a>Azure-erőforrások kezelése

Az Azure Cognitive Services Felügyeleti réteg használatával hozhatja létre, szerkesztheti, listázhatja és törölheti a Language Understanding vagy a kognitív szolgáltatás erőforrását.

Az eszközön alapuló dokumentáció keresése:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Szerzői és előrejelzési kérelmek Language Understanding

A Language Understanding szolgáltatás elérhető egy Azure-erőforrásból, amelyet létre kell hoznia. Két erőforrás létezik:

* A **szerzői** erőforrással a létrehozás, szerkesztés, betanítás és közzététel lehetőséget használhatja.
* A futásidejű **előrejelzéssel** elküldheti a felhasználó szövegét, és előrejelzést kaphat.

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

[Cognitive Services mintakód](https://github.com/Azure-Samples/cognitive-services-quickstart-code) használatával megismerheti és használhatja a leggyakoribb feladatokat.

### <a name="rest-specifications"></a>REST-specifikációk

A [Luis Rest-specifikációk](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), valamint az összes [Azure Rest-specifikáció](https://github.com/Azure/azure-rest-api-specs)nyilvánosan elérhető a githubon.

### <a name="rest-apis"></a>REST API-k

A szerzői és előrejelzési végpont API-jai a REST API-kon keresztül érhetők el:

|Típus|Verzió|
|--|--|
|Tartalomkészítés|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[előzetes verzió – v3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Előrejelzés|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST-végpontok

A LUIS jelenleg két típusú végpontot tartalmaz:

* Szerzői műveletek a betanítási végponton
* lekérdezési előrejelzés a futásidejű végponton.

|Cél|URL-cím|
|--|--|
|Létrehozás a betanítási végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 futtatókörnyezet – a futásidejű végpont összes előrejelzése|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 futtatókörnyezet – verziók előrejelzése futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 futtatókörnyezet – a tárolóhelyek előrejelzése futásidejű végponton|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

Az alábbi táblázat az előző táblázatban szereplő, kapcsos zárójelekkel `{}`paramétereket ismerteti.

|Paraméter|Cél|
|--|--|
|`your-resource-name`|Azure-Erőforrás neve|
|`q` vagy `query`|például csevegőrobot ügyfélalkalmazástól utterance (kifejezés) szövegét|
|`version`|10 karakteres verzió neve|
|`slot`| `production` vagy `staging`|

### <a name="language-based-sdks"></a>Language-alapú SDK-k

|Nyelv |Dokumentáció|Csomag|Példák|Gyors útmutatók|
|--|--|--|--|--|
|C#|[Tartalomkészítés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Előrejelzés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet készítése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet előrejelzése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Alkalmazás létrehozása és kezelése](sdk-authoring.md?pivots=programming-language-csharp)<br>[Lekérdezés-előrejelzési végpont](sdk-query-prediction-endpoint.md)|
|Indítás|[Szerzői műveletek és előrejelzések](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Szerzői műveletek és előrejelzések a REST használatával](luis-get-started-get-intent-from-rest.md)|
|Java|[Szerzői műveletek és előrejelzések](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven-készítés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-előrejelzés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Szerzői műveletek és előrejelzések](luis-get-started-get-intent-from-rest.md)
|Node.js|[Tartalomkészítés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Előrejelzés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM készítése](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM előrejelzése](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Szerzői műveletek és előrejelzések a REST használatával](luis-get-started-get-intent-from-rest.md)|
|Python|[Szerzői műveletek és előrejelzések](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tartalomkészítés](sdk-authoring.md?pivots=programming-language-python)<br>[Előrejelzés REST használatával](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Tárolók

A Language Understanding (LUIS) egy [tárolót](luis-container-howto.md) biztosít az alkalmazás helyszíni és a benne foglalt verziói számára.

### <a name="export-and-import-formats"></a>Exportálási és importálási formátumok

Language Understanding lehetővé teszi, hogy az alkalmazás és a modelljei JSON formátumban, a `.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) formátumban és a Language Understanding tároló tömörített csomagjaival is kezelhetők legyenek.

Ezen formátumok importálása és exportálása az API-kon és a LUIS-portálon érhető el. A portál az alkalmazások listájának és a verziók listájának részeként biztosítja az importálást és az exportálást.

## <a name="other-tools-and-sdks"></a>Egyéb eszközök és SDK-k

A bot Framework számos különböző nyelven és szolgáltatásként, [Azure bot Service](https://dev.botframework.com/)használatával érhető el [SDK](https://github.com/Microsoft/botframework) -val.

A bot Framework [számos eszközt](https://github.com/microsoft/botbuilder-tools) biztosít a Language Understandinghoz, beleértve a következőket:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – Luis Language Understanding models – Markdown-fájlok használatával
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – a Luis.ai-alkalmazások létrehozása és kezelése
* [Feladás](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– szülő és gyermek alkalmazások kezelése
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – C#/Typescript-osztályok automatikus előállítása a Luis-szándékok és entitások számára.
* [Bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) – egy asztali alkalmazás, amely lehetővé teszi a robot-fejlesztők számára a bot Framework SDK használatával létrehozott robotok tesztelését és hibakeresését
* A [robot Framework zeneszerzője](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) – integrált fejlesztői eszköz a fejlesztők és a több-fegyelmi csapatok számára a robotok és a társalgási tapasztalatok létrehozásához a Microsoft bot Framework használatával

## <a name="next-steps"></a>Következő lépések

* A gyakori http- [hibakódok](luis-reference-response-codes.md) ismertetése
* Az API-k és SDK-k [hivatkozási dokumentációja](https://docs.microsoft.com/azure/index)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) és [Azure bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Kognitív tárolók](../cognitive-services-container-support.md)
