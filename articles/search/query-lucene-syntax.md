---
title: Lucene lekérdezési szintaxis
titleSuffix: Azure Cognitive Search
description: Hivatkozás a teljes Lucene lekérdezés szintaxisához, az Azure Cognitive Search helyettesítő karakteres, intelligens keresés, RegEx és más speciális lekérdezési szerkezetek használatával.
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
ms.openlocfilehash: 1392f69bea09996e46ad4c112474f9067ff5a63d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656914"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Lucene lekérdezés szintaxisa az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search lekérdezéseket a speciális [lekérdezési űrlapok gazdag Lucene Query Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) szintaxisa alapján írhat: helyettesítő karakter, intelligens keresés, közelségkeresés, reguláris kifejezések néhány példa. A Lucene Query Parser szintaxisának nagy része [sértetlenül van megvalósítva az Azure Cognitive Search szolgáltatásban,](search-lucene-query-architecture.md)kivéve az Azure Cognitive Search kifejezésein keresztül `$filter` készített *tartományalapú kereséseket.* 

> [!NOTE]
> A teljes Lucene szintaxis a [Keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/search-documents) API **keresési** paraméterében átadott lekérdezési kifejezésekhez használatos, nem tévesztendő össze az API $filter paraméteréhez használt [OData](search-filters.md) [szintaxissal.](query-odata-filter-orderby-syntax.md) Ezek a különböző szintaxisok saját szabályokat létrehozni lekérdezések, menekülési karakterláncok, és így tovább.

## <a name="how-to-invoke-full-parsing"></a>Hogyan lehet meghívni a teljes elemzés

Állítsa `queryType` be a keresési paramétert a használni kívánt elemző megadására. Az érvényes `simple|full`értékek `simple` közé tartozik `full` a ( alapértelmezett ) és a Lucene érték. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Példa a teljes szintaxis megjelenítésére

A következő példa a Lucene lekérdezés szintaxisával találja `queryType=full` meg a dokumentumokat az indexben, ami nyilvánvaló a paraméterben. Ez a lekérdezés olyan szállodákat ad vissza, ahol a kategóriamező a "költségvetés" kifejezést és a "nemrég felújított" kifejezést tartalmazó összes kereshető mezőt tartalmazza. A "nemrég felújított" kifejezést tartalmazó dokumentumok a "boost value" (3) kifejezés miatt magasabb rangúak.  

A `searchMode=all` paraméter ebben a példában releváns. Amikor operátorok vannak a lekérdezésben, `searchMode=all` általában be kell állítani, hogy az összes feltétel megfelel.In Operators are on the query, you should generally set to ensure that *all* of the criteria is matched.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Másik lehetőségként használja a POST-ot:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

További példákat lásd: [Lucene lekérdezés szintaxis példák az Azure Cognitive Search lekérdezések létrehozásához.](search-query-lucene-examples.md) A lekérdezési paraméterek teljes függőjének megadásáról a [Search Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)című témakörben olvashat részletesen.

> [!NOTE]  
>  Az Azure Cognitive Search támogatja [az egyszerű lekérdezésszintaxist](query-simple-syntax.md)is, amely egy egyszerű és robusztus lekérdezési nyelv, amely egyszerű kulcsszókereséshez használható.  

##  <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a>Szintaktikai alapok  
 A következő szintaxis alapjai a Lucene szintaxist használó összes lekérdezésre vonatkoznak.  

### <a name="operator-evaluation-in-context"></a>Az üzemeltető értékelése a kontextusban

Az elhelyezés határozza meg, hogy a szimbólumot operátorként vagy csak egy karakterlánc egy másik karaktereként értelmezi-e.

A Lucene teljes szintaxisában például a tilde (~) az intelligens kereséshez és a közelségkereséshez is használatos. Amikor után elhelyezett idézett kifejezés, ~ meghívja közelség keresés. Amikor helyezni a végén egy kifejezés, ~ meghívja fuzzy keresés.

