---
title: 'Oktatóanyag: Azure Database for MariaDB tervezése az Azure Portal használatával'
description: Ez az oktatóanyag azt ismerteti, hogyan hozható létre és kezelhető az Azure Database for MariaDB-kiszolgáló és -adatbázis az Azure Portallal.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: tutorial
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 2e1cd0d28c544b2e4c5dc86c7a6db39a5d20c1ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991011"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-using-the-azure-portal"></a>Oktatóanyag: Azure Database for MariaDB-adatbázis tervezése az Azure Portal használatával
Az Azure Database for MariaDB egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure Portallal könnyedén kezelheti a kiszolgálót és tervezhet adatbázist.

Ebből az oktatóanyagból az Azure Portal használatával megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Database for MariaDB létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a mysql parancssori eszközzel
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a kedvenc webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása
Az Azure Database for MariaDB-kiszolgáló számítási és tárolási erőforrások <!--[compute and storage resources](./concepts-compute-unit-and-storage.md)--> egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) belül jön létre.

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

2. A keresőmezőbe írja be az **Azure Database for MariaDB** szöveget, hogy megtalálja a szolgáltatást.
   
   ![Ugrás a MySQL-re](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Kattintson az **Azure Database for MariaDB** csempére, majd a **Létrehozás** elemre. Töltse ki az Azure Database for MariaDB űrlapot.
   
   ![Űrlap létrehozása](./media/tutorial-design-database-using-portal/2-create-form.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása** 
    ---|---|---
    Kiszolgálónév | Egyedi kiszolgálónév | Válasszon egy egyedi nevet, amely azonosítja az Azure Database for MariaDB-kiszolgálót. Például mydemoserver. A rendszer hozzáfűzi a *.mariadb.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
    Előfizetés | Az Ön előfizetése | Válassza ki a kiszolgálóhoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
    Erőforráscsoport | *myresourcegroup* | Adjon meg egy új vagy egy létező erőforráscsoportnevet.    Erőforráscsoport|*myresourcegroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
    Forrás kiválasztása | *Üres* | Válassza az *Üres* lehetőséget egy teljesen új kiszolgáló létrehozásához. (Ha egy meglévő Azure Database for MariaDB-kiszolgáló georedundáns biztonsági mentéséből hoz létre kiszolgálót, válassza a *Biztonsági mentés* lehetőséget).
    Kiszolgáló-rendszergazdai bejelentkezés | myadmin | A kiszolgálóhoz való csatlakozáshoz használni kívánt bejelentkezési fiók. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
    Jelszó | *A választása szerint* | Adjon meg új jelszót a kiszolgálói rendszergazdai fiók számára. 8–128 karakter hosszúságú lehet. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
    Jelszó megerősítése | *A választása szerint*| Erősítse meg a rendszergazdafiók jelszavát.
    Hely | *A felhasználókhoz legközelebb eső régió*| Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet.
    Verzió | *A legújabb verzió*| A legújabb verzió (ha nincsenek más verzió használatát megkövetelő egyedi igényei).
    Tarifacsomag | **Általános célú**, **5. generációs**, **2 virtuális mag**, **5 GB**, **7 nap**, **Georedundáns** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **Tarifacsomag** lehetőséget, majd az **Általános célú** lapot. A **Számítási generáció**, **Virtuális mag**, **Tárolás** és **Biztonsági mentések megőrzési időszaka** beállítások alapértelmezett értékei a következők: *5. generációs*, *2 virtuális mag*, *5 GB* és *7 nap*. A csúszkákat nem szükséges módosítania. A kiszolgáló georedundáns tárhelyre való biztonsági mentésének engedélyezéséhez válassza a **Biztonsági másolat redundanciabeállításai** területen a **Georedundáns** lehetőséget. A tarifacsomag beállításának mentéséhez válassza az **OK** gombot. A következő képernyőkép ezeket a beállításokat tartalmazza.
    
   ![Tarifacsomag](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Kattintson a **Create** (Létrehozás) gombra. Egy-két percen belül már fut a felhőben egy új Azure Database for MariaDB-kiszolgáló. Ha monitorozni kívánja az üzembe helyezés folyamatát, kattintson az **Értesítések** gombra az eszköztáron.

## <a name="configure-firewall"></a>Tűzfal konfigurálása
Az Azure Database for MariaDB-t egy tűzfal védi. A rendszer alapértelmezés szerint elutasítja a kiszolgálóra és a kiszolgálón lévő adatbázisokra irányuló összes kapcsolatot. Mielőtt először csatlakozna az Azure Database for MariaDB-hez, konfigurálja a tűzfalat, és adja hozzá az ügyfél gépének nyilvános hálózati IP-címét (vagy IP-címtartományát).

1. Kattintson az újonnan létrehozott kiszolgálóra, majd a **Kapcsolatbiztonság** elemre.
   
   ![Kapcsolatbiztonság](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Itt **hozzáadhat saját IP-címet**, vagy tűzfalszabályokat konfigurálhat. Ne felejtsen a **Mentés** gombra kattintani, miután létrehozta a szabályokat.
Most a mysql parancssori eszközzel vagy a MySQL Workbench GUI eszközzel csatlakozhat a kiszolgálóhoz.

> [!TIP]
> Az Azure Database for MariaDB-kiszolgáló a 3306-os porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat a 3306-os porton keresztül. Ebben az esetben nem tud csatlakozni az Azure Database for MariaDB-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 3306-os portot.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le az Azure Database for MariaDB-kiszolgáló teljes **kiszolgálónevét** és **kiszolgáló-rendszergazdai bejelentkezési nevét** az Azure Portalon. A teljes kiszolgálónévvel csatlakozhat a kiszolgálóhoz a mysql parancssori eszköz használatával. 

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Minden erőforrás** elemre a bal oldali menüben, és keresse meg az Azure Database for MariaDB-kiszolgálót. Válassza ki a kiszolgáló nevét a részletek megtekintéséhez.

2. Az **Áttekintés** oldalon jegyezze fel a **Kiszolgálónév** és a **Kiszolgálói rendszergazda bejelentkezési neve** értékét. A mezők melletti másolás gombra kattintva a vágólapra másolhatja az értékeket.
   ![4-2 Kiszolgáló tulajdonságai](./media/tutorial-design-database-using-portal/2-server-properties.png)

Ebben a példában a kiszolgáló neve *mydemoserver.mariadb.database.azure.com*, a kiszolgáló-rendszergazdai bejelentkezési név pedig *myadmin@mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
Az Azure Database for MariaDB-kiszolgálóhoz a [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) használatával kapcsolódhat. A mysql parancssori eszköz a böngészőben az Azure Cloud Shell használatával, a saját számítógépen pedig a helyileg telepített mysql eszközök használatával futtatható. Az Azure Cloud Shell indításához kattintson a `Try It` gombra ennek a cikknek valamelyik kódblokkjában, vagy kattintson az Azure Portal jobb felső eszköztárának `>_` ikonjára. 

Gépelje be a parancsot a csatlakozáshoz:
```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amellyel dolgozhat.
```sql
CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot a kapcsolat az újonnan létrehozott adatbázisra történő átállítására:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy tudja, hogyan csatlakozhat az Azure Database for MariaDB-adatbázishoz, elvégezhet néhány alapvető feladatot:

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzunk létre egy táblát leltáradatok tárolásához.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy már rendelkezünk egy táblával, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most két mintaadatsorral rendelkezünk a korábban létrehozott táblában.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést az adatbázistáblában lévő információk lekéréséhez.
```sql
SELECT * FROM inventory;
```

A táblákban lévő adatokat frissítheti is.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor az adatok lekérésekor megfelelően frissül.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül törölt egy fontos adatbázistáblát, és nem tudja könnyen helyreállítani az adatokat. Az Azure Database for MariaDB lehetővé teszi a kiszolgáló visszaállítását egy időpontra, és egy új kiszolgálón létrehozza az adatbázisok másolatát. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a mintakiszolgálót a tábla hozzáadása előtti időpontra állítják vissza.

1. Az Azure Portalon keresse meg az Azure Database for MariaDB-adatbázist. Az **Áttekintés** oldalon kattintson az eszköztár **Visszaállítás** gombjára. Megnyílik a Visszaállítás oldal.

   ![10-1 Adatbázis visszaállítása](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Töltse ki a **Visszaállítás** űrlapot a szükséges információkkal.
   
   ![10-2 Visszaállítás űrlap](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Visszaállítási pont**: A listában szereplő időkereten belül válasszon ki egy időpontot, amelyre vissza kívánja állítani az adatbázist. Ne felejtse el UTC időre átalakítani a helyi időzóna szerinti időt.
   - **Visszaállítás új kiszolgálóra**: Adja meg egy új kiszolgáló nevét, amelyre vissza kívánja állítani az adatbázist.
   - **Hely**: A régió megegyezik a forráskiszolgálóéval, és nem módosítható.
   - **Tarifacsomag**: A tarifacsomag megegyezik a forráskiszolgálóéval, és nem módosítható.
   
3. Az **OK** gombra kattintva állítsa vissza a kiszolgálót a tábla törlése előtti időpontra <!--[restore to a point in time](./howto-restore-server-portal.md)-->. A kiszolgáló visszaállítása létrehoz egy új másolatot a kiszolgálóról a megadott időpontban aktuális állapotában. 

<!--## Next steps
In this tutorial, you use the Azure portal to learned how to:

> [!div class="checklist"]
> * Create an Azure Database for MariaDB
> * Configure the server firewall
> * Use mysql command-line tool to create a database
> * Load sample data
> * Query data
> * Update data
> * Restore data

><> [!div class="nextstepaction"]
> [How to connect applications to Azure Database for MariaDB](./howto-connection-string.md)-->
