---
title: "ExpressRoute-kapcsolatcsoportok módosítása klasszikusról Resource Manager-alapúra | Microsoft Docs"
description: "Ez az oldal a klasszikus és a Resource Manager-alapú üzemi modellek áthidalásához szükséges ismeretek áttekintését tartalmazza."
documentationcenter: na
services: expressroute
author: ganesr
manager: carmonm
editor: 
ms.assetid: bdf01217-1a98-4ec0-a08e-d84fd37f78af
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: ganesr
ms.openlocfilehash: 7f8386b518ada850fc03e23c5cae3b159b3b213e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>ExpressRoute-kapcsolatcsoportok áthelyezése a klasszikusból a Resource Manager-alapú üzemi modellbe
Ez a cikk azt tekinti át, hogy az Azure ExpressRoute-kapcsolatcsoportok hogyan helyezhetők át a klasszikusból az Azure Resource Manager-alapú üzemi modellbe.

Egyetlen ExpressRoute-kapcsolatcsoporttal csatlakozhat a klasszikus és a Resource Manager-alapú üzemi modellekbe telepített virtuális hálózatokhoz. Az ExpressRoute-kapcsolatcsoportok mostantól a létrehozási módjuktól függetlenül képesek virtuális hálózatokhoz csatlakozni mindkét üzemi modellben.

