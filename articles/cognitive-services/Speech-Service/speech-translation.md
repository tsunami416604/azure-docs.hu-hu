---
title: Beszéd fordítási kapcsolatos |} Microsoft Docs
description: A beszédfelismerés fordítási lehetőségeinek áttekintését
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: 43fcde887c21794989aa43540a214ef34893a630
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349695"
---
# <a name="about-the-speech-translation-api"></a>Diktálásfelismerési API fordítására vonatkozó

A Microsoft-hang transzformációs API lehetővé teszi a beszéd-végpontok közötti, valós idejű, a többnyelvű fordítási hozzáadását az alkalmazások, eszközök és eszközök. Az azonos API beszéd beszéd és a beszéd-szöveg fordításhoz használható.

A Microsoft Translator Diktálásfelismerési API-val ügyfélalkalmazások adatfolyam beszéd hang a szolgáltatáshoz, és adatfolyamon vissza eredményt. Ezekkel az eredményekkel a felismert szöveg az adatforrás nyelvi és a fordítás a célként megadott nyelven szerepel. Ideiglenes fordítások lehet megadott befejezéséig egy utterance, hogy mely végleges fordítását valósul meg.

Másik lehetőségként szintetizált hang verzióját a végleges fordítását előkészítésére szolgál, igaz beszéd beszéd fordítási engedélyezése.

A beszédfelismerés fordítási API a websocket elemek protokollt használja az ügyfél és a kiszolgáló közötti kétirányú kommunikációs csatornát biztosítanak. De nem kell foglalkozni a websocket elemek; a beszédfelismerés SDK kezeli, amely meg.

A beszédfelismerés fordítási API a különböző Microsoft-termékek és szolgáltatások biztosítására a technológiát alkalmaz. Ez a szolgáltatás már használja vállalatok ezer világszerte, alkalmazások és munkafolyamatok.

## <a name="about-the-technology"></a>A technológia

Az alapul szolgáló Microsoft fordítási motor a rendszer két különböző szempontok: statisztikai gépi fordítás (SMT) és a Neurális gépi fordítás (NMT). Az utóbbi, a Neurális hálózatokat alkalmazó mesterséges eszközintelligencia megközelítés a korszerűbb gépi fordítás módja van. NMT jobb fordítások biztosítja – nem csupán több pontos, de is több gördülékeny és természetes. A kulcs a rugalmasság oka, hogy használja-e NMT mondat teljes környezetében lefordítani a szavakat.

Jelenleg Microsoft tért át NMT a legnépszerűbb nyelvek SMT alkalmazó csak a kevésbé használt nyelveket. Minden [beszéd beszéd fordítási a nyelveket](supported-languages.md#speech-translation) NMT szerint vannak kapcsolva. Beszéd-szöveg fordítási használhat SMT vagy NMT attól függően, hogy a két nyelv között. A megadott nyelv NMT támogatja, a teljes fordítási akkor NMT által biztosított. A megadott nyelv NMT által nem támogatott, ha a fordítás egy hibrid NMT és SMT, mint "pivot" angol között a két nyelv használatával.

Modellek közötti különbségek a fordítási motor belső. A végfelhasználók csak a továbbfejlesztett fordítási minőségét, különösen a kínai, japán és arab figyelje meg.

> [!NOTE]
> Felkeltettük az érdeklődését technológiáját használja a Microsoft fordítási motor többet? Lásd: [gépi fordítás](https://www.microsoft.com/en-us/translator/mt.aspx).

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
