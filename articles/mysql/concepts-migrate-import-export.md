---
title: Importálás és exportálás - Azure Database for MySQL
description: Ez a cikk ismerteti az adatbázisok importálásának és exportálásának gyakori módjait az Azure Database for MySQL-ben, például a MySQL Workbench eszközeivel.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163726"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>A MySQL-adatbázis áttelepítése importálás és exportálás használatával
Ez a cikk két gyakori módszert ismerteti az adatok importálása és exportálása egy Azure Database for MySQL-kiszolgáló a MySQL Workbench használatával. 

## <a name="before-you-begin"></a>Előkészületek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Azure-adatbázis a MySQL-kiszolgálóhoz, az [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)létrehozása az Azure Portal használatával című szöveget követve.
- MySQL Workbench [MySQL Workbench Letöltés](https://dev.mysql.com/downloads/workbench/) vagy más harmadik fél MySQL eszköz, hogy ezt az import / export.

## <a name="use-common-tools"></a>Általános eszközök használata
A közös segédprogramok és eszközök, például a MySQL Workbench vagy a mysqldump segítségével távolról csatlakozhat, és adatokat importálhat vagy exportálhat a MySQL Azure Database for MySQL-adatbázisba. 

Használja ezeket az eszközöket az ügyfélgépen egy internet-kapcsolaton keresztül, hogy csatlakozzon az Azure Database for MySQL-hez. Ssl-titkosítású kapcsolat használata a legjobb biztonsági eljárásokhoz, az [SSL-kapcsolat konfigurálása](concepts-ssl-connection-security.md)az Azure Database for MySQL alkalmazásban című részben leírtak szerint.

Az Azure Database for MySQL-be való áttelepítéskor nem kell áthelyeznie az importálási és exportálási fájlokat semmilyen speciális felhőhelyre. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Adatbázis létrehozása az Azure Database for MySQL-kiszolgálón
Hozzon létre egy üres adatbázist az Azure Database for MySQL-kiszolgálón, ahol át szeretné telepíteni az adatokat. Az adatbázis létrehozásához használjon egy eszközt, például a MySQL Workbench, a Vaad vagy a Navicat eszközt. Az adatbázis neve megegyezhet a kimászott adatokat tartalmazó adatbázisnevével, vagy létrehozhat egy másik nevű adatbázist is.

A csatlakozáshoz keresse meg a kapcsolatadatait az Azure Database for MySQL **áttekintése** című témakörben.

![A kapcsolatadatainak megkeresése az Azure Portalon](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Adja hozzá a kapcsolat adatait a MySQL Workbench-hez.

![MySQL Workbench kapcsolati karakterlánc](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Annak meghatározása, hogy mikor kell importálási és exportálási technikákat használni a memóriakép helyett, és állítsa vissza
A MySQL-eszközök segítségével importálhat és exportálhat adatbázisokat az Azure MySQL-adatbázisba a következő esetekben. Más esetekben előfordulhat, hogy a [memóriakép és](concepts-migrate-dump-restore.md) visszaállítási megközelítés használata. 

- Ha egy meglévő MySQL-adatbázisból az Azure MySQL-adatbázisba importálandó táblákat szelektíven kell kiválasztania, a legjobb, ha az importálási és exportálási technikát használja.  Ezzel kihagyhatja a szükségtelen táblákat az áttelepítésből, hogy időt és erőforrásokat takarítson meg. Például használja `--include-tables` a `--exclude-tables` vagy kapcsolót a `--tables` [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) és a kapcsoló [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Amikor a tábláktól eltérő adatbázis-objektumokat helyezi át, explicit módon hozza létre ezeket az objektumokat. Tartalmazza a kényszereket (elsődleges kulcs, idegen kulcs, indexek), nézeteket, függvényeket, eljárásokat, eseményindítókat és minden más áttelepíteni kívánt adatbázis-objektumot.
- Ha nem MySQL-adatbázisból származó adatokat telepít át, hozzon létre egylapos fájlokat, és importálja őket a [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)segítségével.

Győződjön meg arról, hogy az adatbázis összes táblája az InnoDB tárolómotort használja, amikor adatokat tölt be az Azure Database for MySQL-be. Az Azure Database for MySQL csak az InnoDB tárolómotort támogatja, így nem támogatja az alternatív tárolómotorokat. Ha a táblák alternatív tárolómotorokat igényelnek, konvertálja őket az InnoDB motorformátum használatára, mielőtt az Azure Database for MySQL-re váltana. 

Ha például van egy WordPress vagy webalkalmazás, amely a MyISAM motort használja, először konvertálja a táblákat az adatok InnoDB táblákba való áttelepítésével. Ezután állítsa vissza az Azure Database for MySQL.Then restore to Azure Database for MySQL. A záradék `ENGINE=INNODB` segítségével állítsa be a tábla létrehozásának motorját, majd az áttelepítés előtt vigye át az adatokat a kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Teljesítményre vonatkozó javaslatok importáláshoz és exportáláshoz
-   Fürtözött indexeket és elsődleges kulcsokat hozhat létre az adatok betöltése előtt. Adatok betöltése elsődleges kulcssorrendben. 
-   A másodlagos indexek létrehozásának késleltetése az adatok betöltéséig. Az összes másodlagos index létrehozása a betöltés után. 
-   Az idegenkulcs-megkötések letiltása betöltés előtt. Az idegen kulcsellenőrzések letiltása jelentős teljesítménynövekedést biztosít. Engedélyezze a korlátozásokat, és ellenőrizze az adatokat a terhelés után a hivatkozási integritás biztosítása érdekében.
-   Adatok betöltése párhuzamosan. Kerülje a túl sok párhuzamosság, amely azt eredményezné, hogy elérje az erőforrás-korlátot, és az azure-portálon elérhető metrikák használatával figyelheti az erőforrásokat. 
-   Szükség esetén használjon particionált táblákat.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importálás és exportálás a MySQL Workbench használatával
A MySQL Workbench programban kétféleképpen exportálhat és importálhat adatokat. Mindegyik más célt szolgál. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Táblázatadatok exportálása és importálása varázslók az objektumböngésző helyi menüjéből
![MySQL Workbench varázslók az objektumböngésző helyi menüjében](./media/concepts-migrate-import-export/p1.png)

A táblaadatok varázslói CSV- és JSON-fájlok használatával támogatják az importálási és exportálási műveleteket. Számos beállítási lehetőséget tartalmaznak, például elválasztókat, oszlopkijelölést és kódolási kijelölést. Minden varázslót helyi vagy távolról csatlakoztatott MySQL-kiszolgálókon hajthat végre. Az importálási művelet tartalmazza a táblázatot, az oszlopot és a típusleképezést. 

Ezeket a varázslókat az objektumböngésző helyi menüjéből érheti el, ha a jobb gombbal egy táblára kattint. Ezután válassza a **Táblaadatok exportálása varázslóvagy** **a Táblaadat-importálás varázsló lehetőséget.** 

#### <a name="table-data-export-wizard"></a>Táblaadat-exportálás varázsló
A következő példa csv-fájlba exportálja a táblát: 
1. Kattintson a jobb gombbal az exportálandó adatbázis táblájára. 
2. Válassza a **Táblaadatok exportálása varázslót**. Jelölje ki az exportálandó oszlopokat, a soreltolást (ha van ilyen) és a darabszámot (ha van ilyen). 
3. Az **Adatok kiválasztása exportáláshoz** lapon kattintson a **Tovább**gombra. Jelölje ki a fájl elérési útját, a CSV- vagy JSON-fájltípust. Válassza ki a sorelválasztót, a karakterláncok befedésének módját és a mezőelválasztót is. 
4. A **Kimeneti fájl helyének kiválasztása** lapon kattintson a **Tovább**gombra. 
5. Az **Adatok exportálása** lapon kattintson a **Tovább gombra.**

#### <a name="table-data-import-wizard"></a>Táblaadat-importálás varázsló
A következő példa csv-fájlból importálja a táblát:
1. Kattintson a jobb gombbal az importálandó adatbázis táblájára. 
2. Tallózással keresse meg és jelölje ki az importálandó CSV-fájlt, majd kattintson a **Tovább**gombra. 
3. Jelölje be a céltáblát (új vagy meglévő), és jelölje be a **Csonka tábla importálás előtt** jelölőnégyzetet, vagy törölje belőle a jelet. Kattintson a **Tovább** gombra.
4. Jelölje ki a kódolást és az importálandó oszlopokat, majd kattintson a **Tovább**gombra. 
5. Az **Adatok importálása** lapon kattintson a **Tovább**gombra. A varázsló ennek megfelelően importálja az adatokat.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL-adatok exportálása és importálása varázslók a Navigátor ablaktáblából
A MySQL Workbench-ből vagy a mysqldump parancsból létrehozott SQL exportálásához vagy importálásához használjon varázslót. A varázslókat a **Navigátor** ablaktáblából vagy a főmenü **Kiszolgáló** parancsával érheti el. Ezután válassza **az Adatexportálás** vagy **adatimportálás lehetőséget.** 

#### <a name="data-export"></a>Adatok exportálása
![MySQL Workbench-adatok exportálása a Navigátor ablaktáblával](./media/concepts-migrate-import-export/p2.png)

Az **Adatexportálás** lapon exportálhatja a MySQL-adatokat. 
1. Jelölje ki az exportálni kívánt sémákat, tetszés szerint válasszon ki adott sémaobjektumokat/táblákat az egyes sémákból, és hozza létre az exportálást. A konfigurációs beállítások közé tartozik a projektmappába vagy az önálló SQL-fájlba való exportálás, a tárolt rutinok és események kiírása vagy a táblaadatok kihagyása. 
 
   Másik lehetőségként az **Eredményhalmaz exportálása** segítségével exportálhat egy adott eredményhalmazt az SQL-szerkesztőben egy másik formátumba, például CSV,JSON, HTML és XML formátumba. 
3. Jelölje ki az exportálni kívánt adatbázis-objektumokat, és adja meg a kapcsolódó beállításokat.
4. Az aktuális objektumok betöltéséhez kattintson a **Frissítés** gombra.
5. Az exportálási művelet finomításához nyissa meg a **Speciális beállítások** lapot. Például adja hozzá a táblázatzárolásokat, használjon csere utasításhelyett, és idézőjelek et backtick karakterekkel.
6. Az exportálási folyamat megkezdéséhez kattintson az **Exportálás indítása** gombra.


#### <a name="data-import"></a>Adatimportálás
![MySQL Workbench-adatimportálás a Management Navigator használatával](./media/concepts-migrate-import-export/p3.png)

Az **Adatimportálás** lapon importálhatja vagy visszaállíthatja az exportált adatokat az adatexportálási műveletből vagy a mysqldump parancsból. 
1. Válassza ki a projektmappát vagy az önálló SQL-fájlt, válassza ki az importálni kívánt sémát, vagy válassza az **Új** lehetőséget új séma definiálásához. 
2. Az importálási folyamat megkezdéséhez kattintson az **Importálás indítása** gombra.

## <a name="next-steps"></a>További lépések
- Egy másik áttelepítési megközelítésként olvassa el [a MySQL-adatbázis áttelepítése dump használatával című olvasnivalót, és állítsa vissza az Azure Database for MySQL alkalmazásban](concepts-migrate-dump-restore.md)című fájlt.
- Az adatbázisok Azure Database for MySQL-be való áttelepítéséről az [Adatbázis-áttelepítési útmutatóban](https://aka.ms/datamigration)talál további információt. 
