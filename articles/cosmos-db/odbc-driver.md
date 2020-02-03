---
title: Csatlakozás az Azure Cosmos DB használatával, BI elemzési eszközök
description: Ismerje meg, hogyan normalizált adatok BI és az adatok elemzési szoftver lehet megtekinteni, hogy a táblák és nézetek létrehozásához az Azure Cosmos DB ODBC-illesztő használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721081"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Csatlakozás az Azure Cosmos DB BI elemzési eszközök használata az ODBC-illesztő

Az Azure Cosmos DB ODBC-illesztő segítségével csatlakozhat az Azure Cosmos DB használatával BI elemzési eszközök, például az SQL Server Integration Services, a Power BI Desktop és a Tableau, így elemezheti és az Azure Cosmos DB-adatok vizualizációkat létrehozni a ezen megoldások.

Az Azure Cosmos DB ODBC-illesztő kompatibilis az ODBC 3.8-as verzióval, és támogatja az ANSI SQL-92 szintaxist. Az illesztő rengeteg funkciót tartalmaz, amelyek segítségével újranormalizálhatja az Azure Cosmos DB adatait. Az illesztő használatával az Azure Cosmos DB-ben található adatokat táblaként és nézetként jelenítheti meg. Az illesztő lehetővé teszi az SQL-műveletek elvégzését a táblákon és nézeteken, például a lekérdezések szerinti csoportosítást, beszúrást, frissítést és törlést.

> [!NOTE]
> Az ODBC-illesztő az Azure Cosmos DB-összekötő csak az Azure Cosmos DB SQL API-fiókok jelenleg támogatott.

## <a name="why-do-i-need-to-normalize-my-data"></a>Miért kell normalizálása az adataimat?
Az Azure Cosmos DB egy séma nélküli adatbázis, amely lehetővé teszi alkalmazások gyors fejlesztésére és az adatmodellek anélkül, hogy folyamatban van egy szigorú sémát korlátozódik ismételt futtatásával lehetővé teszi a. Egyetlen Azure Cosmos-adatbázis tartalmazhat különböző szerkezetű JSON-dokumentumokat. Ez kiválóan alkalmazható az alkalmazások gyors fejlesztésére, de azt szeretné, elemzésére és az adatok adatelemzés és BI-eszközök használatával jelentéseket hozhat létre, amikor az adatok gyakran kell egybesimított és a egy adott sémára formátumhoz.

Ez az, ahol az ODBC-illesztőprogram érhető el. Az ODBC-illesztő használatával az adatokat az Azure Cosmos DB-táblák és nézetek, amelyek illeszkednek az adatelemzési és jelentéskészítési szükségletek most normalizálását. A renormalized sémák nincs hatással az alapul szolgáló adatokat, és nem korlátozza a fejlesztők igazodnia kell őket. Ehelyett lehetővé teszik az ODBC-kompatibilis eszközeivel férhet hozzá az adatokhoz. Így most az Azure Cosmos-adatbázis nem csak a kedvenc fejlesztői csapatának, hanem az adatelemzők is szeretik.

Kezdjük az ODBC-illesztőt.

## <a id="install"></a>1. lépés: az Azure Cosmos DB ODBC-illesztő telepítése

