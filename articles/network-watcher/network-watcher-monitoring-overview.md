---
title: Bevezetés az Azure hálózati figyelőt |} Microsoft Docs
description: Ezen a lapon a hálózati figyelőt szolgáltatás Figyelés áttekintése és hálózati megjelenítése kapcsolódó erőforrások az Azure-ban
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: 792b96e4f5ba5dc0f2f943f099a2fee339407d66
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-network-monitoring-overview"></a>Azure-hálózat – áttekintés

Az ügyfelek egy Azure-végpontok közötti hálózati koordinálása, és különböző egyes hálózati erőforrások, például virtuális hálózaton, ExpressRoute, Alkalmazásátjáró, terheléselosztók, és több összeállítása hozhat létre. Figyelési érhető el minden olyan a hálózati erőforrásokhoz. Tekintse meg a "figyelés" erőforrás-szintű figyelése.

A végpontok közötti hálózati összetett konfigurációk és erőforrások létrehozása összetett forgatókönyvek esetében, forgatókönyv-alapú hálózati figyelőt figyeléshez igénylő közötti interakció rendelkezhet.

Ez a cikk ismerteti a forgatókönyv és erőforrás-szintű figyelése. Hálózatfigyelés az Azure-ban átfogó, és ismerteti a két kategóriába sorolhatók:

