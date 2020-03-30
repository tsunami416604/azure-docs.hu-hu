---
title: Az Azure Monitor insightsáttekintése | Microsoft dokumentumok
description: Az insights személyre szabott figyelési élményt nyújt az Azure Monitorban az egyes alkalmazások és szolgáltatások számára. Ez a cikk röviden ismerteti a jelenleg rendelkezésre álló elemzéseket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657248"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Az Azure Monitor insightsának áttekintése
Az insights személyre szabott figyelési élményt nyújt az egyes alkalmazásokés szolgáltatások számára. Adatokat tárolnak az [Azure Monitor adatplatformján,](../platform/data-platform.md) és az Azure Monitor más funkcióit használják elemzésre és riasztásra, de további adatokat gyűjthetnek, és egyedi felhasználói élményt nyújthatnak az Azure Portalon. Az Azure-portál Azure Monitor menüjének **Insights** szakaszából származó elemzési adatok hoz.

Az alábbi szakaszok röviden ismertetik az Azure Monitorban jelenleg elérhető elemzéseket. Az egyes adatokkal kapcsolatos részleteket lásd a részletes dokumentációban.

## <a name="application-insights"></a>Application Insights
Az Application Insights egy bővíthető és több platformon működő alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. A legkülönbözőbb platformokon is működik, beleértve a .NET, node.js és Java EE alkalmazásokat, amelyeket a helyszínen, hibrid vagy bármely nyilvános felhőben üzemeltet. A DevOps-folyamattal is integrálható, és számos fejlesztői eszközhöz csatlakoztatási pontokat tartalmaz.

Lásd: [Mi az Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
Az Azure Monitor tárolók figyeli az Azure Container-példányok vagy az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett felügyelt Kubernetes-fürtöküzembe helyezett tárolószámítási feladatok teljesítményét. A tárolók figyelése kritikus fontosságú, különösen akkor, ha egy éles fürt ötös méretekben, több alkalmazással fut.

Tekintse meg [az Azure Monitor tárolók áttekintését.](../insights/container-insights-overview.md)

![Azure Monitor tárolókhoz](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure-figyelő erőforráscsoportokhoz (előzetes verzió)
Az Azure Monitor erőforráscsoportok segít a triage és diagnosztizálni az egyes erőforrások at, miközben az erőforráscsoport egészének állapotát és teljesítményét.

Lásd: [Erőforráscsoportok figyelése az Azure Monitorsegítségével (előzetes verzió).](../insights/resource-group-insights.md)

![Azure-figyelő erőforráscsoportokhoz](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure-figyelő virtuális gépekhez (előzetes verzió)
Az Azure Monitor virtuális gépekfigyeli az Azure virtuális gépek (VM) és a virtuális gép méretezési csoportok nagy méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket.

Tekintse [meg, hogy mi az Azure Monitor virtuális gépekhez?](vminsights-overview.md)

![Azure Monitor virtuális gépekhez](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure-figyelő hálózatokhoz (előzetes verzió)
[Az Azure Monitor for Networks](network-insights-overview.md) átfogó képet nyújt az összes hálózati erőforrás állapotáról és metrikáiról. A speciális keresési funkció segítségével azonosíthatja az erőforrás-függőségeket, lehetővé téve az olyan forgatókönyveket, mint például a webhelyet üzemeltető erőforrások azonosítása, egyszerűen a webhely nevének keresésével.

![Azure Monitor hálózatokhoz](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>További lépések
* További információ az [Azure Monitor adatplatformról,](../platform/data-platform.md) amelyet az elemzések használ.
* Ismerje meg az Azure Monitor által használt különböző [adatforrásokat](../platform/data-sources.md) és az egyes elemzések által gyűjtött különböző típusú adatokat.
