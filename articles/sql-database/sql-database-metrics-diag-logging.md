---
title: Metrikák és erőforrásnaplók streamelési exportálásának konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a metrikák és az erőforrás-naplók streamelési exportálását, beleértve az Azure SQL Database intelligens diagnosztikai elemzését az Ön által választott célhelyre az erőforrás-kihasználtságra és a lekérdezés-végrehajtási statisztikákra vonatkozó információk tárolására.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: 288d4e4d0c5faa6bb2b51451fb36bbb6d666c9eb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683185"
---
# <a name="configure-streaming-export-of-azure-sql-database-diagnostic-telemetry"></a>Az Azure SQL Database diagnosztikai telemetriai adatfolyam-exportálásának konfigurálása

Ebben a cikkben megismerheti az Azure SQL Database teljesítménymetrikák és erőforrás-naplók, amelyek elemzésre exportálhatók a számos cél egyikébe. Megtudhatja, hogyan konfigurálhatja a diagnosztikai telemetriai adatok streamelési exportálását az Azure Portalon, a PowerShellen, az Azure CLI-n, a REST API-n és az Azure Resource Manager-sablonokon keresztül.

Azt is megtudhatja, hogy a célok, amelyekhez streamelheti ezt a diagnosztikai telemetriai adatokat, és hogyan választhat ezek közül a lehetőségek közül. Az úti célok a következők:

