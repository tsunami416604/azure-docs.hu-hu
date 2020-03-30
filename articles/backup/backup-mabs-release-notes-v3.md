---
title: Kibocsátási megjegyzések a Microsoft Azure Backup Server v3-hoz
description: Ez a cikk a Microsoft Azure Backup Server (MABS) 3-as verzióval kapcsolatos ismert problémáival és kerülő megoldásokkal kapcsolatos információkat tartalmazza.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172280"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Kibocsátási megjegyzések a Microsoft Azure biztonsági másolat készítő kiszolgálóhoz

Ez a cikk a Microsoft Azure Backup Server (MABS) V3 ismert problémáit és kerülő megoldásait ismerteti.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>A biztonsági mentés és a helyreállítás sikertelen a fürtözött munkaterhelések esetén

**Leírás:** A biztonsági mentés/visszaállítás sikertelen a fürtözött adatforrások, például a Hyper-V-fürt vagy az SQL-fürt (SQL Always On) vagy az Exchange esetében az adatbázis rendelkezésre állási csoportjában (DAG) a MABS V2 MABS V3-ra való frissítése után.

**A megoldás:** Ennek elkerülése érdekében nyissa meg az SQL Server Management Studio (SSMS)) rendszert, és futtassa a következő SQL-parancsfájlt a DPM DB-n:

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Frissítés MABS V3 nem sikerül az orosz locale

**Leírás:** Az orosz területi beállításban a MABS V2-ről a MABS V3-ra történő frissítés **4387-es**hibakóddal sikertelen.

**A megoldás:** Az alábbi lépésekkel frissíthet a MABS V3-ra az orosz telepítőcsomag használatával:

1. [Készítsen biztonsági másolatot](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) az SQL-adatbázisról, és távolítsa el a MABS V2-t (válassza a védett adatok eltávolítását az eltávolítás során).
2. Frissítsen az SQL 2017 (Enterprise) rendszerre, és távolítsa el a jelentéseket a frissítés részeként.
3. [Telepítés](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4. [Telepítés](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) SQL Server Management Studio (SSMS).
5. A Jelentéskészítés konfigurálása az [SSRS-konfigurációban az SQL 2017-tel](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)dokumentált paraméterek használatával.
6. [Telepítés](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Visszaállítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL segítségével SSMS és fuss DPM-Sync eszköz [az itt](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))leírtak szerint .
8. Frissítse a "DataBaseVersion" tulajdonságot a dbo.tbl_DLS_GlobalSetting táblában a következő paranccsal:

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. Indítsa el az MSDPM szolgáltatást.

## <a name="next-steps"></a>További lépések

[A MABS V3 újdonságai](backup-mabs-whats-new-mabs.md)
