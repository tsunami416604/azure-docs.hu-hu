---
title: Azure Monitor munkafüzetek adatforrásai | Microsoft-dokumentumok
description: Egyszerűsítse az összetett jelentéseket a több adatforrásból készült, előre összeállított és egyéni paraméterezett Azure Monitor-munkafüzetekkel
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248579"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Az Azure Monitor munkafüzetek adatforrásai

A munkafüzetek számos adatforrással kompatibilisek. Ez a cikk végigvezeti az Azure Monitor munkafüzetek jelenleg elérhető adatforrások.

## <a name="logs"></a>Naplók

A munkafüzetek lehetővé teszik a naplók lekérdezését a következő forrásokból:

* Azure Monitor naplók (Application Insights-erőforrások és a Log Analytics-munkaterületek.)
* Erőforrás-központú adatok (tevékenységnaplók)

A munkafüzet-szerzők kql-lekérdezésekkel alakíthatják át az alapul szolgáló erőforrásadatokat, így kiválaszthatnak egy olyan eredményhalmazt, amely szövegként, diagramként vagy rácsként jeleníthető meg.

![Képernyőkép a munkafüzetnapló-jelentés felületéről](./media/workbooks-overview/logs.png)

A munkafüzet-szerzők egyszerűen lekérdezhetnek több erőforrást, így egy igazán egységes, gazdag jelentéskészítési élményt nyújt.

## <a name="metrics"></a>Mérőszámok

Az Azure-erőforrások [olyan metrikákat bocsátanak](data-platform-metrics.md) ki, amelyek munkafüzeteken keresztül érhetők el. A metrikák a munkafüzetekben egy speciális vezérlőn keresztül érhetők el, amely lehetővé teszi a célerőforrások, a kívánt metrikák és összesítésük megadását. Ezek az adatok ezután diagramokon vagy rácsokon ábrázolhatók.

![Képernyőkép a munkafüzet metrikáinak diagramjairól a cpu-kihasználtságról](./media/workbooks-overview/metrics-graph.png)

![Képernyőkép a munkafüzet metrikáinak felületéről](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

A munkafüzetek támogatják az erőforrások és metaadataik lekérdezését az Azure Resource Graph (ARG) használatával. Ez a funkció elsősorban a jelentések egyéni lekérdezési hatókörének létrehozására szolgál. Az erőforrás hatóköre az ARG által támogatott KQL-részhalmazon keresztül fejeződik ki – ami gyakran elegendő a gyakori használati esetekhez.

Ahhoz, hogy egy lekérdezésvezérlő használja ezt az adatforrást, a Lekérdezés típusa legördülő válassza ki az Azure Resource Graph, és válassza ki a megcélozni kívánt előfizetések. A Lekérdezés vezérlővel hozzáadhatja az ARG KQL-részhalmazt, amely egy érdekes erőforrás-részhalmazt választ ki.


![Képernyőkép az Azure Resource Graph KQL lekérdezéséről](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Riasztások (előzetes verzió)

A munkafüzetek lehetővé teszik a felhasználók számára az erőforrásaikhoz kapcsolódó aktív riasztások megjelenítését. Ez a funkció lehetővé teszi olyan jelentések létrehozását, amelyek egyetlen jelentésbe egyesítik az értesítési adatokat (riasztás) és a diagnosztikai információkat (metrikák, naplók). Ez az információ összevonható is, hogy olyan bővített jelentéseket hozzon létre, amelyek egyesítik az adatforrások közötti elemzéseket.

Ha azt szeretné, hogy egy lekérdezésvezérlő használja ezt az adatforrást, a Lekérdezés típus legördülő menüben válassza a Riasztások lehetőséget, és válassza ki a megcélozni kívánt előfizetéseket, erőforráscsoportokat vagy erőforrásokat. A riasztási szűrő legördülő funkcióival kiválaszthat egy érdekes, az analitikus igényeknek megfelelő riasztási részhalmazt.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Számítási feladatok állapota (előzetes verzió)

Az Azure Monitor olyan funkciókkal rendelkezik, amelyek proaktív módon figyelik a Windows vagy Linux vendég operációs rendszerek rendelkezésre állását és teljesítményét. Az Azure Monitor modellek kulcsfontosságú összetevők és azok kapcsolatait, az összetevők állapotának mérésére vonatkozó feltételeket, és mely összetevők figyelmeztetik, ha egy nem megfelelő állapot ot észlel. A munkafüzetek lehetővé teszik a felhasználók számára, hogy ezeket az információkat gazdag interaktív jelentések készítésére használják.

Ahhoz, hogy egy lekérdezésvezérlő használja ezt az adatforrást, a **Lekérdezés típusa** legördülő menüvel válassza a Számítási feladatok állapota lehetőséget, és válassza az előfizetést, az erőforráscsoportot vagy a virtuális gép erőforrásait a célként. Az állapotszűrő legördülő változatai segítségével válassza ki az egészségügyi események érdekes részhalmazát az analitikus igényeinek megfelelően.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Az Azure-erőforrások állapota 

A munkafüzetek támogatják az Azure-erőforrások állapotának beszerzését és kombinálását más adatforrásokkal, hogy gazdag, interaktív állapotjelentéseket készítsenek

Ahhoz, hogy egy lekérdezésvezérlő használja ezt az adatforrást, használja a **Lekérdezés típusa** legördülő válassza ki az Azure állapotát, és válassza ki a megcélozni kívánt erőforrásokat. Az állapotszűrő legördülő változatai segítségével az analitikus igényeknek megfelelő erőforrásproblémák érdekes részhalmazát választhatja ki.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Data Explorer (előzetes verzió)

Munkafüzetek most már támogatja a lekérdezést az [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/) fürtök a hatékony [Kusto](https://docs.microsoft.com/azure/kusto/query/index) lekérdezési nyelv.   

![Képernyőkép a Kusto lekérdezésablakáról](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