- [Log Analytics és SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure-tárhely](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export-for-azure-sql-database"></a>Diagnosztikai telemetria az Azure SQL Database exportálásához

Az exportálható diagnosztikai telemetriai adatok közül a legfontosabb az Intelligent Insights (SQLInsights) napló. [Az Intelligent Insights](sql-database-intelligent-insights.md) beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligenciával, és észleli a gyenge teljesítményt okozó zavaró eseményeket. Észlelése után egy részletes elemzést végeznek, amely létrehoz egy intelligens insights-naplót a probléma intelligens felmérésével. Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma kiváltó okelemzéséből és lehetőség szerint a teljesítmény javítására vonatkozó javaslatokból áll. A napló tartalmának megtekintéséhez konfigurálnia kell a napló streamelési exportálását.

Az Intelligent Insights-napló exportálása mellett számos teljesítménymutatót és további SQL-adatbázis-naplókat is exportálhat. Az alábbi táblázat ismerteti a teljesítmény mutatók és erőforrás naplók, amelyek konfigurálható streamelési exportálás a több cél egyikén. Ez a diagnosztikai telemetria konfigurálható egyetlen adatbázisok, rugalmas készletek és készletezött adatbázisok, valamint a felügyelt példányok és példányok adatbázisok.

| Diagnosztikai telemetria iaadatbázisokhoz | Egyadatbázis- és készletezésű adatbázis-támogatás | Felügyelt példány adatbázisának támogatása |
| :------------------- | ----- | ----- |
| [Alapmetrikák:](#basic-metrics)Tartalmazza a DTU/CPU százalékos, DTU/CPU-korlát, fizikai adatok olvasási százaléka, napló írási százaléka, sikeres/sikertelen/tűzfal-kapcsolatok által blokkolt, munkamenetek százalékos, a dolgozók százalékos, a tárolás, a tárolási százalék és az XTP-tárolási százalék. | Igen | Nem |
| [Példány és alkalmazás speciális](#advanced-metrics): Tempdb rendszeradatbázis-adatokat és naplófájlméretet és a használt tempdb százalékos naplófájlt tartalmazza. | Igen | Nem |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): A lekérdezés futásidejű statisztikáival, például a PROCESSZOR-használattal és a lekérdezés időtartamával kapcsolatos statisztikákkal kapcsolatos információkat tartalmazza. | Igen | Igen |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): A lekérdezés várakozási statisztikáival kapcsolatos információkat tartalmaz (amelyeken a lekérdezések várakoztak), például a CPU, a LOG és a LOCKING. | Igen | Igen |
| [Hibák](#errors-dataset): Az adatbázis SQL-hibáival kapcsolatos információkat tartalmaz. | Igen | Igen |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): Arról a döngésző információt tartalmazza, hogy egy adatbázis mennyi időt töltött a különböző várakozási típusokra való várakozással. | Igen | Nem |
| [Időközök](#time-outs-dataset): Az adatbázis időhamenteivel kapcsolatos információkat tartalmaz. | Igen | Nem |
| [Blokkok](#blockings-dataset): Az adatbázisban lévő események blokkolásával kapcsolatos információkat tartalmazza. | Igen | Nem |
| [Holtpontok](#deadlocks-dataset): Az adatbázisholt-pontokról szóló információkat tartalmaz. | Igen | Nem |
| [Automatikus hangolás](#automatic-tuning-dataset): Az adatbázis automatikus hangolási javaslataival kapcsolatos információkat tartalmaz. | Igen | Nem |
| [SQLInsights](#intelligent-insights-dataset): Intelligens elemzési adatokat tartalmaz az adatbázis teljesítményével. További információ: [Intelligent Insights](sql-database-intelligent-insights.md). | Igen | Igen |

> [!NOTE]
> A diagnosztikai beállítások nem konfigurálhatók a **rendszeradatbázisokhoz,** például fő-, msdb-, modell-, erőforrás- és tempdb-adatbázisok.

## <a name="streaming-export-destinations"></a>Exportálási célhelyek streamelése

Ez a diagnosztikai telemetriai adatok streamelhető konkretizált a következő Azure-erőforrások elemzésre.

- **[Log Analytics munkaterület](#stream-into-sql-analytics)**:

  A [Log Analytics-munkaterületre](../azure-monitor/platform/resource-logs-collect-workspace.md) streamelt adatokat az [SQL Analytics](../azure-monitor/insights/azure-sql.md)felhasználhatja. Az SQL Analytics egy csak felhőalapú figyelési megoldás, amely az adatbázisok intelligens figyelését biztosítja, amely teljesítményjelentéseket, riasztásokat és kockázatcsökkentési javaslatokat tartalmaz. A Log Analytics-munkaterületre streamelt adatok elemezhetők más összegyűjtött figyelési adatokkal, és lehetővé teszik az Azure Monitor egyéb funkcióinak, például a riasztások nak és a vizualizációknak a kihasználását is.
- **[Azure Eseményközpontok:](#stream-into-event-hubs)**

  Az [Azure Event Hubra](../azure-monitor/platform/resource-logs-stream-event-hubs.md)streamelt adatok a következő funkciókat biztosítják:

  - **Naplót továbbít a külső gyártó naplózási és telemetriai rendszereibe:** Az összes metrikát és erőforrásnaplót egyetlen eseményközpontba streamelheti, hogy egy külső SIEM- vagy naplóelemző eszközhöz továbbítsa a naplóadatokat.
  - **Egyéni telemetriai és naplózási platform létrehozása:** Az eseményközpontok nagymértékben skálázható közzétételi-előfizetési jellege lehetővé teszi a metrikák és az erőforrásnaplók rugalmas betöltését egy egyéni telemetriai platformba. A részletekért tekintse meg [a globális méretezési telemetriai platform tervezése és méretezése az Azure Event Hubs-on.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)
  - **A szolgáltatás állapotának megtekintése a Power BI-ba történő adatfolyam-továbbítással:** Az Event Hubs, a Stream Analytics és a Power BI segítségével a diagnosztikai adatokat közel valós idejű elemzési adatokká alakíthatja az Azure-szolgáltatásokban. Lásd: Stream Analytics és Power BI: A megoldás részleteiért tekintse meg a Stream Analytics és a Power BI: A megoldással kapcsolatos részletekért tekintse meg a [valós idejű elemzési irányítópultot.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)
- **[Azure Storage :](#stream-into-azure-storage)**

  Az Azure [Storage-ba](../azure-monitor/platform/resource-logs-collect-storage.md) streamelt adatok lehetővé teszi, hogy archiválja a nagy mennyiségű diagnosztikai telemetriai adatok az előző két streamelési lehetőség költségének töredékéért.

Ez a diagnosztikai telemetriai streamelt egyik ilyen célok segítségével felmérni az erőforrás-kihasználtság és a lekérdezés-végrehajtási statisztikák a teljesítmény figyelése.

![Architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>A diagnosztikai telemetria ifolyam-exportálásának engedélyezése és konfigurálása

A metrikák at és a diagnosztikai telemetriai naplózást az alábbi módszerek egyikével engedélyezheti és kezelheti:

- Azure Portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager-sablon

> [!NOTE]
> A biztonsági telemetriai adatok naplózási naplójának engedélyezéséhez olvassa [el az Adatbázis naplózásának beállítása](sql-database-auditing.md#setup-auditing) és naplózási naplók beállítása az Azure Monitor [naplóiban és az Azure Event Hubs-ban.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>A diagnosztikai telemetria ifolyam-exportálásának konfigurálása

Az Azure Portal **diagnosztika beállításai** menüsegítségével engedélyezheti és konfigurálhatja a diagnosztikai telemetriai adatok streamelését. Emellett használhatja a PowerShell, az Azure CLI, a [REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)és a [Resource Manager-sablonok](../azure-monitor/platform/diagnostic-settings-template.md) diagnosztikai telemetriai adatok streamelésének konfigurálásához. Beállíthatja a következő úti célokat a diagnosztikai telemetriai adatok streameléséhez: Azure Storage, Azure Event Hubs és Azure Monitor naplók.

> [!IMPORTANT]
> A diagnosztikai telemetriai adatok streamelési exportálása alapértelmezés szerint nincs engedélyezve.

Válassza ki az alábbi lapok egyikét a részletes útmutatást a diagnosztikai telemetriai adatok streamelési exportálásának konfigurálásához az Azure Portalon, és parancsfájlok a PowerShell és az Azure CLI használatával.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="elastic-pools"></a>Rugalmas készletek

Beállíthat egy rugalmas készleterőforrást a következő diagnosztikai telemetriai adatok összegyűjtésére:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Rugalmas medence** | [Az alapszintű metrikák](sql-database-metrics-diag-logging.md#basic-metrics) eDTU/CPU-százalékot, eDTU/CPU-korlátot, fizikai adatolvasási százalékot, naplóírási százalékot, munkamenetek százalékos arányát, a dolgozók százalékos arányát, a tárolást, a tárolási százalékot, a tárolási korlátot és az XTP-tárolási százalékot tartalmaznak. |

A diagnosztikai telemetriai adatok rugalmas készletek és készletezett adatbázisok streamelésének konfigurálásához külön-külön kell konfigurálnia őket:

- Diagnosztikai telemetriai adatok streamelésének engedélyezése rugalmas készlethez
- Diagnosztikai telemetriai adatok streamelésének engedélyezése minden egyes adatbázishoz rugalmas készletben

A rugalmas készlettároló saját telemetriai adatokat külön minden egyes készletező adatbázis telemetriai adatok.

A diagnosztikai telemetriai adatok streamelésének engedélyezéséhez egy rugalmas készleterőforráshoz hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **rugalmas készlet** erőforrás az Azure Portalon.
2. Válassza **a Diagnosztikai beállítások lehetőséget**.
3. Válassza **a Diagnosztika bekapcsolása** lehetőséget, ha nincsenek korábbi beállítások, vagy válassza a **Szerkesztés beállítást** egy korábbi beállítás szerkesztéséhez.

   ![A rugalmas készletek diagnosztikájának engedélyezése](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

4. Adja meg a saját hivatkozásának beállítási nevét.
5. Válasszon célerőforrást a streamelési diagnosztikai adatokhoz: **Archiválás tárfiókba**, **Streamelés egy eseményközpontba**vagy **Küldés a Log Analytics szolgáltatásba**.
6. A naplóelemzéshez válassza a **Konfigurálás** és új munkaterület létrehozása lehetőséget az **Új munkaterület létrehozása**lehetőség kiválasztásával, vagy válasszon ki egy meglévő munkaterületet.
7. Jelölje be a rugalmas készlet diagnosztikai telemetriai adatok: **Alapszintű** metrikák jelölőnégyzetét.
   ![A rugalmas készletek diagnosztikájának konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

8. Kattintson a **Mentés** gombra.
9. Emellett konfigurálja a diagnosztikai telemetriai adatok streamelését a rugalmas készleten belül, amelyet figyelni szeretne a következő szakaszban ismertetett lépések végrehajtásával.

> [!IMPORTANT]
> A rugalmas készlet diagnosztikai telemetriai adatainak konfigurálása mellett a rugalmas készlet minden egyes adatbázisához diagnosztikai telemetriai adatokat is konfigurálnia kell.

### <a name="single-or-pooled-database"></a>Egyszeres vagy összevont adatbázis

Egyetlen vagy készletes adatbázis-erőforrást állíthat be a következő diagnosztikai telemetriai adatok gyűjtésére:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Egyszeres vagy összevont adatbázis** | [Az alapszintű metrikák](sql-database-metrics-diag-logging.md#basic-metrics) tartalmazza a DTU százalékos, DTU-korlátot, a PROCESSZOR százalékos számát, a fizikai adatok olvasási százalékát, a napló írási százalékát, a sikeres/sikertelen/tűzfal-kapcsolatok által letiltott értékeket, a munkamenetek százalékos arányát, a dolgozók százalékos arányát, a tárolást, a tárolási százalékot, az XTP-tárolási százalékot és a holtpontokat. |

Egyetlen vagy készletbe adott adatbázis diagnosztikai telemetriai adatainak streameléséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure **SQL-adatbázis-erőforrást.**
2. Válassza **a Diagnosztikai beállítások lehetőséget**.
3. Válassza **a Diagnosztika bekapcsolása** lehetőséget, ha nincsenek korábbi beállítások, vagy válassza a **Szerkesztés beállítást** egy korábbi beállítás szerkesztéséhez. Legfeljebb három párhuzamos kapcsolatot hozhat létre a diagnosztikai telemetriai adatok streameléséhez.
4. Válassza **a Diagnosztikai beállítás hozzáadása** lehetőséget a diagnosztikai adatok párhuzamos streamelésének több erőforráshoz való konfigurálásához.

   ![Diagnosztika engedélyezése egy- és készletezésű adatbázisokhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)

5. Adja meg a saját hivatkozásának beállítási nevét.
6. Válasszon célerőforrást a streamelési diagnosztikai adatokhoz: **Archiválás tárfiókba**, **Streamelés egy eseményközpontba**vagy **Küldés a Log Analytics szolgáltatásba**.
7. A szabványos, eseményalapú figyelési élményhez jelölje be a következő jelölőnégyzeteket az adatbázis-diagnosztikai napló telemetriájához: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errors**, **DatabaseWaitStatistics**, **Timeouts**, **Blocks**és **Holtpontok**.
8. Egy speciális, egy perces monitorozási élmény, jelölje be a jelölőnégyzetet **az alapvető** metrikák.

   ![Diagnosztika konfigurálása egy-, készletezésű vagy példányadatbázisokhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
9. Kattintson a **Mentés** gombra.
10. Ismételje meg ezeket a lépéseket minden figyelni kívánt adatbázisesetében.

> [!TIP]
> Ismételje meg ezeket a lépéseket minden egyes, figyelni kívánt adatbázis esetében.

### <a name="managed-instance"></a>Felügyelt példány

A következő diagnosztikai telemetriai adatok gyűjtésére beállíthat egy felügyelt példány-erőforrást:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Felügyelt példány** | [A ResourceUsageStats](#resource-usage-stats-for-managed-instances) virtuális magok számát, átlagos CPU-százalékot, IO-kérelmeket, olvasott/írott bájtokat, fenntartott tárhelyet és használt tárhelyet tartalmaz. |

A felügyelt példányok és példányadatbázisok diagnosztikai telemetriai adatainak streamelésének konfigurálásához külön-külön kell konfigurálnia az egyes adatokat:

- Diagnosztikai telemetriai adatok streamelésének engedélyezése a felügyelt példányhoz
- Diagnosztikai telemetriai adatok streamelésének engedélyezése az egyes példányok adatbázisában

A felügyelt példány tárolója saját telemetriai adatokat külön az egyes példányok adatbázis telemetriai adatok.

A felügyelt példány-erőforrás diagnosztikai telemetriai adatainak streameléséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **felügyelt példány** erőforrásaz Azure Portalon.
2. Válassza **a Diagnosztikai beállítások lehetőséget**.
3. Válassza **a Diagnosztika bekapcsolása** lehetőséget, ha nincsenek korábbi beállítások, vagy válassza a **Szerkesztés beállítást** egy korábbi beállítás szerkesztéséhez.

   ![Diagnosztika engedélyezése a felügyelt példányhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

4. Adja meg a saját hivatkozásának beállítási nevét.
5. Válasszon célerőforrást a streamelési diagnosztikai adatokhoz: **Archiválás tárfiókba**, **Streamelés egy eseményközpontba**vagy **Küldés a Log Analytics szolgáltatásba**.
6. A naplóelemzéshez válassza a **Konfigurálás** és új munkaterület létrehozása lehetőséget az **+Új munkaterület létrehozása**, vagy egy meglévő munkaterület használatával.
7. Jelölje be a példához használt diagnosztikai telemetriai adatok **jelölőnégyzetét: ResourceUsageStats**.

   ![Diagnosztika konfigurálása a felügyelt példányhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

8. Kattintson a **Mentés** gombra.
9. Emellett konfigurálja a diagnosztikai telemetriai adatok streamelését a felügyelt példányon belül a figyelni kívánt példányminden egyes példányához a következő szakaszban ismertetett lépések végrehajtásával.

> [!IMPORTANT]
> A felügyelt példány diagnosztikai telemetriai adatainak konfigurálása mellett minden példányadatbázishoz konfigurálnia kell a diagnosztikai telemetriai adatokat is.

### <a name="instance-database"></a>Példány-adatbázis

Beállíthat egy példány-adatbázis-erőforrást a következő diagnosztikai telemetriai adatok gyűjtésére:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Példány-adatbázis** | [A ResourceUsageStats](#resource-usage-stats-for-managed-instances) virtuális magok számát, átlagos CPU-százalékot, IO-kérelmeket, olvasott/írott bájtokat, fenntartott tárhelyet és használt tárhelyet tartalmaz. |

Egy példányadatbázis diagnosztikai telemetriai adatainak streamelésének engedélyezéséhez hajtsa végre az alábbi lépéseket:

1. Nyissa meg a **példány adatbázis-erőforrását** a felügyelt példányon belül.
2. Válassza **a Diagnosztikai beállítások lehetőséget**.
3. Válassza **a Diagnosztika bekapcsolása** lehetőséget, ha nincsenek korábbi beállítások, vagy válassza a **Szerkesztés beállítást** egy korábbi beállítás szerkesztéséhez.
   - Legfeljebb három (3) párhuzamos kapcsolatot hozhat létre a diagnosztikai telemetriai adatok streameléséhez.
   - Válassza a **+Add diagnostic setting** lehetőséget a diagnosztikai adatok párhuzamos streamelésének több erőforráshoz való konfigurálásához.

   ![Például adatbázisok diagnosztikának engedélyezése](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. Adja meg a saját hivatkozásának beállítási nevét.
5. Válasszon célerőforrást a streamelési diagnosztikai adatokhoz: **Archiválás tárfiókba**, **Streamelés egy eseményközpontba**vagy **Küldés a Log Analytics szolgáltatásba**.
6. Jelölje be az adatbázis-diagnosztikai telemetriai adatok jelölőnégyzetét: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**és **Errors**.
   ![A példányadatbázisok diagnosztikájának konfigurálása](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
7. Kattintson a **Mentés** gombra.
8. Ismételje meg ezeket a lépéseket minden egyes figyelni kívánt példányadatbázisesetében.

> [!TIP]
> Ismételje meg ezeket a lépéseket minden egyes figyelni kívánt példányadatbázisesetében.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

A PowerShell használatával metrikák és diagnosztika naplózása engedélyezheti.

- A metrikák és az erőforrásnaplók tárfiókban való tárolásának engedélyezéséhez használja ezt a parancsot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  A tárfiók azonosítója a céltárfiók erőforrásazonosítója.

- A metrikák és az erőforrásnaplók eseményközpontba való streamelésének engedélyezéséhez használja ezt a parancsot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Az Azure Service Bus szabályazonosítója (rule ID) egy sztring az alábbi formátumban:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Ha engedélyezni szeretné a metrikák és az erőforrásnaplók küldését a Log Analytics-munkaterületre, használja ezt a parancsot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- A Log Analytics-munkaterület erőforrás-azonosítóját (ResourceId) az alábbi paranccsal kérheti le:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Ezeket a paramétereket kombinálhatja is, ha több kimeneti eredményt szeretne kapni.

**Több Azure-erőforrás konfigurálása**

Több előfizetés támogatásához használja a PowerShell-parancsfájlt az [Azure-erőforrás-metrikák naplózásának engedélyezése a PowerShell használatával.](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/)

Adja meg a munkaterületi erőforrás-azonosító \<$WSID\> paraméterként a parancsfájl `Enable-AzureRMDiagnostics.ps1` végrehajtásakor, hogy több erőforrásból diagnosztikai adatokat küldjön a munkaterületre.

- A munkaterület-azonosító \<$WSID\> diagnosztikai adatok céljának lekérnie a következő parancsfájlt:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Cserélje \<le\> \<a subID-t az előfizetés-azonosítóra, RG_NAME\> az erőforráscsoport nevére, és \<WS_NAME\> a munkaterület nevére.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatával metrikák és diagnosztika naplózása engedélyezheti.

> [!IMPORTANT]
> A diagnosztikai naplózást engedélyező parancsfájlok az Azure CLI 1.0-s szolgáltatása esetén támogatottak. Az Azure CLI 2.0-s vagyonának jelenleg nem támogatott.

- A metrikák és az erőforrásnaplók tárfiókban való tárolásának engedélyezéséhez használja ezt a parancsot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  A tárfiók azonosítója a céltárfiók erőforrásazonosítója.

- A metrikák és az erőforrásnaplók eseményközpontba való streamelésének engedélyezéséhez használja ezt a parancsot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  A Service Bus szabályazonosítója a következő formátumú karakterlánc:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- Ha engedélyezni szeretné a metrikák és az erőforrásnaplók küldését a Log Analytics-munkaterületre, használja ezt a parancsot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Ezeket a paramétereket kombinálhatja is, ha több kimeneti eredményt szeretne kapni.

---

## <a name="stream-into-sql-analytics"></a>Streamelés az SQL Analytics szolgáltatásba

Az Azure SQL Analytics felhasználhatja a Log Analytics-munkaterületre streamelt SQL Database-metrikákat és erőforrásnaplókat. Az Azure SQL Analytics egy felhőalapú megoldás, amely figyeli az egyes adatbázisok, rugalmas készletek és készletezett adatbázisok teljesítményét, valamint a felügyelt példányok és példányok adatbázisait nagy léptékben és több előfizetésben. Segíthet az Azure SQL Database teljesítménymutatóinak összegyűjtésében és megjelenítésében, és beépített intelligenciával rendelkezik a teljesítményhibaelhárításhoz.

![Az Azure SQL Analytics – áttekintés](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>A telepítés áttekintése

Az Azure SQL Analytics szolgáltatással az Azure SQL Analytics segítségével figyelheti az Azure SQL-adatbázisok gyűjteményét az alábbi lépésekkel:

1. Hozzon létre egy Azure SQL Analytics-megoldást az Azure Piactérről.
2. Hozzon létre egy Log Analytics-munkaterületet a megoldásban.
3. Konfigurálja az adatbázisokat a diagnosztikai telemetriai adatok munkaterületre való streameléséhez.

Konfigurálhatja a diagnosztikai telemetriai adatok streamelési exportálását az Azure Portal diagnosztikai beállítások lapján található beépített **Küldés a loganalytics-nek** beállítás használatával. A Log Analytics-munkaterületre való streamelést a [PowerShell-parancsmagok](sql-database-metrics-diag-logging.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), az [Azure CLI](sql-database-metrics-diag-logging.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), az [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)vagy a [Resource Manager-sablonok](../azure-monitor/platform/diagnostic-settings-template.md)diagnosztikai beállításaival is engedélyezheti.

### <a name="create-an-azure-sql-analytics-resource"></a>Azure SQL Analytics-erőforrás létrehozása

1. Keresse meg az Azure SQL Analytics szolgáltatást az Azure Marketplace-en, és válassza ki.

   ![Az Azure SQL Analytics keresése a portálon](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Válassza a **Létrehozás lehetőséget** a megoldás áttekintő képernyőjén.

3. Töltse ki az Azure SQL Analytics-űrlapot a szükséges további információkkal: munkaterület neve, előfizetés, erőforráscsoport, hely és tarifacsomag.

   ![Az Azure SQL Analytics konfigurálása a portálon](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. A megerősítéshez válassza az **OK** gombot, majd a **Létrehozás gombot.**

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Az erőforrás konfigurálása metrikák és erőforrásnaplók rögzítésére

Külön konfigurálnia kell a diagnosztikai telemetriai adatfolyamot az egy- és készletezésű adatbázisokhoz, rugalmas készletekhez, felügyelt példányokhoz és példányadatbázisokhoz. A legegyszerűbb módja annak konfigurálása, ahol egy erőforrás rekordok metrikák az Azure Portal használatával. A részletes lépésekért [lásd: A diagnosztikai telemetria ifolyam-exportálásának konfigurálása.](sql-database-metrics-diag-logging.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry)

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Az Azure SQL Analytics használata figyeléshez és riasztáshoz

Az SQL Analytics hierarchikus irányítópultként való használatával megtekintheti az SQL-adatbázis erőforrásait.

- Az Azure SQL Analytics használatáról az [SQL-adatbázis figyelése az SQL Analytics használatával](../log-analytics/log-analytics-azure-sql.md)című témakörben olvashat.
- A riasztások SQL Analytics szolgáltatásban való beállításáról az [Adatbázis, a rugalmas készletek és a felügyelt példányok riasztások létrehozása című témakörben](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)olvashat.

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Az SQL Database-metrikák és az erőforrás-naplók streamelése az Event Hubs-ba a beépített stream használatával **egy eseményközpont-beállítás** az Azure Portalon. A Service Bus-szabályazonosítót a PowerShell-parancsmagok, az Azure CLI vagy az Azure Monitor REST API diagnosztikai beállítások használatával is engedélyezheti. Győződjön meg arról, hogy az eseményközpont ugyanabban a régióban van, mint az adatbázis és a kiszolgáló.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Mi a teendő a metrikákkal és az erőforrásnaplókkal az Eseményközpontokban?

Miután a kijelölt adatokat az Event Hubs-ba továbbítja, egy lépéssel közelebb kerül a speciális figyelési forgatókönyvek engedélyezéséhez. Az Event Hubs egy eseményfolyamat bejárati ajtajaként működik. Miután az adatokat egy eseményközpontba gyűjtötte, átalakítható és tárolható egy valós idejű elemzési szolgáltató vagy egy tárolóadapter használatával. Az Eseményközpontok leválasztja az események egy adatfolyamának termelését az események felhasználásától. Ily módon az eseményfogyasztók saját ütemezésük szerint férhetnek hozzá az eseményekhez. Az Event Hubs-ról további információt a következő témakörökben talál:

- [Mik azok az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Az Eseményközpontokban streamelt metrikák segítségével a következőkre használhatja:

- **A szolgáltatás állapotának megtekintése a hot-path adatok Power BI-ba való streamelésével**

  Az Event Hubs, a Stream Analytics és a Power BI használatával könnyedén átalakíthatja metrikáit és diagnosztikai adatait közel valós idejű elemzési adatokká azure-szolgáltatásaiban. Ha áttekintést szeretne kapni arról, hogyan állíthat be eseményközpontot, hogyan dolgozható fel adatokat a Stream Analytics szolgáltatással, és hogyan használhatja a Power BI-t kimenetként, olvassa el a Stream Analytics és a Power BI című [témakört.](../stream-analytics/stream-analytics-power-bi-dashboard.md)

- **Naplók streamelése harmadik féltől származó naplózási és telemetriai adatfolyamokba**

  Az Event Hubs streameléshasználatával a metrikákat és az erőforrásnaplókat különböző külső monitorozási és logelemzési megoldásokba szerezheti be.

- **Egyéni telemetriai és naplózási platform létrehozása**

  Van már egy egyéni tervezésű telemetriai platform, vagy fontolgatja, hogy egy? Az Event Hubs nagymértékben méretezhető közzététel-előfizetés jellege lehetővé teszi a metrikák és az erőforrásnaplók rugalmas betöltését. Tekintse meg [Dan Rosanova útmutatóját az Event Hubs globális szintű telemetriai platformon való használatáról.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="stream-into-azure-storage"></a>Streamelés az Azure Storage-ba

Metrikák at és erőforrásnaplókat tárolhat az Azure Storage-ban a beépített **Archiválás egy tárfiókhoz** beállítás használatával az Azure Portalon. A Storage-tárolást a PowerShell-parancsmagok, az Azure CLI vagy az Azure Monitor REST API-n keresztüli diagnosztikai beállítások használatával is engedélyezheti.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Metrikák és erőforrásnaplók sémája a tárfiókban

A metrikák és az erőforrásnaplók gyűjteményének beállítása után létrejön egy tárolótároló a kiválasztott tárfiókban, amikor az adatok első sorai elérhetők. A blobok szerkezete:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Vagy, egyszerűbben:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Az alapszintű metrikák blobneve például a következő lehet:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

A rugalmas készletből származó adatok tárolására szolgáló blobnév így néz ki:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Adatmegőrzési irányelv és árképzés

Ha az Event Hubs vagy a Storage-fiók lehetőséget választja, megadhat egy adatmegőrzési szabályzatot. Ez a házirend törli a kijelölt időszaknál régebbi adatokat. Ha megadja a Log Analytics, az adatmegőrzési szabályzat függ a kiválasztott tarifacsomag. Ebben az esetben a megadott ingyenes adatbetöltési egységek havonta több adatbázis ingyenes figyelését tehetik lehetővé. A diagnosztikai telemetriai adatok ingyenes egységeket meghaladó felhasználása költségeket vonhat maga után.

> [!IMPORTANT]
> A nehezebb számítási feladatokkal rendelkező aktív adatbázisok több adatot szeretne be, mint az írásnemi adatok. További információ: [Log analytics pricing](https://azure.microsoft.com/pricing/details/monitor/).

Ha Az Azure SQL Analytics szolgáltatást használja, figyelheti az adatok betöltésének felhasználását az Azure SQL Analytics navigációs menüjében az **OMS-munkaterület** kiválasztásával, majd a **Használat** és **a Becsült költségek kiválasztásával.**

## <a name="metrics-and-logs-available"></a>Elérhető mérőszámok és naplók

Az egyes adatbázisokhoz, készletezett adatbázisokhoz, rugalmas készletekhez, felügyelt példányhoz és példányadatbázisokhoz elérhető telemetriai adatok figyelése a cikk ebben a szakaszában van dokumentálva. Az SQL Analytics-en belüli összegyűjtött figyelési telemetriai adatok az [Azure Monitor naplólekérdezési](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nyelvének használatával saját egyéni elemzéshez és alkalmazásfejlesztéshez használhatók.

### <a name="basic-metrics"></a>Alapvető mutatók

Az alábbi táblázatokban az alapvető metrikák erőforrásonkénti részleteit találja.

> [!NOTE]
> Az alapszintű metrikák beállítás korábban Minden metrika néven volt ismert. A módosítás csak az elnevezést, és nem volt változás a metrikák figyelt. Ez a módosítás azért történt, hogy a jövőben további metrikakategóriák at lehessen bevezetni.

#### <a name="basic-metrics-for-elastic-pools"></a>Rugalmas készletek alapmetrikák

|**Erőforrás**|**Mérőszámok**|
|---|---|
|Rugalmas készlet|eDTU százalék, használt eDTU, eDTU-korlát, CPU-százalék, fizikai adatolvasási százalék, naplóírási százalék, munkamenetek százaléka, dolgozók százaléka, tárolás, tárolási százalék, tárolási korlát, XTP-tárolási százalék |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Az egy- és összevont adatbázisok alapvető metrikája

|**Erőforrás**|**Mérőszámok**|
|---|---|
|Egy- és összevont adatbázis|DTU százalék, használt DTU, DTU-korlát, PROCESSZOR százalék, fizikai adatolvasási százalék, naplóírási százalék, sikeres/sikertelen/tűzfal-kapcsolatok által blokkolt, munkamenetek százalékos aránya, dolgozók százaléka, tárolás, tárolási százalék, XTP-tárolási százalék és holtpont |

### <a name="advanced-metrics"></a>Speciális mérőszámok

A speciális mutatókkal kapcsolatos részleteket az alábbi táblázattartalmazza.

|**Metrika**|**Metrikus megjelenítendő név**|**Leírás**|
|---|---|---|
|sqlserver_process_core_percent<sup>1.</sup>|SQL Server folyamat alapszázaléka|Az SQL Server folyamat processzorhasználati százaléka az operációs rendszer szerint mérve.|
|<sup>1.</sup> sqlserver_process_memory_percent |SQL Server folyamatmemória százaléka|Az SQL Server folyamat memóriahasználati százaléka az operációs rendszer szerint mérve.|
|<sup>tempdb_data_size 2.</sup>| Tempdb-adatfájl mérete kilobájt |Tempdb adatfájl mérete kilobájt.|
|<sup>tempdb_log_size 2.</sup>| Tempdb naplófájl mérete Kilobájt |Tempdb naplófájl mérete kilobájt.|
|<sup>tempdb_log_used_percent 2.</sup>| Használt Tempdb százaléknapló |Tempdb százaléknapló használatban.|

<sup>1</sup> Ez a metrika a virtuális mag 2 virtuálismaggal és magasabb magokkal, illetve 200 DTU-val és magasabb magokkal rendelkező adatbázisokhoz érhető el. 

<sup>2</sup> Ez a metrika a virtuális mag 2 virtuálismaggal és magasabb magokkal, illetve 200 DTU-val és magasabb magokkal rendelkező adatbázisokhoz érhető el. Ez a metrika jelenleg nem érhető el a nagy kapacitású adatbázisok vagy adatraktárak.

### <a name="basic-logs"></a>Egyszerű naplók

Az összes naplóhoz elérhető telemetriai adatokat az alábbi táblázatok dokumentálják. Tekintse meg [a támogatott diagnosztikai telemetriai adatokat,](#diagnostic-telemetry-for-export-for-azure-sql-database) hogy megismerje, mely naplók támogatottak egy adott adatbázis-ízhez – az Azure SQL egyetlen, készletezett vagy példányadatbázis.

#### <a name="resource-usage-stats-for-managed-instances"></a>Erőforrás-használati statisztika a felügyelt példányokhoz

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure|
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: ResourceUsageStats |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: MANAGEDINSTANCES |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|A felügyelt példány neve |
|ResourceId|Erőforrás URI-ja |
|SKU_s|Felügyelt példány terméktermékterméktermék-terméke |
|virtual_core_count_s|Az elérhető virtuális magok száma |
|avg_cpu_percent_s|Átlagos CPU-százalék |
|reserved_storage_mb_s|Fenntartott tárolókapacitás a felügyelt példányon |
|storage_space_used_mb_s|Használt tároló a felügyelt példányon |
|io_requests_s|IOPS-szám |
|io_bytes_read_s|IOPS-bájtok olvasása |
|io_bytes_written_s|IOPS bájtok írásban |

#### <a name="query-store-runtime-statistics"></a>A Query Store futásidejű statisztikái

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: QueryStoreRuntimeStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreRuntimeStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|query_hash_s|Kivonat lekérdezése |
|query_plan_hash_s|Lekérdezési terv kivonata |
|statement_sql_handle_s|Utasítás sql leírója |
|interval_start_time_d|Az intervallum kezdő dátumeltolása az 1900-1-1 közötti kullancsok számában |
|interval_end_time_d|Az intervallum záró dátumeltolása az 1900-1-1 közötti kullancsok számában |
|logical_io_writes_d|Logikai I-írások teljes száma |
|max_logical_io_writes_d|A logikai ÍRÁSok maximális száma végrehajtásonként |
|physical_io_reads_d|Fizikai IO-olvasások teljes száma |
|max_physical_io_reads_d|A logikai IO-olvasások maximális száma végrehajtásonként |
|logical_io_reads_d|Logikai IO-olvasások teljes száma |
|max_logical_io_reads_d|A logikai IO-olvasások maximális száma végrehajtásonként |
|execution_type_d|Végrehajtás típusa |
|count_executions_d|A lekérdezés végrehajtásáinak száma |
|cpu_time_d|A lekérdezés által felhasznált teljes processzoridő mikroszekundumban |
|max_cpu_time_d|Maximális cpu-időfogyasztó egyetlen végrehajtással mikroszekundumban |
|dop_d|A párhuzamosság fokainak összege |
|max_dop_d|Az egyetlen végrehajtáshoz használt párhuzamosság maximális foka |
|rowcount_d|Visszaadott sorok száma összesen |
|max_rowcount_d|Az egyetlen végrehajtásban visszaadott sorok maximális száma |
|query_max_used_memory_d|A KB-ban felhasznált memória teljes mennyisége |
|max_query_max_used_memory_d|Az egyetlen végrehajtás által használt memória maximális mennyisége KB-ban |
|duration_d|Teljes végrehajtási idő mikroszekundumban |
|max_duration_d|Egyetlen végrehajtás maximális végrehajtási ideje |
|num_physical_io_reads_d|Fizikai olvasások teljes száma |
|max_num_physical_io_reads_d|A fizikai olvasások maximális száma végrehajtásonként |
|log_bytes_used_d|A felhasznált naplóbájtok teljes mennyisége |
|max_log_bytes_used_d|A végrehajtásonként használt naplóbájtok maximális mennyisége |
|query_id_d|A lekérdezés azonosítója a Lekérdezéstárolóban |
|plan_id_d|A terv azonosítója a Lekérdezéstárolóban |

További információ a [Query Store futásidejű statisztikáinak adatairól.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

#### <a name="query-store-wait-statistics"></a>Lekérdezéstároló várakozási statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: QueryStoreWaitStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|wait_category_s|A várakozás kategóriája |
|is_parameterizable_s|Paraméterezhető-e a lekérdezés? |
|statement_type_s|A kimutatás típusa |
|statement_key_hash_s|Kimutatás kulcskivonata |
|exec_type_d|A végrehajtás típusa |
|total_query_wait_time_ms_d|A lekérdezés teljes várakozási ideje az adott várakozási kategóriában |
|max_query_wait_time_ms_d|A lekérdezés maximális várakozási ideje az adott várakozási kategóriában az egyes végrehajtásban |
|query_param_type_d|0 |
|query_hash_s|Lekérdezési kivonat a Lekérdezéstárolóban |
|query_plan_hash_s|Lekérdezési terv kivonata a Lekérdezéstárolóban |
|statement_sql_handle_s|Kimutatásleíró a Lekérdezéstárolóban |
|interval_start_time_d|Az intervallum kezdő dátumeltolása az 1900-1-1 közötti kullancsok számában |
|interval_end_time_d|Az intervallum záró dátumeltolása az 1900-1-1 közötti kullancsok számában |
|count_executions_d|A lekérdezés végrehajtásának száma |
|query_id_d|A lekérdezés azonosítója a Lekérdezéstárolóban |
|plan_id_d|A terv azonosítója a Lekérdezéstárolóban |

További információ a [Lekérdezéstároló várakozási statisztikáinak adatairól](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Hibák adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: Hibák |
|OperationName|A művelet neve. Mindig: ErrorEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|Üzenet|Egyszerű szöveges hibaüzenet |
|user_defined_b|A felhasználó által definiált hibaüzenet |
|error_number_d|Hibakód |
|Severity|A hiba súlyossága |
|state_d|A hiba állapota |
|query_hash_s|A sikertelen lekérdezés kivonatának lekérdezése, ha rendelkezésre áll |
|query_plan_hash_s|A sikertelen lekérdezés lekérdezési tervkivonata, ha elérhető |

További információ az [SQL Server hibaüzeneteiről](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

#### <a name="database-wait-statistics-dataset"></a>Adatbázis várakozási statisztikai adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: DatabaseWaitStatistics |
|OperationName|A művelet neve. Mindig: DatabaseWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|wait_type_s|A várakozási típus neve |
|start_utc_date_t [UTC]|Mért időszak kezdési időpontja |
|end_utc_date_t [UTC]|Mért időszak befejezési ideje |
|delta_max_wait_time_ms_d|Maximális várakozási idő végrehajtásonként |
|delta_signal_wait_time_ms_d|Összes jel várakozási ideje |
|delta_wait_time_ms_d|Teljes várakozási idő az időszakban |
|delta_waiting_tasks_count_d|Várakozási feladatok száma |

További információ az [adatbázisvárakozási statisztikáról](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Idő-out adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: Idő-out |
|OperationName|A művelet neve. Mindig: TimeoutEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|error_state_d|Hibaállapot-kód |
|query_hash_s|Lekérdezési kivonat, ha elérhető |
|query_plan_hash_s|Lekérdezési terv kivonata, ha elérhető |

#### <a name="blockings-dataset"></a>Blokkolja az adatkészletet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: Blokkok |
|OperationName|A művelet neve. Mindig: BlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|lock_mode_s|A lekérdezés által használt zárolási mód |
|resource_owner_type_s|A zár tulajdonosa |
|blocked_process_filtered_s|Blokkolt folyamatjelentés XML-fájlja |
|duration_d|A zár időtartama mikroszekundumban |

#### <a name="deadlocks-dataset"></a>Holtpontadatkészletek

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC] |Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: Holtpontok |
|OperationName|A művelet neve. Mindig: DeadlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|deadlock_xml_s|Holtpontjelentés XML-fájlja |

#### <a name="automatic-tuning-dataset"></a>Automatikus hangolási adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlőazonosítója |
|SourceSystem|Mindig: Azure |
|Időgenerálva [UTC]|Időbélyegző a napló rögzítésekekénekkor |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. Sql |
|Kategória|A kategória neve. Mindig: Automatikus hangolás |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: SZERVEREK/ADATBÁZISOK |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoportjának neve |
|LogicalServerName_s|Az adatbázis kiszolgálójának neve |
|LogicalDatabaseName_s|Az adatbázis neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|RecommendationHash_s|Az automatikus hangolási javaslat egyedi kivonata |
|OptionName_s|Automatikus hangolási művelet |
|Schema_s|Adatbázisséma |
|Table_s|Érintett táblázat |
|IndexName_s|Tárgymutató neve |
|IndexColumns_s|Oszlop neve |
|IncludedColumns_s|Oszlopokkal együtt |
|EstimatedImpact_s|A JSON automatikus hangolási ajánlás becsült hatása |
|Event_s|Az automatikus hangolási esemény típusa |
|Timestamp_t|Utolsó frissítés időbélyege |

#### <a name="intelligent-insights-dataset"></a>Intelligens Insights-adatkészlet

További információ az [Intelligent Insights naplóformátumról.](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>További lépések

A naplózás engedélyezéséről, valamint a különböző Azure-szolgáltatások által támogatott metrikák és naplókategóriák megismerésének módjáról a következő témakörben olvashat:

- [A Microsoft Azure mutatóinak áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Az Azure platformnaplók áttekintése](../azure-monitor/platform/platform-logs-overview.md)

Az Event Hubs ról az útmutatót olvashatja:

- [Mi az az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

A naplóelemzésből származó telemetriai adatok on-ra vonatkozó riasztások beállításáról a következő témakörökben olvashat:

- [Riasztások létrehozása az SQL Database és a felügyelt példány számára](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
