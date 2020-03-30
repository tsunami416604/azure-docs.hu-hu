---
title: Az Azure site recovery figyelése az Azure Figyelő naplóival
description: Ismerje meg, hogyan figyelheti az Azure Site Recovery szolgáltatást az Azure Figyelőnaplók (Log Analytics) segítségével
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133417"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>A Site Recovery monitorozása az Azure Monitor naplóival

Ez a cikk azt ismerteti, hogy miként figyelheti az Azure [Site Recovery](site-recovery-overview.md)által replikált gépeket az Azure [Monitor naplók](../azure-monitor/platform/data-platform-logs.md)és a Log [Analytics](../azure-monitor/log-query/log-query-overview.md)használatával.

Az Azure Monitor Naplók egy naplóadat-platformot biztosít, amely összegyűjti a tevékenység- és diagnosztikai naplókat, valamint más figyelési adatokat. Az Azure Monitor naplók, a Log Analytics segítségével naplólekérdezések írása és tesztelése, valamint a naplóadatok interaktív elemzéséhez. Megjelenítheti és lekérdezheti a napló eredményeit, és beállíthatja a riasztásokat a figyelt adatokon alapuló műveletek hez.

A Site Recovery esetében az Azure Monitor naplók segítségével az alábbi műveleteket teheti:

- **A hely helyreállításának állapotának és állapotának figyelése**. Figyelheti például a replikáció állapotát, a feladatátvétel állapotát, a Hely-helyreállítási eseményeket, a védett gépek helyreállításipont-célkitűzéseit (RPM) és a lemez-/adatváltozási sebességet.
- **Riasztások beállítása a Site Recovery rendszerhez.** Beállíthatja például a gép állapotára, a teszt feladatátvételi állapotára vagy a Site Recovery feladat állapotára vonatkozó riasztásokat.

Az Azure Monitor naplók használata a Site Recovery támogatja az **Azure-azure-replikáció,** és **a VMware VM/fizikai kiszolgáló az Azure replikáció.**

> [!NOTE]
> A lemorzsolódási adatnaplók és a Virtuálisgép-szoftverek és a fizikai gépek feltöltési sebességnaplóinak lekérnie kell egy Microsoft figyelési ügynököt a process serveren. Ez az ügynök elküldi a replikálógépek naplóit a munkaterületre. Ez a funkció csak 9.30-as mobilitási ügynökverziótól kezdve érhető el.

## <a name="before-you-start"></a>Előkészületek

A következőkre lesz szüksége:

- Legalább egy számítógép védett a Recovery Services tárolójában.
- A Log Analytics munkaterület a Site Recovery naplók tárolására. További információ a munkaterület [beállításáról.](../azure-monitor/learn/quick-create-workspace.md)
- A Log Analytics naplólekérdezéseinek írása, futtatása és elemzése alapvető ismerete. [További információ](../azure-monitor/log-query/get-started-portal.md).

Javasoljuk, hogy a kezdés előtt tekintse át a [gyakori figyelési kérdéseket.](monitoring-common-questions.md)

## <a name="configure-site-recovery-to-send-logs"></a>A Site Recovery konfigurálása naplók küldésére

1. A tárolóban kattintson a **Diagnosztikai beállítások** > **Diagnosztikai beállítás hozzáadása parancsra.**

    ![Diagnosztikai naplózás kiválasztása](./media/monitoring-log-analytics/add-diagnostic.png)

2. A **Diagnosztikai beállítások párbeszédpanelen**adja meg a nevet, és jelölje be a **Küldés a Log Analytics szolgáltatásba**jelölőnégyzetet.
3. Válassza ki az Azure Monitor Naplók előfizetést és a Log Analytics munkaterületet.
4. Válassza ki az **Azure Diagnosztika** a kapcsolóban.
5. A naplólistából válassza ki az **AzureSiteRecovery**előtaggal rendelkező összes naplót. Ezt követően kattintson az **OK** gombra.

    ![Munkaterület kiválasztása](./media/monitoring-log-analytics/select-workspace.png)

A site recovery naplók elkezd betáplálni egy táblába (**AzureDiagnostics**) a kiválasztott munkaterületen.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>A Microsoft felügyeleti ügynök konfigurálása a process serveren lemorzsolódási és feltöltési sebességnaplók küldésére

Rögzítheti az adatforgalom-adatokat és a forrásadatok feltöltési arányára vonatkozó információkat a VMware/fizikai gépekhez a helyszínen. Ennek engedélyezéséhez a Folyamatkiszolgálóra telepíteni kell egy Microsoft figyelési ügynököt.

