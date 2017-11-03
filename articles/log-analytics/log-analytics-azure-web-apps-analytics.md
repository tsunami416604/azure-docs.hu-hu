---
title: "Azure Web Apps analitikai adatok megtekintése |} Microsoft Docs"
description: "Használhatja az Azure Web Apps elemzési megoldások kapcsolódva információt kaphat a Azure Web Apps kapcsolatos különböző metrikák begyűjtenie az Azure-webalkalmazás-erőforrások között."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.openlocfilehash: 9ef26d4b6bfd92925a70b7bbdf8979e287c73445
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Az Azure-webalkalmazás-erőforrások között metrikáihoz analitikai adatok megtekintése

![Web Apps szimbólum](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
Az Azure Web Apps Analytics (előzetes verzió) megoldás betekintést biztosít a [Azure Web Apps](../app-service/app-service-web-overview.md) különböző metrikák begyűjtenie az Azure-webalkalmazás-erőforrások között. A megoldás elemezheti és web app erőforrás metrika adatok kereséséhez.

A segítségével megtekintheti a:

- A legmagasabb válaszidő felső webalkalmazások
- A webes alkalmazások között, beleértve a sikeres és sikertelen kérelmek kérelmek száma
- A legmagasabb bejövő és kimenő forgalmat felső webalkalmazások
- A magas Processzor- és memóriafelhasználását felső szolgáltatáscsomagok
- Az Azure Web Apps tevékenység napló üzemeltetése

## <a name="connected-sources"></a>Összekapcsolt források

Legtöbb egyéb Naplóelemzési megoldásoktól eltérően adatokat nem gyűjtötte be a Azure Web Apps-ügynökök által. A megoldás által használt összes adatok származnak, közvetlenül az Azure-ból.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Windows-ügynökök](log-analytics-windows-agents.md) | Nem | A megoldás a Windows-ügynökök nem gyűjt adatokat. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | A megoldás a Linux-ügynökök nem gyűjt adatokat. |
| [SCOM felügyeleti csoport](log-analytics-om-agents.md) | Nem | A megoldás nem gyűjt adatokat az ügynökök a csatlakoztatott SCOM felügyeleti csoport. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A megoldás nem az Azure storage nem gyűjtemény adatait. |

## <a name="prerequisites"></a>Előfeltételek

- Azure Web Apps metrika erőforrásadatok eléréséhez Azure-előfizetéssel kell rendelkeznie.

## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépésekkel állíthatja be az Azure Web Apps Analytics megoldás a munkaterületek.

