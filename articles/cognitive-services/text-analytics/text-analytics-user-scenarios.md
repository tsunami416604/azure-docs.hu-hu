---
title: Felhasználói bemutató példák a szövegelemzési API
titleSuffix: Azure Cognitive Services
description: Ez a cikk segítségével megtekintheti a szövegelemzési API integrálása a szolgáltatások és -folyamatok számára néhány gyakori forgatókönyvet.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/04/2019
ms.author: aahi
ms.openlocfilehash: d8d2aed16b5af60c44501e6d72332783a164ff91
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478388"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Felhasználói bemutató példák a szövegelemzési API

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett, természetes nyelvi feldolgozás szöveg felett. Ez a cikk ismerteti az egyes használati példák esetében az API-t integrálása az üzleti megoldásokat és folyamatokat. 

## <a name="analyze-survey-results"></a>Elemezheti a felmérés eredménye

Hasznos információkhoz juthat az ügyfelek és alkalmazottak bizalmas felmérés eredménye a nyers szöveg válaszok API használatával Hangulatelemzés feldolgozásával. Összesítés a csapatával az eredményeket elemzéshez, nyomon követése, és a vezetési bevonására.

![Kép arról, hogyan hangulatelemzés az ügyfelek és alkalmazottak bizalmas felmérések végrehajtásához.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Rögzített bejövő ügyfelek hívásainak elemzése

Elemzési adatokat nyerhet ki a vásárlói szolgáltatások szöveg-beszéd átalakítás, Hangulatelemzés és Kulcskifejezések kulcs használatával. Az eredmények megjelenítése a Power BI-irányítópultra vagy jobban megismerheti ügyfelei, jelölje ki a vásárlói szolgáltatás trendeket és a meghajtó customer engagement portálon. Küldje el API-kérések egy kötegelt jelentéskészítéshez vagy a valós idejű beavatkozás. Lásd: [mintakód](https://github.com/rlagh2/callcenteranalytics).

![Egy képet arról, hogyan automatizálhatja származó ügyfélszolgálati hívások hangulatelemzés használatával](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Dolgozza fel, és a támogatási incidensek kategorizálása

Kulcs kulcsszókeresés és entitások felismerése használatával elküldött strukturálatlan szöveges formátum támogatási kérelmeket. A kibontott mondatokat és entitások használatával kategorizálása a kérelem erőforrás tervezési és trendelemzés céljából.

![Kép: az incidensekkel kapcsolatos jelentéseket és a trendek csoportosítására a kulcsfontosságú kifejezések kinyerése és egyéb entitások felismerése használata](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>A termék, közösségi hírcsatornákról figyelése

Felhasználói visszajelzés a termék twitter vagy Facebook-oldalon figyelheti. Új termék világpiaci felé az ügyfelek hangulatának elemzéséhez, kinyerheti a kulcsfontosságú kifejezéseket, szolgáltatásokat és a funkciókérések vagy a cím felhasználói panaszok, ahogy azok az adatok használatával.

![Egy képet arról, hogyan figyelheti a termék- és visszajelzés a közösségi média kulcsszókeresést használatával](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>További lépések

* [Mi az a szövegelemzési API-t?](overview.md)
* [A Text Analytics API használatával egy kérelem küldéseC#](quickstarts/csharp.md)
