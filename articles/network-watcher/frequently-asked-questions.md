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
ms.openlocfilehash: 3305590f2d8abf0d894bc1df42b84edcc96a2b2d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598223"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Gyakran ismételt kérdések (GYIK) az Azure Network Watcher
Az [azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) szolgáltatás olyan eszközöket biztosít, amelyekkel figyelheti, diagnosztizálhatja és megtekintheti a metrikákat, valamint engedélyezheti vagy letilthatja az Azure-beli virtuális hálózatok erőforrásaihoz tartozó naplókat. Ez a cikk a szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános

### <a name="what-is-network-watcher"></a>Mi az Network Watcher?
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

### <a name="which-regions-is-network-watcher-available-in"></a>Mely régiókban Network Watcher érhető el?
Az [Azure szolgáltatás rendelkezésre állási lapján](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) megtekintheti a legújabb regionális elérhetőséget.

### <a name="what-are-resource-limits-on-network-watcher"></a>Mik a Network Watcher erőforrás-korlátai?
Tekintse meg az összes korlátot a [szolgáltatás korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits#network-watcher-limits) lapon.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Miért csak egyetlen példányban engedélyezett Network Watcher régiónként?
Network Watcher csak egyszer kell engedélyezni ahhoz, hogy az előfizetése működjön, ez a szolgáltatás nem megengedett.

## <a name="nsg-flow-logs"></a>NSG

### <a name="what-does-nsg-flow-logs-do"></a>Mit jelentenek a NSG flow-naplók?
Az Azure hálózati erőforrásait [hálózati biztonsági csoportok (NSG-EK)](https://docs.microsoft.com/azure/virtual-network/security-overview)segítségével lehet egyesíteni és felügyelni. A NSG flow-naplók lehetővé teszik az 5 rekordos adatfolyamok naplózását a NSG keresztüli összes forgalomról. A nyers flow-naplók egy Azure Storage-fiókba íródnak, ahonnan szükség szerint további feldolgozásra, elemzésre, lekérdezésre vagy exportálásra kerülhet sor.

### <a name="are-there-any-caveats-to-using-nsg-flow-logs"></a>Vannak kikötések a NSG flow-naplók használatára?
A NSG flow-naplók használatához nincsenek előfeltételek. Van azonban két korlátozás
- A **szolgáltatási végpontok nem lehetnek jelen a VNET**: a NSG-adatfolyamok a virtuális gépeken lévő ügynökökből vannak kibocsátva a Storage-fiókokba. Jelenleg azonban csak a naplókat lehet közvetlenül a Storage-fiókokba kibocsátani, és nem használhat a VNET hozzáadott szolgáltatási végpontot.

- A **Storage-fióknak nem szabad tűzfallal rendelkeznie**: a belső korlátozások miatt a Storage-fiókoknak elérhetőnek kell lenniük a nyilvános interneten keresztül a NSG-flow naplófájljainak használatához. A forgalom továbbra is az Azure-on keresztül lesz átirányítva, és nem fog megjelenni az extra kimenő költségek.

A problémák megoldásával kapcsolatos útmutatásért tekintse meg a következő két kérdést. A következő korlátozásokat a rendszer január 2020-én tárgyalja.

### <a name="how-do-i-use-nsg-flow-logs-with-service-endpoints"></a>Hogyan NSG-flow-naplókat használ a szolgáltatási végpontokkal?

*1. lehetőség: konfigurálja újra a NSG-folyamatok naplóit az Azure Storage-fiókba VNET-végpontok nélkül*

* Alhálózatok keresése végpontokkal:

    - Az Azure Portalon keressen rá az **erőforráscsoportok** kifejezésre a lap tetején található globális keresés használatával
    - Lépjen a használt NSG-t tartalmazó erőforráscsoporthoz
    - A második legördülő lista szerinti szűréshez és a **virtuális hálózatok** kiválasztásához használja a következőt:
    - Kattintson a szolgáltatásvégpontokat tartalmazó virtuális hálózatra
    - Válassza a **Szolgáltatásvégpontok** elemet a bal oldali ablaktábla **Beállítások** részén
    - Jegyezze fel azokat az alhálózatokat, amelyeken a **Microsoft.Storage** engedélyezve lett

* Szolgáltatási végpontok letiltása:

    - A fentiekben leírtakat folytatva válassza az **Alhálózatok** elemet a bal oldali ablaktábla **Beállítások** részén
    * Kattintson a szolgáltatásvégpontokat tartalmazó alhálózatra
    - A **Szolgáltatásvégpontok** szakasz **Szolgáltatások** részén törölje a **Microsoft.Storage** bejelölését

Pár perc elteltével ellenőrizheti a tárnaplókat – egy frissített időbélyeget vagy egy újonnan létrehozott JSON-fájlt kell látnia.

*2. lehetőség: a NSG-folyamatok naplófájljainak letiltása*

Ha a Microsoft.Storage szolgáltatásvégpontokat mindenképpen használni kell, le kell tiltania az NSG-forgalom naplóit.

### <a name="how-do-i-disable-the--firewall-on-my-storage-account"></a>Hogyan letiltani a tűzfalat a saját Storage-fiókomban?

A probléma megoldásához engedélyezze a "minden hálózat" lehetőséget a Storage-fiók eléréséhez:

* Keresse meg a tárfiók nevét. Ehhez keresse meg az NSG-t az [NSG-forgalom naplóinak áttekintési oldalán](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)
* Lépjen a tárfiókhoz a tárfiók nevét a portál globális keresési eszközébe beírva
* A **BEÁLLÍTÁSOK** szakaszban válassza a **Tűzfalak és virtuális hálózatok** elemet
* Válassza az **Összes hálózat** lehetőséget, és mentse a beállítást. Ha ez a beállítás már ki lett választva, nincs szükség módosításra.  

### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Mi a különbség a flow-naplók között 1 & 2 verzió között?
A flow-naplók 2. verziója bevezeti a *folyamat állapotának* fogalmát & tárolja a bájtok és a továbbított csomagok adatait. [További információk](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file).

## <a name="next-steps"></a>Következő lépések
 - Néhány oktatóanyagért tekintse át a [dokumentáció áttekintését ismertető oldalt](https://docs.microsoft.com/azure/network-watcher/) , amellyel elsajátíthatja a Network Watcher.
