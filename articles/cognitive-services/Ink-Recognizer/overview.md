---
title: Mi az Ink felismerő? – Tinta felismerő API
titlesuffix: Azure Cognitive Services
description: Az alkalmazások, webhelyek, eszközök és más megoldásokat biztosít a szabadkézi felismerő integrálása...
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ed1a72a5dc61458200b72c768ad722656b820d8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65027228"
---
# <a name="what-is-the-ink-recognizer-api"></a>Mi az Ink Recognizer API?


A szabadkézi felismerő Cognitive Service felhőalapú REST API-t biztosít elemzéséhez, és ismeri fel a digitális ink tartalmat. Optikai karakterfelismerés (OCR) használó szolgáltatások, ellentétben az API bemenetként digitális ink körvonal adatokra van szüksége. Digitális tollvonások idő rendezett készleteket 2D pontok (X, Y koordinátáit), amelyek a mozgásban lévő bemeneti eszközök, például a digitális tollak vagy adatelemzéseket is. Ezután és alakzatokat és a bemeneti kézzel írt tartalmak, és minden felismert entitásokat tartalmazó JSON-választ adja vissza.

![Egy leíró küldése az API-t ink körvonal bemenete folyamatábrája](media/ink-recognizer-pen-graph.png)

## <a name="features"></a>Funkciók

A tinta felismerő API-val könnyen megjegyezhető kézzel írt tartalmak az alkalmazásokban. 

|Szolgáltatás  |Leírás  |
|---------|---------|
| Kézírás-felismerés | Ismeri fel a 63 core kézzel írt tartalmak [nyelv és területi beállítások](language-support.md). | 
| Elrendezés felismerése | A digitális ink tartalom szerkezeti adatainak beolvasása. A tartalom felosztása írása a régiók, bekezdések, sorok, szavakat, listajeles listák készítéséhez. Az alkalmazások ezután az elrendezés információk segítségével hozhat létre további szolgáltatások, mint az automatikus lista formázásának, és formázhatja igazítását. |
| Alakzat felismerése | Ismeri fel a leggyakrabban használt [geometriai alakzatok](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) véve a megjegyzések. |
| Kombinált alakzatokat és szövegek felismerése | Melyik festék vonások alakzatok vagy kézzel írt tartalmak tartozik ismeri fel, és külön-külön őket classify.|

## <a name="workflow"></a>Munkafolyamat

A szabadkézi felismerő API egy REST-alapú webszolgáltatás, így könnyen hívása minden programozási nyelvet, amely HTTP-kérelmeket és JSON elemzése.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Regisztrációt követő lépések:

1. Az ink körvonal adatok és [formázza azt](concepts/send-ink-data.md#sending-ink-data) érvényes JSON-ba.
1. A kérés küldése a szabadkézi felismerő API adatait.
1. Az API válaszának feldolgozásához elemezze a visszaadott JSON-üzenetet.

## <a name="next-steps"></a>További lépések

Próbálja ki a rövid útmutató a szabadkézi felismerő API-hívások indításához az alábbi nyelveken.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

A tinta Recognition API működését egy digitális szabadkézi alkalmazás megtekintéséhez tekintse meg a következő minta alkalmazásokat a Githubon:
* [C# és Univerzális Windows-platform (Universal Windows Platform, UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# és Windows megjelenítési alaprendszer (Windows Presentation Foundation, WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript-webböngészőalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java- és Android-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift- és iOS-mobilalkalmazás](https://go.microsoft.com/fwlink/?linkid=2089805)
