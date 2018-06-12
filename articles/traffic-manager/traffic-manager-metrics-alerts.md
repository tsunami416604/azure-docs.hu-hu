---
title: Metrikák és a riasztások az Azure Traffic Manager |} Microsoft Docs
description: A cikkből elérhető metrikák a Traffic Manager az Azure-ban.
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
ms.openlocfilehash: 424782be2d814df6d598591198b5005fb494d3da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303753"
---
# <a name="traffic-manager-metrics-and-alerts"></a>A TRAFFIC Manager metrikák és riasztások

A TRAFFIC Manager DNS-alapú terheléselosztás viszony – például több útválasztási metódusok és figyelési lehetőségek végpont biztosít. Ez a cikk ismerteti a metrikák és a hozzá tartozó riasztási, amelyek az ügyfél számára érhető el. 

## <a name="metrics-available-in-traffic-manager"></a>A Traffic Managerben elérhető metrikák 

A TRAFFIC Manager profilonként képes használni a Traffic manager használatát és a végpontokat a profilhoz állapotának ügyfelek, amelyek a következő metrikákat biztosít.  

### <a name="queries-by-endpoint-returned"></a>Lekérdezések által visszaadott végpont
Használjon [Ez a metrika](../monitoring-and-diagnostics/monitoring-supported-metrics.md) az adott időszakon belül a Traffic Manager-profil által feldolgozott lekérdezések megtekintéséhez. A Traffic Manager válaszokban visszaadott, valamit megismerheti, hogyan végpont sokszor végpont szintű részletességű ugyanazokat az információkat is megtekintheti.

Az alábbi példában az 1. ábra a Traffic Manager-profil által visszaadott eredményobjektumokban tárolt összes válaszokban jeleníti meg. 

  
![A TRAFFIC Manager metrikák – összes lekérdezés összesített ábrázolása](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*1. ábra: Az összes lekérdezési nézet összesítése*
  
2. ábra ugyanazokat az információkat jelenít meg, azonban felosztott végpontok által. Ennek eredményeképpen tekintheti meg, amelyben egy adott végpont visszaadott válaszokban mennyiségét.

![A TRAFFIC Manager metrikák - osztott lekérdezés kötet végpontonként nézet](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*2. ábra: Lekérdezés mennyiségi visszaadott végpontonként látható osztott nézet*

## <a name="endpoint-status-by-endpoint"></a>Végpont végpont állapota
Használjon [Ez a metrika](../monitoring-and-diagnostics/monitoring-supported-metrics.md) tudni, hogy a profil végpontjainak állapotinformációit. Két érték tart:
 - használjon **1** , ha a végpont működik-e.
 - használjon **0** Ha a végpont nem működik.

Ez a metrika is látható, vagy a metrikák (a 3. ábra) állapotát jelző összesített értékként, vagy azt oszthatók (lásd a 4. ábra) meghatározott végpontokhoz állapotának megjelenítése. Esetén az volt, ha az összesítési szint van kiválasztva **Avg**, ilyen érték mérőszáma végpontjai állapotának aritmetikai átlaga. Például ha egy profil rendelkezik-e két végpontot, és csak egy megfelelő állapotban, ez a metrika értéke lesz az **0,50** a 3. ábrán látható módon. 


![A TRAFFIC Manager metrikák - végpont állapota összetett ábrázolása](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*3. ábra: Összetett végpont állapota metrika – kijelölt "Avg" összesítő nézete*


![A TRAFFIC Manager metrikák - osztott végpont állapot nézet](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*4. ábra: A végpont állapota mérőszámok nézet felosztása*

A metrikák keresztül is felhasználhatnak [Azure-figyelő szolgáltatás](../monitoring-and-diagnostics/monitoring-supported-metrics.md)tartozó portal, [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), és [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), vagy a Traffic Manager webportál élmény metrikák szakasza.

## <a name="alerts-on-traffic-manager-metrics"></a>Riasztások a Traffic Manager metrikák
Feldolgozási és metrikákat a Traffic Manager megjelenítése kívül Azure a figyelő lehetővé teszi az ügyfelek konfigurálásához és a metrikák társított értesítéseket. Feltételek kell teljesíteni a riasztást fordulhat elő, metrikákat, milyen gyakran feltételek figyelni szeretne, és hogyan a riasztások kell küldeni, kiválasztása További információkért lásd: [Azure a figyelő riasztást küld a dokumentáció](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>További lépések
- További információ [szolgáltatás Azure figyelése](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Megtudhatja, hogyan [hozzon létre egy új Azure-figyelővel diagram](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)