---
title: Mi az a tintafelismerő? - Tintafelismerő API
titleSuffix: Azure Cognitive Services
description: Integrálja a tintafelismerőt alkalmazásaiba, webhelyeibe, eszközeibe és egyéb megoldásaiba, hogy a szabadkézi körvonaladatok azonosíthatók és bemenetként használhatók legyenek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448150"
---
# <a name="what-is-the-ink-recognizer-api"></a>Mi az Ink Recognizer API?


A Tintafelismerő kognitív szolgáltatás felhőalapú REST API-t biztosít a digitális tintatartalom elemzéséhez és felismeréséhez. Az optikai karakterfelismerést (OCR) használó szolgáltatásokkal ellentétben az API-hoz digitális tintavonásos adatokra van szükség bemenetként. A digitális tollvonások 2D pontok (X, Y koordináták) időrendelt halmazai, amelyek a beviteli eszközök, például a digitális tollak vagy az ujjak mozgását jelölik. Ezután felismeri az alakzatokat és a kézzel írt tartalmat a bemenetből, és egy JSON-választ ad vissza, amely tartalmazza az összes felismert entitást.

![Folyamatábra, amely egy inkvonásbemenet et ír le az API-ba](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Szolgáltatások

A Tintafelismerő API-val könnyedén felismerheti a kézzel írt tartalmakat az alkalmazásokban. 

|Szolgáltatás  |Leírás  |
|---------|---------|
| Kézírás-felismerés | A kézzel írt tartalom felismerése 63 alapvető [nyelven és területi beállításban.](language-support.md) | 
| Elrendezés felismerése | Strukturális információk beszerezése a digitális tintatartalomról. Bontsa a tartalmat írási területekre, bekezdésekre, sorokra, szavakra, listajeles listákra. Az alkalmazások ezután az elrendezési információk segítségével további funkciókat hozhatnak létre, például automatikus listaformázást és alakzatigazítást. |
| Alakzat felismerése | A leggyakrabban használt [geometriai alakzatok felismerése](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) jegyzeteléskor. |
| Kombinált alakzatok és szövegfelismerés | Ismerje fel, hogy mely érintővonások tartoznak alakzatokhoz vagy kézzel írt tartalomhoz, és külön osztályozza őket.|

## <a name="workflow"></a>Munkafolyamat

A Tintafelismerő API egy RESTful webszolgáltatás, amely megkönnyíti a http-kérelmek et és a JSON elemzésre képes programozási nyelvről történő hívását.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

A regisztráció után:

1. Vegye ki a tollvonásadatait, és [formázza](concepts/send-ink-data.md#sending-ink-data) érvényes JSON-ra.
1. Küldjön egy kérést a Tintafelismerő API-nak az adataival együtt.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Próbálja ki a következő nyelveken a híváskezdeményezés megkezdéséhez a Tintafelismerő API-t.
* [C #](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [Javascript](quickstarts/javascript.md)

A szabadkézi elismerésapi digitális szabadkézi alkalmazásokban való működésének megtekintéséhez tekintse meg a githubon található alábbi mintaalkalmazásokat:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
