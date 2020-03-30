---
title: Csatlakozás az Azure Cosmos DB-hez a BI-elemzési eszközökkel
description: Ismerje meg, hogyan hozhat létre táblákat és nézeteket az Azure Cosmos DB ODBC illesztőprogramjával, hogy normalizált adatok at tekinthetnek meg az üzletiintelligencia- és adatelemzési szoftverekben.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: 8be17f0b624c5c34709fb420adb434b77dbc0d91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721081"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Csatlakozás az Azure Cosmos DB-hez az ODBC-illesztőprogrammal rendelkező BI-elemző eszközökkel

Az Azure Cosmos DB ODBC illesztőprogram lehetővé teszi, hogy bi-elemzési eszközök, például az SQL Server Integration Services, a Power BI Desktop és a Tableau használatával csatlakozzon az Azure Cosmos DB-adatok hozásához, így elemezheti és létrehozhatja az Azure Cosmos DB-adatainak vizualizációit ezekben a megoldásokban.

Az Azure Cosmos DB ODBC-illesztő kompatibilis az ODBC 3.8-as verzióval, és támogatja az ANSI SQL-92 szintaxist. Az illesztő rengeteg funkciót tartalmaz, amelyek segítségével újranormalizálhatja az Azure Cosmos DB adatait. Az illesztő használatával az Azure Cosmos DB-ben található adatokat táblaként és nézetként jelenítheti meg. Az illesztő lehetővé teszi az SQL-műveletek elvégzését a táblákon és nézeteken, például a lekérdezések szerinti csoportosítást, beszúrást, frissítést és törlést.

> [!NOTE]
> Az Azure Cosmos DB-hez az ODBC-illesztőprogrammal való csatlakozás jelenleg csak az Azure Cosmos DB SQL API-fiókok számára támogatott.

## <a name="why-do-i-need-to-normalize-my-data"></a>Miért kell normalizálni az adataimat?
Az Azure Cosmos DB egy séma nélküli adatbázis, amely lehetővé teszi a gyors alkalmazásfejlesztést és az adatmodellek iterációs képességét anélkül, hogy szigorú sémára korlátozódna. Egyetlen Azure Cosmos-adatbázis különböző struktúrák JSON-dokumentumait is tartalmazhatja. Ez nagyszerű az alkalmazások gyors fejlesztéséhez, de ha adatelemzéssel és üzletiintelligencia-eszközökkel szeretné elemezni és létrehozni az adatokról szóló jelentéseket, az adatokat gyakran össze kell olvasztani, és egy adott sémához kell ragaszkodni.

Itt jön a napaba az ODBC illesztőprogram. Az ODBC-illesztőprogram használatával most már újranormalizálhatja az adatokat az Azure Cosmos DB-ben olyan táblákká és nézetekké, amelyek megfelelnek az adatelemzési és jelentéskészítési igényeknek. Az újranormalizált sémák nincsenek hatással az alapul szolgáló adatokra, és nem korlátozzák a fejlesztőket, hogy betartsák őket. Ehelyett lehetővé teszik, hogy az ODBC-kompatibilis eszközöket használja az adatok eléréséhez. Így most az Azure Cosmos-adatbázis nem csak a fejlesztői csapat kedvence lesz, de az adatelemzők is imádni fogják.

Kezdjük az ODBC illesztőprogrammal.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>1. lépés: Az Azure Cosmos DB ODBC illesztőprogram telepítése

