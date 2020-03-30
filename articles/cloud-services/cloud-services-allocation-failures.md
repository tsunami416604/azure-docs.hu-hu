---
title: A felhőszolgáltatás foglalási hibájának hibaelhárítása | Microsoft dokumentumok
description: Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 470778e5c441bb05ffc7c5e1c5ef97a6c30d3359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247539"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban
## <a name="summary"></a>Összefoglalás
Amikor példányokat telepít egy felhőszolgáltatásba, vagy új webes vagy feldolgozói szerepkör-példányokat ad hozzá, a Microsoft Azure számítási erőforrásokat oszt ki. Előfordulhat, hogy a műveletek végrehajtásakor időnként hibaüzenetet kaphat, még az Azure-előfizetési korlátok elérése előtt. Ez a cikk ismerteti a gyakori felosztási hibák okait, és lehetséges szervizelést javasol. Az információk akkor is hasznosak lehetnek, ha a szolgáltatások telepítését tervezi.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Háttér – A felosztás működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Egy új felhőszolgáltatás-foglalási kérelmet kísérel meg több fürtben. Ha az első példány egy felhőszolgáltatásba van telepítve (átmeneti vagy éles környezetben), a felhőszolgáltatás egy fürthöz kerül. A felhőszolgáltatás további üzembe helyezései ugyanabban a fürtben történnek. Ebben a cikkben ezt "fürthöz rögzítve" hivatkozunk. Az alábbi 1. A 2. ábra a fürt 2-höz rögzített foglalás esetét mutatja be, mert a meglévő felhőszolgáltatás CS_1 a jelenlegi helyen található.

![Felosztási diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Miért történik foglalási hiba?
Ha egy foglalási kérelem van rögzítve egy fürthöz, nagyobb az esélye, hogy nem talál szabad erőforrásokat, mivel a rendelkezésre álló erőforráskészlet fürtre korlátozódik. Továbbá, ha a foglalási kérelem egy fürthöz van rögzítve, de a kért erőforrás típusát a fürt nem támogatja, a kérés sikertelen lesz, még akkor is, ha a fürt szabad erőforrással rendelkezik. Az alábbi 3. 4. ábra bemutatja az esetben, ha egy rögzített foglalás sikertelen, mert az egyetlen jelölt fürt nem támogatja a kért virtuális gép méretét, annak ellenére, hogy a fürt szabad erőforrásokkal rendelkezik.

![Rögzített foglalási hiba](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>A felhőszolgáltatások foglalási hibájának hibaelhárítása
### <a name="error-message"></a>Hibaüzenet
A következő hibaüzenet jelenhet meg:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Gyakori problémák
Az alábbiakban azokat a gyakori foglalási forgatókönyveket olvashatja, amelyek miatt a foglalási kérelmek egyetlen fürthöz kerülnek rögzítésre.

* Üzembe helyezés átmeneti tárolóhelyre – Ha egy felhőszolgáltatás rendelkezik egy központi telepítés mindkét tárolóhelyen, majd a teljes felhőszolgáltatás egy adott fürthöz van rögzítve.  Ez azt jelenti, hogy ha egy üzemelő példány már létezik a termelési helyen, akkor egy új átmeneti üzemelő példányt csak ugyanabban a fürtben lehet lefoglalni, ahol a termelési hely is található. Ha a fürt kapacitása közeledik, a kérés sikertelen lehet.
* Méretezés – Új példányok egy meglévő felhőszolgáltatáshoz való hozzáadása esetén az új példányokat ugyanabban a fürtben kell lefoglalni.  A kisebb méretezési kérések általában lefoglalhatóak, de nem mindig. Ha a fürt kapacitása közeledik, a kérés sikertelen lehet.
* Affinity Group – egy új üzembe helyezés egy üres felhőszolgáltatás hozrendelhető a háló bármely fürtaz adott régióban, kivéve, ha a felhőszolgáltatás van rögzítve egy affinitási csoport. Ugyanahhoz az affinitási csoporthoz történő központi telepítést kísérel meg a telepítés ugyanazon a fürtön. Ha a fürt kapacitása közeledik, a kérés sikertelen lehet.
* Affinitáscsoport virtuális hálózat – A régebbi virtuális hálózatok a régiók helyett affinitáscsoportokhoz voltak kötve, és ezekben a virtuális hálózatokban a felhőszolgáltatások az affinitáscsoport-fürthöz lesznek rögzítve. Az ilyen típusú virtuális hálózatra a telepített fürtön a telepítés megkísérli. Ha a fürt kapacitása közeledik, a kérés sikertelen lehet.

## <a name="solutions"></a>Megoldások
1. Újratelepítés egy új felhőszolgáltatásra – Ez a megoldás valószínűleg a legsikeresebb, mivel lehetővé teszi, hogy a platform az adott régió összes fürtje közül válasszon.

   * A számítási feladatok üzembe helyezése egy új felhőszolgáltatásba  
   * Frissítse a CNAME vagy A rekordot, hogy az új felhőszolgáltatásra irányítsa a forgalmat
   * Ha nulla forgalom megy a régi helyre, törölheti a régi felhőszolgáltatást. Ennek a megoldásnak nulla állásidőt kell kitennie.
2. Éles és átmeneti tárolóhelyek törlése – Ez a megoldás megőrzi a meglévő DNS-nevet, de leállást okoz az alkalmazásnak.

   * Egy meglévő felhőszolgáltatás éles és átmeneti bővítőhelyeinek törlése, hogy a felhőszolgáltatás üres legyen, majd
   * Hozzon létre egy új központi telepítést a meglévő felhőszolgáltatásban. Ez újra megpróbálja leosztani a régió összes fürtjeit. Győződjön meg arról, hogy a felhőszolgáltatás nincs affinitáscsoporthoz kötve.
3. Fenntartott IP - Ez a megoldás megőrzi a meglévő IP-címet, de leállást okoz az alkalmazásszámára.  

   * ReservedIP létrehozása a meglévő központi telepítéshez a PowerShell használatával

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Kövesse #2 felülről, ügyelve arra, hogy adja meg az új ReservedIP a szolgáltatás CSCFG.
4. Az új telepítések affinitáscsoportjának eltávolítása – az affinitáscsoportok már nem ajánlottak. Új felhőszolgáltatás üzembe helyezéséhez kövesse a fenti 1-es pont lépéseit. Győződjön meg arról, hogy a felhőszolgáltatás nem egy affinitási csoportban.
5. Konvertálás regionális virtuális hálózatra – Lásd: [Az Affinitáscsoportokból regionális virtuális hálózatba (VNet) való áttelepítése.](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)
