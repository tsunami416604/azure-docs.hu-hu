---
title: Az Azure SQL Database felügyelt példányába való online áttelepítéssel kapcsolatos ismert problémák és korlátozások
description: Ismerje meg az Azure SQL Database által felügyelt példányba való online áttelepítéssel kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648665"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Ismert problémák/áttelepítési korlátozások az Azure SQL Database felügyelt példányába való online áttelepítéssel kapcsolatban

Az SQL Serverről az Azure SQL Database által felügyelt példányba történő online áttelepítéssel kapcsolatos ismert problémák és korlátozások az alábbiakban olvashatók.

> [!IMPORTANT]
> Az SQL Server online áttelepítése az Azure SQL Database-be, SQL_variant adattípusok áttelepítése nem támogatott.

## <a name="backup-requirements"></a>Biztonsági mentési követelmények

- **Biztonsági mentések ellenőrzőösszeggel**

    Az Azure Database Migration Service a biztonsági mentési és visszaállítási módszert használja a helyszíni adatbázisok SQL Database felügyelt példányba való áttelepítéséhez. Az Azure Database Migration Service csak az ellenőrzőösszeg használatával létrehozott biztonsági másolatokat támogatja.

    [Biztonsági mentési ellenőrzőösszegek engedélyezése vagy letiltása biztonsági mentés vagy visszaállítás (SQL Server) során](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Ha az adatbázis biztonsági mentéseit tömörítéssel készíti, az ellenőrzőösszeg az alapértelmezett viselkedés, kivéve, ha kifejezetten le van tiltva.

    Az offline áttelepítések esetén, ha az **I will Let Azure Database Migration Service...** lehetőséget választja, akkor az Azure Database Migration Service az ellenőrzőösszeg beállítás sal együtt biztonsági másolatot készít az adatbázisról.

- **Biztonsági másolat adathordozója**

    Győződjön meg arról, hogy minden biztonsági mentést külön biztonsági másolaton (biztonságimásolat-fájlokon) készít. Az Azure Database Migration Service nem támogatja a biztonsági mentések, amelyek hozzáfűzik egyetlen biztonsági másolat fájlba. A biztonsági másolat készítése és a biztonsági mentés készítése külön biztonsági másolatkészítésre szolgál.

## <a name="data-and-log-file-layout"></a>Adat- és naplófájl-elrendezés

- **Naplófájlok száma**

    Az Azure Database Migration Service nem támogatja a több naplófájllal rendelkező adatbázisokat. Ha több naplófájlja van, zsugorítsa és rendezze át őket egyetlen tranzakciónaplófájlba. Mivel nem lehet távolról naplózni a nem üres fájlokat, először biztonsági másolatot kell tartania a naplófájlról.

## <a name="sql-server-features"></a>Az SQL Server szolgáltatásai

- **FileStream/FileTables**

    Az SQL Database által felügyelt példány jelenleg nem támogatja a FileStream és a FileTables fájlokat. A funkcióktól függő számítási feladatok esetében azt javasoljuk, hogy az Azure-beli virtuális gépeken futó SQL-kiszolgálókat válassza Azure-célként.

- **Memóriabe helyezett táblák**

    A memórián belüli OLTP az SQL Database felügyelt példányának prémium és üzleti legkritikusabb rétegeiben érhető el; az általános célú szint nem támogatja a memóriában oltp.

## <a name="migration-resets"></a>Az áttelepítés visszaállítása

- **Üzemelő példányok**

    Az SQL Database által felügyelt példány egy PaaS-szolgáltatás, amely automatikus javítást és verziófrissítéseket tartalmaz. Az SQL Database felügyelt példányának áttelepítése során a nem kritikus frissítések akár 36 órát is segíthetnek. Ezt követően (és a kritikus frissítések), ha az áttelepítés megszakad, a folyamat visszaáll a teljes visszaállítási állapotba.

    Az áttelepítés immára való átállás csak a teljes biztonsági mentés visszaállítása után hívható meg, és utolérheti az összes naplóbiztonsági mentést. Ha az éles áttelepítési átállásokat érinti, lépjen kapcsolatba az [Azure DMS Visszajelzés aliasával.](mailto:dmsfeedback@microsoft.com)
