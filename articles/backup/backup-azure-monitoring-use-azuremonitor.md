---
title: Az Azure Biztonsági mentés figyelése az Azure Monitorsegítségével
description: Figyelheti az Azure Backup számítási feladatait, és egyéni riasztásokat hozhat létre az Azure Monitor használatával.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459514"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitor nagy méretekben az Azure Monitor használatával

Az Azure Backup [beépített figyelési és riasztási lehetőségeket](backup-azure-monitoring-built-in-monitor.md) biztosít a Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. Ez a beépített szolgáltatás azonban a következő esetekben korlátozott:

- Ha több Helyreállítási szolgáltatások-tárolóból származó adatokat figyel az előfizetések között
- Ha az előnyben részesített értesítési csatorna *nem* e-mail
- Ha a felhasználók további esetekre vonatkozó annektre vallanak
- If you want to view information from an on-premises component such as System Center Data Protection Manager in Azure, which the portal doesn't show in [**Backup Jobs**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) or [**Backup Alerts**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>A Log Analytics munkaterületének használata

### <a name="create-alerts-by-using-log-analytics"></a>Riasztások létrehozása a Log Analytics használatával

Az Azure Monitorban létrehozhatja saját riasztásait egy Log Analytics-munkaterületen. A munkaterületen *azure-műveletcsoportok* használatával válassza ki a kívánt értesítési mechanizmust.

> [!IMPORTANT]
> A lekérdezés létrehozásának költségeiről az [Azure Monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/)című témakörben talál további információt.

Nyissa **meg** a Log Analytics munkaterület Naplók szakaszát, és hozzon létre egy lekérdezést a saját naplókhoz. Amikor az Új riasztási szabály lehetőséget **választja,** megnyílik az Azure Monitor riasztási létrehozási lapja, ahogy az az alábbi képen látható.

