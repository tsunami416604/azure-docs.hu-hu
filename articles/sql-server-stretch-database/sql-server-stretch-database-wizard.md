<properties
    pageTitle="Első lépések: az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatása | Microsoft Azure"
    description="Ebből a cikkből megtanulhatja, hogyan konfigurálhatja adatbázisait a Stretch Database-zel való használatra az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatásával."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager=""
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/17/2016"
    ms.author="douglasl"/>

# Első lépések: az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatása

Az adatbázisoknak a Stretch Database-zel való használatra történő beállításához futtassa az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázslót.  Ebben a témakörben leírjuk, hogy milyen adatokat kell megadnia, és milyen lehetőségeket kell választania a varázslóban.

Ha többet szeretne tudni a Stretch Database alkalmazásról, olvassa el a [Stretch Database áttekintését](sql-server-stretch-database-overview.md).

## A varázsló elindítása

1.  Az SQL Server Management Studióban, az Object Explorerben válassza ki az adatbázist, amelyen engedélyezni szeretné a Stretch Database-t.

2.  Kattintson a jobb gombbal a **Tasks** (Feladatok) elemre, válassza a **Stretch** lehetőséget, majd a varázsló elindításához az **Enable** (Engedélyezés) elemet.

## <a name="Intro"></a>Introduction (Bevezetés)
Itt olvashat a varázsló céljáról, illetve előfeltételeiről.

![A Stretch Database varázsló Introduction (Bevezetés) lapja][StretchWizardImage1]

## <a name="Tables"></a>Select tables (Táblák kiválasztása)
Itt választhatja ki azokat a táblákat, amelyeken engedélyezni kívánja a Stretch Database-t.

![A Stretch Database varázsló Select tables (Táblák kiválasztása) lapja][StretchWizardImage2]

