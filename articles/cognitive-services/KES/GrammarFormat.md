---
title: A Tudásbázis feltárása Service API nyelvtanformátum |} Microsoft Docs
description: További tudnivalók a nyelvtanformátum a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 27202379b8c36696a380049336229cac040b0108
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347206"
---
# <a name="grammar-format"></a>Nyelvtanformátum
A nyelvtan, amely meghatározza a súlyozott készletét, amely a szolgáltatás el tudja értelmezni, valamint hogyan a természetes nyelvű lekérdezéseket Szemantikus lekérdezési kifejezések fordításának természetes nyelvű lekérdezés XML-fájl.  A nyelvtan szintaxis alapján [SRGS](http://www.w3.org/TR/speech-grammar/), a W3C szabvány Beszéd felismerés nyelvtanok, a kiterjesztések index Adatintegráció és szemantikai funkciók támogatásához.

Az alábbiak azt ismertetik, amelyek a nyelvtanban használható szintaktikai elemek.  Lásd: [ebben a példában](#example) egy teljes nyelvtanhoz, amely bemutatja, hogy ezeket az elemeket a környezetben.

### <a name="grammar-element"></a>a nyelvtan elem 
A `grammar` eleme a legfelső szintű elem a nyelvtan specifikációjában XML.  A szükséges `root` attribútum meghatározza a legfelső szintű szabály, amely meghatározza a nyelvtan kiindulópontjaként nevét.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Elem importálása
A `import` elem sémadefiníciót importál egy külső fájlból hivatkozások engedélyezéséhez. Az elemnek kell lennie a legfelső szintű gyermeke `grammar` elem és jelennek meg minden `attrref` elemek. A szükséges `schema` attribútum határozza meg a nyelvtan XML-fájl ugyanabban a könyvtárban található séma fájl nevét. A szükséges `name` elem azt adja meg a séma alias, amely későbbi `attrref` elemek használja, ha ebben a sémában meghatározott attribútumok hivatkozik.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>a szabály elem
A `rule` elem definiálja a nyelvtani szabály egy szerkezeti egység, amely meghatározza, hogy a rendszer tudja értelmezni lekérdezési kifejezések készlete.  Az elemnek kell lennie a legfelső szintű gyermeke `grammar` elemet.  A szükséges `id` attribútum meghatározza, hogy az a szabály neve, a hivatkozott `grammar` vagy `ruleref` elemek.

A `rule` elem jogi bővítések álló készletet határoz meg.  Szöveg jogkivonatok közvetlenül a bemeneti lekérdezés egyeztetéshez.  `item` elemek adja meg a ismétlődik, és módosítsa a értelmezése valószínűség.  `one-of` elemek jelzésére alternatív lehetőségeket.  `ruleref` elemek engedélyezése építése összetettebb bővítések a egyszerűbb állók közül.  `attrref` elemek az indexből attribútumértékek egyezések engedélyezése.  `tag` elemek adja meg a értelmezési szemantikáját, és módosíthatja a értelmezése valószínűség.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Példa elem
A választható `example` elem határozza meg, előfordulhat, hogy fogadja el a tartalmazó példa kifejezések `rule` definíciója.  Ez használható dokumentáció és/vagy automatikus tesztelése.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>elem eleme
A `item` elem csoportok nyelvtan szerkezetek sorozata.  Jelzi a ismétlődését tartalmazza a bővítés során, vagy megadhatja a alternatívák együtt is használható a `one-of` elemet.

Ha egy `item` elem nem gyermeke a `one-of` elem, zárt művelet megismétlését hozzárendelésével határozhatja meg a `repeat` egy számérték attribútumot.  A count érték "*n*" (ahol *n* egész szám) azt jelzi, hogy a feladatütemezési pontosan kell-e magukat *n* alkalommal.  A count érték "*m*-*n*" lehetővé teszi, hogy a feladatütemezési közötti *m* és *n* időkorlátja, szélsőértékeket is beleértve.  A count érték "*m*-" határozza meg, hogy a feladatütemezési szerepelnie kell legalább *m* alkalommal.  A választható `repeat-logprob` attribútum segítségével módosítsa a minimális túl minden további ismétlődési értelmezése valószínűségét.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Ha `item` elem gyermekeként jelennek meg a `one-of` elem, azok bővítése alternatívák a kulcstulajdonságokat határozza meg.  Az használata nem kötelező `logprob` attribútum határozza meg a különböző lehetőségek között a relatív napló valószínűség.  Megadott valószínűség *p* 0 és 1 között, a napló számítható ki a megfelelő naplót valószínűség (*p*), ahol a log() a természetes napló függvény.  Ha nincs megadva, `logprob` az alapértelmezett érték 0, amely nem módosítja a értelmezése valószínűség.  Vegye figyelembe, hogy napló valószínűség mindig a lebegőpontos negatív vagy 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>egy-elem
A `one-of` elem határozza meg az alternatív bővítések között gyermekszerepkörei `item` elemek.  Csak `item` elemek belsejében szerepelhet egy `one-of` elemet.  A különböző lehetőségek között a relatív valószínűség keresztül adható meg a `logprob` minden gyermek attribútum `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref elem
A `ruleref` elem megadja egy másikra hivatkozik keresztül érvényes bővítések `rule` elemet.  Használatával `ruleref` elemeket, az összetett kifejezések építhetők az egyszerűbb szabályok alapján.  A szükséges `uri` attribútum azt jelöli, a hivatkozott neve `rule` a szintaxis "#*rulename*".  A hivatkozott szabály szemantikai kimenetét rögzíti, használja az opcionális `name` attribútum segítségével adhatja meg, amely a szemantikai kimeneti hozzá van rendelve a változó nevét.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref elem
A `attrref` elemre hivatkozik egy index attribútum, így a megfigyelt megfelelő attribútum értékekkel, amelyet az index.  A szükséges `uri` attribútum határozza meg az index séma és a szintaxis attribútum nevét "*sémanév*#*attrName*".  Lennie kell egy megelőző `import` importálására a séma nevű elem *sémanév*.  Az attribútum neve a megfelelő sémában meghatározott elemnévvel attribútum nevét.

Felhasználói bevitel megfelelő mellett a `attrref` elem is objektumot ad vissza egy structured query objektumok részét kiválasztja a megfelelő a bemeneti érték index kimenetként.  Használja az opcionális `name` attribútum segítségével adhatja meg, ha a lekérdezés objektum eredményét szeretné tárolni a változó nevét.  A lekérdezés objektum összeállítható kialakításához több más lekérdezés objektummal összetett kifejezések.  Lásd: [szemantikai értelmező](SemanticInterpretation.md) részleteiről.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Lekérdezés-végrehajtási 
Lekérdezés befejezésekhez támogatja a részleges felhasználói lekérdezések értelmezése során, a minden hivatkozott attribútum tartalmaznia kell "starts_with" műveletként a séma-definícióban.  Adott felhasználó lekérdezésben előtag, `attrref` értékekre, minden az indexet, amelyek befejezik a előtag, és minden egyes teljes értéknek, a szintaxis külön értelmezésének yield.  

Példák:
* Megfelelő `<attrref uri="academic#Keyword" name="keyword"/>` szemben a lekérdezés "dat" előtag állít elő, az "adatbázis" kapcsolatos által írt cikkeket egy értelmezése, egy megfeleltetés a által írt cikkeket kapcsolatos "adatbányászat" stb.
* Megfelelő `<attrref uri="academic#Year" name="year"/>` szemben a lekérdezés "200" előtag hoz létre egy értelmezése által írt cikkeket az "2000", az egyik értelmezése "2001" által írt cikkeket, stb.

#### <a name="matching-operations"></a>Megfelelő művelete
Pontos egyezés mellett válassza attribútum típusok is támogatási előtag, és az opcionális keresztül egyezik egyenlőtlen `op` attribútum.  Az index nem objektumnak egy megfelelő értéket, ha a nyelvtan elérési le van tiltva, és a szolgáltatás nem hoz létre minden értelmezéseket áthaladó a nyelvtan útvonalon keresztül.   A `op` "eq" attribútum alapértelmezett értéke.

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

A következő táblázat felsorolja a támogatott `op` értékek az egyes attribútum.  A használatához szükséges, hogy a megfelelő indexművelet attribútum sémadefiníciót szerepeltetni.

| Attribútum típusa | Op érték | Leírás | Az index művelet
|----|----|----|----|
| Sztring | EQ | Karakterlánc pontos egyezés | egyenlő |
| Sztring | starts_with | Karakterlánc-egyeztetést előtagja | starts_with |
| Int32, Int64, dupla | EQ |  Numerikus pontos egyezés | egyenlő |
| Int32, Int64, dupla | lt, le, gt, ge | Numerikus egyenlőtlen egyezés (<, < =, >, > =) | is_between |
| Int32, Int64, dupla | starts_with | Előtag meg a decimális jelöléssel érték | starts_with |

Példák:
* `<attrref uri="academic#Year" op="lt" name="year"/>` megfelel a "-2000" bemeneti karakterlánc, és kizárólag a 2000 előtt közzétett összes által írt cikkeket adja vissza.
* `<attrref uri="academic#Year" op="lt" name="year"/>` nem egyezik a bemeneti karakterlánc "20", mert az index a 20 évnél korábban közzétett nem által írt cikkeket.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` a bemeneti karakterlánc "dat" megegyezik, és adja meg egy egyetlen értelmezése által írt cikkeket "adatbázis", "adatbányászat", stb.  Ez az eset ritka használja.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` megfelel a bemeneti karakterlánc "20-ra és értéket ad vissza egy egyetlen értelmezése által írt cikkeket a közzétett 200-299, 2000-2999, stb.  Ez az eset ritka használja.

### <a name="tag-element"></a>Címke elem
A `tag` elem határozza meg, hogyan kell értelmezni van a nyelvtan keresztül elérési útja.  Pontosvessző végződő utasítások sorozatát tartalmaz.  Egy utasítás hozzárendelése egy vagy egy változó egy másik változó lehet.  Egy változóhoz is rendelhet hozzá a kimenet egy függvény 0 vagy több paramétert.  Minden egyes függvényparaméter szövegkonstans vagy változó adható meg.  A függvény nem ad eredményül kimenetet, ha meg van adva a-hozzárendelés.  A változó hatókörének helyi tartalmazó szabályhoz.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Minden egyes `rule` a nyelvtanban rendelkezik előre meghatározott képviselő változót, nevű, "out", a szabály szemantikai kimenetét.  Az értékét számítja ki a útvonalon keresztül be szemantikai utasítás kiértékelése a `rule` bemeneti a felhasználó megfelelő lekérdezése.  A kiértékelés végén "out" változójához rendelt érték a szabály szemantikai kimenetét.  A szemantikai a nyelvtan egy felhasználó lekérdezése értelmezése eredménye a gyökérszintű szabály szemantikai kimenetét.

Néhány utasítások additívak napló valószínűség eltolás bevezetésével módosíthatja egy értelmezése görbe valószínűségét.  Néhány utasítások elutasíthatja értelmezési elemet, ha meg van adva a feltételek nem teljesülnek.

A szemantikai támogatott funkciók listáját lásd: [szemantikai funkciók](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Értelmezés valószínűség
A nyelvtan keresztül értelmezése elérési útjának valószínűségét az összes összesített napló valószínűségét a `<item>` elemek és menet észlelt szemantikai funkciók.  Ismerteti az egy adott bemeneti feladatütemezési megfelelő relatív valószínűségét.

A valószínűségi megadott *p* 0 és 1 között, a napló számítható ki a megfelelő naplót valószínűség (*p*), ahol a log() a természetes log függvény.  Napló valószínűség használata lehetővé teszi a rendszer felhalmozhat egyszerű továbbá használatával értelmezése elérési útjának közös valószínűségét.  Emellett Ezzel elkerülheti lebegőpontos alulcsordulás közös ilyen közös valószínűségi számításoknál használhatja.  Vegye figyelembe, hogy úgy lett kialakítva, a napló valószínűség mindig a lebegőpontos negatív vagy 0, ahol nagyobb értékek azt jelzik, nagyobb valószínűséggel.

<a name="example"></a>
## <a name="example"></a>Példa
A következő példában látható egy XML a academic kiadványok tartományból, amely bemutatja a nyelvtan különböző elemeinek:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
