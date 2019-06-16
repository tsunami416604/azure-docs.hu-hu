---
title: Az Azure monitorban Insights áttekintése |} A Microsoft Docs
description: Insights testre szabott figyelési felületet tudjon biztosítani az Azure Monitor az adott alkalmazáshoz és szolgáltatáshoz. A cikk ismerteti, amelyek jelenleg az insights mindegyike rövid leírását.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247229"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Az Azure monitorban Insights áttekintése
Insights figyelési testreszabott élményt biztosítani adott alkalmazásokat és szolgáltatásokat. Ezek az adatok tárolása a [Azure Monitor adatplatform](../platform/data-platform.md) és más az Azure Monitor funkciói elemzés és riasztás céljából, de előfordulhat, hogy további adatok gyűjtése és a egy egyedi felhasználói felületet biztosít az Azure Portalon. Is használható elemzéseket készítsenek eléréséhez a **Insights** az Azure Portalon az Azure figyelő menü szakaszában.

A következő szakaszok az elemzéseket, amelyek jelenleg az Azure monitorral rövid leírását. Az egyes tekintse meg a részletes dokumentációjában.

## <a name="application-insights"></a>Application Insights
Az Application Insights egy bővíthető és több platformon működő alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. Számos különböző platformokról, többek között a .NET, Node.js és Java EE-alapú alkalmazások esetében működik a helyszíni, hibrid vagy bármely nyilvános felhőben üzemeltetett. Is integrálható a fejlesztési és üzemeltetési folyamatot, és rendelkezik kapcsolódási ponttal, számos különböző fejlesztői eszközöket.

Lásd: [Mi az Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Az Azure Monitor for containers szolgáltatásban
A tárolók figyelők az Azure Monitor a tárolók számítási feladatainak teljesítményét vagy az Azure Container Instances szolgáltatásban üzembe helyezett, vagy az Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök felügyelt. A tárolók alapvető fontosságú, különösen akkor, ha nagy mennyiségű, több alkalmazással rendelkező egy éles fürtöt futtat.

Lásd: [tárolók áttekintése az Azure Monitor](../insights/container-insights-overview.md).

![Az Azure Monitor for containers szolgáltatásban](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Az Azure Monitor az erőforrás-csoportokhoz (előzetes verzió)
Erőforráscsoportok az Azure Monitor segítségével, osztályozhatja és diagnosztizálhatja problémáit az egyes erőforrások észlel, miközben környezet állapotát és teljesítményét az erőforráscsoport, egy teljes esetleges szabályozási hiányosságok elhárítását.

Lásd: [erőforráscsoportok monitorozása az Azure Monitor (előzetes verzió) szolgáltatással](../insights/resource-group-insights.md).

![Az Azure Monitor erőforrás-csoportok](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Az Azure Monitor-beli virtuális gépek (előzetes verzió)
A virtuális gépek az Azure Monitor figyeli az Azure-beli virtuális gépek (VM), és a virtuálisgép-méretezési csoportok ipari méretekben. A szolgáltatás elemzi a Windows és Linux rendszerű virtuális gépek teljesítményét és állapotát, valamint figyeli folyamataikat és a más erőforrásokkal és külső folyamatokkal kapcsolatos függőségeiket.

Lásd: [Mi az Azure Monitor-beli virtuális gépek?](vminsights-overview.md)

![Azure Monitor virtuális gépekhez](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>További lépések
* Tudjon meg többet a [Azure Monitor adatplatform](../platform/data-platform.md) insights által használható.
* További információ a különböző [Azure figyelő által használt adatforrások](../platform/data-sources.md) és az egyes az insights által gyűjtött adatok különböző fajtáit.
