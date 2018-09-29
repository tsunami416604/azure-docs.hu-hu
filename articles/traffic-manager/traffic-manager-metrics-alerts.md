---
title: Mérőszámok és riasztások az Azure Traffic Managerben |} A Microsoft Docs
description: Ez a cikk ismerteti a metrikák elérhető a Traffic Managerhez az Azure-ban.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: fe4cc3c06af6868396f5db6fd88804022f5f9793
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432477"
---
# <a name="traffic-manager-metrics-and-alerts"></a>A TRAFFIC Manager-mérőszámok és riasztások

A TRAFFIC Manager DNS-alapú terheléselosztási funkcióval, amely magában foglalja a több útválasztási módszerek és megfigyelési lehetőségek végpontot biztosít. Ez a cikk ismerteti a metrikák és a kapcsolódó riasztásokat, amelyek az ügyfelek számára érhető el. 

## <a name="metrics-available-in-traffic-manager"></a>A Traffic Managerben elérhető metrikák 

A TRAFFIC Manager profilonként ügyfelek számára, hogy a Traffic manager használatát és azok végpontjait, hogy a profilhoz állapotát is felhasználhatnak, amely az a következő metrikákat biztosítja.  

### <a name="queries-by-endpoint-returned"></a>Lekérdezések által visszaadott végpont
Használat [Ez a metrika](../monitoring-and-diagnostics/monitoring-supported-metrics.md) megtekintése egy meghatározott időtartamon belül a Traffic Manager-profil által feldolgozott lekérdezések száma. Egy végpont szintű részletességig, valamit megismerheti, hogyan sokszor egy végpontot a lekérdezésekre adott válaszok a Traffic Manager a visszaadott, ugyanazokat az információkat is megtekintheti.

Az alábbi példában az 1. ábra jelenik meg a Traffic Manager-profil által visszaadott összes lekérdezés választ. 

  
![A TRAFFIC Manager mérőszámai – összes lekérdezés összesített nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*1. ábra: Összesített nézet az összes lekérdezés*
  
2. ábra ugyanazokat az információkat jeleníti meg, azonban felosztási szempont végpontok. Ennek eredményeképpen a lekérdezési válaszok, amelyben egy adott végpontnak adott vissza a kötet látható.

![A TRAFFIC Manager-metrikák - osztott lekérdezés kötet végpontonként nézet](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*2. ábra: A visszaadott végpontonként látható lekérdezés kötet osztott nézet*

## <a name="endpoint-status-by-endpoint"></a>Végpont végpont állapota
Használat [Ez a metrika](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) tudni, hogy a profil a végpontok állapotát. Két érték szükséges:
 - használjon **1** , ha a végpont működik.
 - használjon **0** Ha a végpont nem működik.

Ez a metrika jeleníthetnek meg, vagy a mérőszámok (3. ábra) állapotát jelző összesített értékként, vagy azt oszthatók fel (lásd a 4. ábra) a meghatározott végpontokhoz állapotának megjelenítése. A korábbi, ha az összesítési szinten van kiválasztva esetén **átlagos**, az érték a metrika az összes végpontok állapotát aritmetikai átlagát. Például ha egy profil rendelkezik két végpontot, és csak az egyiket kifogástalan állapotban, ez a mérőszám lesz érték **0,50** a 3. ábrán látható módon. 


![A TRAFFIC Manager metrikák - végpont állapota összetett nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*3. ábra: Összetett végpont állapota metrika – kijelölt "Átlagos" összesítő nézete*


![A TRAFFIC Manager-metrikák - felosztás végpont állapotának megtekintése](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*4. ábra: Felosztása végpont állapota metrikák megtekintése*

Használhatja fel ezeket a metrikákat keresztül [Azure Monitor szolgáltatásban](../monitoring-and-diagnostics/monitoring-supported-metrics.md)a portálon [REST API-val](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/monitor), és [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/azurerm.insights), vagy – Traffic Manager portálja metrikáit ismertető szakaszban.

## <a name="alerts-on-traffic-manager-metrics"></a>Riasztások a Traffic Manager-metrikák
Feldolgozása és a Traffic Managerből metrikák megjelenítése mellett az Azure Monitor segítségével az ügyfelek konfigurálása, valamint ezek a metrikák tartozó riasztások fogadása. Feltételeknek kell teljesülniük az ezeket a metrikákat egy riasztás történik, gyakoriságát. Ezek a feltételek figyelni szeretne, és hogyan a riasztások kell küldeni, választhat. További információkért lásd: [Azure Monitor riasztásainak dokumentáció](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure Monitor szolgáltatásban](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Ismerje meg, hogyan [hozzon létre egy új diagramot, az Azure Monitor használatával](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)
