---
title: "Felhasználók létrehozása az Azure Database PostgreSQL-kiszolgáló"
description: "Ez a cikk ismerteti, hogyan hozhat létre új felhasználói fiókokat egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis kommunikál."
services: postgresql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 87a73929185112190d5dd6698e014db225ebc08e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Felhasználók létrehozása az Azure Database PostgreSQL-kiszolgáló 
Ez a cikk ismerteti, hogyan hozhat létre felhasználók PostgreSQL-kiszolgáló egy Azure-adatbázis.

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdai fiók
A PostgreSQL a az Azure-adatbázis kezdeti létrehozásakor megadott egy kiszolgálói rendszergazda felhasználónevet és jelszót. További információkért kövesse a [gyors üzembe helyezés](quickstart-create-server-database-portal.md) a megközelítésnek megjelenítéséhez. Mivel a kiszolgáló-Rendszergazda felhasználóneve egy egyéni nevet, megkeresheti az Azure-portálon választott kiszolgáló rendszergazdai felhasználónév.

Az 3 alapértelmezett szerepköröket, meghatározott PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis jön létre. Ezek a szerepkörök láthatja a parancs futtatásával: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- a kiszolgálói rendszergazda felhasználó

A kiszolgálói rendszergazda felhasználó a azure_pg_admin szerepkör tagja. Azonban a kiszolgáló rendszergazdai fiók nincs a azure_superuser szerepkör részét. Mivel ez a szolgáltatás egy felügyelt PaaS-szolgáltatás, akkor csak a Microsoft a felügyelő szerepkör részét képezi. 

A PostgreSQL motor által jogosultságokkal adatbázis-objektumok való hozzáférés szabályozása leírtaknak megfelelően a [PostgreSQL termékdokumentációjának](https://www.postgresql.org/docs/current/static/sql-createrole.html). Azure-adatbázisban PostgreSQL, a kiszolgálói rendszergazda felhasználó ezeket a jogokat kap: bejelentkezés, NOSUPERUSER, ÖRÖKLÉSI, CREATEDB, CREATEROLE, NOREPLICATION

A kiszolgáló-rendszergazda felhasználói fiók segítségével további felhasználók létrehozása, és adja meg azoknak a felhasználóknak a azure_pg_admin szerepkörhöz. A kiszolgáló-rendszergazdai fiókot is, korlátozott engedélyekkel rendelkező felhasználók és az egyes adatbázisok és -sémákat hozzáféréssel rendelkező szerepkörök létrehozására használható.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>További rendszergazda felhasználóknak PostgreSQL Azure-adatbázis létrehozása
1. A kapcsolat információkat és a rendszergazda felhasználónév olvasható be.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Könnyedén megtalálhatja a kiszolgáló nevét és a bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure portálon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való csatlakozáshoz. Az ügyfél előnyben részesített eszköz, például pgAdmin vagy psql használható.
   Ha biztos benne, hogy hogyan csatlakoztathat, lásd: [psql felhő rendszerhéj használatával PostgreSQL-adatbázishoz való kapcsolódás](./quickstart-create-server-database-portal.md#connect-to-the-postgresql-database-by-using-psql-in-cloud-shell)

3. Szerkessze, és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét < új_felhasználó_jelszava > új felhasználónevét, és cserélje le a helyőrző jelszó erős jelszót. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Adatbázis-felhasználók PostgreSQL Azure-adatbázis létrehozása

1. A kapcsolat információkat és a rendszergazda felhasználónév olvasható be.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Könnyedén megtalálhatja a kiszolgáló nevét és a bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure portálon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való csatlakozáshoz. Az ügyfél előnyben részesített eszköz, például pgAdmin vagy psql használható.

3. Szerkessze, és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét `<db_user>` a kívánt új felhasználó nevét, és a helyőrző értékét `<newdb>` a saját adatbázis nevével. Cserélje le a helyőrző jelszó erős jelszót. 

   Az sql-kódot szintaxis létrehoz egy új adatbázist nevű testdb, például célokra. Ezután a PostgreSQL szolgáltatás hoz létre egy új felhasználót, és biztosít jogosultsága csatlakozni az adott felhasználó az új adatbázis. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE testdb TO <db_user>;
   ```

4. Egy rendszergazda fiók használatával, szükség lehet további jogosultságokat az objektumok az adatbázis védelmét. Tekintse meg a [PostgreSQL-dokumentáció](https://www.postgresql.org/docs/current/static/ddl-priv.html) további tájékoztatást talál a adatbázis-szerepkörök és a jogosultságokat. Példa: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Jelentkezzen be a kiszolgálóra, adja meg a kijelölt adatbázis, az új felhasználónevet és jelszót használja. Ez a példa bemutatja a psql parancssorban. Ezzel a paranccsal kéri a jelszót a felhasználónévvel. Cserélje le a saját kiszolgáló neve, az adatbázisnév és a felhasználó neve.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>További lépések
Nyissa meg a tűzfalon a IP-címekhez, hogy azok tudjanak csatlakozni az új felhasználók gépek: [hozzon létre és kezelheti az Azure-adatbázis PostgreSQL tűzfalszabályokat az Azure portál használatával](howto-manage-firewall-using-portal.md) vagy [Azure CLI](howto-manage-firewall-using-cli.md).

Felhasználóifiók-kezelés kapcsolatos további információkért tekintse meg a termékhez tartozó dokumentáció PostgreSQL [adatbázis-szerepkörök és a jogosultságok](https://www.postgresql.org/docs/current/static/user-manag.html), [ADJON szintaxis](https://www.postgresql.org/docs/current/static/sql-grant.html), és [jogosultságokkal](https://www.postgresql.org/docs/current/static/ddl-priv.html).
