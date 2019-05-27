---
title: fájl belefoglalása
description: fájl belefoglalása
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66154495"
---
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) létrehozása

Kövesse az alábbi lépéseket az Azure-adatbázis PostgreSQL-kiszolgálóhoz létrehozásához:
1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **Azure-adatbázis PostgreSQL-kiszolgálóhoz** lehetőséget.
3. Az üzembe helyezési lehetőséget választotta, kattintson a **létrehozás** gomb alatt **nagy kapacitású (Citus) kiszolgálócsoport - előzetes verzió.**
4. Adja meg az alábbi adatokat az új kiszolgálóűrlapon:
   - Erőforráscsoport: kattintson a **új létrehozása** a szövegmezőbe, a mező alatti hivatkozás. Írjon be egy nevet, például **myresourcegroup**.
   - Kiszolgálócsoport neve: Adjon meg egy egyedi nevet az új kiszolgálócsoport, amelyet egy kiszolgáló altartomány is használhat.
   - Rendszergazdai felhasználónév: az értéknek kell lennie jelenleg szükséges **citus**, és nem módosítható.
   - Jelszó: nem lehet legalább nyolc karakter lehet, és a következő kategóriák közül hármat tartalmaznia – angol nagybetűs betűket, angol kisbetűs karakterek, számjegyek (0 – 9) és nem alfanumerikus karakterek (!, $, #, % stb.)
   - Hely: tegye lehetővé számukra a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz a felhasználók legközelebb eső helyet használja.

   > [!IMPORTANT]
   > Jelentkezzen be a kiszolgáló és az adatbázisokhoz szükséges az itt megadott kiszolgáló-rendszergazdai jelszót. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

5. Kattintson a **konfigurálása kiszolgálócsoport**. Ebben a szakaszban nem változik, hagyja meg a beállításokat, és kattintson a **mentése**.
6. Kattintson a **felülvizsgálat + létrehozása** , majd **létrehozás** a kiszolgáló üzembe helyezéséhez. Az üzembe helyezés eltarthat néhány percig.
7. Az oldal átirányítja központi telepítésének figyeléséhez. Amikor az élő állapota változik **az üzembe helyezés folyamatban van** való **az üzembe helyezés befejeződése**, kattintson a **kimenetek** menüpont az oldal bal oldalán található.
8. A kimenetek lap tartalmaz egy gomb mellett, hogy az érték másolása a vágólapra a koordinátor állomásnévvel. Jegyezze fel ezt az információt későbbi használatra.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Az Azure-adatbázis PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) szolgáltatást használ a kiszolgálószintű tűzfal. Alapértelmezés szerint a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a koordinátor-csomópont és minden olyan kiszolgálón lévő adatbázisokra. Azt hozzá kell adnia egy szabályt, amely egy adott IP-címtartomány számára megnyitja a tűzfalat.

1. Az a **kimenetek** szakaszt, ahol korábban másolt a koordinátor-csomópont állomásnév, kattintson ismét a **áttekintése** menüpontot.

2. Keresse meg az üzemelő példány kiszolgálói csoport nevére, és kattintson rá. (A kiszolgálócsoport neve lesz *nem* utótagja. Elemek rendelkező, például "-c", "-w0", vagy "-w1" a kiszolgálócsoport nem állnak.)

3. Kattintson a **tűzfal** alatt **biztonsági** a bal oldali menüben.

4. Kattintson a hivatkozásra **+ Hozzáadás tűzfalszabályt az aktuális ügyfél IP-cím**.

5. Végül kattintson a **mentése** gombra.

   > [!NOTE]
   > Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL-adatbáziskiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 5432-es portot.
   >

## <a name="connect-to-the-database-using-psql"></a>Csatlakozás az adatbázishoz a psql használatával

Az Azure Database for PostgreSQL-kiszolgáló létrehozásakor egy alapértelmezett adatbázis nevű **citus** jön létre. Az adatbázis-kiszolgálóhoz csatlakozni, szüksége van egy kapcsolati karakterláncot, és a rendszergazdai jelszót.

1. Szerezze be a kapcsolati karakterláncot. A kiszolgáló oldalán kattintson a **kapcsolati karakterláncok** menüpontot. (Alatt áll **beállítások**.) Keresse meg a megjelölt karakterláncot  **C++ (libpq)**. A képernyő lesz:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Másolja a karakterláncot. Le kell cserélnie "{a\_jelszó}" és a korábban kiválasztott rendszergazdai jelszóval. A rendszer nem tárolja a titkosítatlan szöveges jelszót, és ezért nem tudja megjeleníteni az Ön számára a kapcsolati karakterláncban.

2. Nyisson meg egy terminálablakot, a helyi számítógépen.

3. Amikor a rendszer, az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozhat a [psql](https://www.postgresql.org/docs/current/app-psql.html) segédprogramot. Ajánlatok, ügyelve arra, a jelszó tartalmazza a kapcsolati karakterláncot adja át:
   ```bash
   psql "{connection_string}"
   ```

   Például a következő parancsot a koordinátor-csomópont a kiszolgálócsoport kapcsolódik **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
