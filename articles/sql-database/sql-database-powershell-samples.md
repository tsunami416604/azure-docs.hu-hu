---
title: "Az Azure PowerShell-parancsfájl példák az SQL Database |} Microsoft Docs"
description: "Az Azure PowerShell-példák, hogyan hozhat létre és kezelheti az Azure SQL Database-kiszolgálók, a rugalmas készletek, az adatbázisok és a tűzfal parancsfájlt."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: overview-samples
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: On Demand
ms.date: 06/23/2017
ms.author: janeng
ms.openlocfilehash: 1d1692cc43a7a5ec50c0689706d93a784a5fed88
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Az Azure SQL Database az Azure PowerShell-példák

A következő táblázat az Azure SQL Database az Azure PowerShell-mintaparancsfájlok hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Hozzon létre egy önálló adatbázis és a rugalmas készlethez**||
| [Hozzon létre egy adatbázist, és a tűzfalszabályok konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell-parancsfájlt hoz létre egy Azure SQL-adatbázis, és konfigurálja egy kiszolgálószintű tűzfalszabályt. |
| [A rugalmas készlet létrehozása és készletezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell-parancsfájlt hoz létre az Azure SQL Database rugalmas készletek, és készletezett adatbázisok helyezi át, és teljesítményszintek vált.|
|**Georeplikálási és a feladatátvétel konfigurálása**||
| [Konfigurálja és feladatátvételi egy önálló adatbázis aktív georeplikációt használ](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell-parancsfájl egy Azure SQL-adatbázis aktív georeplikáció konfigurálja, és átadja a másodlagos replikára. |
| [Konfigurálja és feladatátvételi egy készletezett adatbázis aktív georeplikációt használ](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl konfigurálja az Azure SQL-adatbázis aktív georeplikáció a rugalmas SQL-készletet, és átadja a másodlagos replikára. |
| [Konfigurálja és a feladatátvételi feladatátvételi csoport számára egy adatbázist (előzetes verzió)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell parancsfájl konfigurálása az Azure SQL Database server-példány egy feladatátvételi csoport, egy adatbázis hozzáadja a feladatátvételi csoport, és átadja a másodlagos kiszolgáló |
|**Egy önálló adatbázisok és rugalmas készletek méretezése**||
| [Egy önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A metrikák egy Azure SQL-adatbázis, a PowerShell parancsfájl figyelők méretezi a magasabb teljesítményszintre, és egy riasztási szabályt hoz létre a Teljesítményelemzési értékek egyike. |
| [Egy rugalmas készlet méretezése](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell parancsfájl figyelők a metrikák egy Azure SQL Database rugalmas készlet méretezi a magasabb teljesítményszintre, és egy riasztási szabályt hoz létre a Teljesítményelemzési értékek egyike.  |
| **Naplózás és a fenyegetések észlelése** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl naplózás és a fenyegetések észlelése házirendek az Azure SQL-adatbázis konfigurálása. |
| **Állítsa vissza, másolása és adatbázis importálása**||
| [Adatbázis helyreállítása](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl Azure SQL-adatbázis visszaállítása egy georedundáns biztonsági másolatból, és visszaállítja az törölt Azure SQL-adatbázis a legutóbbi biztonsági mentése. |
| [Új kiszolgáló adatbázis másolása](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell-parancsfájl egy létező Azure SQL-adatbázis másolatát egy új Azure SQL Server hoz létre. |
| [Adatbázis bacpac-fájlból való importálása](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell-parancsfájl az Azure SQL Server adatbázis importál egy bacpac-fájlból. |
| **Adatbázisok között szinkronizálja az adatokat**||
| [SQL-adatbázisok között szinkronizálja az adatokat](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell parancsfájl adatszinkronizálás több Azure SQL-adatbázisok közötti szinkronizálására konfigurálja. |
| [SQL Database és SQL Server helyszíni között szinkronizálja az adatokat](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | A PowerShell parancsfájl adatszinkronizálás Azure SQL-adatbázis és a helyszíni SQL Server-adatbázisok közötti szinkronizálására konfigurálja. |
|||
|||
