---
title: 'Gyors útmutató: űrlap-felismerő ügyféloldali kódtár vagy REST API'
titleSuffix: Azure Cognitive Services
description: Az űrlap-felismerő ügyféloldali kódtár vagy REST API használatával létrehozhat egy űrlap-feldolgozó alkalmazást, amely Kinyeri a kulcs/érték párokat és a tábla adatait az egyéni dokumentumokból.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: űrlapok feldolgozása, automatizált adatfeldolgozás
ms.openlocfilehash: c915e7753487eba284d89dbb480f9848e90c1ef1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132283"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Gyors útmutató: az űrlap-felismerő ügyféloldali kódtár vagy REST API használata

Az űrlap-felismerő használatának első lépései a választott nyelven. Az Azure Form-felismerő egy olyan kognitív szolgáltatás, amely lehetővé teszi a gépi tanulási technológia használatával automatizált adatfeldolgozási szoftverek készítését. Azonosíthatja és kinyerheti a szöveget, kulcs/érték párokat, a kiválasztási jeleket, a táblák adatait, valamint az űrlap dokumentumaiból származó adatokat, &mdash; a szolgáltatás az eredeti fájlban lévő kapcsolatokat tartalmazó strukturált adatokat jeleníti meg. Az űrlap-felismerőt a REST API vagy az SDK segítségével használhatja. Az alábbi lépéseket követve telepítheti az SDK-csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. 

Űrlap-felismerő használata a következőhöz:

* [Elrendezés elemzése](#analyze-layout)
* [Visszaigazolások elemzése](#analyze-receipts)
* [Üzleti kártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
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

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
