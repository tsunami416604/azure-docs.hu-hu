---
title: Adategységek regisztrálása az Azure Data Catalogban
description: Ez az oktatóanyag ismerteti, hogyan regisztrálhatja az adatok at az Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 4bd2b7093100ff24b21b67ea84613ac9b2ec8299
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68950235"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Oktatóanyag: Adateszközök regisztrálása az Azure Data Catalogban

Ebben az oktatóanyagban a regisztrációs eszköz segítségével regisztrálhatja az adatok at az Azure SQL-adatbázis minta a katalógusból. A regisztráció az a folyamat, amelynek során az adatforrásból és a benne található adategységekből kinyert fő szerkezeti metaadatok (például nevek, típusok és helyek) hozzáadódnak a katalógushoz. Az adatforrás és az adategységek az eredeti helyükön maradnak, de a metaadatok használatával a katalógus könnyebben feltárhatóvá és értelmezhetővé teszi őket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adategységek regisztrálása 
> * Adatelemek keresése
> * Adategységek ellátása dekorációkkal
> * Csatlakozás adategységekhez
> * Adategységek felügyelete
> * Adateszközök törlése

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez be kell fejeznie a [rövid útmutatót.](register-data-assets-tutorial.md)

* [Microsoft Azure-előfizetés.](https://azure.microsoft.com/)
* Saját Azure Active [Directory-bérlővel kell rendelkeznie.](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

A Data Catalog beállításához egy Azure-előfizetés tulajdonosának vagy társtulajdonosának kell lennie.

## <a name="register-data-assets"></a>Adategységek regisztrálása

### <a name="register-a-data-source"></a>Adatforrás regisztrálása

Az [Azure SQL-adatbázis-mintából](../sql-database/sql-database-single-database-get-started.md)regisztrálhat adateszközöket (táblákat), de bármilyen támogatott adatforrást használhat, ha olyan adatokkal szeretne dolgozni, amelyek ismerősek és relevánsak a szerepkörszámára. A támogatott adatforrások listájáért lásd: [Supported data sources](data-catalog-dsr.md) (Támogatott adatforrások).

Az ebben az oktatóanyagban használt Azure SQL-adatbázis neve az *RLSTest*.

Most már regisztrálhatja az adatok at az Azure SQL-adatbázis minta az Azure Data Catalog használatával.

1. Nyissa meg az [Azure Data Catalog kezdőlapját,](http://azuredatacatalog.com) és válassza **az Adatok közzététele**lehetőséget.

   ![Azure Data Catalog – Adatok közzététele gomb](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Válassza **az Alkalmazás indítása** lehetőséget a regisztrációs eszköz letöltéséhez, telepítéséhez és futtatásához a számítógépen.

   ![Azure Data Catalog – Indítás gomb](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Az **üdvözlőlapon** válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait.

    ![Azure Data Catalog – Kezdőlap](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. A **Microsoft Azure Data Catalog (Microsoft Azure Data Catalog)** lapon válassza az **SQL Server** és **a Tovább**lehetőséget.

    ![Azure Data Catalog – adatforrások](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Adja meg az Azure SQL-adatbázis-minta SQL Server-kapcsolatának tulajdonságait, és válassza a **CONNECT**lehetőséget.

   ![Azure Data Catalog – SQL Server-kapcsolati tulajdonságok](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Regisztrálja az adategység metaadatait. Ebben a példában **regisztrálja a termékobjektumokat** az Azure SQL-adatbázis mintanévteréből:

    1. A **Kiszolgálói hierarchia** fán bontsa ki az Azure SQL-adatbázismintáját, és válassza a **SalesLT**lehetőséget.

    2. Válassza a Product , **ProductCategory**, ProductDescription és ProductModel **(Termékkategória**, **Termékleírás**és **ProductModel** ) lehetőséget a Ctrl+select billentyűkombinációval.

    3. jelölje ki az **áthelyezésre kijelölt nyilat** (**>**). Ez a művelet az összes kiválasztott objektumot áthelyezi az **Objects to be registered** (Regisztrálandó objektumok) listára.

          ![Azure Data Catalog oktatóanyag – objektumok megkeresése és kiválasztása](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Ha szeretne pillanatképes előnézetet is hozzáadni az adatokról, jelölje be az **Előnézet belefoglalása** jelölőnégyzetet. A pillanatkép minden táblából legfeljebb 20 rekordot tartalmaz, és a rendszer átmásolja a katalógusba.

    5. Ha szeretné, hogy az adatprofil objektumstatisztikáit (például: minimális, maximális és átlagos értékek az oszlopban, sorok száma) bemutató pillanatképet is tartalmazza a katalógus, válassza az **Adatprofil belefoglalása** lehetőséget.

    6. A **Címkék hozzáadása** mezőbe írja be az **értékesítés, a termék,** az azure sql értéket. Ez a művelet hozzáadja az adategységekhez a keresési címkéket. A címkék nagy segítséget nyújtanak abban, hogy a felhasználók megtaláljanak egy bizonyos regisztrált adatforrást.

    7. Adja meg az adatokhoz kapcsolódó **szakértő** nevét (kihagyható).

          ![Azure Data Catalog oktatóanyag – regisztrálandó objektumok](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Válassza **a REGISTER**lehetőséget. Az Azure Data Catalog regisztrálja a kiválasztott objektumokat. Ebben a gyakorlatban a kijelölt objektumok az Azure SQL-adatbázis minta regisztrálva vannak. A regisztrációs eszköz kinyeri a metaadatokat az adategységből, majd az Azure Data Catalogba másolja az adatokat. Az adatok ott maradnak, ahol jelenleg tartózkodnak. Az adatok továbbra is az eredetrendszer adminisztrátorainak és házirendjeinek ellenőrzése alatt maradnak.

          ![Azure Data Catalog – regisztrált objektumok](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. A regisztrált adatforrásobjektumok megtekintéséhez válassza a **Portál megtekintése**lehetőséget. Az Azure Data Catalog portálon ellenőrizze, hogy mind a négy tábla és az adatbázis a rács nézetben (ellenőrizze, hogy a keresősáv egyértelmű).

        ![Objektumok az Azure Data Catalog-portálon](media/register-data-assets-tutorial/data-catalog-view-portal.png)

Ebben a gyakorlatban regisztrált objektumokat az Azure SQL-adatbázis minta, így azok könnyen felderíthető a felhasználók a szervezeten belül.

A következő gyakorlatból elsajátíthatja, hogyan találhatja meg a regisztrált adategységeket.

## <a name="discover-data-assets"></a>Adategységek felderítése

Az Azure Data Catalog felderítési funkciója elsődlegesen két mechanizmust használ: keresés és szűrés.

A keresés nem csupán magától értetődő, de rendkívül hatékony is. Alapértelmezés szerint a keresőkifejezéseket a rendszer összeveti a katalógusban szereplő összes tulajdonsággal, még a felhasználók által beírt dekorációkkal is.

A szűrés a keresést hivatott kiegészíteni. Különböző jellemzőket (például szakértők, adatforrástípusok, objektumtípusok és címkék) adhat meg, és megtekintheti az azokkal egyező adategységeket, illetve az egyezést mutató adategységekre szűkítheti a találatokat.

A keresés és a szűrés kombinációjával gyorsan navigálhat az Azure Data Catalog-ban regisztrált adatforrások között.

Ebben a gyakorlatban az Azure Data Catalog-portál segítségével fogja felderíteni az előző gyakorlat során regisztrált adategységeket. A keresési szintaxissal kapcsolatban lásd: [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (A Data Catalog keresési szintaxisának leírása).

Az alábbiakban különböző példákat hozunk a katalógusban szereplő adategységek felderítésére.  

### <a name="discover-data-assets-with-basic-search"></a>Adategységek felderítése az alapszintű kereséssel

Az alapszintű kereséssel egy vagy több keresőkifejezést megadva végezhet keresést a katalógusban. Megjelenik az összes olyan adategység, amelynek tulajdonságai egyeznek egy vagy több megadott kifejezéssel.

1. válassza a **Kezdőlap** lehetőséget az Azure Data Catalog portálon. Ha bezárta a webböngészőt, nyissa meg az [Azure Data Catalog kezdőlapját.](https://www.azuredatacatalog.com)

2. A keresőmezőbe írja be a `product` kifejezést, majd nyomja le az **ENTER** billentyűt.

    ![Azure Data Catalog – alapszintű szöveges keresés](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Ellenőrizze, hogy mind a négy tábla és az adatbázis megjelenik-e az eredmények között. A **rácsnézet** és **a listanézet** között az eszköztár gombjainak kijelölésével válthat, ahogy az az alábbi képen látható. Figyelje meg, hogy a kereséshez használt kulcsszót a rendszer kijelöli a találatok között. Ez azért van, mert a **Kiemelés** funkció **BE** értékre van állítva. Megadhatja az **eredmények száma oldalanként** beállítás értékét is.

    ![Azure Data Catalog – alapszintű szöveges keresés, találatok](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    A **Keresések** panel bal oldalon, a **Tulajdonságok** panel jobb oldalon található. A **Keresések** panelen módosíthatja a keresési feltételeket, valamint szűrheti a találatokat. A **Tulajdonságok** panelen láthatja a rácsban vagy a listában kiválasztott objektum tulajdonságait.

4. a keresési eredmények között válassza a **Termék** lehetőséget. jelölje ki az **Előnézet,** **Oszlopok**, **Adatprofil**és **Dokumentáció** lapot, vagy a nyílra az alsó ablaktábla kibontásához.  

    ![Azure Data Catalog – alsó panel](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    A **Preview** (Előnézet) lapon a **Product** táblában szereplő adatok előnézete látható.  
5. az **Oszlopok** lapon megkeresheti az adateszköz oszlopainak részleteit (például **nevét** és **adattípusát).**

6. az **Adatprofil** lapon megtekintheti az adatok profilozását (például sorok száma, adatok mérete vagy minimális érték egy oszlopban) az adateszközben.

### <a name="discover-data-assets-with-property-scoping"></a>Adategységek felderítése tulajdonságértékekben való kereséssel

A tulajdonságértékekben való keresés segítségével megkeresheti azokat az adategységeket, amelyeknél a keresőkifejezés és a megadott tulajdonság között egyezés található.

1. A **Szűrők** menü **Objektumtípus** szűrőjének **Tábla** beállításánál törölje az oda korábban esetleg beírt értéket.  

2. A keresőmezőbe írja be a `tags:product` kifejezést, majd nyomja le az **ENTER** billentyűt. Az adatkatalógusban való kereséshez használható összes tulajdonság megtekintéséhez lásd: [Data Catalog Search syntax reference](/rest/api/datacatalog/#search-syntax-reference) (A Data Catalog keresési szintaxisának leírása).

3. Ellenőrizze, hogy a táblák és az adatbázis láthatók-e az eredményekközött.  

    ![Data Catalog – tulajdonságértékekben való keresés, találatok](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>A keresés mentése

1. Az **Aktuális keresés** csoport **Keresés** ablaktáblájában adja meg a keresés nevét, és válassza a **Mentés gombot.**

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

4. Győződjön meg arról, hogy a **Product**, **ProductCategory**és **ProductDescription** táblák és a keresési eredményekben regisztrált Azure SQL-adatbázis jelennek meg.

    ![Azure Data Catalog – összehasonlító találatok](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Az adateszközök felderítésével kapcsolatos részletes információkért olvassa [el Az adateszközök felderítése](data-catalog-how-to-discover.md) című témakört. A keresési szintaxisról további információt az [Adatkatalógus-keresés szintaktikai hivatkozáscímű témakörben talál.](/rest/api/datacatalog/#search-syntax-reference)

## <a name="annotate-data-assets"></a>Adategységek ellátása dekorációkkal

Ebben a gyakorlatban az Azure Data Catalog portál használatával jegyzetel (például leírások, címkék vagy szakértők hozzáadása) meglévő adatelemek a katalógusban. A jegyzetek kiegészítik az adatforrásból a regisztráció során kinyert szerkezeti metaadatokat. A jegyzetelés sokkal könnyebbé teszi az adatelemek felderítését és megértését.

Ebben a gyakorlatban egyetlen adategységet (ProductPhoto) fogunk dekorációkkal ellátni. Hozzáadunk egy rövid nevet és egy leírást a ProductPhoto adategységhez.  

1. Nyissa meg az Azure Data Catalog `tags:product` [kezdőlapját,](https://www.azuredatacatalog.com) és keressen a regisztrált adatelemek megkeresésével.

2. válassza **a ProductModel** elemet a keresési eredmények között.  

3. A **Rövid név** mezőbe írja be: **Product images**, a **Leírás** mezőbe pedig a következőt: **Product photos for marketing materials**.

    ![Azure Data Catalog – ProductPhoto leírása](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    A **Description** (Leírás) mező segít másoknak megismerni és értelmezni, hogy miért és hogyan használják a kiválasztott adategységet. Lehetősége van további címkék hozzáadására és oszlopok megtekintésére is. Az adatforrások között a katalógushoz hozzáadott leíró metaadatok alapján kereshet és szűrhet.

Ezen az oldalon a következő lépéseket is megteheti:

* Szakértőket adhat az adategységekhez. válassza a **Hozzáadás** lehetőséget a **Szakértők** területen.

* Címkéket adhat hozzá az adatkészlet szintjén. válassza a **Hozzáadás** lehetőséget a **Címkék** területen. A címke lehet felhasználói vagy szószedetcímke. A Data Catalog Standard kiadása tartalmaz egy üzleti szószedetet is, amelynek segítségével a katalógus-rendszergazdák megalkothatják a központi üzleti elnevezési rendszert. A katalógus felhasználói ezután a szószedet kifejezéseivel jelölhetik meg az adategységeket. További információ: [A szabályozott címkézés üzleti szószedetének beállítása](data-catalog-how-to-business-glossary.md)

* Címkéket adhat hozzá az oszlopok szintjén. Válassza a **Hozzáadás** lehetőséget a Jegyzetekkel kívánt oszlop **Címkék** csoportában.

* Leírást adhat hozzá az oszlopok szintjén. Töltse ki az oszlop **Leírás** mezőjét. Megtekintheti az adatforrásból kinyert leírási metaadatokat.

* Adjon hozzá **Hozzáférés kérése** típusú információkat, amelyekből a felhasználók megtudhatják, hogyan kérhetnek hozzáférést az adategységhez.
  
* Válassza a **Dokumentáció** lapfület, és adja meg az adategységhez tartozó dokumentációt. Az Azure Data Catalog dokumentációs funkciójával az adatkatalógus egyben tartalomtárként is használható, amely bemutatja az adategységek közötti összefüggéseket is.
  
Ugyanazt a dekorációt egyszerre több adategységhez is hozzáadhatja. Kiválaszthatja például a korábban regisztrált összes adategységet, és megadhat hozzájuk egy szakértőt.

![Azure Data Catalog – dekoráció készítése több adategységhez](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Az Azure Data Catalog közösségi kiszervezést alkalmazó megközelítést használ a dekorációknál. Bármely adatkatalógus-felhasználó hozzáadhat címkéket (felhasználó vagy szószedet), leírásokat és egyéb metaadatokat. Ezzel a felhasználók perspektívát adnak hozzá egy adateszközhöz és annak használatához, és megosztják azt más felhasználókkal.

Az adategységek dekorációkkal való ellátásával kapcsolatban [az adategységek dekorálását bemutató cikkben](data-catalog-how-to-annotate.md) olvashat részletesen.

## <a name="connect-to-data-assets"></a>Csatlakozás adategységekhez

Ebben a gyakorlatban a kapcsolatadatok segítségével az adategységeket egy integrált ügyféleszközben (Excel) és egy nem integrált eszközben (SQL Server Management Studio) is meg fogja nyitni.

> [!NOTE]
> Nem szabad elfelejteni, hogy az Azure Data Catalog nem ad hozzáférést magához az adatforráshoz, csupán megkönnyíti a felderítését és a megértését. Amikor Ön csatlakozik egy adatforráshoz, a választott ügyfélalkalmazás a windowsos hitelesítési adatokat fogja használni, illetve ha szükséges, felszólítja Önt a hitelesítési adatok megadására. Ha korábban még nem kapott hozzáférést az adatforráshoz, akkor a csatlakozáshoz hozzáférést kell kapnia.

### <a name="connect-to-a-data-asset-from-excel"></a>Csatlakozás adategységhez az Excelből

1. A találatok közül válassza ki a **Product** elemet. az eszköztáron válassza a **Megnyitás** gombot, majd az **Excel**lehetőséget.

    ![Azure Data Catalog – csatlakozás adategységhez](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Válassza a **Megnyitás** lehetőséget a letöltési előugró ablakban. Ez böngészőnként eltérően jelenhet meg.

3. A **Microsoft Excel biztonsági közlemény ablakában** válassza az **Engedélyezés**lehetőséget.

    ![Azure Data Catalog – Excel biztonsági előugró ablak](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Tartsa meg az alapértelmezett értékeket az **Adatok importálása** párbeszédpanelen, és válassza az **OK gombot.**

    ![Azure Data Catalog – Excel, adatok beolvasása](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Tekintse meg az adatforrást az Excelben.

    ![Azure Data Catalog – terméktábla az Excelben](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Ebben a gyakorlatban az Azure Data Catalog segítségével felderített adategységekhez fog csatlakozni. Az Azure Data Catalog-portál segítségével közvetlenül, a **Open in** (Megnyitás a következőben) menüben elérhető integrált ügyfélalkalmazásokkal végezheti el a csatlakozást. Ezenfelül tetszőleges alkalmazáshoz csatlakozhat az adategység metaadataiban szereplő csatlakozási helyadatok segítségével. Például az SQL Server Management Studio segítségével csatlakozhat az Azure SQL-adatbázishoz az oktatóanyagban regisztrált adatelemek ben regisztrált adatok eléréséhez.

1. Nyissa meg az **SQL Server Management Studiót**.

2. A **Kapcsolódás kiszolgálóhoz** párbeszédpanelen írja be a kiszolgáló nevét az Azure Data Catalog portál **Tulajdonságok** paneljéről.

3. Adja meg az adategység eléréséhez szükséges megfelelő hitelesítési és bejelentkezési adatokat. Ha nincs hozzáférése, használja a **Hozzáférés kérése** mezőben szereplő adatokat, és kérjen hozzáférést.

    ![Azure Data Catalog – hozzáférés kérése](media/register-data-assets-tutorial/data-catalog-request-access.png)

Válassza **a Kapcsolati karakterláncok megtekintése** lehetőséget, ha ADO.NET, ODBC és OLEDB kapcsolati karakterláncokat szeretne átnézni a vágólapra az alkalmazásban való használatra.

## <a name="manage-data-assets"></a>Adategységek felügyelete

Ebben a lépésben bemutatjuk, hogyan állítható be az adategységek biztonsága. A Data Catalog nem ad hozzáférést magának az adatnak a felhasználókszámára. Az adatok elérését az adatforrás tulajdonosa szabályozza.

A Data Catalog szolgáltatás arra szolgál, hogy adatforrásokat derítsen fel, és megtekintse a katalógusban regisztrált forrásokhoz tartozó metaadatokat. Előfordulhat azonban, hogy az adatforrásokat csak bizonyos felhasználók, illetve adott csoportok tagjai számára szeretné láthatóvá tenni. Ezekben az esetekben a Data Catalog segítségével átveheti a regisztrált adateszközök tulajdonjogát, és szabályozhatja a saját eszközeinek láthatóságát.

> [!NOTE]
> Az ebben a gyakorlatban leírt felügyeleti funkciók kizárólag az Azure Data Catalog Standard kiadásában érhetők el, az ingyenes kiadásban nem.
> Az Azure Data Catalog lehetőséget nyújt az adategységek saját tulajdonba vételére, társtulajdonosok hozzáadására, valamint az adategységek láthatóságának beállítására.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Adategységek birtokbavétele és láthatóságának korlátozása

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com). A **Search** (Keresés) mezőbe írja be a `tags:cycles` kifejezést, majd nyomja le az **ENTER** billentyűt.

2. jelöljön ki egy elemet az eredménylistában, és válassza a **Tulajdonlás lehetőséget** az eszköztáron.

3. A **Tulajdonságok** panel **Kezelés** szakaszában válassza a **Tulajdonátvétel**lehetőséget.

    ![Azure Data Catalog – saját tulajdonba vétel](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. A láthatóság korlátozásához válassza a **Láthatóság** csoport **& A felhasználók tulajdonosai** lehetőséget, majd a **Hozzáadás gombot.** A szövegmezőbe írja be a kívánt e-mail-címet, majd nyomja le az **ENTER** billentyűt.

    ![Azure Data Catalog – hozzáférés korlátozása](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Adategységek eltávolítása

Ebben a gyakorlatban az Azure Data Catalog-portál használatával el fogja távolítani az előnézeti adatokat a regisztrált adategységekből, valamint adategységeket fog törölni a katalógusból.

Az Azure Data Catalogban az adategységek egyesével és csoportosan is törölhetők.

1. Nyissa meg az [Azure Data Catalog honlapját](https://www.azuredatacatalog.com).

2. A **Keresés** mezőbe írja `tags:cycles` be az **ENTER billentyűt,** és válassza az ENTER elemet.

3. Jelöljön ki egy elemet az eredménylistában, és válassza a **Törlés** lehetőséget az eszköztáron az alábbi képen látható módon:

    ![Azure Data Catalog – rácselem törlése](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Ha a listanézetet használja, a jelölőnégyzet az elembal balra látható, ahogy az az alábbi képen látható:

    ![Azure Data Catalog – listaelem törlése](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Ahogy az az alábbi képen is látható, egyszerre több adategységet is kiválaszthat:

    ![Azure Data Catalog – több adategység törlése](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> A katalógus alapértelmezett viselkedése szerint bármely felhasználó regisztrálhat bármilyen adatforrást, és bármely felhasználó törölhet bármilyen regisztrált adatforrást. Az Azure Data Catalog Standard kiadásának felügyeleti lehetőségei további lehetőségeket nyújtanak, amelyekkel az adategységek saját tulajdonba vehetők, és korlátozható az adategységeket felderíteni és törölni jogosult felhasználók köre.

## <a name="summary"></a>Összefoglalás

Ebben az oktatóanyagban bemutattuk az Azure Data Catalog alapvető funkcióit, például a regisztrálást, a dekorálást, a felderítést és a vállalati adategységek felügyeletét. Most, hogy teljesítette az oktatóprogramot, ideje megkezdeni a használatot. Regisztrálja az adatforrásokat, amelyeket a csapatával használ, és hívja fel a munkatársai figyelmét is a katalógus használatára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Támogatott adatforrások](data-catalog-dsr.md)
