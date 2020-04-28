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
ms.openlocfilehash: dfd75ad2c6ae246bfe6ee8b983744b3db07a841f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194941"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search

Az Azure Cognitive Search két Lucene-alapú lekérdezési nyelvet implementál: az [egyszerű lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene lekérdezés-elemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Az egyszerű elemző rugalmasabb, és akkor is megkísérli értelmezni a kérést, ha nem tökéletesen áll. Ennek a rugalmasságnak köszönhetően ez az alapértelmezett megoldás az Azure Cognitive Search-beli lekérdezésekhez. 

Az egyszerű szintaxis a `search` [keresési dokumentumok kérelem](https://docs.microsoft.com/rest/api/searchservice/search-documents)paraméterében átadott lekérdezési kifejezésekhez használatos, nem tévesztendő össze az azonos keresési dokumentumok API-hoz tartozó [$Filter Expressions](search-filters.md) paraméterhez használt [OData szintaxissal](query-odata-filter-orderby-syntax.md) . A `search` és `$filter` a paraméterek eltérő szintaxissal rendelkeznek, és a saját szabályaikat használják a lekérdezések összeállításához, a karakterláncok kiszökéséhez stb.

Bár az egyszerű elemző az [Apache Lucene egyszerű lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) osztályán alapul, az Azure-Cognitive Search megvalósítása kizárja a zavaros keresést. Ha [intelligens keresési](search-query-fuzzy.md) vagy egyéb speciális lekérdezési űrlapokra van szüksége, vegye figyelembe az alternatív [teljes Lucene lekérdezési szintaxist](query-lucene-syntax.md) .

## <a name="invoke-simple-parsing"></a>Egyszerű elemzés meghívása

Az egyszerű szintaxis az alapértelmezett. A hívás csak akkor szükséges, ha a szintaxist teljesről egyszerűre állítja alaphelyzetbe. A szintaxis explicit beállításához használja a `queryType` keresési paramétert. Az érvényes értékek `queryType=simple` a `queryType=full`következők: `simple` vagy, ahol az alapértelmezett `full` , és meghívja a [teljes Lucene lekérdezés-elemzőt](query-lucene-syntax.md) a fejlettebb lekérdezésekhez. 

## <a name="syntax-fundamentals"></a>Szintaxis alapjai

Egy vagy több kifejezéssel rendelkező szöveg érvényes kiindulási pontnak tekintendő a lekérdezés végrehajtásához. Az Azure Cognitive Search a feltételek bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezteti, beleértve a szöveg elemzése során talált módosításokat is.

Ahogy ez hangzik, az Azure Cognitive Search lekérdezés-végrehajtásának egyik aspektusa *, amely váratlan eredményeket eredményezhet,* és a keresési eredmények csökkentése helyett egyre nagyobb mértékben bővül, és a rendszer hozzáadja a bemeneti karakterlánchoz az operátorokat. Az, hogy ez a bővítés ténylegesen bekövetkezik-e, a nem operátorok belefoglalásával, valamint a **searchMode** paraméter beállításával párosul, amely meghatározza, hogy a rendszer hogyan értelmezi a és vagy a viselkedést. További információ: [not operátor](#not-operator).

### <a name="precedence-operators-grouping"></a>Elsőbbségi operátorok (csoportosítás)

A zárójelek használatával allekérdezéseket hozhat létre, beleértve az zárójeles utasításban található operátorokat is. Például `motel+(wifi|luxury)` megkeresi a "Motel" kifejezést és a "WiFi" vagy a "luxus" (vagy mindkettő) tartalmú dokumentumokat.

A mező csoportosítása hasonló, de a csoportosítás egyetlen mezőre vonatkozik. Például `hotelAmenities:(gym+(wifi|pool))` megkeresi a "hotelAmenities" mezőt a "tornaterem" és a "WiFi", a "tornaterem" és a "pool" mezőnél.  

### <a name="escaping-search-operators"></a>Keresési operátorok elől  

Az egyszerű szintaxisban a keresési operátorok a következő karaktereket tartalmazzák:`+ | " ( ) ' \`  

Ha a karakterek bármelyike az indexben található token részét képezi, akkor a lekérdezésben lévő egy fordított perjel (`\`) előtaggal kell elmenekülnie. Tegyük fel például, hogy egy egyéni elemzőt használt a teljes kifejezés jogkivonatok létrehozása, és az index tartalmazza a "Luxury + Hotel" karakterláncot. Ha pontos egyezést szeretne kapni ehhez a jogkivonathoz, szúrjon `search=luxury\+hotel`be egy Escape-karaktert:. 

Ahhoz, hogy a dolgok egyszerűbbek legyenek a tipikus esetekben, két kivétel vonatkozik erre a szabályra, ha nincs szükség a szökésre:  

+ A nem operátort `-` csak akkor kell megszökni, ha az első karakter a szóköz után. Ha a `-` középen (például a `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) jelenik meg, kihagyhatja a szökést.

+ Az utótag operátort `*` csak akkor kell megszökni, ha az utolsó karakter a szóköz előtt. Ha a `*` középen (például a-ben `4*4=16`) megjelenik, nincs szükség a szökésre.

> [!NOTE]  
> Alapértelmezés szerint a standard Analyzer törli és megszakítja a szavakat a kötőjelek, a szóközök, a jelek és más karakterek számára a [lexikális analízis](search-lucene-query-architecture.md#stage-2-lexical-analysis)során. Ha speciális karakterek szükségesek a lekérdezési karakterláncban, előfordulhat, hogy olyan elemzőre van szüksége, amely megőrzi őket az indexben. Bizonyos lehetőségek közé tartoznak a Microsoft természetes [nyelvi elemzők](index-add-language-analyzers.md), amelyek megőrzik az elválasztott szavakat, vagy egy egyéni elemzőt összetettebb mintákhoz. További információ: [részleges feltételek, mintázatok és speciális karakterek](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása az URL-címekben

Győződjön meg arról, hogy az összes nem biztonságos és fenntartott karakter egy URL-címben van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-címben szereplő töredék/horgony azonosító. A karaktert kódolni kell, `%23` ha az URL-címben van használatban. A (z) "&" és a "=" például fenntartott karakterek, amelyek a paramétereket korlátozzák, és értékeket határoznak meg az Azure Cognitive Searchban. További részletekért tekintse meg a [RFC1738: egységes erőforrás-lokátorok (URL)](https://www.ietf.org/rfc/rfc1738.txt) című témakört.

A nem biztonságos karakterek ``" ` < > # % { } | \ ^ ~ [ ]``a következők:. A fenntartott `; / ? : @ = + &`karakterek a következők:.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Lekérdezési méret korlátai

 Korlátozva van az Azure Cognitive Searchba küldendő lekérdezések mérete. Pontosabban megadhatja a legfeljebb 1024 záradékot (a kifejezéseket és a, vagy a, és így tovább). A lekérdezésben szereplő egyes kifejezések méretének körülbelül 32 KB-os korlátja is van. Ha az alkalmazás programozott módon hozza létre a keresési lekérdezéseket, javasoljuk, hogy úgy tervezze meg úgy, hogy ne generáljon nem kötött méretű lekérdezéseket.  

## <a name="boolean-search"></a>Logikai keresés

A lekérdezési karakterláncban logikai operátorokat (és, vagy nem) ágyazhat be, hogy olyan részletes feltételeket hozzon létre, amelyekkel a megfelelő dokumentumok találhatók. 

### <a name="and-operator-"></a>ÉS operátor`+`

A és az operátor egy plusz jel. Például megkeresi a `wifi + luxury` `wifi` és a- `luxury`t tartalmazó dokumentumokat.

### <a name="or-operator-"></a>VAGY operátor`|`

A vagy operátor egy függőleges sáv vagy cső karakter. Például `wifi | luxury` megkeresi a `wifi` vagy `luxury` a vagy a mindkettőt tartalmazó dokumentumokat.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NEM operátor`-`

A NOT operátor egy mínusz jel. Például `wifi –luxury` megkeresi azokat a dokumentumokat, amelyek rendelkeznek `wifi` a kifejezéssel és/vagy nem `luxury`.

A lekérdezési kérelem **searchMode** paramétere azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel (feltéve, hogy `+` a `|` többi feltétel nem vagy operátor.) Az érvényes értékek `any` a `all`következők: vagy.

`searchMode=any`a további eredményekkel együtt növeli a lekérdezések visszahívását, és `-` alapértelmezés szerint "vagy nem" lesz értelmezve. Például `wifi -luxury` megegyeznek azok a dokumentumok, amelyek tartalmazzák a kifejezést `wifi` , vagy amelyek nem tartalmazzák a kifejezést `luxury`.

`searchMode=all`növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt ad meg, és alapértelmezés szerint "és nem" lesz értelmezve. Például a a `wifi -luxury` kifejezést `wifi` tartalmazó dokumentumokat fogja egyeztetni, és nem tartalmazza a "luxus" kifejezést. Ez vitathatatlanul intuitív viselkedést biztosít a `-` kezelő számára. Ezért érdemes megfontolni, hogy `searchMode=all` ahelyett `searchMode=any` , hogy visszahívást szeretne használni a pontossághoz, *és* a felhasználók gyakran használják a keresésekben `-` a kezelőt.

**SearchMode** -beállítás meghatározásakor vegye figyelembe a felhasználói interakciós mintákat a különböző alkalmazásokban található lekérdezésekhez. Azok a felhasználók, akik információt keresnek, nagyobb valószínűséggel tartalmaznak egy operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Előtag-keresés

Az utótag operátor egy csillag `*`. Például a " `lingui*` nyelvi" vagy "linguini" kifejezést fogja megkeresni, figyelmen kívül hagyva az esetet. 

A szűrőkhöz hasonlóan az előtag-lekérdezések pontos egyezést keresnek. Ezért nincs releváns pontozás (az összes találat 1,0-as keresési pontszámot kap). Az előtag-lekérdezések lassúak lehetnek, különösen akkor, ha az index nagy, és az előtag kis számú karakterből áll. 

Ha egy utótag-lekérdezést szeretne végrehajtani, akkor a karakterlánc utolsó részének megfelelő [karakteres keresést](query-lucene-syntax.md#bkmk_wildcard) és a teljes Lucene szintaxist kell használnia.

## <a name="phrase-search-"></a>Kifejezés keresése`"`

A kifejezéses keresés egy vagy több kifejezés lekérdezése, ahol a feltételek bármelyike egyezésnek minősül. A kifejezéses keresés az idézőjelek közé foglalt pontos kifejezés `" "`. Előfordulhat például, hogy `Roach Motel` a (idézőjelek nélkül) megkeresi `Roach` az összes olyan `Motel` dokumentumot, amely bármilyen sorrendben `"Roach Motel"` tartalmaz és/vagy bárhol, (idézőjelekkel) csak az egész kifejezést tartalmazó dokumentumokat fogja össze, és ebben a sorrendben (a szöveg elemzése továbbra is érvényes).

## <a name="see-also"></a>Lásd még  

+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Példák az egyszerű keresésre](search-query-simple-examples.md)
+ [Példák a teljes Lucene keresésre](search-query-lucene-examples.md)
+ [Dokumentumok keresése – REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md) 
