---
title: Azure-erőforrás figyelése Azure Monitor
description: Megtudhatja, hogyan gyűjthet és elemezheti az Azure-erőforrások adatait Azure Monitorban.
ms.service: azure-monitor
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: 3081c0f8fa8450643a992c8e82840a44db263f76
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863978"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Gyors útmutató: Azure-erőforrások figyelése Azure Monitor
[Azure monitor](../overview.md) elindítja az adatok gyűjtését az Azure-erőforrásokból a létrehozásuk pillanatában. Ez a rövid útmutató egy adott erőforráshoz automatikusan összegyűjtött és a Azure Portalban megtekinthető adatokat ismerteti. Később további adatokat is hozzáadhat a konfigurációhoz, és a Azure Monitor menüben megtekintheti az előfizetéshez tartozó összes erőforráshoz gyűjtött adatokat.

Az Azure-erőforrásokból gyűjtött monitorozási adatok részletes ismertetését lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../insights/monitor-azure-resource.md).


## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 


## <a name="overview-page"></a>Áttekintő lap
Számos szolgáltatás a működésük gyors áttekintése érdekében az **áttekintő** oldalon található megfigyelési információkat is tartalmazza. Ez általában a Azure Monitor Mérőszámokban tárolt platform-mérőszámok egy részhalmazán alapul.

1. Keresse meg az Azure-erőforrást az előfizetésében.
2. Lépjen az **Áttekintés** lapra, és jegyezze fel, hogy van-e megjeleníthető teljesítményadatok. Ezeket az adatAzure Monitor biztosítjuk. Az alábbi példa egy Azure Storage-fiók **áttekintő** lapja, amelyen láthatja, hogy több metrika is megjelenik.

    ![Áttekintő lap](media/quick-monitor-azure-resource/overview.png)

3. Bármelyik gráfra kattintva megnyithatja az adatokat a metrikák Explorerben, amely az alábbiakban olvasható.

## <a name="view-the-activity-log"></a>A műveletnapló megtekintése
A tevékenység naplója betekintést nyújt az egyes Azure-erőforrások műveleteibe az előfizetésben. Ez olyan információkat tartalmaz, mint az erőforrások létrehozásakor vagy módosításakor, a feladatok indításakor vagy egy adott művelet bekövetkeztekor.

1. Az erőforrás menüjének tetején válassza a **műveletnapló**lehetőséget.
2. Az aktuális szűrő az erőforráshoz kapcsolódó eseményekre van beállítva. Ha nem lát eseményeket, próbálja meg módosítani a **TimeSpan** az idő hatókörének növeléséhez.

    ![Tevékenységnapló](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Ha az előfizetéshez tartozó más erőforrásokból származó eseményeket szeretne látni, módosítsa a feltételeket a szűrőben, vagy távolítsa el a szűrő tulajdonságait is.

    ![Tevékenységnapló](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Metrikák megtekintése
A metrikák olyan numerikus értékek, amelyek egy adott időpontban írják le az erőforrás bizonyos aspektusait. A Azure Monitor az összes Azure-erőforrásból egy percen belül automatikusan gyűjti a platform metrikáit. Ezek a metrikák a metrikák Explorerrel tekinthetők meg.

1. Az erőforrás menüjének **figyelés** területén válassza a **metrikák**lehetőséget. Ekkor megnyílik a metrikák kezelője az erőforráshoz beállított hatókörrel.
2. A diagramhoz metrika hozzáadásához kattintson a **metrika hozzáadása** lehetőségre.
   
   ![Metrikák Explorer](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Válasszon ki egy **mérőszámot** a legördülő listából, majd egy **összesítést**. Ez határozza meg, hogyan történjen az összegyűjtött értékek mintavételezése az egyes időintervallumokban.

    ![Metrikák Explorer](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. Kattintson a **metrika hozzáadása** lehetőségre további mérőszámok és összesítési kombinációk hozzáadásához a diagramhoz.

    ![Metrikák Explorer](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban megtekintett egy Azure-erőforrás tevékenység-naplóját és metrikáit, amelyeket a Azure Monitor automatikusan gyűjt. Az erőforrás-naplók betekintést nyújtanak az erőforrás részletes műveleteibe, de a gyűjtéshez konfigurálni kell őket. Folytassa az erőforrás-naplók Log Analytics munkaterületre való összegyűjtését ismertető oktatóanyagot, ahol a naplózási lekérdezések használatával elemezhetők.

> [!div class="nextstepaction"]
> [Erőforrás-naplók összegyűjtése és elemzése Azure Monitor](tutorial-resource-logs.md)
