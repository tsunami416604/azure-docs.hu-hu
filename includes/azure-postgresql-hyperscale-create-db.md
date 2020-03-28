---
title: fájl belefoglalása
description: fájl belefoglalása
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: e7a6f7b4ba4219483cd3eb8f4600bc94213df131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973414"
---
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Azure-adatbázis létrehozása a PostgreSQL számára – Nagy kapacitás (Citus)

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1. Kattintson **az Erőforrás létrehozása** az Azure Portal bal felső sarkában.
2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
3. A központi telepítési beállításhoz kattintson a Create **(Citus) kiszolgálócsoport** **létrehozás** gombjára.
4. Adja meg az alábbi adatokat az új kiszolgálóűrlapon:
   - Erőforráscsoport: kattintson az **Új létrehozása** hivatkozásra a mező szövegmezője alatt. Írjon be egy nevet, például **a myresourcegroupnevet.**
   - Kiszolgálócsoport neve: adja meg az új kiszolgálócsoport egyedi nevét, amelyet egy kiszolgálóaltartományhoz is használni fog.
   - Rendszergazdai felhasználónév: jelenleg szükséges, hogy az érték **citus**, és nem lehet módosítani.
   - Jelszó: legalább nyolc karakter hosszúnak kell lennie, és a következő kategóriák közül három karaktert tartalmazhat – angol nagybetűk, angol kisbetűk, számok (0-9) és nem alfanumerikus karakterek (!, $, #, %, és így tovább.)
   - Hely: használja a felhasználókhoz legközelebb eső helyet, hogy a leggyorsabb hozzáférést biztosítson számukra az adatokhoz.

   > [!IMPORTANT]
   > Az itt megadott kiszolgálórendszergazdai jelszó szükséges a kiszolgálóra és annak adatbázisaiba való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

5. Kattintson **a Kiszolgálócsoport konfigurálása gombra.** Hagyja a szakaszok beállításait változatlanul, és kattintson a **Mentés gombra.**
6. Kattintson a **Tovább gombra: A** képernyő alján található hálózati >.

7. A **Hálózat** lapon kattintson a **Nyilvános végpont** választógombra.
   ![Nyilvános végpont kiválasztva](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Kattintson a **+ Aktuális ügyfél IP-címének hozzáadása hivatkozásra**.
   ![Ügyfél IP-címe hozzáadva](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ha igen, csak akkor tud csatlakozni a nagykapacitású (Citus) fürthöz, ha az informatikai részleg megnyitja az 5432-es portot.
   >

9. Kattintson **a Véleményezés + létrehozás,** majd a **Létrehozás gombra** a kiszolgáló kiépítéséhez. Az üzembe helyezés eltarthat néhány percig.
10. A lap átirányítja a telepítés figyelésére. Amikor az élő állapot változások **a Telepítés folyamatban van** a Telepítés **befejeződött,** kattintson a **Kimenetek** menüpont a lap bal oldalán.
11. A kimenetek lap egy koordinátor állomásnevét tartalmazza, mellette egy gombbal az érték vágólapra másolásához. Rögzítse ezt az információt későbbi használatra.

## <a name="connect-to-the-database-using-psql"></a>Csatlakozás az adatbázishoz a psql használatával

Amikor létrehozza az Azure Database for PostgreSQL-kiszolgáló, egy alapértelmezett adatbázis nevű **citus** jön létre. Az adatbázis-kiszolgálóhoz való csatlakozáshoz kapcsolati karakterláncra és rendszergazdai jelszóra van szükség.

1. Szerezze be a kapcsolati karakterláncot. A kiszolgálócsoport lapon kattintson a **Kapcsolati karakterláncok** menüelemre. (Ez a **Beállítások**.) Keresse meg a **psql**. Ez lesz a forma:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Másolja a karakterláncot. A(z) "{your\_password}" szót le kell cserélnie a korábban kiválasztott rendszergazdai jelszóra. A rendszer nem tárolja az egyszerű szöveges jelszót, így nem tudja megjeleníteni a kapcsolati karakterláncban.

2. Nyisson meg egy terminálablakot a helyi számítógépen.

3. A parancssorban csatlakozzon az Azure Database for PostgreSQL-kiszolgálóhoz a [psql](https://www.postgresql.org/docs/current/app-psql.html) segédprogrammal. Adja át a kapcsolati karakterláncot idézőjelek között, és győződjön meg róla, hogy tartalmazza a jelszavát:
   ```bash
   psql "host=..."
   ```

   A következő parancs például a **mydemoserver**kiszolgálócsoport koordinátorcsomópontjához csatlakozik:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
