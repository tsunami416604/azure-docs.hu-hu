---
title: Egyszerű lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search teljes szöveges keresési lekérdezéséhez használt egyszerű lekérdezési szintaxis referenciája.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: d07364e20cc11abc52ad9b308eb5daed8a65c146
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923381"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search

Az Azure Cognitive Search két Lucene-alapú lekérdezési nyelvet implementál: az [egyszerű lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Az egyszerű elemző rugalmasabb, és akkor is megkísérli értelmezni a kérést, ha nem tökéletesen áll. Ennek a rugalmasságnak köszönhetően ez az alapértelmezett megoldás az Azure Cognitive Search-beli lekérdezésekhez. 

Az egyszerű szintaxis a `search` [keresési dokumentumok kérelem](/rest/api/searchservice/search-documents)paraméterében átadott lekérdezési kifejezésekhez használatos, nem tévesztendő össze az azonos keresési dokumentumok API-hoz tartozó [$Filter Expressions](search-filters.md) paraméterhez használt [OData szintaxissal](query-odata-filter-orderby-syntax.md) . A `search` és a `$filter` Paraméterek eltérő szintaxissal rendelkeznek, és a saját szabályaikat használják a lekérdezések összeállításához, a karakterláncok kiszökéséhez stb.

Bár az egyszerű elemző az [Apache Lucene egyszerű lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) osztályán alapul, az Azure-Cognitive Search megvalósítása kizárja a zavaros keresést. Ha [intelligens keresési](search-query-fuzzy.md) vagy egyéb speciális lekérdezési űrlapokra van szüksége, vegye figyelembe az alternatív [teljes Lucene lekérdezési szintaxist](query-lucene-syntax.md) .

## <a name="invoke-simple-parsing"></a>Egyszerű elemzés meghívása

Az egyszerű szintaxis az alapértelmezett. A hívás csak akkor szükséges, ha a szintaxist teljesről egyszerűre állítja alaphelyzetbe. A szintaxis explicit beállításához használja a `queryType` keresési paramétert. Az érvényes értékek a következők `queryType=simple` `queryType=full` : vagy, ahol az `simple` alapértelmezett, és `full` meghívja a [teljes Lucene lekérdezés-elemzőt](query-lucene-syntax.md) a fejlettebb lekérdezésekhez. 

## <a name="syntax-fundamentals"></a>Szintaxis alapjai

Egy vagy több kifejezéssel rendelkező szöveg érvényes kiindulási pontnak tekintendő a lekérdezés végrehajtásához. Az Azure Cognitive Search a feltételek bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezteti, beleértve a szöveg elemzése során talált módosításokat is.

Ahogy ez hangzik, az Azure Cognitive Search lekérdezés-végrehajtásának egyik aspektusa *, amely váratlan eredményeket eredményezhet,* és a keresési eredmények csökkentése helyett egyre nagyobb mértékben bővül, és a rendszer hozzáadja a bemeneti karakterlánchoz az operátorokat. Az, hogy ez a bővítés ténylegesen bekövetkezik-e, a nem operátorok belefoglalásával, valamint a **searchMode** paraméter beállításával párosul, amely meghatározza, hogy a rendszer hogyan értelmezi a és vagy a viselkedést. További információ: [not operátor](#not-operator).

### <a name="precedence-operators-grouping"></a>Elsőbbségi operátorok (csoportosítás)

A zárójelek használatával allekérdezéseket hozhat létre, beleértve az zárójeles utasításban található operátorokat is. Például `motel+(wifi|luxury)` megkeresi a "Motel" kifejezést és a "WiFi" vagy a "luxus" (vagy mindkettő) tartalmú dokumentumokat.

A mező csoportosítása hasonló, de a csoportosítás egyetlen mezőre vonatkozik. Például `hotelAmenities:(gym+(wifi|pool))` megkeresi a "hotelAmenities" mezőt a "tornaterem" és a "WiFi", a "tornaterem" és a "pool" mezőnél.  

### <a name="escaping-search-operators"></a>Keresési operátorok elől  

Az egyszerű szintaxisban a keresési operátorok a következő karaktereket tartalmazzák: `+ | " ( ) ' \`  

Ha a karakterek bármelyike az indexben található token részét képezi, akkor a lekérdezésben lévő egy fordított perjel () előtaggal kell elmenekülnie `\` . Tegyük fel például, hogy egy egyéni elemzőt használt a teljes kifejezés jogkivonatok létrehozása, és az index tartalmazza a "Luxury + Hotel" karakterláncot. Ha pontos egyezést szeretne kapni ehhez a jogkivonathoz, szúrjon be egy Escape-karaktert:  `search=luxury\+hotel` . 

Ahhoz, hogy a dolgok egyszerűbbek legyenek a tipikus esetekben, két kivétel vonatkozik erre a szabályra, ha nincs szükség a szökésre:  

+ A nem operátort csak akkor kell `-` megszökni, ha az első karakter a szóköz után. Ha a `-` középen (például a) jelenik meg `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` , kihagyhatja a szökést.

+ Az utótag operátort `*` csak akkor kell megszökni, ha az utolsó karakter a szóköz előtt. Ha a `*` középen (például a-ben) megjelenik `4*4=16` , nincs szükség a szökésre.

> [!NOTE]  
> Alapértelmezés szerint a standard Analyzer törli és megszakítja a szavakat a kötőjelek, a szóközök, a jelek és más karakterek számára a [lexikális analízis](search-lucene-query-architecture.md#stage-2-lexical-analysis)során. Ha speciális karakterek szükségesek a lekérdezési karakterláncban, előfordulhat, hogy olyan elemzőre van szüksége, amely megőrzi őket az indexben. Bizonyos lehetőségek közé tartoznak a Microsoft természetes [nyelvi elemzők](index-add-language-analyzers.md), amelyek megőrzik az elválasztott szavakat, vagy egy egyéni elemzőt összetettebb mintákhoz. További információ: [részleges feltételek, mintázatok és speciális karakterek](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása az URL-címekben

Győződjön meg arról, hogy az összes nem biztonságos és fenntartott karakter egy URL-címben van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-címben szereplő töredék/horgony azonosító. A karaktert kódolni kell, `%23` Ha az URL-címben van használatban. A (z) "&" és a "=" például fenntartott karakterek, amelyek a paramétereket korlátozzák, és értékeket határoznak meg az Azure Cognitive Searchban. További részletekért tekintse meg a [RFC1738: egységes erőforrás-lokátorok (URL)](https://www.ietf.org/rfc/rfc1738.txt) című témakört.

A nem biztonságos karakterek a következők: ``" ` < > # % { } | \ ^ ~ [ ]`` . A fenntartott karakterek a következők: `; / ? : @ = + &` .

### <a name="querying-for-special-characters"></a>Speciális karakterek lekérdezése

Bizonyos esetekben érdemes lehet speciális karaktert keresni, például a "❤" Emoji vagy a "€" jelet. Ebben az esetben ügyeljen arra, hogy a használt analizátor ne szűrje ki ezeket a karaktereket.  A standard Analyzer figyelmen kívül hagyja a speciális karakterek nagy részét, hogy ne legyenek tokenek az indexben.

Ezért az első lépés az, hogy olyan elemzőt használjon, amely figyelembe veszi az elemek jogkivonatait. A "szóköz" elemző például figyelembe veszi a szóközökkel elválasztott összes karakteres sorozatot tokenként, így a "❤" karakterlánc tokennek tekintendő. Azt is megteheti, hogy az analizátor, például a Microsoft angol analizátor ("en. Microsoft"), figyelembe veszi a "€" karakterláncot tokenként. Az [analizátor tesztelésével](/rest/api/searchservice/test-analyzer) megtekintheti, hogy milyen jogkivonatokat hoz létre egy adott lekérdezéshez.

Unicode-karakterek használatakor győződjön meg arról, hogy a lekérdezési URL-címben a szimbólumok megfelelően vannak kimaradva (például "❤" esetén a escape-sorozatot kellene használni `%E2%9D%A4+` ). A Poster automatikusan elvégzi ezt a fordítást.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a> Lekérdezési méret korlátai

 Korlátozva van az Azure Cognitive Searchba küldendő lekérdezések mérete. Pontosabban megadhatja a legfeljebb 1024 záradékot (a kifejezéseket és a, vagy a, és így tovább). A lekérdezésben szereplő egyes kifejezések méretének körülbelül 32 KB-os korlátja is van. Ha az alkalmazás programozott módon hozza létre a keresési lekérdezéseket, javasoljuk, hogy úgy tervezze meg úgy, hogy ne generáljon nem kötött méretű lekérdezéseket.  

## <a name="boolean-search"></a>Logikai keresés

A lekérdezési karakterláncban logikai operátorokat (és, vagy nem) ágyazhat be, hogy olyan részletes feltételeket hozzon létre, amelyekkel a megfelelő dokumentumok találhatók. 

### <a name="and-operator-"></a>ÉS operátor `+`

A és az operátor egy plusz jel. Például `wifi + luxury` megkeresi a és a-t tartalmazó dokumentumokat `wifi` `luxury` .

### <a name="or-operator-"></a>VAGY operátor `|`

A vagy operátor egy függőleges sáv vagy cső karakter. Például `wifi | luxury` megkeresi a vagy a vagy a `wifi` `luxury` mindkettőt tartalmazó dokumentumokat.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NEM operátor `-`

A NOT operátor egy mínusz jel. Például `wifi –luxury` megkeresi azokat a dokumentumokat, amelyek rendelkeznek a `wifi` kifejezéssel és/vagy nem `luxury` .

A lekérdezési kérelem **searchMode** paramétere azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel (feltéve, hogy `+` `|` a többi feltétel nem vagy operátor.) Az érvényes értékek a következők: `any` vagy `all` .

`searchMode=any` a további eredményekkel együtt növeli a lekérdezések visszahívását, és alapértelmezés szerint `-` "vagy nem" lesz értelmezve. Például `wifi -luxury` megegyeznek azok a dokumentumok, amelyek tartalmazzák a kifejezést, `wifi` vagy amelyek nem tartalmazzák a kifejezést `luxury` .

`searchMode=all` növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt ad meg, és alapértelmezés szerint "és nem" lesz értelmezve. Például `wifi -luxury` a a kifejezést tartalmazó dokumentumokat fogja egyeztetni, `wifi` és nem tartalmazza a "luxus" kifejezést. Ez vitathatatlanul intuitív viselkedést biztosít a kezelő számára `-` . Ezért érdemes megfontolni, hogy `searchMode=all` ahelyett `searchMode=any` , hogy visszahívást szeretne használni a pontossághoz, *és* a felhasználók gyakran használják a `-` keresésekben a kezelőt.

**SearchMode** -beállítás meghatározásakor vegye figyelembe a felhasználói interakciós mintákat a különböző alkalmazásokban található lekérdezésekhez. Azok a felhasználók, akik információt keresnek, nagyobb valószínűséggel tartalmaznak egy operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel.

<a name="prefix-search"></a>

## <a name="wildcard-prefix-matching--"></a>Helyettesítő karakteres előtag (*,?)

A "Start with" lekérdezésekkel a kifejezés hátralévő részében adjon hozzá egy utótag operátort helyőrzőként. Használjon csillagot `*` több karakternél vagy `?` egyetlen karakternél. Például `lingui*` a "nyelvi" vagy a "linguini" kifejezésnek kell megfelelnie, figyelmen kívül hagyva a kis-és nagybetűket. 

A szűrőkhöz hasonlóan az előtag-lekérdezések pontos egyezést keresnek. Ezért nincs releváns pontozás (az összes találat 1,0-as keresési pontszámot kap). Ügyeljen arra, hogy az előtag-lekérdezések lassúak legyenek, különösen akkor, ha az index nagy, és az előtag kis számú karakterből áll. Egy alternatív módszer, például az Edge n-Gram jogkivonatok létrehozása is gyorsabban elvégezhető.

Más helyettesítő karakteres lekérdezési változatok esetében, például utótag vagy Infix, amely a kifejezés végére vagy közepére illeszkedik, használja a [helyettesítő karakteres keresés teljes Lucene szintaxisát](query-lucene-syntax.md#bkmk_wildcard).

## <a name="phrase-search-"></a>Kifejezés keresése `"`

A kifejezéses keresés egy vagy több kifejezés lekérdezése, ahol a feltételek bármelyike egyezésnek minősül. A kifejezéses keresés az idézőjelek közé foglalt pontos kifejezés `" "` . Előfordulhat például, hogy `Roach Motel` (idézőjelek nélkül) olyan dokumentumokat keres `Roach` `Motel` , amelyek bármilyen sorrendben tartalmaznak és/vagy bárhol vannak ( `"Roach Motel"` az idézőjelekkel együtt) csak az egész kifejezést tartalmazó dokumentumokat egyeznek meg, és ebben a sorrendben (a lexikális analízis továbbra is érvényes).

## <a name="see-also"></a>Lásd még  

+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Példák az egyszerű keresésre](search-query-simple-examples.md)
+ [Példák a teljes Lucene keresésre](search-query-lucene-examples.md)
+ [Dokumentumok keresése – REST API](/rest/api/searchservice/Search-Documents)
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md)