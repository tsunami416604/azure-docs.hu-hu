---
title: "Ismerkedés az Azure-figyelő |} Microsoft Docs"
description: "Ismerkedjen meg az Azure Monitor használatával, hogy betekintést kapjon az erőforrásai működéséről, és az adatok alapján végrehajthassa a megfelelő műveleteteket."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: johnkem
ms.openlocfilehash: ba4e8fe0d54deb4a980174ff7d0904854c794d3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-monitor"></a>Ismerkedés az Azure Monitor szolgáltatással
Az Azure Monitor egy olyan platformszolgáltatás, mely egyetlen forrást kínál az Azure-erőforrások figyeléséhez. Az Azure Monitorral az egyes erőforrásoktól az Azure-ba érkező metrikákat és naplókat láthatóvá teheti, lekérdezheti, átirányíthatja, archiválhatja, illetve ezekkel különböző műveleteket is végezhet. Ezekkel az adatokkal a Monitor portálpaneljén, valamint a [Monitor PowerShell-parancsmagok](insights-powershell-samples.md), a [platformfüggetlen parancssori felület](insights-cli-samples.md) vagy az [Azure Monitor REST API-k](https://msdn.microsoft.com/library/dn931943.aspx) használatával dolgozhat. Ebben a cikkben sorra vesszük az Azure Monitor főbb összetevőit, és a portál segítségével mutatjuk be őket.

## <a name="walkthrough"></a>Útmutatás
1. A portálon lépjen a **More services** (További szolgáltatások) lehetőségre, és keresse meg a **Monitor** pontot. A csillag ikonra kattintva adja hozzá ezt a lehetőséget a kedvencekhez, így az a bal oldali navigációs sávról mindig könnyen elérhető lesz.

    ![A Monitor pont a szolgáltatások listájában](./media/monitoring-get-started/monitor-more-services.png)
2. Kattintson a **Monitor** lehetőségre a **Monitor** panel megnyitásához. Ez a panel egyetlen, összevont nézetben jeleníti meg az összes figyelési beállítást és adatot. A panel az első alkalommal a **Tevékenységnapló** területet megjelenítve nyílik meg.

    ![Navigálás a Monitor panelen](./media/monitoring-get-started/monitor-blade-nav.png)

    Az Azure Monitor az adatok figyelését három fő kategóriába sorolja: ez a **tevékenységnapló**, a **metrikák** és a **diagnosztikai naplók**.
3. A **Tevékenységnapló** lehetőségre kattintva jelenítse meg a tevékenységnapló területét.

    ![Tevékenységnapló panel](./media/monitoring-get-started/monitor-act-log-blade.png)

    A [**tevékenységnapló**](monitoring-overview-activity-logs.md) az előfizetéshez tartozó erőforrásokon végrehajtott összes műveletet ismerteti. A tevékenységnapló használatával az előfizetés erőforrásain végzett minden létrehozási, frissítési vagy törlési művelet esetén választ kaphat a „ki, mit és mikor” kérdésre. A tevékenységnaplóban láthatja például, hogy ki és mikor állított le egy webalkalmazást. A tevékenységnapló eseményeit tárolja a platform, és 90 napig lekérdezhetők maradnak.

    Létrehozhatja és mentheti a gyakran alkalmazott szűrőket tartalmazó lekérdezéseket, majd a legfontosabb lekérdezéseket rögzítheti a portál irányítópultján, így mindig tudni fogja, hogy előfordultak-e olyan események, amelyek megfelelnek a megadott feltételeknek.
4. Szűrje a nézetet egy adott erőforráscsoport múlt heti eseményeire szűkítve, majd kattintson a **Mentés** gombra.

    ![Tevékenységnapló lekérdezésének mentése](./media/monitoring-get-started/monitor-act-log-save.png)
5. Most kattintson a **Rögzítés** gombra.

    ![Kattintás a Rögzítés gombra a tevékenységnaplóban](./media/monitoring-get-started/monitor-act-log-pin.png)

    Az útmutatóban szereplő legtöbb nézet rögzíthető az irányítópulton. Ezt kihasználva létrehozhat egy olyan információs felületet, amelyet egyetlen forrásként használhat a szolgáltatásai működési adataihoz.
6. Térjen vissza az irányítópulthoz. Láthatja, hogy a lekérdezés (és az eredmények száma) megjelenik az irányítópulton. Ez akkor hasznos, ha azt szeretné, gyorsan megtekintheti az előfizetésében nemrég történt magas-profil műveleteket, például egy új szerepkört rendelték, vagy egy virtuális gép törölve lett.

    ![Tevékenységi naplóit rögzítve az irányítópulton](./media/monitoring-get-started/monitor-act-log-db.png)
7. Térjen vissza a **Monitor** csempére, majd kattintson a **Metrikák** szakaszra. Először válasszon ki egy erőforrástípust a szűrés, majd válassza a legördülő lista beállítások segítségével a panel tetején.

    ![Erőforrás szűrése metrikákhoz](./media/monitoring-get-started/monitor-met-filter.png)

    Minden Azure-erőforrás szolgáltat [**metrikákat**](monitoring-overview-metrics.md). Ez a nézet egyetlen panelen gyűjti össze az összes metrikát, így Ön könnyen elemezheti az erőforrások működését. Emellett, olvassa el a márka [élmény diagramkészítési új mérőszám](https://aka.ms/azuremonitor/new-metrics-charts) kattintva a **metrikák (előzetes verzió)** fülre.
8. Miután kiválasztott egy erőforrást, minden rendelkezésre álló metrika megjelenik a panel bal oldalán. A metrikák kiválasztásával, illetve a diagram típusának és az időtartománynak a módosításával egyszerre több metrikát is ábrázolhat. Az adott erőforráshoz beállított összes metrikariasztást is megtekintheti.

    ![Metrika panel](./media/monitoring-get-started/monitor-metric-blade.png)

   > [!NOTE]
   > Bizonyos metrikák csak akkor érhetők el, ha engedélyezi az [Application Insights](../application-insights/app-insights-overview.md) és/vagy a Windows- vagy Linux-alapú Azure Diagnostics szolgáltatást az erőforráson.
   >
   >
9. Ha a megjelenített diagrammal elégedett, a **Rögzítés** gombbal rögzítheti az irányítópulton.
10. Térjen vissza a **Monitor** panelre, és kattintson a **Diagnosztikai naplók** lehetőségre.

    ![Diagnosztikai naplók panel](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    A [**diagnosztikai naplók**](monitoring-overview-of-diagnostic-logs.md) olyan, egy-egy erőforrás *által* létrehozott naplók, amelyek az adott erőforrás műveleteire vonatkozó adatokkal szolgálnak. Például a hálózati biztonsági csoportok szabályainak számlálói, illetve a Logic Apps-munkafolyamatok naplói is a diagnosztikai naplók egy-egy típusát képezik. Ezek a naplók tárolhatók egy tárfiókban, közvetíthetők egy eseményközpontba, és/vagy elküldhetők a [Log Analytics](../log-analytics/log-analytics-overview.md) szolgáltatásba. A Log Analytics a Microsoft speciális keresésekhez és riasztásokhoz használható operatív információs terméke.

    A portálon megjelenítheti és szűrheti az előfizetés összes erőforrását annak megállapításához, hogy engedélyezve vannak-e ezekhez a diagnosztikai naplók.
11. Kattintson egy erőforrásra a Diagnosztikai naplók panelen. Ha a diagnosztikai naplókat egy tárfiókban tárolja, látni fogja az óránként létrehozott naplók listáját, és közvetlenül innen le is töltheti őket.

    ![Diagnosztikai naplók egy erőforráshoz](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    A **Diagnosztikai beállítások** lehetőségre kattintva beállíthatja vagy módosíthatja a tárfiókba való archiválás, az Event Hubs szolgáltatásba való streaming vagy a Log Analytics munkaterületére való küldés beállításait.

    ![Diagnosztikai naplók engedélyezése](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Ha a diagnosztikai naplóknak a Log Analytics szolgáltatásba való elküldését állította be, akkor a naplók között a portál **Naplók keresése** területén kereshet.
12. Keresse meg a Monitor panel **Riasztások** területét.

    ![Nyilvános riasztások panel](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Itt kezelheti az Azure-erőforrások összes [**riasztását**](monitoring-overview-alerts.md). Ez magában foglalja a riasztások a metrikákat, naplózási eseményeket, az Application Insights webalkalmazás-tesztek (hely) és az Application Insights proaktív diagnosztika. A riasztások küldhetnek e-mailt vagy egy HTTP POST-ot egy webhook URL-címre.
13. Kattintson az **Add metric alert** (Metrikariasztás hozzáadása) lehetőségre riasztás létrehozásához.

    ![Metrikariasztás hozzáadása](./media/monitoring-get-started/monitor-alerts-add.png)

    A riasztásokat ezután az irányítópulton rögzítheti, így bármikor egyszerűen megtekinthető az állapotuk.

    Az Azure figyelő most is rendelkezik [ **közel valós idejű metrika riasztások**](https://aka.ms/azuremonitor/near-real-time-alerts)(előzetes verzió), amely a lehető percenként gyakorisággal értékelhetők!
    
14. A Monitor terület emellett [Application Insights](../application-insights/app-insights-overview.md)-alkalmazásokra és [Log Analytics](../log-analytics/log-analytics-overview.md) felügyeleti megoldásokra mutató hivatkozásokat is tartalmaz. Ezek az egyéb Microsoft-termékek szervesen az Azure Monitor szolgáltatásba vannak integrálva.
15. Ha nem használja az Application Insights vagy a Log Analytics szolgáltatást, az Azure Monitor valószínűleg jelenlegi figyelési, naplózási és riasztási termékeivel működik együtt. Partnereink teljes listájához, illetve az integrálásra vonatkozó utasításokhoz tekintse meg [partneroldalunkat](monitoring-partners.md).

Ha a fenti lépéseket követi, és az összes releváns csempét rögzíti az irányítópulton, alkalmazásairól és infrastruktúrájáról ehhez hasonló átfogó nézeteket hozhat létre:

![Az Azure Monitor irányítópultja](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Következő lépések
* Olvassa el [az Azure Monitort áttekintő](monitoring-overview.md)cikket.
