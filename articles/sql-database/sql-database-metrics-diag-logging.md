---
title: Az Azure SQL Database-metrikák és diagnosztikai naplózás |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az Azure SQL Database erőforrás-használat és a lekérdezés végrehajtási statisztika tárolásához.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: f023ab4fe55cf180ac1e3f0634856a528c911746
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426500"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Az Azure SQL Database-metrikák és diagnosztikai naplózás

Önálló adatbázisok, rugalmas készletek a készletezett adatbázisok és példányok adatbázisai a felügyelt példány is könnyebben alkalmazásteljesítmény-figyelés a metrikák és diagnosztikai naplók streamelése. Az adatbázis erőforrás-használat, a dolgozók és a munkamenetek és a egy, a következő Azure-erőforrások kapcsolat továbbítására konfigurálhatja:

- **Az Azure SQL Analytics**: intelligens monitorozást az Azure SQL Database-adatbázisok, amely tartalmazza az teljesítményjelentések készítésére, riasztások és javaslatok kockázatcsökkentési beolvasásához.
- **Az Azure Event Hubs**: SQL Database telemetriai adatainak integrálása saját egyedi monitorozási megoldásokkal vagy élő adatfolyamatokkal.
- **Az Azure Storage**: archiválására hatalmas mennyiségű telemetriai árának töredékéért.

    ![Architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

A különböző Azure-szolgáltatások által támogatott mérőszámokban és naplófájlokban kategóriák kapcsolatos további információkért lásd:

- [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure-beli diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md)

Ez a cikk nyújt útmutatást az Azure SQL adatbázisok, rugalmas készletek és a felügyelt példányok diagnostics telemetriai adatainak engedélyezéséhez. Azt is segítségével megtudhatja, hogyan konfigurálhat egy Azure SQL Analytics egy figyelési eszköz adatbázis diagnostics telemetriai adatainak megtekintéséhez.

## <a name="enable-logging-of-diagnostics-telemetry"></a>A telemetria diagnosztikai naplózás engedélyezése

Engedélyezheti és kezelheti a metrikák és diagnosztikai telemetriai naplók az alábbi módszerek egyikének használatával:

- Azure Portal
- PowerShell
- Azure CLI
- Az Azure Monitor REST API-val
- Azure Resource Manager-sablon

Ha engedélyezi a metrikák és diagnosztikai naplózás, kell adja meg az Azure-erőforrás cél a diagnostics telemetriai adatainak gyűjtéséhez. Elérhető lehetőségek a következők:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Új Azure-erőforrás kiépítéséhez, vagy egy meglévő erőforrás kiválasztása. Az erőforrás kiválasztása után a **diagnosztikai beállítások** lehetőségnél adja meg az adatok gyűjtéséhez.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Diagnosztikai naplózás támogatott az Azure SQL Database-adatbázisok és példányok adatbázisai

A metrikák és diagnosztikai SQL-adatbázis naplózásának engedélyezése – nincs alapértelmezés szerint van engedélyezve.

A következő diagnostics telemetriai adatainak gyűjtéséhez állíthat be az Azure SQL Database-adatbázisok és példányok adatbázisai:

| Telemetria adatbázisok figyelése | Önálló adatbázis és a készletezett adatbázisok támogatása | Példány adatbázisok támogatása |
| :------------------- | ----- | ----- |
| [Minden metrika](#all-metrics): Dtu-k/Processzorhasználat (%), a dtu-k/CPU-korlát, a fizikai tartalmaz napló írási adatok olvasása a százalékos aránya, százalékos aránya, sikeres/sikertelen/letiltott által tűzfalkapcsolatok, munkamenetek százaléka, feldolgozók százalékos aránya, storage, storage, és XTP tároló (%). | Igen | Nem |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): A lekérdezések futásidejének statisztikai adatait, például a CPU-használat és a lekérdezés időtartama statisztikai információkat tartalmaz. | Igen | Igen |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): A lekérdezés várakozási statisztika (Mi a lekérdezések megvárta) kapcsolatos információkat tartalmaz például a Processzor, a napló és a ZÁROLÁS is. | Igen | Igen |
| [Hibák](#errors-dataset): Az adatbázisok SQL-hibákkal kapcsolatos információkat tartalmaz. | Igen | Igen |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Egy adatbázis töltött a különböző várakozási típusok vár mennyi ideig kapcsolatos információt tartalmazza. | Igen | Nem |
| [Időtúllépések](#time-outs-dataset): Egy adatbázis időtúllépések információkat tartalmazza. | Igen | Nem |
| [Blokkok](#blockings-dataset): Blokkoló események az adatbázisok kapcsolatos információt tartalmazza. | Igen | Nem |
| [Holtpontok](#deadlocks-dataset): Az adatbázisok holtpont eseményekkel kapcsolatos információkat tartalmazza. | Igen | Nem |
| [AutomaticTuning](#automatic-tuning-dataset): Az adatbázis automatikus finomhangolási ajánlásait kapcsolatos információt tartalmazza. | Igen | Nem |
| [SQLInsights](#intelligent-insights-dataset): Intelligent Insights adatbázis teljesítményéről tartalmaz. További tudnivalókért lásd: [Intelligent Insights](sql-database-intelligent-insights.md). | Igen | Igen |

> [!IMPORTANT]
> Rugalmas készletek és a felügyelt példány rendelkezik a saját külön diagnostics telemetriai adatainak tartalmaznak adatbázisokból. Ez fontos megjegyezni a diagnostics telemetriai adatainak külön-külön vannak beállítva ezen erőforrások mindegyike módon lentebb.

> [!NOTE]
> Biztonsági naplózás és SQLSecurityAuditEvents naplók nem lehet engedélyezni az adatbázis diagnosztikai beállítások (bár a képernyőn látható). Engedélyezheti a naplózási naplóstreamelés [beállítása az adatbázis naplózási](sql-database-auditing.md#subheading-2), és [naplózás az Azure Monitor naplóira és az Azure Event Hubs-naplók](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).

## <a name="azure-portal"></a>Azure Portal

Használhat **diagnosztikai beállítások** menüje minden egyes, a készletbe vont, vagy az Azure Portalon konfigurálhatja a diagnostics telemetriai adatainak adatfolyamként adatbázis-példány. Emellett telemetria diagnosztikája is külön konfigurálható az adatbázis-tárolókhoz: rugalmas készletek és a figyelt példányokat. A következő célok a diagnostics telemetriai adatainak streamelésére állíthatja be: Az Azure Storage, az Azure Event Hubs és az Azure Monitor naplókat.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurálja a diagnostics telemetriai adatainak a rugalmas készletekhez adatfolyamként

   ![Rugalmas készlet ikon](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

A következő diagnostics telemetriai adatainak gyűjtéséhez állíthat be egy rugalmas készlet egyenlő erőforrás:

| Erőforrás | Telemetriai adatok figyelése |
| :------------------- | ------------------- |
| **Rugalmas készlet** | [Minden metrika](sql-database-metrics-diag-logging.md#all-metrics) eDTU/Processzorhasználat (%), eDTU/CPU-korlát, fizikai tartalmazza az beolvasott adatok százalékos aránya, napló írási százalékos, munkamenetek százaléka, feldolgozók százalékos, storage, tárolási százalékos, tárolási kapacitása és XTP tárolási százalékos aránya. |

Adatfolyam-telemetria diagnosztikai a rugalmas készletek és rugalmas készletekben található adatbázisokat konfigurálásához kell külön konfigurálni **mindkét** a következők közül:

- Rugalmas készletek, a diagnostics telemetriai adatainak streamelésének engedélyezéséhez **és**
- A rugalmas készletben lévő egyes adatbázisokhoz a diagnostics telemetriai adatainak streamelésének engedélyezéséhez

Ennek oka az, egy adatbázis-tárolóban a saját telemetriát külön az egyes adatbázis-telemetria a rugalmas készlet használata.

Rugalmas készlet erőforrás diagnostics telemetriai adatainak streamelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a **rugalmas készlet** erőforrását az Azure Portalon.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.

   ![Engedélyezze a diagnosztikát a rugalmas készletekhez](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. Válassza ki a log Analytics, **konfigurálása** , és hozzon létre egy új munkaterületet kiválasztásával **+ létrehozás új munkaterület**, vagy válasszon ki egy meglévő munkaterületet.
1. Válassza ki a rugalmas készlet diagnostics telemetriai adatainak jelölőnégyzetét: **AllMetrics**.
   ![Rugalmas készletek diagnosztika konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Kattintson a **Mentés** gombra.
1. Emellett konfigurálhatja adatfolyam-diagnosztika telemetria hajtsa végre a következő szakaszban leírt figyelni szeretné a rugalmas készleten belül minden egyes adatbázishoz.

> [!IMPORTANT]
> Azonkívül, hogy a rugalmas készletek a diagnostics telemetriai adatainak, is kell konfigurálnia az egyes adatbázisok diagnostics telemetriai adatainak a rugalmas készlet módon lentebb. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Adatfolyam-diagnosztika telemetria egyetlen adatbázishoz vagy rugalmas készlet adatbázis konfigurálása

   ![Az SQL Database ikonja](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Az önálló vagy készletezett adatbázisok diagnostics telemetriai adatainak streamelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure-bA **SQL-adatbázis** erőforrás.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.
   - Stream diagnostics telemetriai adatainak legfeljebb három párhuzamos kapcsolatokat hozhat létre.
   - Válassza ki **+ diagnosztikai beállítás hozzáadása** párhuzamos streamelési több erőforrás diagnosztikai adatok konfigurálása.

   ![A diagnosztika egyetlen, készletezett vagy adatbázis-példány](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. A standard szintű, esemény-alapú figyelési környezetet válassza az alábbi jelölőnégyzetek az adatbázis diagnosztikai naplózási telemetriai adatok: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **hibák** , **DatabaseWaitStatistics**, **időtúllépések**, **blokkok**, és **holtpontok**.
1. Egy speciális, egy perc-alapú figyelési környezetet, válassza a jelölőnégyzet **AllMetrics**.
   ![Egyetlen diagnosztika konfigurálása, a készletezett vagy adatbázis-példány](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Kattintson a **Mentés** gombra.
1. Ismételje meg ezeket a lépéseket minden egyes figyelni kívánt adatbázist.

> [!NOTE]
> Biztonsági naplózás és SQLSecurityAuditEvents naplók nem lehet engedélyezni az adatbázis diagnosztikai beállítások (bár a képernyőn látható). Engedélyezheti a naplózási naplóstreamelés [beállítása az adatbázis naplózási](sql-database-auditing.md#subheading-2), és [naplózás az Azure Monitor naplóira és az Azure Event Hubs-naplók](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242).
> [!TIP]
> Ismételje meg ezeket a lépéseket minden egyes Azure SQL Database figyelni szeretné.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Konfigurálja a felügyelt példányok diagnostics telemetriai adatainak adatfolyamként

   ![Felügyelt példány ikon](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

A következő diagnostics telemetriai adatainak gyűjtéséhez állíthat be a felügyelt példány erőforrás:

| Erőforrás | Telemetriai adatok figyelése |
| :------------------- | ------------------- |
| **Felügyelt példány** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance) virtuális magok száma, átlagos Processzorhasználat (%), i/o-kérelmek, bájtot írt vagy olvasott, foglalt tárhely tartalmaz, és a használt tárterület. |

Felügyelt példány és a példány adatbázisok diagnostics telemetriai adatainak adatfolyamként konfigurálásához kell külön konfigurálni **mindkét** a következők közül:

- Felügyelt példány diagnostics telemetriai adatainak streamelésének engedélyezéséhez **és**
- Az egyes példányok adatbázisok diagnostics telemetriai adatainak streamelésének engedélyezéséhez

Ez azért, mert a felügyelt példány a saját telemetriát, egy egyéni példány adatbázis-telemetriai külön egy adatbázis-tárolóban.

Felügyelt példány erőforrás diagnostics telemetriai adatainak streamelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a **felügyelt példány** erőforrását az Azure Portalon.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.

   ![Felügyelt példány diagnosztika engedélyezése](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. Válassza ki a log analytics **konfigurálása** , és hozzon létre egy új munkaterületet kiválasztásával **+ létrehozás új munkaterület**, vagy használjon egy meglévő munkaterületet.
1. Jelölje be például a diagnostics telemetriai adatainak: **ResourceUsageStats**.
   ![Felügyelt példány diagnosztika konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Kattintson a **Mentés** gombra.
1. Emellett konfigurálása adatfolyamként diagnostics telemetriai adatainak minden példány adatbázis belül hajtsa végre a következő szakaszban leírt figyelni szeretné a felügyelt példány számára.

> [!IMPORTANT]
> Azonkívül, hogy a diagnostics telemetriai adatainak a felügyelt példány, szükség diagnostics telemetriai adatainak minden példány adatbázis konfigurálása módon lentebb. 

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Konfigurálja a diagnostics telemetriai adatainak adatfolyamként például adatbázisok

   ![A felügyelt példány ikon példány adatbázisa](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Adatfolyamként történő diagnostics telemetriai adatainak, például adatbázisok, kövesse az alábbi lépéseket:

1. Lépjen a **példány adatbázisa** felügyelt példány-erőforrást.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.
   - Legfeljebb három (3) a stream diagnostics telemetriai adatainak párhuzamos kapcsolatot hozhat létre.
   - Válassza ki **+ diagnosztikai beállítás hozzáadása** párhuzamos streamelési több erőforrás diagnosztikai adatok konfigurálása.

   ![Engedélyezze a diagnosztikát, például adatbázisok](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. Válassza ki az adatbázis diagnostics telemetriai adatainak jelölőnégyzetét: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** és **hibák**.
   ![Például adatbázisok diagnosztika konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Kattintson a **Mentés** gombra.
1. Ismételje meg ezeket a lépéseket minden egyes figyelni kívánt példány adatbázisa.

> [!TIP]
> Ismételje meg ezeket a lépéseket minden egyes figyelni kívánt példány adatbázisa.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

Metrikák és diagnosztikai naplózás PowerShell használatával engedélyezheti.

- Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója a cél tárfiók erőforrás-azonosító.

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Ahhoz, hogy a küldő diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics munkaterület erőforrás-Azonosítóját a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="to-configure-multiple-azure-resources"></a>Több Azure-erőforrások konfigurálása

Több előfizetés is támogatja, használja a PowerShell-szkript [engedélyezése az Azure resource metrikák naplózás PowerShell-lel](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Adja meg a munkaterület erőforrás-azonosító \<$WSID\> paraméterként a parancsfájl végrehajtása közben `Enable-AzureRMDiagnostics.ps1` különböző erőforrásokból származó diagnosztikai adatokat küldeni a munkaterületen.

- A munkaterület-azonosító beolvasásához \<$WSID\> a cél a diagnosztikai adatok, használja a következő szkriptet:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Cserélje le \<subID\> az előfizetés-azonosító, a \<RG_NAME\> az az erőforráscsoport nevét, és \<WS_NAME\> munkaterület nevét.

### <a name="azure-cli"></a>Azure CLI

Metrikák és diagnosztikai naplózás az Azure CLI használatával engedélyezheti.

> [!NOTE]
> Diagnosztikai naplózás engedélyezése a parancsfájlok az Azure CLI 1.0-s verzió támogatottak. Vegye figyelembe, hogy CLI 2.0-s verzió jelenleg nem támogatott.

- Ahhoz, hogy a tárfiókot a diagnosztikai naplók tárolására, használja ezt a parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A tárfiók azonosítója a cél tárfiók erőforrás-azonosító.

- A diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- A Log Analytics-munkaterületet a diagnosztikai naplók küldése engedélyezéséhez használja ezt a parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="rest-api"></a>REST API

Megtudhatja, hogyan lehet a [diagnosztikai beállítások módosítása az Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Resource Manager-sablon

Megtudhatja, hogyan lehet a [erőforrás létrehozásakor a diagnosztikai beállítások engedélyezése Resource Manager-sablon használatával](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Az Azure SQL Analytics Stream

Az Azure SQL Analytics egy felhőalapú megoldás, amely figyeli az Azure SQL adatbázisok, rugalmas készletek és ipari méretekben, és több előfizetésre kiterjedő felügyelt példányok teljesítményét. Megkönnyíti összegyűjtését és az Azure SQL-adatbázis teljesítmény-mérőszámok megjelenítését, és a teljesítménnyel kapcsolatos hibaelhárítás beépített intelligenciával rendelkezik.

![Az Azure SQL Analytics áttekintése](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database-metrikák és diagnosztikai naplókat is lehet streameli az Azure SQL Analytics használatával a beépített **Küldés a Log Analyticsnek** lehetőség a diagnosztikai beállítások lapon a portálon. A log analytics használatával egy diagnosztikai beállítás PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API-n keresztül is engedélyezheti.

### <a name="installation-overview"></a>Telepítés – áttekintés

Az Azure SQL Analytics egy SQL Database flotta követheti nyomon. Hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy Azure SQL Analytics megoldás az Azure Marketplace-ről.
2. A megoldás létrehozása a figyelés munkaterületen.
3. A stream diagnostics telemetriai adatainak-adatbázisok konfigurálása a munkaterületre.

Ha felügyelt példányai vagy rugalmas készletek használjuk, is kell streamelés ezeket az erőforrásokat a diagnostics telemetriai adatainak konfigurálása.

### <a name="create-azure-sql-analytics-resource"></a>Az Azure SQL Analytics-erőforrás létrehozása

1. Keresse meg az Azure SQL Analytics az Azure Marketplace-en, és válassza ki azt.

   ![Keresse meg az Azure SQL Analytics portálon](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Válassza ki **létrehozás** a megoldás áttekintése képernyőn.

3. Töltse ki az Azure SQL Analytics az űrlapot a szükséges további információkat tartalmazó: munkaterület neve, előfizetés, erőforráscsoport, helye és tarifacsomag.

   ![Az Azure SQL Analytics konfigurálása a portálon](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Válassza ki **OK** erősítse meg, és válassza ki a **létrehozás**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Rekord metrikák és diagnosztikai naplókat,-adatbázisok konfigurálása

A legegyszerűbben úgy konfigurálja, ahol a adatbázisok metrika rögzítése van az Azure portal használatával. Az előzőekben leírtak nyissa meg az Azure Portalon, és válassza az SQL adatbázis-erőforrás **diagnosztikai beállítások**.

Ha felügyelt példányai vagy rugalmas készletek használjuk, is szeretné ezeket az erőforrásokat a diagnostics telemetriai adatainak a munkaterületre streamelésére engedélyezéséhez a diagnosztikai beállítások konfigurálása.

### <a name="use-the-sql-analytics-solution"></a>Az SQL Analytics megoldás használata

Hierarchikus irányítópultként SQL Analytics használatával megtekintheti az SQL Database-erőforrásokat. Az SQL Analytics megoldás használatával kapcsolatban lásd: [SQL Database megfigyelése az SQL Analytics megoldás használatával](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Streamelheti SQL Database-metrikák és diagnosztikai naplók az Event Hubsba a beépített használatával **Stream egy eseményközpontba** lehetőség az Azure Portalon. A Service Bus Szabályazonosító egy diagnosztikai beállítás PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API használatával is engedélyezi.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Mit kell tenni a metrikák és diagnosztikai naplók az Event Hubs

A kiválasztott adatok streamelése az Event Hubsba, miután Ön, amely lehetővé teszi a speciális megfigyelési forgatókönyvekhez egy lépéssel közelebb kerülnek. Az Event Hubs egy eseményfolyamat számára a bejárati ajtó funkcionál. Miután az adatok az event hubs-eseményközpontba gyűjtése, átalakíthatók, és a valós idejű elemzési szolgáltató vagy egy tárolási adapter használatával tárolhatók. Az Event Hubs elválasztja az üzemi datového proudu események elkülöníti az események. Ily módon az eseményfelhasználók férhetnek hozzá a saját ütemezésüknek az eseményeket. Az Event Hubs további információkért lásd:

- [Mik az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Event hubs adatfolyamként továbbított metrikákat is használhatja:

- **Szolgáltatás állapotának megtekintéséhez a streamelési adatok ritkáról gyakori elérésű útvonal a Power bi-bA**. Az Event Hubs, a Stream Analytics és a Power bi-ban, a metrikák és diagnosztikai adatok közel valós idejű elemzések egyszerűen alakíthatja át az Azure-szolgáltatások. Bemutatja, hogyan állíthat be egy eseményközpontot, dolgozza fel az adatokat a Stream Analytics és a Power BI használja kimenetként, lásd [Stream Analytics és a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Stream naplók külső naplózás és a telemetriai Streamek**. Streamelés az Event Hubs használatával lekérheti a metrikák és diagnosztikai naplók különböző külső figyelés és a log analytics megoldásokban.

- **Hozhat létre egy egyéni telemetriát és a naplózás platform**. Ehhez már rendelkezik egy személyre szabott telemetriai platform vagy használatát fontolgatja létrehozására? A rugalmasan skálázható közzétételi és előfizetési jellege az Event Hubs lehetővé teszi, hogy rugalmasan diagnosztikai naplók. Lásd: [Dan Rosanova útmutató az Event Hubs használatával egy globális szintű telemetriai platformon](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream Storage-bA

SQL-adatbázis-metrikák is tárolhatja, és a diagnosztikai naplók az Azure Storage használatával a beépített **archiválás tárfiókba** lehetőség az Azure Portalon. Storage PowerShell parancsmagjainak, az Azure CLI vagy az Azure Monitor REST API-n keresztül a diagnosztikai beállítás használatával is engedélyezheti.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Séma metrikái és diagnosztikai naplók a storage-fiókban

Miután beállította a metrikák és diagnosztikai naplók gyűjtése, egy storage-tároló jön létre a tárfiókban, ha elérhetővé válik az adatok első sorát választotta. A blobok struktúráját a következő:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Vagy egyszerűen több:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Például az összes metrikát a blobnév lehet:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

A rugalmas készlet-adatok tárolására szolgáló blob nevében hasonlóan néz ki:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Metrikák és naplók letöltése a Storage-ból

Ismerje meg, hogyan [metrikák és diagnosztikai naplók letöltése a Storage-ból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).

## <a name="data-retention-policy-and-pricing"></a>Az adatmegőrzési házirend és díjszabás

Ha az Event Hubs vagy a Storage-fiók lehetőséget választja, megadhatja az adatmegőrzési. Ez a szabályzat egy kiválasztott időszakban régebbi adatokat törli. Ha megadja a Log Analytics, a megőrzési házirend a kiválasztott tarifacsomag függ. Ebben az esetben a megadott ingyenes egységek az adatbetöltés ingyenes figyelését több adatbázist havonta is engedélyezheti. Bármely az diagnostics telemetriai adatainak biztosította az ingyenes egységek felhasználását előfordulhat, hogy költségekkel. Vegye figyelembe, hogy a nagyobb számítási feladatokkal aktív adatbázisok betöltési üresjárati adatbázisok több adatot. További információkért lásd: [Log analytics díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).

Az Azure SQL Analytics használja, ha az adathasználat Adatbetöltési a megoldásban figyelheti kiválasztásával **OMS-munkaterület** az Azure SQL Analytics, és válassza a navigációs menü **használati** és **becsült költség**.

## <a name="metrics-and-logs-available"></a>Metrikák és naplók érhető el

Figyelés elérhető telemetriai adatokat az Azure SQL Database, a rugalmas készletek és a felügyelt példány van leírása az alábbiakban található. Az SQL Analytics belül gyűjtött figyelési telemetriai is használható a saját egyéni elemzési és a fejlesztési használó [Azure Monitor log-lekérdezések](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nyelv.

## <a name="all-metrics"></a>Az összes metrikák

Tekintse meg az összes metrikák részleteit az alábbi táblázatok erőforrás szerint.

### <a name="all-metrics-for-elastic-pools"></a>Rugalmas készletek minden metrika

|**Erőforrás**|**Mérőszámok**|
|---|---|
|Rugalmas készlet|eDTU százalékos értéke, eDTU használja, az eDTU-korlát, processzor, fizikai adatok olvasási százalékos aránya, napló írási százalékos, munkamenetek százaléka, feldolgozók százalékos, tárhely, tárolási százalékos, tárolási kapacitása, XTP tárolási százalékos aránya |

### <a name="all-metrics-for-azure-sql-databases"></a>Az Azure SQL Database összes metrikák

|**Erőforrás**|**Mérőszámok**|
|---|---|
|Azure SQL-adatbázis|Napló írási DTU százalékos értéke, dtu-k használt, DTU-korlát, processzor, fizikai adatok olvasási százalékos aránya, százalékos aránya, a sikeres/sikertelen/letiltott tűzfalkapcsolatok, munkamenetek százaléka, feldolgozók százalékos aránya, storage, tárolási százalékos aránya, XTP tárolási százalékos aránya, és holtpontok |

## <a name="all-logs"></a>Az összes napló

Elérhető az összes napló telemetriai részletezni az alábbi táblázatokban. Lásd: [támogatja a diagnosztikai naplózás](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) megismerni, hogy milyen naplókat támogatottak egy adott adatbázis íz – egyetlen, az Azure SQL készletezésük, vagy adatbázis-példány.

### <a name="resource-usage-stats-for-managed-instance"></a>Erőforrás-használati statisztikák felügyelt példány

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: ResourceUsageStats |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: MANAGEDINSTANCES |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|A felügyelt példány neve |
|ResourceId|Erőforrás-URI |
|SKU_s|Felügyelt példány termékváltozat |
|virtual_core_count_s|Elérhető virtuális magok száma |
|avg_cpu_percent_s|Átlagos Processzorhasználat (%) |
|reserved_storage_mb_s|A felügyelt példányon fenntartott tárolási kapacitás |
|storage_space_used_mb_s|A felügyelt példányon használt tároló |
|io_requests_s|IOPS száma |
|io_bytes_read_s|IOPS olvasott bájtok |
|io_bytes_written_s|Iops-t írt bájtok száma |

### <a name="query-store-runtime-statistics"></a>Query Store futásidejű statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: QueryStoreRuntimeStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreRuntimeStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|query_hash_s|Lekérdezés kivonata |
|query_plan_hash_s|Lekérdezés csomag kivonata |
|statement_sql_handle_s|Sql utasításleíró |
|interval_start_time_d|Indítsa el a datetimeoffset intervallum az 1900-1-1 közötti számát |
|interval_end_time_d|Záró datetimeoffset 1900-1-1-től számát az intervallum |
|logical_io_writes_d|Logikai i/o-írások száma összesen |
|max_logical_io_writes_d|Végrehajtásonkénti ír logikai IO maximális száma |
|physical_io_reads_d|Fizikai i/o-olvasások száma összesen |
|max_physical_io_reads_d|Végrehajtásonkénti beolvassa logikai IO maximális száma |
|logical_io_reads_d|Logikai i/o-olvasások száma összesen |
|max_logical_io_reads_d|Végrehajtásonkénti beolvassa logikai IO maximális száma |
|execution_type_d|Végrehajtási típus |
|count_executions_d|A lekérdezés végrehajtásainak száma |
|cpu_time_d|Mikroszekundumban a lekérdezés által felhasznált teljes CPU-idő |
|max_cpu_time_d|CPU maximális idő fogyasztói által egy egyetlen végrehajtási mikroszekundumban |
|dop_d|Párhuzamossági fokot összege |
|max_dop_d|Maximális párhuzamossági fok egyetlen végrehajtásához |
|rowcount_d|Visszaadott sorok száma |
|max_rowcount_d|Egyetlen végrehajtási által visszaadott sorok maximális száma |
|query_max_used_memory_d|KB-ban használt memória mennyisége összesen |
|max_query_max_used_memory_d|A KB-ban egyetlen végrehajtási által használt memória maximális mennyisége |
|duration_d|Összesített végrehajtási ideje mikroszekundumban |
|max_duration_d|Egyetlen végrehajtási maximális végrehajtási ideje |
|num_physical_io_reads_d|Fizikai olvasások száma összesen |
|max_num_physical_io_reads_d|Fizikai olvasások végrehajtásonkénti maximális száma |
|log_bytes_used_d|Teljes összeg napló mérete bájtban |
|max_log_bytes_used_d|Maximális mennyisége végrehajtásonkénti napló mérete bájtban |
|query_id_d|A lekérdezés a Query Store azonosítója |
|plan_id_d|A Query Store-csomag azonosítója |

Tudjon meg többet [Query Store futásidejű statisztikai adatok](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Query Store várakozási statisztika

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: QueryStoreWaitStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|wait_category_s|Várakozás kategóriáját |
|is_parameterizable_s|A lekérdezés parametrizálható van |
|statement_type_s|Az utasítás típusa |
|statement_key_hash_s|Utasítás kulcsának kivonata |
|exec_type_d|Végrehajtási típus |
|total_query_wait_time_ms_d|A lekérdezés az adott várakozási kategória teljes várakozási idő |
|max_query_wait_time_ms_d|Maximális várakozási idő a lekérdezés az egyes végrehajtását az adott várakozási kategória |
|query_param_type_d|0 |
|query_hash_s|A Query Store lekérdezés kivonata |
|query_plan_hash_s|A Query Store lekérdezés csomag kivonata |
|statement_sql_handle_s|A Query Store utasításleíró |
|interval_start_time_d|Indítsa el a datetimeoffset intervallum az 1900-1-1 közötti számát |
|interval_end_time_d|Záró datetimeoffset 1900-1-1-től számát az intervallum |
|count_executions_d|A lekérdezés végrehajtásainak száma |
|query_id_d|A lekérdezés a Query Store azonosítója |
|plan_id_d|A Query Store-csomag azonosítója |

Tudjon meg többet [Query Store várjon statisztikai adatok](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Hibák adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQ |
|Kategória|A kategória nevét. Mindig: Hibák |
|OperationName|A művelet neve. Mindig: ErrorEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|Üzenet|Egyszerű szöveges hibaüzenet |
|user_defined_b|Hiba történt a felhasználó által definiált bites |
|error_number_d|Hibakód |
|Severity|A hiba súlyossága |
|state_d|A hiba állapot |
|query_hash_s|Lekérdezés kivonata hibás lekérdezés, ha elérhető |
|query_plan_hash_s|Lekérdezési terv kivonat a sikertelen lekérdezés, ha elérhető |

Tudjon meg többet [SQL Server-hibaüzenetek](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Adatbázis várakozási statisztika adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: DatabaseWaitStatistics |
|OperationName|A művelet neve. Mindig: DatabaseWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|wait_type_s|A várakozási típus neve |
|start_utc_date_t [UTC]|Mért időtartam kezdési ideje |
|end_utc_date_t [UTC]|Mért időtartam befejezési időpontja |
|delta_max_wait_time_ms_d|Maximális végrehajtási ideje adatbázis |
|delta_signal_wait_time_ms_d|Teljes jelek várakozási idő |
|delta_wait_time_ms_d|Teljes várakozási idő az az időszak |
|delta_waiting_tasks_count_d|Várakozó feladatok száma |

Tudjon meg többet [adatbázis-wait statisztika](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Időtúllépések adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: Időtúllépések |
|OperationName|A művelet neve. Mindig: TimeoutEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|error_state_d|Hibakód állapota: |
|query_hash_s|Lekérdezés-kivonat, ha elérhető |
|query_plan_hash_s|Lekérdezési terv kivonat, ha elérhető |

### <a name="blockings-dataset"></a>Blockings adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: blokkok |
|OperationName|A művelet neve. Mindig: BlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|lock_mode_s|A lekérdezés által használt zárolási mód |
|resource_owner_type_s|A zárolás tulajdonosa |
|blocked_process_filtered_s|Letiltott folyamat jelentés XML |
|duration_d|A Zárolás időtartama ezredmásodpercben |

### <a name="deadlocks-dataset"></a>Holtpontok adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC] |Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: Holtpontok |
|OperationName|A művelet neve. Mindig: DeadlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|deadlock_xml_s|Holtpont jelentés XML |

### <a name="automatic-tuning-dataset"></a>Az automatikus finomhangolási adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Typo|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT.SQL |
|Kategória|A kategória nevét. Mindig: AutomaticTuning |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK ÉS ADATBÁZISOK |
|SubscriptionId|Az adatbázis-előfizetés GUID azonosítója |
|ResourceGroup|Az adatbázis az erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis a kiszolgáló neve |
|LogicalDatabaseName_s|Az adatbázis neve |
|ElasticPoolName_s|Az adatbázis, ha van ilyen, a rugalmas készlet neve |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás-URI |
|RecommendationHash_s|Az automatikus finomhangolási javaslat egyedi kivonata |
|OptionName_s|Automatikus hangolási művelet |
|Schema_s|Adatbázis-séma |
|Table_s|Az érintett tábla |
|IndexName_s|Index neve |
|IndexColumns_s|Oszlop neve |
|IncludedColumns_s|Tartalmazott oszlopok |
|EstimatedImpact_s|Becsült hatás automatikus finomhangolási ajánlás JSON |
|Event_s|Az automatikus finomhangolási esemény típusa |
|Timestamp_t|Utolsó frissítés időbélyege |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights adatkészlet

Tudjon meg többet a [Intelligent Insights naplóformátum](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan naplózás engedélyezése és a metrikák és naplózása támogatja a különböző Azure-szolgáltatások, kategóriák:

- [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure-beli diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md)

Az Event Hubs szolgáltatásról, olvassa el:

- [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Az Azure Storage szolgáltatással kapcsolatos további tudnivalókért lásd: [metrikák és diagnosztikai naplók letöltése a Storage-ból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
