---
title: Példa felhasználói forgatókönyvekre a Text Analytics API-hoz
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből néhány gyakori forgatókönyvet láthat a Text Analytics API szolgáltatásokba és folyamatokba való integrálására.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 6847059de2a8685a56719f07a041a40456f2aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219246"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Példa felhasználói forgatókönyvekre a Text Analytics API-hoz

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett természetes nyelvi feldolgozást biztosít a szövegen keresztül. Ez a cikk néhány példa használati esetek az API-t az üzleti megoldások és folyamatok integrálására. 

## <a name="analyze-survey-results"></a>Felmérés eredményeinek elemzése

Az ügyfél- és alkalmazotti felmérés eredményeiből elemzéseket nyerhet a nyers szöveges válaszok hangulatelemzés használatával történő feldolgozásával. Az eredmények összesítése elemzés, nyomon követés és vezetői kötelezettségvállalás.

![Az ügyfél- és alkalmazotti felmérések hangulatelemzését leíró kép.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Rögzített bejövő vevői hívások elemzése

Elemzéseket nyerhet ki az ügyfélszolgálati hívásokból a szöveg-beszéd, a hangulatelemzés és a kulcskifejezések kinyerése használatával. Az eredményeket a Power BI irányítópultján vagy egy portálon jelenítheti meg az ügyfelek jobb megértése, az ügyfélszolgálati trendek kiemelése és az ügyfelek elköteleződésének ösztönzése érdekében. API-kérelmek küldése kötegként jelentéskészítéshez, vagy valós időben a beavatkozáshoz. Tekintse meg a mintakódot [a GitHubon.](https://github.com/rlagh2/callcenteranalytics)

![Az ügyfélszolgálati hívásokból származó elemzések hangulatelemzéssel történő beszerzésének automatizálását lehetővé tévő kép](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Támogatási incidensek feldolgozása és kategorizálása

A kulcskifejezések kinyerése és az entitásfelismerés segítségével strukturálatlan szöveges formátumban benyújtott támogatási kérelmeket dolgozfel. A kinyert kifejezések és entitások segítségével kategorizálhatja az erőforrás-tervezési és trendelemzési kérelmeket.

![Az incidensjelentések és -trendek kategorizálása kulcskifejezések kinyerésének és entitásfelismerésének kategorizálását leíró kép](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>A termék közösségi média hírcsatornáinak figyelése

Figyelemmel kísérheti a felhasználói termékek visszajelzéseit a termék Twitter vagy Facebook oldalán. Az adatok segítségével elemezheti az ügyfelek hangulatát az új termékek bevezetése felé, kinyerheti a funkciókkal és a szolgáltatáskérésekkel kapcsolatos legfontosabb kifejezéseket, vagy megoldhatja az ügyfelek panaszait. Lásd a példa [Microsoft Flow sablont](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Egy kép, amely leírja, hogyan figyelheti a terméket és a vállalati visszajelzéseket a közösségi médiában a kulcskifejezések kinyerésével](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Bizalmas adatokat tartalmazó dokumentumok osztályozása és kivonása

A Névvel ellátott entitásfelismerés segítségével azonosíthatja a dokumentumokban lévő személyes és bizalmas adatokat. Az adatok segítségével osztályozhatja a dokumentumokat, vagy kiújíthatja őket, hogy azok biztonságosan megoszthatók legyenek.

![A NER segítségével a személyes adatok észlelése, valamint a dokumentumok osztályozása és kivonása című kép](media/use-cases/sensitive-docs.jpg)

## <a name="next-steps"></a>További lépések

* [Mi a Text Analytics API?](overview.md)
* [Kérelem küldése a Text Analytics API-nak az ügyféltár használatával](quickstarts/text-analytics-sdk.md)
