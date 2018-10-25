---
title: Importálása és exportálása az Azure Database for MySQL-hez
description: Ez a cikk ismerteti a gyakori módjai importálása és exportálása az adatbázisok az Azure Database for MySQL, eszközök, például a MySQL Workbench használatával.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: ee291f24a1ad77f84e7cdb8cf4c687af7dfa3f17
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986219"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>A MySQL-adatbázis áttelepítése importálása és exportálása
Ez a cikk leírja a két leggyakoribb módszer az adatok importálása és exportálása egy Azure Database for MySQL-kiszolgálóhoz a MySQL Workbench használatával. 

## <a name="before-you-begin"></a>Előkészületek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Egy Azure Database for MySQL-kiszolgáló, a következő [hozzon létre egy Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával](quickstart-create-mysql-server-database-using-azure-portal.md).
- A MySQL Workbench [letöltött](https://dev.mysql.com/downloads/workbench/), vagy egy másik MySQL eszköz importálása és exportálása.

## <a name="use-common-tools"></a>Általános eszközökkel
Általános eszközökkel, például a MySQL Workbench, varangy vagy Navicat használatával távolról csatlakozás és adatok importálása és exportálása az Azure Database MySQL-hez készült. 

Internetkapcsolattal rendelkező az ügyfélszámítógép az ilyen eszközök használatával csatlakozás az Azure Database for MySQL-hez. Egy SSL-titkosítású kapcsolat használata ajánlott biztonsági eljárások leírtak szerint [SSL-összekapcsolhatóság konfigurálása az Azure Database for MySQL-hez](concepts-ssl-connection-security.md).

Nem kell áthelyezni az importálás és különleges cloud bárhova fájljainak exportálását, amikor áttelepítése az Azure Database for MySQL-hez. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Adatbázis létrehozására az Azure Database for MySQL-kiszolgáló
Hozzon létre egy üres adatbázist a az Azure Database for MySQL-kiszolgálót, ahová az adatok áttelepítéséhez. Az adatbázis létrehozásához használja a megfelelő eszköz, például a MySQL Workbench, varangy vagy Navicat. Az adatbázis neve megegyezik a változásképek adatokat tartalmazó adatbázisban is van, vagy létrehozhat egy adatbázist egy másik névvel.

A csatlakozás, keresse meg a kapcsolati információkat a **áttekintése** , az Azure Database for MySQL-hez.

![Keresse meg a kapcsolati adatokat az Azure Portalon](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

A kapcsolati adatok hozzáadása a MySQL Workbench.

![A MySQL Workbench kapcsolati karakterlánc](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Hogy mikor importálása és exportálása helyett egy memóriakép technikák és visszaállítása
MySQL eszközeivel importálása és exportálása az adatbázisok az Azure MySQL Database-be a következő esetekben. Más esetekben, előfordulhat, hogy előnyei származhatnak a [memóriakép és visszaállítás](concepts-migrate-dump-restore.md) megközelítést helyette. 

- Kell külön-külön válassza pár táblák importálása egy meglévő MySQL-adatbázisból Azure MySQL-adatbázis, esetén, az importálás és exportálás módszer használata ajánlott.  Ezzel a módszerrel hagyja el minden felesleges táblákat az áttelepítést az időt és erőforrásokat takaríthat meg. Például a `--include-tables` vagy `--exclude-tables` felülettel [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) és a `--tables` felülettel [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- A táblázatokon kívül az adatbázis-objektumokat telepít át, ha explicit módon létre azokat az objektumokat. Például a megkötések (elsődleges kulcs, külső kulcs, indexek), nézetek, függvények, eljárások, eseményindítók és bármely más adatbázis-objektumok szeretne áttelepíteni.
- Való migráláshoz adatokat egy MySQL-adatbázis nem külső adatforrásokból származó, egybesimított fájlok létrehozása, és importálhatja őket a [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Győződjön meg arról, hogy az adatbázis minden táblájához InnoDB tárolási motort használják, amikor tölt be adatokat az Azure Database for MySQL-hez. Azure Database for MySQL támogatja a csak az InnoDB tárolási motorra, így nem támogatja a másodlagos tárolási motorok. Ha a táblák másodlagos tárolási motorok van szükség, mindenképpen alakíthatja át őket a InnoDB motor formátumba, Azure Database-adatbázishoz az áttelepítés előtt használata a MySQL-hez. 

Például ha egy WordPress vagy webes alkalmazás, amely a MyISAM-motort használja, először konvertálja a táblák InnoDB-táblákba az adatok áttelepítése. Majd állítsa vissza az Azure Database for MySQL-hez. Használja a záradékot `ENGINE=INNODB` az összetevő tábla létrehozása és majd átviszi az adatokat az áttelepítés előtt a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Teljesítménnyel kapcsolatos javaslat importálása és exportálása
-   Adatok betöltése előtt hozzon létre a fürtözött indexek és az elsődleges kulcsok. Elsődleges kulcs szerinti sorrendben adatokat betölteni. 
-   Késleltetés létrehozása a másodlagos indexek adatok után betöltődik. Az összes másodlagos indexek létrehozása után betöltésekor. 
-   Tiltsa le a külső kulcsra vonatkozó megkötések betöltés előtt. Idegen kulcs ellenőrzések letiltása a jelentős teljesítménynövekedést biztosít. A korlátozások engedélyezése és a terhelés annak biztosítása érdekében a hivatkozási integritás után ellenőrizze az adatokat.
-   Párhuzamos adatokat betölteni. Ne használjon túl sok párhuzamos végrehajtás, amely miatt a eléri a erőforrás korlátot, és erőforrások monitorozása az Azure Portalon elérhető metrikák használatával. 
-   Használja a particionált táblákat, ha szükséges.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importálás és exportálás a MySQL Workbench használatával
Kétféleképpen exportálhat és importálhat adatokat a MySQL Workbench. Minden más célt szolgál. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Táblák adatainak exportálása és importálása a varázslók az objektumtallózó helyi menüből
![A MySQL Workbench varázslók az objektumtallózó helyi menüben](./media/concepts-migrate-import-export/p1.png)

Táblaadatok szolgáló varázslókban támogatja az importálási és exportálási műveletet a fürt megosztott kötetei szolgáltatás és a JSON-fájlok használatával. Több konfigurációs beállítások, például az elválasztó, az oszlop kiválasztása és a kódolási kiválasztása tartalmazzák. Egyes varázslók helyi vagy távoli csatlakoztatott MySQL-kiszolgálók ellen is végezhet. Az importálási művelet magában foglalja a táblázatok, oszlopok és adattípus-leképezés. 

Ezekben a varázslókban a Objektumtallózó helyi menüben kattintson a jobb gombbal a tábla eléréséhez. Ezután válasszon **tábla adatainak exportálása varázsló** vagy **tábla adatok importálása varázsló**. 

#### <a name="table-data-export-wizard"></a>Adatok exportálása varázsló
Az alábbi példa a tábla exportálása CSV-fájlba: 
1. Kattintson a jobb gombbal a táblázat az adatbázis exportálásának. 
2. Válassza ki **tábla adatok exportálása varázsló**. Válassza ki az oszlopok exportálható, sor eltolását (ha van ilyen) és count (ha van). 
3. Az a **válassza ki az exportálandó adatok** kattintson **tovább**. Válassza ki a fájl elérési útját, CSV- vagy JSON-fájl típusa. A sor elválasztó, karakterláncok és mezőelválasztó a befoglaló módszert is választhatja. 
4. Az a **válassza ki a kimeneti fájl helye** kattintson **tovább**. 
5. Az a **adatok exportálása** kattintson **tovább**.

#### <a name="table-data-import-wizard"></a>Adatok importálása varázsló
Az alábbi példa a tábla importálja a CSV-fájlból:
1. Kattintson a jobb gombbal a táblázat az adatbázis importálni. 
2. Keresse meg és válassza ki az importálni, és kattintson a CSV-fájl **tovább**. 
3. Válassza ki a céloldali tábla (új vagy meglévő), és válassza ki, vagy törölje a jelet a **csonkolási tábla importálása előtt** jelölőnégyzetet. Kattintson a **Tovább** gombra.
4. SELECT kódolás és importálni, és kattintson a kívánt oszlopok **tovább**. 
5. Az a **adatimportálás** kattintson **tovább**. A varázsló importálja az adatokat annak megfelelően.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL-adatok exportálása és varázslók importálhat a navigációs panel
Egy varázsló segítségével exportálja vagy importálhat mysqldump parancsot a létrehozott vagy a MySQL Workbench segítségével létrehozott SQL. Ezekben a varázslókban, a hozzáférés a **kezelő** ablaktáblán vagy kiválasztásával **kiszolgáló** fő menüjéből. Válassza ki **adatexportálás** vagy **adatimportálás**. 

#### <a name="data-export"></a>Adatok exportálása
![A MySQL Workbench-adatok exportálása a navigációs panel használatával](./media/concepts-migrate-import-export/p2.png)

Használhatja a **adatexportálás** fülre, és a MySQL-adatok exportálása. 
1. Válassza ki, amelyet szeretne exportálni, igény szerint válasszon adott séma objektumok/táblák minden séma, és létrehozni az exportálás minden sémát. Konfigurációs lehetőségek közé tartozik egy projektmappába vagy az önálló SQL-fájl exportálása, dump tárolt eljárások és események, vagy hagyja ki a tábla adatait. 
 
   Másik megoldásként használhatja **exportálása egy eredményhalmazban** egy adott eredményhalmazt, az SQL-szerkesztő, egy másik formátumba, például a fürt megosztott kötetei szolgáltatás, JSON, HTML vagy XML exportálása. 
3. Válassza ki az adatbázis-objektumok exportálása, és a kapcsolódó beállításokat.
4. Kattintson a **frissítése** betölteni a jelenlegi objektumokat.
5. Szükség esetén nyissa meg a **speciális beállítások** , amellyel pontosíthatja az exportálási művelet lapot. Például adja hozzá a table zárolások, insert utasítások és ajánlat azonosítókat használni kívánt szintaxiskiemelést karakterrel helyett használja cseréje.
6. Kattintson a **Start exportálása** az exportálási folyamat megkezdéséhez.


#### <a name="data-import"></a>Adatok importálása
![MySQL Workbench-adatok importálása felügyeleti kezelő használatával](./media/concepts-migrate-import-export/p3.png)

Használhatja a **adatimportálási** fülre, és importálja vagy állítsa vissza az exportált adatok az exportálási művelet, illetve a mysqldump parancsot. 
1. A projektmappa fájllistájának vagy az önálló SQL-fájlt, és válassza a séma importálása, vagy válasszon **új** definiálásához egy új sémát. 
2. Kattintson a **Start importálása** az importálási folyamat elindításához.

## <a name="next-steps"></a>További lépések
- Egy másik migrálási megközelítés, mint olvasni [áttelepítése a MySQL database használatával dump, és állítsa vissza az Azure Database for MySQL-hez](concepts-migrate-dump-restore.md).
- További információ az adatbázisok az Azure Database for MySQL-hez, lásd: a [adatbázis-Migrálási útmutató](https://aka.ms/datamigration). 
