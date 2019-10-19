---
title: Az információk áttekintése Azure Monitorban | Microsoft Docs
description: Az adatvizsgálatok testreszabott figyelési élményt nyújtanak az egyes alkalmazások és szolgáltatások Azure Monitor. Ez a cikk a jelenleg elérhető összes információ rövid leírását tartalmazza.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 3abf3d562b9ca4d6fc9907c065dbaf9a52d58c5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555340"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Az információk áttekintése Azure Monitor
Az egyes alkalmazásokhoz és szolgáltatásokhoz testreszabott figyelési funkciókkal szolgálnak. A [Azure monitor adatplatformban](../platform/data-platform.md) tárolják az adatokat, és más Azure monitor funkciókat is kihasználhatnak az elemzéshez és a riasztásokhoz, azonban további adatokat gyűjthetnek, és egyedi felhasználói élményt biztosíthatnak a Azure Portal. A Azure Portal Azure Monitor menüjének bepillantást **nyerhet a bepillantást az** adatokból.

A következő szakaszokban a Azure Monitor aktuálisan elérhető információinak rövid leírását találhatja meg. További részletekért tekintse meg a részletes dokumentációt.

## <a name="application-insights"></a>Application Insights
Az Application Insights egy bővíthető és több platformon működő alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. Számos platformon használható, többek között a .NET, a Node. js és a Java EE, helyszíni, hibrid vagy nyilvános felhőben üzemelő alkalmazásokhoz. Emellett a DevOps folyamattal is integrálható, és a különböző fejlesztői eszközökhöz kapcsolódó kapcsolódási pontokkal rendelkezik.

Lásd: [Mi az Application Insights?](../app/app-insights-overview.md)

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor tárolók számára
A tárolók Azure Monitor figyeli az Azure Kubernetes szolgáltatásban (ak) üzemeltetett Azure Container Instances vagy felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményét. A tárolók monitorozása kritikus fontosságú, különösen akkor, ha éles fürtöt futtat, több alkalmazással.

Lásd: [Azure monitor a tárolók áttekintéséhez](../insights/container-insights-overview.md).

![Azure Monitor tárolók számára](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Erőforráscsoportok Azure Monitor (előzetes verzió)
Az erőforráscsoportok Azure Monitor segíti az egyes erőforrásokkal kapcsolatos problémák osztályozását és diagnosztizálását, miközben az erőforráscsoport állapotára és teljesítményére vonatkozó kontextust kínál.

Lásd: [erőforráscsoportok figyelése Azure monitor (előzetes verzió)](../insights/resource-group-insights.md).

![Erőforráscsoportok Azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (előzetes verzió)
Azure Monitor for VMs az Azure-beli virtuális gépeket (VM) és a virtuálisgép-méretezési csoportokat nagy méretben figyeli. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket.

Lásd: [Mi az Azure monitor for VMS?](vminsights-overview.md)

![Azure Monitor virtuális gépekhez](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Következő lépések
* További információ az elemzések által kihasználható [Azure monitor adatplatformról](../platform/data-platform.md) .
* Ismerje meg a [Azure monitor által használt különböző adatforrásokat](../platform/data-sources.md) , valamint az egyes elemzések által gyűjtött különféle adatokat.
