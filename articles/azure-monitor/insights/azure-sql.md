---
title: Azure SQL Analytics-megoldás az Azure Monitorban | Microsoft dokumentumok
description: Az Azure SQL Analytics-megoldás segít az Azure SQL-adatbázisok kezelésében
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275463"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Az Azure SQL-adatbázis figyelése az Azure SQL Analytics használatával (előzetes verzió)

![Az Azure SQL Analytics szimbóluma](./media/azure-sql/azure-sql-symbol.png)

Az Azure SQL Analytics egy fejlett felhőfigyelő megoldás az összes Azure SQL-adatbázis teljesítményének figyelésére, egyetlen nézetben, több előfizetésben. Az Azure SQL Analytics összegyűjti és vizualizálja a legfontosabb teljesítménymutatókat a teljesítményhibaelhárításhoz beépített intelligenciával.

Ezekkel az összegyűjtött metrikák használatával egyéni figyelési szabályokat és riasztásokat hozhat létre. Az Azure SQL Analytics segítségével azonosíthatja a problémákat az alkalmazásverem minden rétegében. Az Azure Diagnosztikai metrikák at az Azure Monitor-nézetek használatával egyetlen Log Analytics-munkaterületen mutatja be az Összes Azure SQL-adatbázis adatait. Az Azure Monitor segítségével strukturált és strukturálatlan adatokat gyűjthet, korrelálhat és jeleníthetik meg.

Az Azure SQL Analytics-megoldás használatával és a tipikus használati forgatókönyvekkel kapcsolatos gyakorlati áttekintést a beágyazott videóban tekintheti meg:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Összekapcsolt források

Az Azure SQL Analytics egy csak felhőfigyelő megoldás, amely támogatja a diagnosztikai telemetriai adatok streamelését az összes Azure SQL-adatbázishoz. Mivel az Azure SQL Analytics nem használ ügynököket az Azure Monitorhoz való csatlakozáshoz, nem támogatja a helyszíni vagy virtuális gépeken üzemeltetett SQL Server figyelését.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| [Diagnosztikai beállítások](../platform/diagnostic-settings.md) | **Igen** | Az Azure-metrika- és naplóadatokat közvetlenül az Azure-naplók küldi el az Azure figyelőnaplóiba. |
| [Azure tárfiók](../platform/collect-azure-metrics-logs.md) | Nem | Az Azure Monitor nem olvassa be az adatokat egy tárfiókból. |
| [Windows-ügynökök](../platform/agent-windows.md) | Nem | A közvetlen Windows-ügynököket az Azure SQL Analytics nem használja. |
| [Linux-ügynökök](../learn/quick-collect-linux-computer.md) | Nem | A közvetlen Linux-ügynököket az Azure SQL Analytics nem használja. |
| [System Center Operations Manage felügyeleti csoport](../platform/om-agents.md) | Nem | Az Azure SQL Analytics nem használja az Operations Manager-ügynök és az Azure Monitor közötti közvetlen kapcsolatot. |

## <a name="azure-sql-analytics-options"></a>Az Azure SQL Analytics beállításai

Az alábbi táblázat ismerteti az Azure SQL Analytics irányítópult két verziójának támogatott lehetőségeit, az egyik az egy- és készletalapú adatbázisokés rugalmas készletek, a másik pedig a felügyelt példányok és a példányadatbázisok esetében.

| Az Azure SQL Analytics-beállítás | Leírás | Egy- és készletezésű adatbázis- és rugalmaskészletek támogatása | Felügyelt példányok és példányok adatbázis-támogatása |
| --- | ------- | ----- | ----- |
| Erőforrás típus szerint | Perspektíva, amely az összes figyelt erőforrást számolja. | Igen | Igen |
| Insights | Hierarchikus részletezést biztosít az Intelligens elemzési adatok teljesítménybe való beásása során. | Igen | Igen |
| Hibák | Hierarchikus részletezést biztosít az adatbázisokban előforduló SQL-hibákról. | Igen | Igen |
| Időtúllépések | Hierarchikus részletezést biztosít az adatbázisokban történt SQL idővizsgálatokba. | Igen | Nem |
| Blokkolások | Hierarchikus részletezést biztosít az adatbázisokban történt SQL-blokkolások ba. | Igen | Nem |
| Az adatbázis várakozik | Hierarchikus részletezést biztosít az adatbázis szintjén lévő SQL várakozási statisztikákba. Tartalmazza a teljes várakozási idő és a várakozási idő várakozási idő összegzését. |Igen | Nem |
| Lekérdezés időtartama | Hierarchikus részletezést biztosít a lekérdezés-végrehajtási statisztikákba, például a lekérdezés időtartamába, a PROCESSZOR-használatba, az adatok i/o-használatára, az Io-használat naplózására. | Igen | Igen |
| Lekérdezési várakozások | Hierarchikus részletezést biztosít a lekérdezés várakozási statisztikáiba várakozási kategória szerint. | Igen | Igen |

