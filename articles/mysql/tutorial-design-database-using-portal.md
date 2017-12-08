---
title: "MySQL-adatbázis - Azure-portálon az első Azure Database kialakítása |} Microsoft Docs"
description: "Ez az oktatóanyag azt ismerteti, hogyan létrehozása és kezelése az Azure-adatbázis a MySQL-kiszolgáló és az Azure portál használatával adatbázis."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: tutorial
ms.date: 11/03/2017
ms.custom: mvc
ms.openlocfilehash: a7f38484e000b05a57cad9bc95abb255414d0162
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Az első Azure-adatbázis MySQL-adatbázis megtervezése
A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure portál használatával egyszerűen kezelheti a kiszolgálót, és egy adatbázis tervezése.

Ebben az oktatóanyagban, használja az Azure-portálon megtudhatja, hogyan:

> [!div class="checklist"]
> * A MySQL az Azure-adatbázis létrehozása
> * A kiszolgáló tűzfal konfigurálása
> * Hozzon létre egy adatbázist a mysql parancssori eszköz segítségével
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a kedvenc webböngészőt, és látogasson el a [Microsoft Azure-portálon](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
A MySQL-kiszolgálóhoz készült Azure-adatbázis [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) belül jön létre.

1. Navigáljon a **adatbázisok** > **Azure MySQL-adatbázis**. Ha nem találja a MySQL-kiszolgáló **adatbázisok** kategória, kattintson a **láthatja az összes** az összes adatbázis elérhető szolgáltatások megjelenítése. Is beírhat **MySQL az Azure-adatbázis** gyorsan megtalálhatja a szolgáltatás a Keresés mezőbe.
![2-1 navigáljon a MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. Kattintson a **MySQL az Azure-adatbázis** csempére, majd **létrehozása**.

Ebben a példában töltse ki az Azure-adatbázishoz MySQL űrlap a következő információkat:

| **Beállítás** | **Ajánlott érték** | **Mező leírása** |
|---|---|---|
| *Kiszolgálónév* | myserver4demo  | A kiszolgálónévnek globálisan egyedinek kell lennie. |
| *Előfizetés* | mysubscription | Válassza ki előfizetését a legördülő listából. |
| *Erőforráscsoport* | myResourceGroup | Hozzon létre erőforráscsoportot, vagy használjon egy meglévőt. |
| *Kiszolgálói rendszergazdai bejelentkezés* | myadmin | A telepítő a rendszergazdai fiók nevét. |
| *Jelszó* |  | Állítson be erős jelszót a rendszergazdafiókhoz. |
| *Jelszó megerősítése* |  | Erősítse meg a rendszergazdafiók jelszavát. |
| *Hely* |  | Válasszon a rendelkezésre álló régiók közül. |
| *Verzió* | 5.7 | Válassza ki a legújabb verziót. |
| *Teljesítmény konfigurálása* | Alapszintű, 50 számítási egység, 50 GB  | Válassza ki a **Tarifacsomagot**, a **Számítási egységek** számát, a GB-ban mért **Tárterületet** , majd kattintson az **OK** gombra. |
| *Rögzítés az irányítópulton* | Jelölőnégyzet | Ajánlott bejelölni a jelölőnégyzetet, hogy a kiszolgáló később könnyen megtalálható legyen |
Ezt követően kattintson a **Create** (Létrehozás) gombra. Egy-két percen belül már fut a felhőben egy új, a MySQL-hez készült Azure Database. Kattinthat **értesítések** gombra az eszköztáron a telepítési folyamat figyelését.

## <a name="configure-firewall"></a>Tűzfal konfigurálása
Az Azure-adatbázis MySQL adatbázis tűzfal védi meg. Alapértelmezés szerint az összes kapcsolat a kiszolgáló és az adatbázisok belül a kiszolgáló visszautasítja. Azure-adatbázishoz szeretne csatlakozni a MySQL először, mielőtt konfigurálja a tűzfalat, hogy az ügyfélszámítógép nyilvános hálózat IP-cím (vagy IP-címtartomány) hozzáadása.

1. Kattintson az újonnan létrehozott kiszolgáló, majd a **kapcsolatbiztonsági**.
   ![3-1 kapcsolatbiztonsági](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. Is **hozzáadása a saját IP**, vagy itt a tűzfalszabályok konfigurálása. Ne felejtsen a **Mentés** gombra kattintani, miután létrehozta a szabályokat.
A kiszolgáló mysql parancssori eszköz vagy MySQL munkaterület grafikus felhasználói felület használatával is csatlakozhat.

> [!TIP]
> MySQL-kiszolgálóhoz tartozó Azure-adatbázis 3306 porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat a 3306-os porton keresztül. Ha igen, akkor Azure-beli MySQL-kiszolgáló nem csatlakozik, kivéve, ha az IT-részleg 3306 portot nyit meg.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
A teljesen minősített beolvasása **kiszolgálónév** és **kiszolgálói rendszergazda bejelentkezési név** MySQL-kiszolgáló Azure-portálról az Azure-adatbázis. A teljes kiszolgálónév használatával kapcsolódni a kiszolgálóhoz, mysql parancssori eszközzel. 

1. A [Azure-portálon](https://portal.azure.com/), kattintson a **összes erőforrás** a bal oldali menüben írja be a nevét, majd keresse meg a MySQL-kiszolgálóhoz tartozó Azure-adatbázis. Válassza ki a kiszolgáló nevét, a részletek megtekintéséhez.

2. A beállítások alatt elemcsoportban kattintson **tulajdonságok**. Jegyezze fel **kiszolgálónév** és **KISZOLGÁLÓI rendszergazda bejelentkezési név**. Előfordulhat, hogy kattint mellett minden mező másolása a vágólapra Másolás gombra.
   ![4-2 kiszolgáló tulajdonságai](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

Ebben a példában a kiszolgáló neve *myserver4demo.mysql.database.azure.com*, a kiszolgáló-rendszergazdai bejelentkezési név pedig *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozzon a kiszolgálóhoz, a mysql használata
A MySQL-hez készült Azure Database-kiszolgálóhoz a [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) használatával kapcsolódhat. A mysql parancssori eszköz a böngészőben az Azure felhőalapú rendszerhéj vagy a saját gépről, mysql-eszközök telepítve helyileg segítségével futtathatja. Indítsa el az Azure-felhő rendszerhéjat, kattintson a `Try It` ebben a cikkben egy kódblokk gombra, vagy keresse fel az Azure-portálon, és kattintson a `>_` jobb felső eszköztárán ikonra. 

Gépelje be a parancsot a csatlakozáshoz:
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Amennyiben az a kiszolgáló csatlakozik, dolgozhat üres adatbázis létrehozása.
```sql
CREATE DATABASE mysampledb;
```

A parancssorba a következő parancs futtatásával váltson kapcsolat az újonnan létrehozott adatbázis futtatásához:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Hozzon létre táblák az adatbázisban
Most, hogy tudja, hogyan MySQL-adatbázis az Azure-adatbázishoz való kapcsolódáshoz, néhány alapvető műveleteket hajtható végre:

Először hozzon létre egy táblát, és töltse be az adatokat. Hozzon létre egy tábla tárolja a leltáradatokat.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy a tábla, néhány adat beviteléhez bele. A parancssor megnyitása ablakban futtassa a következő lekérdezés szúrható be néhány sornyi adatot.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most már rendelkezik mintaadatok a táblázatba a korábban létrehozott két sorát.

## <a name="query-and-update-the-data-in-the-tables"></a>Lekérdezés, és frissítse a táblák adatait
A következő lekérdezés futtatásával adatok lekérését a adatbázistábla hajtható végre.
```sql
SELECT * FROM inventory;
```

A tábla adatait is frissítheti.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor ennek megfelelően frissül, az adatok.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, egy fontos adatbázistábla véletlenül törölt, és könnyen nem állítható helyre az adatokat. Azure MySQL-adatbázis segítségével visszaállítása a kiszolgáló egy időben, az adatbázis másolatának létrehozása az új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépéseket a a minta kiszolgáló pontra visszaállításához, mielőtt a tábla hozzá lett adva.

1. Az Azure portálon keresse meg a MySQL az Azure-adatbázis. Az a **áttekintése** kattintson **visszaállítása** az eszköztáron. A Helyreállítás lap nyílik meg.

   ![10-1 visszaállítási adatbázis](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. Töltse ki a **visszaállítása** a szükséges információt a képernyőn.
   
   ![10-2 visszaállítási képernyő](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **Visszaállítási pont**: válassza ki a-időpontban, amelyet szeretne visszaállítani, a felsorolt időkereten belül. Győződjön meg arról, hogy a helyi időzóna átalakítása UTC.
   - **Új kiszolgáló visszaállításához**: Adjon meg egy új, a visszaállítani kívánt kiszolgálónevet.
   - **Hely**: A régióban legyen, mint a forráskiszolgálón, és nem módosítható.
   - **IP-címek**: Ez a tarifacsomag ugyanaz, mint a forráskiszolgálón, és nem módosítható.
   
3. Kattintson a **OK** visszaállítása a kiszolgáló [egy időben visszaállítása](./howto-restore-server-portal.md) előtt a tábla törölve lett. A kiszolgáló visszaállítása hoz létre egy új példányát a kiszolgálón, az időpont, adjon meg érvényes. 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban az Azure portál, az ismert hogyan használja:

> [!div class="checklist"]
> * A MySQL az Azure-adatbázis létrehozása
> * A kiszolgáló tűzfal konfigurálása
> * Hozzon létre egy adatbázist a mysql parancssori eszköz segítségével
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
> [A MySQL az Azure Database-alkalmazások összekapcsolása](./howto-connection-string.md)
