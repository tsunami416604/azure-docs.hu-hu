---
title: Felhasználók létrehozása a Azure Database for PostgreSQL-nagy kapacitású (Citus)
description: Ez a cikk azt ismerteti, hogyan hozhat létre új felhasználói fiókokat Azure Database for PostgreSQL-nagy kapacitású (Citus) való interakcióhoz.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7187135b29f0a9a790c032330c73bcb1ae27229b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515940"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Felhasználók létrehozása a Azure Database for PostgreSQL-nagy kapacitású (Citus)

Ez a cikk azt ismerteti, hogyan hozhat létre felhasználókat a nagy kapacitású (Citus) csoporton belül. Az Azure-előfizetések felhasználóinak és a hozzájuk tartozó jogosultságoknak a megismeréséhez látogasson el az [Azure szerepköralapú hozzáférés-vezérlés (RBAC) című cikkre](../role-based-access-control/built-in-roles.md) , vagy tekintse át a [szerepkörök testreszabását](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdai fiók

Az újonnan létrehozott nagy kapacitású-(Citus-) kiszolgálócsoport több szerepkört is tartalmaz előre definiált:

* Az [alapértelmezett PostgreSQL-szerepkörök](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

A kiszolgáló-rendszergazda felhasználó, a *citus*, a *azure_pg_admin* szerepkör tagja.
Azonban nem része a *postgres* (felügyelői) szerepkörnek.  Mivel a nagy kapacitású egy felügyelt Pásti szolgáltatás, csak a Microsoft tagja a felügyelői szerepkörnek.

A PostgreSQL-motor jogosultságokat használ az adatbázis-objektumokhoz való hozzáférés vezérlésére, ahogy azt a [PostgreSQL termékdokumentációja](https://www.postgresql.org/docs/current/static/sql-createrole.html)tárgyalja.
Azure Database for PostgreSQL a kiszolgáló-rendszergazda felhasználó megkapja ezeket a jogosultságokat: bejelentkezés, nem rendszergazda, öröklés, CREATEDB, CREATEROLE, nincs REPLIKÁCIÓ

## <a name="how-to-create-additional-users"></a>További felhasználók létrehozása

A *citus* -rendszergazdai fióknak nincs engedélye további felhasználók létrehozására. Felhasználó hozzáadásához használja helyette a Azure Portal.

1. Lépjen a nagy kapacitású-kiszolgálócsoport **szerepkörök** lapjára, majd kattintson a **+ Hozzáadás**gombra:

   ![A szerepkörök lap](media/howto-hyperscale-create-users/1-role-page.png)

2. Adja meg a szerepkör nevét és jelszavát. Kattintson a **Save** (Mentés) gombra.

   ![Szerepkör hozzáadása](media/howto-hyperscale-create-users/2-add-user-fields.png)

A felhasználó a kiszolgálócsoport koordinátor csomópontján jön létre, és az összes munkavégző csomópontra propagálva lesz.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Felhasználó törlése vagy jelszavuk módosítása

Lépjen a nagy kapacitású-kiszolgálócsoport **szerepkörök** lapjára, majd kattintson a három pontra **...** a felhasználó mellett. Az ellipszisek menüt nyitnak meg a felhasználó törléséhez vagy a jelszavuk alaphelyzetbe állításához.

   ![Szerepkör szerkesztése](media/howto-hyperscale-create-users/edit-role.png)

A *citus* szerepkör jogosultsággal rendelkezik, és nem törölhető.

## <a name="how-to-modify-privileges-for-role"></a>A szerepkörhöz tartozó jogosultságok módosítása

Az új szerepköröket általában korlátozott jogosultságokkal rendelkező adatbázis-hozzáférés biztosítására használják. A felhasználói jogosultságok módosításához használja a standard PostgreSQL-parancsokat egy olyan eszköz használatával, mint például a PgAdmin vagy a psql. (Lásd: [Csatlakozás a psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) -hez a nagy kapacitású (Citus) rövid útmutatójában.)

Ha például engedélyezni szeretné a *db_user* számára a *sajáttábla*olvasását, adja meg az engedélyt:

```sql
GRANT SELECT ON mytable TO db_user;
```

A nagy kapacitású (Citus) a teljes fürtön keresztül propagálja az egytáblás engedélyezési utasításokat, és alkalmazza azokat az összes munkavégző csomóponton. Azonban a teljes rendszerszintű (például a sémában lévő összes táblához tartozó) támogatásnak minden nap csomóponton futnia kell.  Használja a *run_command_on_workers ()* Helper függvényt:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>További lépések

Nyissa meg a tűzfalat az új felhasználói gépek IP-címei számára, hogy csatlakozhasson a kapcsolódáshoz: [nagy kapacitású-(Citus-) tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](howto-hyperscale-manage-firewall-using-portal.md).

Az adatbázis-felhasználói fiókok kezelésével kapcsolatos további információkért lásd a PostgreSQL termékdokumentációját:

* [Adatbázis-szerepkörök és-jogosultságok](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Szintaxis megadása](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Kiváltságok](https://www.postgresql.org/docs/current/static/ddl-priv.html)
