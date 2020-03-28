---
title: 'Rövid útmutató: Text Analytics ügyféltár v3 | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval összekapcsolhatja alkalmazásait az Azure Cognitive Services Text Analytics API-jával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: cc3f48ec1113f954336cfae0bda2cba2499d9a1d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80240126"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Rövid útmutató: A Text Analytics-ügyféltár használata

Ismerkedés a Text Analytics ügyféltárral. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

A Szövegelemzési ügyféltár segítségével hajtsa végre a következőket:

* Hangulatelemzés
* Nyelvfelismerés
* Entitások felismerése
* Kulcskifejezések kinyerése

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A Text Analytics API legújabb `3.0-preview`előzetes verziója , amely nyilvános előnézetet tartalmaz a jobb [hangulatelemzéshez](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és [a megnevezett entitásfelismeréshez (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A legutolsó `2.1`stabil változat van.
>    * Ügyeljen arra, hogy csak a használt verzióra vonatkozó utasításokat kövesse.
> * A jelen cikkben szereplő kód egyszerű okokból szinkron metódusokat és nem biztonságos hitelesítő adatok tárolására használja. Éles környezetben azt javasoljuk, hogy a teljesítmény és a méretezhetőség a kötegelt aszinkron módszerek használata. Lásd az alábbi referenciadokumentációt.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Ez a rövid útmutató `3.0-preview` csak a Text Analytics ügyfélkódtár verziójára vonatkozik, amely nyilvános előzetes verziót tartalmaz a jobb [hangulatelemzéshez](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és [a NÉVvel ellátott entitásfelismeréshez (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * A jelen cikkben szereplő kód egyszerű okokból szinkron metódusokat és nem biztonságos hitelesítő adatok tárolására használja. Éles környezetben azt javasoljuk, hogy a teljesítmény és a méretezhetőség a kötegelt aszinkron módszerek használata. Lásd az alábbi referenciadokumentációt.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A Text Analytics API legújabb `3.0-preview`előzetes verziója , amely nyilvános előnézetet tartalmaz a jobb [hangulatelemzéshez](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és [a megnevezett entitásfelismeréshez (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A legutolsó `2.1`stabil változat van.
>    * Ügyeljen arra, hogy csak a használt verzióra vonatkozó utasításokat kövesse.
> * A jelen cikkben szereplő kód egyszerű okokból szinkron metódusokat és nem biztonságos hitelesítő adatok tárolására használja. Éles környezetben azt javasoljuk, hogy a teljesítmény és a méretezhetőség a kötegelt aszinkron módszerek használata. Lásd az alábbi referenciadokumentációt.
> * A Text Analytics ügyfélkönyvtár ezen verzióját a [böngészőben](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)is futtathatja.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A Text Analytics API legújabb `3.0-preview`előzetes verziója , amely nyilvános előnézetet tartalmaz a jobb [hangulatelemzéshez](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és [a megnevezett entitásfelismeréshez (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A legutolsó `2.1`stabil változat van.
>    * Ügyeljen arra, hogy csak a használt verzióra vonatkozó utasításokat kövesse.
> * A jelen cikkben szereplő kód egyszerű okokból szinkron metódusokat és nem biztonságos hitelesítő adatok tárolására használja. Éles környezetben azt javasoljuk, hogy a teljesítmény és a méretezhetőség a kötegelt aszinkron módszerek használata. Lásd az alábbi referenciadokumentációt. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>További nyelvi támogatás

Ha erre a lapra kattintott, valószínűleg nem látott rövid útmutatót a kedvenc programozási nyelvén. Ne aggódjon, további rövid útmutatóink is rendelkezésre állnak. A táblázat segítségével megtalálhatja a programozási nyelvnek megfelelő mintát.

| Nyelv | Elérhető verzió | 
|----------|------------------------|
| Ruby     | [2.1-es verzió](ruby-sdk.md) | 
| Indítás       | [2.1-es verzió](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a hozzá társított egyéb erőforrásokat is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megoldás felfedezése](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Szövegelemzés – áttekintés](../overview.md)
* [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvfelismerés](../how-tos/text-analytics-how-to-language-detection.md)
