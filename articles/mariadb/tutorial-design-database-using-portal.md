---
title: 'Oktatóanyag: Azure Database for MariaDB tervezése az az Azure portal használatával'
description: Ez az oktatóanyag azt ismerteti, hogyan hozható létre és kezelhető egy Azure Database for MariaDB-kiszolgáló és -adatbázis az Azure Portalon.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: tutorial
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: 1eb24d90c3aefa81f53a3e31c0bd460f45e5a250
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617699"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Oktatóanyag: Egy Azure Database for MariaDB-adatbázis tervezése az Azure portal használatával

Az Azure Database for MariaDB egy felügyelt szolgáltatás, amellyel a magas rendelkezésre állású MySQL-adatbázisokat futtathatja, kezelheti és skálázhatja a felhőben. Az Azure Portalon könnyedén kezelheti a kiszolgálót és tervezheti meg az adatbázist.

Ebből az oktatóanyagból az Azure Portal használatával megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Database for MariaDB létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a mysql parancssori eszközzel
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a böngészőjében az [Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása

Az Azure Database for MariaDB-kiszolgálót [számítási és tárolási erőforrások](concepts-pricing-tiers.md) egy meghatározott készletével együtt fogja létrehozni. A kiszolgáló egy [Azure-erőforráscsoporton](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) belül jön létre.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

2. Válassza ki **adatbázisok** > **Azure Database for MariaDB**. Is beírhat **MariaDB** a keresőmezőbe, hogy megtalálja a szolgáltatást.
   
   ![Ugrás a MySQL-hez](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Válassza az **Azure Database for MariaDB** csempét, majd a **Létrehozás** lehetőséget. Adja meg vagy válassza ki a kért információkat.
   
   ![Űrlap létrehozása](./media/tutorial-design-database-using-portal/2-create-form.png)

    Beállítás | Ajánlott érték | Mező leírása 
    ---|---|---
    Kiszolgálónév | *egyedi kiszolgálónév* | Válasszon egy egyedi nevet, amely azonosítja az Azure Database for MariaDB-kiszolgálót. Ilyen név lehet például a **mydemoserver**. A rendszer hozzáfűzi a *.mariadb.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. A jelszó 3–63 karakterből állhat.
    Előfizetés | *az Ön előfizetése* | Válassza ki a kiszolgálóhoz használni kívánt Azure-előfizetést. Ha több előfizetéssel is rendelkezik, válassza ki azt az előfizetést, amelynek a keretében az erőforrásért fizet.
    Erőforráscsoport | **myresourcegroup** | Adjon meg egy új erőforráscsoport-nevet, vagy válasszon egy meglévő erőforráscsoportot.
    Forrás kiválasztása | **Üres** | Válassza az **Üres** lehetőséget egy új kiszolgáló létrehozásához. (Ha egy meglévő Azure Database for MariaDB-kiszolgáló georedundáns biztonsági mentéséből hoz létre kiszolgálót, válassza a **Biztonsági mentés** lehetőséget).
    Kiszolgáló-rendszergazdai bejelentkezés | **myadmin** | A kiszolgálóhoz való csatlakozáshoz használni kívánt bejelentkezési fiók. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
    Jelszó | *tetszés szerinti* | Adjon meg a kiszolgálói rendszergazdai fiókhoz egy új jelszót. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: Angol nagybetűs betűket, angol kisbetűs karakterek, számjegyek (0 – 9) és nem alfanumerikus karakterek (!, $, #, %, és így tovább).
    Jelszó megerősítése | *tetszés szerinti*| Erősítse meg a rendszergazdafiók jelszavát.
    Földrajzi egység | *a felhasználókhoz legközelebb eső régió*| Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet.
    Verzió | *a legújabb verzió*| A legújabb verzió (ha nincsenek olyan egyedi igényei, amelyek miatt egy másikat kell kiválasztania).
    Tarifacsomag | Lásd a leírást. | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **Tarifacsomag** > **Általános célú** lehetőséget. A következő beállításoknál használja az alapértelmezett értékeket:<br><ul><li>**Számítási generáció** (Gen 5)</li><li>**virtuális mag** (4 mag)</li><li>**Tárolási** (100 GB)</li><li>**Biztonsági másolatok megőrzési időszaka** (7 nap)</li></ul><br>A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Georedundáns** lehetőséget a **Biztonsági másolat redundanciabeállításai** területen. <br><br>A tarifacsomag beállításának mentéséhez válassza az **OK** gombot. A következő képernyőkép ezeket a beállításokat tartalmazza.
    
   ![Tarifacsomag](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Kattintson a **Létrehozás** gombra. Egy-két percen belül már fut a felhőben egy új Azure Database for MariaDB-kiszolgáló. Az eszköztáron válassza az **Értesítések** elemet az üzembehelyezési folyamat nyomon követéséhez.

## <a name="configure-the-firewall"></a>A tűzfal konfigurálása

Az Azure Database for MariaDB-t egy tűzfal védi. A rendszer alapértelmezés szerint elutasítja a kiszolgálóra és a kiszolgálón lévő adatbázisokra irányuló összes kapcsolatot. Mielőtt először csatlakozna az Azure Database for MariaDB-hez, konfigurálja a tűzfalat úgy, hogy adja meg az ügyfél gépének nyilvános hálózati IP-címét (vagy IP-címtartományát).

1. Válassza ki az újonnan létrehozott kiszolgálót, majd a **Kapcsolatbiztonság** elemet.
   
   ![Kapcsolatbiztonság](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. **Saját IP-címet is megadhat**, vagy konfigurálhatja a tűzfalszabályokat. Ne felejtsen a **Mentés** gombra kattintani, miután létrehozta a szabályokat.

Most már csatlakozhat a mysql parancssori eszközzel vagy a MySQL Workbench eszközzel a kiszolgálóhoz.

> [!TIP]
> Az Azure Database for MariaDB-kiszolgáló a 3306-os porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat a 3306-os porton keresztül. Ebben az esetben az Azure Database for MariaDB-kiszolgálóhoz való csatlakozáshoz az informatikai részlegnek meg kell nyitnia a 3306-os portot.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Kérje le az Azure Database for MariaDB-kiszolgáló teljes **kiszolgálónevét** és **kiszolgáló-rendszergazdai bejelentkezési nevét** az Azure Portalon. A teljes kiszolgálónévvel csatlakozhat a kiszolgálóhoz a mysql parancssori eszközben. 

1. Az [Azure Portal](https://portal.azure.com/) bal oldali menüjében válassza a **Minden erőforrás** elemet. Adja meg a kiszolgáló nevét, és keresse meg Azure Database for MariaDB-kiszolgálóját. Válassza ki a kiszolgáló nevét a kiszolgálóadatok megtekintéséhez.

2. Az **Áttekintés** lapon jegyezze fel a **kiszolgálónevet** és a **kiszolgáló-rendszergazdai bejelentkezési nevet**. A mezők melletti **másolás** gombra kattintva a vágólapra is kimásolhatja az értékeket.

   ![Kiszolgáló tulajdonságai](./media/tutorial-design-database-using-portal/2-server-properties.png)

Ebben a példában a kiszolgáló neve, **mydemoserver.mariadb.database.azure.com** a kiszolgáló rendszergazdai bejelentkezési név pedig **myadmin\@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával

A [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) építsen ki egy kapcsolatot az Azure Database for MariaDB-kiszolgálóhoz. A mysql parancssori eszköz a böngészőben az Azure Cloud Shell-lel, a saját számítógépén pedig a helyileg telepített mysql-eszközökkel futtatható. Az Azure Cloud Shell megnyitásához válassza a **Próbálja ki** gombot ennek a cikknek valamelyik kódblokkjában, vagy lépjen az Azure Portalra, és kattintson a **>_** ikonra a jobb felső eszköztárban. 

A csatlakozáshoz írja be a következő parancsot:

```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist

Amikor csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amellyel dolgozhat:

```sql
CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot, hogy átállítsa a kapcsolatot az újonnan létrehozott adatbázisra:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban

Most, hogy tudja, hogyan csatlakozhat az Azure Database for MariaDB-adatbázishoz, elvégezhet néhány alapvető feladatot.

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzon létre egy táblát leltáradatok tárolásához:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Adatok betöltése a táblákba

Most, hogy van egy táblája, szúrjon be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése

Futtassa a következő lekérdezést az adatbázistáblában lévő információk lekéréséhez:

```sql
SELECT * FROM inventory;
```

A táblákban lévő adatokat frissítheti is:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor az adatok lekérésekor frissül:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra

Tegyük fel, hogy véletlenül törölt egy fontos adatbázistáblát, és nem tudja könnyen helyreállítani az adatokat. Az Azure Database for MariaDB lehetővé teszi a kiszolgáló visszaállítását egy adott időpontra. Ehhez egy új kiszolgálón létrehozza az adatbázisok másolatát. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a mintakiszolgálót a tábla hozzáadása előtti időpontra állítják vissza:

1. Az Azure Portalon keresse meg az Azure Database for MariaDB-adatbázist. Az **áttekintő** oldalon válassza a **Visszaállítás** elemet.

   ![Adatbázis helyreállítása](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. A **Visszaállítás** lapon adja meg vagy válassza ki a következő információkat:
   
   ![Visszaállítás űrlap](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Visszaállítási pont**: Válasszon ki egy pontot szeretné visszaállítani, a felsorolt időkereten időben. Ne felejtse el UTC időre átalakítani a helyi időzóna szerinti időt.
   - **Visszaállítás új kiszolgálóra**: Adjon meg egy új kiszolgálónevet való visszaállításához.
   - **Hely**: A régió, ugyanaz, mint a forráskiszolgálón, és nem módosítható.
   - **A tarifacsomag**: A tarifacsomag megegyezik a forráskiszolgálóéval, és nem módosítható.
   
3. Az **OK** gomb kiválasztásával [állítsa vissza a kiszolgálót egy időpontra](./howto-restore-server-portal.md), amely megelőzi a tábla törlését. A kiszolgáló visszaállítása egy olyan új másolatot hoz létre a kiszolgálóról, amely a megadott időpontban aktuális állapotát tükrözi. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a következők elvégzését sajátította el az Azure Portallal:

> [!div class="checklist"]
> * Azure Database for MariaDB létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a mysql parancssori eszközzel
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
> [Alkalmazások csatlakoztatása az Azure Database for MariaDB-hez](./howto-connection-string.md)
