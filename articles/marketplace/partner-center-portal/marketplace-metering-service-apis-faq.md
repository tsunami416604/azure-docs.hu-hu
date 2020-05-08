---
title: Mérési szolgáltatás API-jai – gyakori kérdések – Microsoft kereskedelmi piactér
description: Gyakori kérdések a Microsoft AppSource és az Azure Marketplace-en található SaaS-ajánlatokkal kapcsolatos mérési szolgáltatás API-król.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb27089777baaaa7a29e020318fbc7635792af2d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857898"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace metering service API-k – GYIK

Ha egy Azure-felhasználó olyan SaaS-szolgáltatásra fizet, amely a mért számlázást is tartalmazza, akkor az ügyfél által használt egyes számlázási dimenziók fogyasztása nyomon követhető. Ha a felhasználás meghaladja az ügyfél által kiválasztott kifejezéshez beállított belefoglalt mennyiségeket, a szolgáltatás használati eseményeket bocsát ki a Microsoftnak.

## <a name="emit-usage-events"></a>Használati események kibocsátása

>[!Note]
>Ez a szakasz csak az SaaS-ajánlatok esetében alkalmazható, ahol legalább az egyik csomag esetében az ajánlat közzétételének időpontjában meghatározott mérési szolgáltatási méretek vannak megadva.

![Használati események kibocsátása](media/isv-emits-usage-event.png)

A használati események kibocsátására szolgáló API-szerződéssel kapcsolatos információkért tekintse meg a [SaaS batch használati esemény API](./marketplace-metering-service-apis.md#batch-usage-event) -ját.

### <a name="how-often-is-it-expected-to-emit-usage"></a>Milyen gyakran várható a használat kibocsátása?

Ideális esetben a használatot minden órában az elmúlt órában kell kibocsátani, csak akkor, ha az előző órában használatban van.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Mi a maximális késleltetés az esemény bekövetkezése időpontja és a Microsoft számára a használati idő kibocsátása között?

Ideális esetben a használati eseményt óránként bocsátjuk ki az elmúlt órában bekövetkezett események esetében. Azonban késések várhatók. A maximálisan engedélyezett késleltetés 24 óra, amely után a használati események nem lesznek elfogadva.

Ha például egy nap 1 ÓRAKOR egy használati esemény történik, akkor a következő napon 1 óráig kell kibocsátania az eseményhez kapcsolódó használati eseményt. Ha a rendszer leállítja a használatot, a szolgáltatás helyreállítja, majd elküldi a használati eseményt a használat során eltelt óra intervallumban, a hűség elvesztése nélkül.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Mi történik, ha egy adott órában több használati eseményt küld?

Az óra intervalluma csak egy használati eseményt fogad el. Az óra intervalluma a 0. percben kezdődik, és 59-kor végződik.  Ha a rendszer egynél több használati eseményt bocsát ki ugyanarra az órás időintervallumra, a rendszer a további használati eseményeket duplikálja.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Mi történik, ha már leiratkozott SaaS-előfizetésre vonatkozó használatot bocsát ki?

A Piactéri platformra kibocsátott használati események nem lesznek elfogadva az SaaS-előfizetés törlése után.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Lekérheti az összes SaaS-előfizetés listáját, beleértve az aktív és a leiratkozott előfizetéseket is?

Igen, ha meghívja az `GET /saas/subscriptions` API-t, az tartalmazza az összes SaaS-előfizetés listáját. Az egyes SaaS-előfizetések válaszában az állapot mező rögzíti, hogy az előfizetés aktív vagy leiratkozott állapotban van-e. Az előfizetések listázására irányuló hívás legfeljebb 100 előfizetést ad vissza.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Mi történik, ha a piactér-mérési szolgáltatás leállás miatt leáll?

Ha az ISV egy egyéni mérőszámot küld, és hibaüzenetet kap, akkor az ISV-nek várnia kell, majd újra kell próbálkoznia.

Ha a hiba továbbra is fennáll, küldje el újra az egyéni mérőszámot a következő órában (a mennyiség felhalmozódása). Folytassa ezt a folyamatot, amíg nem érkezik a hiba a válaszba.

## <a name="next-steps"></a>További lépések

- További információ: Marketplace- [mérési szolgáltatás API](./marketplace-metering-service-apis.md)-k.