|Oszlop|Leírás|
|----------|---------------|
|(nincs cím)|Az ebben az oszlopban található jelölőnégyzetek bejelölésével választhatja ki azokat a táblákat, amelyen engedélyezni kívánja a felhőbe való archiválást.|
|**Name (Név)**|A táblában található oszlopok nevét adja meg.|
|(nincs cím)|Ha ebben az oszlopban egy szimbólumot lát, az általában azt jelzi, hogy a kiválasztott táblán nem engedélyezheti a felhőbe való archiválást, mert ezt valamilyen probléma megakadályozza. Ilyen körülmény lehet például, ha a tábla nem támogatott adattípust használ. Vigye a kurzort a szimbólum fölé, ha szeretne további információkhoz jutni. Ezek az elemleírásban jelennek meg. További információk: [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md) (A Stretch Database felületi korlátozásai, illetve a használatát akadályozó problémák).|
|**Stretched (Felhőbe archiválva)**|Azt adja meg, hogy engedélyezve van-e a táblán ez a funkció.|
|**Migrate (Áttelepítés)**|Áttelepítheti a teljes táblát (**Entire Table** lehetőség), de a varázslóban dátumalapú szűrőpredikátumot is megadhat. Ha egy másik szűrőpredikátumot szeretne használni az áttelepítendő sorok kiválogatására, miután bezárta a varázslót, a szűrőpredikátum meghatározásához futtassa az ALTER TABLE utasítást. A szűrőpredikátumról további információkat talál a következő cikkben: [Use a filter predicate to select rows to migrate (Stretch Database)](sql-server-stretch-database-predicate-function.md) (Áttelepítendő sorok kiválasztása szűrőpredikátum segítségével (Stretch Database)). A predikátum alkalmazásával kapcsolatos további információkért lásd a következő cikkeket: [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md) (A Stretch Database engedélyezése a táblákon) és [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Rows (Sorok)**|A táblában található sorok számát jeleníti meg.|
|**Size (Méret) (KB)**|A tábla méretét adja meg KB-ban.|

## <a name="Filter"></a>Dátumalapú szűrőpredikátum megadása (nem kötelező)

Ha az áttelepítendő sorok kiválasztásához dátumalapú szűrőpredikátumot szeretne megadni, tegye a következőket a **Select tables** (Táblák kiválasztása) lapon.

1.  A **Select the tables you want to stretch** (Felhőbe archiválandó táblák kiválasztása) listában kattintson a táblához tartozó sorban található **Entire Table** (Teljes tábla) lehetőségre. Megjelenik a **Select rows to stretch** (Felhőbe archiválandó sorok kiválasztása) párbeszédpanel.

    ![Dátumalapú szűrőpredikátum meghatározása][StretchWizardImage2a]

2.  A **Select rows to stretch** (Felhőbe archiválandó sorok kiválasztása) párbeszédpanelen válassza a **Choose Rows** (Sorok kiválasztása) lehetőséget.

3.  A **Name** (Név) mezőben adjon nevet a szűrőpredikátumnak.

4.  A **Where** záradéknál válasszon egy dátumoszlopot a táblából, válasszon operátort, majd adja meg a dátumértéket.

5. A predikátum kipróbálásához kattintson a **Check** (Ellenőrzés) gombra. Ha a predikátum eredményeket ad vissza a táblából, azaz léteznek a feltételnek megfelelő áttelepíthető sorok, a teszt eredményeként a **Success** (Siker) felirat jelenik meg.

6.  A Done (Kész) gombra kattintva visszaléphet a **Select tables** (Táblák kiválasztása) lapra.

    ![Select tables (Táblák kiválasztása) lap a szűrőpredikátum meghatározását követően][StretchWizardImage2b]

## <a name="Configure"></a>Az Azure-telepítés konfigurálása

1.  Jelentkezzen be a Microsoft Azure-ba Microsoft-fiókjával.

    ![Bejelentkezés az Azure-ba – Stretch Database varázsló][StretchWizardImage3]

2.  Válassza ki a Stretch Database-hez használni kívánt Azure-előfizetést.

3.  Válassza ki a kívánt Azure-régiót. Ha új kiszolgálót hoz létre, az ebben a régióban fog létrejönni.

    A késés csökkentése érdekében érdemes azt az Azure-régiót választani, amelyben az SQL Server is található. A régiókról további információkat tudhat meg [Az Azure régiói](https://azure.microsoft.com/regions/) című cikkből.

4.  Adja meg, hogy egy meglévő kiszolgálót szeretne használni, vagy új Azure-kiszolgálót szeretne létrehozni.

    Ha az SQL Serveren működő Active Directory össze van vonva az Azure Active Directoryval, a távoli Azure-kiszolgálóval való kommunikációra összevont SQL Server-szolgáltatásfiókot is használhat. E lehetőség követelményeivel kapcsolatban további információkat az [ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) (Az ALTER DATABASE SET parancs beállításai (Transact-SQL)) című cikkből tudhat meg.

    -   **Create new server (Új kiszolgáló létrehozása)**

        1.  Adja meg a kiszolgáló-rendszergazda felhasználónevét és jelszavát.

        2.  Opcionális megoldásként akár összevont SQL Server-szolgáltatásfiókot is használhat a távoli Azure-kiszolgálóval való kommunikációhoz.

        ![Új Azure-kiszolgáló létrehozása – Stretch Database varázsló][StretchWizardImage4]

    -   **Existing server (Meglévő kiszolgáló)**

        1.  Válassza ki vagy adja meg a meglévő Azure-kiszolgáló nevét.

        2.  Válassza ki a hitelesítési módot.

            -   Ha az **SQL Server Authentication** (SQL Server-hitelesítés) lehetőséget választja, adja meg az új felhasználónevet és jelszót.

            -   Ha összevont SQL Server-szolgáltatásfiókot szeretne használni a távoli Azure-kiszolgálóval való kommunikációhoz, válassza az **Active Directory Integrated Authentication** (Integrált Active Directory-hitelesítés) lehetőséget.

        ![Meglévő Azure-kiszolgáló kiválasztása – Stretch Database varázsló][StretchWizardImage5]

## <a name="Credentials"></a>Secure credentials (Biztonságos hitelesítő adatok)
Válasszon erős jelszót az adatbázis főkulcsának létrehozásához. Ha már rendelkezik adatbázisfőkulccsal, adja meg az ehhez tartozó jelszót.

Ahhoz, hogy biztonságossá tegye a Stretch Database által a távoli adatbázishoz való kapcsolódáshoz használt adatokat, mindenképp be kell állítania az adatbázisfőkulcsot.

![A Stretch Database varázsló Secure credentials (Biztonságos hitelesítő adatok) lapja][StretchWizardImage6]

Az adatbázisfőkulccsal kapcsolatos további információkat a [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) és a [Create a Database Master Key](https://msdn.microsoft.com/library/aa337551.aspx) (Adatbázisfőkulcs létrehozása) című cikkekben talál. A varázsló által létrehozott hitelesítő adatokkal kapcsolatos további információk: [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Select IP address (IP-cím kiválasztása)
Használja az SQL Server nyilvános IP-címét, vagy adjon meg egy IP-tartományt. Ez ahhoz szükséges, hogy a rendszer létre tudja hozni az Azure-tűzfalszabályt, amely lehetővé teszi, hogy az SQL Server kommunikáljon a távoli Azure-kiszolgálóval.

Az ezen a lapon megadott IP-cím vagy -címek azt közlik az Azure-kiszolgálóval, hogy engedélyezze az SQL Server által indított bejövő adatok, lekérdezések és felügyeleti műveletek áthaladását az Azure-tűzfalon. A varázsló az SQL Serveren megadott tűzfalbeállításokat nem módosítja.

![A Stretch Database varázsló Select IP address (IP-cím kiválasztása) lapja][StretchWizardImage7]

## <a name="Summary"></a>Summary (Összefoglalás)
Itt ellenőrizheti a varázsló előző lapjain bevitt értékeket és kiválasztott beállításokat. A felhőbe való archiválás engedélyezéséhez válassza a **Finish** (Befejezés) lehetőséget.

![A Stretch Database varázsló Summary (Összefoglalás) lapja][StretchWizardImage8]

## <a name="Results"></a>Results (Eredmények)
Itt tekintheti meg a művelet eredményét.

Ha szeretné a Stretch Database Monitor segítségével nyomon követni az adatok áttelepítésének állapotát, kattintson a **Monitor** (Figyelés) lehetőségre. További információk: [Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md) (Az adatok áttelepítésének figyelése és hibáinak elhárítása (Stretch Database)).

## <a name="KnownIssues"></a>A varázslóban előforduló hibák elhárítása
**A Stretch Database varázsló hibát jelzett.**
A varázsló hibát jelez, ha a kiszolgáló szintjén még nincs engedélyezve a Stretch Database, és úgy Ön futtatja a varázslót, hogy nem rendelkezik a Stretch Database engedélyezéséhez szükséges rendszergazdai engedélyekkel. Kérje meg a rendszergazdát, hogy engedélyezze a Stretch Database-t a helyi kiszolgálópéldányon, majd futtassa ismét a varázslót. További információk: [Prerequisite: Permission to enable Stretch Database on the server](sql-server-stretch-database-enable-database.md#EnableTSQLServer) (Előfeltétel: a Stretch Database kiszolgálón való engedélyezéséhez szükséges engedély).

## Következő lépések
Engedélyezze a Stretch Database használatát további táblákon. Figyelje az adatáttelepítést, és kezelje a felhőben való archiválásra engedélyezett adatbázisokat és táblákat.

-   A további táblákon való használathoz [engedélyezze a Stretch Database használatát a különböző táblákon](sql-server-stretch-database-enable-table.md).

-   [Figyelje a Stretch Database-t](sql-server-stretch-database-monitor.md) az adatok áttelepítési állapotának ellenőrzése céljából.

-   [Pause and resume Stretch Database (A Stretch Database folyamatának szüneteltetése és folytatása)](sql-server-stretch-database-pause.md)

-   [Manage and troubleshoot Stretch Database (A Stretch Database felügyelete és hibáinak elhárítása)](sql-server-stretch-database-manage.md)

-   [Backup and restore Stretch-enabled databases (A felhőben való archiválásra adatbázisok biztonsági mentése és visszaállítása)](sql-server-stretch-database-backup.md)

## Lásd még:

[Enable Stretch Database for a database (A Stretch Database használatának engedélyezése adatbázisokon)](sql-server-stretch-database-enable-database.md)

[Enable Stretch Database for a table (A Stretch Database használatának engedélyezése táblákon)](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png



<!--HONumber=Jun16_HO2-->


