---
title: Letiltott Azure-előfizetés újraaktiválása
description: Leírja, miért lehet letiltva egy Azure-előfizetése, és hogyan aktiválhatja újra.
keywords: letiltott Azure-előfizetés
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: cad3082981bcfc699bc230badf44e2ffc2e1bed3
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744425"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Letiltott Azure-előfizetés újraaktiválása

Előfordulhat, hogy egy Azure-előfizetése le lesz tiltva, ha lejárt a hitelkártyája, elérte a költségkeretét, van egy lejárt esedékességű számlája, elérte a hitelkártyakeretét, vagy az előfizetést a fiókadminisztrátor törölte. Derítse ki, melyik eset vonatkozik Önre, és kövesse a cikk lépéseit, melyekkel újraaktiválhatja előfizetését.

## <a name="your-credit-is-expired"></a>A kredit lejárt

Amikor regisztrál egy ingyenes Azure-fiókot, kap egy ingyenes próba-előfizetést, amelyhez 200 dollár értékű, 30 napig felhasználható Azure-kredit jár, valamint 12 hónapos ingyenes hozzáférés a szolgáltatásokhoz. A 30 nap letelte után az Azure letiltja az előfizetést. Az előfizetés letiltására azért van szükség, hogy Ön ne halmozzon fel véletlenül használati díjakat az előfizetéshez járó kreditek és ingyenes szolgáltatások keretein túlmenően. Az Azure-szolgáltatások további használatához [frissítenie kell az előfizetést](upgrade-azure-subscription.md). A frissítést követően 12 hónapig továbbra is hozzáférhet az ingyenes szolgáltatásokhoz. Csak az ingyenes szolgáltatásokon és mennyiségeken felüli használatért kell fizetnie.

## <a name="you-reached-your-spending-limit"></a>Elérte a költségkeretét

A kreditekkel rendelkező Azure-előfizetések, például az ingyenes próba és a Visual Studio Enterprise költségkeretekkel rendelkeznek. Ez azt jelenti, hogy a szolgáltatásokat csak az előfizetésben foglalt kreditek erejéig használhatja. Ha a használat eléri a költségkeretet, az Azure letiltja az előfizetést az adott számlázási időszak hátralévő részére. Az előfizetés letiltására azért van szükség, hogy Ön ne halmozzon fel véletlenül használati díjakat az előfizetéshez járó kreditek keretén túlmenően. A költségkeret eltávolítása: [Költségkeret eltávolítása a Fiókközpontban](spending-limit.md#remove).

> [!NOTE]
> Ha ingyenes próba-előfizetéssel rendelkezik, és eltávolítja a költségkeretet, az előfizetés az ingyenes próbaidőszak végeztével átalakul egyéni, használatalapú fizetéses előfizetéssé. Az előfizetés létrehozása után a fennmaradó kreditek még 30 teljes napig megmaradnak. Az ingyenes szolgáltatásokhoz emellett további 12 hónapig lesz hozzáférése.

Az Azure számlázási tevékenységeinek monitorozásával és kezelésével kapcsolatban tekintse meg, [hogyan tervezheti meg az Azure-költségek kezelését](../understand/plan-manage-costs.md).


## <a name="your-bill-is-past-due"></a>Lejárt a számla esedékessége

A lejárt esedékességű tartozások kiegyenlítése: [Az Azure-előfizetés lejárt esedékességű tartozásának kezelése az Azure-tól kapott e-mail után](resolve-past-due-balance.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>A számla összege meghaladja a hitelkártyakeretét

A probléma megoldásához [váltson át egy másik hitelkártyára](change-credit-card.md). Vagy ha egy vállalkozást képvisel, [átválthat számlás fizetésre](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>Az előfizetés véletlenül törölve lett

Ha Ön a fiókadminisztrátor, és véletlenül lemond egy egyéni, használatalapú fizetéses előfizetés, a Fiókközpontban újraaktiválhatja azt.

1. Jelentkezzen be a [Fiókközpontba](https://account.windowsazure.com/Subscriptions).
1. Válassza ki a lemondott előfizetést.
1. Kattintson az **Újraaktiválás** lehetőségre.

    ![Képernyőkép a jobb oldali panelen látható újraaktiválási hivatkozásokról](./media/subscription-disabled/reactivate-sub.png)

Más típusú előfizetések újraaktiválásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="after-reactivation"></a>Az újraaktiválás után

Az előfizetés újraaktiválása után előfordulhat, hogy az erőforrások létrehozása vagy kezelése során késést tapasztal. Ha a késés meghaladja a 30 percet, forduljon az [Azure számlázási ügyfélszolgálati csapatához](https://go.microsoft.com/fwlink/?linkid=2083458). A legtöbb Azure-erőforrás működése automatikusan folytatódik, és ez nem igényel semmilyen beavatkozást. Emellett javasoljuk, hogy ellenőrizze az Azure-szolgáltatás erőforrásait, és indítsa újra azokat, amelyek működése nem folytatódik automatikusan.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, [hogyan tervezheti meg az Azure-költségek kezelését](../understand/plan-manage-costs.md).
