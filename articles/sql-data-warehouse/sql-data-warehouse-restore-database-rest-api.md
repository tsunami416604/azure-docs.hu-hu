---
title: Állítsa vissza az Azure SQL Data Warehouse - REST API-t |} Microsoft Docs
description: Állítsa vissza az Azure SQL Data Warehouse REST API-k használatával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Állítsa vissza az Azure SQL Data Warehouse REST API-k
Állítsa vissza az Azure SQL Data Warehouse REST API-k használatával.

## <a name="before-you-begin"></a>Előkészületek
**A DTU-kapacitásának ellenőrzése.** Minden egyes SQL Data Warehouse üzemelteti a logikai SQL server (pl. myserver.database.windows.net) amely tartalmaz egy alapértelmezett [DTU-kvótáról](../sql-database/sql-database-what-is-a-dtu.md).  SQL Data Warehouse visszaállítása előtt ellenőrizze, hogy az az SQL server elég fennmaradó DTU-kvótáról az adatbázis visszaállítása folyamatban van. Több DTU kérelmezéséhez is [támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Az aktív vagy szüneteltetett adatraktár visszaállítása
Adatraktár visszaállításához:

1. Az adatbázis-visszaállítási pontok Get művelettel adatbázis visszaállítási pontok listájának.
2. A visszaállítás megkezdéséhez használatával a [létrehozása adatbázis visszaállítási kérelem](https://msdn.microsoft.com/library/azure/dn509571.aspx) műveletet.
3. A visszaállítási állapotának nyomon követése használatával a [adatbázis-művelet állapota](https://msdn.microsoft.com/library/azure/dn720371.aspx) műveletet.

> [!NOTE]
> A visszaállítás befejezése után a helyreállított adatraktár követve konfigurálhatja [konfigurálása az adatbázis helyreállítása után](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>A törölt adatraktár visszaállítása
A törölt adatraktár visszaállításához:

1. Listázza az azon a visszaállítható törölt adatraktárak összes használatával a [lista visszaállítható adatbázisok eldobott](https://msdn.microsoft.com/library/azure/dn509562.aspx) műveletet.
2. Ismerje meg a részleteket az a [Get visszaállítható eldobott adatbázis] [Get visszaállítható eldobott adatbázis] művelettel visszaállítani kívánt törölt adatraktár.
3. A visszaállítás megkezdéséhez használatával a [létrehozása adatbázis visszaállítási kérelem](https://msdn.microsoft.com/library/azure/dn509571.aspx) műveletet.
4. A visszaállítási állapotának nyomon követése használatával a [adatbázis-művelet állapota](https://msdn.microsoft.com/library/azure/dn720371.aspx) műveletet.

> [!NOTE]
> Az adatraktár beállítása után a visszaállítás befejeződött: [konfigurálása az adatbázis helyreállítása után](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>További lépések
Az üzleti folytonosságot biztosító szolgáltatásokat az Azure SQL Database kiadásainak kapcsolatos információkért olvassa el a [Azure SQL Database üzleti folytonosság – áttekintés](../sql-database/sql-database-business-continuity.md).
