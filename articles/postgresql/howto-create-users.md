---
title: Felhasználók létrehozása az Azure Database for PostgreSQL-kiszolgáló
description: Ez a cikk bemutatja, hogyan kommunikálhat egy Azure Database for PostgreSQL-kiszolgáló új felhasználói fiókokat hozhat létre.
author: jasonwhowell
ms.author: jasonh
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2018
ms.openlocfilehash: 8b1bf6f1eccefb9235751c9e113c90566dfdff79
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540825"
---
# <a name="create-users-in-azure-database-for-postgresql-server"></a>Felhasználók létrehozása az Azure Database for PostgreSQL-kiszolgáló 
Ez a cikk bemutatja, hogyan hozhat létre felhasználókat az Azure Database for PostgreSQL-kiszolgálóhoz.

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdai fiók
Első létrehozásakor az Azure Database for postgresql-hez, a megadott egy kiszolgáló-rendszergazdai felhasználói nevet és jelszót. További információkért kövesse a [rövid](quickstart-create-server-database-portal.md) a lépésekre bontott eljárással megtekintéséhez. Mivel a kiszolgáló-rendszergazdai felhasználónév egy egyéni nevet, a kiválasztott kiszolgálóra rendszergazdai felhasználónév az Azure Portalon találja.

Az Azure Database for PostgreSQL-kiszolgálóhoz megadott 3 alapértelmezett szerepkör jön létre. Ezek a szerepkörök a parancs futtatásával tekintheti meg: `SELECT rolname FROM pg_roles;`
- azure_pg_admin
- azure_superuser
- a kiszolgálói rendszergazda felhasználó

A kiszolgálói rendszergazda felhasználó a azure_pg_admin szerepkör tagja. Azonban a kiszolgálói rendszergazdai fiók nem szerepel a azure_superuser szerepkör. Mivel ez a szolgáltatás egy felügyelt PaaS-szolgáltatás, akkor csak a Microsoft a felügyelő szerepkör részét képezi. 

A PostgreSQL motor által jogosultsággal férhet hozzá az adatbázis-objektumok, az a [PostgreSQL termékdokumentáció](https://www.postgresql.org/docs/current/static/sql-createrole.html). Az Azure Database for postgresql-hez a kiszolgálói rendszergazda felhasználó ezeket a jogosultságokat kap: BEJELENTKEZÉS, NOSUPERUSER, ÖRÖKLIK, CREATEDB, CREATEROLE, NOREPLICATION

A kiszolgálói rendszergazdai felhasználói fiókkal használható további felhasználók létrehozása, és biztosítson számára ezek a felhasználók a azure_pg_admin szerepkörhöz. A kiszolgálói rendszergazdai fiók is, kevesebb jogosultsággal rendelkező felhasználók és szerepkörök, amelyek hozzáférhetnek a különálló adatbázisok és sémák létrehozásához használható.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Hogyan lehet további rendszergazda felhasználóknak az Azure Database for PostgreSQL létrehozása
1. Kérje le a kapcsolati információkat és a rendszergazdai felhasználó nevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Megtalálhatja a kiszolgáló nevét és bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure Portalon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való kapcsolódáshoz. A preferált ügyfél eszköz, például a pgAdmin vagy psql használható.
   Ha biztos benne, hogy hogyan kapcsolódhat, [Ez a rövid útmutató](./quickstart-create-server-database-portal.md)

3. Szerkessze és futtassa a következő SQL-kódot. Cserélje le a helyőrző értékét < új_felhasználó_jelszava > az új felhasználó nevét, és cserélje le a helyőrző jelszót a saját erős jelszót. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Hogyan lehet az Azure Database for PostgreSQL adatbázis-felhasználók létrehozása

1. Kérje le a kapcsolati információkat és a rendszergazdai felhasználó nevét.
   Az adatbázis-kiszolgálóhoz való csatlakozáshoz szüksége van a teljes kiszolgálónévre és a rendszergazdai bejelentkezési hitelesítő adatokra. Megtalálhatja a kiszolgáló nevét és bejelentkezési adatait a kiszolgálóról **áttekintése** lap vagy az **tulajdonságok** oldal az Azure Portalon. 

2. A rendszergazdai fiókot és jelszót használja az adatbázis-kiszolgálóhoz való kapcsolódáshoz. A preferált ügyfél eszköz, például a pgAdmin vagy psql használható.

3. Szerkessze és futtassa a következő SQL-kódot. A helyőrző értékét cserélje le `<db_user>` a kívánt új felhasználónevet és a helyőrző értékét `<newdb>` a saját adatbázis nevére. A helyőrző jelszót cserélje le a saját erős jelszót. 

   Az sql-kódot szintaxis egy új adatbázist hozunk testdb, példa célokra. Ezután létrehoz egy új felhasználót a PostgreSQL szolgáltatás, és biztosít jogosultságokkal csatlakozhat az új adatbázisnak, hogy a felhasználó számára. 

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Egy rendszergazdai fiók használatával, szükség lehet további jogosultságokat az objektumokat az adatbázis védelmét. Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/current/static/ddl-priv.html) további tudnivalók az adatbázis-szerepkörök és jogosultságok. Példa: 
   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Jelentkezzen be a kiszolgálóra, adja meg a kijelölt adatbázis, az új felhasználónévvel és jelszóval. Ez a példa bemutatja a psql parancssor. Ezzel a paranccsal kéri a felhasználónévhez tartozó jelszót. Cserélje le a saját kiszolgáló nevét, az adatbázis neve és a felhasználó neve.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>További lépések
Megnyitja a tűzfalat, hogy azok tudjanak csatlakozni az új felhasználók gépek IP-címek: [Hozzon létre és kezelhető az Azure Database for PostgreSQL tűzfalszabályok az Azure portal használatával](howto-manage-firewall-using-portal.md) vagy [Azure CLI-vel](howto-manage-firewall-using-cli.md).

Fiókkezeléssel kapcsolatos további információkért tekintse meg a termékhez tartozó dokumentáció PostgreSQL [adatbázis-szerepkörök és jogosultságok](https://www.postgresql.org/docs/current/static/user-manag.html), [GRANT szintaxis](https://www.postgresql.org/docs/current/static/sql-grant.html), és [jogosultságokkal](https://www.postgresql.org/docs/current/static/ddl-priv.html).
