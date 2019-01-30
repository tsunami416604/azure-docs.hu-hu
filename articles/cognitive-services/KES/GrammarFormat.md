---
title: Szintaxisformátum – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: További információ a szintaxisformátum a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f3cb307a52cfddebfa97c1b8608549acdd89169d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215068"
---
# <a name="grammar-format"></a>Szintaxisformátum

A szintaxis egy XML-fájlt, amely meghatározza a természetes nyelvű lekérdezések, amelyek a szolgáltatás el tudja értelmezni, valamint hogyan fordítja az ezeket a természetes nyelvű lekérdezéseket a szemantikai lekérdezési kifejezések súlyozott készletét.  A nyelvi szintaxist alapján [SRGS](http://www.w3.org/TR/speech-grammar/), egy W3C szabvány a speech recognition grammars kiterjesztésű index Adatintegráció és Szemantikus funkciók támogatásához.

A következő ismerteti a használható egy gramatice szintaktikai elemek.  Lásd: [ebben a példában](#example) , amely bemutatja, hogy ezeket az elemeket a környezet teljes körű nyelvtan esetében.

### <a name="grammar-element"></a>Elem szintaxis

A `grammar` elem a legfelső szintű elem a nyelvi specifikáció XML.  A szükséges `root` attribútum meghatározza a legfelső szintű szabály, amely meghatározza a nyelvtani kiindulópontjaként nevét.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Elem importálása

A `import` elem sémadefiníciót importál a külső fájl engedélyezéséhez attribútum hivatkozik. Az elemnek kell lennie, a legfelső szintű gyermek `grammar` elem és jelennek meg minden `attrref` elemeket. A szükséges `schema` attribútum nevét adja meg egy séma fájlt a nyelvtani XML-fájl ugyanabban a könyvtárban található. A szükséges `name` elem azt határozza meg a séma alias, amely későbbi `attrref` elemek használja, ha ebben a sémában meghatározott attribútumok hivatkozik.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>a szabály elem

A `rule` elem egy gramatické pravidlo egy szerkezeti egység, amely meghatározza, hogy a rendszer tudja értelmezni lekérdezési kifejezések készletét definiálja.  Az elemnek kell lennie, a legfelső szintű gyermek `grammar` elemet.  A szükséges `id` attribútum nevét adja meg a szabályt, amely a hivatkozott `grammar` vagy `ruleref` elemeket.

A `rule` elem határozza meg azon jogi bővülésből.  Szöveg jogkivonatok közvetlenül a bemeneti lekérdezés egyeztetéshez.  `item` elemek ismétlődések adja meg, és értelmezése valószínűségek alter.  `one-of` adatok azt jelzik, hogy alternatív lehetőségeket.  `ruleref` elemek engedélyezése építése összetettebb bővülésből egyszerűbb állók közül.  `attrref` elemek lehetővé teszik az indexből attribútumértékek egyezések.  `tag` elemek adja meg a értelmezése szemantikáját, és értelmezése mekkora valószínűséggel módosíthatja.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Példa elem

A választható `example` elem azt határozza meg, előfordulhat, hogy fogadja el a tartalmazó példa kifejezések `rule` definíciója.  Ez használható dokumentáció és/vagy automatizált tesztelés.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>konfigurációelem-elem

A `item` elem csoportok nyelvi szerkezeteket sorozata.  Jelzi a helybővítés során ismétléseihez való használata, vagy adja meg az alternatív megoldások együtt is használhatók a `one-of` elemet.

Ha egy `item` elem nem gyermeke egy `one-of` elemben hozzárendelésével adhatja ismétlését a karakterláncon belül lévő feladatütemezési a `repeat` attribútum count értékre.  Egy számláló értékét "*n*" (ahol *n* egy egész szám) azt jelzi, hogy a feladatütemezés pontosan meg kell történnie *n* alkalommal.  Egy számláló értékét "*m*-*n*" lehetővé teszi, hogy a feladatütemezés közötti *m* és *n* alkalommal szélsőértékeket is beleértve.  Egy számláló értékét "*m*–" Megadja, hogy a feladatütemezés szerepelnie kell legalább *m* alkalommal.  A választható `repeat-logprob` attribútum segítségével módosítható a minimális túl minden további ismétlési értelmezése valószínűségét.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Amikor `item` elemek jelennek meg gyermekeiként egy `one-of` elem, meghatározzák a helybővítés alternatívák készletét.  A használat, a választható `logprob` attribútum meghatározza, hogy a relatív log mekkora valószínűséggel a különböző lehetőségek közül.  Adott valószínűség *p* 0 és 1 közötti megfelelő napló valószínűségét, log számított (*p*), ahol a log() a természetes logaritmusát függvény.  Ha nincs megadva, `logprob` , az alapértelmezett érték 0, amely nem módosítja a értelmezése valószínűségét.  Vegye figyelembe, hogy log valószínűség mindig negatív lebegőpontos számnak vagy 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>egy-elem

A `one-of` elem azt határozza meg alternatív bővülésből között gyermekszerepkörei `item` elemeket.  Csak `item` elemek belül jelennek meg a `one-of` elemet.  A különböző lehetőségek közül relatív valószínűségek keresztül lehet megadni a `logprob` minden gyermek attribútum `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref elem

A `ruleref` elem azt határozza meg az érvényes kiterjesztések keresztül egy másik mutató hivatkozások `rule` elemet.  Használatával `ruleref` elemeket, az összetett kifejezések felépíthető egyszerűbb szolgáló szabályok hatálya alól.  A szükséges `uri` attribútum azt jelöli, a hivatkozott neve `rule` a szintaxis használatával "#*rulename*".  A hivatkozott szabály szemantikai kimenetét rögzíti, használja az opcionális `name` attribútumot, amely a szemantikai kimeneti hozzá van rendelve a változó nevét adja meg.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref elem

A `attrref` elemre hivatkozik egy index attribútum, lehetővé téve az index megfigyelhető a megfelelő attribútum értékei alapján.  A szükséges `uri` attribútum meghatározza, hogy az index sémát és attribútum nevét, a szintaxis használatával "*%{schemaname/*#*attrName*".  Lennie kell egy előző `import` elem, amely importálja a sémát nevű *%{schemaname/*.  Az attribútum neve a megfelelő sémában meghatározott elemnévvel attribútum neve.

Egyező felhasználói bevitelt, mellett a `attrref` elem is objektumot ad vissza egy strukturált lekérdezési kimeneteként, amely az objektumok részhalmazát kiválasztja az index a bemeneti érték megfelelő.  A választható `name` attribútum használatával adja meg, ahol a lekérdezési objektum kimenetet kell tárolni a változó nevét.  A lekérdezési objektummal összeállítható kialakításához több más lekérdezési objektummal összetett kifejezések.  Lásd: [szemantikai értelmezés](SemanticInterpretation.md) részleteiről.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Lekérdezés befejezése

Lekérdezés befejezésekből támogatja a részleges felhasználói lekérdezések értelmezése során, a minden egyes hivatkozott attribútumot tartalmaznia kell "starts_with" sémadefiníciója műveletként.  Egy felhasználó lekérdezés előtagot adott `attrref` fog, amelyek befejezik a előtagot az index összes értéket, és az eddig is számtalan előnyét minden teljes érték egy külön értelmezése a szintaxis mint.  

Példák:
* Egyező `<attrref uri="academic#Keyword" name="keyword"/>` ellen a lekérdezés "dat" előtag állít elő, az "adatbázis" kapcsolatos tanulmányok egy értelmezéséhez, a "data szintű adatbányászatra", és így tovább kapcsolatos tanulmányok egy értelmezése.
* Egyező `<attrref uri="academic#Year" name="year"/>` ellen a lekérdezés "200" előtag állít elő, a "2000", tanulmányok egy értelmezése egy értelmezése tanulmányok "2001" stb.

#### <a name="matching-operations"></a>Egyező műveletek

Pontos egyezés mellett válassza attribútumot típusok is támogatási előtagot, és egyenlótlenség megegyezik a választható keresztül `op` attribútum.  Az index nem objektum nem megfelelő értéket, ha a szintaxis elérési le van tiltva, és a szolgáltatás nem hoz létre bármilyen értelmezéseket a nyelvtani elérési áthaladó.   A `op` "eq" attribútum alapértelmezett értéke.

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

A következő táblázat felsorolja a támogatott `op` értékek az egyes attribútum.  Azok a megfelelő index művelet sémadefiníciója attribútum foglalandó használatához szükséges.

| Attribútum típusa | Op érték | Leírás | Index művelet
|----|----|----|----|
| Karakterlánc | EQ | Karakterlánc pontos egyezés | egyenlő |
| Karakterlánc | starts_with | Előtag-egyeztetést karakterlánc | starts_with |
| Int32, Int64, dupla | EQ |  Numerikus pontos egyezés | egyenlő |
| Int32, Int64, dupla | lt, a le, a BT, a ge | Numerikus egyenlótlenség match (<, < =, >, > =) | is_between |
| Int32, Int64, dupla | starts_with | Előtag-egyeztetést a decimális jelölés érték | starts_with |

Példák:
* `<attrref uri="academic#Year" op="lt" name="year"/>` a bemeneti karakterlánc "-2000" megegyezik, és kizárólag a 2000, mielőtt közzétett összes tanulmányok adja vissza.
* `<attrref uri="academic#Year" op="lt" name="year"/>` nem egyezik a bemeneti karakterlánc "20", mert nincs ismertetők az indexben, az év 20 előtt közzé.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` a "dat" bemeneti karakterlánc megfelel, és adja vissza egy egyetlen értelmezése tanulmányok "adatbázis", "data szintű adatbányászatra", stb.  Ez a ritka használati eset.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` a bemeneti karakterlánc "20" és a egy egyetlen értelmezése ismertetők az értéket ad vissza 200-299, a 2000-2999, stb-ben közzétett megegyezik.  Ez a ritka használati eset.

### <a name="tag-element"></a>Címke elem

A `tag` elem azt határozza meg, hogyan kell értelmezni van a nyelvtani keresztül elérési útja.  Sorozata, pontosvesszővel válassza el végződő utasításokat tartalmazza.  Lehet, hogy egy utasítás szövegkonstans vagy egy változót, egy másik változó-hozzárendelés.  Változóhoz is rendelhet hozzá a kimenet egy függvény 0 vagy több paramétert.  Minden függvény paramétere egy vagy egy változó adható meg.  Ha a függvény nem ad vissza kimenetet, a hozzárendelés van hagyva.  Változó hatóköre helyi tartalmazó szabályhoz.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Minden egyes `rule` nyelvtani rendelkezik előre meghatározott nevű változó "lejárt", a szabály szemantikai kimenetét képviselő.  Az érték kiszámítása minden egyes olyan útvonalon keresztül átnézése szemantikai utasítás kiértékelésével a `rule` a felhasználónak bemenet lekérdezése.  Az értékelés végén található "lejárt" változóhoz rendelt érték a szabály a szemantikai kimenet.  A szemantikai értelmezése a szintaxis egy felhasználó a lekérdezéshez kimenete a legfelső szintű szabály szemantikai kimenetét.

Egyes utasítások megváltoztathatják értelmezése elérési útnak valószínűségét additív log valószínűség eltolás bevezetésével.  Egyes utasítások elutasíthatja értelmezése funkciót azonban teljesen, ha meg van adva a feltételek nem teljesülnek.

Szemantikai támogatott funkciók listáját lásd: [szemantikai funkciók](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Értelmezés valószínűsége

A valószínűsége annak, értelmezését elérési útnak a nyelvtani keresztül minden összegző napló valószínűségét a `<item>` elemeket és a vizualizáción észlelt szemantikai funkciók.  Azt ismerteti, hogy a bemeneti megadottal egyező relatív valószínűségét.

Adott valószínűség *p* 0 és 1 közötti megfelelő napló valószínűségét, log számított (*p*), ahol a log() a természetes logaritmusát függvény.  Napló valószínűségek lehetővé teszi a rendszert, hogy egy egyszerű értelmezését útvonala összesített valószínűségét összeadódhatnak.  Azt is ilyen közös valószínűségi számításoknál közös lebegőpontos alulcsordulás elkerülhető.  Vegye figyelembe, hogy a kialakításból fakadóan log mekkora valószínűséggel mindig negatív lebegőpontos számnak vagy 0, ahol nagyobb értékek azt jelzik, nagyobb valószínűséggel.

## <a name="example"></a>Példa

Az alábbiakban látható a tanulmányi kiadványok tartományból, amely bemutatja a különböző elemek nyelvtan, például XML:

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