1. Töltse le a környezetéhez vezető illesztőprogramokat:

    | Beiktató | Támogatott operációs rendszerek| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64 bites.msi](https://aka.ms/cosmos-odbc-64x64) 64 bites Windows rendszerhez| A Windows 8.1 vagy újabb, a Windows 8, a Windows 7, a Windows Server 2012 R2, a Windows Server 2012 és a Windows Server 2008 R2 64 bites verziói.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) 32 bites 64 bites Windows rendszeren| A Windows 8.1 vagy újabb, a Windows 8, a Windows 7, a Windows XP, a Windows Vista, a Windows Server 2012 R2, a Windows Server 2012, a Windows Server 2008 R2 és a Windows Server 2003 64 bites verziói.| 
    |[Microsoft Azure Cosmos DB ODBC 32 bites.msi](https://aka.ms/cosmos-odbc-32x32) 32 bites Windows rendszerhez|A Windows 8.1-es vagy újabb verzióinak 32 bites verziói, Windows 8, Windows 7, Windows XP és Windows Vista.|

    Futtassa az msi fájlt helyileg, amely elindítja a **Microsoft Azure Cosmos DB ODBC illesztőprogram-telepítő varázslót.** 

1. Töltse ki a telepítővarázslót az odbc-illesztőprogram telepítéséhez az alapértelmezett bevitellel.

1. Nyissa meg az **ODBC adatforrás-felügyelő** alkalmazást a számítógépen. Ezt úgy teheti meg, hogy beírja az **ODBC adatforrásokat** a Windows keresőmezőjébe. 
    Az illesztőprogram telepítését az **Illesztőprogramok** fülre kattintva ellenőrizheti, hogy a **Microsoft Azure Cosmos DB ODBC illesztőprogram** szerepel a listában.

    ![Azure Cosmos DB ODBC adatforrás-rendszergazda](./media/odbc-driver/odbc-driver.png)

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>2. lépés: Csatlakozás az Azure Cosmos-adatbázishoz

1. Az [Azure Cosmos DB ODBC illesztőprogram telepítése](#install)után az **ODBC adatforrás-felügyelő** ablakban kattintson a **Hozzáadás**gombra. Létrehozhat egy felhasználót vagy a rendszerdsnát. Ebben a példában felhasználói dsn-t hoz létre.

1. Az **Új adatforrás létrehozása** ablakban válassza a **Microsoft Azure Cosmos DB ODBC driver**lehetőséget, majd kattintson a Befejezés **gombra.**

1. Az **Azure Cosmos DB ODBC illesztőprogram sdn telepítőablakában** töltse ki a következő információkat: 

    ![Az Azure Cosmos DB ODBC illesztőprogram–adatforrástelepítő ablaka](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Adatforrás neve**: Az ODBC dsn saját rövid neve. Ez a név az Azure Cosmos DB-fiókjában egyedi, ezért megfelelően nevezze el, ha több fiókkal rendelkezik.
    - **Leírás**: Az adatforrás rövid leírása.
    - **Gazdagép:** URI az Azure Cosmos DB-fiókjához. Ezt az Azure Cosmos DB Keys lapjáról az Azure Portalon lekérheti, ahogy az a következő képernyőképen látható. 
    - **Hozzáférési kulcs:** Az elsődleges vagy másodlagos, írási vagy csak olvasható kulcs az Azure Cosmos DB Keys lapján az Azure Portalon, ahogy az a következő képernyőképen látható. Azt javasoljuk, hogy az írásvédett kulcsot használja, ha a dsn csak olvasható adatfeldolgozáshoz és jelentéskészítéshez használatos.
    ![Az Azure Cosmos DB Keys lap](./media/odbc-driver/odbc-cosmos-account-keys.png)
    - **Access Key titkosítása:** Válassza ki a legjobb választást a számítógép felhasználói alapján. 
    
1. Kattintson a **Teszt** gombra, és győződjön meg arról, hogy csatlakozhat az Azure Cosmos DB-fiókjához. 

1.  Kattintson **a Speciális beállítások gombra,** és állítsa be a következő értékeket:
    *  **REST API-verzió:** Válassza ki a [REST API-verzióa](https://docs.microsoft.com/rest/api/cosmos-db/) a műveletekhez. Az alapértelmezett 2015-12-16. Ha [nagy partíciókulcsokkal](large-partition-keys.md) rendelkező tárolókkal rendelkezik, és rest API 2018-12-31-es verzióját igényli:
        - Típus **a 2018-12-31-ben** a REST API verziójához
        - A **Start** menüben írja be a "regedit" parancsot a **Rendszerleíróadatbázis-szerkesztő** alkalmazás megkereséséhez és megnyitásához.
        - A Rendszerleíróadatbázis-szerkesztőben keresse meg a következő elérési utat: **Számítógép\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC. INI**
        - Hozzon létre egy új alkulcsot a DSN nevével megegyező névvel, például "Contoso Account ODBC DSN".
        - Nyissa meg a "Contoso Account ODBC DSN" alkulcsot.
        - Új **karakterlánc-érték** hozzáadásához kattintson a jobb gombbal:
            - Érték neve: **IgnoreSessionToken**
            - Értékadatok: **1**
            ![Rendszerleíróadatbázis-szerkesztő beállításai](./media/odbc-driver/cosmos-odbc-edit-registry.png)
    - **Lekérdezéskonzisztencia:** Válassza ki a műveletek [konzisztenciaszintjét.](consistency-levels.md) Az alapértelmezett beállítás a Munkamenet.
    - **Újrapróbálkozások száma:** Adja meg, hogy hányszor próbálja meg újra a műveletet, ha a kezdeti kérés nem fejeződik be a szolgáltatás sebességének korlátozása miatt.
    - **Sémafájl**: Számos lehetőség közül választhat.
        - Alapértelmezés szerint ha ezt a bejegyzést üresen hagyja, az illesztőprogram az összes tároló adatainak első oldalát ellenőrzi az egyes tárolók sémájának meghatározásához. Ezt tárolóleképezésnek nevezzük. Sémafájl definiálása nélkül az illesztőprogramnak minden illesztőprogram-munkamenetnél el kell végeznie a vizsgálatot, és a DSN használatával magasabb indítási időt eredményezhet az alkalmazások számára. Azt javasoljuk, hogy mindig társítson egy sémafájlt egy dsn-hez.
        - Ha már rendelkezik sémafájllal (amelyet valószínűleg a Sémaszerkesztővel hozott létre), kattintson a **Tallózás**gombra, keresse meg a fájlt, kattintson a **Mentés**gombra, majd az **OK**gombra.
        - Ha új sémát szeretne létrehozni, kattintson **az OK**gombra, majd a főablakban a **Sémaszerkesztő parancsra.** Ezután folytassa a Sémaszerkesztő adataival. Az új sémafájl létrehozása után ne felejtsen el visszatérni a **Speciális beállítások** ablakba, hogy tartalmazza az újonnan létrehozott sémafájlt.

1. Miután befejezte és bezárja az **Azure Cosmos DB ODBC illesztőprogram-telepítő** ablakot, az új felhasználói adatforrásneve hozzáadódik a felhasználói adatforráshoz.

    ![Új Azure Cosmos DB ODBC DSN a felhasználói adatforrások lapján](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>3. lépés: Sémadefiníció létrehozása a tárolóleképezési módszerrel

Kétféle mintavételi módszert használhat: **tárolóleképezés** vagy **táblahatároló.** A mintavételi munkamenet mindkét mintavételi módszert használhatja, de minden tároló csak egy adott mintavételi módszert használhat. Az alábbi lépések hozzon létre egy sémát az adatok egy vagy több tárolóban a tároló leképezési módszer használatával. Ez a mintavételi módszer lekéri az adatokat az oldalon egy tároló, hogy meghatározzák az adatok szerkezetét. Átültet egy tárolót az ODBC oldalán lévő asztalra. Ez a mintavételi módszer hatékony és gyors, ha a tartályban lévő adatok homogének. Ha egy tároló heterogén típusú adatokat tartalmaz, javasoljuk, hogy használja a [tábla-határolók leképezési módszert,](#table-mapping) mivel robusztusabb mintavételi módszert biztosít a tárolóban lévő adatstruktúrák meghatározásához. 

1. Miután elvégezte [az](#connect)1-4. **Schema Editor** **Azure Cosmos DB ODBC Driver DSN Setup**

    ![Sémaszerkesztő gomb az Azure Cosmos DB ODBC illesztőprogram-illesztőprogram telepítési ablakában](./media/odbc-driver/odbc-driver-schema-editor.png)
1. A **Sémaszerkesztő** ablakban kattintson az **Új létrehozása gombra.**
    A **Séma létrehozása** ablak ban az Azure Cosmos DB-fiók összes tárolóját megjeleníti. 

1. Jelöljön ki egy vagy több mintát venni kívánt tárolót, majd kattintson **a Minta gombra.** 

1. A **Tervező nézet** lapon az adatbázis, a séma és a tábla jelenik meg. A táblanézetben a vizsgálat megjeleníti az oszlopnevekhez társított tulajdonságok készletét (SQL Név, Forrásnév stb.).
    Minden oszlopesetében módosíthatja az SQL oszlop nevét, az SQL-típust, az SQL-hosszt (ha van ilyen), a Méretezés (ha van ilyen), a Pontosságot (ha van ilyen) és a Nullable oszlopot.
    - Az **Oszlop elrejtése értéket** **igaz** értékre állíthatja, ha ki szeretné zárni az oszlopot a lekérdezés eredményéből. Az Oszlop elrejtés = true jelölésű oszlopok nem kerülnek vissza a kijelöléshez és a vetítéshez, bár még mindig a séma részét képezik. Például elrejtheti az Összes Azure Cosmos DB rendszer szükséges tulajdonságok kezdve "_".
    - Az **id** oszlop az egyetlen olyan mező, amely nem rejthető el, mivel a normalizált sémában elsődleges kulcsként használja. 

1. Miután befejezte a séma definiálását, kattintson a | **Fájlmentés**gombra, keresse meg a könyvtárat a séma mentéséhez, majd kattintson a **Mentés**gombra. **File**

1. Ha ezt a sémát DSN-nel szeretné használni, nyissa meg az **Azure Cosmos DB ODBC illesztőprogram telepítőablakát** (az ODBC adatforrás-felügyelőn keresztül), kattintson a **Speciális beállítások**gombra, majd a **Sémafájl** mezőben keresse meg a mentett sémát. Ha egy sémafájlt egy meglévő DSN-be ment, a DSN-kapcsolat a séma által meghatározott adatokhoz és struktúrahez való hatókörhöz.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>4. lépés: Sémadefiníció létrehozása a táblahatárolók leképezési módszerével

Kétféle mintavételi módszert használhat: **tárolóleképezés** vagy **táblahatároló.** A mintavételi munkamenet mindkét mintavételi módszert használhatja, de minden tároló csak egy adott mintavételi módszert használhat. 

A következő lépések hozzon létre egy sémát az adatok egy vagy több tárolóban a **tábla-határolók** leképezési módszer rel. Azt javasoljuk, hogy használja ezt a mintavételi módszert, ha a tárolók heterogén típusú adatokat tartalmaznak. Ezzel a módszerrel a mintavételi egy sor attribútumok és a megfelelő értékek hatókörét. Ha például egy dokumentum "Type" tulajdonságot tartalmaz, a mintavételezést a tulajdonság értékeire is kihasználhatja. A mintavétel végeredménye a megadott típus minden egyes értékéhez tartozó táblák halmaza lenne. A Type = Car például egy Autó táblát hoz létre, míg a Type = Plane egy Sík táblát.

1. Miután elvégezte [az](#connect)1-4. **Schema Editor**

1. A **Sémaszerkesztő** ablakban kattintson az **Új létrehozása gombra.**
    A **Séma létrehozása** ablak ban az Azure Cosmos DB-fiók összes tárolóját megjeleníti. 

1. Jelöljön ki egy tárolót a **Mintanézet** lapon, a tároló **Leképezési definíciója** oszlopban kattintson a **Szerkesztés gombra.** Ezután a **Leképezési definíció** ablakban válassza a **Táblahatárolók** metódust. Ezután tegye a következőket:

    a. Az **Attribútumok** mezőbe írja be egy határoló tulajdonság nevét. Ez a tulajdonság a dokumentumban, hogy szeretné, hogy a mintavételi, például a Város és nyomja meg az Enter. 

    b. Ha csak a mintavételezést szeretné hatókörbe vinni a fent megadott attribútum bizonyos értékeire, jelölje ki az attribútumot a kijelölési mezőben, írjon be egy értéket az **Érték** mezőbe (pl. Seattle), és nyomja meg az Enter billentyűt. Továbbra is hozzáadhat több értéket az attribútumokhoz. Csak győződjön meg arról, hogy a megfelelő attribútum van kiválasztva, amikor beírja az értékeket.

    Ha például a Város **attribútumértékét** adja meg, és a táblázatot úgy szeretné korlátozni, hogy csak new yorki és dubaji városértékű sorokat tartalmazzon, akkor a Város értéket az Attribútumok mezőbe, a New York és dubai értéket kell beírnia az **Értékek** mezőbe.

1. Kattintson az **OK** gombra. 

1. A mintában mintát venni kívánt tárolók hozzárendelési definícióinak befejezése után a **Sémaszerkesztő** ablakban kattintson a **Minta gombra.**
     Minden oszlopesetében módosíthatja az SQL oszlop nevét, az SQL-típust, az SQL-hosszt (ha van ilyen), a Méretezés (ha van ilyen), a Pontosságot (ha van ilyen) és a Nullable oszlopot.
    - Az **Oszlop elrejtése értéket** **igaz** értékre állíthatja, ha ki szeretné zárni az oszlopot a lekérdezés eredményéből. Az Oszlop elrejtés = true jelölésű oszlopok nem kerülnek vissza a kijelöléshez és a vetítéshez, bár még mindig a séma részét képezik. Például elrejtheti az Azure Cosmos DB rendszer `_`szükséges tulajdonságait a kezdővel.
    - Az **id** oszlop az egyetlen olyan mező, amely nem rejthető el, mivel a normalizált sémában elsődleges kulcsként használja. 

1. Miután befejezte a séma definiálását, kattintson a | **Fájlmentés**gombra, keresse meg a könyvtárat a séma mentéséhez, majd kattintson a **Mentés**gombra. **File**

1. Az **Azure Cosmos DB ODBC illesztőprogram-adatforrástelepítő** ablakában kattintson a **Speciális beállítások gombra.** Ezután a **Sémafájl** mezőben keresse meg a mentett sémafájlt, és kattintson az **OK**gombra. A dsn mentéséhez kattintson ismét az **OK** gombra. Ezzel menti a létrehozott sémát a dsn-be. 

## <a name="optional-set-up-linked-server-connection"></a>(Nem kötelező) Csatolt kiszolgálókapcsolat beállítása

Az Azure Cosmos DB lekérdezése az SQL Server Management Studio (SSMS) egy csatolt kiszolgálói kapcsolat beállításával.

1. Hozzon létre egy rendszeradatforrást a `SDS Name` [2.](#connect)

1. [Telepítse az SQL Server Management Studio alkalmazást,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és csatlakozzon a kiszolgálóhoz. 

1. Az SSMS lekérdezésszerkesztőben hozzon `DEMOCOSMOS` létre egy csatolt kiszolgálóobjektumot az adatforráshoz a következő parancsokkal. Cserélje `DEMOCOSMOS` le a csatolt kiszolgáló `SDS Name` nevét és a rendszeradatforrás nevét.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Az új csatolt kiszolgáló nevének megtekintéséhez frissítse a Csatolt kiszolgálók listát.

![Csatolt kiszolgáló SSMS-ben](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Csatolt adatbázis lekérdezése

A csatolt adatbázis lekérdezéséhez adjon meg egy SSMS-lekérdezést. Ebben a példában a lekérdezés a következő `customers`tárolóban lévő táblából választ ki:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Hajtsa végre a lekérdezést. Az eredménynek hasonlónak kell lennie ehhez:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> A csatolt Cosmos DB-kiszolgáló nem támogatja a négyrészből álló elnevezést. A következő höz hasonló hibaüzenet jelenik meg:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Nem kötelező) Nézetek létrehozása
A mintavételi folyamat részeként nézeteket definiálhat és hozhat létre. Ezek a nézetek egyenértékűek az SQL-nézetekkel. Ezek csak olvasható, és az Azure Cosmos DB SQL-lekérdezés definiált kiválasztásának és vetületeinek hatóköre. 

Ha nézetet szeretne létrehozni az adatokhoz, a **Sémaszerkesztő** **ablakdefiníciók megtekintése** oszlopában kattintson a **Hozzáadás** gombra a mintavételhez a tároló sorában. 
    ![Adatok nézetének létrehozása](./media/odbc-driver/odbc-driver-create-view.png)


Ezután a **Definíciók megtekintése** ablakban tegye a következőket:

1. Kattintson az **Új**gombra, adja meg a nézet nevét, például Az Alkalmazottak a SeattleView nézetből, majd kattintson az **OK**gombra.

1. A **Szerkesztés nézet** ablakban adjon meg egy Azure Cosmos DB-lekérdezést. Ennek például `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"` [Azure Cosmos DB SQL-lekérdezésnek](how-to-sql-query.md)kell lennie, majd kattintson az **OK**gombra.

    ![Lekérdezés hozzáadása nézet létrehozásakor](./media/odbc-driver/odbc-driver-create-view-2.png)


Tetszés szerint több nézetet is létrehozhat. Miután befejezte a nézetek definiálását, mintát vehet az adatokból. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5. lépés: Adatok megtekintése üzletiintelligencia-eszközökben, például a Power BI Desktopban

Az új DSN segítségével bármilyen ODBC-kompatibilis eszközzel csatlakozhat az Azure Cosmos DB-hez – ez a lépés egyszerűen bemutatja, hogyan csatlakozhat a Power BI Desktophoz, és hogyan hozhat létre Power BI-megjelenítést.

1. Nyissa meg a Power BI Desktopot.

1. Kattintson az Adatok beszerezni e **gombra.**

    ![Adatok lekérése a Power BI Desktopban](./media/odbc-driver/odbc-driver-power-bi-get-data.png)

1. Az **Adatok bekerülése** ablakban kattintson az **Egyéb** | **ODBC-csatlakozás** | **gombra.**

    ![Az ODBC adatforrás kiválasztása a Power BI adatok becsatornázása között](./media/odbc-driver/odbc-driver-power-bi-get-data-2.png)

1. A **From ODBC** ablakban jelölje ki a létrehozott adatforrásnevet, majd kattintson az **OK**gombra. A Speciális beállítások bejegyzéseket üresen **hagyhatja.**

    ![Adatforrásnevének (DSN) kiválasztása a Power BI adatok beolvasása között](./media/odbc-driver/odbc-driver-power-bi-get-data-3.png)

1. Az **OdBC-illesztőprogram ablakával** az Adatforrás elérése korválassza az **Alapértelmezett vagy az Egyéni** lehetőséget, majd kattintson a Csatlakozás **gombra.** Nem kell megadnia a **Hitelesítő adatok kapcsolati karakterláncának tulajdonságait.**

1. A **Navigátor** ablakbal ablakában bontsa ki az adatbázist, a sémát, majd jelölje ki a táblát. Az eredmények ablaktábla a létrehozott séma használatával tartalmazza az adatokat.

    ![Táblázat kiválasztása a Power BI-ban Adatok bekéselése](./media/odbc-driver/odbc-driver-power-bi-get-data-4.png)

1. Ha meg szeretné jeleníteni az adatokat a Power BI asztalon, jelölje be a táblanév előtti jelölőnégyzetet, majd kattintson a **Betöltés gombra.**

1. A Power BI Desktop bal szélen kattintson az Adatok lapra ![Adatok lap a Power BI Desktopban](./media/odbc-driver/odbc-driver-data-tab.png) az adatok importálásának ellenőrzéséhez.

1. A Power BI segítségével most már létrehozhat vizualizációkat, ha](./media/odbc-driver/odbc-driver-report-tab.png)a Power BI Desktop Jelentés fülére ![kattint, az Új **vizualizáció**gombra kattint, majd testre szabja a csempét. A képi megjelenítések Power BI Desktopban való létrehozásáról a [Képimegjelenítési típusok a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/)című témakörben talál további információt.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenet jelenik **Access Key** meg, győződjön meg **arról,** hogy az Azure Portal 2. [Step 2](#connect) Használja a másolási gombok a jobb oldalon a **gazdagép** és **az access key** értékek az Azure Portalon az értékek másolása hiba mentes.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>További lépések

További információ az Azure Cosmos DB-ről: [Üdvözli az Azure Cosmos DB](introduction.md).
