---
title: Példa a Text Analytics API felhasználói forgatókönyvei
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan integrálhatja a Text Analytics APIt a szolgáltatásaiba és folyamataiba.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: f11d23207a21af5c73831d0edd5ffc9eaa59c119
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280674"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Példa a Text Analytics API felhasználói forgatókönyvei

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett természetes nyelvi feldolgozást biztosít szövegen keresztül. Ez a cikk néhány példát mutat be arra, hogyan integrálhatja az API-t üzleti megoldásaiba és folyamataiba. 

## <a name="analyze-survey-results"></a>Felmérés eredményeinek elemzése

Elemzéseket készíthet az ügyfelek és az alkalmazottak felmérési eredményeiről azáltal, hogy a nyers szöveges válaszokat a Hangulatelemzés használatával dolgozza fel. Összesítheti az elemzés, a nyomon követés és a bevezetések eredményeit.

![Egy rendszerkép, amely leírja, hogyan végezheti el az ügyfelek és az alkalmazottak felméréseit.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Rögzített bejövő ügyfelek hívásainak elemzése

Text to Speech, Hangulatelemzés és Kulcsszókeresés használatával kinyerheti az ügyfelek által kezdeményezett bepillantást. Jelenítse meg az eredményeket Power BI irányítópulton vagy egy portálon az ügyfelek jobb megismeréséhez, az ügyfélszolgálat trendjeinek kiemeléséhez és az ügyfelek bevonásához. API-kéréseket küldhet a jelentéskészítési kötegként, vagy valós időben a beavatkozáshoz. Lásd: [mintakód](https://github.com/rlagh2/callcenteranalytics).

![Egy rendszerkép, amely leírja, hogyan automatizálható az ügyfélszolgálati hívások beszerzése az adatelemzések használatával](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Támogatási incidensek feldolgozása és kategorizálása

Az Kulcsszókeresés és az entitások felismerésével feldolgozhatja a strukturálatlan szöveges formátumban küldött támogatási kérelmeket. A kinyert kifejezésekkel és entitásokkal kategorizálhatja az erőforrás-tervezésre és a trend elemzésére vonatkozó kéréseket.

![Egy rendszerkép, amely leírja, hogyan használható a Key kifejezés kinyerése és az entitások felismerése az incidensek jelentéseinek és trendjeinek kategorizálásához](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>A termék közösségi média-hírcsatornáinak monitorozása

Felhasználói termékkel kapcsolatos visszajelzések figyelése a termék Twitter-vagy Facebook-oldalán. Az adatok segítségével elemezheti az ügyfelek hangulatát az új termékek bevezetésekor, kinyerheti a funkciókkal és szolgáltatásokkal kapcsolatos fontos kifejezéseket, vagy megtekintheti az ügyfelekre vonatkozó panaszokat. Tekintse meg a példa [Microsoft flow sablont](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Egy rendszerkép, amely leírja, hogyan figyelheti meg a termékeit és a vállalati visszajelzéseket a közösségi médián a kulcsfontosságú kifejezés kibontásával](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Következő lépések

* [Mi a Text Analytics API?](overview.md)
* [Kérelem küldése a Text Analytics APInak az ügyféloldali kódtár használatával](quickstarts/text-analytics-sdk.md)
