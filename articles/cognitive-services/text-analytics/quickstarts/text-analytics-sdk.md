---
title: 'Rövid útmutató: Text Analytics ügyféloldali kódtára | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban az Azure Cognitive Services Text Analytics API használatával ismeri fel a nyelvet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/23/2020
ms.author: aahi
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: 89680ad61296cd225bb87d2d6b46259586304da2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774106"
---
# <a name="quickstart-use-the-text-analytics-client-library-v3"></a>Rövid útmutató: a Text Analytics ügyféloldali kódtár használata

Ismerkedjen meg az Text Analytics ügyféloldali kódtár használatába. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

A végrehajtásához használja a Text Analytics ügyféloldali kódtár `3.0-preview` verzióját:

* Hangulat elemzése (nyilvános előzetes verzió)
* Nyelvfelismerés
* Elnevezett entitások felismerése (nyilvános előzetes verzió)
* A kulcsfontosságú kifejezések kinyerése

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# quickstart](../includes/quickstarts/v3/csharp.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python quickstart](../includes/quickstarts/v3/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java quickstart](../includes/quickstarts/v3/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/v3/nodejs-sdk.md)]

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megoldás megismerése](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [A Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)
