---
title: Állítsa vissza az Azure SQL Data Warehouse - REST API-val |} A Microsoft Docs
description: Állítsa vissza az Azure SQL Data Warehouse – REST API-k használatával.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: daf013472e5fa533912920e4c14a552905b5d333
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241518"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Állítsa vissza az Azure SQL Data Warehouse a REST API-kkal
Állítsa vissza az Azure SQL Data Warehouse – REST API-k használatával.

## <a name="before-you-begin"></a>Előkészületek
**A dtu-k kapacitásának ellenőrzése.** Egy logikai SQL-kiszolgáló (például a myserver.database.windows.net) amely alapértelmezés szerint minden SQL Data Warehouse üzemeltetett [DTU-kvóta](../sql-database/sql-database-what-is-a-dtu.md).  SQL Data Warehouse a visszaállításhoz, ellenőrizze, hogy az az SQL Servernek elég fennmaradó DTU-kvótába a visszaállított adatbázis számára. Kérjen további DTU, is [hozzon létre egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Az aktív vagy szüneteltetett data warehouse visszaállítása
Adattárház visszaállítása:

1. Az adatbázis-visszaállítási pontok Get művelettel adatbázis-visszaállítási pontok listájának beolvasása.
2. A visszaállítás megkezdéséhez használatával a [adatbázis visszaállítási kérés létrehozásakor](https://msdn.microsoft.com/library/azure/dn509571.aspx) műveletet.
3. Használatával a visszaállítás állapotának nyomon követése a [adatbázis-művelet állapotának](https://msdn.microsoft.com/library/azure/dn720371.aspx) műveletet.

> [!NOTE]
> A visszaállítás befejeztével a visszaállított adatraktár konfigurálhatja az alábbi [konfigurálása az adatbázis helyreállítása után](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Egy törölt adatraktár visszaállítási
Egy törölt adatraktár visszaállítása:

1. Használatával a visszaállítható törölt adattárházak felsorolását a [lista visszaállítható adatbázisok eldobott](https://msdn.microsoft.com/library/azure/dn509562.aspx) műveletet.
2. Részletes információkkal szolgál a [Get visszaállítható adatbázis] [Get visszaállítható adatbázis] műveletet a visszaállítani kívánt törölt adatraktár.
3. A visszaállítás megkezdéséhez használatával a [adatbázis visszaállítási kérés létrehozásakor](https://msdn.microsoft.com/library/azure/dn509571.aspx) műveletet.
4. Használatával a visszaállítás állapotának nyomon követése a [adatbázis-művelet állapotának](https://msdn.microsoft.com/library/azure/dn720371.aspx) műveletet.

> [!NOTE]
> Az adatraktár beállítása után a visszaállítás befejeződött: [konfigurálása az adatbázis helyreállítása után](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>További lépések
Az üzletmenet-folytonossági funkciókat az Azure SQL Database-kiadás kapcsolatos további információkért olvassa el a [Azure SQL Database üzletmenet folytonossága – áttekintés](../sql-database/sql-database-business-continuity.md).
