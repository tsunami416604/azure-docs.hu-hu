---
title: "Adatbetöltési útmutató – Azure SQL Data Warehouse | Microsoft Docs"
description: "Javaslatok az adatok betöltéséhez és ELT végrehajtásához az Azure SQL Data Warehouse segítségével"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Útmutató az adatok betöltéséhez az Azure SQL Data Warehouse-ba
Javaslatok és teljesítményoptimalizálás az adatok betöltéséhez az Azure SQL Data Warehouse-ba 

- További információ a PolyBase-ről és egy kinyerési, betöltési és átalakítási (ETL) folyamat megtervezéséről: [ETL tervezése SQL Data Warehouse-hoz](design-elt-data-loading.md).
- Betöltési oktatóanyag: [Adatok betöltése az Azure Blob Storage-ból az Azure SQL Data Warehouse-ba a PolyBase használatával](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="extract-source-data"></a>Forrásadatok kinyerése

Amikor ORC fájlformátumba exportál adatokat az SQL Serverből vagy az Azure SQL Data Warehouse-ból, előfordulhat, hogy a rendszer a sok szöveget tartalmazó oszlopok számát legfeljebb 50 oszlopra korlátozza a Java Nincs elég memória típusú hibái miatt. Ezt a problémát úgy küszöbölheti ki, ha az oszlopok csak egy részhalmazát exportálja.


## <a name="land-data-to-azure"></a>Adatok betöltése az Azure-ba
A PolyBase nem képes egymillió bájtnál több adatot tartalmazó sorok betöltésére. Az Azure Blob Storage-ba vagy az Azure Data Lake Store-ba helyezett szöveges fájlok nem tartalmazhatnak egy millió bájtnál több adatot. Ez a meghatározott táblázatsémától függetlenül érvényes.

A késés minimalizálása érdekében helyezze egymás mellé a tárolási réteget és az adattárházat.

## <a name="data-preparation"></a>Adatok előkészítése

Minden fájlformátum eltérő teljesítményjellemzővel rendelkezik. A leggyorsabb betöltés érdekében használjon tömörített, tagolt szövegfájlokat. Az UTF-8 és UTF-16 formátum teljesítménye között minimális a különbség.

A nagy tömörített fájlokat ossza fel kisebb tömörített fájlokra.

## <a name="create-designated-loading-users"></a>Kijelölt, betöltést végző felhasználó létrehozása
A betöltések megfelelő számítási erőforrásokkal való futtatásához hozzon létre betöltések futtatására kijelölt betöltést végző felhasználókat. Az egyes betöltést végző felhasználókat rendelje hozzá egy adott erőforrásosztályhoz. Betöltés futtatásához jelentkezzen be az egyik betöltést végző felhasználóként, majd futtassa a betöltést. A betöltés a felhasználó erőforrásosztályával fut.  Ez a módszer egyszerűbb, mint a felhasználó erőforrásosztályának módosításával próbálkozni, hogy az megfeleljen az aktuális erőforrásosztály-igénynek.

Ez kód létrehoz egy betöltést végző felhasználót a staticrc20 erőforrásosztályhoz. Ez vezérlői jogosultságot biztosít a felhasználó számára egy adatbázishoz, majd felveszi a felhasználót a staticrc20 adatbázis-szerepkör tagjaként. Ha a staticRC20 erőforrásosztályokhoz tartozó erőforrással szeretne betöltést futtatni, egyszerűen jelentkezzen be LoaderRC20 felhasználóként, és futtassa a betöltést. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

A betöltéseket inkább statikus, mint dinamikus erőforrásosztályokkal futtassa. A statikus erőforrásosztályok használata garantálja az azonos erőforrásokat a [szolgáltatási szinttől](performance-tiers.md#service-levels) függetlenül. Ha dinamikus erőforrásosztályt használ, az erőforrások a szolgáltatásszinttől függően változhatnak. Dinamikus osztályok esetében egy alacsonyabb szolgáltatási szint azt jelenti, hogy feltehetően nagyobb erőforrásosztályt kell használnia a betöltést végző felhasználóhoz.

### <a name="example-for-isolating-loading-users"></a>Példa a betöltést végző felhasználók elkülönítésére

Gyakran van szükség több olyan felhasználóra, akik képesek adatokat betölteni egy SQL DW-be. Mivel a [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] parancshoz szükség van CONTROL jogosultságokra az adatbázishoz, végül több felhasználónak is lesz vezérlési hozzáférése az összes sémához. Ennek korlátozására használhatja a DENY CONTROL utasítást.

Vegyünk például két adatbázissémát: schema_A az A részleghez, és schema_B a B részleghez. Legyen user_A és user_B két PolyBase-betöltést végző adatbázis-felhasználó az A, illetve a B részlegen. Mindkét felhasználó kapott adatbázisszintű CONTROL jogosultságokat. Az A és B séma létrehozói zárolják a sémáikat a DENY utasítás segítségével:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

User_A és user_B számára mostantól nem lesz hozzáférhető a másik részleg sémája.


## <a name="load-to-a-staging-table"></a>Betöltés előkészítési táblába

A leggyorsabb betöltési sebesség eléréséhez egy round_robin halom-előkészítési táblába végezze a betöltést. Ez az adatok az Azure Storage-rétegből az SQL Data Warehouse-ba való áthelyezésének leghatékonyabb módja.

Ha nagy betöltési feladatra számít, skálázza fel vertikálisan az adattárházát.

Az optimális betöltési teljesítmény érdekében egyszerre egy betöltési feladatot végezzen.

### <a name="optimize-columnstore-index-loads"></a>Oszlopcentrikus index betöltésének optimalizálása

Az oszlopcentrikus indexek sok memóriát igényelnek az adatok magas minőségű sorcsoportokba való tömörítéséhez. A legjobb tömörítési és indexelési hatékonyság érdekében az oszlopcentrikus indexnek 1 048 576 sort kell tömörítenie az egyes sorcsoportokba. Ez a sorok sorcsoportonkénti maximális száma. Ha korlátozott a rendelkezésre álló memória mennyisége, előfordulhat, hogy az oszlopcentrikus index nem éri el a maximális tömörítési sebességet. Ez hatással van a lekérdezés teljesítményére. A témakör részletes bemutatása: [Oszloptár memóriájának optimalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Annak érdekben, hogy elég memória álljon a betöltést végző felhasználók rendelkezésére a maximális tömörítési sebesség eléréséhez, használjon olyan betöltést végző felhasználókat, akik közepes vagy nagy erőforrásosztály tagjai. 
- Töltsön be elég sort az új sorcsoportok teljes feltöltéséhez. Kötegelt betöltés esetén minden 1 048 576 sor közvetlenül az oszloptárba kerül. A 102 400 sornál kisebb betöltések a deltatárba küldik a sorokat, amely egy fürtözött indexben tartja a sorokat, amíg elegendő nem gyűlik össze a tömörítéshez. Ha kevesebb sort tölt be, előfordulhat, hogy mind a deltatárba kerül, és a rendszer nem tömöríti azokat azonnal oszloptár formátumba.


### <a name="handling-loading-failures"></a>Betöltési hibák kezelése

Külső táblát használó betöltés meghiúsulhat a következő hibával: *„A lekérdezés megszakadt – a rendszer elérte a felső visszautasítási küszöbértéket külső forrásból való beolvasás során”*. Ez azt jelzi, hogy a külső adatok *szabálytalan* rekordokat tartalmaznak. Az adatrekord akkor számít „szabálytalannak”, ha az aktuális adattípusok/az oszlopok száma nem felel meg a külső tábla definícióinak, vagy ha az adatok nem felelnek meg a megadott külső fájlformátumnak. 

A probléma megoldásához győződjön meg arról, hogy a külső tábla és fájlformátumok definíciói helyesek, és hogy a külső adatok megfelelnek ezeknek a definícióknak. Amennyiben a külső adatrekordok egy részhalmaza szabálytalan, dönthet úgy, hogy nem tart igényt ezekre a rekordokra a lekérdezéseihez. Ehhez használja a CREATE EXTERNAL TABLE DDL visszautasítási lehetőségeit.



## <a name="insert-data-into-production-table"></a>Adatok beszúrása a termelési táblába
Ezek a javaslatok sorok termelési táblákba való beszúrására vonatkoznak.


### <a name="batch-insert-statements"></a>Kötegelt INSERT utasítások
A kis táblák [INSERT utasítással](/sql/t-sql/statements/insert-transact-sql.md) végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is kielégítheti az igényeit, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Az egyszeres beszúrásoknál hatékonyabb egy kötegelt betöltés végrehajtása. 

Ha több ezer egyszeres beszúrást hajt végre egy nap, kötegelje a beszúrásokat, hogy kötegelve tölthesse be őket.  Fejlesszen folyamatokat, amelyek az egyszeres beszúrásokat egy fájlhoz fűzik, majd hozzon létre egy másik folyamatot, amely időszakosan betölti a fájlt.

### <a name="create-statistics-after-the-load"></a>Statisztika létrehozása a betöltés után

A lekérdezési teljesítmény javításához fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően.  A statisztika részletes ismertetése: [Statistics][Statistics]. A következő példa a Customer_Speed tábla öt oszlopára vonatkozó statisztikákat hoz létre.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Tárkulcsok rotálása
Biztonsági szempontból érdemes rendszeresen módosítani a Blob Storage hozzáférési kulcsát. A Blob Storage-fiókhoz két tárkulcs tartozik. Ez azért van, hogy váltogathassa a kulcsokat.

Az Azure Storage-fiók kulcsainak rotálása:

1. Hozzon létre egy második adatbázishoz kötődő hitelesítőadat-egységet másodlagos tárelérési kulccsal.
2. Hozzon létre egy második külső adatforrást az új hitelesítőadatok alapján.
3. Törölje, majd hozza létre a külső táblá(ka)t, hogy az új külső adatforrásokra mutassanak. 

A külső táblák új adatforrásba való migrálása után hajtsa végre a következő karbantartási feladatokat:

1. Vesse el az első külső adatforrást.
2. Törölje az elsődleges tárelérési kulcs alapján készült, első adatbázishoz kötődő hitelesítő adatokat.
3. Jelentkezzen be az Azure-ba, és hozza létre újra az elsődleges hozzáférési kulcsot, hogy készen álljon a következő rotálásra.


## <a name="next-steps"></a>Következő lépések
A betöltési folyamat monitorozása: [A számítási feladat monitorozása DMV-kkel](sql-data-warehouse-manage-monitor.md).



