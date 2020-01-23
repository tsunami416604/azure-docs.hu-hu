---
title: Gyakran ismételt kérdések (GYIK) az Azure Network Watcherról | Microsoft Docs
description: Ez a cikk az Azure Network Watcher szolgáltatással kapcsolatos gyakori kérdésekre ad választ.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: 856c249b72e9e0ff8667d10821ad14b3432b0775
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509189"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Gyakran ismételt kérdések (GYIK) az Azure Network Watcher
Az [azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) szolgáltatás olyan eszközöket biztosít, amelyekkel figyelheti, diagnosztizálhatja és megtekintheti a metrikákat, valamint engedélyezheti vagy letilthatja az Azure-beli virtuális hálózatok erőforrásaihoz tartozó naplókat. Ez a cikk a szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

### <a name="what-is-network-watcher"></a>Mi az a Network Watcher?
Network Watcher úgy van kialakítva, hogy figyelje és javítsa a IaaS (infrastruktúra-szolgáltatás) összetevőinek hálózati állapotát, beleértve a Virtual Machines, a virtuális hálózatokat, az alkalmazás-átjárókat, a terheléselosztó és az Azure-beli virtuális hálózatok egyéb erőforrásait. Nem megoldás a Péter (platform-szolgáltatás) infrastruktúrájának figyelésére, vagy a web/mobil Analitika beszerzésére.

### <a name="what-tools-does-network-watcher-provide"></a>Milyen eszközöket biztosít a Network Watcher?
Network Watcher a képességek három fő készletét biztosítja
* Monitoring
  * A [topológia nézetben](https://docs.microsoft.com/azure/network-watcher/view-network-topology) a virtuális hálózat erőforrásai és a köztük lévő kapcsolatok láthatók.
  * A [kapcsolat figyelője](https://docs.microsoft.com/azure/network-watcher/connection-monitor) lehetővé teszi a virtuális gép és egy másik hálózati erőforrás közötti kapcsolat és késés figyelését.
  * A [Network Performance monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) lehetővé teszi a kapcsolatok és a késések figyelését a hibrid hálózati architektúrák, a Expressroute áramkörök és a szolgáltatási/alkalmazási végpontok között.  
* Diagnosztika
  * Az [IP-forgalom ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) lehetővé teszi, hogy a virtuális gép szintjén észlelje a forgalom szűrésével kapcsolatos problémákat.
  * A [következő ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) segítséget nyújt a forgalmi útvonalak ellenőrzésében és az útválasztási problémák észlelésében.
  * A [kapcsolati hibakeresés](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) lehetővé teszi a virtuális gép és egy másik hálózati erőforrás közötti egyszeri kapcsolódást és késést.
  * A [csomagok rögzítése](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) lehetővé teszi a virtuális hálózatban lévő virtuális gépek forgalmának rögzítését.
  * A [VPN-hibaelhárítás](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) több diagnosztikai ellenőrzést futtat a VPN-átjárón és a kapcsolatokon a hibák elhárítása érdekében.
* Naplózás
  * A [NSG flow-naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) lehetővé teszik a [hálózati biztonsági csoportok (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) összes forgalmának naplózását
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) dolgozza fel a NSG flow-napló adatait, így megjelenítheti, lekérdezheti, elemezheti és értelmezheti a hálózati forgalmat.


További részletekért tekintse meg az [Network Watcher áttekintése lapot](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).


### <a name="how-does-network-watcher-pricing-work"></a>Hogyan működik a Network Watcher díjszabása?
Látogasson el a Network Watcher-összetevők [díjszabási oldalára](https://azure.microsoft.com/pricing/details/network-watcher/) , valamint azok díjszabására.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Mely régiók Network Watcher támogatott/elérhető?
Az [Azure szolgáltatás rendelkezésre állási lapján](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) megtekintheti a legújabb regionális elérhetőséget.

### <a name="what-are-resource-limits-on-network-watcher"></a>Mik a Network Watcher erőforrás-korlátai?
Tekintse meg az összes korlátot a [szolgáltatás korlátai](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) lapon.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Miért csak egyetlen példányban engedélyezett Network Watcher régiónként?
Network Watcher csak egyszer kell engedélyezni ahhoz, hogy az előfizetése működjön, ez a szolgáltatás nem megengedett.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Miért kell telepíteni a Network Watcher bővítményt? 
A Network Watcher-bővítményt minden olyan szolgáltatáshoz meg kell adni, amely egy virtuális gépről érkező forgalmat kell előállítani vagy feltartóztatni. 

### <a name="which-features-require-the-network-watcher-extension"></a>Mely szolgáltatásokhoz szükséges a Network Watcher bővítmény?
Csak a csomagok rögzítése, a kapcsolati hibák és a kapcsolat figyelője szükséges a Network Watcher kiterjesztésének megjelenni.

## <a name="nsg-flow-logs"></a>NSG

### <a name="what-does-nsg-flow-logs-do"></a>Mit jelentenek a NSG flow-naplók?
Az Azure hálózati erőforrásait [hálózati biztonsági csoportok (NSG-EK)](https://docs.microsoft.com/azure/virtual-network/security-overview)segítségével lehet egyesíteni és felügyelni. A NSG flow-naplók lehetővé teszik az 5 rekordos adatfolyamok naplózását a NSG keresztüli összes forgalomról. A nyers flow-naplók egy Azure Storage-fiókba íródnak, ahonnan szükség szerint további feldolgozásra, elemzésre, lekérdezésre vagy exportálásra kerülhet sor.

### <a name="how-do-i-use-nsg-flow-logs-on-a-storage-account-with-a-firewall"></a>Hogyan használja a NSG flow-naplókat egy tűzfallal rendelkező Storage-fiókhoz?

Ha tűzfallal szeretné használni a Storage-fiókot, meg kell adnia egy kivételt a megbízható Microsoft-szolgáltatások számára a Storage-fiókhoz való hozzáféréshez:

* Navigáljon a Storage-fiókhoz úgy, hogy beírja a Storage-fiók nevét a portálon vagy a [Storage-fiókok lapon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) található globális Keresés mezőbe.
* A **BEÁLLÍTÁSOK** szakaszban válassza a **Tűzfalak és virtuális hálózatok** elemet
* A "hozzáférés engedélyezése innen" területen válassza a **kiválasztott hálózatok**elemet. Ezután a **kivételek**alatt jelölje be a **"megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz"** jelölőnégyzetet. 
* Ha ez a beállítás már ki lett választva, nincs szükség módosításra.  
* Keresse meg a cél NSG a [NSG flow naplói – áttekintés oldalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) , és engedélyezze a NSG-folyamat naplóit a fenti Storage-fiók kiválasztásával.

Pár perc elteltével ellenőrizheti a tárnaplókat – egy frissített időbélyeget vagy egy újonnan létrehozott JSON-fájlt kell látnia.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints-for-storage"></a>Hogyan a NSG-flow-naplókat a tárolási végpontokkal együtt használni?

Tekintse [meg a szolgáltatási végpontok engedélyezésével foglalkozó oktatóanyagot](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint). 


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Mi a különbség a flow-naplók között 1 & 2 verzió között?
A flow-naplók 2. verziója bevezeti a *folyamat állapotának* fogalmát & tárolja a bájtok és a továbbított csomagok adatait. [További információk](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Következő lépések
 - Néhány oktatóanyagért tekintse át a [dokumentáció áttekintését ismertető oldalt](https://docs.microsoft.com/azure/network-watcher/) , amellyel elsajátíthatja a Network Watcher.