1. Nyissa meg a Log Analytics munkaterületet, és kattintson a **Speciális beállítások gombra.**
2. Kattintson a **Csatlakoztatott források lapra,** és válassza ki a **Windows-kiszolgálók lehetőséget.**
3. Töltse le a Windows Agentet (64 bites) a folyamatkiszolgálóra. 
4. [A munkaterület-azonosító és a kulcs beszerzése](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Ügynök konfigurálása a TLS 1.2 használatára](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. A beszerzett munkaterület-azonosító és kulcs megadásával [végezze el az ügynök telepítését.](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)
7. A telepítés befejezése után nyissa meg a Log Analytics munkaterületet, és kattintson a **Speciális beállítások gombra.** Nyissa meg az **Adatok** lapot, és kattintson a **Windows teljesítményszámlálói ra.** 
8. Kattintson a **'+'** gombra a következő két számláló hozzáadásához 300 másodperces mintavételi időközzel:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

A lemorzsolódási és feltöltési sebesség adatai megkezdik a munkaterületre való betáplálást.


## <a name="query-the-logs---examples"></a>A naplók lekérdezése – példák

A naplókból a [Kusto lekérdezési nyelvvel](../azure-monitor/log-query/get-started-queries.md)írt naplólekérdezésekkel lehet adatokat beolvasni. Ez a szakasz néhány példát mutat be a site recovery figyeléséhez használható gyakori lekérdezésekre.

> [!NOTE]
> Néhány példa az **A2A** **replicationProviderName_s.** Ez lekéri az Azure virtuális gépek, amelyek replikálódnak egy másodlagos Azure-régióban a Site Recovery használatával. Ezekben a példákban lecserélheti **az A2A-t** **az InMageAzureV2-re,** ha a helyszíni VMware virtuális gépeket vagy az Azure-ba a Site Recovery használatával replikált fizikai kiszolgálókat szeretne beolvasni.


### <a name="query-replication-health"></a>Replikáció állapotának lekérdezése

Ez a lekérdezés egy kördiagramot hoz az összes védett Azure-beli virtuális gép aktuális replikációs állapotához, három állapotra bontva: Normál, Figyelmeztetés vagy Kritikus.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Lekérdezési mobilitási szolgáltatás verziója

Ez a lekérdezés a Site Recovery szolgáltatással replikált Azure-beli virtuális gépek kördiagramját keresi, a futó mobilitási ügynök verziója szerinti bontásban.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>LekérdezésI RPO idő

Ez a lekérdezés a Site Recovery szolgáltatással replikált Azure-beli virtuális gépek sávdiagramját ábrázolja helyreállításipont (RPO) szerinti bontásban: Kevesebb, mint 15 perc, 15–30 perc, több mint 30 perc.

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

![Lekérdezés RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Hely-helyreállítási feladatok lekérdezése

Ez a lekérdezés lekéri az összes Site Recovery feladatok (az összes vész-helyreállítási forgatókönyvek), az elmúlt 72 órában, és azok befejezésének állapotát.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Lekérdezési hely helyreállítási eseményei

Ez a lekérdezés lekéri az összes site recovery események (az összes vész-helyreállítási forgatókönyvek) az elmúlt 72 órában, azok súlyosságával együtt. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Lekérdezési teszt feladatátvételi állapota (kördiagram)

Ez a lekérdezés egy kördiagramot hoz a Site Recovery-vel replikált Azure-beli virtuális gépek tesztfeladat-átvételi állapotához.

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

Ez a lekérdezés egy táblát keres a Site Recovery-vel replikált Azure-beli virtuális gépek tesztfeladat-átvételi állapotához.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Lekérdezési gép RPO

Ez a lekérdezés egy trenddiagramot hoz, amely egy adott Azure-beli virtuális gép (ContosoVM123) RPO-ját követi nyomon az elmúlt 72 órában.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Lekérdezési gép RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Lekérdezési adatok változási sebessége (lemorzsolódás) és feltöltési arány egy Azure virtuális gép

Ez a lekérdezés egy adott Azure-gép (ContosoVM123) trenddiagramját ábrázolja, amely az adatváltozási sebességet (Másodpercenként írása) és az adatfeltöltési arányt jelöli. 

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
![Lekérdezési adatok módosítása](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Lekérdezési adatok változási sebessége (lemorzsolódás) és feltöltési arány egy VMware vagy fizikai gép

> [!Note]
> Győződjön meg arról, hogy a folyamatkiszolgálón beállította a figyelési ügynököt a naplók lekéréséhez. A [figyelési ügynök konfigurálásához](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)olvassa el a lépéseket.

Ez a lekérdezés egy **win-9r7sfh9qlru**replikált elem egy adott **lemezlemezének** trenddiagramját ábrázolja, amely az adatváltozási sebességet (Másodpercenként írása) és az adatfeltöltési arányt jelöli. A lemez nevét a **replikált** elem lemezpaneljén található a helyreállítási szolgáltatások tárolójában. A lekérdezésben használandó példánynév a számítógép DNS-neve, amelyet a _ és a lemezneve követ, mint ebben a példában.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
A Process Server 5 percenként leküldéses az adatokat a Log Analytics munkaterületre. Ezek az adatpontok az 5 percig számított átlagot jelölik.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Vész-helyreállítási összefoglaló lekérdezése (Azure az Azure-ba)

Ez a lekérdezés egy másodlagos Azure-régióba replikált Azure-beli virtuális gépek összefoglaló tábláját ábrázolja.  Ez azt mutatja, virtuális gép nevét, replikációs és védelmi állapotát, RPO, teszt feladatátvételi állapot, mobilitási ügynök verziója, az aktív replikációs hibák, és a forrás helyét.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Vész-helyreállítási összefoglaló lekérdezése (VMware/fizikai kiszolgálók)

Ez a lekérdezés az Azure-ba replikált VMware virtuális gépek és fizikai kiszolgálók összefoglaló tábláját ábrázolja.  Megmutatja a számítógép nevét, a replikációt és a védelmi állapotot, az RPO-t, a teszt feladatátvételi állapotát, a mobilitási ügynök verzióját, az aktív replikációs hibákat és a megfelelő folyamatkiszolgálót.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Riasztások beállítása – példák

Site Recovery riasztások az Azure Monitor adatai alapján állíthatja be. [További információ](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) a naplóriasztások beállításáról. 

> [!NOTE]
> Néhány példa az **A2A** **replicationProviderName_s.** Ez riasztásokat állít be az Azure-beli virtuális gépekhez, amelyek egy másodlagos Azure-régióba replikálódnak. Ezekben a példákban lecserélheti **az A2A-t** **az InMageAzureV2-re,** ha riasztásokat szeretne beállítani a helyszíni VMware virtuális gépekhez vagy az Azure-ba replikált fizikai kiszolgálókhoz.

### <a name="multiple-machines-in-a-critical-state"></a>Több gép kritikus állapotban

Állítson be egy riasztást, ha több mint 20 replikált Azure-beli virtuális gép kritikus állapotba kerül.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
A riasztáshoz állítsa **a Küszöbérték értéket** 20-ra.

### <a name="single-machine-in-a-critical-state"></a>Egyetlen gép kritikus állapotban

Állítson be egy riasztást, ha egy adott replikált Azure-virtuális gép kritikus állapotba kerül.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
A riasztáshoz állítsa **a Küszöbérték értéket** 1-re.

### <a name="multiple-machines-exceed-rpo"></a>Több gép meghaladja az RPO-t

Állítson be egy riasztást, ha az RPO több mint 20 Azure-beli virtuális gép meghaladja a 30 percet.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
A riasztáshoz állítsa **a Küszöbérték értéket** 20-ra.

### <a name="single-machine-exceeds-rpo"></a>Egyetlen gép meghaladja az RPO-t

Állítson be egy riasztást, ha az RPO egyetlen Azure-beli virtuális gép 30 percet meghaladó.

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
A riasztáshoz állítsa **a Küszöbérték értéket** 1-re.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Több gép feladatátvételének tesztelése meghaladja a 90 napot

Állítson be egy riasztást, ha az utolsó sikeres teszt feladatátvétel több mint 90 nap volt, több mint 20 virtuális gép. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
A riasztáshoz állítsa **a Küszöbérték értéket** 20-ra.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Az egygépes feladatátvétel tesztelése meghaladja a 90 napot

Állítson be egy riasztást, ha egy adott virtuális gép utolsó sikeres sikeres sikeres feladatátvétele több mint 90 nappal ezelőtt volt.
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
A riasztáshoz állítsa **a Küszöbérték értéket** 1-re.

### <a name="site-recovery-job-fails"></a>A hely-helyreállítási feladat sikertelen

Állítson be egy riasztást, ha egy hely-helyreállítási feladat (ebben az esetben a visszavédelmi feladat) sikertelen minden hely-helyreállítási forgatókönyv esetén, az utolsó nap során. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

A riasztás, állítsa **küszöbérték értéke** 1, **és időszak** 1440 perc, az utolsó nap hibáinak ellenőrzéséhez.

## <a name="next-steps"></a>További lépések

További információ a beépített webhely-helyreállítási [figyelésről.](site-recovery-monitor-and-troubleshoot.md)
