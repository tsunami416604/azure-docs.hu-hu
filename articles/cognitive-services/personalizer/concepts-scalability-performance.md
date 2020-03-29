---
title: Méretezhetőség és teljesítmény – személyre szabó
titleSuffix: Azure Cognitive Services
description: 'A nagy teljesítményű és nagy forgalmú webhelyek és alkalmazások két fő tényezőt kell figyelembe venniük a Méretezhetőség és a teljesítmény érdekében: a késés és a betanítási teljesítmény.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490789"
---
# <a name="scalability-and-performance"></a>Méretezhetőség és teljesítmény

A nagy teljesítményű és nagy forgalmú webhelyek nek és alkalmazásoknak két fő tényezőt kell figyelembe venniük a Personalizer segítségével a méretezhetőség és a teljesítmény érdekében:

* Alacsony késleltetés a Rank API-hívások kezdeményezése során
* Annak biztosítása, hogy a betanítási átviteli adata lépést tart az eseménybevitellel

A személyre szabás gyorsan visszaadhat egy rangot, és a hívás időtartamának nagy része a REST API-n keresztüli kommunikációra van felszentelve. Az Azure automatikusan skálázódik a kérelmek gyors megválaszolásának képességét.

##  <a name="low-latency-scenarios"></a>Kis késleltetésű forgatókönyvek

Egyes alkalmazások alacsony késéseket igényelnek, amikor egy rangsort adnak vissza. Alacsony késések szükségesek:

* Annak érdekében, hogy a felhasználó ne várjon észrevehető időt a rangsorolt tartalom megjelenítése előtt.
* A szélsőséges forgalmat tapasztaló kiszolgáló segítése érdekében, hogy ne kösse le a szűkös számítási időt és a hálózati kapcsolatokat.


## <a name="scalability-and-training-throughput"></a>Méretezhetőség és betanítási átmenő képesség

A Personalizer úgy működik, hogy frissít egy modellt, amely átvan programezve a Personalizer által a rangsor és jutalom API-k után aszinkron módon küldött üzenetek alapján. Ezeket az üzeneteket egy Azure EventHub az alkalmazás használatával küldi el.

 Nem valószínű, hogy a legtöbb alkalmazás eléri a Personalizer maximális illesztési és képzési átaputlását. A maximális érték elérése nem lassítja le az alkalmazást, ez azt jelentené, hogy az Event Hub-várólisták belsőleg gyorsabban töltődnek be, mint ahogy azok megtisztíthatók.

## <a name="how-to-estimate-your-throughput-requirements"></a>Az átviteli követelmények becslése

* Becsülje meg a bájtok átlagos számát rangsorolási eseményenként, hozzáadva a környezet és a művelet JSON-dokumentumainak hosszát.
* 20 MB/s-os elosztani ezzel a becsült átlagos bájttal.

Ha például az átlagos hasznos adat 500 funkcióval rendelkezik, és mindegyik becsült 20 karakter, akkor minden esemény körülbelül 10 kb. Ezekkel a becslésekkel 20 000 000 / 10 000 = 2000 esemény/mp, ami körülbelül 173 millió esemény/nap. 

Ha eléri ezeket a korlátokat, kérjük, forduljon támogatási csapatunkhoz építészeti tanácsért.

## <a name="next-steps"></a>További lépések

[A Personalizer létrehozása és konfigurálása.](how-to-settings.md)