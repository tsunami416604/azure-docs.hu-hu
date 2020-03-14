---
title: Felhőalapú szolgáltatás foglalási hibáinak elhárítása | Microsoft Docs
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247539"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Hozzárendelési hibák elhárítása a Cloud Services telepítése során az Azure szolgáltatásban
## <a name="summary"></a>Összegzés
Ha példányokat helyez üzembe egy felhőalapú szolgáltatásban, vagy új webes vagy feldolgozói szerepkört ad hozzá, a Microsoft Azure kiosztja a számítási erőforrásokat. Időnként előfordulhat, hogy az Azure-előfizetések korlátainak megkezdése előtt ezeket a műveleteket is elvégezheti. Ez a cikk ismerteti a gyakori lefoglalási hibák okait, és javaslatot tesz a lehetséges szervizelésre. Az információk akkor is hasznosak lehetnek, ha a szolgáltatások üzembe helyezését tervezi.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Háttér – a foglalás működése
Az Azure-adatközpontokban lévő kiszolgálók fürtökre vannak particionálva. Egy új felhőalapú szolgáltatás foglalási kérelme több fürtön is próbálkozik. Ha az első példány üzembe helyezése egy felhőalapú szolgáltatásban (átmeneti vagy éles üzemben) történik, a felhőalapú szolgáltatás egy fürthöz lesz rögzítve. A felhőalapú szolgáltatás további telepítései ugyanazon a fürtön történnek. Ebben a cikkben a következőképpen fogunk hivatkozni: "fürtbe rögzítve". Az alábbi ábra egy normál kiosztást mutat be, amely több fürtön próbálkozik. A 2. diagram azt szemlélteti, hogy a 2. fürthöz rögzített foglalások esetében hol található a meglévő Cloud Service CS_1.

![Foglalási diagram](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Miért történik a foglalási hiba
Ha egy foglalási kérelem egy fürtre van rögzítve, nagyobb eséllyel nem talál szabad erőforrásokat, mivel a rendelkezésre álló erőforráskészlet egy fürtre korlátozódik. Továbbá, ha a foglalási kérelem egy fürthöz van rögzítve, de a kért erőforrás típusa nem támogatott az adott fürtben, a kérés sikertelen lesz, még akkor is, ha a fürt rendelkezik szabad erőforrással. Az alábbi ábra szemlélteti azt az esetet, amikor egy rögzített foglalás meghiúsul, mert az egyetlen jelölt fürtnek nincs szabad erőforrása. A 4. ábra azt szemlélteti, hogy a rögzített foglalás meghiúsul, mert az egyetlen jelölt fürt nem támogatja a kért virtuálisgép-méretet, annak ellenére, hogy a fürt rendelkezik szabad erőforrásokkal.

![Rögzített foglalási hiba](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>A Cloud Services foglalási hibáinak elhárítása
### <a name="error-message"></a>Hibaüzenet
A következő hibaüzenet jelenik meg:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Gyakori problémák
Az alábbi gyakori foglalási forgatókönyvek azt okozzák, hogy a foglalási kérelmek egyetlen fürthöz legyenek rögzítve.

* Üzembe helyezés átmeneti tárolóhelyre – ha egy felhőalapú szolgáltatásban van üzembe helyezés bármelyik tárolóhelyen, akkor a teljes felhőalapú szolgáltatás egy adott fürthöz van rögzítve.  Ez azt jelenti, hogy ha egy üzemelő példány már létezik a termelési helyen, akkor egy új átmeneti üzemelő példányt csak ugyanabban a fürtben lehet lefoglalni, ahol a termelési hely is található. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.
* Méretezés – az új példányok meglévő felhőalapú szolgáltatásba való felvételéhez ugyanabban a fürtben kell lefoglalni.  A kisebb méretezési kérések általában lefoglalhatóak, de nem mindig. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.
* Affinitási csoport – az üres felhőalapú szolgáltatás új üzembe helyezését a háló bármely, a régióban található fürtben kioszthatja, kivéve, ha a felhőalapú szolgáltatás egy affinitási csoportba van rögzítve. Ugyanazon a fürtön ugyanazon az affinitási csoporton üzemelő példányok fognak próbálkozni. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.
* Az affinitási csoport vNet – a régebbi virtuális hálózatok a régiók helyett affinitási csoportokhoz voltak kötve, és ezekben a virtuális hálózatokban található felhőalapú szolgáltatások az affinitási csoport fürtjébe lesznek rögzítve. Az ilyen típusú virtuális hálózatra irányuló központi telepítések a rögzített fürtön lesznek megkísérelve. Ha a fürt eléri a kapacitást, előfordulhat, hogy a kérelem meghiúsul.

## <a name="solutions"></a>Megoldások
1. Új felhőalapú szolgáltatás újbóli üzembe helyezése – ez a megoldás valószínűleg a legsikeresebb, mivel lehetővé teszi a platform számára, hogy az adott régióban lévő összes fürtből kiválassza a platformot.

   * A számítási feladatok üzembe helyezése egy új felhőalapú szolgáltatásban  
   * Frissítse a CNAME-t vagy egy rekordot az új felhőalapú szolgáltatás felé irányuló forgalom kimutatása érdekében
   * Ha nulla forgalom lesz a régi helyre, törölheti a régi felhőalapú szolgáltatást. Ennek a megoldásnak nulla állásidőt kell fizetnie.
2. Az éles és az előkészítési tárolóhelyek törlése – ez a megoldás megőrzi a meglévő DNS-nevet, de az alkalmazáshoz való állásidőt is okozhatja.

   * Törölheti egy meglévő felhőalapú szolgáltatás éles és átmeneti tárolóhelyeit, hogy a felhőalapú szolgáltatás üres legyen, majd
   * Hozzon létre egy új központi telepítést a meglévő felhőalapú szolgáltatásban. Ez a művelet újrapróbálkozik a régió összes fürtjén való kiosztással. Győződjön meg arról, hogy a felhőalapú szolgáltatás nem kötődik affinitási csoporthoz.
3. Fenntartott IP – ez a megoldás megőrzi a meglévő IP-címét, de az alkalmazás leállását okozhatja.  

   * Foglalt IP létrehozása meglévő telepítéshez a PowerShell használatával

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Kövesse #2 a fentiekben leírtak szerint, és győződjön meg arról, hogy az új foglalt IP meg van határozva a szolgáltatás CSCFG.
4. Az új központi telepítések affinitási csoportjának eltávolítása – az affinitási csoportok már nem ajánlottak. Új felhőszolgáltatás üzembe helyezéséhez kövesse a fenti 1-es pont lépéseit. Győződjön meg arról, hogy a felhőalapú szolgáltatás nem egy affinitási csoportban van.
5. Átalakítás regionális Virtual Networkra – lásd: az [áttelepítést az affinitási csoportokból egy regionális Virtual Networkra (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
