---
title: Azure-költségkeret | Microsoft Docs
description: Ez a cikk az Azure-költségkeret működését és eltávolításának módját ismerteti.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114452"
---
# <a name="azure-spending-limit"></a>Azure-költségkeret

Az Azure-költségkerettel elkerülheti a kreditek mennyiségét meghaladó kiadásokat. A költségkeret alapértelmezés szerint be van kapcsolva minden új felhasználó esetében, aki Azure-próbaverzióra regisztrál, illetve olyan ajánlatok esetében, amelyek több hónapnyi kreditet tartalmaznak. A költségkeret 0 dollár, és nem módosítható. Nem módosíthatja tehát a költségkeretet például 100 dollárra. Lehetősége van azonban a költségkeret eltávolítására. Tehát vagy nincs költségkerete, vagy nulla értékű költségkerete van, amellyel elkerülheti a legtöbb kiadást. A költségkeret nem érhető el bizonyos csomagok részét képező előfizetésekhez, például a hűségszerződéses csomagokhoz és a használatalapú fizetéses csomagokhoz. Tekintse meg [az Azure-ajánlatok teljes listáját és a költségkeret elérhetőségét](https://azure.microsoft.com/support/legal/offer-details/) ismertető cikket.

## <a name="reaching-a-spending-limit"></a>A költségkeret elérése

Ha a használat olyan költségeket eredményez, amelyek kimerítik az Azure-előfizetéssel járó havi összeget, akkor a rendszer letiltja az üzembe helyezett szolgáltatásokat a számlázási időszak fennmaradó idejére.

Ha például elkölti az előfizetésével járó összes kreditet, akkor a rendszer eltávolítja az üzembe helyezett Azure-erőforrásokat az éles környezetből, valamint leállítja és felszabadítja az Azure-beli virtuális gépeket. A tárfiókokban lévő adatok írásvédett formában érhetők el.

Ha a következő számlázási időszak elején az előfizetési ajánlat több hónapra vonatkozó kredittel rendelkezik, akkor a rendszer automatikusan újraengedélyezi az előfizetést. Ezután újból üzembe helyezheti az Azure-erőforrásokat, és teljes hozzáféréssel fog rendelkezni a tárfiókokhoz és az adatbázisokhoz.

Az előfizetéshez tartozó költségkeret elérésekor az Azure e-mailes értesítést küld. A költségkeretet elérő előfizetésekkel kapcsolatos értesítések megtekintéséhez jelentkezzen be a [Fiókközpontba](https://account.windowsazure.com/Subscriptions).

Ha ingyenes próba-előfizetéssel rendelkezik, és eléri a költségkeretet, akkor a költségkeret eltávolításához és az előfizetés automatikus engedélyezéséhez [használatalapú fizetéses](billing-upgrade-azure-subscription.md) díjszabású csomagra frissíthet.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>A költségkeret eltávolítása a Fiókközpontban

A költségkeretet bármikor eltávolíthatja mindaddig, amíg az Azure-előfizetéséhez érvényes fizetési mód van társítva. A több hónapnyi kreditet tartalmazó ajánlatok esetében a következő számlázási időszak elején is engedélyezheti a költségkeretet.

A költségkeret eltávolításához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Fiókközpontba](https://account.windowsazure.com/Subscriptions).
1. Válasszon egy előfizetést. Ha az előfizetés a költségkeret elérése miatt le van tiltva, kattintson erre az értesítésre: **Az előfizetés elérte a költségkeretet, és a további díjak elkerülése érdekében le lett tiltva.** Egyéb esetben kattintson a **Költségkeret eltávolítása** elemre az **ELŐFIZETÉS ÁLLAPOTA** területen.
1. Válassza ki az Önnek megfelelő lehetőséget.

![A költségkeret eltávolítására szolgáló lehetőség kiválasztása](./media/billing-spending-limit/remove-spending-limit.PNG)

| Beállítás | Hatás |
| --- | --- |
| A költségkeret eltávolítása határozatlan időre | Úgy távolítja el a költségkeretet, hogy az a következő számlázási időszak kezdetekor nem kapcsol be automatikusan. |
| A költségkeret eltávolítása az aktuális számlázási időszakra | Úgy távolítja el a költségkeretet, hogy az a következő számlázási időszak kezdetekor automatikusan bekapcsol. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>A költségkeret eltávolításának lehetséges okai

A költségkeret megakadályozhatja bizonyos külső vagy Microsoft-szolgáltatások üzembe helyezését vagy használatát. A következő helyzetekben érdemes eltávolítania az előfizetéséhez tartozó költségkeretet.

-  Belső rendszerképeket (például Oracle) vagy szolgáltatásokat (például Azure DevOps Services) szeretne üzembe helyezni. Ez a helyzet a költségkeret szinte azonnali átlépéséhez és az előfizetés letiltásához vezet.
- Olyan szolgáltatásokkal rendelkezik, amelyek megszakadását szeretné elkerülni.
- Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyek beállításait (például virtuális IP-címek) nem szeretné elveszíteni. A szolgáltatások és erőforrások felszabadításakor ezek a beállítások elvesznek.

## <a name="turn-on-the-spending-limit-after-removing"></a>A költségkeret bekapcsolása eltávolítás után

Ez a funkció csak a költségkeret határozatlan időre történő eltávolítása esetén érhető el. Módosítsa úgy, hogy automatikusan bekapcsoljon a következő számlázási időszak kezdetekor.

1. Jelentkezzen be a [Fiókközpontba](https://account.windowsazure.com/Subscriptions).
1. A költségkeret beállításának módosításához kattintson a sárga szalagra.
1. Válassza **A költségkeret bekapcsolása a következő számlázási időszakban \<számlázási időszak kezdési dátuma\>** lehetőséget.

## <a name="custom-spending-limit"></a>Egyéni költségkeret

Egyéni költségkeretek nem érhetők el.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>A költségkerettel nem kerülhető el minden költség

[Az Azure Marketplace-en közzétett egyes külső szolgáltatások](billing-understand-your-azure-marketplace-charges.md) esetén nem használhatja az előfizetésével járó krediteket, és külön költségek jelentkeznek még akkor is, ha költségkeretet állított be. Ilyenek például a Visual Studio-licencek, a prémium szintű Azure Active Directory, a támogatási csomagok és a legtöbb külső szolgáltatás. Új külső szolgáltatás üzembe helyezésekor megjelenik egy figyelmeztetés, amely arról tájékoztatja, hogy a szolgáltatások számlázása külön történik:

![Marketplace vásárlási figyelmeztetése](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Frissítsen egy [használatalapú fizetéses](billing-upgrade-azure-subscription.md) díjszabású csomagra.
