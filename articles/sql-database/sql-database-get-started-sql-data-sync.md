---
title: Azure SQL adatszinkronizálás (előzetes verzió) beállítása |} Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan állíthat be Azure SQL adatszinkronizálás (előzetes verzió)
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: article
ms.date: 04/10/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 86b0e78f362d1cf3c2480aad97ef5281c5f3bc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-sql-data-sync-preview"></a>SQL adatszinkronizálás (előzetes verzió) beállítása
Ebben az oktatóanyagban elsajátíthatja, hogyan Azure SQL Data szinkronizálás beállítása az Azure SQL Database és az SQL Server-példányokat tartalmazó hibrid szinkronizálási csoportok létrehozásával. Az új szinkronizálási csoport teljes van konfigurálva, és a beállított ütemezés szerint szinkronizálja.

Ez az oktatóanyag feltételezi, hogy rendelkezik-e legalább némi tapasztalattal az SQL Database és SQL-kiszolgálóval. 

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync előzetes verziójával végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

Teljes PowerShell-példák bemutatják, hogyan konfigurálja az SQL adatszinkronizálás tekintse meg a következő cikkeket:
-   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
-   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>1. lépés – a sync-csoport létrehozása

### <a name="locate-the-data-sync-settings"></a>Keresse meg az adatok szinkronizálási beállítások

1.  A böngészőben navigáljon az Azure-portálon.

