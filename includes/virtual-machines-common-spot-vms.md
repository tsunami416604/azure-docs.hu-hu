---
title: fájl belefoglalása
description: fájl belefoglalása
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e7dbac1f4fad940b817befa3a45447cf7367c28c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317643"
---
A helyszíni virtuális gépek használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. A helyszíni virtuális gépek üzembe helyezésekor az Azure kiosztja a virtuális gépeket, ha rendelkezésre áll kapacitás, de ezekhez a virtuális gépekhez nem biztosítunk SLA-t. A helyszíni virtuális gépek nem biztosítanak magas rendelkezésre állású garanciákat. Az Azure-infrastruktúra minden olyan időpontban, amikor az Azure-nak szüksége van a kapacitásra, 30 másodperces figyelmeztetéssel kizárja a helyszíni virtuális gépeket. 


## <a name="eviction-policy"></a>Kizárási szabályzat

A virtuális gépeket a kapacitás vagy a beállított maximális ár alapján lehet kizárni. A virtuális gépek esetében a kizárási szabályzat úgy van beállítva, hogy *felszabadítsa* a kizárt virtuális gépeket a leállított, lefoglalt állapotba, így később újra üzembe helyezheti a kizárt virtuális gépeket. A helyszíni virtuális gépek újbóli kiosztása azonban attól függ, hogy elérhető-e a hely kapacitása. A fel nem osztott virtuális gépek a helyszíni vCPU-kvótába kerülnek, és a mögöttes lemezekért díjat számítunk fel. 

A felhasználók eldönthetik, hogy a virtuális gép értesítéseit az [Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md)használatával kapják meg. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 


| Beállítás | Eredmény |
|--------|---------|
| A maximális díj értéke >= a jelenlegi díj. | A virtuális gép üzembe helyezése esetén a kapacitás és a kvóta elérhető. |
| A maximális árat úgy kell beállítani, hogy az aktuális árat <. | A virtuális gép nincs telepítve. Hibaüzenetet kap arról, hogy a maximális árat >= aktuális áron kell megadnia. |
| Leállítás/felszabadítási virtuális gép újraindítása, ha a maximális díj >= a jelenlegi díj | Ha van kapacitása és kvóta, akkor a rendszer telepíti a virtuális gépet. |
| Leállítás/felszabadítási virtuális gép újraindítása, ha a maximális díj < a jelenlegi áron | Hibaüzenetet kap arról, hogy a maximális árat >= aktuális áron kell megadnia. | 
| A virtuális gép ára felment, és most már > a maximális árat. | A virtuális gép el lesz távolítva. A tényleges kizárást megelőzően egy 30%-os értesítést kap. | 
| A kizárás után a virtuális gép ára visszatérhet, hogy < a maximális árat. | A virtuális gép nem indul el automatikusan újra. Saját maga is újraindíthatja a virtuális gépet, és az aktuális áron kell fizetnie. |
| Ha a maximális díj értéke`-1` | A virtuális gép díjszabása nem kerül kizárásra. A maximális díj a jelenlegi díj, amely a standard szintű virtuális gépek árával függ. A standard díj felett soha nem számítunk fel díjat.| 
| A maximális ár módosítása | A maximális díj megváltoztatásához fel kell szabadítania a virtuális gépet. Szabadítsa fel a virtuális gépet, állítson be egy új maximális árat, majd frissítse a virtuális gépet. |

## <a name="limitations"></a>Korlátozások

A következő virtuálisgép-méretek nem támogatottak a Direktszínű virtuális gépek esetében:
 - B sorozat
 - Bármilyen méretű promóciós verzió (például Dv2, NV, NC, H promo-méretek)

A helyszíni virtuális gépek jelenleg nem használhatnak ideiglenes operációsrendszer-lemezeket.

A helyszíni virtuális gépek bármely régióba üzembe helyezhetők, kivéve Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Díjszabás

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 


A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta.


##  <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** A létrehozása után a egy direkt virtuális gép, amely azonos a normál szabványos virtuális géppel?

**A:** Igen, a helyszíni virtuális gépekhez nem biztosítunk SLA-t, és a szolgáltatás bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a virtuális gépek helyett használjon szabványos virtuális gépeket, ha a kapacitásra azonnal szükség van.


**K:** Hogyan kezelik a kvóta a helyszíni virtuális gépeket?

**A:** A helyszíni virtuális gépek külön kvóta-készlettel rendelkeznek. A helyszíni kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**K:** Igényelhetek további kvótát a helyszínen?

**A:** Igen, elküldheti a kérést, hogy növelje a helyszíni virtuális gépek kvótáját a [normál kvóta-kérési folyamaton](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)keresztül.


**K:** Milyen csatornák támogatják a helyszíni virtuális gépeket?

**A:** Tekintse meg az alábbi táblázatot a helyszíni virtuális gépek rendelkezésre állásához.

<a name="channel"></a>

| Azure-csatornák               | Azure helyszíni virtuális gépek rendelkezésre állása       |
|------------------------------|-----------------------------------|
| Nagyvállalati Szerződés         | Igen                               |
| Használatalapú fizetés                | Igen                               |
| Felhőalapú szolgáltató (CSP) | [Kapcsolatfelvétel a partnerrel](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Microsoft-ügyfélszerződés | Igen                               |
| Előnyök                     | Nem érhető el                     |
| Szponzorált                    | Igen                               |
| Ingyenes próbaverzió                   | Nem érhető el                     |


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](https://docs.microsoft.com/answers/topics/azure-spot.html). 