## <a name="configuration"></a>Konfiguráció

Az [Azure Monitor-megoldások hozzáadása a Megoldások galériából](../../azure-monitor/insights/solutions.md) című részben ismertetett folyamat segítségével hozzáadhatja az Azure SQL Analytics (előzetes verzió) szolgáltatást a Log Analytics-munkaterülethez.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Az Azure SQL-adatbázisok konfigurálása a diagnosztikai telemetria streamelésére

Miután létrehozta az Azure SQL Analytics-megoldást a munkaterületen, **konfigurálnia** kell minden egyes erőforrást, amelyet figyelni szeretne a diagnosztikai telemetriai adatok Azure SQL Analytics-be való streameléséhez. Kövesse a részletes utasításokat ezen az oldalon:

- Engedélyezze az Azure Diagnostics azure-beli SQL-adatbázisában, hogy [a diagnosztikai telemetriát továbbíthassa az Azure SQL Analytics szolgáltatásba.](../../sql-database/sql-database-metrics-diag-logging.md)

A fenti oldal is útmutatást nyújt a támogatás engedélyezéséhez több Azure-előfizetések egyetlen Azure SQL Analytics-munkaterület egyetlen üvegablakon.

## <a name="using-azure-sql-analytics"></a>Az Azure SQL Analytics használata

Amikor hozzáadja az Azure SQL Analytics-et a munkaterületéhez, az Azure SQL Analytics csempe hozzáadódik a munkaterülethez, és megjelenik az áttekintésben. A csempe tartalmának betöltéséhez válassza az Összegzés megtekintése hivatkozást.

![Az Azure SQL Analytics összefoglaló csempéje](./media/azure-sql/azure-sql-sol-tile-01.png)

Betöltés után a csempe az egy- és készletalapú adatbázisok, rugalmas készletek, felügyelt példányok és felügyelt példány-adatbázisok számát jeleníti meg, amelyekből az Azure SQL Analytics diagnosztikai telemetriai adatokat kap.

![Az Azure SQL Analytics csempéje](./media/azure-sql/azure-sql-sol-tile-02.png)

Az Azure SQL Analytics két külön nézetet biztosít – az egyik az egyes adatbázisok, a készletezett adatbázisok és rugalmas készletek figyelésére, a másik nézet a felügyelt példányok és a példányadatbázisok figyelésére.

Az azure-sql analytics figyelési irányítópultjának megtekintéséhez egy- és készletalapú adatbázisok és rugalmas készletek, kattintson a csempe felső részén. Az Azure SQL Analytics figyelési irányítópultjának felügyelt példányok és példányadatbázisok megtekintéséhez kattintson a csempe alsó részére.

### <a name="viewing-azure-sql-analytics-data"></a>Az Azure SQL Analytics-adatok megtekintése

Az irányítópult tartalmazza az összes adatbázis áttekintését, amelyek et különböző perspektívákon keresztül figyelnek. Ahhoz, hogy a különböző perspektívák működjenek, engedélyeznie kell a megfelelő metrikákat vagy naplókat az SQL-erőforrásokon a Log Analytics-munkaterületre való streameléshez.

Ha bizonyos metrikák vagy naplók nem streamelt az Azure Monitor, az Azure SQL Analytics csempéi nem töltődnek fel figyelési információkkal.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Egy- és készletezésű adatbázisok és rugalmas készletek nézete

Miután az Azure SQL Analytics csempe az adatbázis kiválasztott, a figyelési irányítópult jelenik meg.

![Az Azure SQL Analytics – áttekintés](./media/azure-sql/azure-sql-sol-overview.png)

Bármelyik csempét kijelölve megnyit egy részletező jelentést az adott perspektívába. A perspektíva kiválasztása után megnyílik a részletező jelentés.

![Az Azure SQL Analytics időtorásai](./media/azure-sql/azure-sql-sol-metrics.png)

Ebben a nézetben minden perspektíva összegzéseket biztosít az előfizetés, a kiszolgáló, a rugalmas készlet és az adatbázis szintjén. Ezen kívül minden perspektíva a jobb oldali jelentésre jellemző perspektívát jelenít meg. Az előfizetés, kiszolgáló, készlet vagy adatbázis listából történő kiválasztása a részletezést folytatja.

### <a name="managed-instance-and-instances-databases-view"></a>Felügyelt példány- és példánynézetek nézet

Miután az Azure SQL Analytics csempe az adatbázisok kiválasztása, a figyelési irányítópult jelenik meg.

![Az Azure SQL Analytics – áttekintés](./media/azure-sql/azure-sql-sol-overview-mi.png)

