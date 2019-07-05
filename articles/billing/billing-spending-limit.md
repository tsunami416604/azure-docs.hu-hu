---
title: Azure-költségkeretét |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan működik az egy Azure költségkeret és annak eltávolításáról.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: fb43f29827309fc8986ee6b4653f5edf303cc21d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490612"
---
# <a name="azure-spending-limit"></a>Azure-költségkeretét

Az Azure-ban a költségkeret megakadályozza, hogy a kreditösszeg erejéig kiadásait. Az Azure próbaverziójára, vagy a több hónapon keresztül krediteket tartalmazó ajánlatok regisztráló új ügyfelek a költségkeretet, alapértelmezés szerint engedélyezve van. A költségkeret 0 USD. Nem lehet módosítani. A költségkeret nem érhető el, az előfizetések néhány ilyen kötelezettségvállalásos csomagok és a használatalapú díjszabás tervek csomagok. Tekintse meg a [teljes listája megtalálható az Azure és a költségkeret elérhetősége](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>A költségkeret elérése

Ha felhasználta a rendelkezésére szereplő havi mennyiséget díjakat a használati eredményezi az Azure-előfizetés részét képező, a telepített szolgáltatások le vannak tiltva, az elszámolási időszakból hátralévő.

Például ha töltött az előfizetéséhez járó összes kreditet, Azure-erőforrások üzembe helyezett el lesznek távolítva éles és az Azure-beli virtuális gépek le lesznek állítva és fel lesznek szabadítva. Az adatok a storage-fiókok csak olvashatóként érhetők el.

A következő elszámolási időszak elején, ha az előfizetési ajánlatok között a több hónapon átívelő krediteket tartalmaz az előfizetés újbóli automatikusan engedélyezve lesz. Ezután ismételt üzembe helyezése az Azure-erőforrások és a tárfiókok és adatbázisok teljes hozzáféréssel rendelkezik.

Az Azure az értesítéseket, amikor elérte a költségkeret-beállítási korlátozza az előfizetéshez tartozó e-mailt küld. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/Subscriptions) értesítéseknek előfizetésekről kiküldött túllépte a költségkeretét.

Ha ingyenes próba-előfizetéssel rendelkezik, és a költségkeret elérése, frissítheti a csomag [használatalapú](billing-upgrade-azure-subscription.md) eltávolítása a költségkeret-beállítási díjszabás korlátozza, és automatikusan engedélyezi az előfizetés.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>A költségkeret eltávolítása az Account Center webhelyen

A költségkeretet bármikor eltávolíthatja mindaddig, amíg egy érvényes fizetési módszer az Azure-előfizetése társítva van. A több hónapon átívelő a krediteket tartalmazó ajánlatok is engedélyezheti a költségkeret a következő elszámolási időszak kezdetétől érvényesül.

Az alábbi lépéseket követve távolíthatja el a költségkeretet:

1. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/Subscriptions).
1. Válasszon egy előfizetést. Ha az előfizetés le van tiltva, hogy a rendszer elérte a költségkeret miatt, kattintson az értesítés: **Előfizetés elérte a költségkeret, és a díjak elkerülése végett le van tiltva.** Ellenkező esetben kattintson a **távolítsa el a költségkeretet** a a **ELŐFIZETÉS állapota** területen.
1. Válasszon egy Önnek megfelelő lehetőséget.

![A költségkeret eltávolítása a megfelelő beállítás kiválasztása](./media/billing-spending-limit/remove-spending-limit.PNG)

| Beállítás | Következmény |
| --- | --- |
| A költségkeret eltávolítása meghatározatlan időre | A költségkeret eltávolítása oly módon, hogy az nem kapcsolódik vissza automatikusan a következő elszámolási időszak kezdetén. |
| A költségkeret eltávolítása az adott elszámolási időszakban | A költségkeret eltávolítása oly módon, hogy a következő elszámolási időszak kezdetén automatikusan visszakapcsolódik. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Miért érdemes a költségkeret eltávolítása

A költségkeret megakadályozhatja, hogy telepítését vagy bizonyos külső és a Microsoft-szolgáltatások használatával. Az alábbiakban a helyzetekben, ahol távolítsa el a költségkeretet az előfizetésén.

-  Belső rendszerképeket például az Oracle és olyan szolgáltatásokat, például az Azure DevOps-szolgáltatások telepítését tervezi. Ez a helyzet okoz szinte azonnal túllépi a költségkeretet, és hatására az előfizetés le kell tiltani.
- A szakadhat meg, hogy nem szeretné, hogy szolgáltatásokat.
- Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyeknek beállításait – például a virtuális IP-címeket – nem szeretné elveszteni. Ezek a beállítások elvesznek, ha a szolgáltatások és erőforrások szüntethető.

## <a name="turn-on-the-spending-limit-after-removing"></a>Kapcsolja be a költségkeret eltávolítása után

Ez a funkció csak a költségkeret határozatlan időre el lett távolítva esetén érhető el. Módosítsa a következő elszámolási időszak elején automatikusan bekapcsolása.

1. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/Subscriptions).
1. Kattintson a sárga szalagcímre módosítása a költési korlát lehetőséget.
1. Válasszon **kapcsolja be a költségkeret a következő elszámolási időszakban \<számlázási időszak kezdete\>**

## <a name="custom-spending-limit"></a>Egyéni költségkeret

Egyéni költségkeretek nem érhetők el.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>A költségkeret nem minden díjak elkerülése

[Az Azure Marketplace-en közzétett néhány külső szolgáltatások](billing-understand-your-azure-marketplace-charges.md) előfizetési kreditek nem használhatók, és képes külön díjat számolunk akkor is, ha a költségkeret van beállítva. Ilyenek például a Visual Studio-licencek, a Azure Active Directory premium, a támogatási csomagok és a legtöbb harmadik féltől származó szolgáltatások. Amikor üzembe helyezi az új külső szolgáltatás, figyelmeztetés, hogy tudja, a szolgáltatások számlázása külön-külön jelenik meg:

![Marketplace-en beszerzési figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Frissítés a rendelkező csomagot [használatalapú](billing-upgrade-azure-subscription.md) díjszabása.