Egy kifejezésen belül, például "business~analyst", a karakter nem értékeli kitágulatként. Ebben az esetben, feltételezve, hogy a lekérdezés egy kifejezés vagy kifejezés lekérdezés, [a teljes szöveges keresés](search-lucene-query-architecture.md) [lexikális elemzés](search-lucene-query-architecture.md#stage-2-lexical-analysis) csíkok ki a ~ és megtöri a "business~ elemző" két: üzleti vagy elemző.

A fenti példa a tilde (~), de ugyanez az elv vonatkozik minden operátor.

### <a name="escaping-special-characters"></a>Menekülés különleges karakterek

 A keresési szöveg részeként különleges karaktereket kell kikerülni. Akkor menekülni őket előtaggal őket\\backslash ( ). A megmenekülendő speciális karakterek a következők:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Ha például egy helyettesítő karakter \\ \*elől szeretne menekülni, használja a használatát.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása URL-címekben

 Győződjön meg arról, hogy minden nem biztonságos és fenntartott karakter url-címbe van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-ben lévő fragement/anchor azonosító. A karaktert `%23` az URL-címhez kell kódolni. A '&' és a '=' példák a fenntartott karakterekre, mivel elhatárolódnak a paraméterek, és értékeket adnak meg az Azure Cognitive Search-ben. További részletekért tekintse meg [az RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) című témakört.

 A nem ``" ` < > # % { } | \ ^ ~ [ ]``biztonságos karakterek . A fenntartott `; / ? : @ = + &`karakterek a következők.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Fontossági operátorok: csoportosítás és mezőcsoportosítás  
 Zárójelek segítségével allekérdezéseket hozhat létre, beleértve a parenthetical utasításon belüli operátorokat is. Például `motel+(wifi||luxury)` a "motel" kifejezést és a "wifi" vagy a "luxus" (vagy mindkettő) tartalmazó dokumentumokat keresi.

A mezőcsoportosítás hasonló, de a csoportosítást egyetlen mezőre ható. Például `hotelAmenities:(gym+(wifi||pool))` a "hotelAmenities" mezőben az "edzőterem" és a "wifi", illetve az "edzőterem" és a "medence" kifejezésre keres.  

### <a name="searchmode-parameter-considerations"></a>A SearchMode paraméterrel kapcsolatos szempontok  
 A lekérdezések `searchMode` hatása, az Azure Cognitive Search egyszerű [lekérdezésszintaxisában](query-simple-syntax.md)leírtak szerint, egyaránt vonatkozik a Lucene lekérdezés szintaxisára. Nevezetesen, `searchMode` együtt NEM operátorok eredményezhet lekérdezési eredmények, amelyek úgy tűnhet, szokatlan, ha nem egyértelmű, hogy milyen következményekkel jár, hogyan kell beállítani a paramétert. Ha megtartja az `searchMode=any`alapértelmezett , és használja a NOT operátor, a művelet számítja ki, mint egy vagy művelet, úgy, hogy "New York" NEM "Seattle" visszatér az összes város, amely nem Seattle.  

##  <a name="boolean-operators-and-or-not"></a><a name="bkmk_boolean"></a>Logikai operátorok (ÉS, VAGY, NEM) 
 Mindig adja meg a szöveges logikai operátorokat (ÉS, VAGY, NEM) csupa nagybetűvel.  

### <a name="or-operator-or-or-"></a>VAGY `OR` operátor vagy`||`

A VAGY operátor függőleges sáv vagy csőkarakter. Például: `wifi || luxury` olyan dokumentumokat keres, amelyek "wifi" vagy "luxus" vagy mindkettőt tartalmazzák. Mivel a VAGY az alapértelmezett kötőszóoperátor, azt `wifi luxury` is kihagyhatja, hogy az egyenértékű legyen a értékkel. `wifi || luxuery`

### <a name="and-operator-and--or-"></a>ÉS `AND`operátor, `&&` vagy`+`

Az AND operátor egy jel vagy egy plusz jel. Például: `wifi && luxury` a "wifi" és a "luxus" tartalmú dokumentumokat keresi. A plusz karakter (+) a kötelező kifejezésekhez használatos. Például `+wifi +luxury` előírja, hogy mindkét kifejezésnek meg kell jelennie valahol egy dokumentum területén.


### <a name="not-operator-not--or--"></a>NEM `NOT`operátor, `!` vagy`-`

A NOT operátor felkiáltójel vagy mínuszjel. Például: `wifi !luxury` olyan dokumentumokat keres, amelyek "wifi" kifejezéssel rendelkeznek és/vagy nincs "luxus". A `searchMode` beállítás azt határozza meg, hogy a NOT operátorral rendelkező kifejezés A + vagy || Üzemeltető. Visszahívás, `searchMode` amely beállítható `any`(alapértelmezett) `all`vagy .

A `searchMode=any` lekérdezések visszahívásának használata növeli a további eredmények belefoglalásával, és alapértelmezés szerint a program "VAGY NEM" értékként értelmezi a lekérdezéseket. Például, `wifi -luxury` egyezik a dokumentumokat, amelyek vagy tartalmazzák a *kifejezést wifi,* vagy azokat, amelyek nem tartalmazzák a *kifejezés luxus.*

A `searchMode=all` lekérdezések pontosságának növelése kevesebb eredmény alkalmazásával, alapértelmezés szerint pedig "ÉS NEM" néven lesz értelmezve. Például `wifi -luxury` megfelelnek a kifejezést `wifi` tartalmazó dokumentumoknak, `luxury`és nem tartalmazzák a kifejezést. Ez vitathatatlanul egy intuitív viselkedés a - üzemeltető. Ezért érdemes megfontolni `searchMode=all` `searchMode=any` a választást, ha a keresést a pontosságra szeretné optimalizálni a visszahívás helyett, *és* a felhasználók gyakran használják az operátort a `-` keresésekben.

##  <a name="query-size-limitations"></a><a name="bkmk_querysizelimits"></a>Lekérdezésméret-korlátozások  
 Az Azure Cognitive Search szolgáltatásba küldhető lekérdezések mérete korlátozva van. Pontosabban legbőlegelhető 1024 záradék (kifejezések egymástól és , VAGY és így tovább). A lekérdezésben megadott bármely kifejezés mérete körülbelül 32 KB-os korlátot jelent. Ha az alkalmazás programozott módon hoz létre keresési lekérdezéseket, azt javasoljuk, hogy úgy tervezze meg, hogy az ne hozzon létre nem kötött méretű lekérdezéseket.  

##  <a name="scoring-wildcard-and-regex-queries"></a><a name="bkmk_searchscoreforwildcardandregexqueries"></a>Helyettesítő karakteres és regex-lekérdezések pontozása
 Az Azure Cognitive Search gyakoriságalapú pontozást[(TF-IDF)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)használ szöveges lekérdezésekhez. Azonban a helyettesítő karakteres és regex lekérdezések, ahol a feltételek hatóköre potenciálisan széles, a gyakorisági tényező figyelmen kívül hagyja, hogy megakadályozzák a rangsor elfogultság felé mérkőzések ritkább kifejezések. A helyettesítő karakteres és regex-keresésekkel minden egyezést egyenlően kezelünk.

##  <a name="fielded-search"></a><a name="bkmk_fields"></a>Mezőalapú keresés  
A mezőalapú keresési műveletet `fieldName:searchExpression` a szintaxissal határozhatja meg, ahol a keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy zárójelben egy összetettebb kifejezés, tetszés szerint logikai operátorokkal. Néhány példa a következők:  

- műfaj:jazz NEM történelem  

- művészek:("Miles Davis" "John Coltrane")

Ügyeljen arra, hogy több karakterláncot helyezzen idézőjelek közé, ha mindkét karakterláncot egyetlen entitásként `artists` szeretné kiértékelni, ebben az esetben két különböző előadót keres a területen.  

A mezőben `fieldName:searchExpression` megadott mezőnek mezőnek `searchable` kell lennie.  Az [Index](https://docs.microsoft.com/rest/api/searchservice/create-index) létrehozása című témakörben részletesen ismerteti, hogyan használják az indexattribútumokat a meződefiníciókban.  

> [!NOTE]
> Mezőalapú keresési kifejezések használata esetén nem kell `searchFields` használnia a paramétert, mert minden mezőre írt keresési kifejezésnek kifejezetten meg van adva mezőneve. A `searchFields` paraméter azonban továbbra is használható, ha olyan lekérdezést szeretne futtatni, amelyben egyes részek hatóköre egy adott mezőre van hatázza, a többi pedig több mezőre is vonatkozhat. A `search=genre:jazz NOT history&searchFields=description` lekérdezés például `jazz` csak a `genre` mezőnek felel `NOT history` meg, `description` míg a mezőnek. A mező név `fieldName:searchExpression` mindig elsőbbséget élvez `searchFields` a paraméterrel szemben, ezért ebben a `genre` példában `searchFields` nem kell szerepelnünk a paraméterben.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a>Fuzzy keresés  
 A homályos keresés hasonló szerkezetű egyezéseket keres. Per [Lucene dokumentáció](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), fuzzy keresések alapján [Damerau-Levenshtein Távolság](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Az intelligens keresések legfeljebb 50 kifejezésre bővíthetik a kifejezést, amely megfelel a távolsági feltételeknek. 

 Fuzzy kereséshez használja a tilde "~" szimbólumot egyetlen szó végén egy választható paraméterrel, 0 és 2 közötti számmal (alapértelmezett), amely megadja a szerkesztési távolságot. A "kék~" vagy a "kék~1" például a "kék", a "blues" és a "glue" szót adja vissza.

 Az intelligens keresés csak kifejezésekre alkalmazható, kifejezésekre nem, de a tilde-t minden kifejezéshez külön-külön hozzáfűzheti egy többrészes névben vagy kifejezésben. Például a "Unviersty~ of~ "Wshington~" megegyezik a "University of Washington"-ban.
 

##  <a name="proximity-search"></a><a name="bkmk_proximity"></a>Közelségkeresés  
 A közelségi keresések segítségével olyan kifejezéseket kereshet, amelyek közel vannak egymáshoz a dokumentumban. Szúrjon be egy tilde "~" szimbólumot a kifejezés végére, amelyet a közelséghatárot létrehozó szavak száma követ. Például `"hotel airport"~5` a "hotel" és a "repülőtér" kifejezéseket 5 szóban találják meg egy dokumentumban.  


##  <a name="term-boosting"></a><a name="bkmk_termboost"></a>A kifejezés növelése  
 A kifejezéskiemelés azt jelenti, hogy a dokumentum magasabbra van rangsorolva, ha az tartalmazza a kiemelt kifejezést, a kifejezést nem tartalmazó dokumentumokhoz képest. Ez abban különbözik a pontozási profiloktól, hogy a pontozási profilok bizonyos mezőket emelnek ki, nem pedig konkrét kifejezéseket.  

A következő példa a különbségek szemléltetése. Tegyük fel, hogy van egy pontozási profil, amely növeli a mérkőzések egy bizonyos területen, mondjuk *műfaj* a [musicstoreindex példában](index-add-scoring-profiles.md#bkmk_ex). A kifejezéskiemelés sel tovább növelheti bizonyos keresési kifejezéseket, amelyek magasabbak, mint mások. Például, `rock^2 electronic` majd kinöveli a dokumentumokat, amelyek tartalmazzák a keresési kifejezéseket a műfaj mező magasabb, mint más kereshető mezők az indexben. Továbbá a keresési kifejezést tartalmazó *dokumentumok* rangsorolása magasabb lesz, mint a többi *elektronikus* keresési kifejezés a kifejezés kiemelési értékének (2) köszönhetően.  

 Egy kifejezés kiemeléséhez használja a "^" szimbólumot, amelynek végén a keresett kifejezés végén egy kiemelési tényező (szám) található. A kifejezéseket is kiemelheti. Minél magasabb a növekedési tényező, annál relevánsabb lesz a kifejezés a többi keresési kifejezéshez képest. Alapértelmezés szerint a kiemelési tényező 1. Bár a lökéstényezőnek pozitívnak kell lennie, 1-nél kisebb is lehet (például 0,20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a>Reguláris kifejezés keresése  
 A reguláris kifejezéskeresés a [RegExp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált perjelek "/" tartalma alapján egyezést talál.  

 Ha például a "motel" vagy "hotel" `/[mh]otel/`tartalmazó dokumentumokat szeretné megkeresni, adja meg a értéket. A reguláris kifejezéskeresések egyetlen szavakkal vannak egyezve.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a>Helyettesítő karakteres keresés  
 Az általánosan elismert szintaxist több (*) vagy egy (?) karakteres helyettesítő karakteres kereséshez is használhatja. Megjegyzés: A Lucene lekérdezéselemző támogatja ezeknek a szimbólumoknak a használatát egyetlen kifejezéssel, nem pedig kifejezéssel.

Az előtag-keresés a csillag`*`( ) karaktert is használja. Például egy lekérdezési `search=note*` kifejezés a "notebook" vagy a "jegyzettömb" értéket adja vissza. Az előtagkereséshez nem szükséges teljes Lucene szintaxis. Az egyszerű szintaxis támogatja ezt a forgatókönyvet.

Az utótagkereséshez `*` `?` , ahol vagy megelőzi a karakterláncot, teljes Lucene szintaxist és reguláris kifejezést igényel (nem használhat * vagy ? szimbólumot a keresés első karaktereként). Az "alfanumerikus" kifejezés miatt a`search=/.*numeric.*/`( ) lekérdezési kifejezés megtalálja az egyezést.

> [!NOTE]  
> A lekérdezéselemzés során az előtagként, utótagként, helyettesítő ként vagy reguláris kifejezésként megfogalmazott lekérdezések a rendszer ugyanúgy kerülnek átadásra a lekérdezési fának, megkerülve a [lexikai elemzést.](search-lucene-query-architecture.md#stage-2-lexical-analysis) Az egyezések csak akkor találhatók meg, ha az index a lekérdezés által megadott formátumban tartalmazza a karakterláncokat. A legtöbb esetben szüksége lesz egy alternatív elemző az indexelés során, amely megőrzi a karakterlánc integritását, hogy a részleges kifejezés és a minta egyeztetése sikeres legyen. További információ: [Részleges kifejezésű keresés az Azure Cognitive Search lekérdezésekben.](search-query-partial-matching.md)

## <a name="see-also"></a>Lásd még  

+ [Dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [OData kifejezés szintaxisa szűrőkhöz és rendezéshez](query-odata-filter-orderby-syntax.md)   
+ [Egyszerű lekérdezésszintaxis az Azure Cognitive Search szolgáltatásban](query-simple-syntax.md)   
