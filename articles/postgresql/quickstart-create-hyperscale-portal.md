---
title: Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) a rövid útmutató
description: 'Gyors útmutató: létrehozása és lekérdezése elosztott táblák, Azure database for PostgreSQL, nagy kapacitású (Citus) (előzetes verzió).'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406453"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) az Azure Portalon

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) kiszolgáló csoportot az Azure portal használatával. Elosztott adatok fog feltárja: horizontális skálázási táblák történik a csomópontokon, mintául szolgáló adatok bevitele, és több csomóponton végrehajtott lekérdezések futtatása.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Azure-adatbázis létrehozása PostgreSQL-hez

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
3. Az üzembe helyezési lehetőséget választotta, kattintson a **létrehozás** gomb alatt **nagy kapacitású (Citus) kiszolgálócsoport - előzetes verzió.**
4. Adja meg az alábbi adatokat az új kiszolgálóűrlapon:
   - Erőforráscsoport: kattintson a **új létrehozása** a szövegmezőbe, a mező alatti hivatkozás. Írjon be egy nevet, például **myresourcegroup**.
   - Kiszolgálócsoport neve: Adjon meg egy egyedi nevet az új kiszolgálócsoport, amelyet egy kiszolgáló altartomány is használhat.
   - Rendszergazdai felhasználónév: Adjon meg egy egyedi felhasználónév, az adatbázishoz való csatlakozáshoz később használandó.
   - Jelszó: legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell hármat a következő kategóriákban – angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0 – 9) és nem alfanumerikus karakterek (!, $, #, %, stb.)
   - Hely: tegye lehetővé számukra a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz a felhasználók legközelebb eső helyet használja.

   > [!IMPORTANT]
   > A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

5. Kattintson a **konfigurálása kiszolgálócsoport**. Ebben a szakaszban nem változik, hagyja meg a beállításokat, és kattintson a **mentése**.
6. Kattintson a **felülvizsgálat + létrehozása** , majd **létrehozás** a kiszolgáló üzembe helyezéséhez. Az üzembe helyezés eltarthat néhány percig.
7. Az oldal átirányítja központi telepítésének figyeléséhez. Amikor az élő állapota változik **az üzembe helyezés folyamatban van** való **az üzembe helyezés befejeződése**, kattintson a **kimenetek** menüpont az oldal bal oldalán található.
8. A kimenetek lap tartalmaz egy gomb mellett, hogy az érték másolása a vágólapra a koordinátor állomásnévvel. Jegyezze fel ezt az információt későbbi használatra.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure-adatbázis PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) szolgáltatást használ a kiszolgálószintű tűzfal. Alapértelmezés szerint a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a koordinátor-csomópont és minden olyan kiszolgálón lévő adatbázisokra. Azt hozzá kell adnia egy szabályt, amely egy adott IP-címtartomány számára megnyitja a tűzfalat.

1. Az a **kimenetek** szakaszt, ahol korábban másolt a koordinátor-csomópont állomásnév, kattintson ismét a **áttekintése** menüpontot.

2. Az üzemelő példány méretezési csoport neve lesz a következő előtaggal "sg –". Keresse meg azt az erőforrások listájában, és kattintson rá.

3. Kattintson a **tűzfal** alatt **biztonsági** a bal oldali menüben.

4. Kattintson a hivatkozásra **+ Hozzáadás tűzfalszabályt az aktuális ügyfél IP-cím**. Végül kattintson a **mentése** gombra.

5. Kattintson a **Save** (Mentés) gombra.

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 5432-es portot.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Csatlakozás az adatbázishoz a psql használatával a Cloud Shellben

