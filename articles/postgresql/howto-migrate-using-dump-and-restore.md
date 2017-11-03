---
title: "Memóriakép, és a PostgreSQL az Azure-adatbázis visszaállítása |} Microsoft Docs"
description: "Bontsa ki a PostgreSQL-adatbázisból egy biztonsági másolat fájlba, és szeretné visszaállítani a PostgreSQL-adatbázishoz egy Azure-adatbázis pg_dump hozta létre a PostgreSQL archívumfájl ismerteti."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/14/2017
ms.openlocfilehash: 190373c4980b67e16b58700e4b7e65658545c615
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Telepítse át az PostgreSQL-adatbázist használ a biztonsági másolat és helyreállítás
Használható [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) PostgreSQL-adatbázisból egy biztonsági másolat fájlba kibontásához és [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) fájlból történő visszaállításához a PostgreSQL-adatbázisból az archív pg_dump hozta létre.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Egy [PostgreSQL-kiszolgáló Azure-adatbázis](quickstart-create-server-database-portal.md) a tűzfalszabályokat access és az adatbázis.
- [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) és [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) telepített parancssori segédprogramok

Kövesse az alábbi lépéseket dump, és visszaállíthatja a PostgreSQL-adatbázisban:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Hozzon létre egy biztonsági másolat fájlt, amely tartalmazza az adatok betöltését pg_dump használatával
Készítsen biztonsági másolatot egy meglévő PostgreSQL adatbázis helyszíni vagy egy virtuális Gépre, a következő parancsot:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Például, ha a helyi kiszolgáló és adatbázis **testdb** benne
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Állítsa vissza az adatokat a cél Azure Database-be a PostrgeSQL pg_restore használatával
Miután létrehozta a céladatbázis, használhatja a pg_restore parancs és a -d, az adatok helyreállítását a kiírt fájlok a cél adatbázisba--dbname paraméter.
```bash
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Ebben a példában, visszaállíthatja az adatokat a memóriakép **testdb.dump** az adatbázisba **mypgsqldb** célkiszolgálón **mypgserver-20170401.postgres.database.azure.com**.
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Következő lépések
- Egy PostgreSQL-adatbázist az exportálás és importálás áttelepítéséhez lásd: [telepítse át az exportálási PostgreSQL-adatbázist, és importálása](howto-migrate-using-export-and-import.md)