![Mindkét üzemi modellben működő virtuális hálózatokhoz csatlakozó ExpressRoute-kapcsolatcsoport](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>A klasszikus üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok
A klasszikus és a Resource Manager-alapú üzemi modellek csatlakozásának engedélyezéséhez a klasszikus üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportokat először át kell helyezni a Resource Manager-alapú üzemi modellbe. A kapcsolatok áthelyezésekor a kapcsolat nem veszik el, illetve nem szakad meg. A kapcsolatcsoportok és virtuális hálózatok között a klasszikus üzemi modellben meglévő összes kapcsolat megmarad (ugyanabban az előfizetésben és az előfizetések között is).

Az áthelyezés sikeres végrehajtása után az ExpressRoute-kapcsolatcsoport megjelenése és működése ugyanolyan lesz, mint egy Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoporté. Ezek után a Resource Manager-alapú üzemi modellben létrehozhatja a kapcsolatokat a virtuális hálózatokhoz.

Miután egy ExpressRoute-kapcsolatcsoportot áthelyezett a Resource Manager-alapú üzemi modellbe, az ExpressRoute-kapcsolatcsoport életciklusát csak a Resource Manager-alapú üzemi modell segítségével kezelheti. Ez azt jelenti, hogy az olyan műveletek, mint a társviszonyok hozzáadása/frissítése/törlése, a kapcsolatcsoport tulajdonságainak (például a sávszélesség, termékváltozat és számlázási típus) frissítése, valamint a kapcsolatcsoportok törlése, csak a Resource Manager-alapú üzemi modellben végezhetők el. A hozzáférés mindkét üzemi modellben történő kezelésének további részleteiért olvassa el a Resource Manager-alapú üzemi modellben létrehozott kapcsolatcsoportokkal kapcsolatos alábbi szakaszokat.

A kapcsolat szolgáltatóját nem kell bevonnia az áthelyezésbe.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>A Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok
Engedélyezheti, hogy a Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok mindkét üzemi modellből elérhetők legyenek. Az előfizetésben lévő bármely ExpressRoute-kapcsolatcsoport esetében engedélyezhető, hogy mindkét üzemi modellből elérhető legyen.

* A Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoportok alapértelmezés szerint nem rendelkeznek hozzáféréssel a klasszikus üzemi modellhez.
* A klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe áthelyezett ExpressRoute-kapcsolatcsoportok alapértelmezés szerint mindkét üzemi modellből elérhetők.
* Az ExpressRoute-kapcsolatcsoportok mindig hozzáférhetnek a Resource Manager-alapú üzemi modellhez, függetlenül attól, hogy a Resource Manager-alapú vagy a klasszikus üzemi modellben jöttek létre. Ez azt jelenti, hogy a [csatlakozás virtuális hálózatokhoz](expressroute-howto-linkvnet-arm.md) című szakasz utasításai alapján létrehozhat kapcsolatokat virtuális hálózatokhoz a Resource Manager-alapú üzemi modellben.
* A klasszikus üzemi modellhez való hozzáférést az ExpressRoute-kapcsolatcsoport **allowClassicOperations** paramétere vezérli.

> [!IMPORTANT]
> A [szolgáltatásokra vonatkozó korlátozások](../azure-subscription-service-limits.md) oldalon ismertetett összes kvóta érvényben van. Egy szabványos kapcsolatcsoport például legfeljebb 10 virtuális hálózati kapcsolattal/csatlakozással rendelkezhet a klasszikus és a Resource Manager-alapú üzemi modellekben.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>A klasszikus üzemi modellekhez való hozzáférés vezérlése
Az ExpressRoute-kapcsolatcsoport **allowClassicOperations** paraméterének beállításával engedélyezheti egyetlen ExpressRoute-kapcsolatcsoport számára, hogy mindkét üzemi modellben csatlakozzon virtuális hálózatokhoz.

Az **allowClassicOperations** paraméter TRUE értékre állítása lehetővé teszi, hogy mindkét üzemi modellből csatlakoztasson virtuális hálózatokat az ExpressRoute-kapcsolatcsoporthoz. A [virtuális hálózatok a klasszikus üzemi modellben való csatlakoztatását](expressroute-howto-linkvnet-classic.md) ismertető témakör utasításai szerint csatlakozhat virtuális hálózatokhoz a klasszikus üzemi modellben. A [virtuális hálózatok Resource Manager-alapú üzemi modellben való csatlakoztatását](expressroute-howto-linkvnet-arm.md) ismertető témakör utasításai szerint csatlakozhat virtuális hálózatokhoz a Resource Manager-alapú üzemi modellben.

Ha az **allowClassicOperations** paramétert FALSE értékre állítja, azzal blokkolhatja a kapcsolatcsoport elérését a klasszikus üzemi modellből. A virtuális hálózatok klasszikus üzemi modellben meglévő összes kapcsolata azonban megmarad. Ebben az esetben az ExpressRoute-kapcsolatcsoport nem látható a klasszikus üzemi modellben.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Támogatott műveletek a klasszikus üzemi modellben
Az ExpressRoute-kapcsolatcsoportokon a következő klasszikus műveletek támogatottak, ha az **allowClassicOperations** TRUE értékre van állítva:

* Az ExpressRoute-kapcsolatcsoport információinak lekérése
* Virtuális hálózati kapcsolatok létrehozása/frissítése/lekérése/törlése klasszikus virtuális hálózatokkal
* Virtuális hálózati kapcsolatok hitelesítéseinek létrehozása/frissítése/lekérése/törlése előfizetések közötti kapcsolatokhoz

A következő műveleteket nem hajthatja végre, ha az **allowClassicOperations** TRUE értékre van állítva:

* Border Gateway Protocol- (BGP-) társviszonyok létrehozása/frissítése/lekérése/törlése Azure privát, Azure nyilvános és Microsoft társviszony-létesítéshez
* ExpressRoute-kapcsolatcsoportok törlése

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Kommunikáció a klasszikus és a Resource Manager-alapú üzemi modellek között
Az ExpressRoute-kapcsolatcsoport hídként működik a klasszikus és a Resource Manager-alapú üzemi modellek között A klasszikus üzemi modellben lévő virtuális hálózatok virtuális gépei és a Resource Manager-alapú üzemi modellben lévő virtuális hálózatok közötti forgalom keresztülhalad az ExpressRoute-on, ha mindkét virtuális hálózat ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz csatlakozik.

Az összesített átviteli sebességet a virtuális hálózati átjáró átviteli kapacitása korlátozza. Ilyen esetekben a forgalom nem lép be a kapcsolat szolgáltatójának hálózataiba vagy az Ön hálózataiba. A virtuális hálózatok közötti forgalom teljes mértékben a Microsoft hálózatán keresztül halad át.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Azure nyilvános és Microsoft társviszony-létesítési erőforrások elérése
Továbbra is zavartalanul elérheti a jellemzően Azure nyilvános társviszonyon és Microsoft-társviszonyon keresztül elérhető erőforrásokat.  

## <a name="whats-supported"></a>Támogatott műveletek
Ez a szakasz az ExpressRoute-kapcsolatcsoportok esetében támogatott műveleteket ismerteti:

* Egyetlen ExpressRoute-kapcsolatcsoportot használhat a klasszikus és a Resource Manager-alapú üzemi modellekbe telepített virtuális hálózatok eléréséhez.
* Áthelyezhet egy ExpressRoute-kapcsolatcsoportot a klasszikusból a Resource Manager-alapú üzemi modellbe. Az áthelyezés után az ExpressRoute-kapcsolatcsoport megjelenése és működése ugyanolyan lesz, mint bármely más, a Resource Manager-alapú üzemi modellben létrehozott ExpressRoute-kapcsolatcsoporté.
* Csak az ExpressRoute-kapcsolatcsoportot tudja áthelyezni. A kapcsolatcsoportok kapcsolatai, a virtuális hálózatok és a VPN-átjárók nem helyezhetők át ezzel a művelettel.
* Miután egy ExpressRoute-kapcsolatcsoportot áthelyezett a Resource Manager-alapú üzemi modellbe, az ExpressRoute-kapcsolatcsoport életciklusát csak a Resource Manager-alapú üzemi modell segítségével kezelheti. Ez azt jelenti, hogy az olyan műveletek, mint a társviszonyok hozzáadása/frissítése/törlése, a kapcsolatcsoport tulajdonságainak (például a sávszélesség, termékváltozat és számlázási típus) frissítése, valamint a kapcsolatcsoportok törlése, csak a Resource Manager-alapú üzemi modellben végezhetők el.
* Az ExpressRoute-kapcsolatcsoport hídként működik a klasszikus és a Resource Manager-alapú üzemi modellek között A klasszikus üzemi modellben lévő virtuális hálózatok virtuális gépei és a Resource Manager-alapú üzemi modellben lévő virtuális hálózatok közötti forgalom keresztülhalad az ExpressRoute-on, ha mindkét virtuális hálózat ugyanahhoz az ExpressRoute-kapcsolatcsoporthoz csatlakozik.
* Az előfizetések közötti kapcsolat a klasszikus és a Resource Manager-alapú üzemi modellekben is támogatott.
* Miután áthelyezett egy ExpressRoute-kapcsolatcsoportot a klasszikusból az Azure Resource Manager-alapú modellbe, [áttelepítheti az ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatokat](expressroute-migration-classic-resource-manager.md) is.

## <a name="whats-not-supported"></a>Nem támogatott műveletek
Ez a szakasz az ExpressRoute-kapcsolatcsoportok esetében nem támogatott műveleteket ismerteti:

* ExpressRoute-kapcsolatcsoportok életciklusának kezelése a klasszikus üzemi modellből.
* Szerepköralapú hozzáférés-vezérlés (RBAC) támogatása a klasszikus üzemi modell esetében. A klasszikus üzemi modellben nem hajthat végre RBAC-vezérlési műveleteket a kapcsolatcsoportokon. Az előfizetés bármelyik rendszergazdája/társrendszergazdája csatlakoztathat virtuális hálózatokat a kapcsolatcsoporthoz, illetve leválaszthatja azokat.

## <a name="configuration"></a>Konfiguráció
Kövesse az [ExpressRoute-kapcsolatcsoportok áthelyezése a klasszikusból a Resource Manager-alapú üzemi modellbe](expressroute-howto-move-arm.md) című szakaszban ismertetett utasításokat.

## <a name="next-steps"></a>Következő lépések
* [ExpressRoute-kapcsolatcsoporthoz kapcsolt virtuális hálózatok áttelepítése a klasszikusból az Azure Resource Manager-alapú modellbe](expressroute-migration-classic-resource-manager.md)
* További információkért lásd: [ExpressRoute-kapcsolatcsoportok kiépítési munkafolyamatai és kapcsolatcsoport-állapotok](expressroute-workflows.md).
* Az ExpressRoute-kapcsolat konfigurálása:
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-arm.md)
  * [Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-arm.md)