* [**Hálózati figyelő** ](#network-watcher) -forgatókönyv-alapú figyelési által biztosított szolgáltatások a hálózati figyelőt. A szolgáltatás része a csomagrögzítéssel, a következő ugrás, az IP-adatfolyam győződjön meg arról, biztonsági csoport megtekintése, NSG folyamata naplókat. Forgatókönyv szintű figyelési jeleníti meg egy végpontok közötti hálózati erőforrások ellentétben egyes hálózati erőforrás-figyelése.
* [**Erőforrás-figyelés** ](#network-resource-level-monitoring) -erőforrás-szintű figyelés négy szolgáltatások diagnosztikai naplók, metrikák, hibaelhárítási és erőforrás állapota áll. Ezek a szolgáltatások a hálózati erőforrás szintjén épülnek.

## <a name="network-watcher"></a>Network Watcher

Hálózati figyelőt olyan regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása szintjén feltételek egy hálózati forgatókönyv, hogy, és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban.

Hálózati figyelőt jelenleg a következő képességekkel rendelkezik:

* **[Topológia](network-watcher-topology-overview.md)**  -a különböző csatlakozás és egy erőforráscsoportban található hálózati erőforrások egymáshoz rendelését hálózati szintű nézetét jeleníti meg.
* **[Változó csomagrögzítéssel](network-watcher-packet-capture-overview.md)**  -csomagadatok mindkét virtuális gép rögzíti. Speciális szűrési beállítások, például fel idő beállítása és korlátozások méretezés finomhangolható vezérlők meg a indít. A csomagadatok a blob-tárolóban, vagy a helyi lemezen .cap formátumban tárolható.
* **[IP-adatfolyam ellenőrizze](network-watcher-ip-flow-verify-overview.md)**  -ellenőrzést, ha egy csomag engedélyezett vagy megtagadott folyamata adatokat 5 rekordos csomag paraméterek (cél IP-címe, forrás IP-címe, Célport, Forrásport és protokoll) alapján. Ha a csomag megtagadta a biztonsági csoport, a szabály és a csoportot, amely a csomag megtagadva ad vissza.
* **[Következő Ugrás](network-watcher-next-hop-overview.md)**  -határozza meg a következő ugrás a csomagok továbbítása az Azure hálózati háló, így lehetővé teszi bármely diagnosztizálása nincs megfelelően konfigurálva a felhasználó által definiált útvonalak.
* **[Biztonsági csoport megtekintése](network-watcher-security-group-view-overview.md)**  -a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép lekérdezi.
* **[NSG Flow naplózási](network-watcher-nsg-flow-logging-overview.md)**  -folyamat naplókat a hálózati biztonsági csoportok lehetővé teszik a engedélyezett vagy megtagadott a csoport biztonsági szabályai forgalmi naplók rögzítése. A folyamat egy 5 rekordos információkat – a forrás IP-cím, a cél IP-cím, a forrásport, a célport és a protokoll határozzák meg.
* **[Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](network-watcher-troubleshoot-manage-rest.md)**  -lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibáinak elhárítása.
* **[Előfizetési korlátozásait a hálózati](#network-subscription-limits)**  -lehetővé teszi a hálózati erőforrás-használati korlátozások megtekintéséhez.
* **[Diagnosztikai naplófájl konfigurálása](#diagnostic-logs)**  – engedélyezheti vagy tilthatja le a diagnosztikai naplókat a hálózati erőforrások az erőforráscsoportban egytáblás biztosít.
* **[Kapcsolat elhárítása](network-watcher-connectivity-overview.md)**  -egy közvetlen TCP-kapcsolatot a virtuális gép egy adott végpont Azure környezetben növelést létrehozásának lehetősége ellenőrzi.
* **[Figyelő kapcsolat](connection-monitor.md)**  -figyelése Azure virtuális gép és egy IP-címe, a forrás és cél IP-cím és port használata között késleltetés és a konfigurációs problémákat.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Szerepköralapú hozzáférés-vezérlés (RBAC) hálózati figyelőt

Hálózati figyelő használja a [átruházásához hozzáférés-vezérlés (RBAC) modell](../active-directory/role-based-access-control-what-is.md). A következő engedélyek szükségesek a hálózati figyelőt. Fontos, győződjön meg arról, hogy a használt hálózati figyelő API-k kezdeményezése vagy hálózati figyelőt használ a portálról szerepkör rendelkezik-e megfelelő hozzáféréssel.

|Erőforrás| Engedély|
|---|---| 
|Microsoft.Storage/ |Olvasás|
|Microsoft.Authorization/| Olvasás| 
|Microsoft.Resources/subscriptions/resourceGroups/| Olvasás|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Műveletek|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Műveletek|
|Microsoft.Storage/storageAccounts/listKeys/ | Műveletek|
|Microsoft.Compute/virtualMachines/ |Olvasás|
|Microsoft.Compute/virtualMachines/ |Írás|
|Microsoft.Compute/virtualMachineScaleSets/ |Olvasás|
|Microsoft.Compute/virtualMachineScaleSets/ |Írás|
|Microsoft.Network/networkWatchers/packetCaptures/ |Olvasás|
|Microsoft.Network/networkWatchers/packetCaptures/| Írás|
|Microsoft.Network/networkWatchers/packetCaptures/| Törlés|
|Microsoft.Network/networkWatchers/ |Írás |
|Microsoft.Network/networkWatchers/| Olvasás |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Hálózati előfizetési korlátozásait

Hálózati előfizetési korlátozásait a hálózati erőforráshoz, a rendelkezésre álló erőforrások maximális számának elleni régióban előfizetés használatának részleteit biztosít.

![hálózati előfizetési korlátját][nsl]

## <a name="network-resource-level-monitoring"></a>Hálózati erőforrás szolgáltatásiszint-figyelés

A következő funkciók érhetők el erőforrás-szintű figyelése:

### <a name="audit-log"></a>Napló

A hálózatok konfiguráció részeként műveleteket a rendszer naplózza. Ezek a naplók megtekinthetők az Azure portálon, vagy visszavonni a Microsoft eszközök, például a Power BI és a külső eszközök használatával. Naplókat a portálon, a PowerShell, a CLI és a Rest API-n keresztül érhetők el. A naplókat további információkért lásd: [naplózási műveletek a Resource Manager](../resource-group-audit.md)

Naplók az összes hálózati erőforrás végzett műveletek érhetők el.

### <a name="metrics"></a>Mérőszámok

Adatok gyűjtése le TELJESÍTMÉNYMÉRÉSEK és egy meghatározott időtartamra vonatkozóan gyűjtött adatait. Adatok gyűjtése le jelenleg elérhető az Alkalmazásátjáró. Mérőszámok segítségével aktiváltak riasztásokat küszöbérték alapján. Lásd: [átjáró Alkalmazásdiagnosztika](../application-gateway/application-gateway-diagnostics.md) megtekintéséhez, hogyan metrikák értesítések létrehozására használható.

![metrikák megtekintése][metrics]

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Rendszeres és önkéntes események hálózati erőforrások által létrehozott, és a storage-fiókok, egy Eseményközpontba vagy Naplóelemzési küldött bejelentkezve. Ezek a naplók erőforrás állapotának betekintést. Ezek a naplók eszközöket, például a Power BI és a Naplóelemzési tekintheti meg. Diagnosztikai naplók megtekintése megismeréséhez látogasson el [Naplóelemzési](../log-analytics/log-analytics-azure-networking-analytics.md).

Diagnosztikai naplók érhetők el [terheléselosztó](../load-balancer/load-balancer-monitor-log.md), [hálózati biztonsági csoportok](../virtual-network/virtual-network-nsg-manage-log.md), útvonalakat, és [Alkalmazásátjáró](../application-gateway/application-gateway-diagnostics.md).

Hálózati figyelőt biztosít a diagnosztikai naplók megtekintése. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezése, és tiltsa le a hálózati erőforrások egyszerűen és gyorsan.

![naplók][logs]

### <a name="troubleshooting"></a>Hibaelhárítás

A hibaelhárítási panelen, a portál élményt biztosítja a hálózati erőforrásokat ma egyedi erőforrás társított gyakori problémák diagnosztizálásához. A forgatókönyvben a következő hálózati erőforrások - ExpressRoute, VPN-átjáró, Alkalmazásátjáró, hálózati biztonsági naplókat, útvonalakat, DNS, Load Balancer és Traffic Manager érhető el. Erőforrás szintű hibaelhárítással kapcsolatos további tudnivalókért látogasson el a [Azure hibaelhárítási diagnosztizálása és megoldása problémái](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)

![hibaelhárítási információ][TS]

### <a name="resource-health"></a>Erőforrás állapota

A hálózati erőforrás állapotának rendszeres időközönként valósul meg. Ilyen erőforrások többek között a VPN-átjáró és a VPN-alagúton. Erőforrás állapota a következő helyen érhető el az Azure portálon. Erőforrás állapota kapcsolatos további információkért látogasson el a [erőforrás állapotának áttekintése](../resource-health/resource-health-overview.md)

## <a name="next-steps"></a>További lépések

Hálózati figyelőt megismerését követően megtanulhatja:

Látogasson el a virtuális gépen a csomagrögzítéssel tegye [változó csomagrögzítéssel az Azure-portálon](network-watcher-packet-capture-manage-portal.md)

Hajtsa végre a proaktív figyeléshez és diagnosztika használatával [figyelmeztetés csomagrögzítéssel](network-watcher-alert-triggered-packet-capture.md).

A biztonsági rések észlelése [elemzése a Wireshark csomagrögzítéssel](network-watcher-deep-packet-inspection.md), nyílt forráskódú eszközökkel.

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











