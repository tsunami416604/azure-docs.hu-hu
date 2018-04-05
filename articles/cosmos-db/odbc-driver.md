---
title: Csatlakozás Azure Cosmos DB BI elemzőeszközök segítségével |} Microsoft Docs
description: Útmutató az Azure Cosmos DB ODBC-illesztő segítségével hozza létre a táblák és nézetek normalizált adatok tekinthetők BI és az adatok analytics szoftver.
keywords: ODBC, odbc-illesztőprogram
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/22/2018
ms.author: mimig
ms.openlocfilehash: 445acafeef67027712826f644afaa1784569b30d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Csatlakozás Azure Cosmos DB BI elemzőeszközök segítségével az ODBC-illesztőprogram

Az Azure Cosmos DB ODBC-illesztőprogram lehetővé teszi, hogy az SQL Server Integration Services, a Power BI Desktop és a Tableau Üzletiintelligencia-analytics eszközök használatával, hogy az Azure Cosmos DB adatok képi megjelenítések létrehozása a partnermegoldások állapotösszegzése és elemzése Azure Cosmos-Adatbázishoz csatlakozhat.

Az Azure Cosmos DB ODBC-illesztőprogram ODBC 3.8 megfelelő, és támogatja az ANSI SQL-92 szintaxist. Az illesztőprogram segítséget nyújt az adatokat az Adatbázisba az Azure Cosmos renormalize gazdag funkciókat nyújtja. A illesztőprogrammal táblák és nézetek az Adatbázisba az Azure Cosmos-adatait tartalmazzák. Az illesztőprogram lehetővé teszi a táblák és nézetek lekérdezések, többek között a következőket csoport szúrja be a frissítéseket, és törli az SQL-műveletek végrehajtásához.

## <a name="why-do-i-need-to-normalize-my-data"></a>Miért kell normalizálása az adataimat?
Azure Cosmos-adatbázis egy séma nélküli adatbázisban, így a alkalmazások többször menet közben az adatmodellt, és nem korlátozza őket egy szigorú séma engedélyezésével lehetővé teszi az alkalmazások gyors fejlesztést. Egyetlen Azure Cosmos DB adatbázisban tartalmazhat különböző struktúrák JSON-dokumentumokat. Ez gyors alkalmazásfejlesztés nagy, de ha azt szeretné, elemzéséhez és az adatok adatelemzés és BI eszközök használatával jelentéseket készíthet, az adatok gyakran kell összevonandó és egy adott séma igazodik.

Ez azért, ahol az ODBC-illesztő érkezik. Az ODBC-illesztő segítségével adatokat az Adatbázisba az Azure Cosmos táblák és nézetek az adatok elemzési és jelentéskészítési igényeinek megfelelően illeszkedő most renormalize. A renormalized sémák nincs hatással az alapul szolgáló adatokat, és nem korlátozza a fejlesztők igazodnia kell őket, lehetővé teszik, hogy kihasználja az ODBC-kompatibilis eszközök adatainak eléréséhez. Ezért most az Azure Cosmos DB adatbázis nem csak lesz a fejlesztői csapat kedvenc, de az adatelemzők szeretni fognak azt túl.

Most már megadható, hogy az ODBC-illesztőprogram az első lépései.

## <a id="install"></a>1. lépés: Az Azure Cosmos DB ODBC-illesztőprogram telepítése

