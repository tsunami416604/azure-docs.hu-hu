<properties
   pageTitle="Azure Data Catalog – Ismerkedés az Azure Data Catalog szolgáltatással | Microsoft Azure"
   description="Átfogó oktatóanyag az Azure Data Catalog forgatókönyveiről és képességeiről."
   documentationCenter=""
   services="data-catalog"
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="05/06/2016"
   ms.author="maroche"/>

# Ismerkedés az Azure Data Catalog szolgáltatással

Ez a cikk átfogó áttekintést nyújt az **Azure Data Catalog** forgatókönyveiről és képességeiről. A szolgáltatásban való regisztrálás után kövesse az alábbi lépéseket egy adatkatalógus létrehozásához, valamint adatforrások regisztrálásához, megjegyzésekkel ellátásához és felderítéséhez.

## Az oktatóanyag előfeltételei

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- **Egy Azure-előfizetés** – Ha nincs előfizetése, néhány perc alatt létrehozhat egy ingyenes próbafiókot. A részletekért lásd: [Ingyenes próbafiók](https://azure.microsoft.com/pricing/free-trial/).
- **Azure Active Directory** – Az Azure Data Catalog az [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) szolgáltatást használja az identitások és hozzáférések felügyeletéhez.
- **Adatforrások** – Az Azure Data Catalog képes adatforrások felderítésére. Ez az oktatóanyag az Adventure Works példaadatbázisát használja, de bármilyen támogatott adatforrást használhat, ha szeretne ismerős és a szerepköre szempontjából releváns adatokkal dolgozni. A támogatott adatforrások listájáért lásd: [Supported data sources](data-catalog-dsr.md) (Támogatott adatforrások).

> [AZURE.NOTE] További információk az Azure-előfizetésekről és az Azure Active Directory szolgáltatásról: [Azure Data Catalog prerequisites](data-catalog-prerequisites.md) (Az Azure Data Catalog előfeltételei).

Kezdjük az Adventure Works példaadatbázisának telepítésével.

## 1. gyakorlat: az Adventure Works példaadatbázisának telepítése

Ebben a gyakorlatban telepíteni fogja az Adventure Works vállalat SQL Server adatbázismotorhoz tartozó példaadatbázisát, amelyet a következő gyakorlatokban is használni fog.

### Az Adventure Works 2014 OLTP adatbázis telepítése

Az Adventure Works adatbázisa szabványos online tranzakciófeldolgozási forgatókönyveket bonyolít le egy fiktív kerékpárgyártó (Adventure Works Cycles) számára, ideértve a termékek, az értékesítések és a vásárlások kezelését is. Ebben az oktatóanyagban a termékekre vonatkozó információkat fog regisztrálni az **Azure Data Catalog** szolgáltatásba.

Az Adventure Works példaadatbázisát az alábbiakban leírt módon telepítheti.

Az Adventure Works példaadatbázis telepítéséhez egy AdventureWorks2014 nevű biztonsági mentés helyreállítása szükséges, amely az [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) fájlban található a CodePlex webhelyen. Az adatbázis helyreállításának egyik módja egy T-SQL-parancsfájl futtatása az SQL Server Management Studióban.

**Az Adventure Works példaadatbázisának telepítése egy T-SQL-parancsfájl használatával**

1.  Hozza létra s C:\DataCatalog_GetStarted mappát. Ha más mappanevet használ, ne felejtse az elérési utat az alábbi T-SQL-parancsfájlban is módosítani.
2.  Töltse le az [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) fájlt.
3.  Csomagolja ki az Adventure Works 2014 Full Database Backup.zip fájlt a C:\DataCatalog_GetStarted mappába. Az alábbi parancsfájl feltételezi, hogy a biztonságimentés-fájl a C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak helyen található.
4.  Az **SQL Server Management Studio** alkalmazásban a **Standard** eszköztáron kattintson a **New Query** (Új lekérdezés) elemre.
5.  A lekérdezési ablakban futtassa a következő T-SQL-kódot.

**Futtassa ezt a parancsfájlt az Adventure Works 2014 adatbázis telepítéséhez**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
        -- AdventureWorks2014.bak file location
        FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

        -- AdventureWorks2014.mdf database location
        WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

        -- AdventureWorks2014.ldf log location
        MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

A T-SQL-parancsfájl futtatása helyett alternatív megoldásként az adatbázist az SQL Server Management Studio használatával is helyreállíthatja. Lásd: [Restore a Database Backup (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx) (Adatbázis biztonsági mentésének helyreállítása (SQL Server Management Studio)).

Ebben a gyakorlatban telepítette az Adventure Works példaadatbázisát, amelyet a következő gyakorlatokban is használni fog. A következő gyakorlatból elsajátíthatja, hogyan regisztrálhat adatforrásokat az **Azure Data Catalog** szolgáltatásba az Adventure Works példaadatbázisában található táblákból.

## 2. gyakorlat: Adatforrások regisztrálása

Ebben a gyakorlatban az **Azure Data Catalog** regisztrációs eszközét használva regisztrálja az Adventure Works adatforrásait a katalógusba. A regisztráció az a folyamat, amely során az adatforrásból és a benne található adategységekből kinyert fő szerkezeti metaadatok (például nevek, típusok és helyek) hozzáadódnak a katalógushoz. Az adatforrások és a bennük tárolt adatok az eredeti helyükön maradnak, de a metaadatok használatával a katalógus könnyebben feltárhatóvá és értelmezhetővé teszi őket.

### Egy adatforrás regisztrálása

1.  Lépjen a https://azure.microsoft.com/services/data-catalog helyre, majd kattintson **Az első lépések** elemre.
2.  Jelentkezzen be az **Azure Data Catalog**-portálra, majd kattintson a **Publish data** (Adatok közzététele) lehetőségre.

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.  Kattintson a **Launch Application** (Alkalmazás indítása) parancsra.

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. A **Kezdőlapon** kattintson a **Sign in** (Bejelentkezés) elemre, és adja meg a hitelesítő adatait.
5. A **Microsoft Azure Data Catalog** oldalon kattintson duplán az **SQL Server** elemre, vagy kattintson az **SQL Server**, majd a **Next** (Tovább) gombra.

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.  Adja meg az AdventureWorks2014 adatbázis SQL Server-kapcsolódási tulajdonságait (lásd a példát alább), majd kattintson a **CONNECT** (CSATLAKOZÁS) gombra.

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.  A következő oldalon kell regisztrálnia az adatforrásához tartozó metaadatokat. Ebben a példában a **Production/Product** objektumait regisztrálja az AdventureWorks Production névtérből. Ennek menete a következő:

    a. A **Server Hierarchy** (Kiszolgálóhierarchia) fán kattintson a **Production** elemre.

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b. Ctrl+kattintással jelölje ki a Product, a ProductCategory, a ProductDescription és a ProductPhoto elemeket.

    c. Kattintson a kiválasztott elemeket áthelyező nyílra (**>**). Ez az összes kiválasztott Product objektumot áthelyezi az **Objects to be registered** (Regisztrálandó objektumok) listára.

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d. Az **Add tags** (Címke hozzáadása) mezőben adja meg a „leírás” és a „fénykép” címkéket. Ez hozzáadja az adategységekhez a keresési címkéket. A címkék nagy segítséget nyújtanak abban, hogy a felhasználók megtaláljanak egy bizonyos regisztrált adatforrást.

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e.  **Választható lehetőség**: Használhatja az **Include a Preview** (Előnézet hozzáadása) és az **Add a data source expert** (Adatforrás-szakértő hozzáadása) lehetőséget is.

    f.  Kattintson a **REGISTER** (REGISZTRÁLÁS) gombra. Az **Azure Data Catalog** regisztrálja a kiválasztott objektumokat. Ebben a gyakorlatban az Adventure Works kiválasztott objektumai lesznek regisztrálva.

    g.  A regisztrált adatforrás-objektumok megtekintéséhez kattintson a **View Portal** (Portál megtekintése) lehetőségre. Az **Azure Data Catalog**-portálon **mozaik** vagy **lista** formájában is megtekintheti az adatforrás-objektumokat.

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

Ebben a gyakorlatban az Adventure Works példaadatbázisából regisztrált néhány objektumot, amelyeket mostantól a szervezet bármely felhasználója könnyen megtalálhat.
A következő gyakorlatból elsajátíthatja, hogyan találhat meg regisztrált adategységeket.

## 3. gyakorlat: Regisztrált adategységek keresése

Ebben a gyakorlatban az **Azure Data Catalog**-portál használatával korábban regisztrált adategységeket fog megkeresni és megtekinteni a hozzájuk tartozó metaadatokat. Az **Azure Data Catalog** számos eszközt biztosít az adategységek kereséséhez, köztük az egyszerű kulcsszavas keresést, az interaktív szűrőket és egy összetett keresési szintaxist a „kiemelt” felhasználók számára.

### Így találhat meg regisztrált adategységeket

Az **Azure Data Catalog** egyszerű, de hatékony keresési szintaxisának köszönhetően egyszerűen hozhatók létre olyan lekérdezések, amelyekkel megtalálhatók a felhasználók számára szükséges adatok. Az **Azure Data Catalog** keresőjével kapcsolatos részletekért lásd: [Data Catalog Search syntax reference](https://msdn.microsoft.com/library/azure/mt267594.aspx) (A Data Catalog keresési szintaxisának leírása).

Az **Azure Data Catalog** a következő keresési lehetőségeket biztosítja:

- Kulcsszavas keresés
- Szűrés
- Összetett keresés

Emellett a megjelenítendő adategységek köre is leszűkíthető. Az **Azure Data Catalog** a következő megtekintési lehetőségeket biztosítja:

- Tulajdonságok megtekintése
- Oszlopok megtekintése
- Előnézet megtekintése

Ebben a példában egy kulcsszavas keresést végzünk el. Az **Azure Data Catalog** keresője számos lekérdezési módszerrel rendelkezik. Ebben a példában egy **csoportosításos** keresési lekérdezést alkalmazunk.

**Lekérdezési módszerek**

|Módszer|Használat|Példa
|---|---|---
|Tulajdonság keresése|Csak olyan adatforrásokat ad vissza, amelyeknél a keresett kifejezés a megadott tulajdonsághoz tartozik|name:termék
|Logikai operátorok|A keresések logikai műveletekkel bővíthetők vagy szűkíthetők, a jelen oldal Logikai operátorok szakaszában leírtak szerint|pénzügyi NOT vállalati
|Csoportosítás zárójelekkel|A lekérdezés részei zárójelekkel csoportosíthatók a logikai elkülönítés érdekében, különösen a logikai operátorokkal együtt használva|name:termék AND (tags:illusztráció OR tags:fénykép)
|Összehasonlító operátorok|A szám és adat adattípusú tulajdonságok esetében a nem egyenlő összehasonlítások is használhatók|creationTime:>11/05/14

Ebben a példában egy **csoportosításos** kereséssel olyan adatokat keresünk, amelyeknek a neve „termék”, a címkék között pedig szerepel az „illusztráció” vagy a „fénykép” szó.

1. Lépjen a https://azure.microsoft.com/services/data-catalog helyre, majd kattintson **Az első lépések** elemre, és jelentkezzen be az **Azure Data Catalog**-portálra.
2. A **Search Data Catalog** (Keresés a Data Catalogban) mezőben adjon meg egy **csoportosításos** lekérdezést: (**tags:illusztráció OR tags:fénykép**).
3. Kattintson a Keresés ikonra, vagy nyomja le az Enter billentyűt. Az **Azure Data Catalog** megjeleníti az ennek a keresési lekérdezésnek megfelelő adategységeket.

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

Ebben a gyakorlatban az **Azure Data Catalog**-portál használatával a katalógusban regisztrált Adventure Works-adategységeket keresett és tekintett meg.

<a name="annotating"/>
## 4. gyakorlat: Regisztrált adatforrások ellátása megjegyzésekkel

Ebben a gyakorlatban az **Azure Data Catalog**-portál használatával a katalógusban korábban regisztrált adategységeket fog megjegyzésekkel ellátni. A megadott megjegyzések kiegészítik és megerősítik az adatforrásból a regisztrálás során kinyert metaadatokat, és sokkal könnyebben megtalálhatóvá és értelmezhetővé teszik az adategységeket. Mivel a **Data Catalog** minden egyes felhasználója hozzáadhatja a saját megjegyzéseit, így minden olyan felhasználó, akinek meglátásai vannak az adatokra vonatkozóan, könnyedén megoszthatja azokat.

### Így adhat megjegyzéseket az adategységekhez

1. Lépjen a https://azure.microsoft.com/services/data-catalog helyre, majd kattintson **Az első lépések** elemre, és jelentkezzen be az **Azure Data Catalog**-portálra.
2. Kattintson a **Discover** (Felderítés) gombra.
3. Válasszon ki egy vagy több **adategységet**. Ebben a példában válassza ki a **ProductPhoto** elemet, és írja be: „Termékfényképek marketingcélú felhasználásra.”
4. Adjon meg egy **leírást**, amely segíteni fog másoknak feltárni és értelmezni, miért és hogyan használják a kiválasztott adategységet. Írja be például, hogy „Termékképek”. Lehetősége van további címkék hozzáadására és oszlopok megtekintésére is.
5. Most megpróbálhatja az adategységeket a katalógushoz hozzáadott leíró metaadatok használatával megkeresni és szűrni.

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

Ebben a gyakorlatban leíró információkat adott hozzá a regisztrált adategységekhez, hogy a katalógus felhasználói számukra érthető kifejezések használatával deríthessenek fel adatforrásokat.

> [AZURE.NOTE] A Data Catalog Standard kiadása egy üzleti szószedetet tartalmaz, amelynek segítségével a katalógus-rendszergazdák meghatározhatnak egy központi üzleti elnevezési rendszert. A katalógus felhasználói ezután a szószedet kifejezéseivel jelölhetik meg az adategységeket. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása)  

## 5. gyakorlat: Metaadatok közösségi hozzáadása

Ebben a gyakorlatban egy másik felhasználóval együttműködve ad hozzá metaadatokat egy adategységhez a katalógusban. Az **Azure Data Catalog** közösségi megjegyzésmegosztási elve bármely felhasználó számára lehetővé teszi, hogy címkéket, leírásokat és egyéb metaadatokat adjon meg, így bárki, aki meglátásokkal rendelkezik egy adategységet és a felhasználását illetően, rögzítheti és más felhasználók számára elérhetővé teheti a meglátásait.

> [AZURE.NOTE] Ha ezen oktatóprogram során nem tud egy másik felhasználóval együttműködni, az sem jelent problémát. Bármely felhasználó, aki hozzáfér az adatkatalógushoz, tetszés szerint hozzáadhatja a saját meglátásait. A metaadatok közösségi megosztási elvének köszönhetően a katalógus tartalma és a katalógus metaadat-gazdagsága az idő haladtával egyre nő.

### Így adhat közösségi megjegyzéseket az adategységekhez

Kérje meg egy munkatársát, hogy ismételje meg a fenti [Regisztrált adatforrások ellátása megjegyzésekkel](#annotating) gyakorlatot. Miután a munkatársai megjegyzéseket adtak egy adategységhez, például egy ProductPhoto adategységhez, Ön több megjegyzést is lát majd.

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

Ebben a gyakorlatban felfedezte az **Azure Data Catalog** közösségi metaadat-megosztási lehetőségeit, amelyekkel a katalógus bármely felhasználója megjegyzéseket fűzhet az általa felfedezett adategységekhez.

## 6. gyakorlat: Kapcsolódás az adatforrásokhoz

Ebben a gyakorlatban az **Azure Data Catalog**-portál használatával egy Microsoft Excel-alapú adatforráshoz fog kapcsolódni.

> [AZURE.NOTE] Fontos észben tartani, hogy az **Azure Data Catalog** nem a konkrét adatforrásokhoz biztosít hozzáférést a felhasználóknak – egyszerűen csak megkönnyíti ezek felderítését és értelmezését. Amikor a felhasználók csatlakoznak egy adatforráshoz, az általuk választott ügyfélalkalmazás a Windows rendszerbeli hitelesítő adataikat használja fel, vagy megkéri őket, hogy adják meg a szükséges hitelesítő adatokat. Ha a felhasználó korábban még nem kapott hozzáférést az adatforráshoz, a csatlakozás előtt be kell szereznie az erre irányuló jogosultságot.

### Így kapcsolódhat egy Excel-alapú adatforráshoz

1. Lépjen a https://azure.microsoft.com/services/data-catalog helyre, majd kattintson **Az első lépések** elemre, és jelentkezzen be az **Azure Data Catalog**-portálra.
2. Kattintson a **Discover** (Felderítés) gombra.
3. Válasszon ki egy adategységet. Ebben a példában válassza a ProductCategory elemet.
4. Válassza az **Open In** > **Excel** (Megnyitás a következőben: Excel) lehetőséget.

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. A **Microsoft Excel Security Notice** (Microsoft Excel biztonsági figyelmeztetés) ablakban kattintson az **Enable** (Engedélyezés) gombra.
6. Nyissa meg a **ProductCategory.odc** fájlt.
7. Az adatforrás megnyílt az Excelben.

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

Ebben a gyakorlatban egy, az **Azure Data Catalog** portál használatával felfedezett adatforráshoz kapcsolódott. Az **Azure Data Catalog**-portál lehetővé teszi a felhasználók számára, hogy közvetlenül, az **Open in…** (Megnyitás a következőben…) menübe integrált ügyfélalkalmazások használatával, illetve az adategység metaadatai között megadott kapcsolódási helyet felhasználva bármilyen szabadon választott alkalmazás használatával kacsolódjanak az adatforrásokhoz.

## 7. gyakorlat: Adatforrások metaadatainak eltávolítása

Ebben a gyakorlatban az **Azure Data Catalog**-portál használatával előnézeti adatokat fog eltávolítani a regisztrált adategységekből, és adategységeket fog törölni a katalógusból.

> [AZURE.NOTE] A katalógus alapértelmezett viselkedése szerint bármely felhasználó regisztrálhat bármilyen adatforrást, és bármely felhasználó törölhet bármilyen regisztrált adatforrást. Az **Azure Data Catalog Standard kiadásának** felügyeleti lehetőségei további lehetőségeket nyújtanak, amelyekkel az adategységek saját tulajdonba vehetők, és korlátozható az adategységeket felfedezni és törölni jogosult felhasználók köre.

Az **Azure Data Catalog**ban az adategységek egyesével vagy csoportosan is törölhetők.

### Így törölhet egyszerre több adategységet

1. Lépjen a https://azure.microsoft.com/services/data-catalog helyre, majd kattintson **Az első lépések** elemre, és jelentkezzen be az **Azure Data Catalog**-portálra.
2. Kattintson a **Discover** (Felderítés) gombra.
3. Válasszon ki egy vagy több adategységet.
4. Kattintson a **Delete** (Törlés) gombra.

Ebben a gyakorlatban regisztrált adategységeket távolított el a katalógusból.

## 8. gyakorlat: Regisztrált adatforrások felügyelete

Ebben a gyakorlatban az **Azure Data Catalog** felügyeleti képességeit kihasználva saját tulajdonába vesz egy adategységet, és szabályozza azt, mit fedezhetnek fel a felhasználók, és hogyan felügyelhetik az adategységeket.

> [AZURE.NOTE] Az ebben a gyakorlatban leírt felügyeleti képességek kizárólag az **Azure Data Catalog Standard kiadásában** érhetők el, az**ingyenes kiadásban** nem.
Az **Azure Data Catalog** lehetőséget nyújt az adategységek saját tulajdonba vételére, társtulajdonosok hozzáadására, valamint az adategységek láthatóságának beállítására.

### Így vehetők saját tulajdonba az adategységek és korlátozható a láthatóságuk

1. Lépjen a https://azure.microsoft.com/services/data-catalog helyre, majd kattintson **Az első lépések** elemre, és jelentkezzen be az **Azure Data Catalog**-portálra.
2. Kattintson a **Discover** (Felderítés) gombra.
3. Válasszon ki egy vagy több adategységet.
4. A **Properties** (Tulajdonságok) panel **Management** (Felügyelet) szakaszában kattintson a **Take Ownership** (Saját tulajdonba vétel) gombra.
5. A láthatóság korlátozásához kattintson az **Owners & These Users** (Tulajdonosok és ezek a felhasználók) lehetőségre.

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

Ebben a gyakorlatban megismerte az **Azure Data Catalog** felügyeleti képességeit, és korlátozta egyes adategységek láthatóságát.

## Összegzés

Ebben az oktatóprogramban megismerte az **Azure Data Catalog** alapvető képességeit, köztük a regisztrálást, a megjegyzésekkel ellátást, a felfedezést és a vállalati adatforrások felügyeletét. Most, hogy teljesítette az oktatóprogramot, ideje megkezdeni a használatot. Regisztrálja az adatforrásokat, amelyeket a csapatával használ, és hívja fel a munkatársai figyelmét is a katalógus használatára.



<!--HONumber=Jun16_HO2-->