Használjuk a [psql](https://www.postgresql.org/docs/current/app-psql.html) parancssori segédprogramot az Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.
1. Indítsa el az Azure Cloud Shell-t a felső navigációs ablakban található terminálikonnal.

   ![Azure-adatbázis PostgreSQL-hez - Azure Cloud Shell terminálikon](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Az Azure Cloud Shell megnyílik a böngészőben, amely lehetővé teszi a bash-parancsok beírását.

   ![Azure-adatbázis PostgreSQL-hez - Azure Shell Bash parancssor](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. A Cloud Shell parancssornál csatlakozzon az Azure-adatbázis PostgreSQL-kiszolgálóhoz a psql-parancsok használatával. A következő formátum segítségével kapcsolódhat egy [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal rendelkező Azure-adatbázis PostgreSQL-kiszolgálóhoz:
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Például a következő parancsot a nevű alapértelmezett adatbázishoz kapcsolódik **citus** PostgreSQL-kiszolgálón **mydemoserver.postgres.database.azure.com** hozzáférési hitelesítő adatok használatával. Adja meg a kiszolgáló-rendszergazdai jelszavát, amikor a rendszer kéri.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Létrehozása és terjesztése a táblák

Miután csatlakozott a psql használatával nagy kapacitású koordinátor-csomóponton, néhány alapvető feladatot is elvégezheti.

Nagy kapacitású belüli kiszolgálók van háromféle táblák:

- Elosztott vagy horizontálisan particionált táblák (terjednek a teljesítmény és a párhuzamos feldolgozás méretezés segítségével)
- Referencia táblák (több példányban karbantartása)
- Helyi táblákkal (gyakran használt belső felügyeleti táblák)

Ebben a rövid útmutatóban elsősorban az fogunk összpontosítani elosztott táblák és az első ismeri azokat.

Az adatmodell fogunk dolgozni használata egyszerű: a GitHub felhasználói és az esemény adatait. Események közé az elágazásában létrehozása, a git véglegesíti az egy szervezet, és egyéb kapcsolódó.

Amint csatlakozott psql-jén keresztül hozzunk létre a táblázatok. A psql-konzol futtatása:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

A `payload` mezőjében `github_events` egy JSONB datatype adattípusa. JSONB a JSON-adattípus szerepel a Postgres bináris formátum. Ez megkönnyíti az egy rugalmas séma tárolásához egy oszlopban.

Postgres hozhat létre egy `GIN` index nA tomto typu ami indexeli-e minden kulcs és a benne lévő érték. Egy index használatával válik a gyors és egyszerű lekérdezési rendelkező különböző körülmények között. Most pedig hozzon létre néhány indexek, hogy az adatok betöltése előtt. A psql-jének:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ezután azt fogja Postgres táblák igénybe a koordinátor-csomóponton, és mondja el nagy kapacitású szegmensre azokat a feldolgozók között. Ehhez egy lekérdezést minden táblához, a kulcs szegmensre fogjuk futtatni azt az. Ebben a példában az események és a felhasználók tábla szegmens küldünk `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Készen állunk betölteni az adatokat. Töltse le a két példa fájlt [users.csv](https://examples.citusdata.com/users.csv) és [events.csv](https://examples.citusdata.com/events.csv). Miután letöltötte a fájlokat, csatlakozzon az adatbázishoz a psql, folyamatban van arra, hogy futtassa a letöltött fájlokat tartalmazó könyvtárat psql használatával. Az adatok betöltése a `\copy` parancsot:

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Lekérdezések futtatása

Most a visszatöltött azt is, valójában a lekérdezések futtatásának részt. Kezdjük az egyszerű `count (*)` mennyi adatot betöltése sikerült megtekintéséhez:

```sql
SELECT count(*) from github_events;
```

Szépen működőképes. Térjen vissza a rendezési kicsit az összesítés, egyelőre most nézzük meg néhány lekérdezést. A JSONB belül `payload` oszlop van egy jó kis adatokat, de, eseménytípus alapján változik. `PushEvent` események olyan méretnél, amely tartalmazza a leküldés egyedi véglegesítések számát tartalmazza. Véglegesítések száma óránként összesített számának használhatók:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Eddig vesz részt a lekérdezések rendelkezik a github\_események kizárólag, de hogy kombinálhatja ezeket az információkat github\_felhasználók. Óta, hogy horizontálisan skálázott felhasználóinak és események az azonos azonosítóval (`user_id`), az egyező felhasználói azonosítók mindkét tábla sorait lesz [védelmicsoport-készletek](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) ugyanazon adatbázis-csomópont, és könnyedén összekapcsolható a szabályzattípussal.

Ha azt csatlakozzon `user_id`, nagy kapacitású is leküldhetik az illesztési végrehajtási párhuzamos végrehajtás szegmensekre munkavégző csomóponton. Például keressük meg felhasználók számára létrehozott tárházak legnagyobb száma:

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben létrehozott egy kiszolgálócsoport Azure-erőforrásokat. Ha várhatóan nincs ezekre az erőforrásokra a későbbiekben szüksége, a kiszolgálócsoport törlése. Nyomja le az **törlése** gombra a **áttekintése** a kiszolgálócsoport oldalán. Amikor a rendszer kéri, egy előugró oldalon, erősítse meg a kiszolgálói csoport nevére, majd kattintson az utolsó **törlése** gombra.

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan lehet kiépíteni egy nagy kapacitású (Citus) kiszolgálócsoport. A psql-jének csatlakozik, létrehozott egy sémát és elosztott adatok.

Ezután az oktatóanyag egy méretezhető, több-bérlős alkalmazások létrehozásához.
> [!div class="nextstepaction"]
> [Több-Bérlős adatbázis tervezése](https://aka.ms/hyperscale-tutorial-multi-tenant)
