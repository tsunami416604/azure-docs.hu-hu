---
title: Megerősítő tanulást - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Nagy teljesítményű és nagy adatforgalmú webhelyekhez és alkalmazásokhoz rendelkezik Personalizer a méretezhetőség és teljesítmény érdekében érdemes figyelembe venni két tényezőt: képzési teljesítmény és a késés.'
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 242cc1b96c08cd79dc3e2ef5efbbe96a934b8ad3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058440"
---
# <a name="scalability-and-performance"></a>Méretezhetőség és teljesítmény

Nagy teljesítményű és nagy adatforgalmú webhelyekhez és alkalmazásokhoz van Personalizer a méretezhetőség és teljesítmény érdekében érdemes figyelembe venni két tényezőt:

* Közel valós idejű tartása amikor rang API-hívások
* Gondoskodik róla, hogy esemény bemeneti össztárhelyet képzési átviteli sebesség

Személyre szabás nagyon gyorsan bővül, a legtöbb, a hívás időtartama a REST API-n keresztüli kommunikáció számára kijelölt adhat vissza egy rang. Az Azure lesz az automatikus méretezés képes gyorsan reagáljanak a kérésekre.

##  <a name="low-latency-scenarios"></a>Közel valós idejű forgatókönyveket

Egyes alkalmazások alacsony késés érdekében szükséges, egy rang visszaadásakor. Erre azért szükség:

* Ahhoz, hogy biztosítsa a felhasználói várakozási idő megjelenítése előtt lemeztevékenységet rangsorolva, amelyekről tartalmat.
* Egy kiszolgáló, amelyen jelentkezik a szélsőséges forgalom segítségével elkerülése érdekében lekötné szűkös számítási idő és a hálózati kapcsolatokat.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Méretezhetőség és képzési átviteli sebesség

Egy olyan modell, a rendszer retrained frissítésével personalizer works által küldött üzenetek aszinkron módon Personalizer után Rank és ellenszolgáltatás API-k alapján. Ezek az üzenetek küldése az Azure-Eseményközpont az alkalmazáshoz.

 Nem valószínű, a legtöbb alkalmazás eléri a maximális csatlakoztatása és képzési átviteli sebességének Personalizer. Elérnék a maximális nem le fogják lassítani az alkalmazást, miközben lenne hasonló Event Hub-várólisták első kitöltött belsőleg gyorsabb ütemben is szabadíthatók fel.

## <a name="how-to-estimate-your-throughput-requirements"></a>Ha meg szeretné becsülni az átviteli sebességet megkövetelő hogyan

* Becsülje meg a felvétele a környezeti és a művelet JSON-dokumentumok hosszának rangsorolási eseményenként bájtok átlagos számát.
* 20MB/s nullával való osztás a becsült bájtok átlagos száma.

Például, ha az átlagos terhelés 500 funkciókkal rendelkezik, és minden egyes egy becsült 20 karakter, majd minden esemény körülbelül 10kb. Ezek a becslések 20,000,000- / 10 000 = 2000 esemény/másodperc, azaz készül 173 millió esemény naponta. 

Ha az elérni próbált ezeket a korlátokat, forduljon az ügyfélszolgálati csapatunkhoz architektúra tanácsokat.

## <a name="next-steps"></a>További lépések

[Létrehozhat és konfigurálhat Personalizer](how-to-settings.md).