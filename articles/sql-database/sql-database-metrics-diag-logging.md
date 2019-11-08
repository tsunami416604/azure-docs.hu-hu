---
title: Metrikák és diagnosztikai naplózás
description: Megtudhatja, hogyan engedélyezheti a diagnosztika használatát a Azure SQL Databaseban az erőforrás-felhasználással és a lekérdezés-végrehajtási statisztikákkal kapcsolatos információk tárolásához.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/21/2019
ms.openlocfilehash: d51acaff89c2a8589b6b524c112c11f9c4f18220
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821776"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database metrikák és diagnosztikai naplózás

Ebben a témakörben megtudhatja, hogyan konfigurálhatja a diagnosztikai telemetria naplózását Azure SQL Database a Azure Portal, a PowerShell, az Azure CLI, a Azure Monitor REST API és a Azure Resource Manager sablon segítségével. Ezek a diagnosztika használhatók az erőforrás-kihasználtság és a lekérdezés-végrehajtási statisztika mérésére.

Az önálló adatbázisok, a rugalmas készletekben található készletezett adatbázisok és a felügyelt példányokban lévő példány-adatbázisok stream-mérőszámokat és diagnosztikai naplókat biztosítanak a teljesítmény monitorozásához. Beállíthat egy adatbázist az erőforrás-használat, a feldolgozók és a munkamenetek továbbítására, valamint a következő Azure-erőforrások egyikéhez való kapcsolódásra:

- **Azure SQL Analytics**: az Azure SQL-adatbázisok intelligens monitorozásához, amely tartalmazza a teljesítményadatokat, a riasztásokat és a kockázatcsökkentő javaslatokat.
- **Azure Event Hubs**: a SQL Database telemetria integrálása az egyéni figyelési megoldásokkal vagy a gyors folyamatokkal.
- **Azure Storage**: nagy mennyiségű telemetria archiválása az ár töredékéért.

    ![Architektúra](./media/sql-database-metrics-diag-logging/architecture.png)

További információ a különböző Azure-szolgáltatások által támogatott metrikákkal és naplózási kategóriákkal kapcsolatban:

- [A Microsoft Azure metrikáinak áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Az Azure Diagnostics-naplók áttekintése](../azure-monitor/platform/resource-logs-overview.md)

Ez a cikk útmutatást nyújt az Azure SQL Database-adatbázisok, rugalmas készletek és felügyelt példányok diagnosztikai telemetria engedélyezéséhez. Emellett azt is megtudhatja, hogyan konfigurálhatja a Azure SQL Analytics felügyeleti eszközként az adatbázis-diagnosztika telemetria megtekintéséhez.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Diagnosztikai telemetria naplózásának engedélyezése

Az alábbi módszerek egyikének használatával engedélyezheti és kezelheti a metrikákat és a diagnosztikai telemetria naplózását:

- Azure Portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager-sablon

Ha engedélyezi a metrikákat és a diagnosztikai naplózást, meg kell adnia a diagnosztikai telemetria gyűjtéséhez szükséges Azure-erőforrás célhelyét. Az elérhető lehetőségek a következők:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Kiépítheti az új Azure-erőforrásokat, vagy kiválaszthat egy meglévő erőforrást. Miután kiválasztott egy erőforrást a **diagnosztikai beállítások** lehetőséggel, adja meg a gyűjteni kívánt adatokat.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Támogatott diagnosztikai naplózás az Azure SQL Database-adatbázisok és-példányok adatbázisaihoz

A metrikák és diagnosztikai naplózás engedélyezése az SQL-adatbázisokban – alapértelmezés szerint nincsenek engedélyezve.

Beállíthatja az Azure SQL Database-adatbázisokat és a példány-adatbázisokat a következő diagnosztikai telemetria gyűjtéséhez:

