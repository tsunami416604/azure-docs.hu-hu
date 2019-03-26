---
title: Lucene lekérdezési szintaxis – Azure Search
description: Az Azure Search szolgáltatással használt a teljes Lucene szintaxis referenciája.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
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
ms.openlocfilehash: f1eba2da1404f5b47d137b3c4f7b4cb9ceab43ea
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438053"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Lucene lekérdezési szintaxis az Azure Search szolgáltatásban
Azure Search-lekérdezéseket a gazdag alapján írhat [Lucene lekérdezéselemző](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) Forms speciális lekérdezési szintaxis: helyettesítő karakter, intelligens keresést, közelségi keresésre, reguláris kifejezések összefűzése néhány példa. Nagy része a Lucene lekérdezéselemző szintaxist [átvenni megvalósítása az Azure Search](search-lucene-query-architecture.md), kivéve a *keresések tartomány* az Azure Search révén kialakítani, amely `$filter` kifejezéseket. 

## <a name="how-to-invoke-full-parsing"></a>Hogyan kell elindítani a teljes körű elemzés

Állítsa be a `queryType` keresési paramétert adja meg, melyik elemzőt használja. Érvényes értékek a következők `simple|full`, a `simple` az alapértelmezett, és `full` Lucene számára. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Példa ábrázoló teljes szintaxis

A következő példa olyan dokumentumokat keres a használatával a Lucene lekérdezési szintaxis, egyértelmű az indexben a `queryType=full` paraméter. Ez a lekérdezés visszaadja a "Hotels", ahol a kategória mező tartalmazza a "budget" kifejezést, és a "nemrégiben felújított" kifejezést tartalmazó összes kereshető mezőt. "Nemrégiben felújított" kifejezést tartalmazó dokumentumok újabb rangsora eredményeként az előfizetési időszak boost érték (3).  

A `searchMode=all` paraméter fontos ebben a példában. Valahányszor operátorok a lekérdezésre, általában célszerű `searchMode=all` annak érdekében, hogy *összes* egyezik a feltételeknek.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28&querytype=full
```

 Másik megoldásként használhatja a POST:  

```
POST /indexes/hotels/docs/search?api-version=2015-02-28
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

