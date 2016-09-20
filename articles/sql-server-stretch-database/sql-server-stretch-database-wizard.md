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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# Első lépések: az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázsló futtatása

Az adatbázisoknak a Stretch Database-zel való használatra történő beállításához futtassa az Enable Database for Stretch (A Stretch Database használatának engedélyezése az adatbázison) varázslót.  Ebben a témakörben leírjuk, hogy milyen adatokat kell megadnia, és milyen lehetőségeket kell választania a varázslóban.

Ha többet szeretne tudni a Stretch Database alkalmazásról, olvassa el a [Stretch Database áttekintését](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Ha később letiltja a Stretch Database-t, ne feledje, hogy a Stretch Database egy táblázat vagy adatbázis számára történő letiltása nem törli a távoli objektumot. Ha törölni szeretné a távoli táblát vagy a távoli adatbázist, az Azure felügyeleti portál használatával kell azt megtennie. A távoli objektumok esetén Azure-költségek fizetendők az objektumok manuális törléséig. 

## A varázsló elindítása

1.  Az SQL Server Management Studióban, az Object Explorerben válassza ki az adatbázist, amelyen engedélyezni szeretné a Stretch Database-t.

2.  Kattintson a jobb \-gombbal a **Tasks** (Feladatok) elemre, válassza a **Stretch** lehetőséget, majd a varázsló elindításához az **Enable** (Engedélyezés) elemet.

## <a name="Intro"></a>Introduction (Bevezetés)
Itt olvashat a varázsló céljáról, illetve előfeltételeiről.

A fontos előfeltételek a következők:

-   Rendszergazdának kell lennie az adatbázis-beállítások módosításához.
-   Rendelkeznie kell Microsoft Azure-előfizetéssel.
-   Az SQL Servernek képesnek kell lennie kommunikálni a távoli Azure-kiszolgálóval.

![A Stretch Database varázsló Introduction (Bevezetés) lapja][StretchWizardImage1]

## <a name="Tables"></a>Select tables (Táblák kiválasztása)
Itt választhatja ki azokat a táblákat, amelyeken engedélyezni kívánja a Stretch Database-t.

A sok sort tartalmazó táblák a rendezett lista tetején jelennek meg. Mielőtt a varázsló megjeleníti a táblák listáját, elemzi azokat, és meghatározza, hogy tartalmaznak-e a Stretch Database által jelenleg nem támogatott adattípusokat.

![A Stretch Database varázsló Select tables (Táblák kiválasztása) lapja][StretchWizardImage2]

|Oszlop|Leírás|
|----------|---------------|
|(nincs cím)|Az ebben az oszlopban található jelölőnégyzetek bejelölésével választhatja ki azokat a táblákat, amelyen engedélyezni kívánja a felhőbe való archiválást.|
|**Name (Név)**|A táblában található oszlopok nevét adja meg.|
|(nincs cím)|Ebben az oszlopban egy szimbólum egy olyan figyelmeztetést \'jelezhet, amely nem akadályozza meg azt, hogy a kiválasztott táblát engedélyezze a Stretch számára. Egy olyan hátráltató tényezőt is jelezhet, amely megakadályozza, hogy a kiválasztott táblát engedélyezze a Stretch számára – \-például azért, mert a táblázat nem támogatott adattípust használ. Vigye a kurzort a szimbólum fölé, ha szeretne további információkhoz jutni. Ezek az elemleírásban jelennek meg. További információ: [Limitations for Stretch Database](sql-server-stretch-database-limitations.md) (A Stretch Database korlátozásai).|
|**Stretched (Felhőbe archiválva)**|Jelzi, hogy a tábla már engedélyezve lett a Stretch számára.|
|**Migrate (Áttelepítés)**|Áttelepíthet egy teljes táblát (**Entire Table** (Teljes tábla)), vagy meghatározhat egy szűrőt a tábla egy meglévő oszlopára vonatkozóan. Ha egy másik szűrőfüggvényt szeretne használni az áttelepíteni kívánt sorok kiválasztásához, futtassa az ALTER TABLE utasítást a szűrő meghatározásához a varázslóból való kilépés után. A szűrőfüggvénnyel kapcsolatos további információk: [Select rows to migrate by using a filter function](sql-server-stretch-database-predicate-function.md) (Áttelepíteni kívánt sorok kiválasztása szűrőfüggvény használatával). A függvény használatával kapcsolatos további információk: [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md) (A Stretch Database használatának engedélyezése táblákon) vagy [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Rows (Sorok)**|A táblában található sorok számát jeleníti meg.|
|**Size (Méret) (KB)**|A tábla méretét adja meg KB-ban.|

## <a name="Filter"></a>Sorszűrő megadása (nem kötelező)

Ha szűrőfüggvényt szeretne biztosítani az áttelepíteni kívánt sorok kiválasztásához, tegye a következőket a **Select tables** (Táblák kiválasztása) oldalon.

1.  A **Select the tables you want to stretch** (Felhőbe archiválandó táblák kiválasztása) listában kattintson a táblához tartozó sorban található **Entire Table** (Teljes tábla) lehetőségre. Megjelenik a **Select rows to stretch** (Felhőbe archiválandó sorok kiválasztása) párbeszédpanel.

    ![Szűrőfüggvény meghatározása][StretchWizardImage2a]

2.  A **Select rows to stretch** (Felhőbe archiválandó sorok kiválasztása) párbeszédpanelen válassza a **Choose Rows** (Sorok kiválasztása) lehetőséget.

3.  A **Name** (Név) mezőben adja meg a szűrőfüggvény nevét.

4.  A **Where** záradékhoz válasszon ki egy oszlopot a táblából, válasszon egy operátort, és adjon meg egy értéket.

5. Kattintson a **Check** (Ellenőrzés) elemre a függvény teszteléséhez. Ha a függvény visszaad eredményeket a táblából – vagy ha vannak olyan áttelepíteni kívánt sorok, amelyek teljesítik a feltételt –, a teszt a **Success** (Siker) eredményt jeleníti meg.

    >   [AZURE.NOTE] A szűrőlekérdezést megjelenítő szövegmező írásvédett. Nem szerkesztheti a szövegmezőben lévő lekérdezést.

6.  A Done (Kész) gombra kattintva visszaléphet a **Select tables** (Táblák kiválasztása) lapra.

A szűrőfüggvény csak akkor jön létre az SQL Serveren, amikor befejezi a varázslót. Addig visszatérhet a **Select tables** (Táblák kiválasztása) oldalra a szűrőfüggvény módosításához vagy átnevezéséhez.

![A Select tables (Táblák kiválasztása) oldal egy szűrőfüggvény meghatározása után][StretchWizardImage2b]

Ha egy másik típusú szűrőfüggvényt szeretne használni az áttelepíteni kívánt sorok kiválasztásához, tegye a következők egyikét.  

-   Lépjen ki a varázslóból, majd futtassa az ALTER TABLE utasítást a Stretch a tábla számára történő engedélyezéséhez és egy szűrőfüggvény megadásához. Tovább információ: [Enable Stretch Database for a table](sql-server-stretch-database-enable-table.md) (A Stretch Database használatának engedélyezése táblákon).  

-   Miután kilépett a varázslóból, futtassa az ALTER TABLE utasítást egy szűrőfüggvény megadásához. A szükséges lépéseket lásd: [Add a filter function after running the Wizard](sql-server-stretch-database-predicate-function.md#addafterwiz) (Szűrőfüggvény hozzáadása a varázsló futtatása után).

## <a name="Configure"></a>Az Azure-telepítés konfigurálása

1.  Jelentkezzen be a Microsoft Azure-ba Microsoft-fiókjával.

    ![Bejelentkezés az Azure-ba – Stretch Database varázsló][StretchWizardImage3]

2.  Válassza ki a Stretch Database-hez használni kívánt meglévő Azure-előfizetést.

3.  Válassza ki a kívánt Azure-régiót.
    -   Ha új kiszolgálót hoz létre, az ebben a régióban fog létrejönni.  
    -   Ha rendelkezik meglévő kiszolgálókkal a kiválasztott régióban, a varázsló megjeleníti azok listáját, amikor az **Existing server** (Meglévő kiszolgáló) elemet választja.

    A késés csökkentése érdekében érdemes azt az Azure-régiót választani, amelyben az SQL Server is található. A régiókról további információkat tudhat meg [Az Azure régiói](https://azure.microsoft.com/regions/) című cikkből.

4.  Adja meg, hogy egy meglévő kiszolgálót szeretne használni, vagy új Azure-kiszolgálót szeretne létrehozni.

    Ha az SQL Serveren működő Active Directory össze van vonva az Azure Active Directoryval, a távoli Azure-kiszolgálóval való kommunikációra összevont SQL Server-szolgáltatásfiókot is használhat. E lehetőség követelményeivel kapcsolatban további információkat az [ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx) (Az ALTER DATABASE SET parancs beállításai (Transact-SQL)) című cikkből tudhat meg.

    -   **Create new server (Új kiszolgáló létrehozása)**

        1.  Adja meg a kiszolgáló-rendszergazda felhasználónevét és jelszavát.

        2.  Opcionális megoldásként akár összevont SQL Server-szolgáltatásfiókot is használhat a távoli Azure-kiszolgálóval való kommunikációhoz.

        ![Új Azure-kiszolgáló létrehozása – Stretch Database varázsló][StretchWizardImage4]

    -   **Existing server (Meglévő kiszolgáló)**

        1.  Válassza ki a meglévő Azure-kiszolgálót.

        2.  Válassza ki a hitelesítési módot.

            -   Ha az **SQL Server Authentication** (SQL Server-hitelesítés) lehetőséget választja, adja meg a rendszergazdai bejelentkezési azonosítót és jelszót.

            -   Ha összevont SQL Server-szolgáltatásfiókot szeretne használni a távoli Azure-kiszolgálóval való kommunikációhoz, válassza az **Active Directory Integrated Authentication** (Integrált Active Directory-hitelesítés) lehetőséget. Ha a kiválasztott kiszolgáló nincs integrálva az Azure Active Directoryval, ez a lehetőség nem jelenik meg.

        ![Meglévő Azure-kiszolgáló kiválasztása – Stretch Database varázsló][StretchWizardImage5]

## <a name="Credentials"></a>Secure credentials (Biztonságos hitelesítő adatok)
Ahhoz, hogy biztonságossá tegye a Stretch Database által a távoli adatbázishoz való kapcsolódáshoz használt adatokat, mindenképp be kell állítania az adatbázisfőkulcsot.  

Ha már létezik adatbázisfőkulcs, adja meg annak jelszavát.  

![A Stretch Database varázsló Secure credentials (Biztonságos hitelesítő adatok) lapja][StretchWizardImage6b]

Ha az adatbázis nem rendelkezik meglévő főkulccsal, adjon meg egy erős jelszót az adatbázisfőkulcs létrehozásához.  

![A Stretch Database varázsló Secure credentials (Biztonságos hitelesítő adatok) lapja][StretchWizardImage6]

Az adatbázisfőkulccsal kapcsolatos további információkat a [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) és a [Create a Database Master Key](https://msdn.microsoft.com/library/aa337551.aspx) (Adatbázisfőkulcs létrehozása) című cikkekben talál. A varázsló által létrehozott hitelesítő adatokkal kapcsolatos további információk: [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Select IP address (IP-cím kiválasztása)
Használja az alhálózati IP-címtartományát (javasolt) vagy az SQL Server nyilvános IP-címét egy olyan tűzfalszabály létrehozásához az Azure-ban, amely lehetővé teszi, hogy az SQL Server kommunikáljon a távoli Azure-kiszolgálóval.

Az ezen a lapon megadott IP-cím vagy -címek azt közlik az Azure-kiszolgálóval, hogy engedélyezze az SQL Server által indított bejövő adatok, lekérdezések és felügyeleti műveletek áthaladását az Azure-tűzfalon. A varázsló az SQL Serveren megadott tűzfalbeállításokat nem módosítja.

![A Stretch Database varázsló Select IP address (IP-cím kiválasztása) lapja][StretchWizardImage7]

## <a name="Summary"></a>Összefoglalás
Tekintse át a megadott értékeket és a varázslóban kiválasztott beállításokat, valamint a becsült költségeket az Azure-ban. A felhőbe való archiválás engedélyezéséhez válassza a **Finish** (Befejezés) lehetőséget.

![A Stretch Database varázsló Summary (Összefoglalás) lapja][StretchWizardImage8]

## <a name="Results"></a>Results (Eredmények)
Itt tekintheti meg a művelet eredményét.

Az adatáttelepítés állapotának megfigyelésének módjával kapcsolatban tekintse meg a [Monitor and troubleshoot data migration (Stretch Database)](sql-server-stretch-database-monitor.md) (Az adatáttelepítés megfigyelése és hibaelhárítása (Stretch Database)) című témakört.

![A Stretch Database varázsló Results (Eredmények) lapja][StretchWizardImage9]

## <a name="KnownIssues"></a>A varázslóban előforduló hibák elhárítása
**A Stretch Database varázsló hibát jelzett.**
A varázsló hibát jelez, ha a kiszolgáló szintjén még nincs engedélyezve a Stretch Database, és úgy Ön futtatja a varázslót, hogy nem rendelkezik a Stretch Database engedélyezéséhez szükséges rendszergazdai engedélyekkel. Kérje meg a rendszergazdát, hogy engedélyezze a Stretch Database-t a helyi kiszolgálópéldányon, majd futtassa ismét a varázslót. További információk: [Prerequisite: Permission to enable Stretch Database on the server](sql-server-stretch-database-enable-database.md#EnableTSQLServer) (Előfeltétel: a Stretch Database kiszolgálón való engedélyezéséhez szükséges engedély).

## Következő lépések
Engedélyezze a Stretch Database használatát további táblákon. \-Figyelje az adatáttelepítést, és kezelje a felhőben való archiválásra engedélyezett adatbázisokat és táblákat.

-   A további táblákon való használathoz [engedélyezze a Stretch Database használatát a különböző táblákon](sql-server-stretch-database-enable-table.md).

-   Az adatáttelepítés állapotának megtekintéséhez [figyelje meg az adatáttelepítést, és hárítsa el a hibákat](sql-server-stretch-database-monitor.md).

-   [Pause and resume Stretch Database (A Stretch Database folyamatának szüneteltetése és folytatása)](sql-server-stretch-database-pause.md)

-   [Manage and troubleshoot Stretch Database (A Stretch Database felügyelete és hibáinak elhárítása)](sql-server-stretch-database-manage.md)

-   [Backup Stretch-enabled databases (A Stretch szolgáltatást használó adatbázisok biztonsági mentése)](sql-server-stretch-database-backup.md)

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
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png



<!--HONumber=sep16_HO1-->


