---
title: Egyszerű lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: Hivatkozás az Azure Cognitive Search teljes szöveges keresési lekérdezéseihez használt egyszerű lekérdezési szintaxishoz.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/03/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 3d5a4ddf863115747c27efbca1808d51444aac8c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656173"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Egyszerű lekérdezésszintaxis az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search két Lucene-alapú lekérdezési nyelvet valósít meg: [az Egyszerű lekérdezéselemzőt](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) és a [Lucene-lekérdezési elemzőt.](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) Az Azure Cognitive Search, az egyszerű lekérdezés szintaxisa kizárja az intelligens/moslék beállításokat.  

> [!NOTE]
> Az egyszerű lekérdezésszintaxis a [Keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents) API **keresési** paraméterében átadott lekérdezési kifejezésekhez használatos, nem tévesztendő össze az API [$filter](search-filters.md) paraméteréhez használt [OData szintaxissal.](query-odata-filter-orderby-syntax.md) Ezek a különböző szintaxisok saját szabályokat létrehozni lekérdezések, menekülési karakterláncok, és így tovább.
>
> Az Azure Cognitive Search egy alternatív [teljes Lucene lekérdezésszintaxist](query-lucene-syntax.md) biztosít a **keresési** paraméter összetettebb lekérdezéseihez. Ha többet szeretne megtudni a lekérdezéselemzési architektúráról és az egyes szintaxisok előnyeiről, olvassa el [a Teljes szöveges keresés működése az Azure Cognitive Search alkalmazásban.](search-lucene-query-architecture.md)

## <a name="how-to-invoke-simple-parsing"></a>Hogyan lehet meghívni az egyszerű elemzés

Az alapértelmezett egyszerű szintaxis. A meghívás csak akkor szükséges, ha a szintaxist teljesről egyszerűre állítja vissza. A szintaxis explicit beállításához `queryType` használja a keresési paramétert. Az érvényes `simple|full`értékek `simple` közé tartozik `full` a ( alapértelmezett ) és a Lucene érték. 

## <a name="query-behavior-anomalies"></a>Lekérdezési viselkedési anomáliák

Az egy vagy több kifejezéssel rendelkező szövegek a lekérdezés végrehajtásának érvényes kiindulópontjának minősülnek. Az Azure Cognitive Search a kifejezések bármelyikét vagy mindegyikét tartalmazó dokumentumokat egyezik meg, beleértve a szöveg elemzése során talált változatokat is. 

Bármilyen egyszerű is ez a hang, az Azure Cognitive Search lekérdezés-végrehajtásának van egy aspektusa, amely váratlan eredményeket *eredményezhet,* és a keresési eredmények csökkentése helyett növekszik, ahogy több kifejezés és operátor hozzáadódik a bemeneti karakterlánchoz. Az, hogy ez a bővítés ténylegesen megtörténik-e, a NOT operátor felvételétől függ, egy `searchMode` paraméterbeállítással kombinálva, amely meghatározza, hogy a NOT értelmezése az AND vagy az OR viselkedés szempontjából történik. Az alapértelmezett `searchMode=Any`, és a NOT operátor miatt a művelet vagy `"New York" NOT Seattle` műveletként kerül kiszámításra, így az összes nem Seattle-i várost adja vissza.  

