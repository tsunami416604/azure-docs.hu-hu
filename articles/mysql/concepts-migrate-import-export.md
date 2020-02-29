---
title: Importálás és exportálás – Azure Database for MySQL
description: Ez a cikk az adatbázisok Azure Database for MySQL-ben történő importálásának és exportálásának általános módszereit ismerteti a MySQL Workbench eszközzel.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163726"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>A MySQL-adatbázis migrálása Importálás és exportálás használatával
Ez a cikk két gyakori megközelítést ismertet az adatAzure Database for MySQL-kiszolgálóra való importálásához és exportálásához a MySQL Workbench használatával. 

## <a name="before-you-begin"></a>Előkészületek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy Azure Database for MySQL-kiszolgáló, [Azure Database for MySQL-kiszolgáló Azure Portal használatával történő létrehozása](quickstart-create-mysql-server-database-using-azure-portal.md)után.
- MySQL Workbench [MySQL Workbench Letöltés](https://dev.mysql.com/downloads/workbench/) vagy más, harmadik féltől származó MySQL-eszköz az importáláshoz/exportáláshoz.

## <a name="use-common-tools"></a>Gyakori eszközök használata
Az olyan gyakori segédprogramok és eszközök, mint például a MySQL Workbench vagy a mysqldump, távolról csatlakozhatnak és importálhatók vagy exportálhatók az Azure Database for MySQLba. 

Az ügyfélgépen lévő eszközök használatával internetkapcsolattal csatlakozhat Azure Database for MySQLhoz. Használjon SSL-titkosítású kapcsolatot az ajánlott biztonsági eljárásokhoz az SSL- [kapcsolat konfigurálása Azure Database for MySQLban](concepts-ssl-connection-security.md)című témakörben leírtak szerint.

A Azure Database for MySQLba való Migrálás során nem kell áthelyeznie az importálási és exportálási fájlokat a Felhőbeli helyre. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Adatbázis létrehozása a Azure Database for MySQL-kiszolgálón
Hozzon létre egy üres adatbázist azon a Azure Database for MySQL-kiszolgálón, amelyen át szeretné telepíteni az adatátvitelt. Az adatbázis létrehozásához használjon például a MySQL Workbench, a Varangy vagy a Navicat eszközt. Az adatbázis neve megegyezik a dömpingelt adattartalommal, vagy létrehozhat egy másik nevet tartalmazó adatbázist is.

A csatlakozáshoz keresse meg a kapcsolati adatokat a Azure Database for MySQL **áttekintésében** .

![A Azure Portal található kapcsolatok adatainak megkeresése](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Adja hozzá a kapcsolódási adatokat a MySQL Workbenchhez.

![MySQL Workbench-beli kapcsolatok karakterlánca](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Annak meghatározása, hogy mikor használjon importálási és exportálási technikákat a memóriakép és a visszaállítás helyett
A MySQL Tools használatával adatbázisok importálhatók és exportálhatók az Azure MySQL-adatbázisba az alábbi esetekben. Más helyzetekben a [memóriakép és a visszaállítás](concepts-migrate-dump-restore.md) megközelítés használata is hasznos lehet. 

- Ha szelektíven kell választania néhány táblázatot egy meglévő MySQL-adatbázisból az Azure MySQL-adatbázisba való importáláshoz, ajánlott az importálási és exportálási módszer használata.  Ezzel kihagyhatja az áttelepítés felesleges tábláit, így időt és erőforrásokat takaríthat meg. Használja például a `--include-tables` vagy `--exclude-tables` kapcsolót a [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) és a `--tables` kapcsolót a [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Ha a táblázatokon kívül más adatbázis-objektumokat helyez át, explicit módon hozza létre ezeket az objektumokat. Belefoglalja a korlátozásokat (elsődleges kulcs, idegen kulcs, indexek), nézeteket, függvényeket, eljárásokat, eseményindítókat és bármely más, az áttelepíteni kívánt adatbázis-objektumot.
- Ha egy MySQL-adatbázistól eltérő külső adatforrásokból végez áttelepítést, hozzon létre lapos fájlokat, és importálja őket a [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)használatával.

Győződjön meg arról, hogy az adatbázisban lévő összes tábla a InnoDB Storage motort használja, ha az adatAzure Database for MySQLba tölti be az adatgyűjtést. A Azure Database for MySQL csak a InnoDB tároló motort támogatja, így nem támogatja az alternatív tárolóeszközöket. Ha a táblák alternatív tárolókat igényelnek, ügyeljen arra, hogy a InnoDB motor formátumát használja a Azure Database for MySQLba való áttelepítés előtt. 

Ha például van egy WordPress vagy egy webalkalmazás, amely a MyISAM motort használja, először alakítsa át a táblákat az InnoDB-táblákba való áttelepítéssel. Ezután állítsa vissza Azure Database for MySQL. A `ENGINE=INNODB` záradék használatával állítsa be a motort egy tábla létrehozásához, majd az áttelepítés előtt vigye át az adatok átvitelét a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Teljesítményre vonatkozó javaslatok importáláshoz és exportáláshoz
-   Fürtözött indexeket és elsődleges kulcsokat hozhat létre az adatbetöltése előtt. Az elsődleges kulcs sorrendjében tölti be az adatterhelést. 
-   A másodlagos indexek létrehozásának késleltetése, amíg az betöltés be nem fejeződik. Az összes másodlagos index létrehozása a betöltés után. 
-   A betöltés előtt tiltsa le a külső kulcsok korlátozásait. A külső kulcsok ellenőrzésének letiltása jelentős teljesítményt nyújt. Engedélyezze a korlátozásokat, és ellenőrizze az adatok betöltését a hivatkozási integritás biztosítása érdekében.
-   Párhuzamosan tölthetők be az adathalmazok. Kerülje a túl sok párhuzamosságot, amelynek hatására elérheti az erőforrás-korlátot, és figyelheti az erőforrásokat a Azure Portal elérhető metrikák használatával. 
-   Szükség esetén particionált táblákat használjon.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importálás és exportálás a MySQL Workbench használatával
A MySQL Workbenchben kétféleképpen exportálhat és importálhat adatfájlokat. Mindegyik más célt szolgál. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Táblázatos adatok exportálása és importálása varázsló az objektum böngésző helyi menüjéből
![MySQL Workbench varázslók az objektum böngésző helyi menüjében](./media/concepts-migrate-import-export/p1.png)

A táblázatos adatokhoz tartozó varázslók a CSV-és JSON-fájlok használatával támogatják az importálási és exportálási műveleteket. Több konfigurációs lehetőséget is tartalmaznak, például elválasztókat, oszlopok kijelölését és a kódolás kijelölését. Az egyes varázslókat helyi vagy távolról csatlakoztatott MySQL-kiszolgálókon is elvégezheti. Az importálási művelet táblázat-, oszlop-és típus-hozzárendelést tartalmaz. 

Ezek a varázslók az objektum böngésző helyi menüjéből érhetők el, ha a jobb gombbal a táblára kattint. Ezután válassza a **tábla adatexportálás varázsló** vagy a **tábla adatimportálása varázslót**. 

#### <a name="table-data-export-wizard"></a>Tábla adatexportálási varázslója
Az alábbi példa egy CSV-fájlba exportálja a táblázatot: 
1. Kattintson a jobb gombbal az exportálni kívánt adatbázis táblára. 
2. Válassza a **tábla adatexportálás varázslót**. Válassza ki az exportálandó oszlopokat, a sorok eltolását (ha van), és a darabszámot (ha van ilyen). 
3. Az **exportálni kívánt adatexportálás** lapon kattintson a **tovább**gombra. Válassza ki a fájl elérési útját, CSV-fájlját vagy JSON-fájltípusát. Válassza ki a vonal elválasztóját, a karakterláncok befoglalásának metódusát és a mező elválasztóját is. 
4. A **kimeneti fájl helyének kiválasztása** lapon kattintson a **tovább**gombra. 
5. Az **adatexportálás** lapon kattintson a **tovább**gombra.

#### <a name="table-data-import-wizard"></a>Tábla adatimportálása varázsló
Az alábbi példa egy CSV-fájlból importálja a táblázatot:
1. Kattintson a jobb gombbal az importálandó adatbázis táblára. 
2. Keresse meg és válassza ki az importálni kívánt CSV-fájlt, majd kattintson a **tovább**gombra. 
3. Válassza ki a céltábla (új vagy meglévő) elemet, és jelölje be a **tábla csonkítása az importálás előtt** jelölőnégyzetet, vagy törölje a jelölést. Kattintson a **Tovább** gombra.
4. Válassza ki a kódolás és az importálandó oszlopok elemet, majd kattintson a **tovább**gombra. 
5. Az **adatimportálás** lapon kattintson a **tovább**gombra. A varázsló ennek megfelelően importálja az adatmennyiséget.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL-adatok exportálási és importálási varázslók a navigátor ablaktáblán
A MySQL Workbenchből generált vagy a mysqldump parancsból generált SQL-alapú exportálás vagy Importálás varázsló használatával. Nyissa meg ezeket a varázslókat a **navigátor** ablaktáblán, vagy válassza a főmenü **kiszolgáló** elemét. Ezután válassza **Az adatexportálás** vagy **az adatimportálás**lehetőséget. 

#### <a name="data-export"></a>Adatexportálás
![MySQL Workbench adatexportálás a navigátor panel használatával](./media/concepts-migrate-import-export/p2.png)

Az **adatexportálás** lapon a MySQL-adatait is exportálhatja. 
1. Válassza ki az exportálni kívánt sémákat, szükség esetén válassza ki az egyes sémák egyes sémái objektumait/táblázatait, majd az exportálást. A konfigurációs beállítások közé tartozik az Exportálás egy Project mappába vagy egy önálló SQL-fájlba, a tárolt rutinok és események kiírása vagy a táblázat adatainak kihagyása. 
 
   Azt is megteheti, hogy egy **eredményhalmaz exportálásával** exportál egy adott EREDMÉNYHALMAZT az SQL-szerkesztőben más formátumba, például CSV, JSON, HTML és XML formátumban. 
3. Válassza ki az exportálandó adatbázis-objektumokat, majd konfigurálja a kapcsolódó beállításokat.
4. Az aktuális objektumok betöltéséhez kattintson a **frissítés** gombra.
5. Megnyithatja a **Speciális beállítások** lapot az exportálási művelet pontosításához. Tegyük fel például, hogy a tábla zárolások hozzáadása, az INSERT utasítás helyett a Replace, és a kezdő karaktereket tartalmazó idézőjelek szerepelnek.
6. Kattintson az **Exportálás megkezdése** elemre az exportálási folyamat megkezdéséhez.


#### <a name="data-import"></a>Adatimportálás
![MySQL Workbench adatimportálás a felügyeleti navigátor használatával](./media/concepts-migrate-import-export/p3.png)

Az **adatimportálás** lapon importálhatja vagy visszaállíthatja az exportált adatok adatait az adatexportálási műveletből vagy a mysqldump parancsból. 
1. Válassza ki a Project mappát vagy az önálló SQL-fájlt, válassza ki az importálni kívánt sémát, vagy válassza az **új** lehetőséget egy új séma definiálásához. 
2. Az importálási folyamat megkezdéséhez kattintson az **Importálás megkezdése** elemre.

## <a name="next-steps"></a>Következő lépések
- Egy másik áttelepítési módszerként olvassa el [a MySQL-adatbázis áttelepítése a dump használatával és a visszaállítás a Azure Database for MySQLban című részt](concepts-migrate-dump-restore.md).
- Az adatbázisok Azure Database for MySQLre való áttelepítésével kapcsolatos további információkért tekintse meg az [adatbázis-áttelepítési útmutatót](https://aka.ms/datamigration). 