| Adatbázisok figyelése telemetria | Önálló adatbázis és készletezett adatbázis-támogatás | Példány-adatbázis támogatása |
| :------------------- | ----- | ----- |
| [Alapszintű mérőszámok](#basic-metrics): tartalmazza a DTU/CPU-arányt, a DTU/CPU-korlátot, a fizikai adatok olvasási százalékos arányát, a napló írási százalékos arányát, a sikeres/sikertelen/letiltott/blokkolt, a munkamenetek százalékos arányát, a feldolgozói százalékos tárolási százalék. | Igen | Nem |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): a lekérdezési futtatókörnyezet statisztikáit, például a CPU-használat és a lekérdezés időtartamára vonatkozó statisztikai adatokat tartalmazza. | Igen | Igen |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): a lekérdezési várakozási statisztikával kapcsolatos információkat tartalmaz (a lekérdezéseket várta), például a processzort, a naplót és a zárolást. | Igen | Igen |
| [Hibák](#errors-dataset): az adatbázis SQL-hibáiról tartalmaz információkat. | Igen | Igen |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): információ arról, hogy mennyi időt töltöttek egy adatbázis a különböző várakozási típusoknál. | Igen | Nem |
| [Időtúllépések](#time-outs-dataset): az adatbázis-időtúllépésekkel kapcsolatos információkat tartalmaz. | Igen | Nem |
| [Blokkok](#blockings-dataset): az adatbázis-események blokkolásával kapcsolatos információkat tartalmaz. | Igen | Nem |
| [Holtpontok](#deadlocks-dataset): az adatbázis holtpont eseményeivel kapcsolatos információkat tartalmaz. | Igen | Nem |
| [AutomaticTuning](#automatic-tuning-dataset): az adatbázis Automatikus hangolási javaslataival kapcsolatos információkat tartalmaz. | Igen | Nem |
| [SQLInsights](#intelligent-insights-dataset): Intelligent Insightst tartalmaz egy adatbázis teljesítményében. További információ: [Intelligent Insights](sql-database-intelligent-insights.md). | Igen | Igen |

> [!IMPORTANT]
> A rugalmas készletek és a felügyelt példányok külön diagnosztikai telemetria rendelkeznek a bennük található adatbázisokból. Ez azért fontos, mert a diagnosztikai telemetria külön van konfigurálva az egyes erőforrások esetében, az alábbi módon dokumentálva.

> [!NOTE]
> A biztonsági naplózási és a SQLSecurityAuditEvents naplók nem engedélyezhetők az adatbázis-diagnosztika beállításaiban (bár a képernyőn látható). A naplózási naplózás engedélyezéséhez tekintse meg az [adatbázis naplózásának beállítása](sql-database-auditing.md#subheading-2)és a [naplók naplózása Azure monitor-naplókban és az Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)című témakört.

## <a name="azure-portal"></a>Azure Portal

A diagnosztikai **Beállítások** menüt a Azure Portal minden egyes, készletezett vagy példány adatbázisához használhatja a diagnosztikai telemetria folyamatos átviteléhez. Emellett a diagnosztikai telemetria külön is konfigurálható az adatbázis-tárolók esetében: rugalmas készletek és felügyelt példányok. A következő célhelyek megadásával továbbíthatja a diagnosztikai telemetria: Azure Storage, Azure Event Hubs és Azure Monitor naplók.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Diagnosztikai telemetria-továbbítás konfigurálása rugalmas készletekhez

   ![Rugalmas készlet ikonja](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

A következő diagnosztikai telemetria gyűjtéséhez beállíthatja a rugalmas készlet erőforrásait:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Rugalmas készlet** | Az [alapszintű mérőszámok](sql-database-metrics-diag-logging.md#basic-metrics) a EDTU/CPU-hányadot, a EDTU/CPU-korlátot, a fizikai adatok olvasási százalékos arányát, a napló írási arányát, a munkamenetek százalékos arányát, a feldolgozók százalékos arányát, a tárterületet, a tárolási |

A rugalmas készletekben lévő rugalmas készletek és adatbázisok diagnosztikai telemetria **folyamatos konfigurálásához külön kell konfigurálnia** a következőket:

- A diagnosztikai telemetria egy rugalmas készlethez való közvetítésének engedélyezése, **valamint**
- Diagnosztikai telemetria a rugalmas készletben lévő minden adatbázishoz való továbbításának engedélyezése

Ennek az az oka, hogy a rugalmas készlet egy adatbázis-tároló, amely a saját telemetria elkülöníti az egyes adatbázis-telemetria.

Az alábbi lépéseket követve engedélyezheti a diagnosztikai telemetria adatfolyam-továbbítását egy rugalmas készlet erőforrásaihoz:

1. Nyissa meg a **rugalmas készlet** erőforrását Azure Portal.
1. Válassza a **diagnosztikai beállítások**lehetőséget.
1. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.

   ![Diagnosztika engedélyezése rugalmas készletekhez](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
1. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub**felé, vagy **Küldés log Analytics**.
1. A log Analytics esetében válassza a **Konfigurálás** lehetőséget, és hozzon létre egy új munkaterületet az **+ Új munkaterület létrehozása**lehetőség kiválasztásával, vagy válasszon egy meglévő munkaterületet.
1. Jelölje be a rugalmas készlet diagnosztikai telemetria: **alapszintű** mérőszámok jelölőnégyzetét.
   ![a rugalmas készletek diagnosztika konfigurálását](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Kattintson a **Mentés** gombra.
1. Továbbá a következő szakaszban ismertetett lépések végrehajtásával konfigurálja a diagnosztikai telemetria adatfolyam-továbbítását a figyelni kívánt rugalmas készletben lévő minden egyes adatbázishoz.

> [!IMPORTANT]
> A rugalmas készlethez tartozó diagnosztikai telemetria konfigurálásán kívül a rugalmas készletben lévő összes adatbázishoz is be kell állítania a diagnosztikai telemetria, az alábbi leírások szerint.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Az önálló adatbázis vagy a rugalmas készletben lévő adatbázis diagnosztikai telemetria folyamatos átvitelének konfigurálása

   ![SQL Database ikon](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

A diagnosztikai telemetria egyetlen vagy készletezett adatbázishoz való továbbításának engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure **SQL Database** -erőforrást.
1. Válassza a **diagnosztikai beállítások**lehetőséget.
1. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.
   - Legfeljebb három párhuzamos kapcsolatot hozhat létre a stream Diagnostics telemetria.
   - Válassza a **+ diagnosztikai beállítások hozzáadása** lehetőséget a diagnosztikai információk párhuzamos átvitelének több erőforráshoz való konfigurálásához.

   ![Diagnosztika engedélyezése egyetlen, készletezett vagy példányos adatbázishoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
1. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub**felé, vagy **Küldés log Analytics**.
1. A standard, eseményvezérelt figyelési felület esetén jelölje be a következő jelölőnégyzeteket az adatbázis-diagnosztikai napló telemetria: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** , **Hibák**, **DatabaseWaitStatistics**, **időtúllépések**, **blokkok**és **holtpontok**.
1. A speciális, egyperces figyelési élmény érdekében jelölje be az **alapszintű** mérőszámok jelölőnégyzetét.
   ![a diagnosztika konfigurálása egyetlen, készletezett vagy példányos adatbázishoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Kattintson a **Mentés** gombra.
1. Ismételje meg ezeket a lépéseket minden figyelni kívánt adatbázis esetében.

> [!NOTE]
> A biztonsági naplózási és a SQLSecurityAuditEvents naplók nem engedélyezhetők az adatbázis-diagnosztika beállításaiban (bár a képernyőn láthatók). A naplózási naplózás engedélyezéséhez tekintse meg az [adatbázis naplózásának beállítása](sql-database-auditing.md#subheading-2)és a [naplók naplózása Azure monitor-naplókban és az Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)című témakört.
> [!TIP]
> Ismételje meg ezeket a lépéseket minden figyelni kívánt Azure SQL Database.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Diagnosztikai telemetria adatfolyamának konfigurálása felügyelt példányokhoz

   ![Felügyelt példány ikonja](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

A következő diagnosztikai telemetria gyűjtéséhez beállíthat felügyelt példány-erőforrást:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Felügyelt példány** | A [ResourceUsageStats](#resource-usage-stats-for-managed-instance) virtuális mag-darabszámot, átlagos CPU-százalékot, i/o-kérelmeket, írási/olvasási, foglalt tárolóhelyet és felhasznált tárolóhelyet tartalmaz. |

A felügyelt példányok és példányok adatbázisaihoz tartozó diagnosztikai telemetria konfigurálásához külön **kell konfigurálnia a következőket** :

- A diagnosztikai telemetria adatfolyam-továbbításának engedélyezése a felügyelt példányhoz, **és**
- Diagnosztikai telemetria az egyes példány-adatbázisokhoz való továbbításának engedélyezése

Ennek az az oka, hogy a felügyelt példány egy saját telemetria rendelkező adatbázis-tároló, amely elkülöníti az egyes példányok adatbázis-telemetria.

Az alábbi lépéseket követve engedélyezheti a diagnosztikai telemetria a felügyelt példányokhoz tartozó erőforrásokhoz való továbbítását:

1. Nyissa meg Azure Portal **felügyelt példány** erőforrását.
1. Válassza a **diagnosztikai beállítások**lehetőséget.
1. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.

   ![Diagnosztika engedélyezése felügyelt példányhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
1. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub**felé, vagy **Küldés log Analytics**.
1. A log Analytics esetében válassza a **Konfigurálás** lehetőséget, és hozzon létre egy új munkaterületet az **+ Új munkaterület létrehozása**lehetőség kiválasztásával, vagy használjon egy meglévő munkaterületet.
1. Jelölje be a példány diagnosztikai telemetria: **ResourceUsageStats**jelölőnégyzetet.
   ![a diagnosztika konfigurálása felügyelt példányhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Kattintson a **Mentés** gombra.
1. Emellett a következő szakaszban ismertetett lépéseket követve konfigurálja a diagnosztikai telemetria adatfolyam-továbbítását a felügyelt példány minden egyes példány-adatbázisához.

> [!IMPORTANT]
> A felügyelt példányok diagnosztikai telemetria konfigurálása mellett az alább leírtaknak megfelelően konfigurálnia kell az egyes példány-adatbázisok diagnosztikai telemetria is.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>A diagnosztikai telemetria a példány-adatbázisokhoz való továbbításának konfigurálása

   ![Példány-adatbázis felügyelt példány ikonjában](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Az alábbi lépéseket követve engedélyezheti a diagnosztikai telemetria adatfolyam-továbbítását a példány-adatbázisok számára:

1. Nyissa meg a **példány adatbázis** -erőforrását a felügyelt példányon belül.
1. Válassza a **diagnosztikai beállítások**lehetőséget.
1. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.
   - Akár három (3) párhuzamos kapcsolatot hozhat létre a stream Diagnostics telemetria.
   - Válassza a **+ diagnosztikai beállítások hozzáadása** lehetőséget a diagnosztikai információk párhuzamos átvitelének több erőforráshoz való konfigurálásához.

   ![Diagnosztika engedélyezése példány-adatbázisokhoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
1. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub**felé, vagy **Küldés log Analytics**.
1. Jelölje be az adatbázis-diagnosztika telemetria: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** és **hibák**jelölőnégyzetét.
   ![a diagnosztika konfigurálása a példány adatbázisaihoz](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Kattintson a **Mentés** gombra.
1. Ismételje meg ezeket a lépéseket minden figyelni kívánt példány-adatbázis esetében.

> [!TIP]
> Ismételje meg ezeket a lépéseket minden figyelni kívánt példány-adatbázis esetében.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A metrikák és a diagnosztika naplózása a PowerShell használatával engedélyezhető.

- A következő parancs használatával engedélyezheti a diagnosztikai naplók tárolását egy Storage-fiókban:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A Storage-fiók azonosítója a cél Storage-fiók erőforrás-azonosítója.

- Ha engedélyezni szeretné a diagnosztikai naplók továbbítását az Event hub-ba, használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   A Azure Service Bus szabály azonosítója a következő formátumú karakterlánc:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- A diagnosztikai naplók Log Analytics munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics munkaterület erőforrás-AZONOSÍTÓját a következő paranccsal szerezheti be:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Ezeket a paramétereket kombinálva több kimeneti beállítást is engedélyezhet.

### <a name="to-configure-multiple-azure-resources"></a>Több Azure-erőforrás konfigurálása

Több előfizetés támogatásához használja a PowerShell-parancsfájlt az [Azure erőforrás-metrikák naplózásának engedélyezése a PowerShell használatával](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/).

Adja meg a munkaterület erőforrás-AZONOSÍTÓját \<$WSID\> paraméterként a parancsfájl végrehajtásakor `Enable-AzureRMDiagnostics.ps1` a diagnosztikai adatok több erőforrásból a munkaterületre való küldéséhez.

- A következő parancsfájl használatával kérheti le a munkaterület AZONOSÍTÓját, \<$WSID\> a diagnosztikai adataihoz tartozó célhelyről:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Cserélje le \<subID\>re az előfizetés-AZONOSÍTÓval, \<RG_NAME\> az erőforráscsoport nevével, és \<WS_NAME\> a munkaterület nevével.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI használatával engedélyezheti a metrikákat és a diagnosztikai naplózást.

> [!NOTE]
> A diagnosztikai naplózást engedélyező parancsfájlok az Azure CLI 1.0-s verziójában támogatottak. Vegye figyelembe, hogy a CLI 2.0-s verzió jelenleg nem támogatott.

- A következő parancs használatával engedélyezheti a diagnosztikai naplók tárolását egy Storage-fiókban:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   A Storage-fiók azonosítója a cél Storage-fiók erőforrás-azonosítója.

- A diagnosztikai naplók esemény-központba való továbbításának engedélyezéséhez használja a következő parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   A Service Bus szabály azonosítója a következő formátumú karakterlánc:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- A diagnosztikai naplók Log Analytics munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Ezeket a paramétereket kombinálva több kimeneti beállítást is engedélyezhet.

### <a name="rest-api"></a>REST API

További információ [a diagnosztikai beállítások módosításáról a Azure Monitor REST API használatával](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Resource Manager-sablon

Olvassa el, hogyan [engedélyezheti a diagnosztikai beállításokat az erőforrás-létrehozáshoz Resource Manager-sablon használatával](../azure-monitor/platform/diagnostic-settings-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Stream a Azure SQL Analyticsba

A Azure SQL Analytics egy felhőalapú megoldás, amely az Azure SQL Database-adatbázisok, rugalmas készletek és felügyelt példányok teljesítményét figyeli nagy léptékben és több előfizetésen keresztül. Segítséget nyújt Azure SQL Database teljesítmény-mérőszámok gyűjtéséhez és megjelenítéséhez, és beépített intelligenciával rendelkezik a teljesítménnyel kapcsolatos hibaelhárításhoz.

![Azure SQL Analytics áttekintése](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database metrikák és diagnosztikai naplók adatfolyamként továbbíthatók Azure SQL Analytics a portál diagnosztikai beállítások lapján található beépített **küldés log Analytics** lehetőséggel. A log Analytics a PowerShell-parancsmagok, az Azure CLI vagy a Azure Monitor REST API segítségével is engedélyezhető diagnosztikai beállításokkal.

### <a name="installation-overview"></a>A telepítés áttekintése

SQL Database flottát Azure SQL Analytics használatával figyelheti. Hajtsa végre a következő lépéseket:

1. Hozzon létre egy Azure SQL Analytics megoldást az Azure Marketplace-en.
2. Hozzon létre egy figyelési munkaterületet a megoldásban.
3. Konfigurálja az adatbázisokat a telemetria a munkaterületre.

Ha rugalmas készleteket vagy felügyelt példányokat használ, a diagnosztika telemetria is be kell állítania ezekből az erőforrásokból.

### <a name="create-azure-sql-analytics-resource"></a>Azure SQL Analytics erőforrás létrehozása

1. Keresse meg Azure SQL Analytics az Azure Marketplace-en, és válassza ki.

   ![Azure SQL Analytics keresése a portálon](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Válassza a **Létrehozás** lehetőséget a megoldás áttekintés képernyőjén.

3. Töltse ki a Azure SQL Analytics űrlapot a szükséges további információkkal: munkaterület neve, előfizetés, Erőforráscsoport, hely és árképzési szintje.

   ![Azure SQL Analytics konfigurálása a portálon](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. A megerősítéshez kattintson **az OK gombra** , majd válassza a **Létrehozás**lehetőséget.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Adatbázisok konfigurálása a metrikák és diagnosztikai naplók rögzítéséhez

A legkönnyebben konfigurálhatja, hogy az adatbázisok hol legyenek rögzítve a metrikák a Azure Portal használatával. Az előzőekben leírtak szerint lépjen a SQL Database erőforrásra a Azure Portal, és válassza a **diagnosztikai beállítások**lehetőséget.

Ha rugalmas készleteket vagy felügyelt példányokat használ, ezen erőforrások diagnosztikai beállításait is be kell állítania ahhoz, hogy a diagnosztika telemetria a munkaterületre.

### <a name="use-the-sql-analytics-solution-for-monitoring-and-alerting"></a>Az SQL Analytics megoldás használata figyeléshez és riasztáshoz

A SQL Database-erőforrások megtekintéséhez használhatja az SQL Analytics hierarchikus irányítópultját.

- Az SQL Analytics megoldás használatának megismeréséhez tekintse meg [a SQL Database figyelése az SQL Analytics megoldás használatával](../log-analytics/log-analytics-azure-sql.md)című témakört.
- Ha szeretné megtudni, hogyan állíthat be riasztásokat SQL Database és felügyelt példányhoz az SQL Analytics alapján, tekintse meg a [riasztások létrehozása SQL Database és felügyelt példányhoz](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)című témakört.

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

SQL Database metrikák és diagnosztikai naplók továbbítása a Event Hubsba a beépített stream használatával a Azure Portal **egy Event hub** -beállításával. A Service Bus-szabály AZONOSÍTÓját a PowerShell-parancsmagok, az Azure CLI vagy a Azure Monitor REST API használatával is engedélyezheti diagnosztikai beállításokkal.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Mi a teendő a metrikák és a diagnosztikai naplók használatával Event Hubs

A kiválasztott adatoknak a Event Hubsba való továbbítása után egy lépéssel közelebb kerül a speciális figyelési forgatókönyvek engedélyezéséhez. Event Hubs az esemény-adatcsatorna bejárati ajtaja. Az adatok egy esemény központba való gyűjtése után a rendszer átalakíthatja és tárolhatja a valós idejű elemzési szolgáltató vagy a Storage-adapter használatával. Event Hubs leválasztja az események adatfolyamának előállítását az események fogyasztása alapján. Így a felhasználók a saját ütemterv szerint férhetnek hozzá az eseményekhez. További információ a Event Hubsről:

- [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

A Event Hubs a következő stream-metrikákat használhatja:

- **A szolgáltatás állapotának megtekintése a gyors elérésű adatoknak a Power BIba való továbbításával**. Event Hubs, Stream Analytics és Power BI használatával könnyedén átalakíthatja a metrikákat és a diagnosztikai adatokat az Azure-szolgáltatások közel valós idejű elemzéséhez. Az Event hub beállításával, az Stream Analyticsával történő adatfeldolgozással és a Power BI kimenetként való használatával kapcsolatos információkért tekintse meg a [stream Analytics és a Power bi](../stream-analytics/stream-analytics-power-bi-dashboard.md)című témakört.

- **A stream a harmadik féltől származó naplózási és telemetria adatfolyamokat naplózza**. Event Hubs streaming használatával a metrikák és diagnosztikai naplók különböző külső monitorozási és log Analytics-megoldásokban is beszerezhetők.

- **Hozzon létre egy egyéni telemetria-és naplózási platformot**. Már rendelkezik egy egyéni kialakítású telemetria-platformmal, vagy felépíti az egyiket? A Event Hubs rugalmasan méretezhető közzétételi-előfizetési természete lehetővé teszi a diagnosztikai naplók rugalmas betöltését. Tekintse [meg a Dan Rosanova útmutatóját, hogy a Event Hubst egy globális méretű telemetria platformon használja](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Stream a Storage-ba

SQL Database metrikákat és diagnosztikai naplókat az Azure Storage-ban tárolhatja, ha a beépített **archívumot** használja a Storage-fiók lehetőségre a Azure Portal. A tárolót a PowerShell-parancsmagok, az Azure CLI vagy a Azure Monitor REST API használatával is engedélyezheti diagnosztikai beállításokkal.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Metrikák és diagnosztikai naplók sémája a Storage-fiókban

A metrikák és a diagnosztikai naplók gyűjteményének beállítása után a rendszer létrehoz egy tárolót a kiválasztott Storage-fiókban, amikor az adatok első sora elérhető. A Blobok szerkezete:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Vagy egyszerűen:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Az alapszintű metrikák blobjának neve például a következő lehet:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

A rugalmas készlet adatainak tárolására szolgáló blob neve a következőképpen néz ki:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Adatmegőrzési szabályzat és díjszabás

Ha Event Hubs vagy egy Storage-fiókot választ, megadhat egy adatmegőrzési szabályt. Ez a szabályzat törli a megadott időszaknál régebbi adatértékeket. Ha Log Analytics ad meg, a megőrzési szabály a kiválasztott díjszabási szinttől függ. Ebben az esetben a megadott ingyenes adategységek lehetővé teszik, hogy havonta több adatbázist is ingyenesen lehessen figyelni. A diagnosztikai telemetria az ingyenes egységeken felüli felhasználásának költsége is felmerülhet. Vegye figyelembe, hogy a súlyosabb számítási feladatokkal rendelkező aktív adatbázisok több adatot töltenek be, mint az üresjárati adatbázisok. További információ: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Ha Azure SQL Analytics használ, a megoldásban a **OMS munkaterületet** a Azure SQL Analytics navigációs menüjében, majd a **használat** és a **becsült költségek**lehetőség kiválasztásával figyelheti.

## <a name="metrics-and-logs-available"></a>Elérhető metrikák és naplók

A Azure SQL Database, a rugalmas készletek és a felügyelt példányok számára elérhető figyelési telemetria az alábbiakban dokumentáljuk. Az SQL Analytics szolgáltatásban összegyűjtött figyelési telemetria a saját egyéni elemzéséhez és az alkalmazások fejlesztéséhez [Azure monitor log lekérdezési](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nyelvet használva.

## <a name="basic-metrics"></a>Alapszintű mérőszámok

Az alapszintű mérőszámok erőforrás alapján történő részletes ismertetését az alábbi táblázatokban találja.

> [!NOTE]
> Az alapszintű mérőszámok beállítás korábbi nevén az összes mérőszám. A módosítás csak a névadásra történt, és nem történt változás a figyelt metrikák esetében. Ezt a változást kezdeményezték a további metrikai kategóriák jövőbeli bevezetésének engedélyezéséhez.

### <a name="basic-metrics-for-elastic-pools"></a>Rugalmas készletek alapszintű mérőszámai

|**Erőforrás**|**Metrikák**|
|---|---|
|Rugalmas készlet|eDTU százalék, felhasznált eDTU, eDTU korlát, CPU-százalék, fizikai adatolvasási százalék, napló írási aránya, munkamenetek százalékos aránya, munkavégzők százalékos aránya, tárolás, tárolási arány, tárolási korlát, XTP tárolási százalék |

### <a name="basic-metrics-for-azure-sql-databases"></a>Alapszintű mérőszámok az Azure SQL Database-adatbázisokhoz

|**Erőforrás**|**Metrikák**|
|---|---|
|Azure SQL-adatbázis|DTU százalék, felhasznált DTU, DTU korlát, CPU-százalék, fizikai adatolvasási százalék, napló írási aránya, sikeres/sikertelen/letiltott a tűzfal kapcsolatai, a munkamenetek aránya, a dolgozók százalékos aránya, tárolás, tárolási százalék, XTP tárolási százalék és holtpontok |

## <a name="basic-logs"></a>Alapszintű naplók

Az összes naplóhoz elérhető telemetria részletei az alábbi táblázatokban vannak dokumentálva. A [támogatott diagnosztikai naplózással](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) megtudhatja, hogy mely naplók támogatottak egy adott adatbázis-íz esetében – az Azure SQL Single, a készletezett vagy a instance Database.

### <a name="resource-usage-stats-for-managed-instance"></a>A felügyelt példány erőforrás-használati statisztikái

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: ResourceUsageStats |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: MANAGEDINSTANCES |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|A felügyelt példány neve |
|ResourceId|Erőforrás URI-ja |
|SKU_s|Felügyelt példány termék SKU |
|virtual_core_count_s|Rendelkezésre álló virtuális mag száma |
|avg_cpu_percent_s|Átlagos CPU-százalék |
|reserved_storage_mb_s|Lefoglalt tárolási kapacitás a felügyelt példányon |
|storage_space_used_mb_s|Használt tároló a felügyelt példányon |
|io_requests_s|IOPS száma |
|io_bytes_read_s|IOPS bájtok olvasása |
|io_bytes_written_s|IOPS bájtok írása |

### <a name="query-store-runtime-statistics"></a>Lekérdezési tár futásidejű statisztikái

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: QueryStoreRuntimeStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreRuntimeStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|query_hash_s|Lekérdezés kivonata |
|query_plan_hash_s|Lekérdezési terv kivonata |
|statement_sql_handle_s|Utasítás SQL-leírója |
|interval_start_time_d|A 1900-1-1-es órajelek számának intervallumának megkezdése DateTimeOffset |
|interval_end_time_d|A 1900-1-1-es órajelek számának záró DateTimeOffset |
|logical_io_writes_d|Logikai IO-írások száma összesen |
|max_logical_io_writes_d|Logikai i/o-írási műveletek maximális száma végrehajtás szerint |
|physical_io_reads_d|Fizikai IO-olvasások száma összesen |
|max_physical_io_reads_d|Logikai i/o-olvasások maximális száma végrehajtáskor |
|logical_io_reads_d|Logikai IO-olvasások száma összesen |
|max_logical_io_reads_d|Logikai i/o-olvasások maximális száma végrehajtáskor |
|execution_type_d|Végrehajtás típusa |
|count_executions_d|A lekérdezés végrehajtásának száma |
|cpu_time_d|A lekérdezés által a másodpercenként felhasznált CPU-idő összesen |
|max_cpu_time_d|A CPU-idő maximális kihasználtsága a másodpercenkénti egyszeri végrehajtással |
|dop_d|Párhuzamossági fok összege |
|max_dop_d|Az egyszeri végrehajtáshoz használt maximális párhuzamossági fok |
|rowcount_d|Visszaadott sorok száma összesen |
|max_rowcount_d|Egyetlen végrehajtásban visszaadott sorok maximális száma |
|query_max_used_memory_d|A KB-ban használt memória teljes mennyisége |
|max_query_max_used_memory_d|Maximális mennyiségű memória, amelyet egyetlen végrehajtás használ a KB-ban |
|duration_d|Összes végrehajtási idő a másodpercenként |
|max_duration_d|Egyetlen végrehajtás maximális végrehajtási ideje |
|num_physical_io_reads_d|Fizikai olvasások száma összesen |
|max_num_physical_io_reads_d|Fizikai olvasások maximális száma végrehajtáskor |
|log_bytes_used_d|Az összes felhasznált naplózási bájt mennyisége |
|max_log_bytes_used_d|A másodpercenként felhasznált naplózási bájtok maximális mennyisége |
|query_id_d|A lekérdezés azonosítója a lekérdezési tárban |
|plan_id_d|A csomag azonosítója a lekérdezési tárolóban |

További információ a [lekérdezési tár futásidejű statisztikáinak adatairól](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Lekérdezési tároló várakozási statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: QueryStoreWaitStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|wait_category_s|A várakozás kategóriája |
|is_parameterizable_s|A lekérdezés parametrizálható |
|statement_type_s|Az utasítás típusa |
|statement_key_hash_s|Utasítás kulcsának kivonata |
|exec_type_d|Végrehajtás típusa |
|total_query_wait_time_ms_d|Az adott várakozási kategória lekérdezésének teljes várakozási ideje |
|max_query_wait_time_ms_d|A lekérdezés maximális várakozási ideje az egyes végrehajtásokban az adott várakozási kategóriában |
|query_param_type_d|0 |
|query_hash_s|Lekérdezési kivonat a lekérdezési tárban |
|query_plan_hash_s|Lekérdezési terv kivonata a lekérdezési tárban |
|statement_sql_handle_s|Utasítás leírója a lekérdezési tárolóban |
|interval_start_time_d|A 1900-1-1-es órajelek számának intervallumának megkezdése DateTimeOffset |
|interval_end_time_d|A 1900-1-1-es órajelek számának záró DateTimeOffset |
|count_executions_d|A lekérdezés végrehajtásának száma |
|query_id_d|A lekérdezés azonosítója a lekérdezési tárban |
|plan_id_d|A csomag azonosítója a lekérdezési tárolóban |

További információ a [lekérdezési tár várakozási statisztikáinak adatairól](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Hibák adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: hibák |
|OperationName|A művelet neve. Mindig: ErrorEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|Üzenet|Egyszerű szöveges üzenet hibaüzenete |
|user_defined_b|A felhasználó által definiált bit |
|error_number_d|Hibakód |
|Severity|A hiba súlyossága |
|state_d|A hiba állapota |
|query_hash_s|Sikertelen lekérdezés kivonata, ha elérhető |
|query_plan_hash_s|A sikertelen lekérdezéshez tartozó lekérdezési terv kivonata, ha elérhető |

További információ a [SQL Server hibaüzenetekről](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors?view=sql-server-ver15).

### <a name="database-wait-statistics-dataset"></a>Adatbázis várakozási statisztikájának adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: DatabaseWaitStatistics |
|OperationName|A művelet neve. Mindig: DatabaseWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|wait_type_s|A várakozási típus neve |
|start_utc_date_t [UTC]|Mért időszak kezdő időpontja |
|end_utc_date_t [UTC]|Mért időszak befejezési időpontja |
|delta_max_wait_time_ms_d|Maximális várakozási idő/végrehajtás |
|delta_signal_wait_time_ms_d|Összes jel várakozási ideje |
|delta_wait_time_ms_d|Teljes várakozási idő az időszakban |
|delta_waiting_tasks_count_d|Várakozási feladatok száma |

További információ az [adatbázis-várakozási statisztikákról](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Időtúllépési adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: időtúllépések |
|OperationName|A művelet neve. Mindig: TimeoutEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|error_state_d|Hiba állapotának kódja |
|query_hash_s|Lekérdezési kivonat, ha elérhető |
|query_plan_hash_s|Lekérdezési terv kivonata, ha elérhető |

### <a name="blockings-dataset"></a>Blokkolási adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: blokkok |
|OperationName|A művelet neve. Mindig: BlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|lock_mode_s|A lekérdezés által használt zárolási mód |
|resource_owner_type_s|A zárolás tulajdonosa |
|blocked_process_filtered_s|Letiltott folyamat jelentésének XML-fájlja |
|duration_d|A zárolás időtartama (mp) |

### <a name="deadlocks-dataset"></a>Holtpontok adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC] |A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: holtpontok |
|OperationName|A művelet neve. Mindig: DeadlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|deadlock_xml_s|Holtpont-jelentés XML-fájlja |

### <a name="automatic-tuning-dataset"></a>Adatkészlet automatikus finomhangolása

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: AutomaticTuning |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|LogicalDatabaseName_s|Az adatbázis neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|RecommendationHash_s|Automatikus hangolási javaslat egyedi kivonata |
|OptionName_s|Automatikus hangolási művelet |
|Schema_s|Adatbázis-séma |
|Table_s|Érintett tábla |
|IndexName_s|Index neve |
|IndexColumns_s|Oszlop neve |
|IncludedColumns_s|Tartalmazott oszlopok |
|EstimatedImpact_s|Az Automatikus hangolási javaslatok JSON-ra gyakorolt becsült hatása |
|Event_s|Az Automatikus hangolási esemény típusa |
|Timestamp_t|Utolsó frissítés időbélyege |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights adatkészlet

További információ a [Intelligent Insights napló formátumáról](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan engedélyezheti a naplózást, és megismerheti a különböző Azure-szolgáltatások által támogatott mérőszámokat és naplózási kategóriákat, tekintse meg a következőt:

- [A Microsoft Azure metrikáinak áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Az Azure Diagnostics-naplók áttekintése](../azure-monitor/platform/resource-logs-overview.md)

A Event Hubsről a következő témakörben olvashat bővebben:

- [Mi az Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Bevezetés az Event Hubs használatába](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Ha szeretné megtudni, hogyan állíthat be riasztásokat a log Analytics telemetria alapján, tekintse meg a következőt:

- [Riasztások létrehozása SQL Database és felügyelt példányhoz](../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)
