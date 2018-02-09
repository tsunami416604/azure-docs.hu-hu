---
title: "Az első Azure Database for MySQL-adatbázis megtervezése – Azure Portal | Microsoft Docs"
description: "Ez az oktatóanyag azt ismerteti, hogyan hozható létre és kezelhető az Azure Database for MySQL-kiszolgáló és -adatbázis az Azure Portallal."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: tutorial
ms.date: 11/03/2017
ms.custom: mvc
ms.openlocfilehash: 76cccf9e2ce0a1e59b43646c43ac165d46dade4a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Az első Azure Database for MySQL-adatbázis megtervezése
A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure Portallal könnyedén kezelheti a kiszolgálót és tervezhet adatbázist.

Ebből az oktatóanyagból az Azure Portal használatával megtanulhatja a következőket:

> [!div class="checklist"]
> * Azure Database for MySQL létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a mysql parancssori eszközzel
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a kedvenc webböngészőjét, és keresse fel a [Microsoft Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
A MySQL-kiszolgálóhoz készült Azure-adatbázis [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) belül jön létre.

1. Válassza az **Adatbázisok** > **Azure Database for MySQL** lehetőséget. Ha nem találja a MySQL Servert az **Adatbázisok** kategóriában, kattintson az **Összes megjelenítése** elemre az összes elérhető adatbázis-szolgáltatás megjelenítéséhez. A keresőmezőbe be is írhatja az **Azure Database for MySQL** szöveget, hogy gyorsan megtalálja a szolgáltatást.
![2-1 Ugrás a MySQL-re](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. Kattintson az **Azure Database for MySQL** csempére, majd a **Létrehozás** elemre.

Ebben a példában töltse ki az Azure Database for MySQL-űrlapot a következő információkkal:

| **Beállítás** | **Ajánlott érték** | **Mező leírása** |
|---|---|---|
| *Kiszolgálónév* | myserver4demo  | A kiszolgálónévnek globálisan egyedinek kell lennie. |
| *Előfizetés* | mysubscription | Válassza ki előfizetését a legördülő listából. |
| *Erőforráscsoport* | myResourceGroup | Hozzon létre erőforráscsoportot, vagy használjon egy meglévőt. |
| *Kiszolgálói rendszergazdai bejelentkezés* | myadmin | Állítsa be a rendszergazdai fiók nevét. |
| *Jelszó* |  | Állítson be erős jelszót a rendszergazdafiókhoz. |
| *Jelszó megerősítése* |  | Erősítse meg a rendszergazdafiók jelszavát. |
| *Hely* |  | Válasszon a rendelkezésre álló régiók közül. |
| *Verzió* | 5.7 | Válassza ki a legújabb verziót. |
| *Teljesítmény konfigurálása* | Alapszintű, 50 számítási egység, 50 GB  | Válassza ki a **Tarifacsomagot**, a **Számítási egységek** számát, a GB-ban mért **Tárterületet** , majd kattintson az **OK** gombra. |
| *Rögzítés az irányítópulton* | Jelölőnégyzet | Ajánlott bejelölni a jelölőnégyzetet, hogy a kiszolgáló később könnyen megtalálható legyen |
Ezt követően kattintson a **Create** (Létrehozás) gombra. Egy-két percen belül már fut a felhőben egy új, a MySQL-hez készült Azure Database. Ha monitorozni kívánja az üzembe helyezés folyamatát, kattintson az **Értesítések** gombra az eszköztáron.

## <a name="configure-firewall"></a>Tűzfal konfigurálása
Az Azure Database for MySQL-adatbázisokat tűzfal védi. A rendszer alapértelmezés szerint elutasítja a kiszolgálóra és a kiszolgálón lévő adatbázisokra irányuló összes kapcsolatot. Mielőtt először csatlakozna az Azure Database for MySQL-hez, konfigurálja a tűzfalat, és adja hozzá az ügyfél gépének nyilvános hálózati IP-címét (vagy IP-címtartományát).

1. Kattintson az újonnan létrehozott kiszolgálóra, majd a **Kapcsolatbiztonság** elemre.
   ![3-1 Kapcsolatbiztonság](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. Itt **hozzáadhat saját IP-címet**, vagy tűzfalszabályokat konfigurálhat. Ne felejtsen a **Mentés** gombra kattintani, miután létrehozta a szabályokat.
Most a mysql parancssori eszközzel vagy a MySQL Workbench GUI eszközzel csatlakozhat a kiszolgálóhoz.

> [!TIP]
> Az Azure Database for MySQL-kiszolgáló a 3306-os porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat a 3306-os porton keresztül. Ebben az esetben csak akkor tud csatlakozni az Azure MySQL kiszolgálóhoz, ha az informatikai részleg megnyitja a 3306-as portot.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le az Azure Database for MySQL-kiszolgáló teljes **kiszolgálónevét** és **kiszolgáló-rendszergazdai bejelentkezési nevét** az Azure Portalon. A teljes kiszolgálónévvel csatlakozhat a kiszolgálóhoz a mysql parancssori eszköz használatával. 

1. Az [Azure Portalon](https://portal.azure.com/) kattintson a **Minden erőforrás** elemre a bal oldali menüben, és keresse meg az Azure Database for MySQL-kiszolgálót. Válassza ki a kiszolgáló nevét a részletek megtekintéséhez.

2. A Beállítások fejléc alatt kattintson a **Tulajdonságok** elemre. Jegyezze fel a **KISZOLGÁLÓNÉV** és a **KISZOLGÁLÓ-RENDSZERGAZDAI BEJELENTKEZÉSI NÉV** értékét. A mezők melletti másolás gombra kattintva a vágólapra másolhatja az értékeket.
   ![4-2 Kiszolgáló tulajdonságai](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

Ebben a példában a kiszolgáló neve *myserver4demo.mysql.database.azure.com*, a kiszolgáló-rendszergazdai bejelentkezési név pedig *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
A MySQL-hez készült Azure Database-kiszolgálóhoz a [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) használatával kapcsolódhat. A mysql parancssori eszköz a böngészőben az Azure Cloud Shell használatával, a saját számítógépen pedig a helyileg telepített mysql eszközök használatával futtatható. Az Azure Cloud Shell indításához kattintson a `Try It` gombra ennek a cikknek valamelyik kódblokkjában, vagy kattintson az Azure Portal jobb felső eszköztárának `>_` ikonjára. 

Gépelje be a parancsot a csatlakozáshoz:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
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
Most, hogy tudja, hogyan csatlakozhat az Azure Database for MySQL-adatbázishoz, elvégezhet néhány alapvető feladatot:

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
Tegyük fel, hogy véletlenül törölt egy fontos adatbázistáblát, és nem tudja könnyen helyreállítani az adatokat. Az Azure Database for MySQL lehetővé teszi a kiszolgáló visszaállítását egy időpontra, és egy új kiszolgálón létrehozza az adatbázisok másolatát. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a mintakiszolgálót a tábla hozzáadása előtti időpontra állítják vissza.

1. Az Azure Portalon keresse meg az Azure Database for MySQL-adatbázist. Az **Áttekintés** oldalon kattintson az eszköztár **Visszaállítás** gombjára. Megnyílik a Visszaállítás oldal.

   ![10-1 Adatbázis visszaállítása](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. Töltse ki a **Visszaállítás** űrlapot a szükséges információkkal.
   
   ![10-2 Visszaállítás űrlap](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **Visszaállítási pont**: A listában szereplő időkereten belül válasszon ki egy időpontot, amelyre vissza kívánja állítani az adatbázist. Ne felejtse el UTC időre átalakítani a helyi időzóna szerinti időt.
   - **Visszaállítás új kiszolgálóra**: Adja meg egy új kiszolgáló nevét, amelyre vissza kívánja állítani az adatbázist.
   - **Hely**: A régió megegyezik a forráskiszolgálóéval, és nem módosítható.
   - **Tarifacsomag**: A tarifacsomag megegyezik a forráskiszolgálóéval, és nem módosítható.
   
3. Az **OK** gombra kattintva állítsa vissza a kiszolgálót a tábla törlése előtti [időpontra](./howto-restore-server-portal.md). A kiszolgáló visszaállítása létrehoz egy új másolatot a kiszolgálóról a megadott időpontban aktuális állapotában. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban a következők elvégzését sajátította el az Azure Portallal:

> [!div class="checklist"]
> * Azure Database for MySQL létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a mysql parancssori eszközzel
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
> [Alkalmazások csatlakoztatása az Azure Database for MySQL-adatbázishoz](./howto-connection-string.md)
