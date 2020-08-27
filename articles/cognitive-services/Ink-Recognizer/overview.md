---
title: Mi az a tinta felismerő? -Ink-felismerő API
titleSuffix: Azure Cognitive Services
description: Integrálja a kézírás-felismerőt az alkalmazásaiba, webhelyeire, eszközeibe és egyéb megoldásaiba, hogy a szabadkézi adatok azonosíthatók legyenek, és bemenetként legyenek használva.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/06/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 989c6464e45b95a276746b6d57e0ca08a345296c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931488"
---
# <a name="what-is-the-ink-recognizer-api"></a>Mi az Ink Recognizer API?


A tinta-felismerő kognitív szolgáltatás felhőalapú REST API biztosít a digitális tinta tartalmának elemzéséhez és felismeréséhez. Az optikai karakterfelismerést (OCR) használó szolgáltatásokkal ellentétben az API a digitális tinta adatkörvonalát igényli bemenetként. A digitális tollvonások a bemeneti eszközök, például a digitális tollak vagy az ujjak mozgását jelképező, időkeretű 2D pontok (X, Y koordináták). Ezután felismeri az alakzatokat és a kézzel írott tartalmat a bemenetből, és egy JSON-választ ad vissza, amely az összes felismert entitást tartalmazza.

![Egy, az API-ba irányuló tollvonási bemenet küldését leíró folyamatábra](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Szolgáltatások

A kézírás-felismerő API használatával könnyedén felismerheti az alkalmazásaiban található kézzel írt tartalmakat. 

|Szolgáltatás  |Leírás  |
|---------|---------|
| Kézírás-felismerés | A kézzel írt tartalmakat 63 fő [nyelven és területi beállításokban](language-support.md)ismerheti fel. | 
| Elrendezés felismerése | A digitális tinta tartalmával kapcsolatos szerkezeti információk beolvasása. A tartalom a régiók, a bekezdések, a sorok, a szavak és a listajeles listák megírásával szakítható meg. Az alkalmazások ezután az elrendezési adatok segítségével további funkciókat hozhatnak létre, például az automatikus lista formázását és az alakzat igazítását. |
| Alakzat felismerése | A leggyakrabban használt [geometriai alakzatok](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) felismerése Megjegyzés készítésekor. |
| Egyesített alakzatok és szöveges felismerés | Ismerje meg, hogy mely szabadkézi ecsetvonások tartoznak az alakzatokhoz vagy a kézzel írott tartalomhoz|

## <a name="workflow"></a>Munkafolyamat

A tinta-felismerő API egy REST-alapú webszolgáltatás, amely megkönnyíti a HTTP-kérelmeket és a JSON-elemzést elvégező programozási nyelv meghívását.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Regisztráció után:

1. Végezze el a szabadkézi körvonal adatait, és [formázza](concepts/send-ink-data.md#sending-ink-data) az érvényes JSON-t. Az API legfeljebb 1500 tollvonási ecsetvonást fogad el. 
1. Küldjön egy kérelmet a tinta-felismerő API-nak az adataival.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

A következő nyelveken kipróbálhat egy rövid útmutatót, amellyel megkezdheti a szabadkézi felismerő API hívását.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Ha szeretné megtudni, hogyan működik a Ink-felismerési API egy digitális, a GitHubon futó alkalmazásban, tekintse meg az alábbi példákat a GitHubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
