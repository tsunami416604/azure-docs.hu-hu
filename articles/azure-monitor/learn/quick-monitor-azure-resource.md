---
title: Azure-erőforrás figyelése az Azure Monitorsegítségével
description: Ismerje meg, hogyan gyűjthet és elemezhet adatokat egy Azure-erőforráshoz az Azure Monitorban.
ms. subservice: logs
ms.topic: quickstart
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: c4b80e62d3800392b847a411dfc66c3278e72bba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77661864"
---
# <a name="quickstart-monitor-an-azure-resource-with-azure-monitor"></a>Rövid útmutató: Azure-erőforrás figyelése az Azure Monitorsegítségével
[Az Azure Monitor](../overview.md) akkor kezdi el az azure-erőforrásokból származó adatok gyűjtését, amikor azok létrejönnek. Ez a rövid útmutató egy rövid áttekintést nyújt az adatok, amelyek automatikusan gyűjtött egy erőforrás, és hogyan tekintheti meg az okat az Azure Portalon egy adott erőforrás. Később hozzáadhat konfigurációt további adatok gyűjtéséhez, és az Azure Monitor menüben ugyanazokat az eszközöket használhatja az előfizetésben lévő összes erőforráshoz gyűjtött adatok eléréséhez.

Az Azure-erőforrásokból gyűjtött figyelési adatok részletesebb leírását az [Azure-erőforrások figyelése az Azure Monitor segítségével című témakörben tartalmazza.](../insights/monitor-azure-resource.md)


## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a . 


## <a name="overview-page"></a>Áttekintő lap
Számos szolgáltatás az **áttekintő** lapon figyeli az adatokat, így rövid áttekintést kap a működésükről. Ez általában az Azure Monitor metrikákban tárolt platformmetrikák egy részhalmazán alapul.

1. Keresse meg az Azure-erőforrást az előfizetésében.
2. Lépjen az **Áttekintés** lapra, és jegyezze fel, hogy megjelennek-e teljesítményadatok. Ezeket az adatokat az Azure Monitor biztosítja. Az alábbi példa az **Áttekintő** lap egy Azure-tárfiók, és láthatja, hogy több metrikák jelennek meg.

    ![Áttekintő lap](media/quick-monitor-azure-resource/overview.png)

3. Bármelyik grafikonra kattintva megnyithatja az adatokat a metrikakezelőben, amelyet az alábbiakban ismertetünk.

## <a name="view-the-activity-log"></a>A tevékenységnapló megtekintése
A tevékenységnapló betekintést nyújt az előfizetés egyes Azure-erőforrások műveleteibe. Ez olyan információkat tartalmaz, mint például egy erőforrás létrehozása vagy módosítása, a feladat indítása kora vagy egy adott művelet bekövetkezése.

1. Az erőforrás menütetején válassza a **Tevékenységnapló lehetőséget.**
2. Az aktuális szűrő az erőforráshoz kapcsolódó eseményekre van beállítva. Ha nem lát eseményeket, próbálja meg módosítani az **időtartományt** az időtartomány növeléséhez.

    ![Tevékenységnapló](media/quick-monitor-azure-resource/activity-log-resource.png)

4. Ha az előfizetés más erőforrásaiból származó eseményeket szeretne látni, módosítsa a feltételeket a szűrőben, vagy akár távolítsa el a szűrő tulajdonságait.

    ![Tevékenységnapló](media/quick-monitor-azure-resource/activity-log-all.png)



## <a name="view-metrics"></a>Metrikák megtekintése
A metrikák olyan numerikus értékek, amelyek az erőforrás egy adott időpontban bizonyos aspektusait írják le. Az Azure Monitor automatikusan összegyűjti a platform metrikák egy perces időközönként az összes Azure-erőforrások. Ezeket a metrikákat a metrikák kezelője segítségével tekintheti meg.

1. Az erőforrás menüjének **Figyelés** szakaszában válassza a **Metrikák**lehetőséget. Ez megnyitja metrikakezelő a hatókör beállítása az erőforráshoz.
2. A **Metrika hozzáadása** gombra kattintva adjon hozzá egy mérőszámot a diagramhoz.
   
   ![Metrika-kezelő](media/quick-monitor-azure-resource/metrics-explorer-01.png)
   
4. Válasszon ki egy **metrikát** a legördülő listából, majd **egy összesítést.** Ez határozza meg, hogy az összegyűjtött értékek mintavételezése az egyes időintervallumokban hogyan történik.

    ![Metrika-kezelő](media/quick-monitor-azure-resource/metrics-explorer-02.png)

5. A **Metrika hozzáadása** gombra kattintva további metrika- és összesítési kombinációkat adhat a diagramhoz.

    ![Metrika-kezelő](media/quick-monitor-azure-resource/metrics-explorer-03.png)



## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megtekintette a tevékenységnaplót és az Azure-erőforrások metrikákat, amelyeket az Azure Monitor automatikusan gyűjt. Az erőforrásnaplók betekintést nyújtanak az erőforrás részletes működésébe, de az összegyűjtéshez konfigurálni kell őket. Folytassa az erőforrásnaplók gyűjtésére szolgáló oktatóanyagot egy Log Analytics-munkaterületre, ahol naplólekérdezésekkel elemezhetők.

> [!div class="nextstepaction"]
> [Erőforrásnaplók gyűjtése és elemzése az Azure Monitor segítségével](tutorial-resource-logs.md)
