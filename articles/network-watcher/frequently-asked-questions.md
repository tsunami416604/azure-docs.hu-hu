---
title: Gyakori kérdések az Azure Network Watcherről | Microsoft dokumentumok
description: Ez a cikk választ ad az Azure Network Watcher szolgáltatással kapcsolatos gyakori kérdésekre.
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
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471856"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Gyakori kérdések az Azure Network Watcherről
Az [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) szolgáltatás egy eszközcsomagot biztosít az Azure virtuális hálózat erőforrásainak figyeléséhez, diagnosztizálásához, metrikák megtekintéséhez és a naplók engedélyezéséhez vagy letiltásához. Ez a cikk a szolgáltatással kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános kérdések

### <a name="what-is-network-watcher"></a>Mi az a Network Watcher?
A Network Watcher az IaaS (Infrastructure-as-a-Service) összetevők hálózati állapotának figyelésére és javítására szolgál, beleértve a virtuális gépeket, virtuális hálózatokat, alkalmazásátjárókat, terheléselosztókat és az Azure virtuális hálózat egyéb erőforrásait. Nem megoldás a PaaS (Platform-as-a-Service) infrastruktúra figyelésére vagy web/mobil elemzés beszerzésére.

### <a name="what-tools-does-network-watcher-provide"></a>Milyen eszközöket biztosít a Network Watcher?
A Network Watcher három fő képességkészletet biztosít
* Figyelés
  * [A Topology nézet](https://docs.microsoft.com/azure/network-watcher/view-network-topology) a virtuális hálózat erőforrásait és a köztük lévő kapcsolatokat jeleníti meg.
  * [A Kapcsolatfigyelő](https://docs.microsoft.com/azure/network-watcher/connection-monitor) lehetővé teszi a virtuális gép és egy másik hálózati erőforrás közötti kapcsolat és késés figyelése.
  * [A hálózati teljesítményfigyelő](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) lehetővé teszi a kapcsolat és a késések figyelését a hibrid hálózati architektúrák, az Expressroute-áramkörök és a szolgáltatás/alkalmazás végpontok között.  
* Diagnosztika
  * [Az IP-folyamat ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) lehetővé teszi a forgalomszűrési problémák virtuális gép szinten történő észlelését.
  * [A Next Hop](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) segítségével ellenőrizheti a forgalmi útvonalakat, és észlelheti az útválasztási problémákat.
  * [A Kapcsolat hibaelhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal) lehetővé teszi az egyszeri kapcsolat és a késés ellenőrzését egy virtuális gép és egy másik hálózati erőforrás között.
  * [Packet Capture](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) lehetővé teszi, hogy rögzítse az összes forgalmat a virtuális gép a virtuális hálózatban.
  * [A VPN hibaelhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview) több diagnosztikai ellenőrzést futtat a VPN-átjárókon és kapcsolatokon a problémák hibakeresésének elősegítésére.
* Naplózás
  * [Az NSG-folyamatnaplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) lehetővé teszik a [hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/security-overview) összes forgalmának naplózását
  * [A Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) feldolgozza az NSG-folyamatnapló adatait, amelyek lehetővé teszik a hálózati forgalom megjelenítését, lekérdezését, elemzését és megértését.


További információt a [Hálózatfigyelő áttekintése lapon talál.](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)


### <a name="how-does-network-watcher-pricing-work"></a>Hogyan működik a Network Watcher árképzése?
Látogasson el a Network Watcher összetevőinek és azok díjszabásának [díjszabási oldalára.](https://azure.microsoft.com/pricing/details/network-watcher/)

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Mely régiókban támogatott/elérhető a Network Watcher?
A legfrissebb regionális elérhetőséget az [Azure-szolgáltatás rendelkezésre állási oldalán tekintheti meg.](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Milyen engedélyek szükségesek a Network Watcher használatához?
Tekintse meg a [Network Watcher használatához szükséges RBAC-engedélyek](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions)listáját. Az erőforrások üzembe helyezéséhez közreműködői engedélyekre van szükség a NetworkWatcherRG-hez (lásd alább).

### <a name="how-do-i-enable-network-watcher"></a>Hogyan engedélyezhetem a Network Watchert?
A Network Watcher szolgáltatás [automatikusan engedélyezve](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) van minden előfizetéshez.

### <a name="what-is-the-network-watcher-deployment-model"></a>Mi a Network Watcher telepítési modellje?
A Network Watcher szülőerőforrás minden régióban egyedi példányban van telepítve. Elnevezési formátum: NetworkWatcher_RegionName. Példa: NetworkWatcher_centralus az "USA középső régió" hálózati figyelőerőforrása.

### <a name="what-is-the-networkwatcherrg"></a>Mi az a NetworkWatcherRG?
Network Watcher források találhatók a rejtett **NetworkWatcherRG** erőforráscsoport, amely automatikusan létre. Például az NSG Flow Logs erőforrás a Network Watcher gyermekerőforrása, és engedélyezve van a NetworkWatcherRG-ben.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Miért kell telepítenem a Network Watcher bővítményt? 
A Network Watcher bővítmény szükséges minden olyan funkcióhoz, amelynek a virtuális gépről kell generálnia vagy elfognia a forgalmat. 

### <a name="which-features-require-the-network-watcher-extension"></a>Mely funkciókhoz szükséges a Network Watcher bővítmény?
A Csomagrögzítés, a kapcsolathiba elhárítása és a kapcsolatfigyelő szolgáltatásainak jelen kell lenniük a Hálózatfigyelő bővítményben.

### <a name="what-are-resource-limits-on-network-watcher"></a>Mik az erőforráskorlátok a Network Watcher számára?
Tekintse meg a [Szolgáltatás korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits) oldalon az összes korlátot.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>Miért csak egy példánya Network Watcher engedélyezett régiónként? 
A Network Watcher csak egyszer kell engedélyezni az előfizetéshez, hogy a funkciói működjenek, ez nem szolgáltatáskorlát.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Hogyan kezelhetem a Network Watcher erőforrást? 
A Network Watcher erőforrás a Network Watcher háttérszolgáltatását képviseli, és az Azure teljes körűen kezeli. Az ügyfeleknek nem kell kezelnie. Az olyan műveletek, mint az áthelyezés, nem támogatottak az erőforráson. Az erőforrás azonban [törölhető.](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal) 

## <a name="nsg-flow-logs"></a>NSG-folyamatnaplók

### <a name="what-does-nsg-flow-logs-do"></a>Mire történik az NSG-folyamatnaplók?
Az Azure hálózati erőforrásai kombinálhatók és [kezelhetők a hálózati biztonsági csoportokon (NSG-k)](https://docs.microsoft.com/azure/virtual-network/security-overview)keresztül. Az NSG flow naplók lehetővé teszik az NSG-ken keresztüli összes forgalom 5-toldalékos folyamatinformációinak naplózását. A nyers folyamatnaplók egy Azure Storage-fiókba kerülnek, ahonnan további feldolgozás, elemzés, lekérdezés vagy exportálás.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>Hogyan használhatom az NSG-folyamatnaplókat egy tűzfal mögötti tárfiókkal?

Ha tűzfal mögött szeretne tárfiókot használni, meg kell adnia egy kivételt a Megbízható Microsoft Services szolgáltatással a tárfiók eléréséhez:

* Keresse meg a tárfiókot a tárfiók nevének beírásával a portálon vagy a [Tárfiókok lapon](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) végzett globális keresésben.
* A **BEÁLLÍTÁSOK** szakaszban válassza a **Tűzfalak és virtuális hálózatok** elemet
* A "Hozzáférés engedélyezése innen" területen válassza ki a **Kijelölt hálózatokat**. Ezután a **Kivételek**területen jelölje be a **"Megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a tárfiókhoz" jelölőnégyzetet.** 
* Ha ez a beállítás már ki lett választva, nincs szükség módosításra.  
* Keresse meg a cél NSG-t az [NSG flow naplók áttekintése oldalon,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) és engedélyezze az NSG-folyamatnaplókat a fenti tárfiók kiválasztásával.

Pár perc elteltével ellenőrizheti a tárnaplókat – egy frissített időbélyeget vagy egy újonnan létrehozott JSON-fájlt kell látnia.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>Hogyan használhatom az NSG-folyamatnaplókat egy szolgáltatásvégpont mögötti tárfiókkal?

Az NSG-folyamatnaplók kompatibilisek a szolgáltatás-végpontokkal anélkül, hogy további konfigurációra lenne szükség. Tekintse meg a [szolgáltatásvégpontok engedélyezéséről a](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint) virtuális hálózatban.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>Mi a különbség az 1& 2-es folyamatnaplók verziói között?
A Flow Logs 2-es verziója bevezeti a *Flow State* & a továbbított bájtok és csomagok adatait tárolja. [Tovább](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)- tovább .

## <a name="next-steps"></a>Következő lépések
 - A Network Watcher segítségével ismerkedés a [dokumentációt áttekintő oldalunkon](https://docs.microsoft.com/azure/network-watcher/) talál néhány oktatóanyagot.
