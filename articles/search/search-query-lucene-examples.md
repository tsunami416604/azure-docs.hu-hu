---
title: Az Azure Search lekérdezési példák Lucene |} A Microsoft Docs
description: Lucene lekérdezési szintaxis az intelligens keresést, közelségi keresésre, időszak kiemelése, reguláris kifejezés keresése és helyettesítő karakteres keresés.
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: liamca
ms.openlocfilehash: 24fa427ad67a953020370a16b4d156c82a0a1cf6
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036666"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Lucene lekérdezési szintaxis példái, amellyel a lekérdezések az Azure Search szolgáltatásban
Lekérdezések az Azure Search létrehozásánál használható vagy az alapértelmezett [egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) vagy a tulajdonos alternatív [Lucene lekérdezéselemző az Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). A Lucene lekérdezéselemző támogatja az összetett lekérdezési szerkezeteket, például a mező-hatáskörű lekérdezések, intelligens keresést, közelségi keresésre, időszak kiemelési és reguláris kifejezés keresése.

Ez a cikk elolvasásával példák elérhető lekérdezési műveletek bemutatásához, ha a teljes szintaxisát használja.

## <a name="viewing-the-examples-in-jsfiddle"></a>A példák JSFiddle megtekintése

Ebben a cikkben szereplő példák összes végrehajtható lekérdezések futtatásához egy előre betöltött Search-index [JSFiddle](https://jsfiddle.net), egy online Kódszerkesztő parancsfájl és HTML teszteléséhez. 

Futtatási, a jobb gombbal a lekérdezési példa URL-címek egy külön böngészőablakot JSFiddle megnyitásához.

> [!NOTE]
> Az alábbi példák a keresési index által megadott adatkészlet alapján elérhető feladatok álló kihasználhatja a [város, New York OpenData](https://nycopendata.socrata.com/) kezdeményezésére. Ezek az adatok nem az aktuális vagy a teljes kell tekinteni. Az index van egy Microsoft által biztosított védőfal szolgáltatásban. Nem kell egy Azure-előfizetést, vagy próbálja ki ezeket a lekérdezéseket az Azure Search.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Hogyan kell elindítani a teljes Lucene-elemzés

Minden ebben a cikkben szereplő példák adja meg a **queryType = full** keresési paramétert, amely azt jelzi, hogy a teljes szintaxis kezeli a Lucene lekérdezéselemző. 

**1. példa** – kattintson a jobb gombbal a következő nyissa meg egy új böngészőlap, amely JSFiddle betölti és futtatja a lekérdezést a lekérdezési kódrészletet:

* [& queryType = full & keresési = *](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Az új böngészőablakban, a forrás JavaScript és HTML-kimenetet jelenik meg egymás mellett. A parancsfájl teljes lekérdezés (nem csak a kódrészletet, ahogyan az a hivatkozás) hivatkozik. A teljes lekérdezést URL-címéből valamennyi példa látható. 

Ez a lekérdezés dokumentumok a New York City feladatok index (nycjobs, a védőfal szolgáltatás betöltése) adja vissza. A lekérdezés kivonatosan határoz meg a csak üzleti címét adja vissza. A teljes alapul szolgáló lekérdezés a következőképpen történik:

    https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

A **searchFields** paraméter csak az üzleti cím mező korlátozza a keresést. A **queryType** értékre van állítva **teljes**, amely arra utasítja az Azure Search használata a Lucene lekérdezéselemző ehhez a lekérdezéshez.

> [!NOTE]
> A lekérdezés feldolgozása a háttér-információkért lásd: [teljes szöveges keresés működése az Azure Search](search-lucene-query-architecture.md). További információ a keresési paraméterek: [dokumentumok keresése (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Fielded lekérdezési művelet
Ebben a cikkben szereplő példák megadásával módosíthatja egy **fieldname:searchterm** konstrukció meghatározásához fielded lekérdezési műveletet, ha a mező egyetlen szó, és a keresési kifejezést, ha is egy szót vagy kifejezést, igény szerint Logikai operátorok. Néhány példa a következők:

* business_title:(senior NOT junior)
* állapot: ("Győr" és "Új Jersey")

Ügyeljen arra, hogy helyezzen több karakterlánc idézőjelek között, ha azt szeretné, hogy mindkét karakterlánc, kiértékelendő egyetlen egységként, ahogy ebben az esetben a két különböző városok a hely mezőben lévő keresése. Arra is ügyeljen az operátor van nagybetű, az nem látható módon és és.

A megadott mező **fieldname:searchterm** kell lennie egy kereshető mező. Lásd: [Index létrehozása (Azure Search szolgáltatás REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) indexattribútumokat használata a Meződefiníciók részleteiért.

**2. példa** – kattintson a jobb gombbal az alábbi lekérdezési kódrészletet, ez a lekérdezés megkeresi azokat, de nem kezdő az előfizetési időszak vezető üzleti az adatfeliratokat:

* [& queryType = full & keresés nem business_title:senior = kezdő](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Intelligens keresést
Egy intelligens keresés egyezések megkeresi, hogy rendelkezik egy hasonló konstrukció. / [Lucene dokumentáció](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), az intelligens keresés alapján [Damerau-Levenshtein távolság](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Ehhez az intelligens keresést, fűzze hozzá a hullámos vonallal `~` szimbólum egy egyetlen szó, és a egy nem kötelező paraméter, a 0. és 2 közötti értéket, amely megadja a Szerkesztés végén. Ha például `blue~` vagy `blue~1` kék blues és integrációs adna vissza.

**3. példa** – kattintson a jobb gombbal a következő lekérdezési kódrészletet. Ez a lekérdezés átvizsgálja a kifejezés társult (ahol, hibásan van megadva) rendelkező feladatok:

* [& queryType = full & keresési = business_title:asosiate ~](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Az intelligens-lekérdezések [elemzett](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), amely knál, ha, amely szerint vagy morfológiai várt lehet. Lexikai elemzés csak hajtható végre teljes igényei szerint (lekérdezési kifejezés vagy kifejezés lekérdezést). Lekérdezési típusokra hiányos feltételeket (előtag lekérdezés, helyettesítő karaktert tartalmazó lekérdezés, regex lekérdezés, az intelligens lekérdezés) kerülnek közvetlenül a lekérdezés fában, az elemzési fázis kihagyásával. Az egyetlen átalakítás hiányos lekérdezési kifejezések végrehajtott lowercasing van.
>

## <a name="proximity-search-example"></a>Közelségi keresés példa
Közelségi keresés segítségével keresse meg a feltételeket, amelyek egymáshoz közel egy dokumentumban. Egy hullámos vonallal beszúrása "~" kifejezés végén jel követ szavak, amelyek a közelségi határ létrehozása száma. Például "Szálloda repülőtér" ~ 5 megtalálja a a feltételek Szálloda és repülőtér belül minden más 5 szó egy dokumentumban.

**4. példa** – kattintson a jobb gombbal a lekérdezést. Keresse meg a feladatok "vezető elemző" kifejezéssel, amelyben azt nem lehet egynél több szóból választják:

* [& queryType = full & keresési = business_title: "vezető elemző" ~ 1](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**5. példa** – próbálja ki újra eltávolítása a szavak közötti "vezető elemző" kifejezés.

* [& queryType = full & keresési = business_title: "vezető elemző" ~ 0](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Példák kiemelési kifejezés
Kifejezés kiemelési prioritása nagyobb, ha a gyorsított időszak alatt, dokumentum, amely nem tartalmazza a kisbetűs viszonyított tartalmazza a dokumentum hivatkozik. Ez eltér pontozási profilok, hogy miként növelhető a pontozási profilok specifikus használati helyett egyes mezők. Az alábbi példa segít a különbségeket mutatja be.

Fontolja meg a relevanciaprofil, amely serkenti az megfelel egy bizonyos mezőben, például **műfaj** musicstoreindex példában. Kifejezés kiemelési felhasználható bizonyos keresési feltételek magasabb, mint a többi további növelése érdekében. Például "rock ^ elektronikus 2" lesz, amely tartalmazza a keresési kifejezéseket, a dokumentumok növelése a **műfaj** magasabb, mint a többi az indexben kereshető mezőt a mező. A keresési kifejezés "rock" tartalmazó dokumentumok továbbá magasabb, mint a többi "elektronikus" a kifejezés boost érték (2) eredményeként keresési kifejezés besorolásra kerülő.

Jelentősen növelheti a kifejezés, használja a beszúrási jellel "^", a keresett kifejezés végén boost tényezővel (szám) szimbólum. Minél nagyobb a boost tényező, több megfelelő kifejezés lesz más keresési feltételek viszonyítva. Alapértelmezés szerint a boost tényező: 1. Bár a boost tényező pozitívnak kell lennie, 1-nél kisebb (például 0.2-es) lehet.

**6. példa** – kattintson a jobb gombbal a lekérdezést. Keresse meg a feladatok, ha nem adott eredményeket a szavak számítógép és az elemzői, de elemző feladat van az eredmények tetején látható "számítógép elemző" kifejezéssel.

* [& queryType = full & keresési business_title:computer elemző =](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**7. példa** – próbálja meg újra, ez alkalommal kiemelési eredmények a kifejezés számítógépekkel a kifejezés elemző keresztül, ha mindkét szavak nem léteznek.

* [& queryType = full & keresési = business_title:computer ^ 2 elemző](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Reguláris kifejezés példa
Keresés reguláris kifejezés közötti útjaiban perjeleket a "/", a dokumentált tartalma alapján egyezést talál az [RegExp osztály](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Példa 8** – kattintson a jobb gombbal a lekérdezést. Keresse meg a feladatok a következők egyikével vezető vagy beosztott kifejezés.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

Ebben a példában az URL-cím nem fognak megfelelően renderelődni az oldalon. Áthidaló megoldásként másolja az alábbi URL-címet, és illessze be a böngésző URL-címe: `https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Helyettesítő karakteres keresés példa
Általában felismerhető szintaxist használhat több (\*) vagy egy (?) karakter helyettesítő karakteres kereséssel. Vegye figyelembe, hogy a Lucene lekérdezéselemző használatát a szimbólumok egyetlen kifejezés, és a egy kifejezés nem támogatja.

**Példa 9** – kattintson a jobb gombbal a lekérdezést. Keressen rá a "programazonosítója", amelynél a programozási feltételeket és a benne programozói üzleti címek előtagot tartalmazó feladatok.

* [& queryType = teljes & $select = business_title & Keresés = business_title:prog*](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

Nem használhatja a * és? szimbólum a keresési első karaktert.

## <a name="next-steps"></a>További lépések
Adja meg a Lucene lekérdezéselemző az a kód. Az alábbi hivatkozások azt ismertetik, hogyan állítható be a keresési lekérdezések a .NET-hez és a REST API-t. A hivatkozások az alapértelmezett egyszerű szintaxist használ, így a megismert információk adja meg, hogy ez a cikk a alkalmazni kell a **queryType**.

* [A .NET SDK használatával az Azure Search-Index lekérdezése](search-query-dotnet.md)
* [A REST API használatával az Azure Search-Index lekérdezése](search-query-rest-api.md)

## <a name="see-also"></a>Lásd még

 [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)
