---
title: Az Azure Data Catalogban az adategységek regisztrálása
description: Adategységek regisztrálása az Azure Data Catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: tutorial
ms.date: 04/08/2019
ms.openlocfilehash: e89bd4806e2bb2369c100e948be51dcf32f2e123
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362423"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Oktatóanyag: Az Azure Data Catalogban az adategységek regisztrálása

Ebben az oktatóanyagban a frissítésregisztráló eszköz regisztrálása az Azure SQL database mintául szolgáló származó adategységeket a katalógusban használja. A regisztráció az a folyamat, amelynek során az adatforrásból és a benne található adategységekből kinyert fő szerkezeti metaadatok (például nevek, típusok és helyek) hozzáadódnak a katalógushoz. Az adatforrás és az adategységek az eredeti helyükön maradnak, de a metaadatok használatával a katalógus könnyebben feltárhatóvá és értelmezhetővé teszi őket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adategységek regisztrálása 
> * Adategységek keresése
> * Adategységek ellátása dekorációkkal
> * Csatlakozás adategységekhez
> * Adategységek felügyelete
> * Adategységek törlése

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez meg kell adnia a [rövid](register-data-assets-tutorial.md).

* A [Microsoft Azure](https://azure.microsoft.com/) előfizetés.
* Rendelkeznie kell a saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

A Data Catalog beállításához az Azure-előfizetés tulajdonosa vagy társtulajdonosa kell lennie.

## <a name="register-data-assets"></a>Adategységek regisztrálása

### <a name="register-a-data-source"></a>Adatforrás regisztrálása

Az adategységeket (táblákat) regisztrálása egy [Azure SQL database mintául szolgáló](../sql-database/sql-database-single-database-get-started.md), de bármilyen támogatott adatforrást használhat, ha szeretné, hogy ismer, vagy amelyek munkaköre adatokkal való munka. A támogatott adatforrások listájáért lásd: [Supported data sources](data-catalog-dsr.md) (Támogatott adatforrások).

Az Azure SQL-adatbázis neve, ebben az oktatóanyagban használt *RLSTest*.

Az Azure SQL database mintául szolgáló származó adategységeket az Azure Data Catalog használatával most már regisztrálhatja.

1. Nyissa meg a [Azure Data Catalog kezdőlapját](http://azuredatacatalog.com) válassza **adatok közzététele**.

   ![Azure Data Catalog – Adatok közzététele gomb](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Válassza ki **alkalmazás indítása** letöltéséhez, telepítéséhez, és a regisztrációs eszköz futtathatja a számítógépen.

   ![Azure Data Catalog – Indítás gomb](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Az a **üdvözlő** lapon jelölje be **jelentkezzen be a** , és adja meg a hitelesítő adatait.

    ![Azure Data Catalog – Kezdőlap](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Az a **Microsoft Azure Data Catalog** lapon jelölje be **SQL Server** és **tovább**.

    ![Azure Data Catalog – adatforrások](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Az Azure SQL database mintául szolgáló adja meg az SQL Server-kapcsolati tulajdonságokat, és válassza ki **CONNECT**.

   ![Azure Data Catalog – SQL Server-kapcsolati tulajdonságok](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Regisztrálja az adategység metaadatait. Ebben a példában regisztrálnia **termék** objektumokat az Azure SQL database mintául szolgáló névtér:

    1. Az a **Kiszolgálóhierarchia** fán, bontsa ki az Azure SQL database mintául szolgáló, és válassza ki **SalesLT**.

    2. Válassza ki **termék**, **ProductCategory**, **ProductDescription**, és **ProductModel** által a Ctrl + válassza.

    3. Válassza ki a **áthelyezési által kiválasztott nyíl** (**>**). Ez a művelet az összes kiválasztott objektumot áthelyezi az **Objects to be registered** (Regisztrálandó objektumok) listára.

          ![Azure Data Catalog oktatóanyag – objektumok megkeresése és kiválasztása](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Ha szeretne pillanatképes előnézetet is hozzáadni az adatokról, jelölje be az **Előnézet belefoglalása** jelölőnégyzetet. A pillanatkép legfeljebb 20 bejegyzést tartalmazhat az minden táblát tartalmaz, és azt a katalógusba másolja.

    5. Ha szeretné, hogy az adatprofil objektumstatisztikáit (például: minimális, maximális és átlagos értékek az oszlopban, sorok száma) bemutató pillanatképet is tartalmazza a katalógus, válassza az **Adatprofil belefoglalása** lehetőséget.

    6. Az a **címkéket adhat hozzá** írja be a következőt **értékesítés, termék, az azure sql**. Ez a művelet hozzáadja az adategységekhez a keresési címkéket. A címkék nagy segítséget nyújtanak abban, hogy a felhasználók megtaláljanak egy bizonyos regisztrált adatforrást.

    7. Adja meg az adatokhoz kapcsolódó **szakértő** nevét (kihagyható).

          ![Azure Data Catalog oktatóanyag – regisztrálandó objektumok](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Válassza ki **REGISZTRÁLÁSA**. Az Azure Data Catalog regisztrálja a kiválasztott objektumokat. Ebben a gyakorlatban a kijelölt objektumok az Azure SQL database mintából vannak regisztrálva. A regisztrációs eszköz kinyeri a metaadatokat az adategységből, majd az Azure Data Catalogba másolja az adatokat. A data marad, ahol a jelenleg marad. Adatok a rendszergazdák a vezérlő és a szabályzatok a származási rendszer alatt maradnak.

          ![Azure Data Catalog – regisztrált objektumok](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. A regisztrált adatforrás-objektumok megtekintéséhez válasszon **portál megtekintése**. Az Azure Data Catalog-portálon győződjön meg arról, hogy mind a négy táblát, és az adatbázis a táblázatos nézetben (Győződjön meg arról, hogy a keresősávba törlése).

        ![Objektumok az Azure Data Catalog-portálon](media/register-data-assets-tutorial/data-catalog-view-portal.png)

Ebben a gyakorlatban regisztrált az Azure SQL database mintául szolgáló objektumait, hogy azok könnyen megtalálhatók legyenek egyszerűen a felhasználók a szervezetben.

A következő gyakorlatból elsajátíthatja, hogyan találhatja meg a regisztrált adategységeket.

## <a name="discover-data-assets"></a>Adategységek felderítése

Az Azure Data Catalog felderítési funkciója elsődlegesen két mechanizmust használ: keresés és szűrés.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Különböző jellemzőket (például szakértők, adatforrástípusok, objektumtípusok és címkék) adhat meg, és megtekintheti az azokkal egyező adategységeket, illetve az egyezést mutató adategységekre szűkítheti a találatokat.

A Keresés és szűrés együttes használatával, gyorsan válthat, amely az Azure Data Catalogban regisztrált adatforrások.

Ebben a gyakorlatban az Azure Data Catalog-portál segítségével fogja felderíteni az előző gyakorlat során regisztrált adategységeket. A keresési szintaxissal kapcsolatban lásd: [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (A Data Catalog keresési szintaxisának leírása).

Az alábbiakban különböző példákat hozunk a katalógusban szereplő adategységek felderítésére.  

### <a name="discover-data-assets-with-basic-search"></a>Adategységek felderítése az alapszintű kereséssel

Az alapszintű kereséssel egy vagy több keresőkifejezést megadva végezhet keresést a katalógusban. Megjelenik az összes olyan adategység, amelynek tulajdonságai egyeznek egy vagy több megadott kifejezéssel.

1. Válassza ki **kezdőlap** az Azure Data Catalog-portálon. Ha már bezárta a böngészőt, lépjen a [Azure Data Catalog kezdőlapját](https://www.azuredatacatalog.com).

2. A keresőmezőbe írja be a `product` kifejezést, majd nyomja le az **ENTER** billentyűt.

    ![Azure Data Catalog – alapszintű szöveges keresés](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Győződjön meg arról, hogy mind a négy táblát, és az adatbázis az eredmények között. Válthat között **rácsnézet** és **listanézet** az eszköztárban található gombokra kattintva a következő képen látható módon. Figyelje meg, hogy a kereséshez használt kulcsszót a rendszer kijelöli a találatok között. Ez azért van, mert a **Kiemelés** funkció **BE** értékre van állítva. Megadhatja az **eredmények száma oldalanként** beállítás értékét is.

    ![Azure Data Catalog – alapszintű szöveges keresés, találatok](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    A **Keresések** panel bal oldalon, a **Tulajdonságok** panel jobb oldalon található. A **Keresések** panelen módosíthatja a keresési feltételeket, valamint szűrheti a találatokat. A **Tulajdonságok** panelen láthatja a rácsban vagy a listában kiválasztott objektum tulajdonságait.

4. Válassza ki **termék** a keresési eredmények között. Válassza ki a **előzetes**, **oszlopok**, **Adatprofil**, és **dokumentáció** lapokat, vagy a nyilat az alsó panel kibontásához.  

    ![Azure Data Catalog – alsó panel](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    A **Preview** (Előnézet) lapon a **Product** táblában szereplő adatok előnézete látható.  
5. Válassza ki a **oszlopok** lapon található oszlopok részletes adatait (például **neve** és **adattípus**) az adategységben szereplő.

6. Válassza ki a **Adatprofil** lapján megtekintheti az adatok profiljának (például: sorok száma, mérete, vagy egy oszlop legalacsonyabb értéke) az adategységben szereplő.

### <a name="discover-data-assets-with-property-scoping"></a>Adategységek felderítése tulajdonságértékekben való kereséssel

A tulajdonságértékekben való keresés segítségével megkeresheti azokat az adategységeket, amelyeknél a keresőkifejezés és a megadott tulajdonság között egyezés található.

1. A **Szűrők** menü **Objektumtípus** szűrőjének **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.  

2. A keresőmezőbe írja be a `tags:product` kifejezést, majd nyomja le az **ENTER** billentyűt. Az adatkatalógusban való kereséshez használható összes tulajdonság megtekintéséhez lásd: [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (A Data Catalog keresési szintaxisának leírása).

3. Győződjön meg arról, hogy a táblákat és az adatbázis az eredmények között.  

    ![Data Catalog – tulajdonságértékekben való keresés, találatok](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>A keresés mentése

1. Az a **keresések** ablaktábláján a **aktuális keresés** részen adja meg egy nevet a keresésnek, és válassza ki **mentése**.

    ![Azure Data Catalog – keresés mentése](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Ellenőrizze, hogy a mentett keresés megjelent-e a **Mentett keresések** menüben.

3. Válasszon a mentett kereséseken elvégezhető műveletek közül (**Átnevezés**, **Törlés**, **Mentés alapértelmezettként**).

### <a name="grouping-with-parentheses"></a>Csoportosítás zárójelekkel

Zárójelek segítségével csoportosítva különböző logikai egységekre bonthatja a lekérdezés részeit, ami különösen a logikai operátorokkal együtt alkalmazva lehet hasznos.

1. A keresőmezőbe írja be a `name:product AND (tags:product AND objectType:table)` kifejezést, majd nyomja le az **ENTER** billentyűt.

2. Ellenőrizze, hogy a találatok között csak a **Product** tábla jelent-e meg.

    ![Azure Data Catalog – keresések csoportosítása](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Összehasonlító operátorok

Az összehasonlító operátorok segítségével a szám és adat adattípusú tulajdonságok esetében a nem egyenlő összehasonlítások is használhatók.

1. A keresőmezőbe írja be a `lastRegisteredTime:>"06/09/2016"` kifejezést.

2. Az **Objektumtípus** szűrő **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.

3. Nyomja le az **ENTER** billentyűt.

4. Győződjön meg arról, hogy a **termék**, **ProductCategory**, és **ProductDescription** táblákat és az Azure SQL database regisztrált keresési eredmények.

    ![Azure Data Catalog – összehasonlító találatok](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Lásd: [adategységek felderítése](data-catalog-how-to-discover.md) data calatog kapcsolatos részletes információkat. A keresési szintaxissal kapcsolatban lásd: [Data Catalog keresési szintaxisának referenciája](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Adategységek ellátása dekorációkkal

Ebben a gyakorlatban megjegyzésekkel használhatja az Azure Data Catalog portál (például leírásokat, címkéket vagy szakértőket hozzáadása) meglévő adategységeket a katalógusban. A dekorációk kiegészítik a regisztráció során az adatforrásból kinyert szerkezeti metaadatokat. Jegyzet sokkal könnyebben megtalálhatóvá és értelmezhetővé teszi az adategységeket.

Ebben a gyakorlatban egyetlen adategységet (ProductPhoto) fogunk dekorációkkal ellátni. Hozzáadunk egy rövid nevet és egy leírást a ProductPhoto adategységhez.  

1. Nyissa meg a [Azure Data Catalog kezdőlapját](https://www.azuredatacatalog.com) és a `tags:product` , keresse meg a Microsoftnál regisztrált adategységeket.

2. Válassza ki **ProductModel** a keresési eredmények között.  

3. A **Rövid név** mezőbe írja be: **Product images**, a **Leírás** mezőbe pedig a következőt: **Product photos for marketing materials**.

    ![Azure Data Catalog – ProductPhoto leírása](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    A **Description** (Leírás) mező segít másoknak megismerni és értelmezni, hogy miért és hogyan használják a kiválasztott adategységet. Lehetősége van további címkék hozzáadására és oszlopok megtekintésére is. Kereshet és szűrheti az adatforrásokat a katalógushoz hozzáadott leíró metaadatok használatával.

Emellett érdemes ezen az oldalon az alábbi lépéseket:

* Szakértőket adhat az adategységekhez. Válassza ki **Hozzáadás** a a **szakértők** területen.

* Címkéket adhat hozzá az adatkészlet szintjén. Válassza ki **Hozzáadás** a a **címkék** területen. A címke lehet felhasználói vagy szószedetcímke. A Data Catalog Standard kiadása tartalmaz egy üzleti szószedetet is, amelynek segítségével a katalógus-rendszergazdák megalkothatják a központi üzleti elnevezési rendszert. A katalógus felhasználói ezután a szószedet kifejezéseivel jelölhetik meg az adategységeket. További információk: [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Az irányított címkézéshez használt üzleti szószedet összeállítása)

* Címkéket adhat hozzá az oszlopok szintjén. Válassza ki **Hozzáadás** alatt **címkék** megjegyzésekkel az az oszlop számára.

* Leírást adhat hozzá az oszlopok szintjén. Töltse ki az oszlop **Leírás** mezőjét. Megtekintheti az adatforrásból kinyert leírási metaadatokat.

* Adjon hozzá **Hozzáférés kérése** típusú információkat, amelyekből a felhasználók megtudhatják, hogyan kérhetnek hozzáférést az adategységhez.
  
* Válassza a **Dokumentáció** lapfület, és adja meg az adategységhez tartozó dokumentációt. Az Azure Data Catalog dokumentációs funkciójával az adatkatalógus egyben tartalomtárként is használható, amely bemutatja az adategységek közötti összefüggéseket is.
  
Ugyanazt a dekorációt egyszerre több adategységhez is hozzáadhatja. Kiválaszthatja például a korábban regisztrált összes adategységet, és megadhat hozzájuk egy szakértőt.

![Azure Data Catalog – dekoráció készítése több adategységhez](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Az Azure Data Catalog közösségi kiszervezést alkalmazó megközelítést használ a dekorációknál. Data Catalog bármelyik felhasználója (felhasználói vagy szószedet) címkéket, leírásokat és más metaadatokat adhat hozzá. Ezzel a módszerrel a felhasználók szempontjából hozzáadása egy adategységet, és annak használatára, és a perspektíva megosztása más felhasználókkal.

Az adategységek dekorációkkal való ellátásával kapcsolatban [az adategységek dekorálását bemutató cikkben](data-catalog-how-to-annotate.md) olvashat részletesen.

## <a name="connect-to-data-assets"></a>Csatlakozás adategységekhez

Ebben a gyakorlatban a kapcsolatadatok segítségével az adategységeket egy integrált ügyféleszközben (Excel) és egy nem integrált eszközben (SQL Server Management Studio) is meg fogja nyitni.

> [!NOTE]
> Nem szabad elfelejteni, hogy az Azure Data Catalog nem ad hozzáférést magához az adatforráshoz, csupán megkönnyíti a felderítését és a megértését. Amikor Ön csatlakozik egy adatforráshoz, a választott ügyfélalkalmazás a windowsos hitelesítési adatokat fogja használni, illetve ha szükséges, felszólítja Önt a hitelesítési adatok megadására. Ha korábban még nem kapott hozzáférést az adatforráshoz, akkor a csatlakozáshoz hozzáférést kell kapnia.

### <a name="connect-to-a-data-asset-from-excel"></a>Csatlakozás adategységhez az Excelből

1. A találatok közül válassza ki a **Product** elemet. Válassza ki **Megnyitás a következőben** az eszköztáron, majd válassza **Excel**.

    ![Azure Data Catalog – csatlakozás adategységhez](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Válassza ki **nyílt** a letöltési előugró ablakban. Ez böngészőnként eltérően jelenhet meg.

3. Az a **Microsoft Excel biztonsági figyelmeztetés** ablakban válassza **engedélyezése**.

    ![Azure Data Catalog – Excel biztonsági előugró ablak](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Tartsa meg az alapértelmezéseket a **adatok importálása** párbeszédpanel, amelyen kiválaszthatja **OK**.

    ![Azure Data Catalog – Excel, adatok beolvasása](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Tekintse meg az adatforrást az Excelben.

    ![Azure Data Catalog – terméktábla az Excelben](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Ebben a gyakorlatban az Azure Data Catalog segítségével felderített adategységekhez fog csatlakozni. Az Azure Data Catalog-portál segítségével közvetlenül, a **Open in** (Megnyitás a következőben) menüben elérhető integrált ügyfélalkalmazásokkal végezheti el a csatlakozást. Ezenfelül tetszőleges alkalmazáshoz csatlakozhat az adategység metaadataiban szereplő csatlakozási helyadatok segítségével. Például az SQL Server Management Studio segítségével csatlakozhat az Azure SQL database hozzáférni az adatokhoz a ebben az oktatóanyagban regisztrált adategységeket.

1. Nyissa meg az **SQL Server Management Studiót**.

2. A **Kapcsolódás kiszolgálóhoz** párbeszédpanelen írja be a kiszolgáló nevét az Azure Data Catalog portál **Tulajdonságok** paneljéről.

3. Adja meg az adategység eléréséhez szükséges megfelelő hitelesítési és bejelentkezési adatokat. Ha nincs hozzáférése, használja a **Hozzáférés kérése** mezőben szereplő adatokat, és kérjen hozzáférést.

    ![Azure Data Catalog – hozzáférés kérése](media/register-data-assets-tutorial/data-catalog-request-access.png)

Válassza ki **kapcsolati karakterláncok megtekintése** megtekintéséhez és az ADO.NET, ODBC és OLEDB kapcsolati karakterláncokat másolása a vágólapra az alkalmazásban használható.

## <a name="manage-data-assets"></a>Adategységek felügyelete

Ebben a lépésben bemutatjuk, hogyan állítható be az adategységek biztonsága. A Data Catalog nem hozzáférést a felhasználóknak magukhoz az adatokhoz. Az adatok elérését az adatforrás tulajdonosa szabályozza.

A Data Catalog szolgáltatás arra szolgál, hogy adatforrásokat derítsen fel, és megtekintse a katalógusban regisztrált forrásokhoz tartozó metaadatokat. Előfordulhat azonban, hogy az adatforrásokat csak bizonyos felhasználók, illetve adott csoportok tagjai számára szeretné láthatóvá tenni. Ebben az esetben a Data Catalog segítségével regisztrált adategységek tulajdonjogát, és szabályozhatja az adategységek láthatóságát.

> [!NOTE]
> Az ebben a gyakorlatban leírt felügyeleti funkciók kizárólag az Azure Data Catalog Standard kiadásában érhetők el, az ingyenes kiadásban nem.
> Az Azure Data Catalog lehetőséget nyújt az adategységek saját tulajdonba vételére, társtulajdonosok hozzáadására, valamint az adategységek láthatóságának beállítására.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Adategységek birtokbavétele és láthatóságának korlátozása

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com). A **Search** (Keresés) mezőbe írja be a `tags:cycles` kifejezést, majd nyomja le az **ENTER** billentyűt.

2. Jelöljön ki egy elemet a találatok listájában, és válassza ki **saját tulajdonba vétel** az eszköztáron.

3. Az a **felügyeleti** szakaszában a **tulajdonságok** panelen kattintson **saját tulajdonba vétel**.

    ![Azure Data Catalog – saját tulajdonba vétel](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Válassza ki a láthatóság korlátozásához **tulajdonosok és ezek a felhasználók** a a **látható-e** szakaszt, és válassza **Hozzáadás**. A szövegmezőbe írja be a kívánt e-mail-címet, majd nyomja le az **ENTER** billentyűt.

    ![Azure Data Catalog – hozzáférés korlátozása](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Adategységek eltávolítása

Ebben a gyakorlatban az Azure Data Catalog-portál használatával el fogja távolítani az előnézeti adatokat a regisztrált adategységekből, valamint adategységeket fog törölni a katalógusból.

Az Azure Data Catalogban az adategységek egyesével és csoportosan is törölhetők.

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com).

2. A a **keresési** szöveget adja meg `tags:cycles` válassza **ENTER**.

3. Jelöljön ki egy elemet a találatok listájában, és válassza ki **törlése** az eszköztáron az alábbi képen látható módon:

    ![Azure Data Catalog – rácselem törlése](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    A listanézetet használja, ha a jelölőnégyzet az elemtől balra látható az a következő képen látható módon:

    ![Azure Data Catalog – listaelem törlése](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Ahogy az az alábbi képen is látható, egyszerre több adategységet is kiválaszthat:

    ![Azure Data Catalog – több adategység törlése](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> A katalógus alapértelmezett viselkedése szerint bármely felhasználó regisztrálhat bármilyen adatforrást, és bármely felhasználó törölhet bármilyen regisztrált adatforrást. Az Azure Data Catalog Standard kiadásának felügyeleti lehetőségei további lehetőségeket nyújtanak, amelyekkel az adategységek saját tulajdonba vehetők, és korlátozható az adategységeket felderíteni és törölni jogosult felhasználók köre.

## <a name="summary"></a>Összegzés

Ebben az oktatóanyagban bemutattuk az Azure Data Catalog alapvető funkcióit, például a regisztrálást, a dekorálást, a felderítést és a vállalati adategységek felügyeletét. Most, hogy teljesítette az oktatóprogramot, ideje megkezdeni a használatot. Regisztrálja az adatforrásokat, amelyeket a csapatával használ, és hívja fel a munkatársai figyelmét is a katalógus használatára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Támogatott adatforrások](data-catalog-dsr.md)