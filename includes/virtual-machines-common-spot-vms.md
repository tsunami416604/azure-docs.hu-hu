---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7cfa6e9810057493cc3007eec7fd1668a70c727e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77179005"
---
A spot virtuális gépek használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek. Ezért a direkt virtuális gépek kiválóan szolgálnak olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. A direkt virtuális gépek üzembe helyezésekor az Azure lefoglalja a virtuális gépeket, ha van kapacitás, de ezeka virtuális gépek nem rendelkeznek SLA-val. A Spot virtuális gép nem nyújt magas rendelkezésre állási garanciát. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek 30 másodperces értesítéssel. 

> [!IMPORTANT]
> A direktpéldányok jelenleg nyilvános előzetes verzióban vannak.
> Ez az előzetes verzió éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

## <a name="eviction-policy"></a>Kilakoltatási politika

A virtuális gépek a kapacitás vagy a beállított maximális ár alapján kilakoltathatók. A virtuális gépek, a kilakoltatási szabályzat beállítása *Felszabadítása,* amely áthelyezi a kilakoltatott virtuális gépek a leállított felszabadított állapotba, amely lehetővé teszi a kilakoltatott virtuális gépek egy későbbi időpontban újratelepítése. A virtuális gépek újraelosztása azonban a rendelkezésre álló azonnali kapacitástól függ. A felszabadított virtuális gépek beleszámítanak a helyszíni vCPU-kvótába, és az alapul szolgáló lemezekért díjat számítunk fel. 

A felhasználók engedélyezhetik, hogy az [Azure-ban megadott eseményeken](../articles/virtual-machines/linux/scheduled-events.md)keresztül fogadjanak a virtuális gépen keresztüli értesítéseket. Ez értesíti, ha a virtuális gépek kivannak zárva, és 30 másodpercáll majd a feladatok befejezésére és a leállítási feladatok végrehajtására a kilakoltatás előtt. 


| Beállítás | Eredmény |
|--------|---------|
| A maximális ár >= az aktuális ár. | Virtuális gép telepítve van, ha a kapacitás és a kvóta áll rendelkezésre. |
| A maximális ár az aktuális ár < van beállítva. | A virtuális gép nincs telepítve. Hibaüzenetet kap, hogy a maximális árnak >= aktuális árnak kell lennie. |
| A virtuális gép leállításának/felszabadításának újraindítása, ha a maximális ár >= az aktuális ár | Ha van kapacitás és kvóta, majd a virtuális gép üzembe helyezése. |
| A virtuális gép leállításának/felszabadításának újraindítása, ha a maximális ár < az aktuális árra | Hibaüzenetet kap, hogy a maximális árnak >= aktuális árnak kell lennie. | 
| A virtuális gép ára felment, és most > a maximális árat. | A virtuális gép kilakoltatva lesz. Kapsz egy 30-as értesítést a tényleges kilakoltatás előtt. | 
| A kilakoltatás után a virtuális gép ára visszaáll a maximális ár <. | A virtuális gép nem indul újra automatikusan. A virtuális gép saját maga is újraindítható, és az aktuális áron kerül felszámolásra. |
| Ha a maximális ár`-1` | A virtuális gép díjszabási okokból nem lesz kizárva. A maximális ár az aktuális ár lesz, a szabványos virtuális gépek áráig. Soha nem kell fizetnie a szokásos ár felett.| 
| A maximális ár módosítása | A maximális ár módosításához fel kell osztania a virtuális gép felszabadítását. Szabadítsa fel a virtuális gép, állítsa be az új maximális ár, majd frissítse a virtuális gép. |

## <a name="limitations"></a>Korlátozások

A következő virtuális gépméretek nem támogatottak a direkt virtuális gépek nél:
 - B-sorozat
 - Promo verziók bármilyen méretű (mint a Dv2, NV, NC, H promo méretben)

A direkt virtuális gépek jelenleg nem használhatják az ideiglenes operációsrendszer-lemezeket.

A direkt virtuális gépek a Microsoft Azure China 21Vianet kivételével bármely régióban telepíthetők.

## <a name="pricing"></a>Díjszabás

A direkt virtuális gépek díjszabása változó, a régió és a termékváltozat alapján. További információ: VM-díjszabás [Linuxra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windowsra.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 


A változó árképzés, akkor lehetősége van arra, hogy állítsa be a maximális ár, USA dollárban (USD), akár 5 tizedesjegy. Az érték `0.98765`például óránként $0,98765 USD max ár. Ha a maximális árat `-1`állítja be, a virtuális gép nem lesz kizárva az ár alapján. A virtuális gép ára az azonnali aktuális ár vagy egy szabványos virtuális gép ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta áll rendelkezésre.


##  <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** Miután létrehozta, egy spot virtuális gép ugyanaz, mint a normál szabványos virtuális gép?

**A.** Igen, kivéve, hogy nincs SLA a direkt virtuális gépekhez, és bármikor kilakoltathatók.


**K:** Mi a teendő, ha kilakoltatnak, de még mindig kapacitásra van szüksége?

**A.** Azt javasoljuk, hogy a direkt virtuális gépek helyett szabványos virtuális gépeket használjon, ha azonnal kapacitásra van szüksége.


**K:** Hogyan történik a kvóta kezelése a direkt virtuális gépekhez?

**A.** A direkt virtuális gépek külön kvótakészletet kapnak. A direkthelykvóta meg lesz osztva a virtuális gépek és a méretezési csoport példányai között. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**K:** Kérhetek további kvótát a Spot-ra?

**A.** Igen, a [szabványos kvótakérelem-folyamaton](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)keresztül elküldheti a direkt virtuális gépekre vonatkozó kvóta növelésére irányuló kérelmet.


**K:** Milyen csatornák támogatják a spot virtuális gépeket?

**A.** Tekintse meg az alábbi táblázatot a virtuális gépek rendelkezésre állásáról.

<a name="channel"></a>

| Azure-csatornák               | Az Azure spot virtuális gépek elérhetősége       |
|------------------------------|-----------------------------------|
| Nagyvállalati Szerződés         | Igen                               |
| Használatalapú fizetés                | Igen                               |
| Felhőszolgáltató (CSP) | [Vegye fel a kapcsolatot partnerével](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Előnyök                     | Nem érhető el                     |
| Szponzorált                    | Nem érhető el                     |
| Ingyenes próbaverzió                   | Nem érhető el                     |


**K:** Hol tehetek fel kérdéseket?

**A.** Tudod felad és tag `azure-spot` a kérdést a [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 



