---
title: Megismerheti az Azure-költségkeretét |} A Microsoft Docs
description: Ismerteti, hogyan működik az Azure-költségkeretét, és annak eltávolításáról
services: ''
documentationcenter: ''
author: genlin
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.openlocfilehash: a564e72cc695830edfa5092eddc1f4f9b80ba519
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583533"
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Tudnivalók az Azure-költségkeretről és annak eltávolításáról

A kreditösszeg erejéig kiadásait elkerülése érdekében létezik költségkeret az Azure-ban. A próbaverzióra vagy a több hónapon átívelő krediteket tartalmazó ajánlatok regisztráló új ügyfelek a költségkeretet, alapértelmezés szerint engedélyezve van. A költségkeret 0 USD. Nem lehet módosítani. A költségkeret nem érhető el az olyan előfizetéstípusokhoz, mint a használatalapú fizetés és a kötelezettségvállalásos csomagok. Tekintse meg a [teljes listája megtalálható az Azure és a költségkeret elérhetősége](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Mi történik a költségkeret elérése esetén?

Ha felhasználta a rendelkezésére szereplő havi mennyiséget díjakat a használati eredményezi az előfizetéséhez járó, a telepített szolgáltatások le vannak tiltva, az elszámolási időszakból hátralévő. 

Például ha töltött az előfizetéséhez járó összes kreditet, Cloud Services telepített szolgáltatásai el lesznek távolítva éles és az Azure-beli virtuális gépek le lesznek állítva és fel lesznek szabadítva. A saját tárfiókokban és adatbázisokban lévő adatokat egy csak olvasható módon érhetők el.

A következő elszámolási időszak kezdetétől érvényesül, ha az előfizetési ajánlatok között a több hónapon átívelő krediteket tartalmaz az előfizetés szeretné újra engedélyezni automatikusan. Ezután ismételt üzembe helyezése a Cloud Services és a tárfiókok és adatbázisok teljes hozzáféréssel rendelkezik.

Ha eléri a költségkeret az előfizetéshez tartozó e-mail-értesítéseket küldünk. Jelentkezzen be a [Account Center](https://account.windowsazure.com/Subscriptions), és láthatja, hogy túllépte a költségkeretét szóló értesítéseket.

Ha rendelkezik egy ingyenes próbaverzióra, és a költségkeret elérése, [használatalapú fizetésre frissítési](billing-upgrade-azure-subscription.md) el kell távolítania a költségkeretet, és automatikusan újra engedélyezi az előfizetés.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>A költségkeret eltávolítása az Account Center webhelyen

A költségkeretet bármikor eltávolíthatja mindaddig, amíg van érvényes fizetési mód rendelve az előfizetéséhez. A több hónapon átívelő a krediteket tartalmazó ajánlatok emellett engedélyezheti újra a költségkeret a következő elszámolási időszak kezdetétől érvényesül.

Az alábbi lépéseket követve távolíthatja el a költségkeretet:

1. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/Subscriptions).
1. Válasszon egy előfizetést. Ha az előfizetés le van tiltva, hogy a rendszer elérte a költségkeret miatt, kattintson a következő értesítésre: "Előfizetés elérte a Költségkeretét, és díjak elkerülése végett le van tiltva." Ellenkező esetben kattintson a **távolítsa el a költségkeretet** a a **ELŐFIZETÉS állapota** területen.
1. Válasszon egy Önnek megfelelő lehetőséget.

![A költségkeret eltávolítása a megfelelő beállítás kiválasztása](./media/billing-spending-limit/remove-spending-limit.PNG)

|Beállítás|Következmény|
|-------|-----|
|A költségkeret eltávolítása meghatározatlan időre|A költségkeret eltávolítása oly módon, hogy az nem kapcsolódik vissza automatikusan a következő elszámolási időszak kezdetén.|
|A költségkeret eltávolítása az adott elszámolási időszakban|A költségkeret eltávolítása oly módon, hogy a következő elszámolási időszak kezdetén automatikusan visszakapcsolódik.|

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Miért érdemes a költségkeretet eltávolítani?

A költségkeret megakadályozhatja, hogy telepítését vagy bizonyos külső és a Microsoft-szolgáltatások használatával. A következő szituációkban javasolt a költségkeret eltávolítása az előfizetésből.

* Belső rendszerképeket például az Oracle és olyan szolgáltatásokat, például az Azure DevOps-szolgáltatások telepítését tervezi. Ez a forgatókönyv hatására szinte azonnal túllépi a költségkeretet, és hatására az előfizetés le kell tiltani.
* Olyan szolgáltatásai vannak, amelyek működése nem szakadhat meg.
* Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyeknek beállításait – például a virtuális IP-címeket – nem szeretné elveszteni. Ezek a beállítások elvesznek, ha a szolgáltatások és erőforrások szüntethető.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Hogyan bekapcsolja a költségkeret eltávolítása után?

Ez a funkció csak a költségkeret határozatlan időre el lett távolítva esetén érhető el. Módosítsa a következő elszámolási időszak elején automatikusan bekapcsolása.

1. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/Subscriptions).
1. Kattintson a sárga szalagcímre módosítása a költési korlát lehetőséget.
1. Válasszon **kapcsolja be a költségkeret a következő elszámolási időszakban \<számlázási időszak kezdete\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Hogyan állíthatok be egyéni költségkeret?

Egyéni költségkeretek nem érhetők el.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>Akadályozza meg a költségkeretet minden költséget az Azure-ból?

[Az Azure Marketplace-en közzétett néhány külső szolgáltatások](billing-understand-your-azure-marketplace-charges.md) előfizetési kreditek nem használhatók, és képes külön díjat számolunk akkor is, ha a költségkeret van beállítva. Ilyenek például a Visual Studio-licencek, a Azure Active Directory premium, a támogatási csomagok és a legtöbb harmadik féltől származó szolgáltatások. Amikor üzembe helyezi az új külső szolgáltatás, figyelmeztetés, hogy tudja, a szolgáltatások számlázása külön-külön jelenik meg:

![Marketplace-en beszerzési figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