Általában nagyobb valószínűséggel látja ezeket a viselkedéseket a tartalomon kereső alkalmazások felhasználói interakciós mintáiban, ahol a felhasználók nagyobb valószínűséggel vesznek fel operátort egy lekérdezésbe, szemben a több beépített navigációs struktúrával rendelkező e-kereskedelmi webhelyekkel. További információ: [NOT operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logikai operátorok (ÉS, VAGY, NEM) 

Operátorok beágyazhatók egy lekérdezési karakterláncba, hogy olyan feltételek gazdag készletét hozhassák létre, amelyek hez megfelelő dokumentumok találhatók. 

### <a name="and-operator-"></a>ÉS operátor`+`

Az ÉS operátor egy plusz jel. Megkeresi `wifi+luxury` például a és `wifi` a `luxury`dokumentumait tartalmazó dokumentumokat.

### <a name="or-operator-"></a>OR operátor`|`

A VAGY operátor függőleges sáv vagy csőkarakter. Például `wifi | luxury` olyan dokumentumokat keres, `wifi` amelyek `luxury` vagy vagy mindkettőt tartalmazzák.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NEM operátor`-`

A NOT operátor mínuszjel. Például `wifi –luxury` olyan dokumentumokat keres, `wifi` amelyek kifejezése és/vagy nincs `luxury` (és/vagy `searchMode`az általuk szabályozott).

> [!NOTE]  
>  A `searchMode` beállítás azt szabályozza, hogy a NOT operátorral rendelkező kifejezés ANDed vagy `+` ORed-e a lekérdezés többi kifejezésével, vagy `|` operátor hiányában. Visszahívás, `searchMode` amely beállítható `any` (alapértelmezett) `all`vagy . Ha a `any`program használja a programot, az növeli a `-` lekérdezések visszahívását további eredmények kel, és alapértelmezés szerint "VAGY NEM" lesz értelmezve. Például `wifi -luxury` egyezik azokkal a `wifi` dokumentumokkal, amelyek vagy `luxury`tartalmazzák a kifejezést, vagy azokat, amelyek nem tartalmazzák a kifejezést. Ha a `all`, ez növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt, és alapértelmezés szerint - lesz értelmezve"ÉS NEM". Például, `wifi -luxury` egyezik a `wifi` dokumentumokat, amelyek tartalmazzák a kifejezést, és nem tartalmazzák a "luxus". Ez vitathatatlanul egy intuitív viselkedés `-` az üzemeltető. Ezért érdemes használni, `searchMode=all` ahelyett, `searchMode=any` hogy optimalizálni szeretné a keresést a pontosság ra `-` a visszahívás helyett, *és* a felhasználók gyakran használják az operátort a keresésekben.

<a name="prefix-search"></a>

## <a name="suffix--operator-for-prefix-search"></a>Utótag `*` operátoraz előtag kereséséhez

Az utótag operátora csillag. `*` Megkeresi `cap*` például azokat a dokumentumokat, `cap`amelyek kifejezése a következővel kezdődik, figyelmen kívül hagyva az esetet. 

A szűrőkhöz hasonlóan az előtag-lekérdezés is pontos egyezést keres. Mint ilyen, nincs relevancia pontozás (minden eredmény kap egy keresési pontszám 1.0). Az előtag-lekérdezések lassúak lehetnek, különösen akkor, ha az index nagy, és az előtag kis számú karakterből áll. 

Ha a karakterlánc utolsó részében egyező utótag-lekérdezést szeretne végrehajtani, használjon [helyettesítő karakteres keresést](query-lucene-syntax.md#bkmk_wildcard) és a teljes Lucene szintaxist.

## <a name="phrase-search-operator"></a>Kifejezéskereső operátor

A kifejezés operátor idézőjelek `" "`közé zár egy kifejezést. Például, `Roach Motel` míg (idézőjelek nélkül) keresne dokumentumokat tartalmazó `Roach` és / vagy `Motel` bárhol bármilyen sorrendben, `"Roach Motel"` (idézőjelrel) csak akkor egyezik dokumentumok, amelyek tartalmazzák, hogy a teljes mondat együtt, és ebben a sorrendben (szövegelemzés továbbra is érvényes).

## <a name="precedence-operator"></a>Elsőbbségoperátor

A prioritás operátor zárójelben zárja a karakterláncot. `( )` Például `motel+(wifi | luxury)` megkeresi a motel kifejezését tartalmazó `wifi` `luxury` dokumentumokat, és vagy (vagy mindkettőt).  

## <a name="escaping-search-operators"></a>Menekülő keresési operátorok  

 Annak érdekében, hogy a fenti szimbólumokat a keresési szöveg tényleges részeként használhassák, meg kell szabadulniuk egy fordított perjellel történő előrögzítéssel. Például `luxury\+hotel` a kifejezést `luxury+hotel`eredményezi. Annak érdekében, hogy a dolgok egyszerűek legyenek a tipikusesetekben, két kivétel van ez alól a szabály alól, ahol nincs szükség szökésre:  

- A NOT `-` operátort csak akkor kell megúszni, ha ez az első karakter a szóköz után, nem akkor, ha egy kifejezés közepén van. Például `wi-fi` egyetlen kifejezés; mivel a GUID-ok `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`(például) egyetlen tokenként vannak kezelve.
- Az utótag `*` operátorát csak akkor kell megkerülni, ha ez az utolsó karakter a szóköz előtt, nem akkor, ha egy kifejezés közepén van. Például `wi*fi` egyetlen jogkivonatként kezeli a kezelt.

> [!NOTE]  
>  Bár a menekülés a tokeneket együtt tartja, a szövegelemzés az elemzési módtól függően feloszthatja őket. A részleteket az Azure Cognitive Search REST&#41;nyelvi [támogatás&#40;&#41;.](index-add-language-analyzers.md)  

## <a name="see-also"></a>Lásd még  

+ [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene lekérdezés szintaxisa](query-lucene-syntax.md)
+ [OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md) 
