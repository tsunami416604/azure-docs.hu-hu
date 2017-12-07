---
title: "Állítsa vissza az Azure SQL Data Warehouse (REST API-t) |} Microsoft Docs"
description: "Azure SQL Data Warehouse visszaállítására feladatok REST API-t."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 3117bf6aa21641c30f33f38f096ded24bb0cf64e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Állítsa vissza az Azure SQL Data Warehouse (REST API-t)
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Ebben a cikkben megtudhatja visszaállítása egy Azure SQL Data Warehouse a REST API használatával.

## <a name="before-you-begin"></a>Előkészületek
**A DTU-kapacitásának ellenőrzése.** Minden egyes SQL Data Warehouse egy SQL server (pl. myserver.database.windows.net), amely rendelkezik egy alapértelmezett DTU-kvótáról üzemelteti.  SQL Data Warehouse visszaállítása előtt ellenőrizze, hogy az az SQL server elég fennmaradó DTU-kvótáról az adatbázis visszaállítása folyamatban van. DTU-igény kiszámításához, vagy kérjen további DTU, lásd: [DTU-kvótát Módosítás kérése][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Az aktív vagy szüneteltetett adatbázis visszaállítása
Egy adatbázis visszaállításához:

1. Az adatbázis-visszaállítási pontok Get művelettel adatbázis visszaállítási pontok listájának.
2. A visszaállítás megkezdéséhez használatával a [létrehozása adatbázis visszaállítási kérelem] [ Create database restore request] műveletet.
3. A visszaállítási állapotának nyomon követése használatával a [adatbázis-művelet állapota] [ Database operation status] műveletet.

> [!NOTE]
> Miután a visszaállítás befejeződött, a helyreállított adatbázis követve konfigurálhatja [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
A törölt adatbázisok visszaállítása:

1. Listázza az összes, a törölt, visszaállítható adatbázisok használatával a [lista visszaállítható adatbázisok eldobott] [ List restorable dropped databases] műveletet.
2. Ezzel az adatokat a visszaállítani kívánt törölt adatbázis a [visszaállítható Get adatbázis eldobása] [ Get restorable dropped database] műveletet.
3. A visszaállítás megkezdéséhez használatával a [létrehozása adatbázis visszaállítási kérelem] [ Create database restore request] műveletet.
4. A visszaállítási állapotának nyomon követése használatával a [adatbázis-művelet állapota] [ Database operation status] műveletet.

> [!NOTE]
> A visszaállítás befejezése után az adatbázis konfigurálásához lásd: [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="next-steps"></a>Következő lépések
Az üzleti folytonosságot biztosító szolgáltatásokat az Azure SQL Database kiadásainak kapcsolatos információkért olvassa el a [Azure SQL Database üzleti folytonosság – áttekintés][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
