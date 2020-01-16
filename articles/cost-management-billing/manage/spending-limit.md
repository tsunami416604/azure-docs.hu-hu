---
title: Azure-költségkeret | Microsoft Docs
description: Ez a cikk az Azure-költségkeret működését és eltávolításának módját ismerteti.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: banders
ms.openlocfilehash: ddb906aeb8d1c2b0d1f0c97fc12bd389da431646
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989942"
---
# <a name="azure-spending-limit"></a>Azure-költségkeret

Az Azure-költségkerettel elkerülheti a kreditek mennyiségét meghaladó kiadásokat. A költségkeret alapértelmezés szerint be van kapcsolva minden új ügyfél esetében, aki ingyenes Azure-fiókra regisztrál, illetve olyan előfizetési típusok esetében, amelyek több hónapnyi kreditet tartalmaznak. A költségkeret megegyezik a kreditmennyiséggel, és nem módosítható. Ha például regisztrált az ingyenes Azure-fiókra, a költségkerete 200 dollár, és nem módosíthatja 500 dollárra. Lehetősége van azonban a költségkeret eltávolítására. Tehát vagy nincs költségkerete, vagy a kreditmennyiségnek megfelelő költségkerete van. Ez megakadályozza a legtöbb kiadást. A költségkeret nem érhető el hűségszerződéses csomagokat és használatalapú fizetéses csomagokat tartalmazó előfizetésekhez. Tekintse meg [az Azure-előfizetések típusait és a költségkeret elérhetőségét](https://azure.microsoft.com/support/legal/offer-details/) ismertető cikket.

## <a name="reaching-a-spending-limit"></a>A költségkeret elérése

Ha a használat olyan költségeket eredményez, amelyek kimerítik a költségkeretet, akkor a rendszer letiltja az üzembe helyezett szolgáltatásokat a számlázási időszak fennmaradó idejére.

Ha például elkölti az ingyenes Azure-fiókhoz járó összes kreditet, akkor a rendszer eltávolítja az üzembe helyezett Azure-erőforrásokat az éles környezetből, valamint leállítja és felszabadítja az Azure-beli virtuális gépeket. A tárfiókokban lévő adatok írásvédett formában érhetők el.

Ha az előfizetési típus több hónapra vonatkozó kredittel rendelkezik, akkor a rendszer automatikusan újraengedélyezi az előfizetést a következő számlázási időszak elején. Ezután újból üzembe helyezheti az Azure-erőforrásokat, és teljes hozzáféréssel fog rendelkezni a tárfiókokhoz és az adatbázisokhoz.

A költségkeret elérésekor az Azure e-mailes értesítést küld. A költségkeretet elérő előfizetésekkel kapcsolatos értesítések megtekintéséhez jelentkezzen be az [Azure Portalra](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Ha ingyenes Azure-fiókra regisztrált, és eléri a költségkeretet, akkor a költségkeret eltávolításához és az előfizetés automatikus újbóli engedélyezéséhez [használatalapú fizetéses](upgrade-azure-subscription.md) díjszabásra válthat.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>A költségkeret eltávolítása az Azure Portalon

A költségkeretet bármikor eltávolíthatja mindaddig, amíg az Azure-előfizetéséhez érvényes fizetési mód van társítva. A több hónapnyi kreditet tartalmazó előfizetési típusok, például a Visual Studio Enterprise és Visual Studio Professional esetében a következő számlázási időszak elején is engedélyezheti a költségkeretet.

Az alábbi lépéseket követve távolíthatja el a költségkeretet:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válasszon előfizetést. Ha elérte a költségkeretet, az előfizetése le lesz tiltva.
1. A lap tetején válassza a **Költségkeret eltávolítása** elemet.
1. Válasszon egy Önnek megfelelő lehetőséget.

![A költségkeret eltávolítására szolgáló lehetőség kiválasztása](./media/spending-limit/remove-spending-limit01.png)

| Lehetőség | Következmény |
| --- | --- |
| A költségkeret eltávolítása meghatározatlan időre | A költségkeret eltávolítása oly módon, hogy az nem kapcsolódik vissza automatikusan a következő elszámolási időszak kezdetén. |
| A költségkeret eltávolítása az adott elszámolási időszakban | A költségkeret eltávolítása oly módon, hogy a következő elszámolási időszak kezdetén automatikusan visszakapcsolódik. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>A költségkeret eltávolításának lehetséges okai

A költségkeret megakadályozhatja bizonyos külső vagy Microsoft-szolgáltatások üzembe helyezését vagy használatát. A következő helyzetekben érdemes eltávolítania az előfizetéséhez tartozó költségkeretet.

-  Belső rendszerképeket (például Oracle) vagy szolgáltatásokat (például Azure DevOps Services) szeretne üzembe helyezni. Ez a helyzet a költségkeret szinte azonnali eléréséhez és az előfizetés letiltásához vezet.
- Olyan szolgáltatásokkal rendelkezik, amelyek megszakadását szeretné elkerülni. Amikor eléri a költségkeretet, a rendszer eltávolítja az üzembe helyezett Azure-erőforrásokat az éles környezetből, valamint leállítja és felszabadítja az Azure-beli virtuális gépeket. Ha olyan szolgáltatásokkal rendelkezik, amelyek megszakadását szeretné elkerülni, el kell távolítania a költségkeretet.
- Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyeknek beállításait – például a virtuális IP-címeket – nem szeretné elveszteni. A költségkeret elérésekor, valamint a szolgáltatások és erőforrások felszabadításakor ezek a beállítások elvesznek.

## <a name="turn-on-the-spending-limit-after-removing"></a>A költségkeret bekapcsolása eltávolítás után

Ez a funkció csak akkor érhető el, ha a költségkeretet határozatlan időre eltávolították az olyan előfizetési típusok esetében, amelyek több hónapnyi kreditet tartalmaznak. Ezzel a funkcióval automatikusan bekapcsolhatja a költségkeretet a következő számlázási időszak kezdetekor.

1. Jelentkezzen be a [Fiókközpontba](https://account.windowsazure.com/Subscriptions).
1. A költségkeret beállításának módosításához kattintson a sárga szalagra.
1. Válassza **A költségkeret bekapcsolása a következő számlázási időszakban \<számlázási időszak kezdési dátuma\>** lehetőséget.

## <a name="custom-spending-limit"></a>Egyéni költségkeret

Egyéni költségkeretek nem érhetők el.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>A költségkerettel nem kerülhető el minden költség

[Az Azure Marketplace-en közzétett egyes külső szolgáltatások](../understand/understand-azure-marketplace-charges.md) esetén nem használhatja az előfizetésével járó krediteket, és külön költségek jelentkeznek még akkor is, ha költségkeretet állított be. Ilyenek például a Visual Studio-licencek, a prémium szintű Azure Active Directory, a támogatási csomagok és a legtöbb külső szolgáltatás. Új külső szolgáltatás üzembe helyezésekor megjelenik egy figyelmeztetés, amely arról tájékoztatja, hogy a szolgáltatások számlázása külön történik:

![Marketplace vásárlási figyelmeztetése](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- Frissítsen egy [használatalapú fizetéses](upgrade-azure-subscription.md) díjszabású csomagra.
