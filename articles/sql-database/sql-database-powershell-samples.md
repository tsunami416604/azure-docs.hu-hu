---
title: Azure PowerShell-példaszkriptek az SQL Database-hez | Microsoft Docs
description: Az Azure PowerShell-példaszkriptek segítenek az Azure SQL Database-kiszolgálók, rugalmas készletek, adatbázisok és tűzfalak létrehozásában és felügyeletében.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: tysonn
tags: azure-service-management
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 2245964cab3361e0f298cf35d26e8b0a1b8456f0
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Azure PowerShell-minták az Azure SQL Database-hez

A következő táblázat az Azure SQL Database-hez elérhető Azure PowerShell-példaszkriptekre mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Önálló adatbázis és rugalmas készlet létrehozása**||
| [Önálló adatbázis létrehozása és egy tűzfalszabály konfigurálása](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egyetlen Azure SQL-adatbázist hoz létre, és konfigurál egy kiszolgálószintű tűzfalszabályt. |
| [Rugalmas készletek létrehozása és készletezett adatbázisok áthelyezése](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript rugalmas Azure SQL Database-készleteket hoz létre, áthelyezi a készletezett adatbázisokat, és módosítja a teljesítményszinteket.|
|**Georeplikáció és feladatátvétel konfigurálása**||
| [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egyetlen Azure SQL-adatbázishoz, és átadja a feladatait a másodlagos replikának. |
| [Készletezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript aktív georeplikációt állít be egy Azure SQL-adatbázishoz a rugalmas SQL-készletben, és átadja a feladatait a másodlagos replikának. |
| [Önálló adatbázis feladatátvételi csoportjának konfigurálása és feladatainak átvétele (előzetes verzió)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript feladatátvételi csoportot állít be az Azure SQL Database egy kiszolgálópéldányához, hozzáad egy adatbázist a feladatátvételi csoporthoz, és átadja a feladatait a másodlagos kiszolgálónak |
|**Önálló adatbázis és rugalmas készlet méretezése**||
| [Önálló adatbázis méretezése](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb teljesítményi szinthez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon. |
| [Rugalmas készlet méretezése](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript az Azure SQL Database egy rugalmas készletének teljesítmény-mérőszámait monitorozza, méretezi azt egy magasabb teljesítményi szinthez, és létrehoz egy riasztási szabályt az egyik teljesítmény-mérőszámon.  |
| **Naplózás és fenyegetésészlelés** |
| [Naplózás és fenyegetésészlelés konfigurálása](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript naplózási és fenyegetésészlelési szabályzatokat konfigurál egy Azure SQL-adatbázishoz. |
| **Adatbázis visszaállítása, másolása és importálása**||
| [Adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript visszaállít egy Azure SQL-adatbázist egy georedundáns biztonsági másolatból, és visszaállít egy törölt Azure SQL-adatbázist a legutóbbi biztonsági másolatra. |
| [Adatbázis másolása új kiszolgálóra](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript másolatot készít egy meglévő Azure SQL-adatbázisról egy új Azure SQL Serveren. |
| [Adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-szkript egy adatbázist importál egy Azure SQL Serverre egy BACPAC-fájlból. |
| **Adatok szinkronizálása adatbázisok között**||
| [Adatok szinkronizálása SQL-adatbázisok között](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript több Azure SQL-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. |
| [Adatok helyszíni szinkronizálása az SQL Database és az SQL Server között](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript egy Azure SQL-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz konfigurálja a Data Syncet. |
| [Az SQL Data Sync szinkronizálási sémájának frissítése](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ez a PowerShell-szkript adatokat ad hozzá a Data Sync szinkronizálási sémához, vagy eltávolítja belőle azokat. |
|||
