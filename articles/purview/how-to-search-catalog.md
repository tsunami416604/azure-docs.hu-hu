---
title: 'Útmutató: a Data Catalog keresése'
description: Ez a cikk áttekintést nyújt a adatkatalógusok kereséséről.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552739"
---
# <a name="search-the-azure-purview-data-catalog"></a>Keresés az Azure-beli hatáskörébe Data Catalog

Ez a cikk az Azure hatáskörébe Data Catalog különböző keresési funkcióinak használatát ismerteti.

## <a name="search-the-catalog-for-assets"></a>Eszközök keresése a katalógusban

Az eszköz keresésének lépései a következők:

1. [Nyissa meg az eszköz keresése párbeszédpanelt](#open-the-asset-search-dialog) a **keresési katalógus** lehetőség kiválasztásával.
1. [Adja meg a keresési kifejezéseket](#enter-search-terms) a feltételeknek megfelelő jellemzőkkel rendelkező eszközök kereséséhez.
1. [Állítsa be a gyors szűrőket](#set-quick-filters) a keresés szűkítéséhez.
1. [Indítsa el a keresést](#start-the-search) , és lépjen a keresési eredményekhez.

Nem számít, hogy a keresési feltételek megadása előtt vagy után beállította-e a gyors szűrőket.

Ha nincsenek keresési kifejezések, és nincsenek szűrők, a keresési eredmények között szerepelnie kell az összes eszköznek.

### <a name="open-the-asset-search-dialog"></a>Az eszköz keresése párbeszédpanel megnyitása

Nyissa meg az eszköz keresése párbeszédpanelt a **keresési katalógus** lehetőség kiválasztásával.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="A &quot;keresési katalógus&quot; alatt található egy bal oldali ablaktábla keresési szűrőkkel, valamint egy jobb oldali ablaktábla a legutóbbi keresésekkel." border="true":::

A Keresés párbeszédpanel gyors szűrőket, keresési előzményeket és a nemrégiben elért eszközök listáját jeleníti meg.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="A keresési lista a jobb oldali ablaktáblán, a &quot;Search Catalog&quot; alatt található." border="true":::

### <a name="enter-search-terms"></a>Keresési kifejezések megadása

Adjon meg egy vagy több keresési kifejezést a **keresési katalógusban**. A beíráskor a legutóbbi keresésekben szereplő keresési kifejezések megjelennek a **legutóbbi keresésekben**, a javasolt keresési feltételek a **keresési javaslatokban** vannak felsorolva, és a megfelelő adategységek szerepelnek az **eszközökre vonatkozó javaslatok** listájában.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Képernyőfelvétel a keresési katalógus mezőben megadott keresés eredményeiről":::

A keresési eredmények csak egy vagy több olyan tulajdonsággal rendelkeznek, amelyek megfelelnek a keresési feltételeknek. Ezek a tulajdonságok közé tartozik az eszköz neve, az eszköz típusa, a besorolások és a névjegyek.

#### <a name="types-of-search-criteria"></a>Keresési feltételek típusai

Az Azure-beli hatáskörébe a következő típusú keresési feltételek támogatottak.

> [!Note]
> Minden nagybetű esetében mindig logikai operátorokat (**és**, **vagy** **nem**) kell megadni. Ellenkező esetben az eset nem számít, és nem végez felesleges szóközöket.

- **struktúra**: **struktúrát** tartalmazó dokumentumok keresése.
- **struktúra-adatbázis**: pontosan **struktúra-adatbázist** tartalmazó dokumentumok keresése.
- **struktúra vagy adatbázis**: olyan dokumentumok keresése, amelyek **struktúrát** vagy **adatbázist** tartalmaznak, vagy mindkettőt.
- **struktúra és adatbázis**, **kaptár && adatbázis**: a **struktúrát** és az **adatbázist** tartalmazó dokumentumok keresése.
- **struktúra és (adatbázis vagy raktár)**: a **struktúrát** , illetve az **adatbázist** vagy a **tárházat** tartalmazó dokumentumokat, vagy mindkettőt keresi.
- **struktúra nem adatbázis**: **struktúra**-t tartalmazó dokumentumok keresése, **adatbázis** nélkül.
- **HIV**: a **HIV**-vel kezdődő szót tartalmazó dokumentumok keresése. Például a **HIV**, a **kaptár**, a **hivbar** (* egy Wild kártya, amely tetszőleges számú karakternek felel meg).

### <a name="set-quick-filters"></a>Gyors szűrők beállítása

A keresési eredmények listája a **keresési katalógusban** megadott keresési kifejezéseken, valamint a gyors szűrőkhöz kiválasztott értékeken alapul.

Egy gyors szűrő korlátozza a keresési eredmények listáját olyan eszközökre, amelyek egy jellemző kijelölt értékével rendelkeznek. A szűrő tartalmaz egy legördülő listát és egy szövegmezőt. A legördülő lista az *aktuális* keresési eredményekben szereplő jellemzők értékeit jeleníti meg. A lista egyes értékei mellett az adott értékkel rendelkező aktuális keresési eredmények számának száma. Ha kijelöl egy értéket a listából, a keresési eredmények az adott értékkel rendelkező eszközökre lesznek korlátozva. Csak egy értéket választhat ki.

A legördülő lista kialakításához használt aktuális keresési eredményeket a következők határozzák meg:

- A **keresési katalógusban** megadott keresési kifejezések. 
- A gyors szűrőkben kiválasztott értékek

Íme egy példa az "eszköz típusának" gyors szűrésére.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Az eszköz típusának gyors szűrése példa." border="true":::

Megadhat szöveget a szövegmezőben, hogy a legördülő listában szereplő értékeket a szövegnek megfelelő vagy részben egyező értékekre korlátozza. A szövegmező használatára vonatkozó példákat a gyors szűrés [: szűrés az eszköz típusa alapján](#search-quick-filter-filter-by-asset-type)és a [Keresés gyors szűrése: szűrés besorolás szerint](#search-quick-filter-filter-by-classification)jelölőnégyzetben talál.

#### <a name="search-quick-filter-filter-by-asset-type"></a>Keresés a gyors szűrőben: szűrés az eszköz típusa szerint

Az eszköz típus szerinti szűréséhez használja az **eszköz típusának** gyors szűrőjét. A legördülő lista az aktuális keresési eredmények között található, a keresési feltételek és a gyors szűrők által meghatározott adattípusokat jeleníti meg. Az egyes típusoknál az adott típusú eszközök száma látható.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Az eszköz típusa gyors szűrők kiemelve. Megjeleníti az eszközök típusát és a darabszámot." border="true":::

Válassza ki az eszköz típusát, hogy a keresési eredményeket az adott típusú eszközökre korlátozza. Csak egy típust választhat ki.

Ha csak azokat az adattípusokat szeretné megjeleníteni, amelyeknek a neve megegyezik egy karakterlánccal, írja be a karakterláncot a szövegmezőbe. Ha például csak az **SQL** értékkel rendelkező típusú eszközöket szeretné megjeleníteni a nevükben, adja meg az **SQL** értéket.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="A Quick Filters (gyors szűrők) ablaktáblán az &quot;eszköz típusa&quot; SQL van. Az SQL-t tartalmazó eszközök listája három bejegyzést mutat be." border="true":::

Válassza ki az eszköz típusát, hogy a keresési eredményeket az adott típusú eszközökre korlátozza. Csak egy típust választhat ki.

#### <a name="search-quick-filter-filter-by-classification"></a>Keresés a gyors szűrőben: szűrés besorolás szerint

Az eszközök besorolása alapján történő szűréshez használja a **besorolási** gyors szűrőt. A legördülő lista azokat a besorolásokat jeleníti meg, amelyek az aktuális keresési eredményekben egy vagy több eszközhöz vannak rendelve, a keresési feltételek és a gyors szűrők alapján. Minden besorolásnál megjelenik a besoroláshoz hozzárendelt eszközök száma.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="A besorolási gyors szűrők ki vannak emelve." border="true":::

Válassza ki a besorolást, hogy a keresési eredményeket a besoroláshoz hozzárendelt eszközökre korlátozza. Csak egy besorolást választhat ki.

Ha csak azokat a besorolásokat szeretné megjeleníteni, amelyeknek a neve megegyezik egy karakterlánccal, írja be a karakterláncot a szövegmezőbe. Ha például csak a nevükben szereplő **számú** besorolást szeretné megjeleníteni, adja meg a **számot**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="A gyors szűrők ablaktáblán a besorolás a &quot;Bank&quot;, a felsorolt besorolások pedig tartalmazzák ezt az értéket." border="true":::

Válasszon ki egy besorolást, hogy a keresési eredményeket a besoroláshoz hozzárendelt eszközökre korlátozza. Egynél több besorolást nem lehet kijelölni.

#### <a name="search-quick-filter-filter-by-contacts"></a>Keresés a gyors szűrőben: szűrés névjegyek alapján

A *kapcsolattartó* olyan személy, aki tulajdonosként vagy szakértőként van hozzárendelve egy adott eszközhöz. Az eszköz adatainak megtekintésekor a névjegyek a **névjegyek** lapon jelennek meg.

Két módon kereshet olyan eszközöket, amelyekhez hozzá van rendelve egy adott partner.

- Adja meg a kapcsolattartó neve vagy egy részét a **keresési katalógusban** , és végezzen keresést. A keresési eredmények közé tartoznak azok az adategységek, amelyeknek a neve megegyezik a keresett kifejezéssel.
- Válassza ki az érdeklődési kapcsolatot a **kapcsolattartó** gyors szűrése területen, és végezzen keresést.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="A gyors szűrők ablaktáblán található személy értéke &quot;Darren&quot;. A javaslatok ablaktáblán három javaslat található." border="true":::

## <a name="search-example"></a>Példa keresése

Tegyük fel, hogy egy feltételezett példát láthatunk, hogy a keresési kifejezések és a gyors szűrők hogyan hatnak a keresési eredmények meghatározására. Különösen figyelni fogjuk az **Azure Blob Storage** típusú eszközök számát.

- Az első keresés nem szerepel a megadott keresési kifejezés nélkül, és nincsenek kiválasztva értékek a gyors szűrőkben. A keresés megkeresi a katalógus összes eszközét. A keresési eredmények listája és az **eszköz típusa** gyors szűrő felfedése:

    - A keresési eredmények listája 164 230 objektumot tartalmaz, ami a katalógus összes eszköze.
    - Az **eszköz típusa** legördülő lista 43 bejegyzést tartalmaz. Ezek a katalógusban szereplő összes tárgyieszköz-típus. Mivel minden eszköz egy és csak egy típusból áll, a 43-es adategységek számának összege 164 230.
    - Az **Azure Blob Storage** eszköz típusa az **eszköz típusának** gyors szűrése legördülő listájának első bejegyzése. Az értékeket darabszám szerint rendezi a rendszer, így az **Azure Blob Storage** a leggyakoribb adategység-típus. A száma 118 174.

- Most beírjuk **parquet** a parketta **kifejezést a keresési katalógusba** , és egy másik kereséssel. A keresési eredmények csak a **Parquet** jellemzőkkel rendelkező eszközöket tartalmazzák. Ez csökkenti az összes számadatot, a következőképpen:

    - A keresési eredmények listáját 493 egység tartalmazza. A katalógusban szereplő 164 230-eszközök 493 csak a "Parque" tulajdonságnak megfelelő jellemzőkkel rendelkezik.
    - Az **eszköz típusa** legördülő lista 15 bejegyzést tartalmaz. Az 493-es eszközök mindegyike 15 típus egyike, és a 15 típus darabszámának összege 493.
    - Az **Azure Blob Storage** típusú eszközökhöz 456. A másik 37-as (493 mínusz 456) eszköz a másik 14 típus egyike.

- Most megtekintjük egy másik gyors szűrő legördülő listáját, a **besorolást**:

    - A találatok listájában 12 besorolás található a 493-eszközökhöz. Az 493-es adategységek száma nem a 493-es számú, mert a besorolások száma egy adott eszközhöz rendelhető hozzá.
    - A **személy nevének** besorolása 36-eszközökhöz van hozzárendelve, több mint bármely más besorolás.

- Kiválasztjuk a **személy nevének** besorolását. A keresési eredmények listája a vártnál 36 egységre csökken, mivel a **személy nevének** száma 36 volt. Ezek egyike sem **Azure Blob Storage** típusú eredmény.

Azt is megállapítjuk, hogy nincs olyan eszköz, amelynek típusa az **Azure Blob Storage** , amely megfelel a **parkettának**, és amely a **személy nevének** besorolását tartalmazza.

## <a name="start-the-search"></a>A keresés elindítása

A kereséskor a keresési **katalógusban** megadott keresési kifejezések az eszköz jellemzőivel egyeznek meg. Ezek a tulajdonságok a következők: név, típus, besorolás és névjegyek. A megfelelő jellemzőkkel rendelkező eszközök megjelennek a keresési eredmények listájában, kivéve, ha egy gyors szűrő kizárja őket.

A keresési feltételek megadása és a gyors szűrők beállítása után indítsa el a keresést az alábbi módszerek egyikével:

- A beírt feltételek alapján történő kereséshez válassza a keresés ikont ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), nyomja le az **ENTER** billentyűt, vagy válassza a **keresési eredmények megtekintése** lehetőséget.
- Egy korábbi keresés feltételeinek megkereséséhez válassza ki azokat a **legutóbbi keresések** közül.
- A javasolt kifejezések használatával történő kereséshez válassza ki őket a **keresési javaslatok** közül.

Válasszon ki egy adategységet az **eszköz javaslatai** közül, hogy közvetlenül az eszköz részleteket tartalmazó lapjára lépjen. A keresés nem történt meg.

A javaslatok és a felhasználói keresések eredményeinek listája némileg eltérő lehet. A javaslatok lista eredményei a zavaros egyezéseken alapulnak, míg a felhasználó által kezdeményezett keresési eredmények a pontos egyezéseken alapulnak.

A keresés során megjelenik a **keresési eredmények** lap, és felsorolja a keresés által talált eszközöket.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="A contoso keresési értékének keresési eredményeit bemutató képernyőkép.":::

Az eszköz részleteinek megtekintéséhez válassza ki az eszköz nevét.

A keresési eredmények lap alján található vezérlők használatával navigáljon az egyéb keresési eredmények oldalaira.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Képernyőfelvétel: a keresési eredmények oldalain való átjárás.":::

### <a name="sort-search-results"></a>Keresési eredmények rendezése

A **Rendezés szempontja** alapján rendezheti a keresési eredményeket **relevancia** vagy **név** szerint.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="A keresési eredmények rendezését bemutató képernyőkép. Ebben a példában a rendezés legördülő lista a relevancia értékre van állítva.":::

### <a name="search-results-dynamic-filters"></a>Keresési eredmények dinamikus szűrői

A **keresési eredmények** oldalon található **szűrő** ablaktábla olyan szűrőket tartalmaz, amelyek az eszközök dinamikus szűrését biztosítják a keresési eredmények listájában. A szűrés dinamikus, hogy a további szűrők a szűrők kiválasztása alapján jelenjenek meg.

A dinamikus szűrők jelölőnégyzete a legördülő listában szereplő minden értéknél megjelenik. Ezekkel a jelölőnégyzetekkel tetszőleges számú értéket szűrheti.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Keresési eredmények dinamikus szűrője: szűrés az eszköz típusa szerint

Ha kijelöl egy eszköz típusát az **eszköz típusa** legördülő listán, a dinamikus szűrők megjelennek, amelyek további lehetőségeket biztosítanak a keresési eredmények szűkítéséhez. A szűrők a kiválasztott eszköz típusától függően változnak. Ha például a **Azure SQL Database** lehetőséget választja, a dinamikus szűrők megjelennek a kiszolgáló, az adatbázis és a séma számára. A szűrők értékei a kiválasztott típus keresési eredményeiben lévő eszközökből származnak.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="A Azure SQL Database szűrő eleme az egyetlen &quot;eszközoldali&quot; típusú tétel, amely ki van választva. Az objektum típusának keresési eredménye is ki van emelve." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Keresési eredmények dinamikus szűrője: szűrés besorolás szerint

A **besorolási** lista minden besorolása legalább egy, a keresési eredmények listáján szereplő tételre vonatkozik. Válasszon ki egy vagy több besorolást, hogy szűkítse a keresési eredményeket a kiválasztott besorolások eszközeire.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="A &quot;keresési eredmények&quot; besorolási szűrő kiemelve." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Keresési eredmények szűrőinek szerkesztése és törlése

Szűrő eltávolításához törölje a szűrő neve melletti jelölőnégyzet jelölését.

### <a name="recently-accessed-assets"></a>Legutóbb elért eszközök

A kibontott keresőmező **legutóbb elért** szakasza megjeleníti a legutóbb elért eszközöket, ha vannak ilyenek.

- Válassza az **összes megtekintése** lehetőséget a **közelmúltban elért** szakaszban a legutóbb elért eszközök teljes listájának megtekintéséhez.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Képernyőfelvétel: a kibontott keresőmező legutóbb elérhető szakasza.":::

   Megjelenik a nemrég elért eszközök listája.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="A nemrég elért eszközök listáját megjelenítő képernyőkép.":::

- A név szerinti szűréshez írjon be egy keresési karakterláncot a **szűrés név alapján**.

- Ha el szeretné távolítani az elemeket a listából, jelölje ki azokat a jelölőnégyzetekkel, majd válassza az **Eltávolítás** lehetőséget.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Képernyőfelvétel: elemek eltávolítása a közelmúltban elért eszközök listájáról.":::

- A teljes lista törléséhez válassza a **Törlés** lehetőséget.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="A nemrég elért eszközök listájának törlését bemutató képernyőkép":::

### <a name="search-assets"></a>Keresés az eszközök között

A **kezdőlapon** kívül számos oldalon található **keresési eszközök** mező felül van. Például itt található egy eszköz adatai lap, amelynek Kiemelt **keresési eszközei** vannak.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="A Kiemelt keresési objektumokat tartalmazó eszköz adatait megjelenítő képernyőkép":::

Válassza a **Keresés az eszközök** között lehetőséget, hogy egy olyan keresőmezőt indítson el, amelyik a **kezdőlapon** található **keresési katalógusból** származik, és ugyanazokkal a képességekkel rendelkezik.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="A kibontott keresési eszközök panelt ábrázoló képernyőfelvétel":::

## <a name="next-steps"></a>Következő lépések

- [Szószedet használati feltételeinek létrehozása, importálása és exportálása](how-to-create-import-export-glossary.md)
- [Az üzleti kifejezések sablonjának kezelése Szószedet](how-to-manage-term-templates.md)
