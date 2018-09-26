---
title: Felhőszolgáltatás-foglalási hibák elhárítása |} A Microsoft Docs
description: Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 22888c76b27d287a8d7fb0f0f1f0a0d39d92375d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092952"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban
## <a name="summary"></a>Összegzés
Amikor példányok egy Felhőszolgáltatásban üzembe helyezni, vagy adja hozzá az új webes vagy feldolgozói szerepkör példányai, a Microsoft Azure számítási erőforrásokat foglal le. Kap időnként hibák, ezek a műveletek végrehajtásakor, még mielőtt eléri az Azure-előfizetés korlátaival. Ez a cikk ismerteti a gyakori hibák némelyike okait, és lehetséges javítási javasol. Az információk is hasznosak lehetnek a szolgáltatások központi telepítésének tervezése során.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Háttér – foglalási működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Új cloud service-foglalási kérelem több fürtön kísérlet történik. Amikor az első példány van üzembe helyezve, egy felhőszolgáltatáshoz (előkészítési vagy termelési), a felhőalapú szolgáltatás, amely egy fürt beolvasása rögzítve. A felhőszolgáltatás további telepítések ugyanazon a fürtön fog történni. Ebben a cikkben azt fog hivatkozni, ez, "fürt rögzített". 1. ábra alább látható a kis-és a egy normál foglalási, amely több fürtön; kísérlet történik 2. ábra szemlélteti a kis-és a egy elkülönített, amely rendelkezik rögzített fürt 2-re, mert a meglévő felhőalapú szolgáltatás CS_1 üzemeltető.

![Foglalási diagramja](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Miért foglalási hiba történik
Egy foglalási kérelem rögzítve van egy fürthöz, ha van egy ingyenes erőforrások megkereséséhez, mivel a rendelkezésre álló erőforráskészletet korlátozódik, a fürt hibás magasabb esélyét. Továbbá ha a foglalási kérelem rögzítve van egy fürthöz, de a fürt nem támogatja a kért erőforrás típusát, a kérelem sikertelen lesz akkor is, ha a fürt rendelkezik ingyenes erőforrás. 3. ábra alább az esetet, ahol egy rögzített felosztás meghiúsul, mert az csak jelölt fürtnek nincs szabad erőforrás mutatja be. 4. ábra mutatja be az esetben, ahol egy rögzített felosztás meghiúsul, mert a csak jelölt fürt nem támogatja a kért Virtuálisgép-méret, annak ellenére, hogy a fürt rendelkezik ingyenes erőforrások.

![Rögzített foglalási hiba](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>A cloud services foglalási hibák elhárítása
### <a name="error-message"></a>Hibaüzenet
A következő hibaüzenet jelenhet meg:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Gyakori problémák
Az alábbiakban az általános foglalási forgatókönyveket, amelyek egy foglalási kérelemmel, egyetlen fürtben rögzíthetők.

* Helyeznek üzembe előkészítési pont – Ha egy felhőalapú szolgáltatás központi telepítés rendelkezik az egyik tárolóhely, majd a teljes felhőalapú szolgáltatás rögzítve van egy adott fürtben.  Ez azt jelenti, hogy ha egy üzemelő példány már létezik a termelési helyen, akkor egy új átmeneti üzemelő példányt csak ugyanabban a fürtben lehet lefoglalni, ahol a termelési hely is található. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.
* Skálázás – új példányokat hozzáadni egy meglévő felhőszolgáltatáshoz ugyanazon fürt kell lefoglalni.  A kisebb méretezési kérések általában lefoglalhatóak, de nem mindig. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.
* Affinitáscsoport - új üzemelő példányt egy üres felhőszolgáltatásban foglalhatók által a háló minden olyan fürt, az adott régióban, kivéve, ha a felhőszolgáltatás egy affinitáscsoporthoz van rögzítve. Központi telepítések számára ugyanabban az affinitáscsoportban kísérli meg a rendszer ugyanazon a fürtön. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.
* Affinitáscsoport vNet - régebbi virtuális hálózatok régiók helyett az affinitáscsoportok lett kötve, és ezeket a virtuális hálózatokat a felhőszolgáltatások lenne rögzíthetők az affinitáscsoport-fürtre. Ez a fajta virtuális hálózatot a központi telepítések meg fogja kísérelni a rögzített fürtön. Ha a fürt közelít kapacitásának határához, a kérelem sikertelen lehet.

## <a name="solutions"></a>Megoldások
1. Új felhőalapú szolgáltatás üzembe helyezése – Ez a megoldás valószínűleg a legsikeresebb alkot, mert a platform összes fürt régió közül választhat.

   * Egy új felhőszolgáltatást a számítási feladatok üzembe helyezése  
   * Forgalom átirányítása az új felhőszolgáltatást a CNAME vagy egy rekord frissítése
   * Miután nulla történik, a régi helyhez, törölheti a régi felhőszolgáltatás. Ez a megoldás állásidő nélkül kell vállalni.
2. Törölje a termelési és az előkészítési ponton – Ez a megoldás is megőrzi a meglévő DNS-név, de leállását, mivel az alkalmazás miatt.

   * A szolgáltatás üzemi és átmeneti tárhelyek egy létező felhőszolgáltatás úgy, hogy a felhőszolgáltatás üres, törölje, majd
   * Hozzon létre egy új központi telepítést a meglévő felhőszolgáltatásra. Ez újra megkísérli a foglalási minden fürtön a régióban. Győződjön meg arról, a felhőszolgáltatás nem kötődik azonban át egy affinitáscsoportba.
3. Fenntartott IP - Ez a megoldás is megőrzi a meglévő IP-cím, de leállását, mivel az alkalmazás miatt.  

   * Hozzon létre egy fenntartott IP-cím a meglévő telepítés Powershell-lel

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * #2 parancssorai ügyelve arra, hogy a szolgáltatás CSCFG adja meg az új fenntartott IP-cím, hajtsa végre.
4. Távolítsa el az új központi telepítéseknél - affinitáscsoport Affinitáscsoportok már nem javasolt. # 1 telepíthet egy új felhőszolgáltatást a fenti lépésekkel. Győződjön meg arról, a felhőalapú szolgáltatás nem szerepel az affinitáscsoportot.
5. Regionális virtuális hálózat – lásd: átalakítása [hogyan kell áttelepíteni egy regionális virtuális hálózat (VNet) az Affinitáscsoportok](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
