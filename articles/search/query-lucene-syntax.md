---
title: Lucene lekérdezési szintaxis – Azure Search
description: A teljes Lucene szintaxisra vonatkozó hivatkozás, amelyet a Azure Search használ.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: e8e9b737676b2695b7b88430f59b0b0e79bc477a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883865"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Lucene lekérdezési szintaxisa Azure Search
A lekérdezéseket a részletes [Lucene lekérdezés-elemző](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) szintaxisa alapján is írhat Azure Search a speciális lekérdezési űrlapokhoz: helyettesítő karakter, fuzzy keresés, közelségi keresés, reguláris kifejezések pedig néhány példa. A Lucene lekérdezés-elemző szintaxisának nagy része [érintetlenül](search-lucene-query-architecture.md)van megvalósítva Azure Searchban, kivéve a tartományon belüli, Azure Search a kifejezéseken `$filter` keresztül létrehozott *kereséseket* . 

## <a name="how-to-invoke-full-parsing"></a>A teljes elemzés meghívása

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

További Példákért lásd a [Lucene lekérdezési szintaxisának példáit a Azure Search lekérdezések létrehozásához](search-query-lucene-examples.md). A lekérdezési paraméterek teljes feltételének megadásával kapcsolatos részletekért lásd: [dokumentumok &#40;keresése&#41;Azure Search szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  A Azure Search az [egyszerű lekérdezési szintaxist](query-simple-syntax.md)is támogatja, amely egyszerű és robusztus lekérdezési nyelvet biztosít, amely egyszerű kulcsszavas kereséshez használható.  

##  <a name="bkmk_syntax"></a>Szintaxis alapjai  
 A következő szintaxis a Lucene szintaxist használó összes lekérdezésre vonatkozik.  

### <a name="operator-evaluation-in-context"></a>Operátor kiértékelése kontextusban

Az elhelyezés meghatározza, hogy egy szimbólum operátorként vagy csak egy sztringben legyen értelmezve.

A Lucene teljes szintaxisa például a tilde (~) a fuzzy kereséshez és a közelségi kereséshez használatos. Idézett kifejezés után a ~ meghívja a közelségi keresést. A kifejezés végén a ~ meghívja a zavaros keresést.

Egy kifejezésen belül, például "Business ~ Analysis", a karakter nem lesz kiértékelve operátorként. Ebben az esetben, ha a lekérdezés egy kifejezés vagy kifejezés lekérdezése, a [teljes szöveges keresés](search-lucene-query-architecture.md) a [lexikális analízissel](search-lucene-query-architecture.md#stage-2-lexical-analysis) a ~ és a "Business ~ Analysis" kifejezést a következő két: üzleti vagy elemző.

A fenti példa a tilde (~), de ugyanez az elv minden operátorra érvényes.

### <a name="escaping-special-characters"></a>Speciális karakterek megmenekülése

 A keresett szöveg részeként a speciális karaktereket el kell menekülni. Elkerülheti őket a fordított perjel (\\) előtaggal. A kihagyni kívánt speciális karakterek közé tartoznak a következők:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Ha például egy helyettesítő karaktert szeretne elmenekülni, használja \\ \*a következőt:.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Nem biztonságos és fenntartott karakterek kódolása az URL-címekben

 Győződjön meg arról, hogy az összes nem biztonságos és fenntartott karakter egy URL-címben van kódolva. Például a "#" egy nem biztonságos karakter, mert egy URL-címben a fragement/Anchor azonosító. A karaktert kódolni kell, `%23` ha az URL-címben van használatban. a (z) "&" és a "=" például fenntartott karakterek, amelyek a paramétereket korlátozzák, és értékeket határoznak meg a Azure Searchban. Tekintse [meg a RFC1738: Az egységes erőforrás-lokátorok (](https://www.ietf.org/rfc/rfc1738.txt) URL-cím) a további részletekért.

 A nem biztonságos karakterek ``" ` < > # % { } | \ ^ ~ [ ]``a következők:. A fenntartott `; / ? : @ = + &`karakterek a következők:.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Elsőbbségi operátorok: csoportosítás és mező csoportosítása  
 A zárójelek használatával allekérdezéseket hozhat létre, beleértve az zárójeles utasításban található operátorokat is. Például `motel+(wifi||luxury)` megkeresi a "Motel" kifejezést és a "WiFi" vagy a "luxus" (vagy mindkettő) tartalmú dokumentumokat.

A mező csoportosítása hasonló, de a csoportosítás egyetlen mezőre vonatkozik. Például `hotelAmenities:(gym+(wifi||pool))` megkeresi a "hotelAmenities" mezőt a "tornaterem" és a "WiFi", a "tornaterem" és a "pool" mezőnél.  

### <a name="searchmode-parameter-considerations"></a>SearchMode-paraméterek szempontjai  
 A lekérdezésekre `searchMode` gyakorolt hatás a [Azure Search egyszerű lekérdezési szintaxisa](query-simple-syntax.md)című részben leírtak szerint, egyformán vonatkozik a Lucene lekérdezési szintaxisára. A nem `searchMode` operátorokkal együtt azonban olyan lekérdezési eredményeket eredményezhet, amelyek szokatlannak tűnhet, ha nem törli a paraméter beállításának következményeit. Ha megtartja az alapértelmezett `searchMode=any`értéket, és nem operátort használ, a műveletet egy vagy műveletként számítja ki a rendszer, így a "New York" not "Seattle" nem minden olyan várost ad vissza, amely nem Seattle.  

##  <a name="bkmk_boolean"></a>Logikai operátorok (és, vagy nem) 
 Mindig a logikai operátorokat (és, vagy nem) az összes nagybetű határozza meg.  

### <a name="or-operator-or-or-"></a>Vagy operátor `OR` vagy`||`

A vagy operátor egy függőleges sáv vagy cső karakter. Például: `wifi || luxury` megkeresi a "WiFi" vagy a "luxus" vagy mindkettőt tartalmazó dokumentumokat. Mivel a vagy az az alapértelmezett, és ez az operátor, azt is megteheti, `wifi luxury` hogy a `wifi || luxuery`megfelelő.

### <a name="and-operator-and--or-"></a>És operátor `AND`, `&&` vagy`+`

Az és az operátor egy jel vagy egy pluszjel. Például: `wifi && luxury` a "WiFi" és a "luxus" tartalmú dokumentumokat fogja keresni. A plusz karaktert (+) a kötelező feltételekhez használja a rendszer. Például azt határozza `+wifi +luxury` meg, hogy mindkét kifejezésnek valahol az egyetlen dokumentum mezőjében kell megjelennie.


### <a name="not-operator-not--or--"></a>Nem operátor `NOT`, `!` vagy`-`

A NOT operátor egy felkiáltójel vagy mínusz jel. Például: `wifi !luxury` megkeresi a "WiFi" kifejezést és/vagy nem "luxus" nevű dokumentumokat. A `searchMode` beállítás azt szabályozza, hogy a nem operátorral rendelkező kifejezés ANDed vagy ORed-e a lekérdezés más feltételeivel egy + vagy | | operátor hiányában. Visszahívás, `searchMode` amely beállítható `any`a következőre: vagy ( `all`alapértelmezett) vagy.

A `searchMode=any` használata növeli a lekérdezések visszahívását azáltal, hogy több eredményt is felhasznál, és alapértelmezés szerint a "vagy nem" értéket fogja értelmezni. Például `wifi -luxury` a *Wi-Fi* vagy a nem a luxus kifejezést tartalmazó dokumentumokat fogja egyeztetni *.*

A `searchMode=all` használata növeli a lekérdezések pontosságát azáltal, hogy kevesebb eredményt ad meg, és alapértelmezés szerint a "és nem" értéket fogja értelmezni. Például a a `wifi -luxury` kifejezést `wifi` tartalmazó dokumentumokat fogja egyeztetni, és nem tartalmazza a kifejezést `luxury`. Ez vitathatatlanul intuitív viselkedés a-operátor számára. Ezért érdemes megfontolnia a `searchMode=all` `searchMode=any` választást, ha optimalizálni szeretné a pontosságot, *és* a felhasználók gyakran használják az `-` operátort a keresésekben.

##  <a name="bkmk_querysizelimits"></a>A lekérdezés méretére vonatkozó korlátozások  
 A Azure Search elküldhető lekérdezések mérete korlátozott. Pontosabban megadhatja a legfeljebb 1024 záradékot (a kifejezéseket és a, vagy a, és így tovább). A lekérdezésben szereplő egyes kifejezések méretének körülbelül 32 KB-os korlátja is van. Ha az alkalmazás programozott módon hozza létre a keresési lekérdezéseket, javasoljuk, hogy úgy tervezze meg úgy, hogy ne generáljon nem kötött méretű lekérdezéseket.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a>Helyettesítő karakterek és regex-lekérdezések
 A Azure Search gyakoriság-alapú pontozást ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) használ szöveges lekérdezésekhez. A helyettesítő karakterek és a regex-lekérdezések esetében azonban a feltételek hatóköre esetleg széles lehet, a rendszer figyelmen kívül hagyja a gyakorisági tényezőt, hogy megakadályozza a rangsorolást a ritkább kifejezésektől való eltérések irányába. A rendszer minden egyezést egyenlően kezel a helyettesítő karakterek és a regex-keresések esetében.

##  <a name="bkmk_fields"></a>Mező szerinti keresés  
Megadhat egy mezőhöz tartozó keresési műveletet a `fieldName:searchExpression` szintaxissal, ahol a keresési kifejezés lehet egyetlen szó vagy kifejezés, vagy egy összetettebb kifejezés zárójelben, opcionálisan logikai operátorokkal. Néhány példa a következőkre:  

- Műfaj: nem a jazz története  

- művészek:("Miles Davis" "John Coltrane")

Ügyeljen arra, hogy az idézőjelek között több karakterláncot is helyezzen el, ha azt szeretné, hogy mindkét sztring egyetlen entitásként legyen kiértékelve, ebben az esetben `artists` a mezőben két különböző előadó keres.  

A mezőben megadott `fieldName:searchExpression` mezőnek `searchable` mezőnek kell lennie.  További részletekért tekintse meg az index [létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index) című témakört, amely azt ismerteti, hogyan használhatók az index attribútumai.  

> [!NOTE]
> A mezőn alapuló keresési kifejezések használatakor nem kell használnia a `searchFields` paramétert, mert az egyes mezőkhöz tartozó keresési kifejezésekhez explicit módon van megadva a mezőnév. Azonban továbbra is használhatja a `searchFields` paramétert, ha olyan lekérdezést szeretne futtatni, amelyben bizonyos részek hatóköre egy adott mezőre vonatkozik, a REST pedig több mezőre is vonatkozhat. A `search=genre:jazz NOT history&searchFields=description` lekérdezés például csak a `genre` mezőnek `jazz` felel meg, míg a `description` mezőnek egyeznie `NOT history` kell. A mezőben megadott `fieldName:searchExpression` mezőnév elsőbbséget élvez a `searchFields` paraméterrel szemben, ezért ebben a példában nem kell belefoglalni `genre` a `searchFields` paraméterbe.

##  <a name="bkmk_fuzzy"></a>Fuzzy keresés  
 A nehezen megtalált találatok a hasonló szerkezettel rendelkező kifejezésekben szerepelnek. A [Lucene dokumentációja](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)alapján a fuzzy keresések a [Damerau-Levenshtein távolságon](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)alapulnak. A fuzzy keresések kiterjeszthetők a távolsági feltételeknek megfelelő maximális 50 kifejezésre. 

 A fuzzy kereséshez használja a "~" szimbólumot egyetlen szó végén egy opcionális paraméterrel, egy 0 és 2 közötti számot (alapértelmezett), amely megadja a szerkesztési távolságot. Például a "Blue ~" vagy a "Blue ~ 1" a "Blue", a "blues" és a "ragasztó" értéket fogja visszaadni.

 A zavaros keresés csak a kifejezésekre alkalmazható, a kifejezésekre nem, de a tilde az egyes kifejezésekhez a több részből álló névvel vagy kifejezéssel is kiegészíthető. Például: "Unviersty ~ of ~" Wshington ~ "megegyeznek a" Washington Egyetem "kifejezéssel.
 

##  <a name="bkmk_proximity"></a>Közelség keresése  
 A közelségi keresések megkeresik a dokumentumokban egymáshoz közeli kifejezéseket. Szúrjon be egy "~" szimbólumot egy kifejezés végén, majd a közelségi határt létrehozó szavak számát. Például `"hotel airport"~5` megkeresi a "Hotel" és a "repülőtér" kifejezést a dokumentumban lévő 5 szóból.  


##  <a name="bkmk_termboost"></a>A kifejezés fokozása  
 A kifejezés növelése arra utal, hogy a dokumentum rangsorolása magasabb, ha a megnövelt kifejezést tartalmazza, a kifejezést nem tartalmazó dokumentumokhoz képest. Ez különbözik az adott pontozási profilok pontozási profiljaitól, és nem adott feltételekkel növeli bizonyos mezőket.  

A következő példa segít illusztrálni a különbségeket. Tegyük fel, hogy van egy pontozási profil, amely egy adott mezőben növeli a egyezéseket, például a *műfajt* a [musicstoreindex](index-add-scoring-profiles.md#bkmk_ex). A kifejezés fokozása felhasználható a másoknál nagyobb keresési kifejezések további növelésére. Például a megjelenő dokumentumokban a keresési kifejezéseket tartalmazó dokumentumok magasabbak `rock^2 electronic` lesznek, mint az index más kereshető mezőinél. A *rock* keresési kifejezést tartalmazó dokumentumok továbbra is magasabbak lesznek, mint a más keresési kifejezés , amely a kifejezés értékének (2) kiemelését eredményezi.  

 Egy kifejezés növeléséhez használja a "^" karaktert, a szimbólumot a keresett kifejezés végén (a szám). A kifejezéseket növelheti is. Minél nagyobb a növelési tényező, annál nagyobb a kifejezés, mint a többi keresési kifejezéshez képest. Alapértelmezés szerint a Boost faktor 1. Bár a Boost faktornak pozitívnak kell lennie, kevesebb, mint 1 (például 0,20).  

##  <a name="bkmk_regex"></a>Reguláris kifejezés keresése  
 A reguláris kifejezéses keresés a "/" perjelek közötti, a [regexp osztályban](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)dokumentált tartalom alapján keres egyezést.  

 Ha például a "Motel" vagy a "Hotel" tartalmú dokumentumokat szeretné megkeresni, akkor a következőt kell megadnia `/[mh]otel/`:.  A reguláris kifejezésekkel végzett keresések egyetlen szóval vannak összehasonlítva.   

##  <a name="bkmk_wildcard"></a>Helyettesítő karakteres keresés  
 Általánosan felismert szintaxist használhat több (*) vagy egy (?) karakteres helyettesítő karakteres kereséshez. Vegye figyelembe, hogy a Lucene-lekérdezés elemzője egyetlen kifejezéssel támogatja a szimbólumok használatát, nem pedig egy kifejezést.  

 Ha például a "Megjegyzés" előtaggal rendelkező szavakat tartalmazó dokumentumokat szeretné megtalálni, például a "notebook" vagy a "Jegyzettömb" kifejezést, akkor a "Megjegyzés *" szót kell megadnia.  

> [!NOTE]  
>  Nem használhat * vagy? a szimbólum a keresés első karaktere.  
>  A helyettesítő karakteres keresési lekérdezések nem végeznek szöveges elemzést. A lekérdezési időpontokban a helyettesítő karakteres lekérdezési kifejezések összevetése a keresési indexben és a kibontott kifejezésekkel történik.

## <a name="see-also"></a>Lásd még  

+ [Dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [A szűrők és a rendezés OData kifejezésének szintaxisa](query-odata-filter-orderby-syntax.md)   
+ [Egyszerű lekérdezési szintaxis a Azure Searchban](query-simple-syntax.md)   
