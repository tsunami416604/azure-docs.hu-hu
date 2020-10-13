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
ms.openlocfilehash: b024b3fac76e51264e89100e63a3cdc477443157
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963051"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>Gyors útmutató: az űrlap-felismerő ügyféloldali kódtár használata

Ismerkedjen meg az űrlap-felismerő ügyféloldali függvénytárával az Ön által választott nyelven. Az Azure Form-felismerő egy olyan kognitív szolgáltatás, amely lehetővé teszi a gépi tanulási technológia használatával automatizált adatfeldolgozási szoftverek készítését. Azonosíthatja és kinyerheti a szöveges, kulcs/érték párokat és táblázatos adatokat az űrlap dokumentumaiból &mdash; a szolgáltatás olyan strukturált adatokat jelenít meg, amelyek tartalmazzák az eredeti fájl kapcsolatait. Az alábbi lépéseket követve telepítheti az SDK-csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. Az űrlap-felismerő ügyféloldali kódtár jelenleg a from felismerő szolgáltatás v 2.0-s verzióját célozza meg.

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