1. Töltse le az illesztőprogramokat az adott környezetben:

    | Telepítő | Támogatott operációs rendszerek| 
    |---|---| 
    |[A Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) 64 bites Windows| 64 bites Windows 8.1 vagy újabb, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 és Windows Server 2008 R2.| 
    |[A Microsoft Azure Cosmos DB ODBC 32 x 64-bit.msi](https://aka.ms/documentdb-odbc-32x64) a 32 bites, 64 bites Windows rendszeren| 64 bites Windows 8.1 vagy újabb, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 és Windows Server 2003.| 
    |[A Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) 32 bites Windows|32 bites Windows 8.1 vagy újabb, Windows 8, Windows 7, Windows XP és Windows Vista.|

    Futtassa az msi-fájl helyben, amely elindítja a **Microsoft Azure Cosmos DB ODBC illesztőprogram telepítővarázsló**. 
2. A bemeneti az ODBC illesztőprogram telepítéséhez az alapértelmezett telepítési varázsló befejezéséhez.
3. Nyissa meg a **ODBC-adatforrás felügyeleti** alkalmazást a számítógépen, akkor ehhez írja be **ODBC adatforrások** a Windows keresési mezőbe. 
    Az illesztőprogram lett telepítve a gombra kattintva ellenőrizheti a **illesztőprogramok** lapra, és győződjön meg arról **Microsoft Azure Cosmos DB ODBC-illesztőprogram** szerepel.

    ![Az Azure Cosmos DB ODBC-adatforrás felügyelő](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>2. lépés: Csatlakozás az Azure Cosmos DB adatbázis

1. Után [az Azure Cosmos DB ODBC-illesztőprogram telepítésekor](#install), a a **ODBC-adatforrás felügyelő** ablak, kattintson a **Hozzáadás**. Létrehozhat egy felhasználó vagy rendszer DSN-NEVET. Ebben a példában a felhasználói DSN létrehozásához.
2. Az a **új adatforrás létrehozása** ablakban válassza ki **Microsoft Azure Cosmos DB ODBC-illesztőprogram**, és kattintson a **Befejezés**.
3. Az a **Azure Cosmos DB ODBC illesztőprogram SDN-alapú telepítő** ablakban töltse ki a következő: 

    ![Az Azure Cosmos DB ODBC illesztőprogram DSN beállítása ablakban](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Adatforrás neve**: az ODBC-adatforrásnévhez saját rövid nevét. Ez a név egyedi Azure Cosmos DB fiókjába, ezért nevezze el megfelelően, ha több fiókot.
    - **Leírás**: az adatforrás rövid leírása.
    - **Állomás**: URI Azure Cosmos DB fiókjához. Beolvasható Ez az Azure-portálon az Azure Cosmos DB kulcsok oldalon az alábbi képernyőfelvételen látható módon. 
    - **Hívóbetű**: az elsődleges vagy másodlagos, olvasási és írási vagy olvasási kulcsot az Azure Cosmos DB kulcsok lapon az Azure portálon, az alábbi képernyőfelvételen látható módon. Azt javasoljuk, hogy a csak olvasható-kulcsot használ, ha a DSN-NEVET használja a csak olvasható az adatfeldolgozás és -jelentéskészítés.
    ![Az Azure Cosmos DB kulcsok lap](./media/odbc-driver/odbc-driver-keys.png)
    - **A hozzáférési kulcs titkosításához**: válassza ki a legjobb választás alapján a felhasználók ezt a gépet. 
4. Kattintson a **teszt** gombra kattintva ellenőrizze, hogy Azure Cosmos DB-fiókjához csatlakozhat. 
5. Kattintson a **speciális beállítások** és állítsa be a következő értékeket:
    - **Lekérdezési konzisztencia**: válassza ki a [konzisztenciaszint](consistency-levels.md) a műveletekhez. Az alapértelmezett érték a munkamenet.
    - **Az újrapróbálkozások számát**: Adja meg a próbálkozások egy műveletet, ha a kezdeti kérelem befejezése sikertelennek bizonyul szolgáltatás szabályozás miatt.
    - **Sémafájl**: több lehetőség itt rendelkeznek.
        - Alapértelmezés szerint ez a bejegyzés (üres), így az illesztőprogramot keres az első lap adatok megállapításához a séma egyes gyűjtemények minden gyűjtemények. Ez más néven gyűjtemény a hozzárendelése. Nélkül definiált sémafájl az illesztőprogram minden egyes illesztőprogram-munkamenet az ellenőrzés elvégzéséhez rendelkezik, és eredményezhet a DSN-NEVET használó alkalmazások a magasabb indítási idő. Azt javasoljuk, hogy mindig társít egy sémafájl a DSN-hez.
        - Ha már rendelkezik egy sémafájl (valószínűleg egy létrehozott a [séma szerkesztő](#schema-editor)), kattintson **Tallózás**, keresse meg a fájlt, kattintson a **mentése**, és kattintson a **OK**.
        - Ha szeretne létrehozni egy új séma, kattintson a **OK**, és kattintson a **séma szerkesztő** fő ablakában. Lépjen a [séma szerkesztő](#schema-editor) információkat. Miután létrehozta az új sémafájl, ne felejtse el kíván térni a **speciális beállítások** az újonnan létrehozott sémafájl ablak.

6. Miután befejezte a, és zárja be a **Azure Cosmos DB ODBC illesztőprogram DSN beállítása** ablakot, az új felhasználó DSN elérhető lesz a felhasználói DSN lapján.

    ![Új Azure Cosmos DB ODBC DSN-NEVET a felhasználói DSN lap](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>3. lépés: A gyűjtemény leképezési metódussal sémadefiníciót létrehozása

Két különböző mintavételi módszerek közül választhat: **gyűjtemény leképezési** vagy **tábla-határolójelek**. A mintavételi munkamenet nyújthatnak mindkét mintavételi módszer, de egyes gyűjtemények csak egy adott mintavételi metódus használatával. Az alábbi lépéseket a sémát hozzon létre egy vagy több gyűjteményt a gyűjtemény leképezési metódussal. Ez a mintavételi metódus lekéri az adatokat a lapon, az adatok szerkezete meghatározásához gyűjtemény. Az ODBC-oldali táblához gyűjtemény transposes azt. Ez a mintavételi módszer esetén gyors és hatékony homogén gyűjtemény adatai. Ha egy gyűjtemény heterogén típusú adatot tartalmaz, azt javasoljuk, használjon a [tábla-határolójelek metódus leképezése](#table-mapping) határozza meg a gyűjtemény adatstruktúrák robusztusabb mintavételi módszert biztosít. 

1. Lépéseket 1-4 befejezése után [az Azure Cosmos DB adatbázishoz való csatlakozás](#connect), kattintson a **séma szerkesztő** a a **Azure Cosmos DB ODBC illesztőprogram DSN beállítása** ablak.

    ![Az Azure Cosmos DB ODBC illesztőprogram DSN beállítása ablakban séma szerkesztő gombra](./media/odbc-driver/odbc-driver-schema-editor.png)
2. Az a **séma szerkesztő** ablak, kattintson a **hozzon létre új**.
    A **készítése séma** ablak az Azure Cosmos DB fiók összes gyűjteményt jeleníti meg. 
3. Válasszon ki egy vagy több gyűjteményt mintát, majd **minta**. 
4. Az a **Tervező nézetben** lapon, az adatbázis, a séma és a tábla jelennek meg. A tábla nézetben a vizsgálat az oszlopok neveit (SQL-neve, adatforrás neve, stb.) társított tulajdonságok készletét jeleníti meg.
    Az oszlopok, módosíthatja az oszlop SQL nevét, az SQL-típus, SQL hossza (ha van ilyen), a Méretezés (ha van ilyen), a pontosság (ha van ilyen) és a Nullable.
    - Beállíthat **oszlop elrejtése** való **igaz** Ha ki szeretné zárni az oszlopnak a lekérdezési eredmények. Oszlopok jelölte meg az oszlop elrejtése = true nem lehet megjeleníteni a kijelölés és a leképezés, annak ellenére, hogy azok továbbra is a séma részei. Például elrejthetők összes kezdődő, és "_" Azure Cosmos DB rendszer szükséges tulajdonság.
    - A **azonosító** oszlop értéke nem lehet rejtett, a normalizált séma az elsődleges kulcsként használt csak mező. 
5. Ha sémájának definiálásához végzett, kattintson a **fájl** | **mentése**, keresse meg a könyvtárat a sémát, és kattintson a **mentése**.

    Ha a jövőben használni kívánt ebben a sémában az adatbázis, nyissa meg az Azure Cosmos DB ODBC illesztőprogram DSN beállítása ablakban (keresztül az ODBC adatforrás-felügyelő), kattintson a Speciális beállítások és a következő sémafájl mezőbe, a mentett séma navigáljon. Meglévő DSN egy séma-fájl mentése módosítja a hatókörben. az adat- és határozzák meg séma struktúrát DSN csatlakozni.

## <a id="table-mapping"></a>4. lépés:, Hozzon létre a tábla-határolójelek használata sémadefiníciót metódus leképezése

Két különböző mintavételi módszerek közül választhat: **gyűjtemény leképezési** vagy **tábla-határolójelek**. A mintavételi munkamenet nyújthatnak mindkét mintavételi módszer, de egyes gyűjtemények csak egy adott mintavételi metódus használatával. 

Az alábbi lépéseket a sémát hozzon létre egy vagy több gyűjteményt használ a **tábla-határolójelek** metódus leképezése. Azt javasoljuk, hogy ezt a módszert mintavételi, ha a gyűjtemények heterogén típusú adatok tartalmazzák. Ezt a módszert olyan attribútumokat és a megfelelő értékeket a mintavétel hatókörének. Például egy dokumentum tartalmaz egy "Type" tulajdonságot, ha hatókörét megadhatja a tulajdonság értékének a mintavétel. A végeredménynek a mintavétel a megadott típus értékeit táblázatok halmazát lenne. Írja be például = Car a művelet létrehoz egy autó tábla típusú közben = Vezérlősík Vezérlősík tábla eredményezne.

1. Lépéseket 1-4 befejezése után [az Azure Cosmos DB adatbázishoz való csatlakozás](#connect), kattintson a **séma szerkesztő** az Azure Cosmos DB ODBC illesztőprogram DSN beállítása ablakban.
2. Az a **séma szerkesztő** ablak, kattintson a **hozzon létre új**.
    A **készítése séma** ablak az Azure Cosmos DB fiók összes gyűjteményt jeleníti meg. 
3. Válasszon ki egy gyűjteményt a a **minta** lap a **leképezési Definition** oszlop ahhoz a gyűjteményhez, kattintson a **szerkesztése**. Ezt a a **leképezési Definition** ablakban válassza ki **tábla határolójelek** metódust. Ezután tegye a következőket:

    a. Az a **attribútumok** mezőbe írja be egy elválasztó tulajdonság nevét. Ez az adott hatókörre a mintavétel, például a város kívánja, és nyomja le az enter tulajdonság. 

    b. Ha csak bizonyos értékeket a megadott attribútum mintavételi hatókör, jelölje ki az attribútumot a kiválasztási mezőben, majd adjon meg egy értéket a **érték** mezőben, például Budapesten és nyomja le az adja meg a. Továbbra is az attribútumok több értékek hozzáadásához. Csak győződjön meg arról, hogy a megfelelő attribútum érték megadásakor folyamatban van-e kiválasztva.

    Ha például egy **attribútumok** értékének Cityben, és a táblázat csak a győri és Dubai város értéket tartalmazó sorok ismételt felvétele korlátozni kívánja, akkor beírnia város az attribútumok mezőben és New York közt, majd Dubai a **Értékek** mezőbe.
4. Kattintson az **OK** gombra. 
5. A gyűjtemények a leképezési definíciók befejezése után szeretne, a a **séma szerkesztő** ablak, kattintson a **minta**.
     Az oszlopok, módosíthatja az oszlop SQL nevét, az SQL-típus, SQL hossza (ha van ilyen), a Méretezés (ha van ilyen), a pontosság (ha van ilyen) és a Nullable.
    - Beállíthat **oszlop elrejtése** való **igaz** Ha ki szeretné zárni az oszlopnak a lekérdezési eredmények. Oszlopok jelölte meg az oszlop elrejtése = true nem lehet megjeleníteni a kijelölés és a leképezés, annak ellenére, hogy azok továbbra is a séma részei. Például az összes Azure Cosmos DB szükséges Rendszertulajdonságok kezdve elrejthetők `_`.
    - A **azonosító** oszlop értéke nem lehet rejtett, a normalizált séma az elsődleges kulcsként használt csak mező. 
6. Ha sémájának definiálásához végzett, kattintson a **fájl** | **mentése**, keresse meg a könyvtárat a sémát, és kattintson a **mentése**.
7. Vissza a **Azure Cosmos DB ODBC illesztőprogram DSN beállítása** ablak, kattintson a **speciális beállítások**. Ezt követően a a **sémafájl** a mentett sémafájl váltson, és kattintson a **OK**. Kattintson a **OK** újra DSN mentésére. A létrehozott séma ez menti a DSN-NEVET. 

## <a name="optional-set-up-linked-server-connection"></a>(Választható) A csatolt kiszolgáló-kapcsolat beállítása

A csatolt kiszolgáló kapcsolatán beállításával lekérdezheti a Azure Cosmos adatbázis az SQL Server Management Studio (SSMS).

1. A rendszer adatforrás létrehozása [2. lépés](#connect), például elnevezett `SDS Name`.
2. [Telepítse az SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).
3. A lekérdezés SSMS editor hoz létre csatolt `DEMOCOSMOS` az alábbi parancsokkal az adatforrásra vonatkozóan. Cserélje le `DEMOCOSMOS` a csatolt kiszolgálóhoz tartozó nevű és `SDS Name` a rendszer az adatforrás nevét.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Az új csatolt kiszolgáló nevének megtekintéséhez a csatolt kiszolgálók listájának frissítése.

![A csatolt kiszolgáló szolgáltatáshoz az ssms](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Csatolt adatbázis lekérdezése

A csatolt adatbázis lekérdezéséhez írja be az SSMS lekérdezést. Ebben a példában a lekérdezés választja ki a táblázatban szereplő nevű gyűjtemény `customers`:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

A lekérdezés végrehajtása. Az eredmény kell kell kinéznie:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> A csatolt Cosmos adatbázis-kiszolgáló nem támogatja a négyrészes elnevezési. Hibát ad vissza az alábbihoz hasonló:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Választható) Nézetek létrehozása
Adja meg, és a mintavételi folyamat részeként nézeteket hozhat létre. Ezek a nézetek SQL-nézetek egyenértékűek. Csak olvashatók, és a hatókör a beállításokat, és a Azure Cosmos DB SQL vetületét definiálva. 

Az adatok nézet létrehozása az a **séma szerkesztő** ablakban, a a **definícióinak megtekintése** oszlopban kattintson **hozzáadása** gyűjteményének mintához a sorban. Ezt a a **nézet definíciók** ablakban tegye a következőket:
1. Kattintson a **új**, írja be a nézet, például EmployeesfromSeattleView nevét, és kattintson **OK**.
2. Az a **nézet szerkesztése** ablak, írja be az Azure Cosmos DB lekérdezést. Ez lehet például Azure Cosmos adatbázis SQL-lekérésen`SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`, és kattintson a **OK**.

Létrehozhat egy számos nézet kedve szerint elrendezheti. Ha elkészült a nézetek meghatározása, akkor is majd az az adatokat. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>5. lépés: Az adatok megtekintése a Power BI Desktop Üzletiintelligencia-eszközök

Az új DSN segítségével csatlakozzon DocumentADB bármely ODBC-kompatibilis eszközök – Ez a lépés egyszerűen bemutatja, hogyan kell csatlakozni a Power BI Desktop és a Power BI képi megjelenítés létrehozása.

1. Nyissa meg a Power BI Desktop.
2. Kattintson a **adatok**.
3. Az a **adatok beolvasása** ablak, kattintson a **más** | **ODBC** | **Connect**.
4. Az a **az ODBC** ablakban, majd jelölje ki az adatforrás nevét, a létrehozott, majd kattintson a **OK**. Ha elhagyja a **speciális beállítások** bejegyzés üres.
5. Az a **ODBC illesztővel adatforrás elérése** ablakban válassza ki **alapértelmezett vagy egyéni** majd **Connect**. Nem kell felvenni a **hitelesítőadat-kapcsolatikarakterlánc-tulajdonságokat**.
6. Az a **Navigator** ablakban a bal oldali ablaktáblán bontsa ki az adatbázist, a séma, és válassza ki a táblázatban. Az eredmények ablaktáblán a létrehozott séma használata adatokat tartalmazza.
7. A Power BI desktop adatok megjelenítéséhez, jelölje be a jelölőnégyzetet a táblázat neve elé, és kattintson **terhelés**.
8. A Power BI Desktopban, a bal oldali, válassza ki az adatok lapon ![A Power BI Desktop adatok lap](./media/odbc-driver/odbc-driver-data-tab.png) Ellenőrizze az adatok importálása volt.
9. Hozhatók létre látványelemek a Power BI segítségével a jelentés fülre kattintva ![jelentés lapot a Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png)gombra, majd **új Visual**, és a csempe majd testreszabása. A Power BI Desktop képi megjelenítések létrehozásával kapcsolatos további információkért lásd: [Power BI-ban a képi megjelenítés típusok](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Hibaelhárítás

A következő hibaüzenet jelenik meg, ha a **állomás** és **hozzáférési kulcs** másolta az Azure portálra az értékek [2. lépés](#connect) helyes-e, és próbálkozzon újra. A Másolás gombbal jobb oldalán a **állomás** és **hozzáférési kulcs** értékek másolása a szabad értékek hiba az Azure portálon.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>További lépések

Azure Cosmos DB kapcsolatos további információkért lásd: [Üdvözli az Azure Cosmos DB](introduction.md).
