---
title: fájl belefoglalása
description: fájl belefoglalása
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/20/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: df78133f602466681da64d2666a311e1649c598f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028795"
---
A helyszíni virtuális gépek használata lehetővé teszi, hogy a kihasználatlan kapacitást jelentős költségmegtakarítással használja. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. A helyszíni virtuális gépek üzembe helyezésekor az Azure kiosztja a virtuális gépeket, ha rendelkezésre áll kapacitás, de ezekhez a virtuális gépekhez nem biztosítunk SLA-t. A helyszíni virtuális gépek nem biztosítanak magas rendelkezésre állású garanciákat. Az Azure-infrastruktúra minden olyan időpontban, amikor az Azure-nak szüksége van a kapacitásra, 30 másodperces figyelmeztetéssel kizárja a helyszíni virtuális gépeket. 


## <a name="eviction-policy"></a>Kiürítési szabályzat

A virtuális gépeket a kapacitás vagy a beállított maximális ár alapján lehet kizárni. A Direktszínű virtuális gépek létrehozásakor beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

A *felszabadítási* házirend áthelyezi a virtuális gépet a leállított, lefoglalt állapotba, így később újra üzembe helyezheti. Azonban nem garantálható, hogy a foglalás sikeres lesz. A fel nem osztott virtuális gépek a kvóta alapján számítanak, és a mögöttes lemezek tárolási költségei lesznek felszámítva. 

Ha azt szeretné, hogy a virtuális gép törölhető legyen a kizárása után, beállíthatja a kizárási házirendet a *törléshez*. A kizárt virtuális gépeket a rendszer a mögöttes lemezekkel együtt törli, így nem kell tovább fizetnie a tárterületért. 

Engedélyezheti, hogy a virtuális gép értesítéseit az [Azure Scheduled Eventson](../articles/virtual-machines/linux/scheduled-events.md)keresztül fogadja. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 


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

A helyszíni virtuális gépek bármely régióba üzembe helyezhetők, kivéve Microsoft Azure China 21Vianet.

<a name="channel"></a>

Jelenleg a következő [típusú ajánlatok](https://azure.microsoft.com/support/legal/offer-details/) támogatottak:

-   Nagyvállalati Szerződés
-   Használatalapú fizetés
-   Szponzorált
- A felhőalapú szolgáltató (CSP) esetében forduljon a partnerhez


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


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](https://docs.microsoft.com/answers/topics/azure-spot.html). 



