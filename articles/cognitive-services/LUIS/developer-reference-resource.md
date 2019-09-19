---
title: Fejlesztői erőforrások – Language Understanding
titleSuffix: Azure Cognitive Services
description: A fejlesztők a REST API-kkal és SDK-kkal is rendelkeznek Language Understandinghoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 01d01461c540949693855ca407920d52b22ad714
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106855"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding fejlesztői erőforrásai

A fejlesztők a REST API-kat és az SDK-kat is használhatják Language Understandinghoz. 

## <a name="azure-resource-management"></a>Azure-erőforrások kezelése

Az Azure Cognitive Services Felügyeleti réteg használatával hozhatja létre, szerkesztheti, listázhatja és törölheti a Language Understanding vagy a kognitív szolgáltatás erőforrását.

Az eszközön alapuló dokumentáció keresése:

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest#az_cognitiveservices_list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Szerzői és előrejelzési kérelmek Language Understanding

A Language Understanding szolgáltatás elérhető egy Azure-erőforrásból, amelyet létre kell hoznia. Két erőforrás létezik: létrehozási és előrejelzési végponti erőforrások. Mindkét erőforrás lehetővé teszi a LUIS-erőforrások szabályozását. 

### <a name="rest-apis"></a>REST API-k

A szerzői és előrejelzési végpont API-jai a REST API-kon keresztül érhetők el:

* [Útmutató](https://go.microsoft.com/fwlink/?linkid=2092087) a szerzői műveletekhez
* Az előrejelzési futtatókörnyezet [dokumentációja](https://go.microsoft.com/fwlink/?linkid=2092356)

### <a name="language-based-sdks"></a>Language-alapú SDK-k

|Nyelv |Segédanyagok|Csomag|Példák|Gyorsútmutatók|
|--|--|--|--|--|
|C#|[Tartalomkészítés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Előrejelzés](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet készítése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet előrejelzése](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[Alkalmazás létrehozása és kezelése](sdk-csharp-quickstart-authoring-app.md)<br>[Lekérdezés-előrejelzési végpont](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Indítás|[Szerzői műveletek és előrejelzések](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[Szerzői műveletek a REST használatával](luis-get-started-go-add-utterance.md)<br>[Előrejelzés REST használatával](luis-get-started-go-get-intent.md)|
|Java|[Szerzői műveletek és előrejelzések](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven-készítés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven-előrejelzés](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[Tartalomkészítés](luis-get-started-java-add-utterance.md)<br>[Előrejelzés](luis-get-started-java-get-intent.md)
|Node.js|[Tartalomkészítés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Előrejelzés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM készítése](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM előrejelzése](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[Előrejelzés](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[Szerzői műveletek a REST használatával](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[Előrejelzés REST használatával](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[Szerzői műveletek és előrejelzések](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Tartalomkészítés](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[Tartalomkészítés](sdk-python-quickstart-authoring-app.md)<br>[Előrejelzés REST használatával](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>Egyéb eszközök és SDK-k

A bot Framework számos különböző nyelven és szolgáltatásként, [Azure bot Service](https://dev.botframework.com/)használatával érhető el [SDK](https://github.com/Microsoft/botframework) -val. 

A bot Framework [számos eszközt](https://github.com/microsoft/botbuilder-tools) biztosít a Language Understandinghoz, beleértve a következőket:

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) – Luis Language Understanding models – Markdown-fájlok használatával
* [Luis CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) – a Luis.ai-alkalmazások létrehozása és kezelése
* [Feladás](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)– szülő és gyermek alkalmazások kezelése
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) – C#/Typescript-osztályok automatikus előállítása a Luis-szándékok és entitások számára.
* [Robot-emulátor](https://github.com/Microsoft/BotFramework-Emulator/releases) – egy asztali alkalmazás, amely lehetővé teszi a robot-fejlesztők számára a bot Framework SDK használatával létrehozott robotok tesztelését és hibakeresését


## <a name="next-steps"></a>További lépések

A gyakori http- [hibakódok](luis-reference-response-codes.md) ismertetése