Bármelyik csempét kijelölve megnyit egy részletező jelentést az adott perspektívába. A perspektíva kiválasztása után megnyílik a részletező jelentés.

A felügyelt példány nézetének kiválasztása a felügyelt példány kihasználtságának részleteit, a benne lévő adatbázisokat és a példányon keresztül végrehajtott lekérdezések telemetriai adatait jeleníti meg.

![Az Azure SQL Analytics időtorásai](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Intelligens insights-jelentés

Az Azure SQL Database [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) segítségével megtudhatja, hogy mi történik az összes Azure SQL-adatbázis teljesítményével. Az összes összegyűjtött intelligens insights láthatóvá és elérhetőaz Insights perspektíva.

![Azure SQL Analytics Insights](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Rugalmas készletek és adatbázis-jelentések

Mind a rugalmas készletek és adatbázisok saját külön jelentések, amelyek az erőforrás a megadott időben gyűjtött összes adatot jelenítimeg.

![Azure SQL Analytics-adatbázis](./media/azure-sql/azure-sql-sol-database.png)

![Azure SQL rugalmas készlet](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Jelentések lekérdezése

A lekérdezés időtartamán keresztül, és a lekérdezés megvárja a perspektívákat, a lekérdezési jelentésen keresztül bármely lekérdezés teljesítményét korrelálhatja. Ez a jelentés összehasonlítja a lekérdezés teljesítményét a különböző adatbázisok között, és megkönnyíti a kijelölt lekérdezést jól végző adatbázisok azonosítását a lassúakkal szemben.

![Azure SQL Analytics-lekérdezések](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Engedélyek

Az Azure SQL Analytics használatához a felhasználóknak meg kell adni egy minimális engedélyt a Reader szerepkör az Azure-ban. Ez a szerepkör azonban nem teszi lehetővé a felhasználók számára a lekérdezés szövegének megtekintését vagy az automatikus hangolási műveletek elvégzését. Az Azure-ban az Azure-beli Tulajdonos, Közreműködő, SQL DB Közreműködő vagy SQL Server Közreműködő további megengedő szerepkörök használata a legteljesebb mértékben lehetővé teszi. Érdemes lehet egy egyéni szerepkört létrehozni a portálon, amely csak az Azure SQL Analytics használatához szükséges adott engedélyekkel rendelkezik, és nem fér hozzá más erőforrások kezeléséhez.

### <a name="creating-a-custom-role-in-portal"></a>Egyéni szerepkör létrehozása a portálon

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Felismerve, hogy egyes szervezetek szigorú engedélyszabályozást kényszerítenek ki az Azure-ban, keresse meg a következő PowerShell-parancsfájlt, amely lehetővé teszi egy egyéni "SQL Analytics Monitoring Operator" szerepkör létrehozását az Azure Portalon a minimális olvasási és írási engedélyekkel használja az Azure SQL Analytics a legteljesebb mértékben.

Cserélje le az alábbi parancsfájlban szereplő "{SubscriptionId}" elemet az Azure-előfizetés-azonosítójára, és hajtsa végre a tulajdonosként vagy közreműködői szerepkörként bejelentkezett parancsfájlt az Azure-ban.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Az új szerepkör létrehozása után rendelje hozzá ezt a szerepkört minden olyan felhasználóhoz, amelynek egyéni engedélyeket kell adnia az Azure SQL Analytics használatához.

## <a name="analyze-data-and-create-alerts"></a>Adatok elemzése és riasztások létrehozása

Az Azure SQL Analytics adatelemzése az egyéni lekérdezések és jelentések [Log Analytics nyelvén](../log-query/get-started-queries.md) alapul. Az adatbázis-erőforrásból az egyéni lekérdezéshez a rendelkezésre álló adatok leírása [a rendelkezésre álló mérőszámokban és naplókban.](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available)

Az Azure SQL Analytics automatikus riasztása egy Log Analytics-lekérdezés írásán alapul, amely egy feltétel teljesülése esetén riasztást vált ki. Az alábbiakban több példát talál a Log Analytics-lekérdezések, amelyek riasztási lehet beállítani az Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Riasztások létrehozása az Azure SQL Database-hez

Egyszerűen hozhat [létre riasztásokat](../platform/alerts-metric.md) az Azure SQL Database-erőforrásokból származó adatokkal. Íme néhány hasznos [naplólekérdezés,](../log-query/log-query-overview.md) amelyet naplóriasztással használhat:

#### <a name="high-cpu-on-azure-sql-database"></a>Magas processzor az Azure SQL-adatbázisban

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok alapvető metrikákat továbbítsanak az Azure SQL Analytics szolgáltatásba.
> - Cserélje le a MetricName cpu_percent dtu_consumption_percent magas DTU-eredmények elérése helyett.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>Magas processzor az Azure SQL Database rugalmas készletein

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok alapvető metrikákat továbbítsanak az Azure SQL Analytics szolgáltatásba.
> - Cserélje le a MetricName cpu_percent dtu_consumption_percent magas DTU-eredmények elérése helyett.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Az Azure SQL Database tárolása átlagosan 95% felett az elmúlt 1 órában

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok alapvető metrikákat továbbítsanak az Azure SQL Analytics szolgáltatásba.
> - Ez a lekérdezés megköveteli, hogy egy riasztási szabályt kell beállítani, hogy tűz ki egy riasztást, ha vannak eredmények (> 0 eredmények) a lekérdezés, amely azt jelenti, hogy a feltétel létezik egyes adatbázisokban. A kimenet az adatbázis-erőforrások listája, amelyek a storage_threshold felett vannak a megadott time_range belül.
> - A kimenet az adatbázis-erőforrások listája, amelyek a storage_threshold felett vannak a megadott time_range belül.

#### <a name="alert-on-intelligent-insights"></a>Riasztás az intelligens elemzésekről

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt adatbázisok streameljék az SQLInsights diagnosztikai naplót az Azure SQL Analytics szolgáltatásba.
> - Ez a lekérdezés megköveteli, hogy egy riasztási szabályt kell beállítani, hogy fut az azonos gyakorisággal, mint alert_run_interval, hogy elkerüljék az ismétlődő eredményeket. A szabályt úgy kell beállítani, hogy a riasztást, ha vannak eredmények (> 0 eredmények) a lekérdezés.
> - A alert_run_interval testreszabásával megadhatja, hogy a feltétel az SQLInsights-napló Azure SQL Analytics-be való streamelésére konfigurált adatbázisokon történt-e.
> - A insights_string testreszabásával rögzítsd az Insights kiváltó okelemzési szövegének kimenetét. Ez ugyanaz a szöveg jelenik meg az Azure SQL Analytics felhasználói felületén, amelyet a meglévő elemzésekből használhat. Másik lehetőségként használhatja az alábbi lekérdezést az előfizetésén létrehozott összes insights szövegének megtekintéséhez. A lekérdezés kimenetét használja a különböző karakterláncok begyűjtéséhez riasztások beállítása insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Riasztások létrehozása felügyelt példányokhoz

#### <a name="managed-instance-storage-is-above-90"></a>A felügyelt példánytárolás meghaladja a 90%-ot

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy figyelte a felügyelt példány a ResourceUsageStats napló streamelése engedélyezve van az Azure SQL Analytics.
> - Ez a lekérdezés megköveteli, hogy egy riasztási szabályt kell beállítani, hogy tűz ki egy riasztást, ha vannak eredmények (> 0 eredmények) a lekérdezés, amely azt jelenti, hogy a feltétel létezik a felügyelt példány. A kimenet a felügyelt példány tárolási százalékos felhasználása.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>Felügyelt példány CPU átlagos fogyasztása meghaladja a 95%-ot az elmúlt 1 óra

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - A riasztás beállításának előfeltétele, hogy a figyelt felügyelt példány rendelkezik a ResourceUsageStats napló streamelése engedélyezve van az Azure SQL Analytics.
> - Ez a lekérdezés megköveteli, hogy egy riasztási szabályt kell beállítani, hogy tűz ki egy riasztást, ha vannak eredmények (> 0 eredmények) a lekérdezés, amely azt jelenti, hogy a feltétel létezik a felügyelt példány. A kimenet a felügyelt példány meghatározott időszakában a CPU-kihasználtság imperalista átlagos százaléka.

### <a name="pricing"></a>Díjszabás

Bár az Azure SQL Analytics szabadon használható, a diagnosztikai telemetriai adatok felhasználása meghaladja a havonta lefoglalt szabad adatbetöltési egységeket, lásd: [Log Analytics-díjszabás.](https://azure.microsoft.com/pricing/details/monitor) Az ingyenes adatbetöltési egységek havonta több adatbázis ingyenes figyelését teszik lehetővé. Aktívabb adatbázisok nagyobb számítási feladatok több adat és tétlen adatbázisok betöltése. Az Azure SQL Analytics szolgáltatásban egyszerűen figyelheti az adatok betöltésének felhasználását, ha az Azure SQL Analytics navigációs menüjében az OMS-munkaterületet választja, majd a Használati és becsült költségek lehetőséget választja.

## <a name="next-steps"></a>További lépések

- Az Azure Monitor [naplólekérdezései](../log-query/log-query-overview.md) segítségével részletes Azure SQL-adatokat tekinthet meg.
- [Hozzon létre saját irányítópultokat az](../learn/tutorial-logs-dashboards.md) Azure SQL-adatokkal.
- [Riasztások létrehozása,](../platform/alerts-overview.md) ha adott Azure SQL-események fordulnak elő.
