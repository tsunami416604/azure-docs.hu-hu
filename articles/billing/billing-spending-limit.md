---
title: Azure-költségkeret | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan működik az Azure-költségkeret, és Hogyan távolítható el.
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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114452"
---
# <a name="azure-spending-limit"></a>Azure-költségkeret

Az Azure-beli költségkeret nem teszi lehetővé a kreditek összegének megadását. Minden új ügyfél, aki regisztrál egy olyan Azure-próbaverzióra vagy-ajánlatra, amely több hónapra kiterjedő kreditet tartalmaz, alapértelmezés szerint be van kapcsolva a költségkeret. A költségkeret $0, és nem módosítható. A költségkeret nem módosítható például $100-re. Azonban eltávolíthatja a költségkeretet. Tehát nem rendelkezik korláttal, vagy nulla korláttal rendelkezik, amely megakadályozza a legtöbb kiadást. A költségkeret nem érhető el az előfizetések esetében, és az utólagos elszámolású díjszabás keretében megjelenő kötelezettségvállalási tervek és csomagok. Tekintse meg az [Azure-ajánlatok teljes listáját és a költségkeret rendelkezésre állását](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Költségkeret elérése

Ha a használat során az Azure-előfizetéshez tartozó havi összegeket kimeríti, az Ön által telepített szolgáltatások le lesznek tiltva a számlázási időszak további részében.

Ha például az előfizetéshez tartozó összes kreditet tölti fel, az Azure-erőforrások üzembe helyezése az éles környezetből megszűnik, és az Azure-beli virtuális gépek le vannak állítva és le lesznek foglalva. A Storage-fiókok adatai csak olvashatóként érhetők el.

Ha a következő elszámolási időszak elején az előfizetés ajánlata több hónapig is tartalmaz krediteket, az előfizetés automatikusan újra engedélyezve lesz. Ezután újra üzembe helyezheti az Azure-erőforrásokat, és teljes hozzáféréssel rendelkezik a Storage-fiókokhoz és-adatbázisokhoz.

Az Azure e-mail-értesítéseket küld, amikor eléri az előfizetés költségkeretét. Jelentkezzen be a [fiók](https://account.windowsazure.com/Subscriptions) központjába, és tekintse meg a költségkeretet elérő előfizetésekkel kapcsolatos értesítéseket.

Ha ingyenes próbaverziós előfizetéssel rendelkezik, és eléri a költségkeretet, az utólagos elszámolású [](billing-upgrade-azure-subscription.md) díjszabással is frissítheti a költségkeretet, és automatikusan engedélyezheti az előfizetést.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>A költségkeret eltávolítása az Account Center szolgáltatásban

A költségkeretet bármikor eltávolíthatja, amíg érvényes fizetési mód van társítva az Azure-előfizetéséhez. A több hónapos Kredittel rendelkező ajánlatok esetében a következő számlázási időszak elején is engedélyezheti a költségkeretet.

Az alábbi lépéseket követve távolíthatja el a költségkeretet:

1. Jelentkezzen be a [fiók](https://account.windowsazure.com/Subscriptions)központjába.
1. Válasszon egy előfizetést. Ha az előfizetés le van tiltva, mert elérte a költségkeretet, kattintson az értesítésre: **Az előfizetés elérte a költségkeretet, és le van tiltva a díjak elkerülése érdekében.** Ellenkező esetben kattintson a költségkeret **eltávolítása** elemre az **előfizetés állapota** területen.
1. Válasszon egy Önnek megfelelő lehetőséget.

![A költségkeret eltávolítására szolgáló lehetőség kiválasztása](./media/billing-spending-limit/remove-spending-limit.PNG)

| Beállítás | Következmény |
| --- | --- |
| A költségkeret eltávolítása meghatározatlan időre | A költségkeret eltávolítása oly módon, hogy az nem kapcsolódik vissza automatikusan a következő elszámolási időszak kezdetén. |
| A költségkeret eltávolítása az adott elszámolási időszakban | A költségkeret eltávolítása oly módon, hogy a következő elszámolási időszak kezdetén automatikusan visszakapcsolódik. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Miért érdemes eltávolítani a költségkeretet

A költségkeret megakadályozhatja bizonyos harmadik féltől származó és Microsoft-szolgáltatások üzembe helyezését vagy használatát. Az alábbi helyzetekben érdemes törölni az előfizetése költségkeretét.

-  Olyan első féltől származó lemezképeket kíván telepíteni, mint például az Oracle és a szolgáltatások, például az Azure DevOps Services. Ez a helyzet azt eredményezi, hogy szinte azonnal túllépi a költségkeretet, és letiltja az előfizetés letiltását.
- Olyan szolgáltatásokkal rendelkezik, amelyeket nem szeretne megszakítani.
- Olyan szolgáltatásokkal és erőforrásokkal rendelkezik, amelyeknek beállításait – például a virtuális IP-címeket – nem szeretné elveszteni. Ezek a beállítások elvesznek, ha a szolgáltatások és erőforrások le vannak foglalva.

## <a name="turn-on-the-spending-limit-after-removing"></a>A költségkeret bekapcsolása az eltávolítás után

Ez a funkció csak akkor érhető el, ha a költségkeretet határozatlan ideig eltávolították. Módosítsa úgy, hogy automatikusan bekapcsolja a következő számlázási időszak elején.

1. Jelentkezzen be a [fiók](https://account.windowsazure.com/Subscriptions)központjába.
1. Kattintson a sárga szalagcímre a költségkeret-korlát módosításához.
1. Válassza a költségkeret bekapcsolása lehetőséget **a számlázási\> időszak \<következő számlázási időszakának kezdő dátumánál**

## <a name="custom-spending-limit"></a>Egyéni költségkeret

Az egyéni költségkeret-korlátok nem érhetők el.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>A költségkeret nem akadályozza meg az összes díjat

[Az Azure piactéren közzétett egyes külső szolgáltatások](billing-understand-your-azure-marketplace-charges.md) nem használhatók fel az előfizetési kreditekhez, és külön díjat számítunk fel, még akkor is, ha a költségkeret be van állítva. Ilyenek például a Visual Studio-licencek, a Azure Active Directory Premium, a támogatási csomagok és a legtöbb harmadik féltől származó márkás szolgáltatás. Új külső szolgáltatás kiépítésekor megjelenik egy figyelmeztetés, amely lehetővé teszi, hogy a szolgáltatások számlázása külön történjen:

![Piactér-vásárlási figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- Az utólagos elszámolású [](billing-upgrade-azure-subscription.md) díjszabással frissítheti a csomagot.
