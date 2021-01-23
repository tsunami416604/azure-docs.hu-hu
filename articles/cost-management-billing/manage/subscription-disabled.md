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
ms.date: 01/19/2021
ms.author: banders
ms.openlocfilehash: d7b5f1ae6db633bd1af10b1a0de1392c2a1fbcef
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685534"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Letiltott Azure-előfizetés újraaktiválása

Előfordulhat, hogy egy Azure-előfizetése le lesz tiltva, ha lejárt a hitelkártyája, elérte a költségkeretét, van egy lejárt esedékességű számlája, elérte a hitelkártyakeretét, vagy az előfizetést a fiókadminisztrátor törölte. Derítse ki, melyik eset vonatkozik Önre, és kövesse a cikk lépéseit, melyekkel újraaktiválhatja előfizetését.

## <a name="your-credit-is-expired"></a>A kredit lejárt

Amikor regisztrál egy ingyenes Azure-fiókot, kap egy ingyenes próba-előfizetést, amelyhez 200 dollár értékű, 30 napig felhasználható Azure-kredit jár, valamint 12 hónapos ingyenes hozzáférés a szolgáltatásokhoz. A 30 nap letelte után az Azure letiltja az előfizetést. Az előfizetés letiltására azért van szükség, hogy Ön ne halmozzon fel véletlenül használati díjakat az előfizetéshez járó kreditek és ingyenes szolgáltatások keretein túlmenően. Az Azure-szolgáltatások további használatához [frissítenie kell az előfizetést](upgrade-azure-subscription.md). A frissítést követően 12 hónapig továbbra is hozzáférhet az ingyenes szolgáltatásokhoz. Csak az ingyenes szolgáltatás mennyiségére vonatkozó korlátozások után számítunk fel díjat.

## <a name="you-reached-your-spending-limit"></a>Elérte a költségkeretét

A kreditekkel rendelkező Azure-előfizetések, például az ingyenes próba és a Visual Studio Enterprise költségkeretekkel rendelkeznek. A szolgáltatásokat csak a benne foglalt kreditek használatára használhatja. Ha a használat eléri a költségkeretet, az Azure letiltja az előfizetést a számlázási időszak további részében. Az előfizetés letiltására azért van szükség, hogy Ön ne halmozzon fel véletlenül használati díjakat az előfizetéshez járó kreditek keretén túlmenően. A költségkeret eltávolításához lásd: [a költségkeret eltávolítása a Azure Portal](spending-limit.md#remove).

> [!NOTE]
> Ha ingyenes próba-előfizetéssel rendelkezik, és eltávolítja a költségkeretet, az előfizetés az ingyenes próbaidőszak végeztével átalakul egyéni, használatalapú fizetéses előfizetéssé. Az előfizetés létrehozása után a fennmaradó kreditek még 30 teljes napig megmaradnak. Az ingyenes szolgáltatásokhoz emellett további 12 hónapig lesz hozzáférése.

Az Azure számlázási tevékenységeinek monitorozásával és kezelésével kapcsolatban tekintse meg, [hogyan tervezheti meg az Azure-költségek kezelését](../understand/plan-manage-costs.md).

## <a name="your-bill-is-past-due"></a>Lejárt a számla esedékessége

Késedelmes egyenleg feloldásához tekintse meg a következő cikkek egyikét:

- Az utólagos elszámolású Microsoft online előfizetési programokra vonatkozó előfizetések esetében lásd: az Azure [-előfizetés lejárt esedékes egyenlegének feloldása az Azure-beli e-mailek lekérése után](resolve-past-due-balance.md).
- A Microsoft ügyfél-szerződés előfizetéseit a következő témakörben tekintheti [meg: Microsoft Azure számlájának kifizetése](../understand/pay-bill.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>A számla összege meghaladja a hitelkártyakeretét

A probléma megoldásához [váltson át egy másik hitelkártyára](change-credit-card.md). Vagy ha egy vállalkozást képvisel, [átválthat számlás fizetésre](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>Az előfizetés véletlenül törölve lett

Ha Ön a fiók rendszergazdája, és véletlenül megszakította az utólagos elszámolású előfizetést, akkor újra aktiválhatja azt a Azure Portalban.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen az előfizetések elemre, majd válassza ki a megszakított előfizetést.
1. Válassza az **újraaktiválás** lehetőséget.
1. **Az OK gombra** kattintva erősítse meg az újraaktiválást.  
    :::image type="content" source="./media/subscription-disabled/reactivate-sub.png" alt-text="Az újraaktiválás megerősítését bemutató képernyőkép" :::

Más típusú előfizetések újraaktiválásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="after-reactivation"></a>Az újraaktiválás után

Az előfizetés újraaktiválása után előfordulhat, hogy az erőforrások létrehozása vagy kezelése során késést tapasztal. Ha a késleltetés meghaladja a 30 percet, forduljon az [Azure számlázási támogatási szolgálatához](https://go.microsoft.com/fwlink/?linkid=2083458) segítségért. A legtöbb Azure-erőforrás működése automatikusan folytatódik, és ez nem igényel semmilyen beavatkozást. Emellett javasoljuk, hogy ellenőrizze az Azure-szolgáltatás erőforrásait, és indítsa újra azokat, amelyek működése nem folytatódik automatikusan.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, [hogyan tervezheti meg az Azure-költségek kezelését](../understand/plan-manage-costs.md).
