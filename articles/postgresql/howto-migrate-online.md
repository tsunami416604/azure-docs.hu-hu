---
title: Az Azure Database-áttelepítés minimális állásidővel PostgreSQL
description: Ez a cikk ismerteti a minimális állásidő áttelepítéshez PostgreSQL-adatbázisból csomagolja ki a biztonsági másolat fájlt, a PostgreSQL-adatbázis visszaállítása az Azure Database pg_dump hozta létre a PostgreSQL archív fájlból, és a kezdeti betöltés beállítását és a céladatbázis Attunity megfelel a Microsoft Migrations a forrásadatbázis szinkronizálási adatok folyamatos.
services: postgresql
author: HJToland3
ms.author: jtoland
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 48cf460405ae3985553f9bff29f4fd7abb008196
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Az Azure Database-áttelepítés minimális állásidővel PostgreSQL
Áttelepítheti a meglévő PostgreSQL-adatbázisban az Azure Database-PostgreSQL az Attunity megfelel a Microsoft Migrations. Attunity megfelel egy közös ajánlat Attunity és a Microsoft. Azure-adatbázis áttelepítése szolgáltatás megfelelően szerepel a Microsoft ügyfelei további költségek nélkül. 

Attunity megfelel segít, hogy minimalizálják az állásidőt adatbázis áttelepítések során, és a folyamat során működési tartja a forráshely adatbázisára.

Attunity megfelel egy replikációs eszköz, amely lehetővé teszi a különböző forrásokból és tárolók közötti adatszinkronizálás. Azt a séma-létrehozási parancsprogrammal és az adatbázis kapcsolódó adatok propagálása zajlik. Attunity replikálás nem propagál minden egyéb összetevők (például SP, eseményindítók, Funkciók, és így tovább) vagy convert, például, a PL/SQL-kódot, amely az ilyen összetevők T-SQL.

> [!NOTE]
> Bár Attunity replikálás áttelepítési forgatókönyvek széles körét támogatja, egy adott részének forrás célgazdagép, célfelhő párokból támogatása összpontosít.

A folyamat minimális állásidővel áttelepítés áttekintését tartalmazza:

* **A PostgreSQL-forrás séma áttelepítése** egy Azure Database segítségével PostgreSQL-adatbázishoz a [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) - n paraméterrel parancsot, és használata a [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) parancs.

* **Kezdeti betöltés és az adatok folyamatos szinkronizálásához a forrás-adatbázisból történő beállítása a céladatbázis** Attunity megfelel a Microsoft Migrations. Ezzel minimálisra csökkenti a forrásadatbázis be kell állítania csak olvashatóként Azure váltani a cél PostgreSQL-adatbázisból az alkalmazások előkészítése az idejét.

A Attunity megfelel az ajánlat Microsoft Migrations kapcsolatos további információkért lásd a következőket:
 - Lépjen a [Attunity megfelel a Microsoft Migrations](https://aka.ms/attunity-replicate) weblap.
 - Töltse le [Attunity megfelel a Microsoft áttelepítések](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Lépjen a [Attunity replikálása közösségi](https://aka.ms/attunity-community/) első lépések útmutatója, oktatóanyagok és támogatási szolgálathoz.
 - Az Attunity megfelel használatával történő PostgreSQL az Azure-adatbázis PostgreSQL áttelepítésének lépésenkénti útmutatásért tekintse meg a [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).