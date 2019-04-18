---
title: Mérőszámok és riasztások az Azure Traffic Managerben
description: Ez a cikk ismerteti a metrikák elérhető a Traffic Managerhez az Azure-ban.
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 1c0c48efc7abf4b810e92b5cd04d1538577c71d3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268285"
---
# <a name="traffic-manager-metrics-and-alerts"></a>A TRAFFIC Manager-mérőszámok és riasztások

A TRAFFIC Manager a DNS-alapú terheléselosztást, amely tartalmazza a több útválasztási módszerek és megfigyelési lehetőségek végpontot biztosít. Ez a cikk ismerteti a metrikák és a kapcsolódó riasztásokat, amelyek az ügyfelek számára érhető el. 

## <a name="metrics-available-in-traffic-manager"></a>A Traffic Managerben elérhető metrikák 

A TRAFFIC Manager profilonként, amelyek lehetővé teszik a Traffic manager használatát és azok végpontjait, hogy a profilhoz állapotát meg a következő metrikákat biztosítja.  

### <a name="queries-by-endpoint-returned"></a>Lekérdezések által visszaadott végpont
Használat [Ez a metrika](../azure-monitor/platform/metrics-supported.md) , amely a Traffic Manager-profil egy meghatározott időtartamon belül feldolgozza a lekérdezések számának megtekintése. Egy végpont szintű részletességig, valamit megismerheti, hogyan sokszor egy végpontot a lekérdezésekre adott válaszok a Traffic Manager a visszaadott, ugyanazokat az információkat is megtekintheti.

A következő példában az 1. ábra, amely a Traffic Manager-profil adja vissza minden lekérdezési válasz jeleníti meg. 

  
![Az összes lekérdezés összesített nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*1. ábra: Az összes lekérdezés összesített nézet*
  
2. ábra ugyanazokat az információkat jeleníti meg, azonban felosztási szempont végpontok. Ennek eredményeképpen a lekérdezési válaszok, amelyben egy adott végpontnak adott vissza a kötet látható.

![A TRAFFIC Manager-metrikák - osztott lekérdezés kötet végpontonként nézet](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*2. ábra: A visszaadott végpontonként látható lekérdezés kötet osztott nézet*

## <a name="endpoint-status-by-endpoint"></a>Végpont végpont állapota
Használat [Ez a metrika](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) tudni, hogy a profil a végpontok állapotát. Két érték szükséges:
 - használjon **1** a végpont működik, ha.
 - használjon **0** Ha a végpont nem működik.

Ez a metrika jeleníthetnek meg, vagy a mérőszámok (3. ábra) állapotát jelző összesített értékként, vagy azt oszthatók fel (lásd a 4. ábra) a meghatározott végpontokhoz állapotának megjelenítése. Ha a korábbi, ha az összesítési szinten van kiválasztva **átlagos**, ez a mérőszám értéke az összes végpontok állapotát aritmetikai átlagát. Például ha egy profil rendelkezik két végpontot, és csak az egyiket állapota kifogástalan, majd ez a mérőszám értéke **0,50** a 3. ábrán látható módon. 


![A TRAFFIC Manager metrikák - végpont állapota összetett nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*3. ábra: Végpont állapota metrika – kijelölt "Átlagos" összesítés összetett megtekintése*


![A TRAFFIC Manager-metrikák - felosztás végpont állapotának megtekintése](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*4. ábra: Végpont állapota metrikák megtekintését felosztása*

Használhatja fel ezeket a metrikákat keresztül [Azure Monitor szolgáltatásban](../azure-monitor/platform/metrics-supported.md)a portálon [REST API-val](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/monitor), és [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/az.applicationinsights), vagy – Traffic Manager portálja metrikáit ismertető szakaszban.

## <a name="alerts-on-traffic-manager-metrics"></a>Riasztások a Traffic Manager-metrikák
Feldolgozása és a Traffic Managerből metrikák megjelenítése mellett az Azure Monitor segítségével az ügyfelek konfigurálása, valamint ezek a metrikák tartozó riasztások fogadása. Feltételeknek kell teljesülniük az ezeket a metrikákat egy riasztás történik, gyakoriságát. Ezek a feltételek figyelni szeretne, és hogyan a riasztások kell küldeni, választhat. További információkért lásd: [Azure Monitor riasztásainak dokumentáció](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure Monitor szolgáltatásban](../azure-monitor/platform/metrics-supported.md)
- Ismerje meg, hogyan [Azure Monitor használatával diagram létrehozása](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