1. Töltse le az illesztőprogramokat az adott környezetben:

    | Telepítő | Támogatott operációs rendszerek| 
    |---|---| 
    |[Microsoft Azure Cosmos db ODBC 64-bit. msi](https://aka.ms/cosmos-odbc-64x64) a 64 bites Windows rendszerekhez| 64 bites Windows 8.1 vagy újabb, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos db ODBC 32x64-bit. msi](https://aka.ms/cosmos-odbc-32x64) for 32-bit 64-bites Windows rendszeren| 64 bites Windows 8.1 vagy újabb, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 és Windows Server 2003.| 
    |[Microsoft Azure Cosmos db ODBC 32-bit. msi](https://aka.ms/cosmos-odbc-32x32) a 32 bites Windows rendszerekhez|32 bites Windows 8.1 vagy újabb, Windows 8, Windows 7, Windows XP és Windows Vista.|

    Futtassa helyileg az msi-fájlt, amely elindítja az **Microsoft Azure Cosmos db ODBC illesztőprogram-telepítő varázslót**. 

1. A bemeneti az ODBC-illesztőprogram telepítéséhez az alapértelmezett telepítési varázsló befejezése.

1. Nyissa meg az **ODBC adatforrás-felügyeleti** alkalmazást a számítógépen. Ezt úgy teheti meg, hogy beírja az **ODBC-adatforrásokat** a Windows keresési mezőjébe. 
    Az illesztőprogram telepítésének megerősítéséhez kattintson az **illesztőprogramok** lapra, és győződjön meg arról, **Microsoft Azure Cosmos db ODBC-illesztő** szerepel a felsorolásban.

    ![Az Azure Cosmos DB ODBC Data Source Administrator](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>2. lépés: Kapcsolódás az Azure Cosmos-adatbázishoz

1. [Az Azure Cosmos db ODBC-illesztő telepítése](#install)után az **ODBC-adatforrás rendszergazdája** ablakban kattintson a **Hozzáadás**gombra. Létrehozhat egy felhasználói vagy Rendszeradatforrás. Ebben a példában létrehoz egy felhasználói DSN.

1. Az **új adatforrás létrehozása** ablakban válassza ki **Microsoft Azure Cosmos db ODBC-illesztő**elemet, majd kattintson a **Befejezés**gombra.

1. Az **Azure Cosmos db ODBC-ILLESZTŐ Sdn beállítása** ablakban adja meg a következő információkat: 

    ![Az Azure Cosmos DB ODBC-illesztőprogram DSN-beállítások ablak](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - Adatforrás **neve**: az ODBC-DSN saját felhasználóbarát neve. Ez a név egyedi az Azure Cosmos DB-fiókja, így neki megfelelő, ha több fiókot.
    - **Description (Leírás**): az adatforrás rövid leírása.
    - **Gazdagép**: a Azure Cosmos db-fiókhoz tartozó URI. Letöltheti ezt az Azure Portalon az Azure Cosmos DB kulcsok oldalról az alábbi képernyőképen látható módon. 
    - **Hozzáférési kulcs**: a Azure Portal Azure Cosmos db kulcsok oldalának elsődleges vagy másodlagos, írható-olvasható vagy írásvédett kulcsa, ahogy az alábbi képernyőfelvételen is látható. Azt javasoljuk, hogy az írásvédett kulcsát, használható, ha a DSN csak olvasható adatok feldolgozása és a jelentéskészítés.
    ![Azure Cosmos DB kulcsok lapja](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Hozzáférési kulcs titkosítása**: válassza ki a legjobb választást a számítógép felhasználói alapján. 
    
1. Kattintson a **teszt** gombra, és ellenőrizze, hogy tud-e csatlakozni a Azure Cosmos db-fiókjához. 

1.  Kattintson a **Speciális beállítások** elemre, és állítsa be a következő értékeket:
    *  **REST API verziója**: válassza ki a [REST API verzióját](https://docs.microsoft.com/rest/api/cosmos-db/) a műveletekhez. Az alapértelmezett 2015-12-16. Ha [nagyméretű partíciós kulcsokkal](large-partition-keys.md) rendelkező tárolókkal rendelkezik, és a REST API 2018-12-31-es verziójának megkövetelése:
        - Írja be a **2018-12-31** -es verzióját REST API verzióra
        - A **Start** menüben írja be a "regedit" parancsot a rendszerleíróadatbázis- **szerkesztő** alkalmazás megkereséséhez és megnyitásához.
        - A Rendszerleíróadatbázis-szerkesztőben navigáljon a következő elérési úthoz: **számítógép \ HKEY_LOCAL_MACHINE \software\odbc\odbc. INI fájl**
        - Hozzon létre egy új alkulcsot ugyanazzal a névvel, mint a DSN, például "contoso-fiók ODBC DSN".
        - Navigáljon a "contoso-fiók ODBC DSN" alkulcshoz.
        - Kattintson a jobb gombbal egy új **karakterláncérték** hozzáadásához:
            - Érték neve: **IgnoreSessionToken**
            - Érték: **1**
            ![Rendszerleíróadatbázis-szerkesztő beállításai](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Lekérdezés konzisztenciája**: válassza ki a műveletek [konzisztenciáji szintjét](consistency-levels.md) . Az alapértelmezett érték a munkamenet.
    - **Újrapróbálkozások száma**: Itt adhatja meg, hogy a rendszer hányszor próbálkozzon újra egy művelettel, ha a kezdeti kérelem nem fejeződött be a szolgáltatási díjak korlátozása miatt.
    - **Sémafájl**: itt számos lehetőség közül választhat.
        - Alapértelmezés szerint, ha a bejegyzést (üres) hagyja, az illesztőprogram megvizsgálja az összes tárolóhoz tartozó adat első oldalát az egyes tárolók sémájának meghatározásához. Ezt nevezzük tároló-hozzárendelésnek. Egy sémafájlt definiált nélkül az illesztőprogram a vizsgálat végrehajtania az egyes illesztőprogram-munkamenet, és hatására a DSN használó alkalmazások magasabb indítási idő. Azt javasoljuk, hogy mindig társít egy sémafájlt az Adatbázisnevet.
        - Ha már rendelkezik egy sémafájl (valószínűleg a séma-szerkesztő használatával létrehozott), kattintson a **Tallózás**gombra, keresse meg a fájlt, és kattintson a **Mentés**elemre, majd **az OK**gombra.
        - Ha új sémát szeretne létrehozni, kattintson az **OK**gombra, majd kattintson a főablakban található **sémakezelő szerkesztő** elemre. Ezután folytassa a séma-szerkesztő adataival. Az új sémafájl létrehozása után ne felejtse el visszatérni a **Speciális beállítások** ablakra az újonnan létrehozott sémafájl belefoglalásához.

1. Miután elvégezte és bezárta az **Azure Cosmos db ODBC-ILLESZTŐ DSN-telepítőjének** ablakát, a rendszer hozzáadja az új felhasználói DSN-t a felhasználói DSN lapra.

    ![Új Azure Cosmos DB ODBC DSN felhasználói DSN lapon](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#container-mapping"></a>3. lépés: séma-definíció létrehozása a tároló-hozzárendelési módszer használatával

Kétféle mintavételi módszer használható: **tároló-hozzárendelés** vagy **tábla-határolójelek**. A mintavételi munkamenetek mindkét mintavételi módszert használhatják, de mindegyik tároló csak egy adott mintavételi módszert használhat. Az alábbi lépések egy sémát hoznak létre egy vagy több tárolóban lévő adataihoz a tároló-hozzárendelési módszer használatával. Ez a mintavételi módszer a tárolók oldalán lévő összes adat lekérésével határozza meg az adat szerkezetét. Egy tárolót helyez át az ODBC-oldalon található táblába. Ez a mintavételi módszer hatékony és gyors, ha a tárolóban lévő adathalmaz homogén. Ha egy tároló heterogén típusú adattípust tartalmaz, javasoljuk, hogy használja a [Table-határolójelek leképezési módszerét](#table-mapping) , mivel ez robusztusabb mintavételi módszert biztosít a tárolóban lévő adatstruktúrák meghatározásához. 

1. Miután befejezte a 1-4-es lépést az [Azure Cosmos-adatbázishoz való kapcsolódás](#connect)során, kattintson a **Azure Cosmos db ODBC-illesztő DSN-telepítő** ablakában található **Schema Editor** elemre.

    ![Az Azure Cosmos DB ODBC DSN illesztőinek ablakban séma szerkesztő gomb](./media/odbc-driver/odbc-driver-schema-editor.png)
1. A **séma-szerkesztő** ablakban kattintson az **új létrehozása**elemre.
    A **séma létrehozása** ablak megjeleníti a Azure Cosmos db fiókban található összes tárolót. 

1. Válasszon ki egy vagy több mintát a mintavételezéshez, majd kattintson a **minta**elemre. 

1. A **tervezési nézet** lapon az adatbázis, a séma és a tábla képviselteti magát. A táblázatban megtekintheti a vizsgálat megjelenítése az oszlopneveket (SQL-neve, adatforrás neve, stb.) társított tulajdonságok.
    Az oszlop SQL nevét, az SQL-típus, SQL hossza (ha van ilyen), módosíthatja az egyes oszlopok méretezés (ha van ilyen), a pontosság (ha alkalmazható) és a üresen hagyható.
    - Beállíthatja az **oszlop elrejtését** **true** értékre, ha ki szeretné zárni az oszlopot a lekérdezési eredményekből. Oszlopok jelölt oszlop elrejtése = true nem lehet megjeleníteni a kijelölés és felhasználását, bár továbbra is a séma részét képezik. Ha például az Azure Cosmos DB-hez szükséges rendszer a tulajdonságokat, "_" kezdetű elrejtheti.
    - Az **ID** oszlop az egyetlen olyan mező, amely nem rejthető el, mert a normalizált sémában elsődleges kulcsként van használatban. 

1. Miután befejezte a séma definiálását, kattintson a **fájl** | **Mentés**gombra, navigáljon a címtárhoz a séma mentéséhez, majd kattintson a **Mentés**gombra.

1. Ha a sémát DSN-sel szeretné használni, nyissa meg az **Azure Cosmos db ODBC-ILLESZTŐ DSN-telepítő ablakát** (az ODBC adatforrás-rendszergazdája használatával), kattintson a **Speciális beállítások**elemre, majd a **séma fájlja** mezőben navigáljon a mentett sémához. Meglévő DSN mentése folyamatban van egy sémafájlt módosítja a DSN-kapcsolat hatókör struktúra séma határozza meg az adatokat.

## <a id="table-mapping"></a>4. lépés: séma-definíció létrehozása a tábla-határolójelek leképezési módszerével

Kétféle mintavételi módszer használható: **tároló-hozzárendelés** vagy **tábla-határolójelek**. A mintavételi munkamenetek mindkét mintavételi módszert használhatják, de mindegyik tároló csak egy adott mintavételi módszert használhat. 

A következő lépések egy sémát hoznak létre egy vagy több tárolóban lévő adatkészletek számára a **Table-határolójelek** leképezési módszerének használatával. Javasoljuk, hogy ezt a mintavételi módszert használja, ha a tárolók heterogén adattípust tartalmaznak. Ez a módszer segítségével attribútumokat és a megfelelő értékeket a mintavételt hatókörét. Ha egy dokumentum egy "Type" tulajdonságot tartalmaz, például korlátozhatja, ez a tulajdonság értékét a mintavételezés körét. A végeredmény a mintavételi lenne minden egyes érték megadása esetén a táblák egy készlete. Írja be például = autó egy autó tábla típusú során állítja elő = Adatsík akkor az eredmény egy Adatsík táblát.

1. Miután befejezte a 1-4-es lépést az [Azure Cosmos-adatbázishoz való kapcsolódás](#connect)során, kattintson a Azure Cosmos db ODBC-illesztő DSN-telepítő ablakában található **Schema Editor** elemre.

1. A **séma-szerkesztő** ablakban kattintson az **új létrehozása**elemre.
    A **séma létrehozása** ablak megjeleníti a Azure Cosmos db fiókban található összes tárolót. 

1. Válasszon egy tárolót a **Minta nézet** lapon, a tároló **hozzárendelés definíciója** oszlopban kattintson a **Szerkesztés**elemre. Ezután a **leképezés definíciója** ablakban válassza a **tábla** határolójelek metódus elemet. Ezután tegye a következőket:

    a. Az **attribútumok** mezőbe írja be a határolójelek tulajdonság nevét. Ez a tulajdonság a dokumentumot, amelyet szeretne a mintavételt, például az városa hatókörét, és nyomja le az enter. 

    b. Ha a fent megadott attribútumhoz csak bizonyos értékekre szeretné szűkíteni a mintavételezést, jelölje ki az attribútumot a kiválasztási mezőben, adjon meg egy értéket az **érték** mezőben (például Seattle), majd nyomja le az ENTER billentyűt. Továbbra is több érték attribútumok hozzáadása. Csak győződjön meg arról, hogy a megfelelő attribútum kiválasztott értékeket adjon meg.

    Ha például a város **attribútum** értékét tartalmazza, és korlátozni szeretné a táblázatot úgy, hogy csak a New York-i és a Dubai városi értékkel rendelkező sorokat foglalja bele, akkor a City értéket kell megadnia az attribútumok mezőben, valamint a New York-i, majd a Dubai **értéket az értékek** mezőben.

1. Kattintson az **OK** gombra. 

1. A mintaként használni kívánt tárolók leképezési definícióinak befejezése után a **sémakezelő** ablakban kattintson a **minta**elemre.
     Az oszlop SQL nevét, az SQL-típus, SQL hossza (ha van ilyen), módosíthatja az egyes oszlopok méretezés (ha van ilyen), a pontosság (ha alkalmazható) és a üresen hagyható.
    - Beállíthatja az **oszlop elrejtését** **true** értékre, ha ki szeretné zárni az oszlopot a lekérdezési eredményekből. Oszlopok jelölt oszlop elrejtése = true nem lehet megjeleníteni a kijelölés és felhasználását, bár továbbra is a séma részét képezik. Elrejtheti például az összes Azure Cosmos DB rendszerkövetelményt a `_`tól kezdődően.
    - Az **ID** oszlop az egyetlen olyan mező, amely nem rejthető el, mert a normalizált sémában elsődleges kulcsként van használatban. 

1. Miután befejezte a séma definiálását, kattintson a **fájl** | **Mentés**gombra, navigáljon a címtárhoz a séma mentéséhez, majd kattintson a **Mentés**gombra.

1. A **Azure Cosmos db ODBC-ILLESZTŐ DSN-telepítő** ablakában kattintson a **Speciális beállítások**elemre. Ezután a **sémafájl** mezőben navigáljon a mentett sémafájl elemhez, és kattintson az **OK**gombra. Kattintson ismét az **OK** gombra a DSN mentéséhez. Ez a séma hozott létre a DSN menti. 

## <a name="optional-set-up-linked-server-connection"></a>(Nem kötelező) A csatolt kiszolgáló-kapcsolat beállítása

Azure Cosmos DB az SQL Server Management Studio (SSMS) kapcsolatot hoz létre csatolt kiszolgáló beállításával lekérdezheti.

1. Hozzon létre egy rendszeradatforrást a [2. lépés](#connect), például a `SDS Name`néven leírtak szerint.

1. [Telepítse a SQL Server Management Studiot](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) , és kapcsolódjon a kiszolgálóhoz. 

1. A SSMS lekérdezési szerkesztőjében hozzon létre egy csatolt kiszolgáló objektumot `DEMOCOSMOS` az adatforráshoz az alábbi parancsokkal. Cserélje le a `DEMOCOSMOS` nevet a csatolt kiszolgáló nevére, és `SDS Name` a rendszeradatforrás nevével.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Az új csatolt kiszolgáló nevének megtekintéséhez frissítse a csatolt kiszolgálók listájához.

![A csatolt kiszolgáló az ssms-ben](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Csatolt adatbázis lekérdezése

Csatolt adatbázis lekérdezése, adja meg az SSMS-lekérdezést. Ebben a példában a lekérdezés a (z) `customers`nevű tárolóban lévő táblából van kiválasztva:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Hajtsa végre a lekérdezést. Az eredmény ehhez hasonló lesz:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> A Cosmos DB csatolt kiszolgáló nem támogatja a négyrészes elnevezési. Hibát ad vissza az alábbihoz hasonló:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Nem kötelező) Nézetek létrehozása
Adja meg, és a mintavételi folyamat részeként nézeteket hozhat létre. Ezek a nézetek az SQL-nézetek egyenértékűek. Ezek csak olvasható és hatókör a beállításokat és az Azure Cosmos DB SQL-lekérdezés definiált leképezések. 

Az adatnézet létrehozásához a **séma szerkesztő** ablakában, a **definíciók megtekintése** oszlopban kattintson a **Hozzáadás** elemre a tárolóhoz a minta sorban. 
    ![az adatnézet létrehozása](./media/odbc-driver/odbc-driver-create-view.png)


Ezután a **definíciók megtekintése** ablakban tegye a következőket:

1. Kattintson az **új**elemre, adja meg a nézet nevét, például EmployeesfromSeattleView, majd kattintson **az OK**gombra.

1. A **nézet szerkesztése** ablakban adjon meg egy Azure Cosmos db lekérdezést. Ennek [Azure Cosmos db SQL-lekérdezésnek](how-to-sql-query.md)kell lennie, például `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`, majd kattintson **az OK**gombra.

    ![A nézet létrehozásakor, a lekérdezés hozzáadása](./media/odbc-driver/odbc-driver-create-view-2.png)


Tetszés szerint számos nézet hozhat létre. Ha elkészült a nézetek meghatározása, segítségével majd mintavételi az adatokat. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5\. lépés: Az üzleti Intelligencia eszközök, például a Power BI Desktop-adatok megtekintése

Az új DSN segítségével bármely ODBC-kompatibilis eszközök az Azure Cosmos DB csatlakozni – ebben a lépésben egyszerűen bemutatja, hogyan csatlakozhat a Power BI Desktop és a Power BI-Vizualizációk létrehozása.

1. Nyissa meg a Power BI Desktop alkalmazást.

1. Kattintson **az adatlekérdezés**elemre.

    ![A Power BI Desktop adatok lekérése](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Az **adatlekérdezés** ablakban kattintson az **egyéb** | **ODBC** | a **kapcsolat**elemre.

    ![Válassza ki az ODBC-adatforrás a Power BI adatok lekérése](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. A **from ODBC** ablakban válassza ki a létrehozott adatforrás nevét, majd kattintson az **OK**gombra. A **Speciális beállítások** bejegyzéseket üresen hagyhatja.

    ![Válassza ki a Power BI adatok lekérése adatforrás neve (DSN)](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. Az **adatforráshoz való hozzáférés ODBC-illesztő használatával** ablakban válassza az **alapértelmezett vagy az egyéni** lehetőséget, majd kattintson a **Kapcsolódás**elemre. Nem kell megadnia a **hitelesítő adatok kapcsolódási karakterláncának tulajdonságait**.

1. A **navigátor** ablak bal oldali ablaktábláján bontsa ki az adatbázist, a sémát, majd válassza ki a táblázatot. Az eredmények ablaktáblán a létrehozott séma használatával adatokat tartalmazza.

    ![Válassza ki a táblát az adatok a Power BI beszerzése](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Az Power BI Desktopban található adatmegjelenítéshez jelölje be a tábla neve előtt található jelölőnégyzetet, majd kattintson a **Betöltés**elemre.

1. A Power BI Desktopban, a bal oldali, válassza ki az adatok lapot ![Az adatok lapon, a Power BI Desktopban](./media/odbc-driver/odbc-driver-data-tab.png) Erősítse meg az adatokat a rendszer importálta.

1. Mostantól a Power BI használatával is létrehozhat vizualizációkat, ha a jelentés lapra ![jelentés lapra kattint Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), majd az **új vizualizáció**elemre kattint, majd testreszabja a csempét. További információ a vizualizációk létrehozásáról a Power BI Desktopban: [Power bi vizualizációs típusai](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenetet kapja, győződjön meg arról, hogy a [2. lépésben](#connect) a Azure Portal átmásolt **gazdagép** -és **hozzáférési kulcs** értéke helyes, majd próbálkozzon újra. Használja a **gazdagép** és a kulcs értékeinek jobb oldalán lévő másolási gombokat a Azure Portal az értékek szabad **eléréséhez** .

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Következő lépések

További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](introduction.md).
