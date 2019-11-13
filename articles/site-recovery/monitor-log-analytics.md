---
title: Azure Site Recovery figyelése Azure Monitor naplókkal
description: Megtudhatja, hogyan figyelheti a Azure Site Recoveryt Azure Monitor-naplókkal (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b5bf568e03d4949b8798dd2e0f4c2d8cbcbbe0c7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73936094"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>A Site Recovery monitorozása az Azure Monitor naplóival

Ez a cikk bemutatja, hogyan figyelheti az Azure [site Recovery](site-recovery-overview.md)által replikált gépeket [Azure Monitor naplók](../azure-monitor/platform/data-platform-logs.md)és [log Analytics](../azure-monitor/log-query/log-query-overview.md)használatával.

Azure Monitor naplók olyan naplózási adatplatformot biztosítanak, amely tevékenység-és diagnosztikai naplókat, valamint más figyelési adatokat gyűjt. Azure Monitor naplókon belül a rendszer Log Analytics használatával naplózza és teszteli a napló lekérdezéseit, és interaktív módon elemezheti a naplózási adatforrásokat. Megjelenítheti és lekérdezheti a napló eredményeit, és konfigurálhatja a riasztásokat, hogy műveleteket hajtson végre a figyelt adatmennyiség alapján.

Site Recovery a naplók segítségével Azure Monitorheti a következő műveleteket:

- **A site Recovery állapotának és állapotának figyelése**. Megfigyelheti például a replikálás állapotát, a feladatátvételi teszt állapotát, a Site Recovery eseményeket, a helyreállítási pontok célkitűzéseit (RPO) a védett gépekhez, valamint a lemez/adatváltozások arányát.
- **Riasztások beállítása site Recoveryhoz**. Beállíthat például riasztásokat a gép állapotára, a feladatátvételi teszt állapotára vagy Site Recovery a feladatok állapotára.

Az **Azure-** ban az Azure-ba való replikáláshoz és a **VMWare virtuális gép/fizikai kiszolgáló Azure** -ba történő replikálásához az Azure monitor-naplók használata támogatott site Recovery használatával.

> [!NOTE]
> Az adatforgalmi naplók és a feltöltési arány naplói csak másodlagos Azure-régióba replikált Azure-beli virtuális gépek esetében érhetők el.

## <a name="before-you-start"></a>Előkészületek

A következőkre lesz szüksége:

- Legalább egy gép védve van egy Recovery Services-tárolóban.
- Log Analytics munkaterület Site Recovery naplók tárolására. [További](../azure-monitor/learn/quick-create-workspace.md) információ a munkaterület beállításáról.
- Alapvető ismeretek a naplók írásához, futtatásához és elemzéséhez Log Analyticsban. [Részletek](../azure-monitor/log-query/get-started-portal.md).

Javasoljuk, hogy a Kezdés előtt tekintse át az [általános monitorozási kérdéseket](monitoring-common-questions.md) .

## <a name="configure-site-recovery-to-send-logs"></a>Site Recovery konfigurálása naplók küldéséhez

1. A tárolóban kattintson a **diagnosztikai beállítások** > **diagnosztikai beállítás hozzáadása**elemre.

    ![Diagnosztikai naplózás kiválasztása](./media/monitoring-log-analytics/add-diagnostic.png)

2. A **diagnosztikai beállítások**területen adjon meg egy nevet, és jelölje be a **Küldés log Analyticsre**jelölőnégyzetet.
3. Válassza ki a Azure Monitor naplók előfizetését és a Log Analytics munkaterületet.
4. Válassza a **Azure Diagnostics** lehetőséget a váltásban.
5. A naplók listából válassza ki az összes naplót a **AzureSiteRecovery**előtaggal. Ezután kattintson az **OK** gombra.

    ![Munkaterület kiválasztása](./media/monitoring-log-analytics/select-workspace.png)

A Site Recovery naplók megkezdik a hírcsatornát a kiválasztott munkaterületen lévő táblába (**AzureDiagnostics**).


## <a name="query-the-logs---examples"></a>A naplók lekérdezése – példák

Az adatok naplókból való lekéréséhez a [Kusto lekérdezési nyelvvel](../azure-monitor/log-query/get-started-queries.md)írt napló lekérdezéseket kell használnia. Ez a szakasz néhány példát mutat be Site Recovery figyeléshez használható gyakori lekérdezésekre.

> [!NOTE]
> Néhány példa a **replicationProviderName_s** beállítása **A2A**értékre. Ezzel lekéri az Azure-beli virtuális gépeket, amelyek egy másodlagos Azure-régióba replikálódnak Site Recovery használatával. Ezekben a példákban lecserélheti a **A2A** -t a **InMageAzureV2**-be, ha az Azure-ba replikált helyszíni VMWare virtuális gépeket vagy fizikai kiszolgálókat szeretné lekérni site Recovery használatával.


### <a name="query-replication-health"></a>Replikáció állapotának lekérdezése

Ez a lekérdezés egy tortadiagramot ábrázol az összes védett Azure-beli virtuális gép aktuális replikációs állapotára vonatkozóan, három állapotra bontva: normál, figyelmeztetés vagy kritikus.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>A mobilitási szolgáltatás verziójának lekérése

Ez a lekérdezés egy tortadiagramot ábrázol a Site Recoveryval replikált Azure-beli virtuális gépekhez, a futó mobilitási ügynök verziószáma szerint lebontva.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Lekérdezési RPO ideje

Ez a lekérdezés egy Site Recovery által replikált Azure-beli virtuális gépek oszlopdiagram diagramját ábrázolja, a helyreállítási időkorlát (RPO) szerinti bontásban: kevesebb, mint 15 perc, 15-30 perc és 30 perc között.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![RPO lekérdezése](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Site Recovery feladatok lekérdezése

Ez a lekérdezés lekérdezi az összes Site Recovery feladatot (az összes vész-helyreállítási forgatókönyv esetében), amely az elmúlt 72 órában aktiválódik, valamint a befejezési állapotukat.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Site Recovery események lekérdezése

A lekérdezés lekérdezi az elmúlt 72 órában kiváltott összes Site Recovery eseményt (az összes vész-helyreállítási forgatókönyv esetében), valamint azok súlyosságát. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Lekérdezési teszt feladatátvételi állapota (tortadiagram)

Ez a lekérdezés egy tortadiagramot ábrázol a Site Recovery-vel replikált Azure-beli virtuális gépek feladatátvételi állapotának teszteléséhez.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Lekérdezési teszt feladatátvételi állapota (tábla)

Ez a lekérdezés a Site Recovery-val replikált Azure-beli virtuális gépek feladatátvételi állapotának tesztelésére szolgáló táblázatot ábrázolja.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Számítógép RPO lekérdezése

Ez a lekérdezés egy trend Graphot ábrázol, amely nyomon követi egy adott Azure-beli virtuális gép (ContosoVM123) RPO az elmúlt 72 órában.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Számítógép RPO lekérdezése](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-for-a-vm"></a>Virtuális gép adatváltozási arányának lekérdezése (forgalom)

> [!NOTE] 
> Az adatforgalomra vonatkozó információk csak másodlagos Azure-régióba replikált Azure-beli virtuális gépek esetében érhetők el.

Ez a lekérdezés egy adott Azure-beli virtuális gép (ContosoVM123) számára ábrázol egy trend Graphot, amely nyomon követi az adatváltozási sebességet (írási bájtok másodpercenként) és az adatfeltöltési sebességet. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Adatváltozások lekérdezése](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Vész-helyreállítási összefoglalás lekérdezése (Azure-ról Azure-ra)

Ez a lekérdezés a másodlagos Azure-régióba replikált Azure-beli virtuális gépek összegző táblázatát ábrázolja.  Megjeleníti a virtuális gépek nevét, replikálási és védelmi állapotát, a RPO, a feladatátvételi teszt állapotát, a mobilitási ügynök verzióját, az esetleges aktív replikációs hibákat és a forrás helyét.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Vész-helyreállítási összefoglalás lekérdezése (VMware/fizikai kiszolgálók)

Ez a lekérdezés az Azure-ba replikált VMware virtuális gépek és fizikai kiszolgálók összegző táblázatát ábrázolja.  Megjeleníti a gép nevét, a replikálási és a védelmi állapotot, a RPO, a feladatátvételi teszt állapotát, a mobilitási ügynök verzióját, az összes aktív replikálási hibát és a megfelelő Process Servert.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Riasztások beállítása – példák

Site Recovery riasztásokat Azure Monitor-adatértékek alapján is beállíthat. [További](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) információ a naplózási riasztások beállításáról. 

> [!NOTE]
> Néhány példa a **replicationProviderName_s** beállítása **A2A**értékre. Ez riasztásokat állít be a másodlagos Azure-régióba replikált Azure-beli virtuális gépekről. Ezekben a példákban lecserélheti a **A2A** -t a **InMageAzureV2** -be, ha riasztásokat szeretne beállítani a helyszíni VMWare virtuális gépekhez vagy az Azure-ba replikált fizikai kiszolgálókhoz.

### <a name="multiple-machines-in-a-critical-state"></a>Több gép kritikus állapotban

Riasztást állíthat be, ha több mint 20 replikált Azure-beli virtuális gép kritikus állapotba kerül.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
A riasztáshoz állítsa 20 értékre a **küszöbértéket** .

### <a name="single-machine-in-a-critical-state"></a>Egy gép kritikus állapotban van

Riasztást állíthat be, ha egy adott replikált Azure-beli virtuális gép kritikus állapotba kerül.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
A riasztáshoz állítsa a **küszöbérték értékét** 1-re.

### <a name="multiple-machines-exceed-rpo"></a>Több gép meghaladja a RPO

Riasztást állíthat be, ha több mint 20 Azure-beli virtuális gép RPO meghaladja a 30 percet.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
A riasztáshoz állítsa 20 értékre a **küszöbértéket** .

### <a name="single-machine-exceeds-rpo"></a>Egy gép meghaladja a RPO

Riasztás, ha egyetlen Azure-beli virtuális gép RPO hossza meghaladja a 30 percet.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
A riasztáshoz állítsa a **küszöbérték értékét** 1-re.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Több gép feladatátvételi tesztje meghaladja a 90 napot

Riasztást állíthat be, ha az utolsó sikeres feladatátvételi teszt több mint 90 nap volt, több mint 20 virtuális gép esetén. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
A riasztáshoz állítsa 20 értékre a **küszöbértéket** .

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Feladatátvételi teszt egyetlen gépen meghaladja a 90 napot

Riasztást állíthat be, ha egy adott virtuális gép utolsó sikeres feladatátvételi tesztje több mint 90 nappal ezelőtt volt.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
A riasztáshoz állítsa a **küszöbérték értékét** 1-re.

### <a name="site-recovery-job-fails"></a>Site Recovery a feladatok sikertelenek

Riasztást állíthat be, ha egy Site Recovery-feladathoz (ebben az esetben az újravédelemi feladathoz) az elmúlt nap során Site Recovery forgatókönyvek meghiúsulnak. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

A riasztáshoz állítsa 1 **értékre a küszöbértéket** , az utolsó **nap során pedig** az 1440 percet.

## <a name="next-steps"></a>Következő lépések

[További információ a](site-recovery-monitor-and-troubleshoot.md) beépített site Recovery figyelésről.
