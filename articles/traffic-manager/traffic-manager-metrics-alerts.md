---
title: Metrikák és riasztások az Azure Traffic Managerben
description: Ebben a cikkben ismerje meg a metrikák és riasztások elérhető Traffic Manager az Azure-ban.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: rohink
ms.openlocfilehash: 521e6ac605d187c0f95545611a17a86cfda6e1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938586"
---
# <a name="traffic-manager-metrics-and-alerts"></a>A Traffic Manager mutatói és riasztásai

A Traffic Manager dns-alapú terheléselosztást biztosít, amely több útválasztási módszert és végpontfigyelési beállításokat tartalmaz. Ez a cikk ismerteti a mutatók at és a kapcsolódó riasztások, amelyek az ügyfelek számára elérhető. 

## <a name="metrics-available-in-traffic-manager"></a>A Traffic Manager ben elérhető mérőszámok 

A Traffic Manager profilonként a következő mutatókat biztosítja, amelyek segítségével az ügyfelek megérthetik a Traffic Manager használatát és az adott profil szerinti végpontjaik állapotát.  

### <a name="queries-by-endpoint-returned"></a>Végpontonként visszaadott lekérdezések
[Ezzel a metrikával](../azure-monitor/platform/metrics-supported.md) megtekintheti a Traffic Manager-profil által egy adott időszakban feladott lekérdezések számát. Ugyanazokat az információkat egy végpontszintű részletességen is megtekintheti, amely segít megérteni, hogy a Traffic Manager lekérdezési válaszaihányszor adott vissza egy végpontot.

A következő példában az 1. 

  
![Az összes lekérdezés összesítése](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*1. ábra: Összesítő nézet az összes lekérdezéssel*
  
ábra ugyanazokat az információkat jeleníti meg, azonban végpontok szerint oszlik meg. Ennek eredményeképpen láthatja a lekérdezési válaszok azon kötetét, amelyben egy adott végpontot adott vissza.

![Traffic Manager-metrikák – a lekérdezési kötet végpontonkénti megosztott nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*2. ábra: A megosztott nézet a visszaadott végpontonként megjelenített lekérdezési kötettel*

## <a name="endpoint-status-by-endpoint"></a>Végpont állapota végpontok szerint
[Ezzel a metrikával](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) a profilban lévő végpontok állapotának megértéséhez. Két értéket igényel:
 - **1-et használjon,** ha a végpont felfelé van.
 - **használja** a 0-t, ha a végpont nem.

Ez a metrika az összes mutató állapotát jelző összesített értékként (3. ábra), vagy felosztható (lásd a 4. ábrát) az adott végpontok állapotának megjelenítéséhez. Ha az előbbi, ha az összesítési szint **választható átlagként,** akkor ennek a metrikának az értéke az összes végpont állapotának számtani átlaga. Ha például egy profil két végponttal rendelkezik, és csak egy kifogástalan, akkor ez a metrika értéke **0,50,** ahogy a 3. 


![Traffic Manager metrikák - összetett nézet a végpont állapota](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*3. ábra: A végpontállapot-mérőszám összetett nézete – "Avg" aggregáció kiválasztva*


![Traffic Manager metrikák - a végpont állapotának megosztott nézete](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*4. ábra: Végpontállapot-mérőszámok osztott nézete*

Ezeket a metrikákat az [Azure Monitor szolgáltatás](../azure-monitor/platform/metrics-supported.md)portálján, REST [API-ján](https://docs.microsoft.com/rest/api/monitor/), Az [Azure CLI-n](https://docs.microsoft.com/cli/azure/monitor)és az [Azure PowerShellen](https://docs.microsoft.com/powershell/module/az.applicationinsights)keresztül, vagy a Traffic Manager portálfelületének metrikák szakaszán keresztül használhatja fel.

## <a name="alerts-on-traffic-manager-metrics"></a>Riasztások a Traffic Manager mutatóira
A Traffic Manager metrikáinak feldolgozása és megjelenítése mellett az Azure Monitor lehetővé teszi az ügyfelek számára, hogy konfigurálják és fogadják az ezekkel a metrikákkal kapcsolatos riasztásokat. Kiválaszthatja, hogy milyen feltételeknek kell teljesülnie ezekben a metrikákban ahhoz, hogy egy riasztás bekövetkezzen, milyen gyakran kell ezeket a feltételeket figyelni, és hogyan kell a riasztásokat küldeni Önnek. További információt az [Azure Monitor riasztási dokumentációjában](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)talál.

## <a name="next-steps"></a>További lépések
- További információ az [Azure Monitor szolgáltatásról](../azure-monitor/platform/metrics-supported.md)
- Ismerje meg, hogyan [hozhat létre diagramot az Azure Monitor használatával](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
