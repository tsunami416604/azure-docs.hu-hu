---
title: Az Azure SQL Data Sync beállítása |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan lehet az Azure SQL Data Sync beállítása
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 7a009d310fe29ce67a16d48edb86a7e652088ac6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727921"
---
# <a name="tutorial-set-up-sql-data-sync-to-sync-data-between-azure-sql-database-and-sql-server-on-premises"></a>Oktatóanyag: A helyszíni adatokat az Azure SQL Database és SQL Server között, az SQL Data Sync beállítása

Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy hibrid szinkronizálási csoport, amely tartalmazza az Azure SQL Database és az SQL Server-példányok Azure SQL Data Sync beállítása. Az új szinkronizálási csoport teljes van konfigurálva, és a beállított ütemezés szerint szinkronizálja.

Ez az oktatóanyag feltételezi, hogy az SQL Database és az SQL Server legalább némi tapasztalattal rendelkezik.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](sql-database-sync-data.md) ismertető cikket.

A teljes PowerShell-példák azt mutatják be, hogyan konfigurálhatja az SQL Data Sync a következő cikkekben talál:

- [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
- [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Az Azure SQL Data Sync does **nem** támogatja az Azure SQL Database felügyelt példánya jelenleg.

## <a name="step-1---create-sync-group"></a>1. lépés – a szinkronizálási csoport létrehozása

### <a name="locate-the-data-sync-settings"></a>Keresse meg az adatok szinkronizálási beállítások

1. A böngészőben navigáljon az Azure Portalon.

2. A portálon keresse meg az SQL-adatbázisok, az irányítópulton, illetve az SQL Database-adatbázisok ikonra az eszköztáron.

    ![Az Azure SQL Database-adatbázisok listája](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3. Az a **SQL-adatbázisok** lapon, válassza ki a meglévő SQL-adatbázis, amelyet szeretne használni a központi adatbázis adatszinkronizálási. Az SQL database oldalon nyílik meg.

    A központi adatbázis a szinkronizálási topológiát, amelyben a szinkronizálási csoport van több adatbázis-végpont központi végpontja. Más adatbázisban szereplő összes végpont a központi adatbázis ugyanazon szinkronizálási csoport -, minden tag adatbázis - szinkronban.

4. A kiválasztott adatbázishoz az SQL database oldalon válassza ki a **szinkronizálás más adatbázisokkal**. A Data Sync lap nyílik meg.

    ![Szinkronizálás más adatbázisok lehetőség](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Hozzon létre egy új szinkronizálási csoport

1. Az adatszinkronizálás oldalon válassza ki a **új szinkronizálási csoport**. A **új szinkronizálási csoport** lap, melyen az 1. lépésben **szinkronizálási csoport létrehozása**, kiemelve. A **adatok szinkronizálási csoport létrehozása** lapon is megnyílik.

2. Az a **adatok szinkronizálási csoport létrehozása** lapon, tegye a következőket:

   1. Az a **szinkronizálási csoport neve** mezőben adjon meg egy nevet az új szinkronizálási csoport.

   2. Az a **szinkronizálási metaadat-adatbázis** területen válassza ki, hogy hozzon létre egy új adatbázist (ajánlott), vagy egy meglévő adatbázis használatát.

        > [!NOTE]
        > A Microsoft azt javasolja, hogy a szinkronizálási metaadat-adatbázisként használni, új, üres adatbázis létrehozása. Adatszinkronizálás hoz létre a táblák az adatbázisban, és a gyakori számítási feladat. Ez az adatbázis automatikusan meg lesz osztva, a szinkronizálási metaadat-adatbázis az összes szinkronizálási csoportot a kiválasztott régióban. A szinkronizálási metaadat-adatbázis vagy a név nem módosítható a régióban a szinkronizálási csoportok és a szinkronizációs ügynökök eltávolítása nélkül.

        Ha úgy döntött **új adatbázis**válassza **új adatbázis létrehozása.** A **SQL Database** lap megnyitásakor. Az a **SQL Database** lapon nevezze el és konfigurálja az új adatbázist. Ezután kattintson az **OK** gombra.

        Ha úgy döntött **létező adatbázis használata**, válassza ki az adatbázist a listából.

   3. Az a **Automatická Synchronizace** szakaszban először válassza ki **a** vagy **ki**.

        Ha úgy döntött **a**, a a **szinkronizálási gyakoriság** szakaszt, adjon meg egy számot, és válassza ki a másodperc, perc, óra vagy nap.

        ![Adja meg a szinkronizálás gyakorisága](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

   4. Az a **ütközésfeloldás** területen válassza ki a "Hub wins" vagy "Tag wins."

        "A hub wins" azt jelenti, ha ütközés lép fel, az adatokat a központi adatbázis felülírja a ütköző adatokat a tag adatbázisban. "Tag wins" azt jelenti, hogy ha ütközés lép fel, a tag adatbázis adatait az ütköző adatokat a központi adatbázis felülírja.

       ![Adja meg, hogyan ütközések feloldása](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

   5. Válassza ki **OK** és várjon, amíg az új szinkronizálási csoport létrehozása és üzembe helyezve.

## <a name="step-2---add-sync-members"></a>2. lépés – szinkronizálási tagok hozzáadása

Miután az új szinkronizálási csoport létrehozása és üzembe helyezett, 2. lépés **szinkronizálási tagok hozzáadása**, kiemelten jelenik meg a **új szinkronizálási csoport** lapot.

Az a **központi adatbázis** területén adja meg a meglévő hitelesítő adatait az SQL Database-kiszolgálóhoz, amelyen a központi adatbázis is található. Ne adjon meg *új* ebben a szakaszban a hitelesítő adatokat.

![Központi adatbázis van adva a szinkronizálási csoport](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Az Azure SQL-adatbázisok hozzáadása

Az a **Tagadatbázis** című igény szerint adja hozzá az Azure SQL Database a szinkronizálási csoport kiválasztásával **hozzáadása egy Azure Database**. A **konfigurálása Azure-adatbázis** lap megnyitásakor.

Az a **konfigurálása Azure-adatbázis** lapon, tegye a következőket:

1. Az a **szinkronizálási tag neve** mezőben adjon meg egy nevet az új szinkronizálási tag. Ez a név nem azonos azzal a maga az adatbázis nevét.

2. Az a **előfizetés** mezőben válassza ki a számlázás szempontjából a kapcsolódó Azure-előfizetést.

3. Az a **Azure SQL Server** mezőben válassza ki a meglévő SQL database-kiszolgálót.

4. Az a **Azure SQL Database** mezőben válassza ki a meglévő SQL-adatbázis.

5. Az a **szinkronizálási irányok** mezőt, válassza ki kétirányú szinkronizálás, a Hub vagy az Eseményközpont.

    ![Egy új SQL-adatbázis szinkronizálási tag hozzáadása](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6. Az a **felhasználónév** és **jelszó** mezők, adja meg a meglévő hitelesítő adatait az SQL Database-kiszolgálóhoz, amelyen a tag adatbázis is található. Ne adjon meg *új* ebben a szakaszban a hitelesítő adatokat.

7. Válassza ki **OK** és várja meg a létrehozott és telepített új szinkronizálási tag.

    ![Új SQL-adatbázis szinkronizálási tag hozzáadva](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Adjon hozzá egy helyszíni SQL Server-adatbázisból

Az a **Tagadatbázis** szakaszt, ha szükséges, hozzáadhat egy helyszíni SQL Server, a szinkronizálási csoport kiválasztásával **egy helyszíni adatbázis hozzáadása**. A **konfigurálása a helyszíni** lap megnyitásakor.

Az a **konfigurálása a helyszíni** lapon, tegye a következőket:

1. Válassza ki **szinkronizációs ügynök átjárójának kiválasztása**. A **szinkronizációs ügynök kiválasztása** lap megnyitásakor.

    ![Szinkronizációs ügynök átjárójának kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2. Az a **szinkronizációs ügynök átjárójának kiválasztása** lapon, döntse el, hogy használjon egy meglévő ügynököt, vagy hozzon létre egy új ügynököt.

    Ha úgy döntött **meglévő ügynökök**, válassza ki a meglévő ügynököt a listából.

    Ha úgy döntött **hozzon létre egy új ügynök**, tegye a következőket:

   1. A megadott hivatkozás szinkronizálási ügynök ügyfélszoftver letöltése, és telepítse a számítógépen, ahol az SQL Server is található. Az adatok közvetlenül a sync-ügynök letölthető [SQL Azure Data Sync-ügynök](https://www.microsoft.com/download/details.aspx?id=27693).

        > [!IMPORTANT]
        > Nyissa meg a kimenő 1433-as TCP-port a tűzfalon, hogy az ügyfél-ügynök kommunikálni a kiszolgálóval rendelkezik.

   2. Adjon meg egy nevet az ügynököt.

   3. Válassza ki **létrehozása és a kulcs létrehozásához**.

   4. Az ügynök kulcs másolása a vágólapra.

        ![Új szinkronizációs ügynök létrehozása](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

   5. Válassza ki **OK** gombra kattintva zárja be a **szinkronizációs ügynök kiválasztása** lapot.

   6. Az SQL Server-számítógépen keresse meg és futtassa az ügyfél-szinkronizálási ügynök alkalmazást.

        ![Az adatok szinkronizálása az ügyfélalkalmazás-ügynök](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

   7. Válassza ki a szinkronizálási ügynök alkalmazásban **Ügynökkulcs elküldése**. A **szinkronizálási metaadat-adatbázis konfigurációja** párbeszédpanel nyílik meg.

   8. Az a **szinkronizálási metaadat-adatbázis konfigurációja** párbeszédablakban illessze be az Azure Portalról másolt ügynök kulcs. A meglévő hitelesítő adatokat is biztosít az Azure SQL Database-kiszolgáló, amelyen a metaadatokat tároló adatbázis is található. (Egy új metaadatokat tároló adatbázis hozta létre, ez az adatbázis-e a központi adatbázis ugyanazon a kiszolgálón.) Válassza ki **OK** és várjon, amíg a konfiguráció befejezéséhez.

        ![Adja meg az ügynök kulcs és a kiszolgáló hitelesítő adatai](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Ha a tűzfal hibaüzenet jelenik meg ezen a ponton, akkor hozzon létre egy tűzfalszabályt az Azure-ban, hogy az SQL Server számítógép érkező bejövő forgalmat. A szabály manuálisan is létrehozhatja, a portálon, de előfordulhat, hogy ez egyszerűbbé teszi az SQL Server Management Studio (SSMS) létrehozásához. Az ssms-ben próbálja meg kapcsolódni a központi adatbázis az Azure-ban. Adja meg a nevét, < hub_database_name >. database.windows.net. Az Azure tűzfalszabály beállításához kövesse a párbeszédpanel. Ezután térjen vissza az ügyfél-szinkronizálási ügynök alkalmazást.

   9. Az ügyfél-szinkronizálási ügynök alkalmazásban kattintson **regisztrálása** -ügynökkel rendelkező SQL Server-adatbázis regisztrálásához. A **SQL Server-konfigurációs** párbeszédpanel nyílik meg.

        ![Adja hozzá, és a egy SQL Server-adatbázis konfigurálása](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

   10. Az a **SQL Server-konfigurációs** párbeszédpanelen válassza ki, hogy az SQL Server-hitelesítés vagy a Windows-hitelesítés használatával csatlakoznak-e. SQL Server-hitelesítést választotta, adja meg a meglévő hitelesítő adatait. Adja meg az SQL Server nevét és a szinkronizálni kívánt adatbázis nevét. Válassza ki **kapcsolat tesztelése** a beállítások teszteléséhez. Ezután válassza a **Save** (Mentés) lehetőséget. A listában megjelenik a regisztrált adatbázis.

        ![SQL Server-adatbázis most már regisztrálva van](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

   11. Most már bezárhatja az ügyfél-szinkronizálási ügynök alkalmazást.

   12. A portálon a a **konfigurálása a helyszíni** lapon jelölje be **válassza ki az adatbázist.** A **adatbázis választása** lap megnyitásakor.

   13. Az a **adatbázis választása** lap a **szinkronizálási tag neve** mezőben adjon meg egy nevet az új szinkronizálási tag. Ez a név nem azonos azzal a maga az adatbázis nevét. Válassza ki az adatbázist a listából. Az a **szinkronizálási irányok** mezőt, válassza ki kétirányú szinkronizálás, a Hub vagy az Eseményközpont.

        ![Jelölje ki a helyi adatbázist](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

   14. Válassza ki **OK** gombra kattintva zárja be a **adatbázis választása** lapot. Válassza ki **OK** gombra kattintva zárja be a **konfigurálása a helyszíni** lapon, és várja meg a létrehozott és telepített új szinkronizálási tag. Végül kattintson **OK** gombra kattintva zárja be a **szinkronizálási tagok kiválasztása** lapot.

        ![A helyi adatbázis hozzáadódik a szinkronizálási csoport](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3. Szeretne csatlakozni az SQL Data Sync és a helyi ügynök, vegye fel a felhasználó nevét a szerepkör `DataSync_Executor`. Adatok szinkronizálása a szerepkör az SQL Server-példányt hoz létre.

## <a name="step-3---configure-sync-group"></a>3. lépés – a szinkronizálási csoport konfigurálása

Miután létrehozott és telepített, 3. lépés az új szinkronizálási csoport tagjait **szinkronizálási csoport konfigurálása**, kiemelten jelenik meg a **új szinkronizálási csoport** lap.

1. Az a **táblák** lapon, a szinkronizálási csoport tagjai listájából válasszon ki egy adatbázist, és válassza **frissítési séma**.

2. Rendelkezésre álló táblák listájánál jelölje ki a szinkronizálni kívánt táblák.

    ![Szinkronizálni kívánt táblák kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3. A táblázat összes oszlopa alapértelmezés szerint ki van jelölve. Ha nem szeretné szinkronizálni az összes oszlopot, tiltsa le a jelölőnégyzetet a az oszlopokat, amelyeket nem szeretne szinkronizálni. Mindenképpen hagyja kiválasztva elsődleges kulcsának oszlopa.

    ![Szinkronizálni kívánt mezők kiválasztása](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4. Végül válassza **mentése**.

## <a name="faq-about-setup-and-configuration"></a>Beállítás és konfiguráció – gyakori kérdések

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Milyen gyakran is Data Sync szinkronizálni az adatokat

A riasztást kiváltó szinkronizálások közötti minimális időtartama öt perc alatt.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>Nem az SQL Data Sync teljes létrehozása és kiépítése az táblák

Ha a szinkronizálási séma táblák már nem készülnek a céladatbázisban, az SQL Data Sync hoz létre őket a kiválasztott oszlopok. Azonban ezt a viselkedést eredményez a teljes visszaadása séma a következő okok miatt:

- Csak a kiválasztott oszlopok jönnek létre a céltáblázatban. Ha a forrástáblákhoz egyes oszlopai nem a szinkronizálási csoport része, ezek az oszlopok nem vannak kiépítve a céltáblákba.
- Indexek csak a kijelölt oszlopok jön létre. Ha a forrás táblázatindexhez oszlopokat, amelyek nem szerepelnek a szinkronizálási csoport van, ezek az indexek nem vannak kiépítve a céltáblákba.
- XML-típusú oszlopok indexei kiépítése nem történt meg.
- ELLENŐRZÉSI korlátozásokban kiépítése nem történt meg.
- A forrástáblákból a meglévő eseményindítók nincsenek kiépítve.
- Nézetek és tárolt eljárások nem jönnek a céladatbázisba.

Ezek a korlátozások miatt javasoljuk, hogy az alábbiakat:

- Éles környezetekben üzembe helyezhető a teljes hűségű séma saját magának.
- A szolgáltatás kipróbálásához, az SQL Data Sync automatikus kiépítés funkcióját is működik.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Miért látok a táblákat, amelyek nem tudok hozott létre

Adatszinkronizálás ügyféloldali táblák az adatbázisban a change Tracking szolgáltatáshoz hoz létre. Ne törölje őket, vagy Data Sync szolgáltatással nem működik.

### <a name="is-my-data-convergent-after-a-sync"></a>Az adatok egy irányba a szinkronizálás után

Nem feltétlenül. A szinkronizálási csoport hubbal, és három küllők (A, B és C) a szinkronizálás a következők hubot, hogy A Hub b és c Hub Ha egy adatbázishoz módosításakor *után* a hubhoz való szinkronizálást, módosítás nem írt adatbázis B vagy C adatbázis a következő szinkronizálási feladat.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Hogyan szerezhetem be a sémamódosítások egy szinkronizálási csoporthoz

Győződjön meg arról, és minden sémaváltozások propagálása manuálisan kell.

1. A sémaváltozások manuálisan replikálja a hub és az összes szinkronizálási tagok.
2. Szinkronizálási sémájának frissítéséhez.

**Új táblák és oszlopok hozzáadásával**.

Új táblák és oszlopok ne befolyásolják a jelenlegi szinkronizálási. Adatszinkronizálás figyelmen kívül hagyja az új táblák és oszlopok mindaddig, amíg a szinkronizálási sémához hozzá őket. Új adatbázis-objektumok hozzáadásakor ezzel esetén kövesse az ajánlott feladatütemezési:

1. A hub és az összes szinkronizálási tagok adja hozzá az új táblákat vagy oszlopokat.
2. Az új táblák vagy oszlopok hozzáadása a szinkronizálási sémához.
3. Indítsa el az új táblák és oszlopok értékeket beszúrni.

**Egy oszlop adattípusának módosítására**.

Amikor módosítja egy létező oszlop adattípusát, a Data Sync továbbra is működik, mindaddig, amíg az új értékeket az eredeti adattípus a szinkronizálási sémában meghatározott elemnévvel illeszkednek. Például, ha a forrás-adatbázisban a típusának módosítása **int** való **bigint**, Data Sync továbbra is működni, amíg nem szúr be egy értéket, amely túl nagy a **int** adattípus . Hajtsa végre a módosítást, manuálisan replikálja a sémaváltozás, a hub és az összes szinkronizálási tagok, és frissítse a szinkronizálási sémához.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Hogyan tudok exportálhat és importálhat egy adatbázist a Data Sync szolgáltatással

Egy adatbázis-exportálását követően egy `.bacpac` fájlt, és importálja a fájlt egy új adatbázis létrehozásához, Data Sync használata az új adatbázis a következő két dolgot be kell:

1. Az a Data Sync objektumok és a kiszolgálóoldali táblák megtisztításához a **új adatbázis** használatával [Ez a szkript](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Ez a szkript az összes Data Sync szükséges objektum törli az adatbázisból.
2. Hozza létre újra a szinkronizálási csoport az új adatbázissal. Ha már nincs szüksége a régi szinkronizálási csoport, törölje azt.

## <a name="faq-about-the-client-agent"></a>Az ügyfélügynök – gyakori kérdések

Az ügyfélügynök – gyakori kérdések, lásd: [ügynök – gyakori kérdések](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>További lépések

Gratulálunk! Létrehozott egy szinkronizálási csoport, amely tartalmazza az SQL Database-példány és a egy SQL Server-adatbázist.

További információ az SQL Data Syncről:

-   Áttekintés – [szinkronizálja az adatokat több felhőalapú és helyszíni adatbázis között az Azure SQL Data Sync szolgáltatással](sql-database-sync-data.md)
-   Data Sync beállítása
    - A PowerShell-lel
        -  [A PowerShell használata több Azure SQL Database-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [A PowerShell használata egy Azure-beli SQL Database-adatbázis és egy helyszíni SQL Server-adatbázis közötti szinkronizáláshoz](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Adatok szinkronizálása az ügynök - [adatok szinkronizálása az Azure SQL Data Sync ügynök](sql-database-data-sync-agent.md)
-   Ajánlott eljárások – [ajánlott eljárások az Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   A figyelő - [a Log Analytics használatával az SQL Data Sync monitorozása](sql-database-sync-monitor-oms.md)
-   Hibaelhárítás – [az Azure SQL Data Sync szolgáltatással kapcsolatos problémák elhárítása](sql-database-troubleshoot-data-sync.md)
-   Szinkronizálási sémájának frissítéséhez
    -   A Transact-SQL - [az Azure SQL Data Sync sémamódosítások-replikáció automatizálása](sql-database-update-sync-schema.md)
    -   PowerShell-lel – [használja a Powershellt, a meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez](scripts/sql-database-sync-update-schema.md)

További információ az SQL Database-ről:

- [Az SQL Database áttekintése](sql-database-technical-overview.md)
- [Az adatbázis életciklusának felügyelete](https://msdn.microsoft.com/library/jj907294.aspx)
