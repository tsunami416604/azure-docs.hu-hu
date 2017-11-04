---
title: "A felhőalapú szolgáltatás észlelt lefoglalási hibák elhárítása |} Microsoft Docs"
description: "Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban"
services: azure-service-management, cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 33d017d0e09e9b288b0514e85c8865f83a8a2fa1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban
## <a name="summary"></a>Összefoglalás
Ha a példányok telepítése az egy felhőalapú szolgáltatás, vagy adja hozzá az új webes vagy feldolgozói szerepkör példányok, a Microsoft Azure számítási erőforrásokat foglal le. Hibák léphetnek ezen műveletek végrehajtásakor, még mielőtt az Azure-előfizetésre vonatkozó korlátok alkalmanként. Ez a cikk ismerteti az egyes a közös pufferallokációs hibák okait, és lehetséges javítási javasol. Az információ is hasznos lehet a szolgáltatásokhoz központi telepítésének tervezése során.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Háttér – foglalási működése
Az Azure adatközpontjaiban van particionálva csoportokba. Egy új felhőalapú szolgáltatás memóriafoglalási kérelem több fürt kísérlet történik. Ha az első példány egy felhőalapú szolgáltatást (az átmeneti vagy üzemi), a felhőalapú szolgáltatás központi telepítése egy fürt lekérdezi rögzítve. A további telepítések a felhőszolgáltatás fordul elő az ugyanabban a fürtben. Ebben a cikkben kifejezés ez szerint "fürt rögzítve". Alább 1 diagram azt ábrázolja, a kis-és egy normál foglalási, amely több fürtökben; kísérlet történik 2. ábrán látható, a kis-és memóriakiosztást, mert, ahol a meglévő felhőalapú szolgáltatás CS_1 tárolása fürt 2 van rögzítve.

![Foglalási diagramja](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Miért memóriafoglalási hiba történik
Egy memóriafoglalási kérelem fürtre van rögzítve, nincs hiányában szabad erőforrások keresésére, mivel a rendelkezésre álló erőforráskészletben található egy fürt csak egy magasabb esélyét. Továbbá ha a memóriafoglalási kérelem fürtre van rögzítve, de az adott fürt nem támogatja a kért erőforrás típusát, a kérelem sikertelen lesz akkor is, ha a szabad erőforrás rendelkezik. 3. ábra alatt az esetben, ha egy rögzített foglalás sikertelen lesz, mivel az egyetlen jelölt fürtnek nincs szabad erőforrást mutatja be. 4. ábrán látható, az esetet, ahol egy rögzített foglalás sikertelen lesz, mivel az egyetlen jelölt fürt nem támogatja a kért Virtuálisgép-méret, annak ellenére, hogy a fürt ingyenes erőforrással rendelkezik.

![Rögzített memóriafoglalási hiba](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>A felhőszolgáltatások észlelt lefoglalási hibák elhárítása
### <a name="error-message"></a>Hibaüzenet
A következő hibaüzenet jelenhet meg:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Gyakori problémák
Az alábbiakban a gyakori foglalási forgatókönyvek, amelyek egy foglalási kérelem egy fürtön rögzítve.

* Telepítésekor átmeneti helyre - felhőszolgáltatás van a központi telepítés vagy tárolóhelye, majd a teljes körű felhőalapú szolgáltatás rögzítve fürtön.  Ez azt jelenti, hogy ha egy telepítés már szerepel az éles webalkalmazásra, majd egy új átmeneti telepítési csak lehet hozzárendelni a fürtön, amelyen az éles webalkalmazásra. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.
* Skálázás – új példányok hozzáadása egy meglévő felhőszolgáltatáshoz kell rendelnie az ugyanabban a fürtben.  Rövid skálázás kérelmek általában osztják, de nem minden esetben. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.
* Affinitáscsoport - új központi telepítést egy üres felhőszolgáltatásban foglalhatók által a hálót a fürthöz az adott régióban, kivéve, ha a felhőalapú szolgáltatás affinitáscsoporthoz van rögzítve. Ugyanabban az affinitáscsoportban történő telepítését ugyanazon a fürtön kísérli meg a rendszer. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.
* Affinitáscsoport vNet - régebbi virtuális hálózatok affinitáscsoportok régiókat helyett volt kötve, és ezeket a virtuális hálózatokat a cloud services az affinitáscsoport fürt lesz rögzítve. Ez a fajta virtuális hálózatot való telepítésének a rögzített fürtön kísérli meg a rendszer. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.

## <a name="solutions"></a>Megoldások
1. Telepítse újra az új felhőalapú szolgáltatás – Ez a megoldás valószínűleg legtöbb sikeres, az adott régióban összes fürt választhat a platform lehetővé teszi.

   * Új felhőalapú szolgáltatás az alkalmazások és szolgáltatások telepítése  
   * Frissítheti a CNAME vagy egy olyan rekordot irányítsák a forgalmat a új felhőszolgáltatással
   * Nulla forgalom a régi helyhez állapotra vált, amennyiben törölheti a régi felhőalapú szolgáltatás. Ez a megoldás kell fel Önnek leállítása nélkül.
2. Üzemi és átmeneti üzembe helyezési ponti törölni – Ez a megoldás megtartja a meglévő DNS-nevével, de az alkalmazás leállás, akkor.

   * A szolgáltatás üzemi és átmeneti üzembe helyezési ponti egy meglévő felhőalapú szolgáltatás, így felhőszolgáltatás nem üres, törölje, majd
   * Hozzon létre egy új központi telepítést a meglévő felhőalapú szolgáltatást. Ez újra megkísérli a foglalási régióban minden fürtön. Győződjön meg róla, felhőalapú szolgáltatás nem kötődik azonban egy affinitáscsoporthoz.
3. Fenntartott IP - Ez a megoldás megtartja a meglévő IP-cím, de az alkalmazás állásidőt okoz.  

   * Hozzon létre egy foglalt IP-cím a meglévő telepítéshez Powershell használatával

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Hajtsa végre a #2 promptjai meggyőződött arról, hogy a szolgáltatás szolgáltatáskonfigurációs SÉMA adni az új foglalt IP-címet.
4. Távolítsa el az új központi telepítéseknél - affinitáscsoport Affinitáscsoportok már nem támogatottak. Kövesse a fenti új felhőalapú szolgáltatás telepítése #1. Győződjön meg róla, felhőalapú szolgáltatás nem olyan affinitáscsoportban van.
5. Regionális virtuális hálózatot – lásd: átalakítása [hogyan kell áttelepíteni egy regionális virtuális hálózatot (VNet) Affinitáscsoportok](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
