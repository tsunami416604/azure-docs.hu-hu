---
title: "Azure költségkeret megértése |} Microsoft Docs"
description: "Ismerteti, hogyan működik a költségkeret Azure és annak eltávolításáról"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/22/2017
ms.author: genli
ms.openlocfilehash: d222300a591bcba556be5813ada2de555d25a1f0
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2017
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Azure-korlátozás és annak eltávolításáról költségeik ismertetése

Az Azure-ban költségkeret létezik-e a jóváírás felett költségeik megelőzése érdekében. Új ügyfelek számára próba vagy ajánlatokat, amely tartalmazza a kreditek több hónapban előfizetés a költségkeret maximumát, alapértelmezés szerint engedélyezve van. A költségkeret maximumát értéke 0. Nem módosítható. A költségkeret nem érhető el az olyan előfizetéstípusokhoz, mint a használatalapú fizetés és a kötelezettségvállalásos csomagok. Tekintse meg a [teljes listáját az Azure-ajánlatok és a költségkeret maximumát rendelkezésre állását](https://azure.microsoft.com/support/legal/offer-details/).

**Számlázási riasztás keres?** Lásd: [számlázással vagy jóváírás értesítések az Azure-előfizetések beállítása](billing-set-up-alerts.md).

## <a name="what-happens-when-i-reach-the-spending-limit"></a>Mi történik a költségkeret elérése esetén?

A díjak, amely a havi díjak lefoglalhat használati eredményezi az előfizetéshez mellékelt, amikor a telepített szolgáltatások le vannak tiltva a többi számlázási időszak. 

Például ha az előfizetés részét képező összes jóváírás eltöltött, központilag telepített Felhőszolgáltatások el lesznek távolítva az üzemi és az Azure virtuális gépek leállítása és deszerializálni lefoglalt. Az adatok a storage-fiókok és az adatbázis csak olvasható módon érhetők el.

A következő számlázási időszak elején, ha az előfizetés ajánlat kreditek is több hónapban, az előfizetés volna automatikusan ismét engedélyezve. Ezután telepítse újra a Cloud Services és a storage-fiókok és az adatbázisok teljes hozzáféréssel rendelkeznek.

Program e-mail értesítéseket küld, amikor a költségkeret maximumát kattint az előfizetéséhez. Jelentkezzen be a [Account Center](https://account.windowsazure.com/Subscriptions), és -előfizetések, amelyek túllépte a költségkeretét szóló értesítések látja.

Ha az ingyenes próbaverzió, és a felhasználók elérését a költségkeret maximumát, akkor [váltson használatalapú fizetés](billing-upgrade-azure-subscription.md) távolítsa el a költségkeret maximumát, és az előfizetés automatikusan ismét engedélyezve van.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>Távolítsa el a költségkeret maximumát Account Center

A költségkeretet bármikor eltávolíthatja mindaddig, amíg van érvényes fizetési mód rendelve az előfizetéséhez. A jóváírás több hónap rendelkező ajánlatok esetén is engedélyezheti újra a költségkeret maximumát a következő számlázási időszak elején.

Az alábbi lépéseket követve távolíthatja el a költségkeretet:

1. Jelentkezzen be a [Account Center](https://account.windowsazure.com/Subscriptions).
1. Válasszon egy előfizetést.
. Ha az előfizetés le van tiltva, mert elérte a költségkeret maximumát, kattintson az értesítés: "Előfizetés elérte a Költségkeretét és díjak elkerülése végett letiltottuk." Ellenkező esetben kattintson a **költségkeret eltávolítása** a a **ELŐFIZETÉS ÁLLAPOTÁT** területen.
1. Válasszon egy Önnek megfelelő lehetőséget.

![A költségkeret távolítsa el a megfelelő beállítás kiválasztása](./media/billing-spending-limit/remove-spending-limit.PNG)

|Beállítás|Következmény|
|-------|-----|
|A költségkeret eltávolítása meghatározatlan időre|A költségkeret eltávolítása oly módon, hogy az nem kapcsolódik vissza automatikusan a következő elszámolási időszak kezdetén.|
|A költségkeret eltávolítása az adott elszámolási időszakban|A költségkeret eltávolítása oly módon, hogy a következő elszámolási időszak kezdetén automatikusan visszakapcsolódik.|

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>Miért érdemes eltávolítani a költségkeret maximumát?

A költségkeret maximumát sikerült akadályozza meg a telepítését, és egyes külső és a Microsoft-szolgáltatások használatával. A következő szituációkban javasolt a költségkeret eltávolítása az előfizetésből.

* Belső rendszerképeket tervez üzembe helyezni, mint például az Oracle, és olyan szolgáltatásokat, mint például a Visual Studio Online Team Services. Ez a forgatókönyv miatt a válaszpuffer szinte azonnal a költségkeret maximumát, és hatására az előfizetés le kell tiltani.
* Olyan szolgáltatásai vannak, amelyek működése nem szakadhat meg.
* Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyeknek beállításait – például a virtuális IP-címeket – nem szeretné elveszteni. Ezek a beállítások is elvesznek, ha a szolgáltatások és erőforrások való hozzárendelése.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>Hogyan az Eltávolítás után a költségkeret maximumát bekapcsolása?

Ez a funkció csak a költségkeret maximumát határozatlan ideig el lett távolítva esetén érhető el. Módosítsa a következő számlázási időszak kezdetekor automatikusan bekapcsolása.

1. Jelentkezzen be a [Account Center](https://account.windowsazure.com/Subscriptions).
1. Kattintson a sárga szalagcím a költségkeret korlát beállítás módosítása.
1. Válasszon **kapcsolja be a következő számlázási időszakban költségkeret \<számlázási időszak kezdete\>**

### <a name="how-do-i-set-a-custom-spending-limit"></a>Hogyan állíthatom be egy egyéni költségkeret maximumát?

Nem tudunk egyéni korlátokat ma töltenek. Azonban a naplózott a [számlázási riasztások szabályozhatja a kiadásokat](billing-set-up-alerts.md).

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>A költségkeret maximumát akadályozza a díjak megfizetése az Azure-ból?

[Egyes külső szolgáltatások közzététele az Azure piactéren](billing-understand-your-azure-marketplace-charges.md) nem használható az előfizetési kreditek, és külön költségek is fel Önnek, akkor is, ha a költségkeret maximumát van beállítva. Ilyen például a Visual Studio licencek, a Azure Active Directory premium, a támogatási csomagokat és a legtöbb külső vállalati arculattal szolgáltatások. Ha egy új külső szolgáltatás kiépítése, figyelmeztetés jelenik meg arról, hogy a szolgáltatások külön vannak számlázva:

![Piactér-beszerzési figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
