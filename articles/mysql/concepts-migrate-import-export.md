---
title: "A MySQL az Azure-adatbázis exportálására és importálásra |} Microsoft Docs"
description: "Ez a cikk ismerteti a leggyakoribb importálása és exportálása az Azure Database adatbázisok MySQL, az eszközöket, például a MySQL munkaterület használatával."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/02/2017
ms.openlocfilehash: 36ffa7082ce60093cbd90d0c12187e28f517646d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>A MySQL-adatbázis áttelepítéséhez importálása és exportálása
Ez a cikk azt ismerteti, két gyakori módszer az adatok importálása és exportálása a MySQL-kiszolgáló Azure adatbázisba MySQL munkaterület használatával. 

## <a name="before-you-begin"></a>Előkészületek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Egy Azure-adatbázis MySQL-kiszolgáló következő [hozzon létre egy Azure-portál használatával MySQL-kiszolgálóhoz tartozó Azure-adatbázis](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL-munkaterület [letöltött](https://dev.mysql.com/downloads/workbench/), vagy egy másik MySQL történő importálására és exportálására.

## <a name="use-common-tools"></a>Általános eszközök használata
Például a MySQL-munkaterületet, varangy vagy Navicat közös eszközök segítségével távolról csatlakozzon és importálhat, illetve exportálhatja az adatokat az Azure-adatbázisba a MySQL. 

Az ilyen eszközök használatát az Internet-kapcsolat ügyfélgépre MySQL Azure adatbázishoz való kapcsolódáshoz. Egy SSL titkosítású kapcsolat használata ajánlott biztonsági eljárások a [MySQL az Azure-adatbázis konfigurálása az SSL-kapcsolat](concepts-ssl-connection-security.md).

Nem kell áthelyezni az importálás és exportfájlok különleges felhő bárhova MySQL az Azure-adatbázis áttelepítésekor. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>A MySQL-kiszolgálóhoz tartozó Azure-adatbázis egy adatbázis létrehozásához.
Üres adatbázist létrehozni az Azure-adatbázisban MySQL kiszolgálók az adatok áttelepítéséhez. Például a MySQL-munkaterületet, varangy vagy Navicat eszköz segítségével hozza létre az adatbázist. Az adatbázis neve megegyezik a dömpingelt adatokat tartalmazó adatbázis is rendelkezik, vagy létrehozhat egy adatbázist egy eltérő nevű.

Kapcsolat létesítése, keresse meg a kapcsolati adatokat a **tulajdonságok** MySQL az Azure-adatbázis paneljén.

![A kapcsolati adatok az Azure portálon található](./media/concepts-migrate-import-export/1_server-properties-name-login.png)

A kapcsolati adatok hozzáadása a MySQL-munkaterületet.