További példák: [Lucene lekérdezési szintaxis példái létrehozásához az Azure Searchben a lekérdezések](search-query-lucene-examples.md). A teljes függő a lekérdezési paraméterek megadásával kapcsolatos részletekért lásd: [dokumentumok keresése &#40;Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  Az Azure Search is támogatja a [egyszerű lekérdezési szintaxis](query-simple-syntax.md), amely egy egyszerű és hatékony lekérdezési nyelvet, az egyszerű kulcsszavas keresést is használható.  

##  <a name="bkmk_syntax"></a> Szintaxis-alapok  
 A következő szintaxis-alapok, a Lucene-szintaxist használó összes lekérdezésére érvényes.  

### <a name="operator-evaluation-in-context"></a>Operátor kiértékelése a környezetben

Elhelyezés határozza meg, e egy szimbólum egy operátort vagy egy másik karakter a karakterláncban szereplő kerül értelmezésre.

Például teljes Lucene-szintaxisban, a hullámos vonallal (~) használatos intelligens keresés és a közelségi keresésre. Ha a határolójeles kifejezés után célszerű elhelyezni ~ közelségi keresésre hív meg. Ha egy előfizetési időszak végén ~ intelligens keresés hív meg.

Belül kifejezést például a "üzleti ~ elemző", a karakter nem abban az esetben az operátornak minősül. Ebben az esetben a rendszer kifejezést lekérdezés, feltéve, hogy a lekérdezés [teljes szöveges keresés](search-lucene-query-architecture.md) a [lexikai elemzés](search-lucene-query-architecture.md#stage-2-lexical-analysis) ki törli a ~ és működésképtelenné válik a kifejezés "üzleti ~ elemző" két: üzleti vagy elemző.

A fenti példában a hullámos vonallal (~), de ugyanaz az elv érvényes az minden operátor.

### <a name="escaping-special-characters"></a>Különleges karaktereket escape-karaktersorozat

 Speciális karaktereket a keresési szöveget részeként használandó kell megjelölni. A fordított perjel előtaggal azokat is formában (\\). Escape-karakterrel kell igénylő speciális karakterek a következők:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Például egy helyettesítő karaktert használja \\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Az URL-címek nem biztonságos és fenntartott karakterek kódolása

 Ellenőrizze, hogy az összes nem biztonságos és fenntartott karakterek kódolt egy URL-címet. Például "#" nem nem biztonságos karakter, mert az egy URL-fragement/horgony azonosítója. A karakter kell kódolni `%23` , ha egy URL-címet használják. "&" '=' és példák a fenntartott karakterek korlátozza a paramétereket, és adja meg az értékeket az Azure Search szolgáltatásban. Lásd: [RFC1738: Egységes erőforrás-keresők (URL)](https://www.ietf.org/rfc/rfc1738.txt) további részletekért.

 Nem biztonságos karakterek ``" ` < > # % { } | \ ^ ~ [ ] ``. Fenntartott karakterek: `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Sorrend operátorokat: csoportosítás és a mező csoportosítás  
 Zárójelek segítségével segédlekérdezések, beleértve az operátorok a zárójeles utasításon belül létrehozni. Ha például `motel+(wifi||luxury)` dokumentumok "amelyben" kifejezés és "Wi-Fi" vagy "engedélyezhető" (vagy mindkettő) fog keresni.

Mezőt a csoportosítás akkor hasonló, de egyetlen mezőt, a csoportosítás hatóköröket. Ha például `hotelAmenities:(gym+(wifi||pool))` megkeresi a "hotelAmenities" mező "Fitnessklub" és "Wi-Fi" vagy "Fitnessklub" és "-készlet".  

### <a name="searchmode-parameter-considerations"></a>SearchMode paraméterrel kapcsolatos szempontok  
 A hatását `searchMode` a lekérdezések, leírtak szerint [egyszerű lekérdezési szintaxis az Azure Search](query-simple-syntax.md), a Lucene lekérdezési szintaxis egyformán vonatkozik. Nevezetesen `searchMode` nem együtt operátorok eredményezhet, amely tűnhet, hogy szokatlan nvdimm-n törlés következményei állíthatja be, hogy a paraméter a lekérdezési eredmények. Ha az alapértelmezett megőrzi `searchMode=any`, és a egy NOT operátor szerinti szűrése, a művelet számított vagy műveletként, hogy "Győr" nem "Seattle" adja vissza, amelyek nem Seattle összes városok használja.  

##  <a name="bkmk_boolean"></a> Logikai operátorokat (AND, OR, NOT) 
 Mindig adjon meg szöveget logikai operátorokat (AND, OR, NOT) minden korlátokat.  

### <a name="or-operator-or-or-"></a>VAGY operátor `OR` vagy `||`

A vagy operátor egy függőleges oszlopra vagy egy függőleges vonal. Például: `wifi || luxury` "Wi-Fi" vagy "engedélyezhető" vagy mindkettőt tartalmazó dokumentumokat fogja keresni. Mivel az alapértelmezett együtt operátor vagy sikerült is hagyhatja azt, úgy, hogy `wifi luxury` megfelelője `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>ÉS az operátorral `AND`, `&&` vagy `+`

Az és operátor egy pontosvessző vagy a plusz jelre. Például: `wifi && luxury` "Wi-Fi" és "engedélyezhető" is tartalmazó dokumentumokat fogja keresni. A plusz (+) karakter szükséges feltételek szolgál. Ha például `+wifi +luxury` előírja, hogy mindkét kifejezés valahol meg kell jelennie az egyetlen dokumentum terén.


### <a name="not-operator-not--or--"></a>NOT operátor `NOT`, `!` vagy `-`

A NOT operátor felkiáltójel vagy a mínuszjelet. Például: `wifi !luxury` fog keresni a dokumentumokon, amelyek a "wifi" távú és/vagy nem rendelkezik "engedélyezhető". A `searchMode` vezérlők lehetőséget, hogy egy kifejezés, és a NOT operátor műveletet vagy végbe hiányában a lekérdezésben az egyéb feltételeket egy + vagy || operátort. Emlékeztetnek arra, hogy `searchMode` is megadni `any`(alapértelmezés) vagy `all`.

Használatával `searchMode=any` növeli a lekérdezések visszahívása, többek között további találatok, és alapértelmezés szerint – értelmezi "Vagy NOT". Ha például `wifi -luxury` egyezni fog a dokumentumok, hogy vagy tartalmazza a kisbetűs *Wi-Fi* vagy azokkal, amelyek nem tartalmaznak a kifejezés *engedélyezhető.*

Használatával `searchMode=all` növeli a lekérdezések a pontosság, többek között kevesebb találatot, és alapértelmezés szerint – értelmezi "És a NOT". Ha például `wifi -luxury` egyezni fog a dokumentumok, amely tartalmazza a kisbetűs `wifi` , és nem tartalmaz kifejezést `luxury`. Ez valószínűleg a intuitívabb viselkedés - operátor esetében. Emiatt érdemes lehet kiválasztása `searchMode=all` keresztül `searchMode=any` szeretné-e visszaírási helyett a pontosság keresések optimalizálásához *és* gyakran használnak a felhasználók a `-` a keresésekben operátor.

##  <a name="bkmk_querysizelimits"></a> Lekérdezés fájlméretre vonatkozó korlátozások  
 Lekérdezések, melyet elküldhet az Azure Search mérete korlátozva van. Pontosabban akkor is, legfeljebb 1024 záradékok (kifejezésnek AND, OR, és így tovább). A lekérdezésben minden egyes hűségidőt mérete körülbelül 32 KB korlátozva is van. Ha az alkalmazás keresési lekérdezéseket programozott módon hoz létre, azt javasoljuk, kialakítása, úgy, hogy azt nem generál korlátlan méretű lekérdezések.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Pontozó helyettesítő és regex lekérdezéseket.
 Az Azure Search használ, a kiértékelés gyakorisága-alapú ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) szöveges lekérdezések. Azonban helyettesítő és regex lekérdezéseket, ahol feltételek hatóköre lehet széleskörű, a gyakoriság tényezőt figyelmen kívül hagyja, hogy az ennek a területnek a hordozóeltolást ritkább feltételek származó egyezések felé. Minden megfelelő elemet a helyettesítő karaktert tartalmazó és a reguláris kifejezés egyaránt kezelik kereséseket.

##  <a name="bkmk_fields"></a> A mező-hatáskörű lekérdezések  
 Megadhat egy `fieldname:searchterm` konstrukció fielded lekérdezési műveletet, ha a mező kitöltése egyetlen szó, és a keresési kifejezést is egy szót vagy kifejezést, igény szerint a logikai operátorokkal együtt meghatározásához. Néhány példa a következők:  

- Műfaj: jazz nem előzmények  

- előadók: ("mérföld is" "John Coltrane")

  Ügyeljen arra, hogy helyezzen több karakterlánc idézőjelek között, ha azt szeretné, mindkét karakterlánc, kiértékelendő egyetlen egységként, ebben az esetben a két különböző előadók keresése a `artists` mező.  

  A megadott mező `fieldname:searchterm` kell lennie egy `searchable` mező.  Lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) indexattribútumokat használata a Meződefiníciók részleteiért.  

##  <a name="bkmk_fuzzy"></a> intelligens keresést  
 Egy intelligens keresés egyezések megkeresi, hogy rendelkezik egy hasonló konstrukció. / [Lucene dokumentáció](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), az intelligens keresés alapján [Damerau-Levenshtein távolság](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Az intelligens keresés kibontásával 50 feltételek, amelyek megfelelnek a távolság által legfeljebb egy kifejezést. 

 Ehhez az intelligens keresést, használja a hullámos vonallal "~" és a egy nem kötelező paraméter, a 0. és 2 (alapértelmezett), közötti szám, amely megadja a Szerkesztés egyetlen szó végén szimbólum. Például "blue ~" vagy "kék ~ 1" adna vissza "blue", "Csupa kék" és "összeragasztásában".

 Intelligens keresés feltételeket, nem a kifejezések csak alkalmazható, de a hullámos vonallal hozzáfűzése külön-külön mindegyik kifejezés egy név vagy kifejezés. Például "Unviersty ~, ~" Wshington ~ "a"Eötvös Loránd Tudományegyetem"megfelel.
 

##  <a name="bkmk_proximity"></a> közelségi keresésre  
 Közelségi keresés segítségével keresse meg a feltételeket, amelyek egymáshoz közel egy dokumentumban. Egy hullámos vonallal beszúrása "~" kifejezés végén jel követ szavak, amelyek a közelségi határ létrehozása száma. Ha például `"hotel airport"~5` a feltételek "hotel", és minden más belül 5 szó "repülőtér" dokumentumban található.  


##  <a name="bkmk_termboost"></a> kiemelési távú  
 Kifejezés kiemelési prioritása nagyobb, ha a gyorsított időszak alatt, dokumentum, amely nem tartalmazza a kisbetűs viszonyított tartalmazza a dokumentum hivatkozik. Ez eltér pontozási profilok, hogy miként növelhető a pontozási profilok specifikus használati helyett egyes mezők.  

Az alábbi példa segít a különbségeket mutatja be. Tegyük fel, hogy egy pontozó profilt, amely felgyorsíthatók megfelel egy bizonyos mezőben, például: *műfaj* a a [musicstoreindex példa](index-add-scoring-profiles.md#bkmk_ex). Kifejezés kiemelési felhasználható bizonyos keresési feltételek magasabb, mint a többi további növelése érdekében. Ha például `rock^2 electronic` fog jelentősen növelheti a műfaj mezőben magasabb, mint más az indexben kereshető mezőket a keresési feltételeket tartalmazó dokumentumokat. További, a keresési kifejezést tartalmazó dokumentumok *rock* magasabb, mint a többi keresési kifejezés besorolásra kerülő *elektronikus* eredményeként az előfizetési időszak boost érték (2).  

 Növelése érdekében egy kifejezést használja a beszúrási jellel "^", a keresett kifejezés végén boost tényezővel (szám) szimbólum. Jelentősen növelheti a mondatok is. Minél nagyobb a boost tényező, több megfelelő kifejezés lesz más keresési feltételek viszonyítva. Alapértelmezés szerint a boost tényező: 1. Bár a boost tényező pozitívnak kell lennie, 1-nél kisebb (például 0,20) lehet.  

##  <a name="bkmk_regex"></a> reguláris kifejezés keresése  
 Keresés reguláris kifejezés közötti útjaiban perjeleket a "/", a dokumentált tartalma alapján egyezést talál az [RegExp osztály](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).  

 Keresse meg a "amelyben" vagy "hotel" tartalmazó dokumentumok, például adja meg a `/[mh]otel/`.  Reguláris kifejezés keresések a rendszer összeveti egyetlen szavakat.   

##  <a name="bkmk_wildcard"></a> helyettesítő karakteres keresés  
 Szintaxis is használható általában felismerhető a több (*) vagy egy (?) karakter helyettesítő karakteres kereséssel. Vegye figyelembe, hogy a Lucene lekérdezéselemző használatát a szimbólumok egyetlen kifejezés, és a egy kifejezés nem támogatja.  

 Például a "note", például a "jegyzetfüzet" vagy "Jegyzettömb" előtaggal szavakat tartalmazó dokumentumok keresése adja meg "Megjegyzés: *".  

> [!NOTE]  
>  Nem használhatja a * és? szimbólum a keresési első karaktert.  
>  Nincs szöveg elemzése a helyettesítő karakteres keresés lekérdezések történik. Lekérdezéskor helyettesítő karaktert tartalmazó lekérdezési kifejezések összehasonlítja a search-index elemzett feltételeit és kibontva.

## <a name="see-also"></a>Lásd még  

+ [Dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [A szűrők és a rendezés OData-kifejezések szintaxisa](query-odata-filter-orderby-syntax.md)   
+ [Az Azure Search egyszerű lekérdezési szintaxis](query-simple-syntax.md)   
