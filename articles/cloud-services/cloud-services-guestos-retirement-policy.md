---
title: Támogatási és megszüntetési szabályzat útmutató az Azure Vendég operációs rendszeréhez | Microsoft dokumentumok
description: Tájékoztatást nyújt arról, hogy a Microsoft mit fog támogatni a Cloud Services által használt Azure Vendég operációs rendszer tekintetében.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68945452"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Az Azure Vendég operációs rendszer támogatása és a megszüntetési szabályzat
Az ezen az oldalon található információk az Azure Guest operációs rendszer[(Vendég operációs rendszer](cloud-services-guestos-update-matrix.md)) a Cloud Services feldolgozó és a webes szerepkörök (PaaS) vonatkozik. Nem vonatkozik a virtuális gépekre (IaaS).

A Microsoft közzétett támogatási szabályzattal rendelkezik [a vendég operációs rendszerhez.](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq) Az éppen olvasott lap leírja a házirend megvalósításának módját.

A politika

1. A Microsoft **legalább a vendég operációs rendszer legutóbbi két családját**támogatja. Ha egy család kivan vonva, az ügyfeleknek 12 hónap áll rendelkezésére a hivatalos nyugdíjazási dátumtól számítva, hogy frissítsenek egy újabb támogatott vendég operációsrendszer-családra.
2. A Microsoft **legalább a támogatott vendégoperációsrendszer-családok legújabb két verzióját**támogatja.
3. A Microsoft **legalább az Azure SDK legújabb két verzióját**támogatja. Az SDK egy verziójának kivonásakor az ügyfeleknek a hivatalos megszüntetési dátumtól számított 12 hónap áll rendelkezésére, hogy újabb verzióra frissítsenek.

Időnként kettőnél több család vagy kiadás is támogatott. A vendég operációs rendszer hivatalos támogatási információi megjelennek az [Azure vendég operációs rendszer kiadásaiban és az SDK kompatibilitási mátrixban.](cloud-services-guestos-update-matrix.md)

## <a name="when-a-guest-os-version-is-retired"></a>Vendég operációs rendszer verziókivonásakor
Új vendég operációs rendszer **verziók** kerülnek bevezetésre körülbelül minden hónapban, hogy bele a legújabb MSRC frissítéseket. A rendszeres havi frissítések miatt a vendég operációs rendszer verziója általában le van tiltva körülbelül 60 nappal a kiadása után. Ez a tevékenység legalább két vendég operációsrendszer-verziót tart minden család hoz rendelkezésre használható.

### <a name="process-during-a-guest-os-family-retirement"></a>Folyamat a vendég operációsrendszer-család nyugdíjba vonulása során
Egyszer a visszavonulás van jelentették be, vásárlók volna egy 12 hónap " átmeneti" időköz előtt a régebb család van hivatalosan távoli -ból szolgáltatás. Ez az átmeneti idő a Microsoft belátása szerint meghosszabbítható. A frissítések et közzétehetjuk az [Azure vendég operációs rendszerének kiadásaiban és az SDK-kompatibilitási mátrixban.](cloud-services-guestos-update-matrix.md)

A fokozatos nyugdíjazási folyamat hat (6) hónappal az átmeneti időszak kezdete előtt kezdődik. Ez idő alatt:

1. A Microsoft értesíti az ügyfeleket a megszüntetésről.
2. Az Azure SDK újabb verziója nem támogatja a kinyugdíjas vendég operációsrendszer-családot.
3. A felhőszolgáltatások új telepítései és átcsoportosításai nem engedélyezettek a nyugdíjas családban

A Microsoft az átmeneti időszak utolsó napjáig, az úgynevezett "lejárati dátumig" folytatja a vendég operációs rendszer legújabb frissítéseit tartalmazó új vendég operációs rendszer-verziót. A lejárati dátum, felhőszolgáltatások továbbra is fut nem támogatott az Azure SLA. A Microsoft saját belátása szerint kényszerítheti a frissítéseket, törölheti vagy leállíthatja ezeket a szolgáltatásokat ezen időpont után.

### <a name="process-during-a-guest-os-version-retirement"></a>Folyamat a vendég operációs rendszer verzióinak levonása során
Ha az ügyfelek úgy vannak beállítva, hogy a vendég operációs rendszer automatikusan frissüljön, soha nem kell aggódniuk a vendég operációs rendszer verzióinak kezelése miatt. Mindig a vendég operációs rendszer legújabb verzióját fogják használni.

