---
title: Felhasználók létrehozása - Azure Database for PostgreSQL - Single Server
description: Ez a cikk bemutatja, hogyan hozhat létre új felhasználói fiókokat a PostgreSQL - Single Server Azure-adatbázissal való együttműködéshez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 127d484d6cfc35368803069f9c3d602e787baa56
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384347"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Felhasználók létrehozása a PostgreSQL Azure-adatbázisában – Egykiszolgálós

Ez a cikk azt ismerteti, hogyan hozhat létre felhasználókat egy Azure Database for PostgreSQL-kiszolgálón.

Ha szeretne többet megtudni az Azure-előfizetés-felhasználók és azok jogosultságai létrehozásáról és kezeléséről, látogasson el az [Azure szerepköralapú hozzáférés-vezérlési (RBAC) cikkbe,](../role-based-access-control/built-in-roles.md) vagy tekintse át a [szerepkörök testreszabását.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdafiók

Amikor először hozta létre az Azure Database for PostgreSQL-t, megadta a kiszolgáló rendszergazdájának felhasználónevét és jelszavát. További információkért kövesse a [rövid útmutatót,](quickstart-create-server-database-portal.md) hogy részletesen tekintse meg a részletes megközelítést. Mivel a kiszolgáló rendszergazdájának felhasználóneve egyéni név, az Azure Portalon megkeresheti a kiválasztott kiszolgálórendszergazdai felhasználónevet.

Az Azure Database for PostgreSQL-kiszolgáló a 3 alapértelmezett szerepkörrel van definiálva. Ezeket a szerepköröket a következő paranccsal láthatja:`SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- a kiszolgáló rendszergazdája

A kiszolgáló rendszergazdája a azure_pg_admin szerepkör tagja. A kiszolgálófelügyeleti fiók azonban nem része a azure_superuser szerepkörnek. Mivel ez a szolgáltatás egy felügyelt PaaS-szolgáltatás, csak a Microsoft része a rendszergazdai felhasználói szerepkörnek.

A PostgreSQL motor az adatbázis-objektumokhoz való hozzáférés szabályozására szolgál, ahogy azt a [PostgreSQL termékdokumentációja tárgyalja.](https://www.postgresql.org/docs/current/static/sql-createrole.html) Az Azure Database for PostgreSQL rendszerben a kiszolgáló rendszergazdája a következő jogosultságokat kapja: LOGIN, NOSUPERUSER, INHERIT, CREATEDB, CREATEROLE, NOREPLICATION

A kiszolgáló rendszergazdai felhasználói fiókja további felhasználók létrehozására használható, és a felhasználók számára a azure_pg_admin szerepkörbe való bekerüléséhez. Emellett a kiszolgálófelügyeleti fiók segítségével kevésbé kiemelt jogosultságú felhasználókat és szerepköröket hozhat létre, amelyek hozzáférnek az egyes adatbázisokhoz és sémákhoz.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>További rendszergazdai felhasználók létrehozása a PostgreSQL Azure Database for PostgreSQL-ben

1. A kapcsolat adatainak és a rendszergazdai felhasználónévnek a beszerezése.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló nevét és bejelentkezési adatait könnyen megtalálhatja a kiszolgáló **áttekintése** lapon vagy az Azure Portal **Tulajdonságok** lapján.

2. Az adatbázis-kiszolgálóhoz való csatlakozáshoz használja a rendszergazdai fiókot és a jelszót. Használja a kívánt ügyféleszközt, például a pgAdmin vagy a psql.
   Ha nem biztos a csatlakozás módjában, olvassa el [a rövid útmutatót](./quickstart-create-server-database-portal.md)

3. A következő SQL-kód szerkesztése és futtatása. Cserélje le az új felhasználónevet a helyőrző értékre, <new_user>, és cserélje le a helyőrző jelszót a saját erős jelszavára. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Adatbázis-felhasználók létrehozása a PostgreSQL Azure Database szolgáltatásában

1. A kapcsolat adatainak és a rendszergazdai felhasználónévnek a beszerezése.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. A kiszolgáló nevét és bejelentkezési adatait könnyen megtalálhatja a kiszolgáló **áttekintése** lapon vagy az Azure Portal **Tulajdonságok** lapján.

2. Az adatbázis-kiszolgálóhoz való csatlakozáshoz használja a rendszergazdai fiókot és a jelszót. Használja a kívánt ügyféleszközt, például a pgAdmin vagy a psql.

3. A következő SQL-kód szerkesztése és futtatása. Cserélje le a `<db_user>` helyőrző értéket a kívánt új `<newdb>` felhasználónévre, és a helyőrző értéket a saját adatbázisnevére. Cserélje le a helyőrző jelszót a saját erős jelszavára.

   Ez az sql kód szintaxis létrehoz egy új adatbázis nevű testdb, például célokra. Ezután létrehoz egy új felhasználót a PostgreSQL szolgáltatásban, és csatlakozási jogosultságokat biztosít az adott felhasználó új adatbázisához.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Rendszergazdai fiók használatával előfordulhat, hogy további jogosultságokat kell biztosítania az adatbázisban lévő objektumok védelméhez. Az adatbázis-szerepkörökkel és jogosultságokkal kapcsolatos további részleteket a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/current/static/ddl-priv.html) találja. Példa:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Jelentkezzen be a kiszolgálóra, adja meg a kijelölt adatbázist az új felhasználónév és jelszó használatával. Ez a példa a psql parancssorát mutatja. Ezzel a paranccsal a rendszer kéri a felhasználónév jelszavát. Cserélje le saját kiszolgálónevét, adatbázisnevét és felhasználónevét.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>További lépések

Nyissa meg a tűzfalat az új felhasználók gépeinek IP-címeihez, hogy azok csatlakozni tudjanak: [Az Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure Portalon](howto-manage-firewall-using-portal.md) vagy az Azure [CLI](howto-manage-firewall-using-cli.md)használatával.

A felhasználói fiókok kezelésével kapcsolatos további információkért olvassa el az [Adatbázis-szerepkörök és jogosultságok](https://www.postgresql.org/docs/current/static/user-manag.html), [A GRANT szintaxis](https://www.postgresql.org/docs/current/static/sql-grant.html)és a Jogosultságok PostgreSQL termékdokumentációjában. [Privileges](https://www.postgresql.org/docs/current/static/ddl-priv.html)
