---
title: "Állítsa vissza az Azure adatraktár - a helyi és georedundáns |} Microsoft Docs"
description: "Az Azure SQL Data Warehouse adatbázis helyreállítása adatbázis visszaállítási lehetőségek áttekintése."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="sql-data-warehouse-restore"></a>Az SQL Data Warehouse visszaállítása
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Az SQL Data Warehouse a helyi és földrajzi visszaállítások részeként a data warehouse vész helyreállítási lehetőségeket nyújt. Az adatok adatraktár biztonsági használatával állítsa vissza az adatraktár egy visszaállítási pontot az elsődleges régióban, vagy georedundáns biztonsági mentések használatával állítsa vissza egy másik földrajzi régióban. Ez a cikk ismerteti a mintaadatokról egy adatraktár a visszaállításakor.

## <a name="what-is-a-data-warehouse-restore"></a>Mi az a data warehouse visszaállítás?
A data warehouse visszaállítás egy új adatraktárat egy meglévő biztonsági másolatából létrehozott vagy törölt adatraktár. A visszaállított adatok adatraktár újra létrehozza a biztonsági másolat adatraktár adott időpontban. Mivel az SQL Data Warehouse egy elosztott rendszert, a data warehouse visszaállítás számos biztonsági mentés fájljaiból, az Azure-blobokat tárolt jön létre. 

Adatbázis visszaállítása nem minden üzleti folytonossági és vészhelyreállítási helyreállítási stratégia nagyon fontos részét képezik, mert az adatok véletlen sérülés vagy törlése után újból létrehozza.

További információkért lásd:

* [Az SQL Data Warehouse biztonsági mentések](sql-data-warehouse-backups.md)
* [Üzleti folytonosság – áttekintés](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Data warehouse visszaállítási pontok
Prémium szintű Azure Storage használatának előnye, mint az SQL Data Warehouse használatával Azure Storage-Blobba pillanatképek biztonsági mentését az elsődleges adatraktár. Minden pillanatképet egy visszaállítási pontot a pillanatkép elindításakor jelölő rendelkezik. Adatraktár visszaállításához válasszon visszaállítási pontot, és adja ki a restore parancsot.  

Az SQL Data Warehouse mindig visszaállítja a biztonsági mentés új adatraktárat. A visszaállított adatok adatraktár és az aktuális hagyja, vagy törölje az egyik legyen. Ha szeretné a jelenlegi adatraktárban cserélje le a visszaállított adatok adatraktár, átnevezheti azt.

Ha a visszaállítandó törölt vagy szüneteltetett adatraktár, akkor [támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Georedundáns helyreállítás
Visszaállíthatja az adatraktár minden régióban támogató Azure SQL Data Warehouse, a kiválasztott teljesítményszint szükséges. Vegye figyelembe, hogy 9000 és 18000 DWU nem támogatottak minden régióban az előzetes.

> [!NOTE]
> Georedundáns visszaállításhoz kell nem visszavonta igényét ezt a szolgáltatást.
> 
> 

## <a name="restore-timeline"></a>Az idősor visszaállítása
Visszaállíthatja egy adatbázis bármely elérhető visszaállítási pont az utóbbi hét napban. A pillanatképek négy és nyolc óránként start és hét napja. Ha pillanatkép régebbi, mint hét nap, jár le, és a helyreállítási pont már nem érhető el.

## <a name="restore-costs"></a>Állítsa vissza a költségek
A tárolási költség a visszaállított adatraktár számlázása a prémium szintű Azure Storage ütemben történik. 

A visszaállított adatok adatraktár szünetelteti, ha van szó, a prémium szintű Azure Storage díj tárolására. Felfüggesztés előnye nem kell fizetnie a DWU számítási erőforrások.

További információ az SQL Data Warehouse díjszabása: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>A visszaállítási használ
Az elsődleges a data warehouse visszaállítási használata adatok véletlen adatvesztést vagy -sérülés utáni helyreállításhoz.

Data warehouse visszaállítási használatával több mint hét napja a biztonsági másolatok megőrzése. A biztonsági mentés helyreáll, amennyiben az adatraktár online rendelkezik, és akár szüneteltetheti is, hogy határozatlan ideig számítási költségek csökkentése érdekében. A felfüggesztett adatbázis terhel tárolási a prémium szintű Azure Storage díj. 

## <a name="related-topics"></a>Kapcsolódó témakörök
### <a name="scenarios"></a>Forgatókönyvek
* Üzleti folytonosság áttekintéséért lásd: [üzleti folytonosság – áttekintés](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

A data warehouse visszaállításhoz, visszaállítás segítségével:

* Az Azure portál, lásd: [visszaállítása egy adatraktár, az Azure portál használatával](sql-data-warehouse-restore-database-portal.md)
* PowerShell-parancsmagok [visszaállítani az PowerShell-parancsmagok használatával adatraktár](sql-data-warehouse-restore-database-powershell.md)
* REST-API-k, lásd: [a REST API-k használatával adatraktár visszaállítása](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
