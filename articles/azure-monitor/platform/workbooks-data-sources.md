---
title: Munkafüzetek adatforrások Azure Monitorása | Microsoft docs
description: Egyszerűsítse az összetett jelentéskészítést az előre elkészített és az egyéni paraméterekkel, Azure Monitor több adatforrásból készített munkafüzetekből
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
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658371"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor munkafüzetek adatforrásai

A munkafüzetek nagy számú adatforrással kompatibilisek. Ez a cikk végigvezeti a Azure Monitor-munkafüzetek számára jelenleg elérhető adatforrásokon.

## <a name="logs"></a>Naplók

A munkafüzetek lehetővé teszik a naplók lekérdezését a következő forrásokból:

* Azure Monitor naplók (Application Insights erőforrások és Log Analytics munkaterületek.)
* Erőforrás-központú adatforrások (tevékenység-naplók)

A munkafüzet szerzője olyan KQL-lekérdezéseket használhat, amelyek átalakítják a mögöttes erőforrás-adatokat olyan eredményhalmaz kiválasztásához, amely szövegként, diagramként vagy rácsként is megjeleníthető.

![Képernyőfelvétel a munkafüzetekről a jelentés felületéről](./media/workbooks-overview/logs.png)

A munkafüzet szerzője könnyedén lekérdezheti a több erőforrást egy valóban egységes, gazdag jelentéskészítési felület létrehozásával.

## <a name="metrics"></a>Mérőszámok

Az Azure-erőforrások olyan [metrikákat](data-platform-metrics.md) bocsátanak ki, amelyek munkafüzetek használatával érhetők el. A metrikák egy speciális vezérlőn keresztül érhetők el a munkafüzetekben, így megadhatja a cél erőforrásokat, a kívánt mérőszámokat és azok összesítését. Ezeket az adatsorokat diagramokban vagy rácsokban lehet ábrázolni.

![Képernyőfelvétel a CPU-kihasználtság munkafüzet-metrikáinak diagramjairól](./media/workbooks-overview/metrics-graph.png)

![Képernyőfelvétel a munkafüzet metrikái felületéről](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

A munkafüzetek támogatják az erőforrások és a metaadatok lekérdezését az Azure Resource Graph (ARG) használatával. Ez a funkció elsősorban a jelentések egyéni lekérdezési hatókörének létrehozásához használatos. Az erőforrás hatóköre az ARG által támogatott KQL-részhalmazon keresztül van kifejezve – ami gyakran elegendő a gyakori használati esetekhez.

Ha szeretné, hogy egy lekérdezés vezérlőelem használja ezt az adatforrást, használja a lekérdezés típusa legördülő listát az Azure Resource Graph kiválasztásához, és válassza ki a célozni kívánt előfizetéseket. A lekérdezés vezérlőelem használatával adja hozzá az ARG KQL-részhalmazt, amely egy érdekes erőforrás-részhalmazt választ ki.


![Képernyőkép az Azure Resource Graph KQL-lekérdezésről](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Riasztások (előzetes verzió)

A munkafüzetek lehetővé teszik a felhasználók számára, hogy megjelenítsék az erőforrásaikkal kapcsolatos aktív riasztásokat. Ez a funkció lehetővé teszi, hogy olyan jelentéseket hozzon létre, amelyek egy jelentésbe összevonják az értesítési adatokat (riasztásokat) és a diagnosztikai adatokat (metrikákat, naplókat). Ezek az információk összekapcsolhatók olyan gazdag jelentések létrehozásához is, amelyek az adatforrások elemzését kombinálják.

Ha szeretné, hogy a lekérdezés vezérlőelem ezt az adatforrást használja, használja a lekérdezés típusa legördülő listát a riasztások kiválasztásához, és válassza ki a célként használni kívánt előfizetéseket, erőforráscsoportokat vagy erőforrásokat. A riasztási szűrő legördülő lista használatával kiválaszthatja az elemzési igényekhez tartozó riasztások érdekes részhalmazát.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Munkaterhelés állapota (előzetes verzió)

Azure Monitor rendelkezik olyan funkciókkal, amelyek proaktív módon figyelik a Windows-vagy Linux-alapú vendég operációs rendszerek rendelkezésre állását és teljesítményét. Azure Monitor a modellek kulcs-összetevőit és azok kapcsolatait, az összetevők állapotának mérésének feltételeit, valamint azt, hogy mely összetevők riasztást küldenek, ha a rendszer nem kifogástalan állapotot észlel. A munkafüzetek lehetővé teszik, hogy a felhasználók részletes interaktív jelentéseket hozzanak létre az adatok használatával.

Ha szeretné, hogy a lekérdezés vezérlőelem ezt az adatforrást használja, a **lekérdezés típusa** legördülő menüből válassza a munkaterhelés állapota lehetőséget, majd válassza az előfizetés, az erőforráscsoport vagy a virtuálisgép-erőforrások célhelyre lehetőséget. Az állapot-szűrő legördülő lista használatával kiválaszthatja az elemzési igényeknek megfelelő egészségügyi incidensek érdekes részhalmazát.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure-erőforrás állapota 

A munkafüzetek lehetővé teszik az Azure Resource Health beszerzését és más adatforrásokkal való összekapcsolását, hogy gazdag, interaktív állapotjelentést hozzanak létre.

Ha szeretné, hogy egy lekérdezés vezérlőelem használja ezt az adatforrást, a **lekérdezés típusa** legördülő listából válassza ki az Azure Health elemet, és válassza ki a célként használni kívánt erőforrásokat. Az állapot-szűrő legördülő lista használatával kiválaszthatja az elemzési igényekhez tartozó erőforrás-problémák érdekes részét.

![A riasztások lekérdezésének képernyőképe](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Adatkezelő (előzetes verzió)

A munkafüzetek mostantól támogatják az [Azure adatkezelő](https://docs.microsoft.com/azure/data-explorer/) -fürtökről való lekérdezést a hatékony [Kusto](https://docs.microsoft.com/azure/kusto/query/index) -lekérdezési nyelvvel.   

![Képernyőfelvétel a Kusto-lekérdezési ablakról](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](workbooks-visualizations.md) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](workbooks-access-control.md) és megosztása.