Vendég operációs rendszer verziók jelennek meg minden hónapban. A rendszeres kiadások aránya miatt minden verzió rögzített élettartammal rendelkezik.

A 60 nap az élettartam, a verzió "*levan tiltva*". A "Letiltva" azt jelenti, hogy a verzió törlődik a portálról. A verzió már nem állítható be a CSCFG konfigurációs fájlból. A meglévő központi telepítések futnak. De az új központi telepítések és a kód és a konfigurációs frissítések a meglévő központi telepítések nem engedélyezett.

Valamikor a "letiltás" után a vendég operációs rendszer verziója "lejár", és a lejárt verziót futtató telepítések biztonsági és biztonsági résproblémáknak vannak kitéve. A lejárat általában kötegekben történik, így a letiltástól a lejáratig terjedő időszak változhat.

Azok az ügyfelek, akik úgy konfigurálják a szolgáltatásaikat, hogy manuálisan frissítsék a vendég operációs rendszert, győződjenek meg arról, hogy a szolgáltatásaik támogatott vendég operációs rendszeren futnak. Ha egy szolgáltatás úgy van beállítva, hogy automatikusan frissítse a vendég operációs rendszert, az alapul szolgáló platform biztosítja a megfelelőséget, és frissít a legújabb vendég operációs rendszerre.

Ezeket az időszakokat a Microsoft saját belátása szerint hosszabb időre lehet letenni az ügyfelek átmenetének megkönnyítése érdekében. A módosításokat az [Azure Vendég operációsrendszer-kiadások és az SDK-kompatibilitási mátrix fogja](cloud-services-guestos-update-matrix.md)közölni.

### <a name="notifications-during-retirement"></a>Értesítések a nyugdíjba vonulás során
* **Családi nyugdíj** <br>A Microsoft blogbejegyzéseket és portálértesítést fog használni. Azok az ügyfelek, akik még mindig egy nyugdíjas vendég operációsrendszer-családot használnak, közvetlen kommunikáción (e-mailen, portálüzeneteken, telefonhíváson) keresztül értesítést kapnak a hozzárendelt szolgáltatás-rendszergazdáktól. Minden módosítás felkerül az [Azure vendég operációsrendszer-kiadásokra és az SDK-kompatibilitási mátrixra.](cloud-services-guestos-update-matrix.md)
* **Verzió kivonása** <br>Minden változás és a dátumok at fordulnak elő az [Azure Vendég operációs rendszer releases és SDK kompatibilitási mátrix](cloud-services-guestos-update-matrix.md), beleértve a kiadás, letiltva és lejárati. A szolgáltatások rendszergazdái e-maileket kapnak, ha a központi operációs rendszer letiltott operációsrendszer-verzióján vagy -családon futó központi telepítéseket futtatnak. Az e-mailek időzítése változhat. Általában ezek legalább egy hónappal a letiltás előtt, bár ez az időzítés nem hivatalos SLA.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
**Hogyan enyhíthetem a migráció hatásait?**

Azt javasoljuk, hogy a legújabb vendég operációsrendszer-család a felhőszolgáltatások tervezéséhez.

1. Kezdje el megtervezni az áttelepítést egy újabb családra.
2. Állítson be ideiglenes teszttelepítéseket az új családon futó felhőszolgáltatás teszteléséhez.
3. Állítsa a vendég operációsrendszer-verziót **automatikusra** (osVersion=* a [.cscfg](cloud-services-model-and-package.md#cscfg) fájlban), így az új vendég operációsrendszer-verziókba való áttelepítés automatikusan megtörténik.

**Mi a teendő, ha a webalkalmazás mélyebb integrációt igényel az operációs rendszerrel?**

Ha a webalkalmazás-architektúra az operációs rendszer mögöttes szolgáltatásaitól függ, használja a platform által támogatott funkciókat, például [az indítási feladatokat](cloud-services-startup-tasks.md) vagy más bővíthetőségi mechanizmusokat. Másik lehetőségként használhatja [az Azure virtuális gépeket](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastruktúra szolgáltatásként), ahol ön felelős az alapul szolgáló operációs rendszer karbantartásáért.

## <a name="next-steps"></a>További lépések
Tekintse át a vendég operációs rendszer legújabb [kiadásait.](cloud-services-guestos-update-matrix.md)
