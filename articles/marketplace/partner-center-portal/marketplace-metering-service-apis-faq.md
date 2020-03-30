---
title: Marketplace-mérési szolgáltatás API-k - GYIK | Azure Piactér
description: SaaS-ajánlat használata az Azure Marketplace-en.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6e5b691a41ef283449f9eeeb90e9d01a91616146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275781"
---
# <a name="marketplace-metering-service-apis---faq"></a>Marketplace metering service API-k – GYIK

Miután egy Azure-felhasználó előfizet egy SaaS-szolgáltatásra, amely forgalmi díjas számlázást tartalmaz, nyomon fogja követni az ügyfél által használt minden egyes számlázási dimenzió kaszkadőrfelhasználását. Ha a felhasználás meghaladja az ügyfél által kiválasztott kifejezéshez beállított benneben foglalt mennyiségeket, a szolgáltatás használati eseményeket bocsát ki a Microsoft számára.

## <a name="emit-usage-events"></a>Használati események kibocsátása

>[!Note]
>Ez a szakasz csak saas-ajánlatok, ahol legalább az egyik a tervek mérési szolgáltatás méretei az ajánlat közzétételekor meghatározott.

![Használati események kibocsátása](media/isv-emits-usage-event.png)

A használati események kibocsátására vonatkozó API-szerződéssel kapcsolatos információkért tekintse meg a [SaaS-köteghasználati esemény API-t.](./marketplace-metering-service-apis.md#batch-usage-event)

### <a name="how-often-is-it-expected-to-emit-usage"></a>Milyen gyakran várható, hogy kibocsát használat?

Ideális esetben az elmúlt órában óránként kell kibocsátania a használatot, csak akkor, ha az előző órában van használat.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Mi a maximális késleltetés az esemény bekövetkezése és a használati esemény Microsoft számára történő kibocsátása között?

Ideális esetben a használati esemény minden órában kibocsátható az elmúlt órában történt események esetén. Azonban késések várhatók. A maximális késleltetés 24 óra, amely után a használati események nem fogadhatók el.

Ha például egy használati esemény egy nap 13:00 órakor következik be, akkor a következő napon 13 óráig van ideje az eseményhez kapcsolódó használati esemény kibocsátására. Ez azt jelenti, abban az esetben, ha a rendszer kibocsátó használat van egy leállási idő, akkor vissza, majd küldje el a használati esemény az óra intervallum, amelyben a használat történt, anélkül, hogy a hűség elvesztése.

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>Mi történik, ha egynél több használati eseményt küld ugyanabban az órában?

Csak egy használati esemény fogadható el az óraintervallumhoz. Az óraintervallum a 0.  Ha egynél több használati eseményt bocsát ki ugyanabban az óraintervallumban, a későbbi használati események ismétlődésként kerülnek ki.

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Mi történik, ha már leiratkozott SaaS-előfizetés ekképpen keresztül bocsát ki használatot?

A marketplace platformra kibocsátott használati események nem fogadhatók el a SaaS-előfizetés törlése után.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Letud-e jutni az összes SaaS-előfizetés listájáról, beleértve az aktív és a leiratkozott előfizetéseket is?

Igen, amikor meghívja az `GET /saas/subscriptions` API-t, tartalmazza az összes SaaS-előfizetés listáját. Az egyes SaaS-előfizetések válaszállapot-mezőjében látható állapotmező rögzíti, hogy az előfizetés aktív vagy leiratkozott-e. Az Előfizetések hívása akkor legfeljebb 100 előfizetést ad vissza.

## <a name="next-steps"></a>További lépések

- További információkért [tekintse meg a Marketplace-mérési szolgáltatás API-jait.](./marketplace-metering-service-apis.md)
