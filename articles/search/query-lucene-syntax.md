---
title: Lucene lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: A teljes Lucene lekérdezési szintaxisra vonatkozó hivatkozás, amelyet az Azure Cognitive Search használhat helyettesítő karakterek, fuzzy keresés, RegEx és egyéb speciális lekérdezési szerkezetek esetében.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f4c3330b23b8b724cdbf5d7e09eec8a8dd5b8cfa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81258983"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Lucene lekérdezési szintaxis az Azure Cognitive Search

Lekérdezéseket írhat az Azure Cognitive Search a speciális lekérdezési űrlapok Rich [Lucene lekérdezési elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) szintaxisa alapján: helyettesítő karakter, fuzzy keresés, közelségi keresés, reguláris kifejezések néhány példa. A Lucene lekérdezés-elemző szintaxisának nagy része [érintetlenül van megvalósítva az azure Cognitive Searchban](search-lucene-query-architecture.md), az Azure-ban Cognitive Search kifejezéseken keresztül `$filter` létrehozott *tartomány-keresések* kivételével. 

> [!NOTE]
> A teljes Lucene szintaxis a [keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents) API **keresési** paramétere által átadott lekérdezési kifejezésekhez használatos, és nem tévesztendő össze az API [$Filter](search-filters.md) paraméteréhez használt [OData-szintaxissal](query-odata-filter-orderby-syntax.md) . Ezek a különböző szintaxisok magukban foglalják a lekérdezések összeállításához, a karakterláncok kiszökéséhez és így tovább.

## <a name="invoke-full-parsing"></a>Teljes elemzés meghívása

Állítsa be `queryType` a keresési paramétert a használni kívánt elemző megadásához. Az érvényes értékek `simple|full`a következők `simple` :, az alapértelmezett és `full` a Lucene esetében. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Teljes szintaxist ábrázoló példa

A következő példa megkeresi az indexben lévő dokumentumokat a Lucene lekérdezési szintaxissal `queryType=full` , amely nyilvánvaló a paraméterben. Ez a lekérdezés azokat a szálláshelyeket adja vissza, ahol a Kategória mező tartalmazza a "költségvetés" kifejezést, valamint az "újonnan felújított" kifejezést tartalmazó kereshető mezőket. A "legutóbb felújított" kifejezést tartalmazó dokumentumok magasabbra vannak rangsorolva a "Boost Value" (3) kifejezés eredményeképpen.  

A `searchMode=all` paraméter releváns ebben a példában. Ha a kezelők a lekérdezésben vannak, általában úgy `searchMode=all` kell beállítani, hogy az *összes* feltételt egyeztetni lehessen.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternatív megoldásként használja a POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