1. Engedélyezze az Azure Web Apps Analytics megoldást [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).
2. [Az OMS Szolgáltatáshoz a PowerShell használatával Azure erőforráscsoport metrikák naplózását](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

Az Azure Web Apps elemzési megoldások metrikák két készletét gyűjti az Azure-ból:

- Az Azure Web Apps metrikák
  - Munkakészlet átlagos memória
  - Átlagos válaszidő
  - Küldött vagy fogadott bájtok
  - CPU-idő
  - Kérelmek
  - Munkakészlet memória
  - Httpxxx
- App Service-csomag metrikák
  - Küldött vagy fogadott bájtok
  - Processzorhasználat (%)
  - Lemezvárólista hossza
  - HTTP-várólista hossza
  - Memóriahasználat (%)

App Service-csomag metrikái összegyűjtése csak egy dedikált service-csomag használata. Ez nem vonatkozik ingyenes vagy közös App Service-csomagokról.

A megoldás az OMS-portálon való hozzáadásakor a következő csempe láthatja. Kell [az OMS Szolgáltatáshoz a PowerShell használatával Azure erőforráscsoport metrikák naplózását](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

![Értékelés értesítési végrehajtása](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

Miután konfigurálta a megoldás, adatokat kell kezdődnie, 15 percen belül a munkaterület halad.

## <a name="using-the-solution"></a>A megoldás használata

Az Azure Web Apps elemzési megoldások a munkaterülethez való hozzáadásakor a **Azure Web Apps Analytics** csempe hozzáadódik az áttekintő irányítópulthoz. Ez a csempe, hogy a megoldás fér hozzá az Azure-előfizetéshez az Azure Web Apps számát jeleníti meg.

![Az Azure Web Apps Analytics csempe](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Azure Web Apps Analytics adatainak megtekintése

Kattintson a **Azure Web Apps Analytics** csempére kattintva nyissa meg a **Azure Web Apps Analytics** irányítópult. Az irányítópult a paneleket az alábbi táblázat tartalmazza. Minden panel adott panelhez feltételeknek, a megadott hatókör és időtartomány legfeljebb tíz elemeket sorolja fel. A napló keresési, amely visszaadja az összes rekord kattintva futtathatja **láthatja az összes** alján a panelről, vagy kattintson a panel fejléc.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| Oszlop | Leírás |
| --- | --- |
| Az Azure-webalkalmazás |   |
| Web Apps kérelem trendek | A kijelölt dátumtartomány webalkalmazások kérelem trendje sor látható, és a felső tíz webes kérelmek listáját tartalmazza. Kattintson a vonaldiagram futtatásához napló keresése<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> <br>Kattintson egy webes kérelem elem a napló keresése a webes kérelem metrika trend kérő futtatásához. |
| Web Apps válaszidő | A kijelölt időtartományban webalkalmazások válaszidő sor diagramot ábrázol. Is felső listája listájának megjelenítése tíz webes alkalmazások response alkalommal fordult elő. Kattintson a diagram futtatásához napló keresése<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code><br> Kattintson a válaszidők vissza a webalkalmazáshoz tartozó napló keresés futtatásához egy webalkalmazást. |
| Webes alkalmazások forgalom | A Web Apps-forgalmat, vonaldiagram mutatja MB-ban, és webalkalmazások forgalom felső sorolja fel. Kattintson a diagram futtatásához napló keresése<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code><br> Azt az elmúlt percben forgalom minden webalkalmazások jelennek meg. Kattintson a napló keresés kapott, és a webalkalmazás számára küldött bájtok megjelenítő futtatásához a webes alkalmazás. |
| Az Azure App Service-csomagok |   |
| App Service-csomagok a CPU-felhasználás &gt; 80 %-át | App Service-csomagok, amelyek CPU-kihasználtsága 80 %-nál nagyobb teljes számát jeleníti meg, és felsorolja a CPU-kihasználtság szerinti a felső 10 App Service-csomagok. Kattintson a napló keresése futtatásához teljes terület<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code><br> Ez az App Service-csomagok és az átlagos processzorhasználat listáját tartalmazza. Kattintson egy App Service-csomag az átlagos processzorhasználat megjelenítő napló keresés futtatásához. |
| App Service-csomagok memóriahasználata a &gt; 80 %-át | App Service-csomagok, amelyek memória kihasználtsága 80 %-nál nagyobb teljes számát jeleníti meg, és felsorolja a memóriahasználat által a felső 10 App Service-csomagok. Kattintson a napló keresése futtatásához teljes terület<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code><br> Ez az App Service-csomagok és az átlagos memóriafelhasználás a listáját jeleníti meg. Kattintson egy App Service-csomag megjelenítő, az átlagos memóriafelhasználás a napló keresés futtatásához. |
| Az Azure Web Apps tevékenységi naplóit |   |
| Az Azure Web Apps tevékenység naplózása | Webalkalmazások teljes számát jeleníti meg [tevékenységi naplóit](log-analytics-activity.md) és az első 10 tevékenység napló műveleteinek listázása. Kattintson a napló keresése futtatásához teljes terület<code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code><br> Ez a tevékenység napló műveletek listáját jeleníti meg. Kattintson a napló-keresés, amely tartalmazza a rekordok a művelet futtatásához egy tevékenység log műveletet. |



### <a name="azure-web-apps"></a>Azure Web Apps

Az irányítópult részletezve további webhelynaplókat a Web Apps metrikákat. Ez először állítsa a paneleken megjelenítése a tendencia, a Web Apps kérések számát hibák (például HTTP404), a forgalom és az átlagos válaszidő adott idő alatt. Különböző Web Apps e metrikák részletes információkat is látható.

![Az Azure webalkalmazás panelen](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

Egy elsődleges adatok jelennek meg, hogy adott oka, hogy magas válaszidő segítségével azonosíthatók a webes alkalmazás, és ellenőrizze, hogy az alapvető okát. Annak érdekében, hogy könnyen azonosítható kapcsolatos problémákkal rendelkező megfelelően is vonatkozik egy küszöbértéket.

- Webalkalmazások vörösen láthatók rendelkezik válaszidő magasabb, mint 1 másodperc.
- Web Apps narancssárga látható válaszidejének 0,7 második és kisebb, mint 1 másodperc nagyobbnak.
- Webalkalmazások zölden láthatók második rendelkezik 0,7-nál kisebb a válaszidőt.

Az alábbi ábrán napló keresési példa, láthatja, hogy a *anugup3* webalkalmazás kellett egy sokkal nagyobb válaszideje, mint a más webes alkalmazásokat.

![naplófájl-keresési példa](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>App Service-csomagok

Dedikált Service-csomagok használ, ha az App Service-csomagok esetében is begyűjtheti a metrikák. Ebben a nézetben látható az App Service-csomagok és magas CPU és memória kihasználtsága (&gt; 80 %-ot). Bemutatja azt is, a felső alkalmazásszolgáltatások magas memóriát és CPU-kihasználtsága a. Hasonlóképpen egy küszöbértéket annak érdekében, hogy könnyen azonosítható kapcsolatos problémákkal rendelkező megfelelően vonatkozik.

- App Service-csomagok vörösen láthatók a Processzor/memória kihasználtsága 80 %-nál nagyobb lehet.
- App Service-csomagokról narancssárga látható memóriakihasználtsága a CPU/magasabb, mint 60 % és 80 %-nál kisebb.
- App Service-csomagok zölden láthatók memóriakihasználtsága a CPU/alacsonyabb, mint 60 %-át.

![Az Azure App Service-csomagok paneleken](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Az Azure Web Apps napló keresések

A **lista a népszerű Azure Web Apps keresési lekérdezések** elsajátíthatja, hogy a kapcsolódó tevékenység naplókat Web Apps, amely a webalkalmazások erőforrásokon végrehajtott műveletek betekintést biztosít. A kapcsolódó műveleteket és a sor került ilyenre hányszor is tartalmazza.

Használja a naplófájl-keresési lekérdezések kiindulási pontként, könnyedén létrehozhat egy riasztást. Például előfordulhat, hogy szeretné egy riasztás létrehozása, ha egy metrika az átlagos válaszidő érték nagyobb, mint 1 másodpercenként.

## <a name="next-steps"></a>Következő lépések

- Hozzon létre egy [riasztás](log-analytics-alerts-creating.md) az adott metrika.
- Használjon [naplófájl-keresési](log-analytics-log-searches.md) a tevékenységi naplóit a részletes információk megtekintéséhez.
