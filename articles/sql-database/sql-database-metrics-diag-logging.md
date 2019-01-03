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
ms.date: 09/20/2018
ms.openlocfilehash: 138368c8e79d68a9a9c5a711b99d8926da7dc68d
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601559"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Az Azure SQL Database-metrikák és diagnosztikai naplózás

Az Azure SQL Database, a rugalmas készletek, a felügyelt példány és az adatbázisok a felügyelt példány is könnyebben alkalmazásteljesítmény-figyelés a metrikák és diagnosztikai naplók streamelése. Az adatbázis erőforrás-használat, a dolgozók és a munkamenetek és a egy, a következő Azure-erőforrások kapcsolat továbbítására konfigurálhatja:

* **Az Azure SQL Analytics**: az Azure-adatbázisok, amely tartalmazza az teljesítményjelentések készítésére, riasztások és javaslatok kockázatcsökkentési intelligens monitorozást beolvasásához.
* **Az Azure Event Hubs**: SQL Database telemetriai adatainak integrálása saját egyedi monitorozási megoldásokkal vagy élő adatfolyamatokkal.
* **Az Azure Storage**: archiválására hatalmas mennyiségű telemetriai árának töredékéért.

    ![Architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

A különböző Azure-szolgáltatások által támogatott mérőszámokban és naplófájlokban kategóriák kapcsolatos további információkért lásd:

* [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Azure-beli diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md)

Ez a cikk nyújt útmutatást az adatbázisok, rugalmas készletek és a felügyelt példány diagnostics telemetriai adatainak engedélyezéséhez. Azt is segítségével megtudhatja, hogyan konfigurálhat egy Azure SQL Analytics egy figyelési eszköz adatbázis diagnostics telemetriai adatainak megtekintéséhez.

## <a name="enable-logging-of-diagnostics-telemetry"></a>A telemetria diagnosztikai naplózás engedélyezése

Engedélyezheti és kezelheti a metrikák és diagnosztikai telemetriai naplók az alábbi módszerek egyikének használatával:

- Azure Portal
- PowerShell
- Azure CLI
- Az Azure Monitor REST API-val
- Azure Resource Manager-sablon

Ha engedélyezi a metrikák és diagnosztikai naplózás, adjon meg az Azure-erőforrás célhelyet a Diagnostics telemetriai adatok gyűjtésének kell. Elérhető lehetőségek a következők:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Új Azure-erőforrás kiépítéséhez, vagy egy meglévő erőforrás kiválasztása. Az erőforrás kiválasztása után a **diagnosztikai beállítások** lehetőségnél adja meg az adatok gyűjtéséhez.

> [!NOTE]
> Rugalmas készletek és a felügyelt példány is használ, azt javasoljuk, hogy engedélyezze a diagnostics telemetriai adatainak, valamint az ezekhez az erőforrásokhoz. A rugalmas készletek és a felügyelt példány adatbázis-tárolók saját külön diagnostics telemetriai adatainak rendelkezik.

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Azure SQL Database vagy a felügyelt példány adatbázisok naplózásának engedélyezése

A metrikák és az SQL Database és a felügyelt példány; az adatbázisokat a diagnosztikai naplózás engedélyezése Ezek a van alapértelmezés szerint nincs engedélyezve.

A következő diagnostics telemetriai adatainak gyűjtéséhez állíthat be az Azure SQL Database és az adatbázisok a felügyelt példány:

| Telemetria adatbázisok figyelése | Az Azure SQL Database-támogatás | A felügyelt példány támogatási adatbázis |
| :------------------- | ------------------- | ------------------- |
| [Minden metrika](sql-database-metrics-diag-logging.md#all-metrics): Dtu-k/Processzorhasználat (%), a dtu-k/CPU-korlát, a fizikai tartalmaz napló írási adatok olvasása a százalékos aránya, százalékos aránya, sikeres/sikertelen/letiltott által tűzfalkapcsolatok, munkamenetek százaléka, feldolgozók százalékos aránya, storage, storage, és XTP tároló (%). | Igen | Nem |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): A lekérdezések futásidejének statisztikai adatait, például a CPU-használat és a lekérdezés időtartama statisztikai információkat tartalmaz. | Igen | Igen |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): A lekérdezés várakozási statisztika (Mi a lekérdezések megvárta) kapcsolatos információt tartalmazza, például a Processzor, a napló és a ZÁROLÁS. | Igen | Igen |
| [Hibák](sql-database-metrics-diag-logging.md#errors-dataset): Az adatbázis SQL-hibákkal kapcsolatos információkat tartalmaz. | Igen | Nem |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset): Az adatbázis töltött a különböző várakozási típusok vár mennyi ideig kapcsolatos információt tartalmazza. | Igen | Nem |
| [Időtúllépések](sql-database-metrics-diag-logging.md#time-outs-dataset): Az adatbázis időtúllépések információkat tartalmazza. | Igen | Nem |
| [Blokkok](sql-database-metrics-diag-logging.md#blockings-dataset): Blokkoló események az adatbázison kapcsolatos információt tartalmazza. | Igen | Nem |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): Intelligent Insights teljesítményéről tartalmaz. További tudnivalókért lásd: [Intelligent Insights](sql-database-intelligent-insights.md). | Igen | Igen |

### <a name="azure-portal"></a>Azure Portal

Használja a **diagnosztikai beállítások** menü adatbázisok az Azure Portalon konfigurálhatja a diagnostics telemetriai adatainak streamelés az Azure SQL Database és a felügyelt példány adatbázisok mindegyike esetében. Beállíthatja, hogy a következő helyre: Az Azure Storage, Azure Event Hubs és az Azure Log Analytics.

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>A diagnostics telemetriai adatainak az Azure SQL Database-streamelés konfigurálása

   ![Az SQL Database ikonja](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Az Azure SQL Database-diagnostics telemetriai adatainak streamelésének engedélyezéséhez, kövesse az alábbi lépéseket:

1. Nyissa meg az Azure SQL Database-erőforrás.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.
   - Stream diagnostics telemetriai adatainak legfeljebb három párhuzamos kapcsolatokat hozhat létre.
   - Válassza ki **+ diagnosztikai beállítás hozzáadása** párhuzamos streamelési több erőforrás diagnosztikai adatok konfigurálása.

   ![Az SQL Database-diagnosztika engedélyezése](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. A standard szintű, esemény-alapú figyelési környezetet válassza az alábbi jelölőnégyzetek az adatbázis diagnosztikai naplózási telemetriai adatok: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **hibák** , **DatabaseWaitStatistics**, **időtúllépések**, **blokkok**, és **holtpontok**.
1. Egy speciális, egy perc-alapú figyelési környezetet, válassza a jelölőnégyzet **AllMetrics**.
1. Kattintson a **Mentés** gombra.

   ![Az SQL Database-diagnosztika konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> Adatbázis diagnosztikai beállítások alapján nem sikerült engedélyezni a biztonsági naplókat. Engedélyezheti a naplózási naplóstreamelés [beállítása az adatbázis naplózási](sql-database-auditing.md#subheading-2), és [SQL Audit naplók az Azure Log Analytics és az Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/).

> [!TIP]
> Ismételje meg ezeket a lépéseket minden egyes Azure SQL Database figyelni szeretné.

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Konfigurálja a felügyelt példány található adatbázisok a diagnostics telemetriai adatainak adatfolyamként

   ![A felügyelt példány ikon adatbázis](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Felügyelt példány található adatbázisok a diagnostics telemetriai adatainak streamelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az adatbázis a felügyelt példányhoz.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.
   - Legfeljebb három (3) a stream diagnostics telemetriai adatainak párhuzamos kapcsolatot hozhat létre.
   - Válassza ki **+ diagnosztikai beállítás hozzáadása** párhuzamos streamelési több erőforrás diagnosztikai adatok konfigurálása.

   ![Felügyelt példány adatbázis-diagnosztika engedélyezése](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. Válassza ki az adatbázis diagnostics telemetriai adatainak jelölőnégyzetét: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** és **hibák**.
1. Kattintson a **Mentés** gombra.

   ![Felügyelt példány adatbázisa diagnosztika konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> Ismételje meg ezeket a lépéseket a figyelni kívánt felügyelt példányon lévő minden egyes adatbázishoz.

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>Rugalmas készletek és a felügyelt példány naplózásának engedélyezése

Rugalmas készletek és a felügyelt példány diagnosztikai telemetria engedélyezése adatbázis tárolókként. A saját diagnostics telemetriai adatainak, alapértelmezés szerint nem rendelkeznek.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Konfigurálja a diagnostics telemetriai adatainak a rugalmas készletekhez adatfolyamként

   ![Rugalmas készlet ikon](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

A következő diagnostics telemetriai adatainak gyűjtéséhez állíthat be egy rugalmas készlet egyenlő erőforrás:

| Erőforrás | Telemetriai adatok figyelése |
| :------------------- | ------------------- |
| **Rugalmas készlet** | [Minden metrika](sql-database-metrics-diag-logging.md#all-metrics) eDTU/Processzorhasználat (%), eDTU/CPU-korlát, fizikai tartalmazza az beolvasott adatok százalékos aránya, napló írási százalékos, munkamenetek százaléka, feldolgozók százalékos, storage, tárolási százalékos, tárolási kapacitása és XTP tárolási százalékos aránya. |

Rugalmas készlet erőforrás diagnostics telemetriai adatainak streamelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a rugalmas készlet egyenlő erőforrás az Azure Portalon.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.

   ![Engedélyezze a diagnosztikát a rugalmas készletekhez](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. Válassza ki a Log Analytics, **konfigurálása** , és hozzon létre egy új munkaterületet kiválasztásával **+ létrehozás új munkaterület**, vagy válasszon ki egy meglévő munkaterületet.
1. Válassza ki a rugalmas készlet diagnostics telemetriai adatainak jelölőnégyzetét: **AllMetrics**.
1. Kattintson a **Mentés** gombra.

   ![Rugalmas készletek diagnosztika konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> Ismételje meg ezeket a lépéseket minden rugalmas készletet szeretné figyelni.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Konfigurálja a streamelést a diagnostics telemetriai adatainak a felügyelt példány

   ![Felügyelt példány ikon](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

A következő diagnostics telemetriai adatainak gyűjtéséhez állíthat be a felügyelt példány erőforrás:

| Erőforrás | Telemetriai adatok figyelése |
| :------------------- | ------------------- |
| **Felügyelt példány** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#resource-usage-stats) virtuális magok száma, átlagos Processzorhasználat (%), i/o-kérelmek, bájtot írt vagy olvasott, foglalt tárhely tartalmaz, és a használt tárterület. |

Felügyelt példány erőforrás diagnostics telemetriai adatainak streamelésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a felügyelt példány erőforrását az Azure Portalon.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **diagnosztika bekapcsolása** Ha nincsenek korábbi beállítások létezik, vagy válasszon **beállítás szerkesztése** előző beállítások módosítása.

   ![Diagnosztika engedélyezése a felügyelt példány](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Adja meg a saját referenciaként a beállítás nevét.
1. Válasszon ki egy cél-erőforrást, a streamelési diagnosztikai adatok: **Archiválás tárfiókba**, **egy eseményközpontba Stream**, vagy **Küldés a Log Analyticsnek**.
1. Válassza ki a Log Analytics **konfigurálása** , és hozzon létre egy új munkaterületet kiválasztásával **+ létrehozás új munkaterület**, vagy használjon egy meglévő munkaterületet.
1. Jelölje be például a diagnostics telemetriai adatainak: **ResourceUsageStats**.
1. Kattintson a **Mentés** gombra.

   ![Diagnosztika konfigurálása a felügyelt példány](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> Ismételje meg ezeket a lépéseket minden egyes figyelni kívánt felügyelt példány esetében.

### <a name="powershell"></a>PowerShell

Metrikák és diagnosztikai naplózás PowerShell használatával engedélyezheti.

- Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A tárfiók azonosítója a cél tárfiók erőforrás-azonosító.

- Diagnosztikai naplók egy eseményközpontba streamelésének engedélyezéséhez használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus Szabályazonosító karakterláncnak a következő formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Ahhoz, hogy a küldő diagnosztikai naplók a Log Analytics-munkaterülethez, használja ezt a parancsot:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics munkaterület erőforrás-Azonosítóját a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Kombinálhatja ezeket a paramétereket, több kimeneti beállítások engedélyezéséhez.

### <a name="to-configure-multiple-azure-resources"></a>Több Azure-erőforrások konfigurálása

Több előfizetés is támogatja, használja a PowerShell-szkript [engedélyezése az Azure resource metrikák naplózás PowerShell-lel](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Adja meg a munkaterület erőforrás-azonosító \<$WSID\> paraméterként a parancsfájl végrehajtása közben `Enable-AzureRMDiagnostics.ps1` különböző erőforrásokból származó diagnosztikai adatokat küldeni a munkaterületen.

- A munkaterület-azonosító beolvasásához \<$WSID\> a cél a diagnosztikai adatok, használja a következő szkriptet:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   Cserélje le \<subID\> az előfizetés-azonosító, a \<RG_NAME\> az az erőforráscsoport nevét, és \<WS_NAME\> munkaterület nevét.

### <a name="azure-cli"></a>Azure CLI

Metrikák és diagnosztikai naplózás az Azure CLI használatával engedélyezheti.

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

Az Azure SQL Analytics egy felhőalapú megoldás, amely figyeli az Azure SQL-adatbázisok, a rugalmas készletek és a felügyelt példány teljesítményének ipari méretekben, és több előfizetés között. Megkönnyíti összegyűjtését és az Azure SQL-adatbázis teljesítmény-mérőszámok megjelenítését, és a teljesítménnyel kapcsolatos hibaelhárítás beépített intelligenciával rendelkezik.

![Az Azure SQL Analytics áttekintése](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database-metrikák és diagnosztikai naplókat is lehet streameli az Azure SQL Analytics használatával a beépített **Küldés a Log Analyticsnek** lehetőség a diagnosztikai beállítások lapon a portálon. A Log Analytics egy diagnosztikai beállítás PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API használatával is engedélyezheti.

### <a name="installation-overview"></a>Telepítés – áttekintés

Az Azure SQL Analytics egy SQL Database flotta követheti nyomon. Hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy Azure SQL Analytics megoldás az Azure Marketplace-ről.
2. A megoldás létrehozása a figyelés munkaterületen.
3. A stream diagnostics telemetriai adatainak-adatbázisok konfigurálása a munkaterületre.

Ha a rugalmas készletek és a felügyelt példány használata esetén is kell streamelés ezeket az erőforrásokat a diagnostics telemetriai adatainak konfigurálása.

### <a name="create-azure-sql-analytics-resource"></a>Az Azure SQL Analytics-erőforrás létrehozása

1. Keresse meg az Azure SQL Analytics az Azure Marketplace-en, és válassza ki azt.

   ![Keresse meg az Azure SQL Analytics portálon](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Válassza ki **létrehozás** a megoldás áttekintése képernyőn.

3. Töltse ki az Azure SQL Analytics az űrlapot a szükséges további információkat tartalmazó: munkaterület neve, előfizetés, erőforráscsoport, helye és tarifacsomag.

   ![Az Azure SQL Analytics konfigurálása a portálon](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Válassza ki **OK** erősítse meg, és válassza ki a **létrehozás**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Rekord metrikák és diagnosztikai naplókat,-adatbázisok konfigurálása

A legegyszerűbben úgy konfigurálja, ahol a adatbázisok metrika rögzítése van az Azure portal használatával. Az előzőekben leírtak nyissa meg az Azure Portalon, és válassza az SQL adatbázis-erőforrás **diagnosztikai beállítások**.

Ha a rugalmas készletek és a felügyelt példány használata esetén is kell ezeket az erőforrásokat a diagnostics telemetriai adatainak a munkaterületre streamelésére engedélyezéséhez a diagnosztikai beállítások konfigurálása.

### <a name="use-the-sql-analytics-solution"></a>Az SQL Analytics megoldás használata

Hierarchikus irányítópultként SQL Analytics használatával megtekintheti az SQL Database-erőforrásokat. Az SQL Analytics megoldás használatával kapcsolatban lásd: [SQL Database megfigyelése az SQL Analytics megoldás használatával](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Streamelheti SQL Database-metrikák és diagnosztikai naplók az Event Hubsba a beépített használatával **Stream egy eseményközpontba** lehetőség az Azure Portalon. A Service Bus Szabályazonosító egy diagnosztikai beállítás PowerShell parancsmagok, az Azure CLI vagy az Azure Monitor REST API használatával is engedélyezi.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Mit kell tenni a metrikák és diagnosztikai naplók az Event Hubs

A kiválasztott adatok streamelése az Event Hubsba, miután Ön, amely lehetővé teszi a speciális megfigyelési forgatókönyvekhez egy lépéssel közelebb kerülnek. Az Event Hubs egy eseményfolyamat számára a bejárati ajtó funkcionál. Miután az adatok az event hubs-eseményközpontba gyűjtése, átalakíthatók, és a valós idejű elemzési szolgáltató vagy egy tárolási adapter használatával tárolhatók. Az Event Hubs elválasztja az üzemi datového proudu események elkülöníti az események. Ily módon az eseményfelhasználók férhetnek hozzá a saját ütemezésüknek az eseményeket. Az Event Hubs további információkért lásd:

- [Mik az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Event hubs adatfolyamként továbbított metrikákat is használhatja:

* **Szolgáltatás állapotának megtekintéséhez a streamelési adatok ritkáról gyakori elérésű útvonal a Power bi-bA**. Az Event Hubs, a Stream Analytics és a Power bi-ban, a metrikák és diagnosztikai adatok közel valós idejű elemzések egyszerűen alakíthatja át az Azure-szolgáltatások. Bemutatja, hogyan állíthat be egy eseményközpontot, dolgozza fel az adatokat a Stream Analytics és a Power BI használja kimenetként, lásd [Stream Analytics és a Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Stream naplók külső naplózás és a telemetriai Streamek**. Streamelés az Event Hubs használatával lekérheti a metrikák és diagnosztikai naplók különböző külső figyelés és a log analytics megoldásokban.

* **Hozhat létre egy egyéni telemetriát és a naplózás platform**. Ehhez már rendelkezik egy személyre szabott telemetriai platform vagy használatát fontolgatja létrehozására? A rugalmasan skálázható közzétételi és előfizetési jellege az Event Hubs lehetővé teszi, hogy rugalmasan diagnosztikai naplók. Lásd: [Dan Rosanova útmutató az Event Hubs használatával egy globális szintű telemetriai platformon](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

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

Ha az Event Hubs vagy a Storage-fiók lehetőséget választja, megadhatja az adatmegőrzési. Ez a szabályzat egy kiválasztott időszakban régebbi adatokat törli. Ha megadja a Log Analytics, a megőrzési házirend a kiválasztott tarifacsomag függ. Ebben az esetben a megadott ingyenes egységek az adatbetöltés ingyenes figyelését több adatbázist havonta is engedélyezheti. Bármely az diagnostics telemetriai adatainak biztosította az ingyenes egységek felhasználását előfordulhat, hogy költségekkel. Vegye figyelembe, hogy a nagyobb számítási feladatokkal aktív adatbázisok betöltési üresjárati adatbázisok több adatot. További információkért lásd: [Log Analytics díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Az Azure SQL Analytics használja, ha az adathasználat Adatbetöltési a megoldásban figyelheti kiválasztásával **OMS-munkaterület** az Azure SQL Analytics, és válassza a navigációs menü **használati** és **becsült költség**.

## <a name="metrics-and-logs-available"></a>Metrikák és naplók érhető el

Figyelési gyűjtött telemetria használhatja a saját _egyéni elemző_ és _alkalmazásfejlesztés_ használatával [SQL Analytics nyelvi](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries). 

## <a name="all-metrics"></a>Az összes metrikák

Tekintse meg az összes metrikák részleteit az alábbi táblázatok erőforrás szerint.

### <a name="all-metrics-for-elastic-pools"></a>Rugalmas készletek minden metrika

|**Erőforrás**|**Metrikák**|
|---|---|
|Rugalmas készlet|eDTU százalékos értéke, eDTU használja, az eDTU-korlát, processzor, fizikai adatok olvasási százalékos aránya, napló írási százalékos, munkamenetek százaléka, feldolgozók százalékos, tárhely, tárolási százalékos, tárolási kapacitása, XTP tárolási százalékos aránya |

### <a name="all-metrics-for-azure-sql-databases"></a>Az Azure SQL Database összes metrikák

|**Erőforrás**|**Metrikák**|
|---|---|
|Azure SQL-adatbázis|Napló írási DTU százalékos értéke, dtu-k használt, DTU-korlát, processzor, fizikai adatok olvasási százalékos aránya, százalékos aránya, a sikeres/sikertelen/letiltott tűzfalkapcsolatok, munkamenetek százaléka, feldolgozók százalékos aránya, storage, tárolási százalékos aránya, XTP tárolási százalékos aránya, és holtpontok |

## <a name="logs-for-managed-instance"></a>Felügyelt példány naplók

Tekintse meg a naplók részleteit az alábbi táblázat a felügyelt példány.

### <a name="resource-usage-statistics"></a>Erőforrás-használati statisztikáit

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|reserved_storage_mb_s|A felügyelt példány fenntartott tárolási kapacitás |
|storage_space_used_mb_s|A felügyelt példány használatban lévő |
|io_requests_s|IOPS száma |
|io_bytes_read_s|IOPS olvasott bájtok |
|io_bytes_written_s|Iops-t írt bájtok száma |

## <a name="logs-for-azure-sql-databases-and-managed-instance-databases"></a>Az Azure SQL Database-adatbázisok és a felügyelt példány adatbázisok naplók

Tekintse meg az alábbi táblázatok az Azure SQL és a felügyelt példány adatbázisok naplók részleteit.

### <a name="query-store-runtime-statistics"></a>Query Store futásidejű statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|Mikor lett rögzítve a napló időbélyeg |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|Típus|Mindig: AzureDiagnostics |
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
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: A MICROSOFT. AZ SQL |
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

* [A Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Azure-beli diagnosztikai naplók áttekintése](../azure-monitor/platform/diagnostic-logs-overview.md)

Az Event Hubs szolgáltatásról, olvassa el:

* [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Az Azure Storage szolgáltatással kapcsolatos további tudnivalókért lásd: [metrikák és diagnosztikai naplók letöltése a Storage-ból](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application).
