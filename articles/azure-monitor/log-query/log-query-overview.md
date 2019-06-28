---
title: Napló áttekintése lekérdezések az Azure Monitor |} A Microsoft Docs
description: Lekérdezések kapcsolatos gyakori kérdésekre adott válaszokat, és megkezdheti a használatukat.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310344"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Az Azure Monitor log-lekérdezések áttekintése
Napló lekérdezések segítségével teljes mértékben kihasználják a gyűjtött adatok értékét [Azure Monitor naplóira](../platform/data-platform-logs.md). Egy hatékony lekérdezési nyelvet lehetővé teszi több táblából származó adatokat, Nagy adatkészleteken, összesítéséhez és minimális programozással összetett műveleteket végez. Gyakorlatilag bármilyen kérdésre választ, és elemzési végre mindaddig, amíg a kapcsolódó adatokat összegyűjtötte-e, és megismerheti a megfelelő lekérdezés létrehozása.

Egyes funkciók az Azure monitorban például [insights](../insights/insights-overview.md) és [megoldások](../insights/solutions-inventory.md) anélkül, hogy Ön az alapul szolgáló lekérdezések Teljesítménynapló-adatok feldolgozása. Teljes mértékben kihasználják a funkciókat a Azure monitor, tisztában kell lennie a lekérdezések tevődnek hogyan, és hogyan használhatja ezeket interaktív módon az Azure Monitor naplóira lévő adatok elemzéséhez.

Ez a cikk kiindulási pontot annak learning az Azure Monitor log-lekérdezésekkel kapcsolatos használja. Gyakori kérdésekre is válaszol, és egyéb dokumentáció, amely további részleteket és a leckék mutató hivatkozásokat tartalmaz.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hogyan lehet megtudhatja, hogyan kell lekérdezéseket írni?
Ha át szeretné ugrani dolgot be, az alábbi oktatóanyagok megkezdése:

- [Ismerkedés a Log Analytics az Azure Monitor](get-started-portal.md).
- [Ismerkedés az Azure monitorban naplólekérdezések](get-started-queries.md).

Miután az alapokat le, adatokat vagy a saját adatait a bemutató környezetben kezdve több leckék végig: 

