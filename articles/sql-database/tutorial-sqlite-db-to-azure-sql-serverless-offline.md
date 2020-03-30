---
title: 'Oktatóanyag: Az SQLite-adatbázis áttelepítése az Azure SQL Database Serverless rendszerbe'
description: Ismerje meg, hogyan hajtson végre offline áttelepítést az SQLite-ról az Azure SQL Database Serverless-re az Azure Data Factory használatával.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780508"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Az SQLite adatbázis áttelepítése az Azure SQL Database Serverless rendszerbe
Sok ember számára az SQLite biztosítja az adatbázisok és az SQL programozás első élményét. Ez a felvétel számos operációs rendszerek és a népszerű alkalmazások teszi SQLite egyik legszélesebb körben telepített és használt adatbázis-motorok a világon. És mivel valószínűleg az első adatbázis-motor sok ember használja, gyakran a végén, mint egy központi része a projektek vagy alkalmazások. Ilyen esetekben, amikor a projekt vagy alkalmazás kinövi a kezdeti SQLite megvalósítás, a fejlesztők szükség lehet, hogy áttelepítsék az adatokat egy megbízható, központosított adattárba.

Az Azure SQL Database kiszolgáló nélküli egy számítási réteg egyetlen adatbázisok, amely automatikusan skálázódik számítási terhelés alapján számítási igény, és a másodpercenként felhasznált számítási mennyiség számláit. A kiszolgáló nélküli számítási szint is automatikusan szünetelteti az adatbázisokat inaktív időszakokban, amikor csak a tárolás számlázása, és automatikusan folytatja az adatbázisokat, amikor a tevékenység visszatér.

Miután követte az alábbi lépéseket, az adatbázis átkerül az Azure SQL Database Serverless, amely lehetővé teszi, hogy az adatbázis elérhetővé más felhasználók vagy alkalmazások a felhőben, és csak fizetni, amit használ, minimális alkalmazáskód-módosításokat.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés
- SQLite2 vagy SQLite3 adatbázis, amelyet át szeretne telepíteni
- Windows-környezet
  - Ha nem rendelkezik helyi Windows-környezettel, használhatja a Windows virtuális gép az Azure-ban az áttelepítéshez. Az Azure Files and Storage Explorer használatával áthelyezheti és elérhetővé teheti SQLite adatbázisfájlját a virtuális gépen.

## <a name="steps"></a>Lépések

1. Új Azure SQL-adatbázis kiépítése a kiszolgáló nélküli számítási rétegben.

    ![képernyőkép az Azure Portalról, amely bemutatja az Azure sql-adatbázis kiszolgáló nélküli kiépítési példáját](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Győződjön meg róla, hogy az SQLite adatbázisfájl elérhető a Windows környezetben. Telepítsen egy SQLite ODBC illesztőprogramot, ha még nem rendelkezik ilyenel http://www.ch-werner.de/sqliteodbc/)(sok áll rendelkezésre a nyílt forráskódú, például .

3. Hozzon létre egy rendszeradat-azonosítót az adatbázishoz. Győződjön meg arról, hogy a rendszerarchitektúrának megfelelő adatforrás-rendszergazdai alkalmazást használja (32 bites és 64 bites). A rendszerbeállításokban megtalálhatja, hogy melyik verziót futtatja.

    - Nyissa meg az ODBC adatforrás-rendszergazdát a környezetében.
    - Kattintson a rendszer dsn fülre, és kattintson a "Hozzáadás"
    - Válassza ki a telepített SQLite ODBC csatlakozót, és adjon értelmes nevet a kapcsolatnak, például sqlitemigrationsource
    - Az adatbázis nevének beállítása .db fájlra
    - Mentés és kilépés

4. Töltse le és telepítse az önkiszolgáló integrációs futásidejűt. Ennek legegyszerűbb módja az Expressz telepítés lehetőség, a dokumentációban részletezett módon. Ha manuális telepítést választ, meg kell adnia az alkalmazásnak egy hitelesítési kulcsot, amely a Data Factory-példányban található:

    - ADF indítása (Szerzői és figyelő a szolgáltatásból az Azure Portalon)
    - Kattintson a bal oldalon található "Szerző" fülre (kék ceruza).
    - Kattintson a Kapcsolatok (bal alsó sarokban), majd az integrációs futtatások elemre
    - Adjon hozzá új, saját üzemeltetésű integrációs futásidejűt, adjon neki nevet, és válassza *a 2.*

5. Hozzon létre egy új csatolt szolgáltatást a forrás SQLite adatbázis a Data Factory.

    ![üres csatolt szolgáltatások paneljét megjelenítő képernyőkép az Azure Data Factoryban](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. A Kapcsolatok csoportcsatolt szolgáltatás csoportban kattintson az Új gombra.

7. Az "ODBC" összekötő keresése és kiválasztása


    ![Az ODBC-összekötő emblémáját az Azure Data Factory csatolt szolgáltatások paneljén látható](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Adjon értelmes nevet a csatolt szolgáltatásnak, például "sqlite_odbc". Válassza ki az integrációs futásidejű a "Connect via integrációs futásidejű" legördülő menüből. Írja be az alábbi értéket a kapcsolati karakterláncba, és cserélje le a Initial Catalog változót a .db fájl fájlelérési parancsára, a DSN-t pedig a rendszer dsn-kapcsolatának nevére: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. A hitelesítési típus beállítása Névtelen re

10. A kapcsolat tesztelése

    ![sikeres kapcsolatot ábrázoló képernyőkép az Azure Data Factoryban](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Hozzon létre egy másik csatolt szolgáltatást a kiszolgáló nélküli SQL-tárolóhoz. Jelölje ki az adatbázist a csatolt szolgáltatásvarázslóval, és adja meg az SQL hitelesítési hitelesítő adatokat.

    ![az Azure Data Factoryban kiválasztott Azure SQL Database-t bemutató képernyőkép](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Bontsa ki a CREATE TABLE kimutatásokat az SQLite adatbázisból. Ezt úgy teheti meg, hogy az alábbi Python-parancsfájlt futtatja az adatbázisfájlban.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Hozza létre a céltáblákat a kiszolgáló nélküli SQL-célkörnyezetben a CREATE táblautasítások copying a CreateTables.sql fájlt, és fut az SQL-utasítások a Lekérdezésszerkesztő az Azure Portalon.

14. Térjen vissza a Data Factory kezdőképernyőjére, és kattintson az "Adatok másolása" gombra a feladatlétrehozási varázslón való futtatáshoz.

    ![Az Adatok másolása varázsló emblémáját ábrázoló képernyőkép az Azure Data Factoryban](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Jelölje ki az összes táblát a forrás SQLite adatbázis a jelölőnégyzetek segítségével, és rendelje hozzá őket a céltáblák az Azure SQL-ben. Miután a feladat futott, sikeresen áttelepítette az adatokat az SQLite-ból az Azure SQL-be!

## <a name="next-steps"></a>További lépések

- Első lépések: [Rövid útmutató: Egyetlen adatbázis létrehozása az Azure SQL Database-ben az Azure Portal használatával című témakörben.](sql-database-single-database-get-started.md)
- Az erőforráskorlátokról a [Kiszolgáló nélküli számítási réteg erőforráskorlátai](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