További Példákért lásd: [Lucene-lekérdezési szintaxis példák az Azure Cognitive Search lekérdezések létrehozásához](search-query-lucene-examples.md). A lekérdezési paraméterek teljes feltételének megadásával kapcsolatos részletekért lásd: [dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Az Azure Cognitive Search az [egyszerű lekérdezési szintaxist](query-simple-syntax.md)is támogatja, amely egyszerű és robusztus lekérdezési nyelvet biztosít, amely egyszerű kulcsszavas kereséshez használható.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Szintaxis alapjai  

a következő szintaxis a Lucene szintaxist használó összes lekérdezésre vonatkozik.  

### <a name="operator-evaluation-in-context"></a>Operátor kiértékelése kontextusban

Az elhelyezés meghatározza, hogy egy szimbólum operátorként vagy csak egy sztringben legyen értelmezve.

A Lucene teljes szintaxisa például a tilde (~) a fuzzy kereséshez és a közelségi kereséshez használatos. Idézett kifejezés után a ~ meghívja a közelségi keresést. A kifejezés végén a ~ meghívja a zavaros keresést.

Egy kifejezésen belül, például "Business ~ Analysis", a karakter nem lesz kiértékelve operátorként. Ebben az esetben, ha a lekérdezés egy kifejezés vagy kifejezés lekérdezése, a [teljes szöveges keresés](search-lucene-query-architecture.md) a [lexikális analízissel](search-lucene-query-architecture.md#stage-2-lexical-analysis) a ~ és a "Business ~ Analysis" kifejezést a következő két: üzleti vagy elemző.

A fenti példa a tilde (~), de ugyanez az elv minden operátorra érvényes.

### <a name="escaping-special-characters"></a>Speciális karakterek megmenekülése

Ha a keresési operátorok valamelyikét a keresett szöveg részeként szeretné használni, akkor a karaktert egyetlen fordított perjel (`\`) előtaggal kell elmenekülnie. Például a helyettesítő karakteres kereséshez `https://`, ahol `://` a a lekérdezési karakterlánc részét képezi, a következőt kell megadnia `search=https\:\/\/*`:. Hasonlóképpen, egy Escape-telefonszám mintázata is így `\+1 \(800\) 642\-7676`néz ki.

A Escape-t igénylő speciális karakterek közé a következők tartoznak:  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> Bár a Escapes megtartja a tokeneket, az indexelés során felmerülő [lexikális elemzések](search-lucene-query-architecture.md#stage-2-lexical-analysis) is felmerülhetnek. A standard Lucene Analyzer például megszakítja a szavakat a kötőjelek, a szóközök és más karakterek esetén. Ha speciális karaktereket szeretne megadni a lekérdezési karakterláncban, előfordulhat, hogy olyan elemzőre van szüksége, amely megőrzi őket az indexben. Bizonyos lehetőségek közé tartoznak a Microsoft természetes [nyelvi elemzők](index-add-language-analyzers.md), amelyek megőrzik az elválasztott szavakat, vagy egy egyéni elemzőt összetettebb mintákhoz. További információ: [részleges feltételek, mintázatok és speciális karakterek](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása az URL-címekben

Győződjön meg arról, hogy az összes nem biztonságos és fenntartott karakter egy URL-címben van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-címben szereplő töredék/horgony azonosító. A karaktert kódolni kell, `%23` ha az URL-címben van használatban. A (z) "&" és a "=" például fenntartott karakterek, amelyek a paramétereket korlátozzák, és értékeket határoznak meg az Azure Cognitive Searchban. További részletekért tekintse meg a [RFC1738: egységes erőforrás-lokátorok (URL)](https://www.ietf.org/rfc/rfc1738.txt) című témakört.

A nem biztonságos karakterek ``" ` < > # % { } | \ ^ ~ [ ]``a következők:. A fenntartott `; / ? : @ = + &`karakterek a következők:.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Lekérdezési méret korlátai

 Korlátozva van az Azure Cognitive Searchba küldendő lekérdezések mérete. Pontosabban megadhatja a legfeljebb 1024 záradékot (a kifejezéseket és a, vagy a, és így tovább). A lekérdezésben szereplő egyes kifejezések méretének körülbelül 32 KB-os korlátja is van. Ha az alkalmazás programozott módon hozza létre a keresési lekérdezéseket, javasoljuk, hogy úgy tervezze meg úgy, hogy ne generáljon nem kötött méretű lekérdezéseket.  

### <a name="precedence-operators-grouping"></a>Elsőbbségi operátorok (csoportosítás)

 A zárójelek használatával allekérdezéseket hozhat létre, beleértve az zárójeles utasításban található operátorokat is. Például `motel+(wifi||luxury)` megkeresi a "Motel" kifejezést és a "WiFi" vagy a "luxus" (vagy mindkettő) tartalmú dokumentumokat.

A mező csoportosítása hasonló, de a csoportosítás egyetlen mezőre vonatkozik. Például `hotelAmenities:(gym+(wifi||pool))` megkeresi a "hotelAmenities" mezőt a "tornaterem" és a "WiFi", a "tornaterem" és a "pool" mezőnél.  

##  <a name="boolean-search"></a><a name="bkmk_boolean"></a>Logikai keresés

 Mindig a logikai operátorokat (és, vagy nem) az összes nagybetű határozza meg.  

### <a name="or-operator-or-or-"></a>VAGY operátor `OR` vagy`||`

A vagy operátor egy függőleges sáv vagy cső karakter. Például: `wifi || luxury` megkeresi a "WiFi" vagy a "luxus" vagy mindkettőt tartalmazó dokumentumokat. Mivel a vagy az az alapértelmezett, és ez az operátor, azt is megteheti, `wifi luxury` hogy a megfelelő `wifi || luxury`.

### <a name="and-operator-and--or-"></a>És operátor `AND`, `&&` vagy`+`

Az és az operátor egy jel vagy egy pluszjel. Például: `wifi && luxury` a "WiFi" és a "luxus" tartalmú dokumentumokat fogja keresni. A plusz karaktert (+) a kötelező feltételekhez használja a rendszer. Például azt határozza `+wifi +luxury` meg, hogy mindkét kifejezésnek valahol az egyetlen dokumentum mezőjében kell megjelennie.

### <a name="not-operator-not--or--"></a>Nem operátor `NOT`, `!` vagy`-`

A NOT operátor egy mínusz jel. Például `wifi –luxury` megkeresi azokat a dokumentumokat, amelyek rendelkeznek `wifi` a kifejezéssel és/vagy nem `luxury`.

A lekérdezési kérelem **searchMode** paramétere azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel (feltéve, hogy `+` a `|` többi feltétel nem vagy operátor.) Az érvényes értékek `any` a `all`következők: vagy.

`searchMode=any`a további eredményekkel együtt növeli a lekérdezések visszahívását, és `-` alapértelmezés szerint "vagy nem" lesz értelmezve. Például `wifi -luxury` megegyeznek azok a dokumentumok, amelyek tartalmazzák a kifejezést `wifi` , vagy amelyek nem tartalmazzák a kifejezést `luxury`.

`searchMode=all`növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt ad meg, és alapértelmezés szerint "és nem" lesz értelmezve. Például a a `wifi -luxury` kifejezést `wifi` tartalmazó dokumentumokat fogja egyeztetni, és nem tartalmazza a "luxus" kifejezést. Ez vitathatatlanul intuitív viselkedést biztosít a `-` kezelő számára. Ezért érdemes megfontolni, hogy `searchMode=all` ahelyett `searchMode=any` , hogy visszahívást szeretne használni a pontossághoz, *és* a felhasználók gyakran használják a keresésekben `-` a kezelőt.

**SearchMode** -beállítás meghatározásakor vegye figyelembe a felhasználói interakciós mintákat a különböző alkalmazásokban található lekérdezésekhez. Azok a felhasználók, akik információt keresnek, nagyobb valószínűséggel tartalmaznak egy operátort egy lekérdezésben, szemben a több beépített navigációs szerkezettel rendelkező e-kereskedelmi webhelyekkel.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Mező szerinti keresés

Megadhat egy mezőhöz tartozó keresési műveletet a `fieldName:searchExpression` szintaxissal, ahol a keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy egy összetettebb kifejezés zárójelben, opcionálisan logikai operátorokkal. Néhány példa a következőkre:  

- Műfaj: nem a jazz története  

- művészek:("Miles Davis" "John Coltrane")

Ügyeljen arra, hogy az idézőjelek között több karakterláncot is helyezzen el, ha azt szeretné, hogy mindkét sztring egyetlen entitásként legyen kiértékelve, ebben az esetben `artists` a mezőben két különböző előadó keres.  

A mezőben megadott `fieldName:searchExpression` mezőnek `searchable` mezőnek kell lennie.  További részletekért tekintse meg az index [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) című témakört, amely azt ismerteti, hogyan használhatók az index attribútumai.  

> [!NOTE]
> A mezőn alapuló keresési kifejezések használatakor nem kell használnia a `searchFields` paramétert, mert az egyes mezőkhöz tartozó keresési kifejezésekhez explicit módon van megadva a mezőnév. Azonban továbbra is használhatja a `searchFields` paramétert, ha olyan lekérdezést szeretne futtatni, amelyben bizonyos részek hatóköre egy adott mezőre vonatkozik, a REST pedig több mezőre is vonatkozhat. A `search=genre:jazz NOT history&searchFields=description` lekérdezés például csak a `jazz` `genre` mezőnek felel meg, míg a `NOT history` `description` mezőnek egyeznie kell. A `fieldName:searchExpression` mezőben megadott mezőnév elsőbbséget élvez `searchFields` a paraméterrel szemben, ezért ebben a példában nem kell belefoglalni `genre` a `searchFields` paraméterbe.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Fuzzy keresés

A nehezen megtalált találatok olyan kifejezések, amelyek hasonló szerkezettel rendelkeznek, és legfeljebb legfeljebb 50 olyan kifejezést mutatnak be, amelyek megfelelnek a két vagy annál kisebb távolsági feltételeknek. További információ: [fuzzy keresés](search-query-fuzzy.md).

 A fuzzy kereséshez használja a "~" szimbólumot egyetlen szó végén egy opcionális paraméterrel, egy 0 és 2 közötti számot (alapértelmezett), amely megadja a szerkesztési távolságot. Például a "Blue ~" vagy a "Blue ~ 1" a "Blue", a "blues" és a "ragasztó" értéket fogja visszaadni.

 A zavaros keresés csak a kifejezésekre alkalmazható, a kifejezésekre nem, de a tilde az egyes kifejezésekhez a több részből álló névvel vagy kifejezéssel is kiegészíthető. Például: "Unviersty ~ of ~" Wshington ~ "megegyeznek a" Washington Egyetem "kifejezéssel.
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Közelség keresése

A közelségi keresések megkeresik a dokumentumokban egymáshoz közeli kifejezéseket. Szúrjon be egy "~" szimbólumot egy kifejezés végén, majd a közelségi határt létrehozó szavak számát. Például `"hotel airport"~5` megkeresi a "Hotel" és a "repülőtér" kifejezést a dokumentumban lévő 5 szóból.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>A kifejezés fokozása

A kifejezés növelése arra utal, hogy a dokumentum rangsorolása magasabb, ha a megnövelt kifejezést tartalmazza, a kifejezést nem tartalmazó dokumentumokhoz képest. Ez különbözik az adott pontozási profilok pontozási profiljaitól, és nem adott feltételekkel növeli bizonyos mezőket.  

A következő példa segít illusztrálni a különbségeket. Tegyük fel, hogy van egy pontozási profil, amely egy adott mezőben növeli a egyezéseket, például a *műfajt* a [musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). A kifejezés fokozása felhasználható a másoknál nagyobb keresési kifejezések további növelésére. Például a megjelenő dokumentumokban a keresési kifejezéseket tartalmazó dokumentumok magasabbak `rock^2 electronic` lesznek, mint az index más kereshető mezőinél. A *rock* keresési kifejezést tartalmazó dokumentumok továbbra is magasabbak lesznek *, mint a más keresési kifejezés,* amely a kifejezés értékének (2) kiemelését eredményezi.  

 Egy kifejezés növeléséhez használja a "^" karaktert, a szimbólumot a keresett kifejezés végén (a szám). A kifejezéseket növelheti is. Minél nagyobb a növelési tényező, annál nagyobb a kifejezés, mint a többi keresési kifejezéshez képest. Alapértelmezés szerint a Boost faktor 1. Bár a Boost faktornak pozitívnak kell lennie, kevesebb, mint 1 (például 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Reguláris kifejezés keresése  
 A reguláris kifejezéses keresés a "/" perjelek közötti, a [regexp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált tartalom alapján keres egyezést.  

 Ha például a "Motel" vagy a "Hotel" tartalmú dokumentumokat szeretné megkeresni, akkor a következőt kell megadnia `/[mh]otel/`:. A reguláris kifejezésekkel végzett keresések egyetlen szóval vannak összehasonlítva.

Egyes eszközök és nyelvek további Escape-jellegű követelményeket támasztanak. A JSON esetében a perjeleket tartalmazó karakterláncok egy visszafelé perjelet tartalmaznak: "microsoft.com/azure/", `search=/.*microsoft.com\/azure\/.*/` ahol `search=/.* <string-placeholder>.*/` beállítja a reguláris kifejezést, és a sztring Escape `microsoft.com\/azure\/` -perjelet tartalmaz.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Helyettesítő karakteres keresés  

Általánosan felismert szintaxist használhat több (*) vagy egy (?) karakteres helyettesítő karakteres kereséshez. Vegye figyelembe, hogy a Lucene-lekérdezés elemzője egyetlen kifejezéssel támogatja a szimbólumok használatát, nem pedig egy kifejezést.

Az előtag-keresés a csillag (`*`) karaktert is használja. A lekérdezés kifejezése például a " `search=note*` jegyzetfüzet" vagy a "Jegyzettömb" értéket adja vissza. Az előtag-kereséshez nem szükséges a teljes Lucene szintaxis. Az egyszerű szintaxis támogatja ezt a forgatókönyvet.

Az utótagok `*` keresésekor `?` a karakterláncnak meg kell adni a teljes Lucene szintaxist és egy reguláris kifejezést (nem használhat * vagy? a szimbólum a keresés első karaktere. Az "alfanumerikus" kifejezés miatt a (z) (`search=/.*numeric.*/`) lekérdezési kifejezése az egyezést fogja megtalálni.

> [!NOTE]  
> A lekérdezések elemzése során előtagként, utótagként, helyettesítő karakterként vagy reguláris kifejezésként megadott lekérdezések a lekérdezési fában lesznek átadva – a [lexikális analízis](search-lucene-query-architecture.md#stage-2-lexical-analysis)megkerülésével. A egyezések csak akkor találhatók meg, ha az index a lekérdezés formátumában megadott karakterláncokat tartalmazza. A legtöbb esetben szüksége lesz egy alternatív elemzőre az indexelés során, amely megőrzi a karakterlánc-integritást, hogy a részleges és a mintázat egyeztetése sikeres legyen. További információ: [részleges kifejezéses keresés az Azure Cognitive Search lekérdezésekben](search-query-partial-matching.md).

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Helyettesítő karakterek és regex-lekérdezések

Az Azure Cognitive Search a frekvencia-alapú pontozást ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) használja szöveges lekérdezésekhez. A helyettesítő karakterek és a regex-lekérdezések esetében azonban a feltételek hatóköre esetleg széles lehet, a rendszer figyelmen kívül hagyja a gyakorisági tényezőt, hogy megakadályozza a rangsorolást a ritkább kifejezésektől való eltérések irányába. A rendszer minden egyezést egyenlően kezel a helyettesítő karakterek és a regex-keresések esetében.

## <a name="see-also"></a>Lásd még

+ [Példák az egyszerű keresésre](search-query-simple-examples.md)
+ [Példák a teljes Lucene keresésre](search-query-lucene-examples.md)
+ [Dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [A szűrők és a rendezés OData kifejezésének szintaxisa](query-odata-filter-orderby-syntax.md)   
+ [Egyszerű lekérdezési szintaxis az Azure-ban Cognitive Search](query-simple-syntax.md)   