![MySQL-munkaterület kapcsolati karakterlánc](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Határozza meg, hogy mikor használja az importálást és technikák helyett egy biztonsági másolat exportálása és visszaállítása
MySQL-eszközök segítségével importálása és exportálása adatbázisokat az Azure-beli MySQL Database a következő esetekben. Más esetekben előfordulhat, hogy kihasználhatja a használatával a [dump és visszaállítási](concepts-migrate-dump-restore.md) közelítik meg helyette. 

- Ha szelektív parancsot néhány meglévő MySQL-adatbázis importálása az Azure-beli MySQL Database van szüksége, célszerű az importálás és exportálás a módszer használata.  Ezzel a módszerrel, hagyja ki ezt az áttelepítési időt és erőforrásokat takaríthat a szükségtelen táblázatra. Tegyük fel például, a `--include-tables` vagy `--exclude-tables` kapcsoló [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) és a `--tables` kapcsoló [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Ha az adatbázis-objektumok táblák nem telepít át, explicit módon hozza létre azokat az objektumokat. Például megkötések (elsődleges kulcs, külső kulcs, indexek), nézetek, függvények, eljárások, eseményindítók és bármely más adatbázis-objektumok szeretne áttelepíteni.
- A MySQL-adatbázis nem külső adatforrások, amelybe migrálna adatokat, amikor egybesimított fájlok létrehozása, és importálja a [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Győződjön meg arról, hogy az összes táblák az adatbázisban használja-e a InnoDB tárolóprogramot, ha tölt be adatokat az Azure-adatbázisba a MySQL. Azure MySQL-adatbázis csak a InnoDB tárolási összetevőt támogatja, így a másodlagos tárolási motorok nem támogatja. Ha a táblák alternatív tárolási motorok van szükség, mindenképpen alakíthatja át őket a MySQL az Azure-adatbázishoz az áttelepítés előtt InnoDB motor formátumot használja. 

Például van egy WordPress vagy webes alkalmazás MyISAM motort használja, alakítani a táblák InnoDB táblákba át kell telepítenie az adatokat. És MySQL az Azure-adatbázis visszaállítása. Használja a záradékot `ENGINE=INNODB` a motor tábla létrehozása, és majd vigye át az adatokat az áttelepítés előtt kompatibilis táblába. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Teljesítmény javaslatok importálása és exportálása
-   Adatok betöltése előtt hozzon létre a fürtözött indexek és az elsődleges kulcsok. Az elsődleges kulcs sorrendben adatok betöltése. 
-   Adatok után másodlagos indexek létrehozását késleltetés be van töltve. Minden másodlagos indexek létrehozása betöltése után. 
-   Tiltsa le a külső kulcsra vonatkozó megkötések betöltés előtt. Teljesítménynövekedéshez idegen kulcs ellenőrzések letiltása biztosít. A megkötések engedélyezéséhez és a hivatkozási integritás biztosított betöltése után ellenőrizze az adatokat.
-   Az adatok párhuzamos betöltése. Ne használjon túl sok párhuzamos okozza, hogy egy erőforrás korlátot elérte volna, és megfigyelje az erőforrásokat az Azure portálon elérhető metrikák használatával. 
-   Használja a particionált táblákat, amikor szükséges.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importálás és exportálás MySQL munkaterület használatával
Két módon lehet MySQL munkaterület adatok importálásához és exportálásához. Minden más célt szolgál. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Tábla adatai exportálása és importálása a varázslók az objektumtallózó helyi menüből
![MySQL-munkaterület varázslók az objektumtallózó helyi menüben](./media/concepts-migrate-import-export/p1.png)

A táblabeli adatok a varázslók importálási támogatja, és műveletek exportálása CSV és a JSON-fájlok használatával. Több konfigurációs beállítások, például az szerepel, az oszlop kiválasztása és a kódolási kiválasztása tartalmaznak. Egyes varázslók helyi vagy távoli csatlakoztatott MySQL-kiszolgálók alapján végezheti el. Az importálási művelet magában foglalja a táblázatok, oszlopok és leképezésének. 

Kattintson a jobb gombbal egy tábla ezekben a varázslókban a Objektumtallózó helyi menüből érheti el. Válasszon **adatok exportálása varázsló** vagy **tábla adatok importálása varázsló**. 

#### <a name="table-data-export-wizard"></a>Adatok exportálása varázsló
A következő példa a tábla CSV-fájlba exportálja: 
1. Kattintson a jobb gombbal a táblázat az adatbázis exportálható. 
2. Válassza ki **adatok exportálása varázsló tábla**. Válassza ki az oszlopok exportálható, sor: eltolását (ha van ilyen) és száma (ha van ilyen). 
3. Az a **jelölje ki az exportált adatokat** kattintson **következő**. Válassza ki a fájl elérési útját, CSV vagy JSON-fájl típusa. Kiválaszthatja a Sorelválasztó, karakterláncok és mezőhatároló befoglaló metódusában. 
4. Az a **válassza kimeneti fájl helye** kattintson **következő**. 
5. Az a **adatok exportálása** kattintson **következő**.

#### <a name="table-data-import-wizard"></a>Adatok importálása varázsló
A következő példa a tábla importál CSV-fájlból:
1. Kattintson a jobb gombbal a tábla, importálandók az adatbázis. 
2. Keresse meg és válassza ki az importálni, és kattintson a CSV-fájl **következő**. 
3. Válassza ki a célként megadott táblája (új vagy meglévő), és válassza ki, vagy törölje a jelet a **importálás előtt Truncate table** jelölőnégyzetet. Kattintson a **Tovább** gombra.
4. Jelölje be kódolás és importálni, és kattintson az oszlopok **következő**. 
5. Az a **adatimportálás** kattintson **következő**. A varázsló ennek megfelelően importálja az adatokat.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL data exportálása és importálása varázsló a Navigátor panelről
A varázsló segítségével exportál vagy importál a MySQL munkaterület jön létre, vagy a mysqldump parancs által létrehozott SQL. Ezekben a varázslókban a hozzáférés a **Navigator** ablaktáblán vagy kiválasztásával **Server** a főmenüből. Válassza ki **adatok exportálása** vagy **adatimportálás**. 

#### <a name="data-export"></a>Adatok exportálása
![MySQL-munkaterület adatok exportálása az Navigator panelen](./media/concepts-migrate-import-export/p2.png)

Használhatja a **adatok exportálása** lapon, a MySQL-adatok exportálása. 
1. Válassza ki, amelyet szeretne exportálni, lehetősége van adott séma objektumok/táblák választhat minden séma és létrehozni az exportálás minden sémát. Konfigurációs beállítások közé tartozik a projektmappa vagy önálló SQL-fájlba exportálása, tárolt eljárások és események dump, vagy hagyja ki a tábla adatai. 
 
   Másik megoldásként használhatja **exportálása eredményhalmaz** egy adott eredményhalmazt, az SQL-szerkesztővel egy másik formátumban, például a fürt megosztott kötetei szolgáltatás, JSON, HTML és XML exportálása. 
3. Válassza ki az adatbázis-objektumok exportálása, és konfigurálja a kapcsolódó beállításokat.
4. Kattintson a **frissítése** az aktuális objektum betöltése.
5. Szükség esetén nyissa meg a **speciális beállítások** pontosítsa az exportálási művelet fülre. Adja hozzá például a táblázat zárolásokat, használja a név felülírandó insert utasításokban, és ajánlat azonosítók backtick karakterekkel helyett.
6. Kattintson a **Start exportálása** az exportálási folyamat megkezdéséhez.


#### <a name="data-import"></a>Adatok importálása
![MySQL munkaterület adatokat importál felügyeleti Navigator használatával](./media/concepts-migrate-import-export/p3.png)

Használhatja a **adatimportálási** fülre, és importálja vagy állítsa vissza az exportált adatok az exportálási művelet, illetve a mysqldump parancsot. 
1. Választhat a projektmappa vagy az önálló SQL-fájl, a séma importálása, vagy válasszon **új** új sémát meghatározásához. 
2. Kattintson a **Start importálása** az importálási folyamat megkezdéséhez.

## <a name="next-steps"></a>Következő lépések
Egy másik áttelepítési módszert használja, mint olvasási [áttelepítése a MySQL adatbázis használatával dump és MySQL az Azure-adatbázis visszaállítása](concepts-migrate-dump-restore.md). 