2.  Keresse meg az SQL-adatbázisok az irányítópultra, illetve az SQL-adatbázisok ikon a portál az eszköztáron.

    ![Azure SQL-adatbázisok listája](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  Az a **SQL-adatbázisok** lapon, válassza ki a meglévő SQL-adatbázis adatszinkronizálás hub-adatbázisként használni kívánt. Megnyílik az SQL-adatbázis lap.

    A központ adatbázisa a központi végpont a szinkronizálási topológia, amelyben a szinkronizálási csoportnak van több adatbázis-végpontot. Az összes adatbázis végpontja a központ adatbázissal ugyanazon szinkronizálás – Ez azt jelenti, hogy minden tag adatbázis - csoport szinkronizálási.

4.  A kiválasztott adatbázishoz az SQL adatbázis lapon jelölje be **az egyéb adatbázisokra szinkronizálási**. Megnyílik az adatszinkronizálás lap.

    ![Szinkronizálni kívánt más adatbázisok beállítás](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Hozzon létre egy új szinkronizálási csoportot

1.  Az adatszinkronizálás oldalon válassza ki a **új szinkronizálású csoport**. A **új szinkronizálású csoport** lap nyílik meg az 1. lépésben, **szinkronizálási csoport létrehozása**, kijelölt. A **adatok szinkronizálása csoport létrehozása** lapon is megnyílik.

2.  Az a **adatok szinkronizálása csoport létrehozása** lapján tegye a következőket:

    1.  Az a **szinkronizálási csoportnév** mezőbe írja be az új szinkronizálási csoport nevét.

    2.  Az a **szinkronizálási metaadatokat tároló adatbázis** területen válassza ki, hogy hozzon létre egy új adatbázist (ajánlott), vagy használjon már meglévő adatbázist.

        > [!NOTE]
        > A Microsoft azt javasolja, hogy hozzon létre egy új, üres adatbázis kívánja használni, mint a szinkronizálás metaadatokat tároló adatbázis. Adatszinkronizálás hoz létre táblák az adatbázisban, és a gyakori munkaterhelés futtatja. Ez az adatbázis automatikusan megosztva a szinkronizálási metaadatokat tároló adatbázis az összes, a szinkronizálási csoportok a kiválasztott régióban. A szinkronizálás metaadatokat tároló adatbázis vagy a név nélküli eldobása nem módosítható.

        Ha úgy döntött, hogy **új adatbázis**, jelölje be **új adatbázis létrehozása.** A **SQL-adatbázis** lap megnyitásakor. Az a **SQL-adatbázis** lapon, nevet, és konfigurálja az új adatbázist. Ezután kattintson az **OK** gombra.

        Ha úgy döntött, hogy **létező adatbázis használata**, a listáról válassza ki az adatbázist.

    3.  Az a **automatikus szinkronizálás** szakaszban, először válassza **a** vagy **ki**.

        Ha úgy döntött, hogy **a**, a a **szinkronizálási gyakoriság** szakaszt, adjon meg egy számot, és válassza ki a másodperc, perc, órák vagy napok.

        ![Adja meg a szinkronizálás gyakorisága](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  Az a **ütközésének feloldása** területen válassza ki a "Hub wins" vagy "Tag wins."

        "Hub wins" azt jelenti, hogy ha ütközés lép fel, a központ adatbázis adatai felülírja a ütköző adatokat a tag adatbázisban. "Tag wins" azt jelenti, hogy ha ütközés lép fel, a tag adatbázis adatai felülírja a ütköző adatokat a központi adatbázisban. 

        ![Adja meg az ütközések feloldása](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Válassza ki **OK** , és várja meg az új szinkronizálási csoport létrehozása és telepítése.

## <a name="step-2---add-sync-members"></a>2. lépés – szinkronizálás tagok hozzáadása

Után az új szinkronizálási csoport létrehozása és telepítése, 2. lépés **szinkronizálási tagok hozzáadása**, kijelölt a **új szinkronizálású csoport** lap.

Az a **Hub adatbázis** területen adja meg a meglévő hitelesítő adatait az SQL-adatbázis kiszolgálóra, amelyen a központ adatbázis található. Ne adjon meg *új* ebben a szakaszban a hitelesítő adatokat.

![Központi adatbázis szinkronizálása csoport hozzáadva](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Egy Azure SQL adatbázis hozzáadása

Az a **Tagadatbázis** szakaszban, és opcionálisan adja hozzá az Azure SQL Database szinkronizálási csoporthoz kiválasztásával **hozzáadása egy Azure-adatbázis**. A **Azure-adatbázis beállítása** lap megnyitásakor.

Az a **Azure-adatbázis beállítása** lapján tegye a következőket:

1.  Az a **szinkronizálási tagneve** mezőbe, adjon meg egy nevet az új szinkronizálási tag. Ez a név nem azonos azzal maga az adatbázis nevét.

2.  Az a **előfizetés** mező mellett válassza ki a társított Azure-előfizetés más célra.

3.  Az a **Azure SQL Server** mező mellett válassza ki a meglévő SQL adatbázis-kiszolgáló.

4.  Az a **Azure SQL Database** mező mellett válassza ki a meglévő SQL-adatbázis.

5.  Az a **szinkronizálási irányban** mező, jelölje be kétirányú szinkronizálás, a központi vagy a központ.

    ![Új SQL-adatbázis szinkronizálási tag hozzáadása](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  Az a **felhasználónév** és **jelszó** mezők, adja meg a meglévő hitelesítő adatait az SQL-adatbázis kiszolgálóra, amelyen a tagok adatbázisa található. Ne adjon meg *új* ebben a szakaszban a hitelesítő adatokat.

7.  Válassza ki **OK** , és várja meg az új szinkronizálási tag létrehozása és telepítése.

    ![Új SQL-adatbázis szinkronizálási tag hozzá lett adva.](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> A helyszíni SQL Server adatbázis hozzáadása

Az a **Tagadatbázis** szakaszban, és opcionálisan adja hozzá egy helyi SQL Server a szinkronizálási csoport kiválasztásával **adja hozzá az On-Premises adatbázis**. A **konfigurálása a helyszíni** lap megnyitásakor.

Az a **konfigurálása a helyszíni** lapján tegye a következőket:

1.  Válassza ki **válassza ki a szinkronizálási ügynök átjáró**. A **válassza ki a szinkronizálási ügynök** lap megnyitásakor.

    ![A szinkronizálási ügynök átjáró kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  Az a **válassza ki a szinkronizálási ügynök átjáró** lapon, válassza ki, hogy egy meglévő ügynököt vagy hozzon létre egy új ügynököt.

    Ha úgy döntött, hogy **meglévő ügynökök**, válassza ki a meglévő ügynököt a listából.

    Ha úgy döntött, hogy **hozzon létre egy új ügynök**, tegye a következőket:

    1.  A szinkronizálási ügynök ügyfélszoftver töltse le a megadott, és telepítse azt a számítógépet, amelyen az SQL Server is található.
 
        > [!IMPORTANT]
        > Meg kell nyitnia az 1433-as kimenő TCP port ahhoz, hogy az ügyfélügynök kommunikálni a kiszolgálóval a tűzfalon.


    2.  Adjon meg egy nevet az ügynök.

    3.  Válassza ki **létrehozása és a kulcs létrehozása**.

    4.  Az ügynök kulcs másolása a vágólapra.
        
        ![Új szinkronizálási ügynök létrehozása](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Válassza ki **OK** bezárásához a **válassza ki a szinkronizálási ügynök** lap.

    6.  Keresse meg az SQL Server-számítógépen és az ügyfél-szinkronizálási ügynök alkalmazás futtatásához.

        ![Az adatok szinkronizálása az ügyfélalkalmazás ügynök](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  Válassza ki a szinkronizálási ügynök alkalmazás **nyújt ügynök kulcs**. A **szinkronizálási metaadatok adatbázis konfigurációja** párbeszédpanel.

    8.  Az a **szinkronizálási metaadatok adatbázis konfigurációja** párbeszédpanelen illessze be az ügynök kulcs másolása az Azure portálról. A meglévő hitelesítő adatok is biztosítanak a Azure SQL Database kiszolgálón, amelyen a metaadatokat tároló adatbázis található. (Ha létrehozott egy új metaadatokat tároló adatbázis, az adatbázis van a központ adatbázis ugyanazon a kiszolgálón.) Válassza ki **OK** , és várja meg a konfiguráció befejezéséhez.

        ![Adja meg az ügynök kulcsot és a kiszolgáló hitelesítő adatait](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Ha a tűzfal hibaüzenet jelenik meg ezen a ponton, hogy egy tűzfalszabály létrehozása az Azure SQL Server-számítógépen a bejövő forgalom engedélyezésére. A szabály manuálisan hozhat létre, a portálon, de előfordulhat, hogy ez egyszerűbbé teszi az SQL Server Management Studio (SSMS) létrehozásához. Az SSMS próbálja meg az Azure-on a központ adatbázishoz való kapcsolódáshoz. Adja meg a < hub_database_name > néven. database.windows.net. Azure tűzfalszabály konfigurálásához kövesse a párbeszédpanel bezárása. Térjen vissza az ügyfél-szinkronizálási ügynök alkalmazást.

    9.  Az ügyfél-szinkronizálási ügynök alkalmazásban kattintson **regisztrálása** egy SQL Server-adatbázis regisztrálására az ügynököt. A **SQL Server-konfigurációs** párbeszédpanel.

        ![Hozzáadása és az SQL Server-adatbázisok konfigurálása](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. Az a **SQL Server-konfigurációs** párbeszédpanelen válassza ki, hogy az SQL Server-hitelesítés vagy a Windows-hitelesítés használatával csatlakoznak-e. Ha SQL Server-hitelesítést választja, adja meg a meglévő hitelesítő adatait. Adja meg az SQL Server nevét és a szinkronizálni kívánt adatbázis nevét. Válassza ki **tesztkapcsolat** a beállításainak ellenőrzéséhez. Ezután válassza a **Save** (Mentés) lehetőséget. A regisztrált adatbázis listájában jelenik meg.

        ![SQL Server-adatbázis most már regisztrálva van](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Most már bezárhatja az ügyfél-szinkronizálási ügynök alkalmazást.

    12. A portálon a a **konfigurálása a helyszíni** lapon jelölje be **válassza ki az adatbázist.** A **adatbázis kijelölése** lap megnyitásakor.

    13. Az a **adatbázis kijelölése** lap a **szinkronizálási tagneve** mezőbe, adjon meg egy nevet az új szinkronizálási tag. Ez a név nem azonos azzal maga az adatbázis nevét. A listáról válassza ki az adatbázist. Az a **szinkronizálási irányban** mező, jelölje be kétirányú szinkronizálás, a központi vagy a központ.

        ![Jelölje ki a helyi adatbázist](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Válassza ki **OK** bezárásához a **adatbázis kijelölése** lap. Válassza ki **OK** bezárásához a **konfigurálása a helyszíni** lapon, és várja meg az új szinkronizálási tag létrehozása és telepítése. Végezetül kattintson **OK** bezárásához a **szinkronizálási tagok kiválasztása** lap.

        ![A helyi adatbázis szinkronizálási csoportba felvett](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Csatlakozás SQL adatszinkronizálás és a helyi ügynök, adja hozzá a felhasználónevet a szerepkörhöz `DataSync_Executor`. Adatszinkronizálás ezt a szerepkört az SQL Server-példányt hoz létre.

## <a name="step-3---configure-sync-group"></a>3. lépés - a szinkronizálási csoport konfigurálása

Miután az új szinkronizálási csoporttagok létrehozott és telepített, 3. lépés, **konfigurálása szinkronizálású csoport**, kijelölt a **új szinkronizálású csoport** lap.

1.  Az a **táblák** lapon válasszon ki egy adatbázist a szinkronizálási csoport tagjainak listájáról, és válassza ki **frissítési séma**.

2.  Az elérhető táblák listájában jelölje ki a szinkronizálni kívánt táblák.

    ![Szinkronizálni kívánt táblák kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Alapértelmezés szerint a táblázat összes oszlopa van kiválasztva. Ha nem szeretné szinkronizálni az oszlopok, tiltsa le az oszlopok nem kívánt szinkronizálására vonatkozó négyzet jelölését. Ne feledje, hogy a kiválasztott elsődleges kulcsként megadott oszlop.

    ![Szinkronizálni kívánt mezők kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Végül válassza ki **mentése**.

## <a name="faq-about-setup-and-configuration"></a>Telepítésével és konfigurálásával kapcsolatos gyakori kérdések

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Milyen gyakran adatszinkronizálás adatok bármikor szinkronizálhatók a? 
A minimális gyakoriság 5 perc nem.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Nem SQL adatszinkronizálás teljes létrehozásának és kiosztásának táblák?

Ha a szinkronizálási séma táblák már nem létrejönnek a céladatbázisban, SQL adatszinkronizálás (előzetes verzió) hoz létre a őket a kijelölt oszlopokban. Azonban ez a viselkedés nem okoz a teljes visszaadása séma a következő okok miatt:

-   Csak a kiválasztott oszlop a céltáblában jönnek létre. Ha a forrástáblákból egyes oszlopai nem a szinkronizálási csoport része, ezek az oszlopok nem törlődnek a céltábla.

-   Indexek csak a kijelölt oszlopok jön létre. Ha a forrás tábla indexe az oszlopokat, amelyek nem a sync-csoport része, ezek az indexek nem törlődnek a céltábla.

-   XML típusú oszlopok indexei nem törlődnek.

-   ELLENŐRZÉSI korlátozásokban nem törlődnek.

-   A forrás táblák meglévő eseményindítók nem törlődnek.

-   Nézetek és tárolt eljárások nem jönnek létre az adatbázist.

Ezek a korlátozások miatt a következőket javasoljuk:
-   Éles környezetben használhatók a a teljes minőségű séma magát.
-   A próbálhatja ki a szolgáltatást, az Automatikus kiépítés szolgáltatása SQL adatszinkronizálás (előzetes verzió) is működik.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Miért látom azt táblázatot, amely nem hozta létre?  
Adatszinkronizálás ügyféloldali táblák az adatbázisban változáskövetési hoz létre. Ne törölje őket, vagy adatszinkronizálás nem működik.

### <a name="is-my-data-convergent-after-a-sync"></a>Az adatok egy irányba a szinkronizálás után?

Nem feltétlenül. A szinkronizálási csoport hubbal és három küllők (A, B és C) a szinkronizálás a következők hubot A Hub b és c Hub Ha egy módosításakor A adatbázis *után* a hubot A szinkronizálás, írt módosítása nem adatbázis B vagy C adatbázis a következő szinkronizálási feladat.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Hogyan szerezhetek sémamódosítások egy szinkronizálási csoporthoz?

És minden sémamódosítások manuálisan propagálása rendelkezik.
1. A séma módosításokat a központi és az összes szinkronizálás manuálisan replikálja.
2. A szinkronizálási séma frissítése.

**Új táblákat és oszlopokat hozzáadása**. Új táblákat és oszlopokat az aktuális szinkronizálási nincs hatással. Adatszinkronizálás figyelmen kívül hagyja az új táblákat és oszlopokat csak akkor adja hozzá a szinkronizálási sémát. Amikor új adatbázis-objektumok, ez az a legjobb módon, hogy kövesse:
1. Az új táblák és oszlopok hozzáadása a központ és az összes szinkronizálás.
2. Az új táblákat vagy oszlopok hozzáadása a szinkronizálási séma.
3. Indítsa el az értékek beszúrása az új táblákat és oszlopokat.

**Az adattípus oszlop módosítása**. Az oszlop adattípusának módosítása esetén adatszinkronizálás folytatja a működést, amíg az új értékekkel fér el az eredeti, a szinkronizálási sémában definiált adattípus. Például, ha az adatbázisában típusának módosítása **int** való **bigint**, adatszinkronizálás folytatja a működést, amíg beszúrása egy érték, amely túl nagy a **int** adattípus . A módosítás befejezéséhez manuálisan replikálja a séma módosulását, a központ és az összes szinkronizálás, és frissítse a a szinkronizálási sémát.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Hogyan exportálása és importálása az adatszinkronizálás adatbázis?
Adatbázis exportálása után egy `.bacpac` fájlt, és importálja a fájlt egy új adatbázis létrehozásához, meg kell nyitnia a következő két dolog adatszinkronizálás használni az új adatbázis:
1.  Az adatszinkronizálás objektumok és kiszolgálóoldali táblák tisztítása a **új adatbázis** használatával [ezt a parancsfájlt](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Ez a parancsfájl az összes szükséges adatszinkronizálás objektum törli az adatbázisból.
2.  Hozza létre újra az új adatbázis szinkronizálási csoportban. Ha már nincs szüksége a régi szinkronizálású csoport, törölje azt.

## <a name="faq-about-the-client-agent"></a>Az ügyfélügynök gyakori kérdések

### <a name="why-do-i-need-a-client-agent"></a>Miért kell egy ügyfélügynök?

Az SQL adatszinkronizálás (előzetes verzió) szolgáltatás SQL Server-adatbázisok az ügyfélügynök keresztül kommunikál. Ez a biztonsági szolgáltatás megakadályozza, hogy a tűzfal mögött lévő adatbázisok közvetlen kommunikációt. Ha az SQL adatszinkronizálás (előzetes verzió) szolgáltatás kommunikál az ügynök használatával végez el titkosított, kapcsolatok, valamint a egyedi jogkivonatot vagy *ügynök kulcs*. Az SQL Server-adatbázisok hitelesíteni az ügynök a kapcsolati karakterlánc és az ügynök kulcs használatával. Ez a kialakítás biztosítja az adatok biztonsági magas szintű.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>A helyi ügynök hány példányban felhasználói felület futtatható?

A felhasználói felület csak egy példány futtatható.

### <a name="how-can-i-change-my-service-account"></a>Hogyan lehet módosítani a szolgáltatásfiók?

Ügyféloldali ügynök telepítése után a csak a szolgáltatásfiók módosítása, távolítsa el azt, és egy új ügyfél-ügynök telepítése az új szolgáltatásfiókkal.

### <a name="how-do-i-change-my-agent-key"></a>Hogyan változtathatom meg a ügynök kulcsot?

Egy ügynök kulcs csak egyszer használható egy ügynök. Nem lehet használni, amikor távolítsa el, majd telepítse újra egy új ügynököt, és nem használható több ügynök lehet. Ha egy új kulcsot létrehozni egy meglévő ügynöknek szüksége, arról, hogy ugyanazzal a kulccsal rögzített, és az SQL adatszinkronizálás (előzetes verzió) szolgáltatással rendelkező kell lennie.

### <a name="how-do-i-retire-a-client-agent"></a>Hogyan kivonása a ügyféloldali ügynök?

Azonnal érvényteleníti kivonása vagy a az ügynök, újragenerálja a kulcsot a portálon, de nem küldje el az ügynököt felhasználói felületén. A kulcs újragenerálása érvényteleníti a korábbi kulcs függetlenül, ha a megfelelő ügynök online vagy offline állapotú.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hogyan egy ügyfélügynök áthelyezése másik számítógépre?

Ha azt szeretné, a helyi ügynök jelenleg meg egy másik számítógépen futnak, tegye a következőket:

1. Telepítse az ügynököt a kívánt számítógépre.

2. Jelentkezzen be az SQL adatszinkronizálás (előzetes verzió) portálra, és az új ügynök egy ügynök kulcs újragenerálása.

3. Az új ügynök kulcs küldhetnek az új ügynök felhasználói felület használatával.

4. Várjon, amíg az ügyfélügynök letölti a helyi adatbázis, amely korábban regisztrálva volt listáját.

5. Adja meg az adatbázis hitelesítő adatai az összes olyan adatbázis, amely megjeleníti az nem érhető el. Ezeket az adatbázisokat az új számítógépre, amelyen az ügynök telepítve van a elérhetőnek kell lennie.

## <a name="next-steps"></a>További lépések
Gratulálunk! Létrehozott egy szinkronizálású csoport, amely tartalmazza az SQL Database-példányt, és egy SQL Server-adatbázis.

További információ az SQL Data Syncről:

-   [Több felhőalapú és helyszíni adatbázis közötti adatszinkronizálás az Azure SQL Data Synckel](sql-database-sync-data.md)
-   [Ajánlott eljárások az Azure SQL Data Synchez](sql-database-best-practices-data-sync.md)
-   [A figyelő az Azure SQL Data szinkronban Naplóelemzési](sql-database-sync-monitor-oms.md)
-   [Az Azure SQL Data Synckel hibaelhárítása](sql-database-troubleshoot-data-sync.md)

-   Teljes PowerShell-példák az SQL Data Sync konfigurálásáról:
    -   [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Az SQL Data Sync REST API dokumentációjának letöltése](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

További információ az SQL Database-ről:

-   [Az SQL Database áttekintése](sql-database-technical-overview.md)
-   [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
