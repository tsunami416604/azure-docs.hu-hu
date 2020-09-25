---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár'
titleSuffix: Azure Cognitive Services
description: Az űrlap-felismerő ügyféloldali kódtár használatával létrehozhat egy űrlapot feldolgozó alkalmazást, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: űrlapok feldolgozása, automatizált adatfeldolgozás
ms.openlocfilehash: eb3fc3ebe5c3d9f659758a14ba620018671d95f5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318906"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>Gyors útmutató: az űrlap-felismerő ügyféloldali kódtár használata

Ismerkedjen meg az űrlap-felismerő ügyféloldali függvénytárával az Ön által választott nyelven. Az Azure Form-felismerő egy olyan kognitív szolgáltatás, amely lehetővé teszi a gépi tanulási technológia használatával automatizált adatfeldolgozási szoftverek készítését. Azonosíthatja és kinyerheti a szöveges, kulcs/érték párokat és táblázatos adatokat az űrlap dokumentumaiból &mdash; a szolgáltatás olyan strukturált adatokat jelenít meg, amelyek tartalmazzák az eredeti fájl kapcsolatait. Az alábbi lépéseket követve telepítheti az SDK-csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját.

Használja az űrlap-felismerő ügyféloldali függvénytárát a következőre:

* [Űrlap tartalmának felismerése](#recognize-form-content)
* [Visszaigazolások felismerése](#recognize-receipts)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end