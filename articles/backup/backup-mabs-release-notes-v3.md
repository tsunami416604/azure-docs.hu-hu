---
title: A Microsoft Azure Backup Server v3 kibocsátási megjegyzései
description: Ez a cikk az MABS v3 ismert problémáit és megkerülő megoldásait ismerteti.
ms.reviewer: v-jysur
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: dacurwin
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: a80a5ac64b58d93bb0d4e4b799cb7424805c9baa
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698374"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup-kiszolgáló kibocsátási megjegyzései
Ez a cikk az Microsoft Azure Backup Server (MABS) v3 ismert problémáit és megkerülő megoldásait ismerteti.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>Fürtözött munkaterhelések esetén a biztonsági mentés és a helyreállítás meghiúsul

**Leírás:** A Backup/Restore művelet sikertelen a fürtözött adatforrások, például a Hyper-V-fürt vagy az SQL-fürt (SQL always on) vagy az adatbázis rendelkezésre állási csoportjában (DAG) az MABS v2 MABS v3 verzióra való frissítése után.

**Megkerülő megoldás:** Ennek elkerüléséhez nyissa meg SQL Server Management Studio (SSMS)), és futtassa a következő SQL-parancsfájlt a DPM-ADATBÁZISon:


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


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Az MABS v3-re való frissítés meghiúsul az orosz területi beállításban

**Leírás:** A MABS v2 verzióról a MABS v3-re való frissítés az orosz területi beállításban meghiúsul, hibakód: **4387**.

**Megkerülő megoldás:** Az alábbi lépéseket követve frissítsen az MABS v3-re az orosz telepítési csomag használatával:

1.  [Készítsen biztonsági másolatot](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) az SQL-adatbázisról, és távolítsa el a MABS v2-et (válassza a védett adat megőrzését az eltávolítás során).
2.  Frissítsen az SQL 2017 (Enterprise) verzióra, és távolítsa el a jelentéskészítést a frissítés részeként.
3. [Telepítés](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) SQL Server Reporting Services (SSRS).
4.  [Telepítés](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms#download-ssms-182) SQL Server Management Studio (SSMS).
5.  Konfigurálja a jelentéskészítést a paraméterekkel az [SSRS-konfigurációban az SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)használatával dokumentált módon.
6.  [Telepítés](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Visszaállítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL a SSMS használatával és a DPM-Sync eszköz futtatása az [itt](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))leírtak szerint.
8.  Frissítse a "DataBaseVersion" tulajdonságot a dbo. tbl _DLS_GlobalSetting táblában a következő parancs használatával:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Indítsa el a MSDPM szolgáltatást.


## <a name="next-steps"></a>További lépések

[Az MABS v3 újdonságai](backup-mabs-whats-new-mabs.md)
