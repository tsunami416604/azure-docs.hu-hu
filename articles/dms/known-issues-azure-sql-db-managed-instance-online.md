---
title: Az Azure SQL felügyelt példányain az online áttelepítéssel kapcsolatos ismert problémák és korlátozások
description: Ismerje meg az Azure SQL felügyelt példányának online áttelepítésével kapcsolatos ismert problémákat/áttelepítési korlátozásokat.
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
ms.openlocfilehash: 871ea665d23a5fba644448ddb6c596179d47bb3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85106407"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Ismert problémák/áttelepítési korlátozások az Azure SQL felügyelt példányaihoz való online áttelepítéssel

Az SQL Serverról az Azure SQL felügyelt példányra való online áttelepítéssel kapcsolatos ismert problémák és korlátozások alább olvashatók.

> [!IMPORTANT]
> A SQL Server online áttelepítésével Azure SQL Database a SQL_variant adattípusok áttelepítése nem támogatott.

## <a name="backup-requirements"></a>Biztonsági mentési követelmények

- **Biztonsági mentések ellenőrzőösszeggel**

    A Azure Database Migration Service a biztonsági mentés és visszaállítás módszert használja a helyszíni adatbázisok SQL felügyelt példányra történő átirányításához. Azure Database Migration Service csak az ellenőrzőösszeg használatával létrehozott biztonsági mentéseket támogatja.

    [Biztonsági másolatok ellenőrzőösszegének engedélyezése vagy letiltása a biztonsági mentés vagy a visszaállítás során (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Ha az adatbázis biztonsági másolatait tömörítéssel végzi el, az ellenőrzőösszeg alapértelmezett viselkedés, kivéve, ha explicit módon le van tiltva.

    Ha az offline áttelepítést választja, akkor a **Azure Database Migration Service.**. Azure Database Migration Service. lehetőséget választva a rendszer engedélyezi az adatbázis biztonsági mentését az ellenőrzőösszeg beállítással.

- **Adathordozó biztonsági mentése**

    Ügyeljen arra, hogy minden biztonsági mentést készítsen egy különálló biztonsági mentési adathordozón (biztonságimásolat-fájl). A Azure Database Migration Service nem támogatja az egyetlen biztonságimásolat-fájlhoz fűzött biztonsági mentéseket. Készítsen teljes biztonsági mentést, és készítsen biztonsági másolatot a biztonságimásolat-fájlokról.

## <a name="data-and-log-file-layout"></a>Az adatfájlok és a naplófájlok elrendezése

- **Naplófájlok száma**

    Azure Database Migration Service nem támogatja több naplófájlt tartalmazó adatbázisok használatát. Ha több naplófájlja is van, akkor egyetlen tranzakciós naplófájlba kell bontania és átrendeznie őket. Mivel nem lehet távolról olyan naplófájlokat létrehozni, amelyek nem üresek, először a naplófájlt kell biztonsági másolatot készíteni.

## <a name="sql-server-features"></a>SQL Server funkciók

- **FileStream/objektumnak**

    A felügyelt SQL-példány jelenleg nem támogatja a FileStream és a objektumnak. A funkcióktól függő munkaterhelések esetében javasoljuk, hogy az Azure-beli virtuális gépeken futó SQL-kiszolgálókat Azure-célként válassza.

- **Memóriában tárolt táblák**

    A memóriában tárolt OLTP az SQL által felügyelt példány prémium és üzletileg kritikus szintjein érhető el; a általános célúi réteg nem támogatja a memóriabeli OLTP.

## <a name="migration-resets"></a>Áttelepítési alaphelyzetbe állítás

- **Központi telepítés**

    Az SQL felügyelt példánya egy olyan Pásti szolgáltatás, amely automatikus javítással és verzióval frissül. Az SQL felügyelt példányának áttelepítése során a nem kritikus frissítések akár 36 óráig is tarthatók. Ezt követően (és a kritikus frissítések esetében), ha az áttelepítés megszakad, a folyamat visszaállítja a teljes visszaállítási állapotot.

    Az áttelepítési átváltás csak a teljes biztonsági mentés visszaállítása után hívhatók meg, és a rendszer az összes naplózott biztonsági mentést felhasználja. Ha az éles környezetbe történő áttelepítés átállásos érintett, forduljon az [Azure DMS visszajelzési aliasához](mailto:dmsfeedback@microsoft.com).
