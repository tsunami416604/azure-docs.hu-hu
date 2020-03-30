---
title: Felhasználók létrehozása - Nagy kapacitású (Citus) - Azure Database for PostgreSQL
description: Ez a cikk ismerteti, hogyan hozhat létre új felhasználói fiókokat a PostgreSQL - Hyperscale (Citus) Azure-adatbázissal való együttműködéshez.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484927"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Felhasználók létrehozása a PostgreSQL Azure-adatbázisában – Nagy kapacitás (Citus)

> [!NOTE]
> A "felhasználók" kifejezés a nagyméretű (Citus) kiszolgálócsoporton belüli felhasználókra vonatkozik. Ha inkább az Azure-előfizetés-felhasználókról és azok jogosultságairól szeretne többet megtudni, látogasson el az [Azure szerepköralapú hozzáférés-vezérlési (RBAC) cikkbe,](../role-based-access-control/built-in-roles.md) vagy tekintse át a [szerepkörök testreszabásának módját.](../role-based-access-control/custom-roles.md)

## <a name="the-server-admin-account"></a>A kiszolgálói rendszergazdafiók

A PostgreSQL motor [szerepköröket](https://www.postgresql.org/docs/current/sql-createrole.html) használ az adatbázis-objektumokhoz való hozzáférés szabályozásához, és egy újonnan létrehozott Hyperscale (Citus) kiszolgálócsoport több előre definiált szerepkörrel rendelkezik:

* Az [alapértelmezett PostgreSQL szerepkörök](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Mivel a Nagykapacitás egy felügyelt PaaS-szolgáltatás, `postgres` csak a Microsoft tud bejelentkezni a rendszergazdai felhasználói szerepkörrel. A korlátozott felügyeleti hozzáférés, `citus` a nagy kapacitás biztosítja a szerepkört.

A szerepkör `citus` engedélyei:

* Olvassa el az összes konfigurációs változót, még azokat a változókat is, amelyek általában csak a szuperfelhasználók számára láthatók.
* Olvassa el\_\_ \* az összes pg stat nézetet, és használjon különböző statisztikákkal kapcsolatos bővítményeket - még olyan nézeteket vagy kiterjesztéseket is, amelyek általában csak a szuperfelhasználók számára láthatók.
* Olyan figyelési funkciók végrehajtása, amelyek az ACCESS SHARE zárolásokat az asztalokon tarthatják, potenciálisan hosszú ideig.
* [PostgreSQL-bővítmények létrehozása](concepts-hyperscale-extensions.md) (mivel a szerepkör `azure_pg_admin`tagja).

Nevezetesen, `citus` a szerep néhány korlátozás:

* Nem lehet szerepköröket létrehozni
* Nem lehet adatbázisokat létrehozni

## <a name="how-to-create-additional-user-roles"></a>További felhasználói szerepkörök létrehozása

Mint említettük, az `citus` admin fiók nem rendelkezik engedéllyel további felhasználók létrehozására. Felhasználó hozzáadásához használja az Azure Portal felületét.

1. Nyissa meg a Nagykapacitású kiszolgálócsoport **Szerepkörök** lapját, és kattintson **a + Hozzáadás gombra:**

   ![A szerepkörök lapja](media/howto-hyperscale-create-users/1-role-page.png)

2. Adja meg a szerepkör nevét és jelszavát. Kattintson a **Mentés** gombra.

   ![Szerepkör hozzáadása](media/howto-hyperscale-create-users/2-add-user-fields.png)

A felhasználó a kiszolgálócsoport koordinátorcsomópontján jön létre, és az összes munkavégző csomópontra propagál. Az Azure Portalon keresztül `LOGIN` létrehozott szerepkörök rendelkeznek az attribútummal, ami azt jelenti, hogy valódi felhasználók, akik bejelentkezhetnek az adatbázisba.

## <a name="how-to-modify-privileges-for-user-role"></a>A felhasználói szerepkör jogosultságai módosítása

Az új felhasználói szerepköröket gyakran használják korlátozott jogosultságokkal rendelkező adatbázis-hozzáférés biztosításához. A felhasználói jogosultságok módosításához használjon szabványos PostgreSQL parancsokat egy olyan eszközzel, mint a PgAdmin vagy a psql. (Lásd: [Csatlakozás a psql-hez](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) a Nagykapacitású (Citus) rövid útmutatóban.)

Például az `db_user` olvasás `mytable`engedélyezéséhez adja meg az engedélyt:

```sql
GRANT SELECT ON mytable TO db_user;
```

A nagykapacitású (Citus) egytáblás GRANT-utasítások a teljes fürtön keresztül propagálva, az összes munkavégző csomóponton alkalmazva azokat. Azonban a rendszerszintű grant-okat (például a séma összes tábláját) minden dátumcsomóponton futtatni kell.  Használja `run_command_on_workers()` a segítő funkciót:

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>Felhasználói szerepkör törlése vagy jelszó módosítása

Felhasználó frissítéséhez keresse fel a Nagykapacitású kiszolgálócsoport **Szerepkörök** lapját, és kattintson a felhasználó melletti három **pontra.** A három pont megnyit egy menüt a felhasználó törléséhez vagy jelszavának visszaállításához.

   ![Szerepkör szerkesztése](media/howto-hyperscale-create-users/edit-role.png)

A `citus` szerepkör privilegizált, és nem törölhető.

## <a name="next-steps"></a>További lépések

Nyissa meg a tűzfalat az új felhasználók gépeinek IP-címeihez, hogy azok csatlakozni tudjanak: [Nagykapacitású (Citus) tűzfalszabályok létrehozása és kezelése az Azure Portal használatával.](howto-hyperscale-manage-firewall-using-portal.md)

Az adatbázis-felhasználói fiókok kezeléséről további információt a PostgreSQL termékdokumentációjában talál:

* [Adatbázis-szerepkörök és jogosultságok](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT szintaxis](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Jogosultságok](https://www.postgresql.org/docs/current/static/ddl-priv.html)