![Riasztás létrehozása a Log Analytics-munkaterületen](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Itt az erőforrás már meg van jelölve a Log Analytics munkaterületként, és a műveletcsoport-integráció biztosított.

![A Log Analytics riasztás-létrehozási lap](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Riasztási feltétel

A riasztás meghatározó jellemzője a kiváltó állapota. Válassza **a Feltétel** lehetőséget, ha a Kusto **lekérdezést** automatikusan be szeretné tölteni a Naplók lapon az alábbi képen látható módon. Itt szerkesztheti az állapotot, hogy megfeleljen az Ön igényeinek. További információ: [Minta Kusto lekérdezések](#sample-kusto-queries).

![Riasztási feltétel beállítása](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Szükség esetén szerkesztheti a Kusto lekérdezést. Válasszon egy küszöbértéket, időszakot és gyakoriságot. A küszöbérték határozza meg, hogy mikor lesz a riasztás. Az időszak az az időszak, amelyben a lekérdezés fut. Ha például a küszöbérték 0-nál nagyobb, az időszak 5 perc, a gyakoriság pedig 5 perc, akkor a szabály 5 percenként futtatja a lekérdezést, és az előző 5 percet tekinti át. Ha az eredmények száma nagyobb, mint 0, a kijelölt műveletcsoporton keresztül értesítést kap.

#### <a name="alert-action-groups"></a>Riasztási műveletcsoportok

Egy műveletcsoport segítségével adjon meg egy értesítési csatornát. A rendelkezésre álló értesítési mechanizmusok megtekintéséhez a **Műveletcsoportok csoportban**válassza **az Új létrehozása lehetőséget.**

![Elérhető értesítési mechanizmusok a "Műveletcsoport hozzáadása" ablakban](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

A Log Analytics minden riasztási és figyelési követelményének megfelelhet, vagy a Log Analytics használatával kiegészítheti a beépített értesítéseket.

További információ: [Naplóriasztások létrehozása, megtekintése és kezelése](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) az Azure Monitor használatával, valamint [műveletcsoportok létrehozása és kezelése az Azure Portalon.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

### <a name="sample-kusto-queries"></a>Minta Kusto-lekérdezések

Az alapértelmezett grafikonok kusto lekérdezéseket biztosítanak az alapvető forgatókönyvekhez, amelyeken riasztásokat hozhat létre. A lekérdezések módosításával is lehetővé teheti a figyelmeztetést. Illessze be a következő kusto-lekérdezéseket a **Naplók** lapra, majd hozzon létre riasztásokat a lekérdezéseken:

- Minden sikeres biztonsági mentési feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Az összes sikertelen biztonsági mentési feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Az összes sikeres Azure Virtuálisgép-biztonsági mentési feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Az összes sikeres SQL-naplóbiztonsági mentési feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Az összes sikeres Azure Backup ügynök-feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Biztonságimásolat-készítési elemenként felhasznált biztonsági mentési tároló

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Diagnosztikai adatok frissítésének gyakorisága

A trezor diagnosztikai adatait a naplóanalytics munkaterületre szivattyúzza némi késéssel. Minden esemény *20–30 perccel* a Recovery Services-tárolóból való leküldése után érkezik meg a Log Analytics-munkaterületre. Itt van további részleteket a lag:

- Az összes megoldás, a biztonsági mentési szolgáltatás beépített riasztások leküldéses, amint azok létrehozása. Így általában 20–30 perc elteltével jelennek meg a Log Analytics-munkaterületen.
- Az összes megoldásban az igény szerinti biztonsági mentési feladatok és a visszaállítási feladatok leküldése azonnal *megtörténik,* amint befejeződnek.
- Az SQL biztonsági mentés kivételével az összes megoldás esetében az ütemezett biztonsági mentési feladatok at lenyomják, amint *befejezték.*
- Az SQL biztonsági mentés, mert a napló biztonsági mentések 15 percenként is előfordulhat, az összes befejezett ütemezett biztonsági mentési feladatok adatait, beleértve a naplókat, kötegelt és leadott 6 óránként.
- Az összes megoldásban az egyéb információk, például a biztonsági mentési elem, a házirend, a helyreállítási pontok, a tárolás és így tovább, naponta legalább *egyszer* leküldéses.
- A biztonsági mentési konfiguráció (például a házirend módosítása vagy a szerkesztési házirend) módosítása az összes kapcsolódó biztonsági mentési információ leküldéses indítását eredményezi.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>A Recovery Services-tároló tevékenységnaplóinak használata

> [!CAUTION]
> A következő lépések csak az *Azure virtuális gépek biztonsági mentései vonatkoznak.* Ezeket a lépéseket nem használhatja olyan megoldásokhoz, mint például az Azure Backup ügynök, az Azure-on belüli SQL-biztonsági mentések vagy az Azure Files.

A tevékenységnaplók segítségével is értesítést kaphat az eseményekről, például a biztonsági mentés sikerességéről. Első lépésként hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg a megfelelő helyreállítási szolgáltatások tároló.
1. A tároló tulajdonságai, nyissa meg a **tevékenységnapló** szakasz.

A megfelelő napló azonosítása és riasztás létrehozása:

1. Ellenőrizze, hogy a sikeres biztonsági mentéshez kap-e tevékenységnaplókat az alábbi képen látható szűrők alkalmazásával. A rekordok megtekintéséhez szükség szerint módosítsa az **Időfesztávolság** értékét.

   ![Szűrés az Azure vm-biztonsági mentések tevékenységnaplóinak megkereséséhez](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Válassza ki a művelet nevét a vonatkozó részletek megtekintéséhez.
1. Válassza **az Új figyelmeztetési szabály** lehetőséget a Szabály létrehozása lap **megnyitásához.**
1. Hozzon létre egy riasztást a [Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)az Azure Monitor használatával című lépésekkel.

   ![Új riasztási szabály](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Itt maga a Recovery Services-tároló található. Ismételje meg ugyanazokat a lépéseket az összes olyan tárolóesetében, amelyben a tevékenységnaplókon keresztül értesítést szeretne kapni. A feltétel nem rendelkezik küszöbértékkel, időszakkal vagy gyakorisággal, mert ez a riasztás eseményeken alapul. Amint a megfelelő tevékenységnapló jön létre, a riasztás jön létre.

## <a name="using-log-analytics-to-monitor-at-scale"></a>A Log Analytics használata nagy méretekben történő figyeléshez

Megtekintheti a tevékenységnaplókból és a Log Analytics-munkaterületekből létrehozott összes riasztást az Azure Monitorban. Csak nyissa meg a **riasztások** ablaktáblát a bal oldalon.

Bár a tevékenységnaplókon keresztül kaphat értesítéseket, javasoljuk, hogy a Log Analytics használatát használja a tevékenységnaplók helyett a nagy méretű figyeléshez. Ennek indokai a következők:

- **Korlátozott forgatókönyvek:** A tevékenységnaplókon keresztüli értesítések csak az Azure virtuális gépek biztonsági mentéseire vonatkoznak. Az értesítéseket minden Recovery Services-tárolóhoz be kell állítani.
- **Definíciós illeszkedés:** Az ütemezett biztonsági mentési tevékenység nem illeszkedik a tevékenységnaplók legújabb definíciójának. Ehelyett az [erőforrásnaplókhoz igazodik.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace) Ez az igazítás nem várt hatásokat okoz, amikor a tevékenységnaplócsatornán áthaladó adatok megváltoznak.
- **A tevékenységnapló-csatornával kapcsolatos problémák:** A Recovery Services-tárolókban az Azure Backupból szivattyúzott tevékenységnaplók egy új modellt követnek. Sajnos ez a változás hatással van a tevékenységnaplók generálására az Azure Governmentben, az Azure Germany-ban és az Azure China 21Vianet-ben. Ha ezek a felhőszolgáltatások felhasználói az Azure Monitor tevékenységnaplóiból származó riasztásokat hoznak létre vagy konfigurálnak, a riasztások nem aktiválódnak. Továbbá az összes Azure nyilvános régióban, ha a felhasználó [gyűjti a Recovery Services tevékenységnaplók at a Log Analytics munkaterület,](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)ezek a naplók nem jelennek meg.

A Log Analytics-munkaterület használatával figyelheti és riasztást készíthet az Azure Backup által védett összes számítási feladathoz.

## <a name="next-steps"></a>További lépések

Egyéni lekérdezések létrehozásáról a [Log Analytics-adatmodell](backup-azure-reports-data-model.md)című témakörben van.
