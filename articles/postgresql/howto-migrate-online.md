---
title: "Az Azure Database-áttelepítés minimális állásidővel PostgreSQL |} Microsoft Docs"
description: "Ez a cikk ismerteti a minimális állásidő áttelepítéshez PostgreSQL-adatbázisból csomagolja ki a biztonsági másolat fájlt, a PostgreSQL-adatbázis visszaállítása az Azure Database pg_dump hozta létre a PostgreSQL archív fájlból, és a kezdeti betöltés beállítását és a céladatbázis Attunity megfelel a Microsoft Migrations a forrásadatbázis szinkronizálási adatok folyamatos."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Az Azure Database-áttelepítés minimális állásidővel PostgreSQL
Áttelepítheti a meglévő PostgreSQL-adatbázisról Azure Database PostgreSQL az Attunity megfelel a Microsoft Migrations, egy közös szponzorált, közös ajánlat Attunity és az Azure-adatbázis áttelepítése szolgáltatás megfelelően biztosított Microsoft minden Microsoft-ügyfél számára további költség nélkül. Microsoft Migrations Attunity megfelel is lehetővé teszi, hogy az adatbázis áttelepítése az állásidő minimális, és a forrás-adatbázis továbbra is fennáll, az áttelepítési folyamat során működőképes legyen.

Attunity megfelel egy olyan adatok replikációs eszköz, amely lehetővé teszi a különböző forrásokból és célok, közötti adatszinkronizálás, propagálása a létrehozási parancsprogrammal és az adatbázis társított adatokat. Attunity replikálás nem propagál minden egyéb összetevők (például SP, eseményindítók, Funkciók, stb.) vagy convert, például ilyen összetevők, T-SQL tárolt PL/SQL-kódot.

> [!NOTE]
> Míg Attunity replikálás áttelepítési forgatókönyvek széles körét támogatja, Microsoft Migrations Attunity megfelel egy adott részének forrás célgazdagép, célfelhő párokból támogatása összpontosít.

A folyamat minimális állásidővel áttelepítés áttekintését tartalmazza:

1. **A PostgreSQL-forrás séma áttelepítése** egy Azure Database segítségével PostgreSQL-adatbázishoz a [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) - n paraméterrel parancsot, és használata a [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) parancs.

2. **Kezdeti betöltés és az adatok folyamatos szinkronizálásához a forrás-adatbázisból történő beállítása a céladatbázis** Attunity megfelel a Microsoft Migrations. Ezzel minimálisra csökkenti a forrásadatbázis be kell állítania csak olvashatóként Azure váltani a cél PostgreSQL-adatbázisból az alkalmazások előkészítése az idejét.

Az Attunity megfelel a Microsoft Migrations ajánlat kapcsolatos további információkért tekintse meg a következőket:
 - A Microsoft Migrations Attunity replikálás [weblap](https://aka.ms/attunity-replicate).
 - [Töltse le](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity megfelel a Microsoft áttelepítésre.
 - Az Attunity replikálás [közösségi](https://microsoft.attunity.com/), az első lépések útmutatója, oktatóanyagok és támogatás.
 - Az Attunity használatával történő PostgreSQL az Azure-adatbázis PostgreSQL áttelepítésének lépésenkénti útmutatásért tekintse meg a [adatbázis áttelepítési útmutató](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).