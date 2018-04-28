---
title: Az Azure Search Lucene lekérdezés példák |} Microsoft Docs
description: Az intelligens egyeztetésű keresési, közelségi kapcsolat keresési, kifejezés kiemelése, reguláris kifejezés keresési, és helyettesítő karakteres keresés Lucene a lekérdezés szintaxisa.
author: LiamCa
manager: jlembicz
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: liamca
ms.openlocfilehash: c83b3b0d9c0cc99ba8a76dc4a6b2f83ed6de49dc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Lucene lekérdezési szintaxis példák az Azure Search lekérdezések létrehozása
Lekérdezések létrehozása az Azure Search, használhatja az alapértelmezett vagy [egyszerű lekérdezés szintaxisát](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vagy a tulajdonos alternatív [Lucene Lekérdezéselemzőben az Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). A Lucene Lekérdezéselemzőben összetett lekérdezési szerkezeteket, például mező hatókörű lekérdezések intelligens egyeztetésű keresési, közelségi kapcsolat keresési, kifejezés kiemelése vagy reguláris kifejezést keresési támogatja.

Ebben a cikkben vehetjük példák bemutatásához lekérdezési műveletek érhető el, ha a teljes szintaxisát.

## <a name="viewing-the-examples-in-jsfiddle"></a>A példák JSFiddle megtekintése

Ebben a cikkben szereplő példák összes végrehajtható lekérdezések, amelyek egy előre betöltött Search-index futtatni [JSFiddle](https://jsfiddle.net), parancsfájl és HTML teszteléséhez egy online kód szerkesztése. 

Futtatni azokat, kattintson a jobb gombbal a lekérdezés példa URL-címek egy külön böngészőablakban JSFiddle megnyitásához.

> [!NOTE]
> Az alábbi példák által biztosított adatkészlet alapján elérhető feladatok álló search-index használja ki a [New York OpenData Város](https://nycopendata.socrata.com/) kezdeményezésére. Ezek az adatok nem tekinthető aktuális vagy teljes. Az index van egy Microsoft által biztosított védőfal szolgáltatásra. Nem kell egy Azure-előfizetés vagy az Azure Search kipróbálásához ezeket a lekérdezéseket.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Hogyan lehet meghívni a teljes Lucene elemzése

Minden ebben a cikkben szereplő példák adja meg a **queryType = teljes** keresési feltétel, amely azt jelzi, hogy a teljes szintaxissal Lucene Lekérdezéselemzőben kezeli. 

**1. példa** – kattintson a jobb gombbal a következő lekérdezés részlet megnyitható egy új böngészőlapot JSFiddle betöltő, és a lekérdezés futtatása:

* [& queryType teljes = & Keresés = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Az új böngészőablakban a JavaScript forrás- és a HTML-kimenetében sorrendben egymás mellett. A parancsfájl teljes lekérdezés (nem csak a kódrészletet, ahogy az a hivatkozás) hivatkozik. A teljes lekérdezés URL-címéből minden példa látható. 

Ez a lekérdezés dokumentumok New York City feladatok indexben (nycjobs, a védőfal szolgáltatás betöltött) adja vissza. Kivonatosan mutatja a lekérdezés egyetlen üzleti címek visszaadott határoz meg. A teljes alapul szolgáló lekérdezés a következőképpen történik:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

A **searchFields** paraméter korlátozza a keresési csak az üzleti cím mező. A **queryType** értéke **teljes**, amely arra utasítja az Azure Search Lucene Lekérdezéselemzőben használni ehhez a lekérdezéshez.

> [!NOTE]
> A lekérdezés feldolgozása a háttérben, lásd: [hogyan teljes szöveges keresés működik az Azure Search](search-lucene-query-architecture.md). A keresési paraméterek további információkért lásd: [dokumentumok keresése (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Fielded lekérdezési művelet
Ebben a cikkben szereplő példák megadásával módosíthatja a **fieldname:searchterm** konstrukció fielded lekérdezési műveletet, ha a mező egyetlen szó, és a keresési kifejezés is egy szót vagy kifejezést, opcionálisan a logikai operátorokkal együtt meghatározásához. Néhány példa a következők:

* business_title:(senior NOT junior)
* állapot: ("Győr" és "Új Jersey")

Győződjön meg arról, amelyre több karakterláncok idézőjelek között, ha azt szeretné, hogy mindkét karakterláncok egyetlen egységként, mint ebben az esetben a hely mezőben két különálló Város keresése kell kiértékelni. Gondoskodjon arról is, az üzemeltető van nagybetű, az nem látható módon és and

A megadott mezők szerepelnek a **fieldname:searchterm** kereshető mezőnek kell lennie. Lásd: [a Create Index (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) attribútum használata a definíciók mező leírását.

**2. példa** – kattintson a jobb gombbal az alábbi lekérdezés kódrészletet, ez a lekérdezés keres a kifejezés vezető őket, de nem kezdő üzleti címeket:

* [& queryType teljes = & Keresés = business_title:senior nem kezdő](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Intelligens egyeztetésű keresési – példa
Egy intelligens kereséséhez megfelel a feltételeknek, egy hasonló konstrukció rendelkező. / [Lucene dokumentáció](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), intelligens keresések alapuló [Damerau-Levenshtein távolság](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Ehhez az intelligens egyeztetésű keresési, hozzáfűzése a hullámos vonallal "~" szimbólum nem kötelező paraméter, a Szerkesztés távolság értéke 0 és 2 között egyetlen szó végén. Például "kék ~" vagy "kék ~ 1" alakítanák vissza kék kékek és kapcsolás.

**3. példa** – kattintson a jobb gombbal a következő lekérdezés részlet. Ez a lekérdezés keresi a feladatokat a kifejezés társult (ahol az hibásan van megadva):

* [& queryType teljes = & Keresés = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Intelligens lekérdezések nincsenek [elemzett](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), amely meglepő, ha várhatóan származó vagy Lemmatizálás lehet. Lexikális elemző csak hajtható végre teljes feltételek (kifejezés lekérdezés vagy kifejezés lekérdezés). Lekérdezéstípusok hiányos adatokkal (előtag lekérdezés, helyettesítő karaktereknek, regex lekérdezés, intelligens lekérdezés) hozzáadódnak közvetlenül a lekérdezés fa az elemzési fázis kihagyásával. A nem teljes lekérdezési kifejezések végre csak átalakítása lowercasing van.
>

## <a name="proximity-search-example"></a>Közelségi kapcsolat keresési – példa
Közelségi kapcsolat keresések használt kifejezések kereséséhez, amelyek egymást egy dokumentumban. Helyezze be a tildével "~" kifejezés végén szimbólum szót, amely a közelségi kapcsolat határ létrehozása követ. Például "Szálloda repülőtéri" egy dokumentumot ~ 5 található a feltételek Szálloda és repülőtéri belül minden más 5 szó.

**4. példa** – kattintson a jobb gombbal a lekérdezést. Keresse meg a feladatok "vezető elemző" kifejezéssel, ahol szóközzel elválasztva legfeljebb egy word:

* [& queryType teljes = & Keresés = business_title: "vezető elemző" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**5. példa** – próbálja meg újra eltávolítani a szavakat közötti "vezető elemző" kifejezés azt.

* [& queryType teljes = & Keresés = business_title: "vezető elemző" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Példák kiemelése kifejezés
Kifejezés kiemelése prioritása nagyobb, ha boosted kifejezés, a kifejezés nem tartalmazó dokumentumok viszonyítva tartalmaz dokumentum hivatkozik. Ez eltér profilok pontozási abban a pontozási profil növelése az egyes mezők ahelyett, hogy adott kifejezések. Az alábbi példa segít különbséget szemléltetik.

Vegye figyelembe a pontozási profil növekedhet, megfelel a bizonyos mezőben, például a **genre** musicstoreindex példában. Kiemelése kifejezés használható bizonyos keresési feltételek magasabb, mint a többire további növelése érdekében. Például "rock ^ 2 elektronikus" lesz a kifejezést tartalmazó dokumentumok növelése a **genre** mező magasabb, mint más kereshető mezőt. Továbbá a keresési kifejezés "rock" tartalmazó dokumentumok fog rangsorolandó magasabb, mint az egyéb keresési kifejezés "elektronikus" miatt a kifejezés program értéket (2).

Egy kifejezés növelése, használja a billentyűzettel "^", a program tényezővel (szám), a keresett kifejezés végén szimbólum. Minél nagyobb a program tényező, több megfelelő kifejezés lesz viszonyítva egyéb keresési feltételeket. Alapértelmezés szerint a program tényező: 1. A program tényező pozitívnak kell lennie, bár lehet (például 0,2) kisebb, mint 1.

**6. példa** – kattintson a jobb gombbal a lekérdezést. Keresse meg a kifejezés "számítógép elemző", ahol a szavakat számítógép és az elemzői eredménytelen, de elemző feladatok olyan az eredmények tetején látható feladatokat.

* [& queryType teljes = & Keresés business_title:computer elemző =](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Példa 7** – próbálja meg újra, az idő kiemelése eredmények a kifejezés számítógéppel a kifejezés elemző keresztül mindkét szavak nem léteznek.

* [& queryType teljes = & Keresés = business_title:computer ^ 2 elemző](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Reguláris kifejezés – példa
A reguláris kifejezés keresési egyezést közötti perjellel "/", a leírtak szerint tartalma alapján a [RegExp szolgáltatást osztály](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Példa 8** – kattintson a jobb gombbal a lekérdezést. Keresse meg a feladatok sem vezető vagy beosztott kifejezés.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

Ebben a példában az URL-címe nem megfelelő jeleníti meg a lapon. A probléma megoldásához másolja az alábbi URL-címet, majd illessze be a böngésző URL-címet: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Helyettesítő karakteres keresés – példa
Több általában felismerhető szintaxis is használható (\*) vagy egy (?) karakter helyettesítő karakteres kereséssel. Vegye figyelembe a Lucene lekérdezéselemzőben egyetlen kifejezés, és nem egy kifejezést a szimbólumok használatát támogatja.

**Példa 9** – kattintson a jobb gombbal a lekérdezést. Keresse meg a "program", amely üzleti címek beletartozik a programozási feltételeket és azt programozói előtag tartalmazó feladatok.

* [& queryType = teljes & $select = business_title & keresést = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

Nem használhatja a * és? Keresés az első karakterként szimbólum.

## <a name="next-steps"></a>További lépések
Adja meg a Lucene Lekérdezéselemzőben az a kódot. Az alábbi hivatkozások azt ismertetik, hogyan állíthatja be a keresési lekérdezések .NET-hez és a REST API-t. A hivatkozások a szintaxissal alapértelmezett egyszerű, adatnézetből származó adja meg, hogy ez a cikk alkalmazni kell a **queryType**.

* [A .NET SDK használatával Azure Search-Index lekérdezése](search-query-dotnet.md)
* [A REST API használatával Azure Search-Index lekérdezése](search-query-rest-api.md)

## <a name="see-also"></a>Lásd még

 [Hogyan teljes szöveges keresés az Azure Search működik](search-lucene-query-architecture.md)