---
title: Egyszerű lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: Hivatkozás az Azure Cognitive Search teljes szöveges keresési lekérdezéseihez használt egyszerű lekérdezési szintaxishoz.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258864"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Egyszerű lekérdezésszintaxis az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search két Lucene-alapú lekérdezési nyelvet valósít meg: [az Egyszerű lekérdezéselemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene-lekérdezési elemzőt.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 

Az Azure Cognitive Search, az egyszerű lekérdezés szintaxisa kizárja az intelligens keresési műveletek. Ehelyett használja a teljes Lucene szintaxist az [intelligens kereséshez](search-query-fuzzy.md).

> [!NOTE]
> Az egyszerű lekérdezésszintaxis a [Keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents) API **keresési** paraméterében átadott lekérdezési kifejezésekhez használatos, nem tévesztendő össze az API [$filter](search-filters.md) paraméteréhez használt [OData szintaxissal.](query-odata-filter-orderby-syntax.md) Ezek a különböző szintaxisok saját szabályokat létrehozni lekérdezések, menekülési karakterláncok, és így tovább.
>
> Az Azure Cognitive Search egy alternatív [teljes Lucene lekérdezésszintaxist](query-lucene-syntax.md) biztosít a **keresési** paraméter összetettebb lekérdezéseihez. Ha többet szeretne megtudni a lekérdezéselemzési architektúráról és az egyes szintaxisok előnyeiről, olvassa el [a Teljes szöveges keresés működése az Azure Cognitive Search alkalmazásban.](search-lucene-query-architecture.md)

## <a name="invoke-simple-parsing"></a>Egyszerű elemzés meghívása

Az alapértelmezett egyszerű szintaxis. A meghívás csak akkor szükséges, ha a szintaxist teljesről egyszerűre állítja vissza. A szintaxis explicit beállításához `queryType` használja a keresési paramétert. Az érvényes `queryType=simple` `queryType=full`értékek `simple` közé tartozik a `full` vagy , ahol az alapértelmezett, és meghívja a [teljes Lucene lekérdezéselemzőt](query-lucene-syntax.md) a speciális lekérdezésekhez. 

## <a name="syntax-fundamentals"></a>Szintaktikai alapok

Az egy vagy több kifejezéssel rendelkező szövegek a lekérdezés végrehajtásának érvényes kiindulópontjának minősülnek. Az Azure Cognitive Search a kifejezések bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezik meg, beleértve a szöveg elemzése során talált változatokat is.