- [Munka karakterláncokkal az Azure Monitor log-lekérdezések](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Milyen nyelven tegye jelentkezzen lekérdezések használata?
Az Azure Monitor naplóira alapján [Azure adatkezelő](/azure/data-explorer), és naplólekérdezések írt azonos Kusto lekérdezési nyelv (KQL) segítségével. Ez egy biztosított a gazdag nyelvi úgy kialakítva, hogy könnyen olvasható és Szerző, és minimális útmutatással használatának megkezdéséhez képesnek kell lennie.

Lásd: [dokumentáció az Azure Data Explorer KQL](/azure/kusto/query) KQL és különböző funkciók érhető el a referencia teljes dokumentációját.<br>
Lásd: [az Azure Monitor log-lekérdezések használatának első lépései](get-started-queries.md) az Azure Monitor naplóira származó nyelv rövid leírását.
Lásd: [Azure Monitor log lekérdezési nyelv különbségek](data-explorer-difference.md) kisebb különbségek KQL Azure figyelő által használt verziójában.

## <a name="what-data-is-available-to-log-queries"></a>Milyen adatokat lekérdezések naplózását érhető el?
Az Azure Monitor naplóira gyűjtött összes adat beolvasása és elemzése a log lekérdezésekben érhető el. Különböző adatforrások különböző táblák beállításfájlra adataikat, de több táblát is felvehet egyetlen lekérdezést küldenek az adatok elemzése több forráson. Ha egy lekérdezést hoz létre, akkor meghozatalához mely táblákat keres, adatok rendelkezik, így a rendelkeznie kell legalább egy hogyan épül fel adatokat az Azure Monitor naplóira alapvető ismeretekkel.

Lásd: [források az Azure Monitor naplóira](../platform/data-platform-logs.md#sources-of-azure-monitor-logs), különböző adatok listáját, amelyek adatforrások esetén az Azure Monitor naplóira feltöltéséhez.<br>
Lásd: [struktúra az Azure Monitor naplóira](logs-structure.md) működéséről, hogyan épül fel az adatokat.

## <a name="what-does-a-log-query-look-like"></a>Milyen kinéznie log lekérdezést?
Lehet, hogy egy lekérdezés beállítható, hogy a táblázat az összes rekord beolvasása céljából egyetlen tábla neve:

```Kusto
Syslog
```

Vagy sikerült adott rekordok szűrése, foglalják össze azokat, és megjelenítheti az eredményeket a diagramon:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Az összetettebb elemzésekre előfordulhat, hogy kérheti le az adatok több táblájából az illesztés segítségével együtt elemezze az eredményeket.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Akkor is, ha nem ismeri a KQL, tudja az ilyen lekérdezések által használt logikai legalább azonosítani kell lennie. Azok a táblázat neve kezdődhet, és adja hozzá a szűrésére és feldolgozni az adatokat több parancsot. A lekérdezés tetszőleges számú parancsokat használhatja, és, megismerkedhet a különböző KQL parancsok a összetettebb lekérdezéseket írhat.

Lásd: [az Azure Monitor log-lekérdezések használatának első lépései](get-started-queries.md) naplólekérdezések oktatóanyagot, amely bemutatja a nyelv és az általános funkciókat.<br>


## <a name="what-is-log-analytics"></a>Mi az a Log Analytics?
A log Analytics naplóbeli lekérdezések írása, és interaktív elemzéséhez az eredményeket az Azure Portalon az elsődleges eszköz. Akkor is, ha egy naplólekérdezés máshol használja az Azure Monitor, általában írása, és a lekérdezés teszteléséhez először a Log Analytics szolgáltatást.

A Log Analytics az Azure Portalon, több helyről is elindítható. A Log Analytics számára elérhető adatok köre el azt határozza meg. Lásd: [lekérdezés hatókörébe](scope.md) további részletekért.

- Válassza ki **naplók** származó a **Azure Monitor** menü vagy **Log Analytics-munkaterületek** menü.
- Válassza ki **Analytics** származó a **áttekintése** oldal az Application Insights-alkalmazás.
- Válassza ki **naplók** egy Azure-erőforrás menüjéből.

![Log Analytics](media/log-query-overview/log-analytics.png)

Lásd: [Ismerkedés a Log Analytics az Azure Monitor](get-started-portal.md) Log Analytics, amely bemutatja az a funkciók számos oktatóanyag leírását.

## <a name="where-else-are-log-queries-used"></a>Hol találhatok még használnak a naplólekérdezések?
Mellett interaktív módon naplólekérdezések és a Log Analytics eredményeik dolgozik, amelyben lekérdezések használatát az Azure Monitor a területek a következők:

- **Riasztási szabályok.** [Riasztási szabályok](../platform/alerts-overview.md) proaktív módon azonosíthatja a problémákat a munkaterületen lévő adatai.  Minden riasztási szabály naplókeresést, amely rendszeres időközönként automatikusan fut alapul.  Határozza meg, ha egy riasztást kell létrehozni a ellenőrzik az eredményeket.
- **Az irányítópultok.** Kitűzheti, minden lekérdezés eredményeit egy [Azure irányítópultján](../learn/tutorial-logs-dashboards.md) Ez lehetővé teszi annak együtt megjelenítheti a napló-és metrikaadatokat, és szükség esetén megoszthatja más Azure-felhasználóval.
- **Nézetek.**  Adatok foglalandó felhasználói irányítópultokat és vizualizációkat hozhat létre [adatforrásnézet-tervezőből](../platform/view-designer.md).  Naplólekérdezések adja meg az adatok által használt [csempék](../platform/view-designer-tiles.md) és [Vizualizáció részek](../platform/view-designer-parts.md) az egyes nézetek.  
- **Exportálás.**  Importálásakor Teljesítménynapló-adatok az Azure Monitor az Excelbe vagy [Power BI](../platform/powerbi.md), az adatok exportálása meghatározásához log lekérdezés létrehozása.
- **PowerShell.** PowerShell-szkriptet a parancssorban vagy egy Azure Automation-runbook által használt futtatható [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) naplóadatok lekérése az Azure Monitor.  Ez a parancsmag egy lekérdezést a lekérni kívánt meghatározásához szükséges.
- **Az Azure Monitor naplók API.**  A [Azure Monitor naplók API](../platform/alerts-overview.md) lehetővé teszi, hogy bármilyen REST API-ügyfél naplóadatok lekérése a munkaterületen.  Az API-kérelem tartalmaz egy lekérdezést, amely a lekérni kívánt meghatározni az Azure Monitor vonatkozóan fut le.


## <a name="next-steps"></a>További lépések
- Végig egy [a Log Analytics szolgáltatást az Azure Portal oktatóanyag](get-started-portal.md).
- Végig egy [oktatóanyag lekérdezések írásáról](get-started-queries.md).