Bármilyen egyszerű is ez a hang, az Azure Cognitive Search lekérdezés-végrehajtásának van egy aspektusa, amely váratlan eredményeket *eredményezhet,* és a keresési eredmények csökkentése helyett növekszik, ahogy több kifejezés és operátor hozzáadódik a bemeneti karakterlánchoz. Az, hogy ez a bővítés ténylegesen megtörténik-e, a NOT operátor felvételétől függ, kombinálva egy **searchMode** paraméterbeállítással, amely meghatározza, hogy a NOT értelmezése az AND vagy az OR viselkedés szempontjából történik. További információ: [NOT operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Elsőbbségi operátorok (csoportosítás)

Zárójelek segítségével allekérdezéseket hozhat létre, beleértve a parenthetical utasításon belüli operátorokat is. Például `motel+(wifi||luxury)` a "motel" kifejezést és a "wifi" vagy a "luxus" (vagy mindkettő) tartalmazó dokumentumokat keresi.

A mezőcsoportosítás hasonló, de a csoportosítást egyetlen mezőre ható. Például `hotelAmenities:(gym+(wifi||pool))` a "hotelAmenities" mezőben az "edzőterem" és a "wifi", illetve az "edzőterem" és a "medence" kifejezésre keres.  

### <a name="escaping-search-operators"></a>Menekülő keresési operátorok  

Annak érdekében, hogy a keresési operátorok bármelyikét a keresési szöveg részeként használhassa,`\`a karakter elől egyetlen fordított perjellel ( ) kell előírni. Ha például helyettesítő karakteres `https://`keresést `://` végez a mezőben, ahol `search=https\:\/\/*`a lekérdezési karakterlánc része, adja meg a mezőt. Hasonlóképpen, egy szökött telefonszám minta `\+1 \(800\) 642\-7676`így néz ki .

A menekülést igénylő speciális karakterek a következők:`- * ? \ /`  

Annak érdekében, hogy a dolgok egyszerűek legyenek a tipikusesetekben, két kivétel van ez alól a szabály alól, ahol nincs szükség szökésre:  

+ A NOT `-` operátort csak akkor kell megúszni, ha ez az első karakter a szóköz után, nem akkor, ha egy kifejezés közepén van. Például a következő GUID a escape `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`karakter nélkül érvényes: .

+ Az utótag `*` operátorát csak akkor kell megkerülni, ha ez az utolsó karakter a szóköz előtt, nem akkor, ha egy kifejezés közepén van. Például `4*4=16` nem igényel fordított perjelet.

> [!NOTE]  
> Bár a menekülés tartja tokenek együtt, [lexikális elemzés](search-lucene-query-architecture.md#stage-2-lexical-analysis) indexelés során is szalag őket. A szabványos Lucene-elemző például törli és megtöri a kötőjeleken, a szóközön és más karaktereken lévő szavakat. Ha speciális karakterekre van szüksége a lekérdezési karakterláncban, szükség lehet egy elemzőre, amely megőrzi azokat az indexben. Néhány választási lehetőség: Microsoft természetes [nyelvi elemzők](index-add-language-analyzers.md), amely megőrzi az elválasztott szavakat, vagy egy egyéni analizátor az összetettebb mintákhoz. További információt a Részleges kifejezések, minták és speciális karakterek című [témakörben talál.](search-query-partial-matching.md)

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása URL-címekben

Győződjön meg arról, hogy minden nem biztonságos és fenntartott karakter url-címbe van kódolva. A "#" például nem biztonságos karakter, mert egy URL-cím töredék-/horgonyazonosítója. A karaktert `%23` az URL-címhez kell kódolni. A '&' és a '=' példák a fenntartott karakterekre, mivel elhatárolódnak a paraméterek, és értékeket adnak meg az Azure Cognitive Search-ben. További részletekért tekintse meg [az RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) című témakört.

A nem ``" ` < > # % { } | \ ^ ~ [ ]``biztonságos karakterek . A fenntartott `; / ? : @ = + &`karakterek a következők.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Lekérdezésméret-korlátok

 Az Azure Cognitive Search szolgáltatásba küldhető lekérdezések mérete korlátozva van. Pontosabban legbőlegelhető 1024 záradék (kifejezések egymástól és , VAGY és így tovább). A lekérdezésben megadott bármely kifejezés mérete körülbelül 32 KB-os korlátot jelent. Ha az alkalmazás programozott módon hoz létre keresési lekérdezéseket, azt javasoljuk, hogy úgy tervezze meg, hogy az ne hozzon létre nem kötött méretű lekérdezéseket.  

## <a name="boolean-search"></a>Logikai keresés

Logikai operátorokat (ÉS, VAGY, NEM) ágyazhat be egy lekérdezési karakterláncba, hogy olyan feltételek et hozzon létre, amelyek hez egyező dokumentumok találhatók. 

### <a name="and-operator-"></a>ÉS operátor`+`

Az ÉS operátor egy plusz jel. Megkeresi `wifi+luxury` például a és `wifi` a `luxury`dokumentumait tartalmazó dokumentumokat.

### <a name="or-operator-"></a>OR operátor`|`

A VAGY operátor függőleges sáv vagy csőkarakter. Például `wifi | luxury` olyan dokumentumokat keres, `wifi` amelyek `luxury` vagy vagy mindkettőt tartalmazzák.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NEM operátor`-`

A NOT operátor mínuszjel. Például `wifi –luxury` olyan dokumentumokat keres, `wifi` amelyek kifejezése és/vagy nincs kifejezése. `luxury`

A lekérdezési kérelem **searchMode** paramétere azt szabályozza, hogy a NOT operátorral rendelkező kifejezés ANDed vagy ORed a lekérdezés más kifejezéseivel (feltéve, hogy a többi kifejezésben nincs `+` vagy `|` operátor). Az érvényes `any` `all`értékek közé tartozik a vagy .

`searchMode=any`növeli a lekérdezések visszahívását azáltal, hogy `-` több eredményt ad, és alapértelmezés szerint "VAGY NEM" lesz értelmezve. Például `wifi -luxury` egyezik azokkal a `wifi` dokumentumokkal, amelyek vagy `luxury`tartalmazzák a kifejezést, vagy azokat, amelyek nem tartalmazzák a kifejezést.

`searchMode=all`növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt, és alapértelmezés szerint - kell értelmezni, mint "ÉS NEM". Például, `wifi -luxury` egyezik a `wifi` dokumentumokat, amelyek tartalmazzák a kifejezést, és nem tartalmazzák a "luxus". Ez vitathatatlanul egy intuitív viselkedés `-` az üzemeltető. Ezért érdemes használni `searchMode=all` a `searchMode=any` helyett, ha azt szeretné optimalizálni keres a pontosság helyett `-` visszahívás, *és* a felhasználók gyakran használják az üzemeltető a keresések.

A **searchMode** beállítás meghatározásakor vegye figyelembe a különböző alkalmazások lekérdezéseinek felhasználói beavatkozási mintáit. Azok a felhasználók, akik információt keresnek, nagyobb valószínűséggel vesznek fel operátort egy lekérdezésbe, szemben az okkal a beépített navigációs struktúrákkal rendelkező e-kereskedelmi webhelyekkel.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Előtag keresése

Az utótag operátora csillag. `*` Például, `lingui*` megtalálja a "nyelvi" vagy "linguini", figyelmen kívül hagyva esetben. 

A szűrőkhöz hasonlóan az előtag-lekérdezés is pontos egyezést keres. Mint ilyen, nincs relevancia pontozás (minden eredmény kap egy keresési pontszám 1.0). Az előtag-lekérdezések lassúak lehetnek, különösen akkor, ha az index nagy, és az előtag kis számú karakterből áll. 

Ha a karakterlánc utolsó részében egyező utótag-lekérdezést szeretne végrehajtani, használjon [helyettesítő karakteres keresést](query-lucene-syntax.md#bkmk_wildcard) és a teljes Lucene szintaxist.

## <a name="phrase-search-"></a>Kifejezés keresés`"`

A kifejezéskeresés egy vagy több kifejezés lekérdezése, ahol a kifejezések bármelyike egyezésnek minősül. A kifejezéskeresés egy idézőjelek közé `" "`zárt kifejezés. Például, `Roach Motel` míg (idézőjelek nélkül) keresne dokumentumokat tartalmazó `Roach` és / vagy `Motel` bárhol bármilyen sorrendben, `"Roach Motel"` (idézőjelrel) csak akkor egyezik dokumentumok, amelyek tartalmazzák, hogy a teljes mondat együtt, és ebben a sorrendben (szövegelemzés továbbra is érvényes).

## <a name="see-also"></a>Lásd még  

+ [Példák lekérdezése egyszerű kereséshez](search-query-simple-examples.md)
+ [Lekérdezési példák a teljes Lucene-kereséshez](search-query-lucene-examples.md)
+ [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene lekérdezés szintaxisa](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md) 
