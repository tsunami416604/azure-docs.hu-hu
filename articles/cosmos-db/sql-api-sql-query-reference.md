---
title: 'Az Azure Cosmos DB: SQL-szintaxis lekérdezés referencia |} A Microsoft Docs'
description: Az Azure Cosmos DB SQL-lekérdezési nyelv dokumentációja.
services: cosmos-db
author: LalithaMV
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: laviswa
ms.openlocfilehash: b8f20bc9e3a46901c88648f89e5b69fda62d8c8f
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990074"
---
# <a name="azure-cosmos-db-sql-syntax-reference"></a>Az Azure Cosmos DB SQL-szintaxis referenciája

Az Azure Cosmos DB támogatja a dokumentumok egy jól ismert SQL (Structured Query Language) lekérdezés nyelvtani például hierarchikus JSON-dokumentumokon végzett explicit séma vagy másodlagos indexek létrehozása nélkül. Ez a témakör az SQL lekérdező nyelve, amely kompatibilis az SQL API-fiókok dokumentációja nyújt.

Az SQL-lekérdezési nyelv leírását lásd: [az Azure Cosmos DB SQL-lekérdezések](sql-api-sql-query.md).  
  
Emellett Felkérjük, hogy látogassa meg a [Query Playground](http://www.documentdb.com/sql/demo) , amelyen az Azure Cosmos DB kipróbálása és az adatkészletet az SQL-lekérdezések futtatásához.  
  
## <a name="select-query"></a>SELECT-lekérdezésben  
Az adatbázisból olvassa be a JSON-dokumentumokat. Kifejezések kiértékelésével, olyan előrejelzéseket, szűrés támogatja, és csatlakozik.  Az ismertető a SELECT utasításokban használt konvencióinak megjelennének a szintaxis konvenciók szakaszban.  
  
**Syntax**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Megjegyzések**  
  
 Tekintse meg a következő szakaszokból megismerheti a részleteket az egyes záradékot:  
  
-   [SELECT záradék](#bk_select_query)  
  
-   [FROM záradékban](#bk_from_clause)  
  
-   [WHERE záradék](#bk_where_clause)  
  
-   [ORDER BY záradék](#bk_orderby_clause)  
  
A SELECT utasítás záradékai kell következniük, ahogy fent látható. A választható záradékot bármelyike elhagyható. De amikor választható záradékot használják, azok kell megjelennie a helyes sorrendben.  
  
**A SELECT utasítás logikai feldolgozási sorrendje**  
  
A rendelés feldolgozása szolgáló szerződéses klauzulák, amelyben a következő:  

1.  [FROM záradékban](#bk_from_clause)  
2.  [WHERE záradék](#bk_where_clause)  
3.  [ORDER BY záradék](#bk_orderby_clause)  
4.  [SELECT záradék](#bk_select_query)  

Vegye figyelembe, hogy ez eltér a megjelenítési sorrendjét a szintaxist. A rendezés van, hogy a feldolgozott záradék által bevezetett új szimbólumok láthatók, és későbbi feldolgozás záradékban használható. Például egy FROM záradékban megadott aliasok érhetők el, ha és a SELECT záradék.  

**Az elválasztó karakterek és megjegyzések**  

Összes szóközt nem részei egy Idézett karakterlánc vagy quoted azonosítója nem részei a nyelvi szintaxis és elemzés során figyelmen kívül hagyja.  

A lekérdezési nyelvet támogatja például a T-SQL stílus megjegyzések  

-   SQL-utasítás `-- comment text [newline]`  

Az elválasztó karakterek és a megjegyzések nincs semmilyen jelentőséggel a gramatice, amíg azok jogkivonatok külön kell használható. Például: `-1e5` egy egyetlen szám token, ideje van`: – 1 e5` egy mínusz tokent követi számának 1 és azonosító e5.  

##  <a name="bk_select_query"></a> SELECT záradék  
A SELECT utasítás záradékai kell következniük, ahogy fent látható. A választható záradékot bármelyike elhagyható. De amikor választható záradékot használják, azok kell megjelennie a helyes sorrendben.  

**Syntax**  
```  
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentumok**  
  
 `<select_specification>`  
  
 Tulajdonságok vagy ki kell választania a az eredményhalmaz érték.  
  
 `'*'`  
  
Itt adhatja meg, hogy az érték legyen beolvasva módosítása nélkül. Kifejezetten a feldolgozott értéke egy objektumot, ha a rendszer lekéri az összes tulajdonság.  
  
 `<object_property_list>`  
  
Meghatározza a lekérdezni kívánt tulajdonságok listája. Minden egyes visszaküldött érték a megadott tulajdonságokkal rendelkező objektum lesz.  
  
`VALUE`  
  
Itt adhatja meg, hogy a JSON értéke legyen beolvasva a teljes JSON-objektum helyett. Ezzel ellentétben `<property_list>` nem wrap funkciót az előre jelzett érték egy objektumot.  
  
`<scalar_expression>`  
  
A kifejezés a következő időpontban számítja értéket jelölő. Lásd: [skaláris kifejezések](#bk_scalar_expressions) című szakasz részletezi.  
  
**Megjegyzések**  
  
A `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradékban van deklarálva pontosan egy aliast. `SELECT *` Itt egy identitás leképezése, amely lehet hasznos, ha nincs leképezés van szükség. Válassza ki * csak akkor érvényes, ha a FROM záradék van megadva, és csak egyetlen bemeneti forrás bevezetni.  
  
Vegye figyelembe, hogy `SELECT <select_list>` és `SELECT *` "szintaktikai cukor", és azt is megteheti egyszerű SELECT utasítás használatával, ahogy az alábbi jelöl.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     a következő azonos:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     a következő azonos:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Lásd még:**  
  
[Skaláris kifejezések](#bk_scalar_expressions)  
[SELECT záradék](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM záradékban  
Itt adhatja meg, a forrás vagy a csatlakoztatott források. A FROM záradék használata nem kötelező. Ha nem a megadott, más záradékok továbbra is hajtható végre, mint ha a FROM záradékban megadott egyetlen dokumentum.  
  
**Syntax**  
  
```  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <collection_expression> [[AS] input_alias]  
        | input_alias IN <collection_expression>  
  
<collection_expression> ::=   
        ROOT   
     | collection_name  
     | input_alias  
     | <collection_expression> '.' property_name  
     | <collection_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentumok**  
  
`<from_source>`  
  
Egy adatforrás, itt adhatja meg, vagy az alias nélkül. Ha nincs megadva alias, azt fogja nelze odvodit z a `<collection_expression>` szabályok a következő használatával:  
  
-   Ha a kifejezés egy Lekérdezésnév, majd a lekérdezésnév használható aliasként.  
  
-   Ha a kifejezés `<collection_expression>`, akkor property_name, majd property_name használja a rendszer aliasként. Ha a kifejezés egy Lekérdezésnév, majd a lekérdezésnév használható aliasként.  
  
AS `input_alias`  
  
Megadja, hogy a `input_alias` van az alapul szolgáló gyűjtemény kifejezés által visszaadott értékek egy halmazát.  
 
`input_alias` IN  
  
Megadja, hogy a `input_alias` kell kívánt csoportjának megfelelő értékeket a léptetés keresztül minden egyes az alapul szolgáló gyűjtemény kifejezés által visszaadott tömb összes tömbelemek rekordsémáját. Alapul szolgáló gyűjtemény kifejezés, amely nem tömb által visszaadott értéket a rendszer figyelmen kívül hagyja.  
  
`<collection_expression>`  
  
Meghatározza azt a gyűjteményt, lekérheti a dokumentumokat.  
  
`ROOT`  
  
Megadja, hogy a dokumentum olvassa be az alapértelmezett beállítás, a jelenleg csatlakoztatott gyűjtemény.  
  
`collection_name`  
  
Megadja, hogy a dokumentum be kell olvasni a megadott gyűjteményből. A gyűjtemény nevét meg kell egyeznie a gyűjtemény nevét, jelenleg kapcsolódik.  
  
`input_alias`  
  
Megadja, hogy a dokumentum határozzák meg a megadott alias a más forrásból kell olvasni.  
  
`<collection_expression> '.' property_`  
  
Itt adhatja meg, a dokumentum elérésével legyen beolvasva a `property_name` vlastnost nebo tömbindex tömbelem az lekéri a dokumentumok a megadott gyűjtemény kifejezés.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Itt adhatja meg, a dokumentum elérésével legyen beolvasva a `property_name` vlastnost nebo tömbindex tömbelem az lekéri a dokumentumok a megadott gyűjtemény kifejezés.  
  
**Megjegyzések**  
  
Összes alias következtetni vagy megadott a `<from_source>(`s) egyedinek kell lennie. A szintaxist `<collection_expression>.`property_name megegyezik a `<collection_expression>' ['"property_name"']'`. Az utóbbi szintaxis azonban használható, ha egy tulajdonság nevében egy nem azonosító karaktereket tartalmaz.  
  
**Hiányzó tulajdonságok, hiányzik a tömb elemei, nem definiált értékek kezelése**  
  
Ha egy gyűjtemény kifejezés fér hozzá, tulajdonságok vagy a tömb elemeinek és, hogy az érték nem létezik, ezt az értéket figyelmen kívül hagyja, és további nincs feldolgozva.  
  
**Gyűjtemény kifejezés környezeti hatókör**  
  
Egy gyűjtemény kifejezés gyűjtemény hatókörű vagy dokumentum hatókörű lehetnek:  
  
-   Egy kifejezés a gyűjtemény hatóköre, ha a gyűjtemény kifejezés az alapul szolgáló forrás vagy a legfelső szintű vagy `collection_name`. Az ilyen kifejezés dokumentumokat közvetlenül a gyűjteményből beolvasni egy halmazát jelölik, és nem függ más gyűjtemény-kifejezések feldolgozása.  
  
-   Egy kifejezés, dokumentum-hatáskörű, ha az alapul szolgáló adatforrás a gyűjtemény kifejezés `input_alias` jelent meg a korábban a lekérdezésben. Például egy kifejezést a fióknévnek gyűjteményhez társított csoporthoz tartozó egyes dokumentumok hatókörében a gyűjtemény kifejezés kiértékelése kapott dokumentumokat egy halmazát jelölik.  Eredő beállítása során az egyes dokumentumok az alapul szolgáló készletben a gyűjtemény kifejezés által beszerzett egy Uniója fogja.  
  
**Illesztés**  
  
A jelenlegi kiadásban az Azure Cosmos DB támogatja a belső illesztések. További illesztési képességek érkeznek.

Belső illesztések egy teljes körű a részt vesz a join készlet keresztszorzatát eredményez. Az N-módon való csatlakozás eredménye egy készletét N-elem rekordokat, ahol a rekord minden egyes érték társítva az aliasnevet a JOIN beállítása a résztvevő, és ez az alias más záradékban való hivatkozással érhető el.  
  
A kiértékelés a JOIN attól függ, hogy a környezet hatókörét a programban részt vevő készletek:  
  
-  Illesztés között A gyűjteménykészlet és a gyűjtemény hatóköre B, több termék eredményezi, beállítja a és b szereplő összes elem beállítása
  
-   A készlet és B, set dokumentum hatókörű közötti csatlakozzon kapott értékelése az egyes dokumentumok B beállítása. a dokumentum hatókörű beállított összes adatkészletek union eredményez.  
  
 A jelenlegi kiadásban egy gyűjtemény hatókörébe tartozó kifejezés legfeljebb a lekérdezés-feldolgozó által támogatott.  
  
**Példák illesztések:**  
  
Nézzük meg, a FROM záradék a következő: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Lehetővé teszik az egyes forrás megadása `input_alias1, input_alias2, …, input_aliasN`. A FROM záradék az N-rekordokat tartalmazó (N értékekkel rendelkező rekordot) adja vissza. Minden egyes rekord összes gyűjtemény alias léptetés keresztül az adott csoportok által előállított értékkel rendelkezik.  
  
*Példa 1-2 adatforrásokhoz CSATLAKOZHAT:*  
  
- Lehetővé teszik `<from_source1>` gyűjtemény-hatóköre beállítható, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source2>` dokumentum hatókörű input_alias1 hivatkozik, és csoportok képviselik:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2>` a következő rekordok felsorolásának eredményez:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*Példa 2-3 adatforrásokhoz CSATLAKOZHAT:*  
  
- Lehetővé teszik `<from_source1>` gyűjtemény-hatóköre beállítható, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source2>` kell hivatkozik a dokumentum-hatáskörű `input_alias1` és -mappáknak a csoportok tartalmazzák:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- Lehetővé teszik `<from_source3>` kell hivatkozik a dokumentum-hatáskörű `input_alias2` és -mappáknak a csoportok tartalmazzák:  
  
    {100, 200-as} számára `input_alias2 = 1,`  
  
    {300} a `input_alias2 = 3,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok felsorolásának eredményez:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200-AS), (B, 3, 300)  
  
> [!NOTE]
> Hiánya más értékkel rekordokat tartalmazó `input_alias1`, `input_alias2`, amelyhez a `<from_source3>` nem adott vissza semmilyen értéket.  
  
*Például: 3, 3 forrásokkal CSATLAKOZZON:*  
  
- Tudassa < from_source1 > kell a gyűjtemény hatóköre, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source1>` gyűjtemény-hatóköre beállítható, és a set {A, B, C} képviseli.  
  
- < From_source2 > hivatkozó input_alias1 dokumentum hatókörű, és képviselik a készletek lehetővé teszik:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- Lehetővé teszik `<from_source3>` tartozni `input_alias1` és -mappáknak a csoportok tartalmazzák:  
  
    {100, 200-as} számára `input_alias2 = A,`  
  
    {300} a `input_alias2 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordok felsorolásának eredményez:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200-AS) (A, 2, 100), (A, 2, 200-AS), C, 4, 300, (C, 5, 300)  
  
> [!NOTE]
> Ennek következtében több termék között `<from_source2>` és `<from_source3>` mindkét hatóköre ugyanaz, mert `<from_source1>`.  Ennek következtében a 4 (2 x 2) a érték kellene kellene B (1 x 0) értéket 0 rekordokat tartalmazó rekordok. és 2 (2 x 1) rekordokat tartalmazó c-értékkel  
  
**Lásd még:**  
  
 [SELECT záradék](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> WHERE záradék  
 Adja meg a keresési feltétel, a lekérdezés által visszaadott dokumentumok számára.  
  
 **Syntax**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentumok**  
  
-   `<filter_condition>`  
  
     Itt adhatja meg az állapotot, a dokumentumok vissza kell teljesülniük.  
  
-   `<scalar_expression>`  
  
     A kifejezés a következő időpontban számítja értéket jelölő. Tekintse meg a [skaláris kifejezések](#bk_scalar_expressions) című szakasz részletezi.  
  
 **Megjegyzések**  
  
 Ahhoz, hogy a dokumentum egy kifejezést a megadott kifejezés adja vissza a feltétel igaz értéket kell adnia. Csak az IGAZ logikai értéket eleget tesz a feltételt, semmilyen más érték: nem meghatározott, NULL értékű, false, szám, tömböt vagy objektumot nem teljesítik a feltételt.  
  
##  <a name="bk_orderby_clause"></a> ORDER BY záradék  
 Megadja a rendezési sorrendjét a lekérdezés által visszaadott eredményeket.  
  
 **Syntax**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentumok**  
  
-   `<sort_specification>`  
  
     Megadja egy tulajdonságot vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés. A rendezési oszlop-név vagy oszlopban alias adható meg.  
  
     Több rendezési oszlop adható meg. Nevének egyedinek kell lennie. Az ORDER BY záradékban szereplő oszlopok rendezése sorrendje határozza meg, hogy a szervezet az rendezett eredményhalmaz. Azt jelenti az eredményhalmaz az első tulajdonság szerint van rendezve, és ezután a rendezett lista van rendezve, a második tulajdonságot, és így tovább.  
  
     Az ORDER BY záradékban hivatkozott oszlop nevét meg kell felelnie a kiválasztási listán vagy egy oszlop vagy egy oszlop definiálva a bármely kétértelműséget nélkül a FROM záradékban megadott táblában.  
  
-   `<sort_expression>`  
  
     Megadja az egyetlen tulajdonságát vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés.  
  
-   `<scalar_expression>`  
  
     Tekintse meg a [skaláris kifejezések](#bk_scalar_expressions) című szakasz részletezi.  
  
-   `ASC | DESC`  
  
     Itt adhatja meg, hogy a megadott oszlopban szereplő értékek növekvő vagy csökkenő sorrendben jelennek meg. ASC rendezi a legkisebb érték a legmagasabb érték. DESC rendezi a legmagasabb érték a legkisebb értéket. ASC az alapértelmezett rendezési sorrend. A legkisebb lehetséges értékek NULL értéket kell kezelni.  
  
 **Megjegyzések**  
  
 A lekérdezési szintaxis támogatja a több sorrend tulajdonságai, amíg az Azure Cosmos DB lekérdezési modul támogatja a rendezés csak egyetlen tulajdonság, és csak elleni tulajdonságneveket, azaz a nem számított tulajdonságokhoz. Rendezés is megköveteli, hogy az indexelési házirendet egy tulajdonság és a megadott típus, a maximális pontosság tartományindexszel tartalmazza-e. Tekintse meg a további részletekért indexelési házirend dokumentációját.  
  
##  <a name="bk_scalar_expressions"></a> Skaláris kifejezések  
 Egy skaláris kifejezés szimbólumok és egyetlen értéket beszerzése kiértékelése operátorok kombinációja. Egyszerű kifejezések állandók, tulajdonság hivatkozik, tömb elem hivatkozásokat, alias hivatkozik, vagy lehet függvényhívások. Egyszerű kifejezések operátorok használatával összetett kifejezések egyesíthetők.  
  
 Előfordulhat, hogy mely skaláris kifejezés értékeket a részletekért lásd: [állandók](#bk_constants) szakaszban.  
  
 **Syntax**  
  
```  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **Argumentumok**  
  
-   `<constant>`  
  
     Egy állandó értéket jelöli. Lásd: [állandók](#bk_constants) című szakasz részletezi.  
  
-   `input_alias`  
  
     Egy által definiált értéket képvisel a `input_alias` rendszerben bevezetett a `FROM` záradékban.  
    Ez az érték nem lehet garantáltan **nem definiált** –**nem definiált** a rendszer kihagyja a bemeneti értékeket.  
  
-   `<scalar_expression>.property_name`  
  
     A tulajdonság az objektum olyan értékét jelöli. Ha a tulajdonság nem létezik vagy tulajdonság egy értéket, amely nem objektum hivatkozik, akkor a kifejezés eredménye **nem definiált** értéket.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     Egy nevű tulajdonság értékét jelöli `property_name` vagy az index tömbelem `array_index` egy objektum vagy tömb. Ha a tulajdonság/tömbindex nem létezik, vagy egy érték, amely nem objektum vagy tömb a tulajdonság/tömbindex hivatkozott, majd a kifejezés eredménye nem definiált értéket.  
  
-   `unary_operator <scalar_expression>`  
  
     Az operátor alkalmazott egyetlen értéket jelöli. Lásd: [operátorok](#bk_operators) című szakasz részletezi.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Az operátor alkalmazott két értéket jelöli. Lásd: [operátorok](#bk_operators) című szakasz részletezi.  
  
-   `<scalar_function_expression>`  
  
     Egy adott hívás eredménye által definiált értéket jelöli.  
  
-   `udf_scalar_function`  
  
     A felhasználó neve meg van adva skaláris függvény.  
  
-   `builtin_scalar_function`  
  
     A beépített skaláris függvény nevét.  
  
-   `<create_object_expression>`  
  
     Egy új objektumot hoz létre a megadott tulajdonságokkal kapott értéket és az értéküket jelöli.  
  
-   `<create_array_expression>`  
  
     Hozzon létre egy új tömbben elemként megadott értékekkel beszerzett értéket jelöli.  
  
-   `parameter_name`  
  
     A megadott paraméternév egy értékét jelöli. Paraméter neve egyetlen @ rendelkeznie kell az első karakter.  
  
 **Megjegyzések**  
  
 Amikor skalárfüggvény hívása egy beépített vagy felhasználó megadott összes argumentumot meg kell határozni. Ha bármelyik argumentum nem definiált, a függvény nem hívható, és az eredmény az lesz meghatározva.  
  
 Objektum létrehozásakor bármely vlastnost, amelyek nem definiált értéket kapja kihagyva, és nem tartalmazza a létrehozott objektum.  
  
 Ha egy tömb, bármely elem érték létrehozása, amely hozzá van rendelve **nem definiált** érték lesz kihagyva és nem tartalmazza a létrehozott objektum. Ennek hatására úgy, hogy a létrehozott tömb nem rendelkezik kihagyja az indexek a sor a következő meghatározott elemhez.  
  
##  <a name="bk_operators"></a> Operátorok  
 Ez a szakasz ismerteti a támogatott. Minden operátor pontosan egy kategória is hozzárendelhető.  
  
 Lásd: **operátor kategóriák** részleteiről az alábbi táblázat kezelésére vonatkozó **nem definiált** értékek, a bemeneti értékek és kezelését a nem egyező típusú értékeket adattípus-követelményekkel.  
  
 **Operátor kategóriák:**  
  
|**Kategória**|**Részletek**|  
|-|-|  
|**Aritmetikai**|Operátor input(s) szám(ok) kell megadni. Kimeneti is az a szám. Ha a bemeneti adatok bármelyike **nem definiált** típusa eltérő számú majd az eredményt vagy **nem definiált**.|  
|**Bitenkénti**|Operátor kell a 32 bites előjeles egész számokat szám(ok) input(s) vár. Kimenet is a 32 bites, előjeles egész szám.<br /><br /> Nem egész értéket a rendszer kerekíti. Pozitív értéket lefelé, az értéket felfelé kerekíti a negatív.<br /><br /> Bármilyen érték, amely a 32 bites egész tartományon kívül esik a legutóbbi 32-bit a két cég hexadecimális végrehajtásával konvertálja.<br /><br /> Ha a bemeneti adatok bármelyike **nem definiált** , vagy írjon be más, mint a szám, akkor az eredmény **nem definiált**.<br /><br /> **Megjegyzés:** a fenti viselkedése összeegyeztethető JavaScript bitenkénti operátor viselkedését.|  
|**Logikai**|Operátor input(s) Boolean(s) kell megadni. Kimeneti akkor is egy logikai érték.<br />Ha a bemeneti adatok bármelyike **nem definiált** , vagy írjon be másik logikai értéket, majd eredményül **nem definiált**.|  
|**Összehasonlítás**|Operátor azonos típusú, és nem lehet a nem definiált input(s) vár. A kimenete egy logikai érték.<br /><br /> Ha a bemeneti adatok bármelyike **nem definiált** vagy a bemeneti adatok különböző rendelkezik, majd az eredmény **nem definiált**.<br /><br /> Lásd: **összehasonlított értékek rendezés** érték rendezése részletei táblázatban.|  
|**string**|Operátor input(s) vágóelem kell megadni. Kimenet: karakterlánc.<br />Ha a bemeneti adatok bármelyike **nem definiált** , vagy írjon be másik karakterláncot, majd az eredmény **nem definiált**.|  
  
 **Az egyoperandusú operátorral:**  
  
|**Name (Név)**|**Operátor**|**Részletek**|  
|-|-|-|  
|**Aritmetikai**|+<br /><br /> -|A szám értéket ad vissza.<br /><br /> Bitenkénti negálást. Szám értéket ad vissza negated.|  
|**Bitenkénti**|~|Kiépítettektől kiegészítése. Egy halmaza egy számértéket ad vissza.|  
|**Logical**|**NEM**|Negálás. Értéket ad vissza negated logikai érték.|  
  
 **Binární operátory:**  
  
|**Name (Név)**|**Operátor**|**Részletek**|  
|-|-|-|  
|**Aritmetikai**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Emellett.<br /><br /> Kivonás.<br /><br /> Szorzás.<br /><br /> Osztás.<br /><br /> Modulációs.|  
|**Bitenkénti**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitenkénti vagy.<br /><br /> Bitenkénti és művelet<br /><br /> Bitenkénti kizáró vagy.<br /><br /> Balra Tolást.<br /><br /> Jobbra Tolást.<br /><br /> Jobbra Tolást nulla ki.|  
|**Logikai**|**ÉS**<br /><br /> **VAGY**|Logikai és. Adja vissza **igaz** Ha mindkét argumentum **igaz**, adja vissza **hamis** más módon.<br /><br /> Logikai és. Adja vissza **igaz** Ha mindkét argumentum **igaz**, adja vissza **hamis** más módon.|  
|**Összehasonlítás**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Egyenlő. Adja vissza **igaz** , ha argumentumok egyenlő adja vissza **hamis** más módon.<br /><br /> Nem egyenlő. Adja vissza **igaz** argumentumok, amelyek nem egyeznek, ha függvény **hamis** ellenkező esetben.<br /><br /> Nagyobb, mint. Értéket ad vissza **igaz** első argumentum értéke nagyobb, mint a második, ha vissza **hamis** más módon.<br /><br /> Nagyobb vagy egyenlő. Értéket ad vissza **igaz** első argumentum értéke kisebb a másodiknál, ha vissza **hamis** más módon.<br /><br /> Kevesebb mint. Értéket ad vissza **igaz** Ha az első argumentum értéke kisebb, mint a második egy visszatérési **hamis** más módon.<br /><br /> Kisebb vagy egyenlő. Értéket ad vissza **igaz** első argumentum értéke kisebb vagy egyenlő, mint a második érték, ha vissza **hamis** más módon.<br /><br /> Coalesce. A második argumentum adja vissza, ha az első argumentum értéke egy **nem definiált** értéket.|  
|**Karakterlánc**|**&#124;&#124;**|Összefűzés. Összefűzésével mindkét argumentumot adja vissza.|  
  
 **Ternáris kezelők:**  
  
|Ternáris operátor|?|A második argumentum adja vissza, ha az első argumentum értéke **igaz**; ellenkező esetben adja vissza a harmadik argumentum.|  
|-|-|-|  
  
 **Az összehasonlított értékek rendezése**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Nincs definiálva**|Nem összehasonlítható.|  
|**NULL**|Egyetlen érték: **null értékű**|  
|**Szám**|Természetes valós szám.<br /><br /> Negatív végtelent értéke kisebb, mint bármely más érték szám.<br /><br /> Pozitív végtelen érték nagyobb, mint bármely más érték szám. **NaN** értéke nem összehasonlítható. Összehasonlítás a **NaN** eredményez **nem definiált** értéket.|  
|**Karakterlánc**|Lexicographical sorrend.|  
|**Pole**|Nincs rendezése, de méltányos.|  
|**Object**|Nincs rendezése, de méltányos.|  
  
 **Megjegyzések**  
  
 Az Azure Cosmos DB a típusú értékek gyakran nem ismert csak a ténylegesen az adatbázisból lekért. Annak érdekében, hogy a hatékony lekérdezések végrehajtása támogatja, az operátorok a legtöbb szigorú adattípus-követelményekkel rendelkeznek. Operátorok önmagukban is hajt végre implicit konverzió.  
  
 Ez azt jelenti, hogy a lekérdezés, például: válassza ki * a legfelső szintű r WHERE r.Age = 21 csak visszatér kora tulajdonsággal rendelkező dokumentumok egyezik a 21. Dokumentumok kora egyenlő a karakterlánc "21" vagy "0021" karakterlánc tulajdonsággal nem egyeznek meg, mint a "21" kifejezés = 21 kiértékeli a nem meghatározott. Ez lehetővé teszi egy jobban használható az indexek, mert az egy adott érték-keresés (azaz számú 21) gyorsabb, mint a lehetséges egyezések (21 szám vagy karakterláncok "21", "021", "21.0" …) határozatlan számú keresése. Ez eltér a hogyan értékeli ki a JavaScript a kezelők az eltérő típusú értékeket.  
  
 **Tömbök és objektumok egyenlőség és összehasonlítása**  
  
 A tartomány operátorral egymáshoz csatolt tömböt vagy objektumot értékek összehasonlítása (>, > =, <, < =) nincs definiálva a nem meghatározott objektum vagy tömb értékek sorrendje nem fog eredményezni. Azonban az egyenlőség operátorok használata (=,! =, <>) támogatott és az értékek szerkezetileg lesz összehasonlítva.  
  
 Tömbök, egyenlő, ha mindkét Pole mají azonos számú elemet, és megfelelő pozíciók található elemekben is egyenlő. Ha nincs definiálva a bármely virtuálisgép-pár elemek eredmények összehasonlítása, a tömb összehasonlítás eredménye nincs meghatározva.  
  
 Objektumok egyenlőek, ha mindkét objektum meghatározott azonos tulajdonságokkal rendelkezik, és megfelelő tulajdonságok értékei a következők is egyenlő. Ha nincs definiálva a bármely vlastnost értékeket eredményez párjai összehasonlítása, az objektum összehasonlítás eredménye nincs meghatározva.  
  
##  <a name="bk_constants"></a> Állandók  
 Egy állandó, más néven szövegkonstans vagy skaláris, egy meghatározott értéket jelölő szimbólumot. Egy állandó formátumát az érték azt jelenti, hogy függ.  
  
 **Skaláris adattípusokat támogatja:**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Nincs definiálva**|Egyetlen érték: **nem definiált**|  
|**NULL**|Egyetlen érték: **null értékű**|  
|**Logikai érték**|Értékek: **hamis**, **igaz**.|  
|**Szám**|Egy dupla pontosságú lebegőpontos számnál, szabvány IEEE 754.|  
|**Karakterlánc**|Nulla vagy több Unicode karakter sorozata. Karakterláncok egyszeres vagy kettős idézőjelek közé kell tenni.|  
|**Pole**|Nulla vagy több elemek sorrendjét. Egyes elemek nincs megadva kivételével minden skaláris adattípusú érték lehet.|  
|**Object**|Egy nulla vagy több név/érték párok rendezetlen készlete. Név egy Unicode karakterlánc, kivéve értéke lehet bármely skaláris adattípusú **Undefined**.|  
  
 **Syntax**  
  
```  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **Argumentumok**  
  
1.  `<undefined_constant>; undefined`  
  
     Nincs megadva típusú érték nem definiált jelöli.  
  
2.  `<null_constant>; null`  
  
     Jelöli **null** típusú érték **Null**.  
  
3.  `<boolean_constant>`  
  
     Logikai érték típusú konstans jelöli.  
  
4.  `false`  
  
     Jelöli **hamis** logikai típusú érték.  
  
5.  `true`  
  
     Jelöli **igaz** logikai típusú érték.  
  
6.  `<number_constant>`  
  
     Egy állandó jelöli.  
  
7.  `decimal_literal`  
  
     Tizedes tört literálok lehetnek olyan szám vagy decimális jelölés, vagy tudományos jelölés használatával.  
  
8.  `hexadecimal_literal`  
  
     Hexadecimális literálok előtagja '0 x' legalább egy hexadecimális számjegy követ használatával értékek, amelyek.  
  
9. `<string_constant>`  
  
     Egy karakterlánc típusú konstans jelöli.  
  
10. `string _literal`  
  
     Karakterlánc-literálnak sorozata, nulla vagy több Unicode-karaktereket vagy escape-karaktersorozatokat által képviselt Unicode karakterláncokat is. Karakterlánc-literálnak aposztrófok közé kell tenni (aposztróf: ") vagy dupla idézőjel (idézőjel:").  
  
 Következő escape-karaktersorozatokat engedélyezettek:  
  
|**Escape-szekvencia**|**Leírás**|**Unicode-karakter**|  
|-|-|-|  
|\\'|aposztróf (')|U+0027|  
|\\"|idézőjel (")|U+0022|  
|\\\|fordított solidus (\\)|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|vissza|U+0008|  
|\f|Lapdobás|U+000C|  
|\n|Sortörés|U+000A|  
|\r|kocsivissza|U+000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Egy 4 hexadecimális számjegy által meghatározott Unicode-karakter.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Lekérdezési teljesítmény irányelvek  
 Ahhoz, hogy egy lekérdezés gyűjteménye hatékonyan végrehajtható, a szűrőket, amelyek egy vagy több index keresztül kiszolgálható kell használnia.  
  
 A keresési index akkor veszi figyelembe a következő szűrőket:  
  
-   Egyenlő operátor (=) használata egy dokumentum elérési út és a egy konstans.  
  
-   Tartomány-operátor használható (<, \<=, >, > =) egy dokumentum elérési út és a szám állandó.  
  
-   A dokumentum elérési út bármely kifejezés, amely azonosítja a dokumentumok a hivatkozott adatbázis-gyűjtemény egy állandó elérési jelöli.  
  
 **A dokumentum az elérésiút-kifejezés**  
  
 A dokumentum elérésiút-kifejezések kifejezések, amelyek elérési útvonalát vlastnost nebo Pole indexelő assessors egy dokumentum-adatbázis-gyűjtemény dokumentumok érkező keresztül is. Az elérési út helyét, közvetlenül az adatbázis-gyűjteményben a dokumentumok belül szűrő hivatkozott értékek azonosításához használható.  
  
 Figyelembe kell venni egy dokumentum elérési út kifejezése kifejezés akkor meg:  
  
1.  A gyűjtemény legfelső szintű közvetlenül hivatkozni.  
  
2.  Hivatkozás tulajdonság vagy állandó tömb indexelő néhány dokumentum elérési útját kifejezés  
  
3.  Alias, néhány dokumentum elérési út kifejezése jelölő hivatkozhat.  
  
     **Szintaxis konvenciók**  
  
     A következő táblázat ismerteti a konvenciók leírása a szintaxisa a következő SQL-hivatkozás.  
  
    |**Volání**|**Használt**|  
    |-|-|    
    |NAGYBETŰK|Kis-és a kulcsszavak.|  
    |Kisbetűk|A kulcsszavak kis-és nagybetűket.|  
    |\<nonterminal >|Nem, külön-külön definiált.|  
    |\<nonterminal >:: =|A nonterminal szintaxis meghatározása.|  
    |other_terminal|Terminálszolgáltatások (token), a szavakat részletes leírása.|  
    |azonosító|Azonosítója. Lehetővé teszi, hogy csak a karaktereket a következő: a – z A – Z 0 – 9 _First karakter nem lehet egy számjegy.|  
    |"string"|Határolójeles karakterlánc. Lehetővé teszi bármely érvényes karakterláncot. Tekintse meg a string_literal leírását.|  
    |"szimbólum"|A szintaxist részét képező szövegkonstans szimbólum.|  
    |&#124;(függőleges vonal)|Alternatívák szintaktikai elemek. Csak a megadott elemek egyikét használhatja.|  
    |[] /(brackets)|Zárójelek közé egy vagy több választható elemet.|  
    |[,.. .n]|Azt jelzi, hogy az előző elemet is lehet az ismétlődő n számú alkalommal. Az előfordulások vesszővel kell elválasztani.|  
    |[.. .n]|Azt jelzi, hogy az előző elemet is lehet az ismétlődő n számú alkalommal. Az előfordulások üres vannak elválasztva.|  
  
##  <a name="bk_built_in_functions"></a> Beépített függvények  
 Az Azure Cosmos DB számos beépített SQL-funkciókat biztosít. A beépített függvénykategóriákat alább láthatók.  
  
|Függvény|Leírás|  
|--------------|-----------------|  
|[Matematikai függvények](#bk_mathematical_functions)|A matematika függvényekkel végezhet a számítást, általában argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.|  
|[Funkciók ellenőrzése típusa](#bk_type_checking_functions)|A típus ellenőrzése funkciók lehetővé teszik az SQL-lekérdezések belül egy kifejezés típusának ellenőrzése.|  
|[Sztringfüggvények](#bk_string_functions)|A karakterlánc-függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza.|  
|[Tömb funkciók](#bk_array_functions)|A tömb funkciók végrehajtása egy logikai érték, vagy a tömb érték, egy tömb bemeneti érték és a visszaadandó numerikus művelet.|  
|[Térbeli funkciók](#bk_spatial_functions)|A térbeli funkciók végrehajtania egy műveletet egy térbeli objektum bemeneti érték a, és a egy numerikus vagy logikai értéket adja vissza.|  
  
###  <a name="bk_mathematical_functions"></a> Matematikai függvények  
 A következő feladatokat végezhet a számítást, általában argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[FELSŐ HATÁR](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[EMELET](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[KEREKÍTÉS](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[NÉGYSZÖG](#bk_square)|[SIGN](#bk_sign)|  
|[BARACKSZÍNŰ](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza.  
  
 **Syntax**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa három eltérő számú ABS funkciójával eredményeket mutatja.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Adja vissza a szög radiánban, amelynek a koszinusza a megadott numerikus kifejezés; egy szám arkusz koszinusza néven is ismert.  
  
 **Syntax**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa az ACOS,-1 értéket adja vissza.  
  
```  
SELECT ACOS(-1)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Adja vissza a szög radiánban, amelynek szinusza a megadott numerikus kifejezés. Arkuszszinusz ezt is nevezik.  
  
 **Syntax**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példában a-1 ASIN adja vissza.  
  
```  
SELECT ASIN(-1)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Adja vissza a szög radiánban, amelynek tangense a megadott numerikus kifejezés. Arkusztangens ezt is nevezik.  
  
 **Syntax**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa a ATAN, a megadott értéket adja vissza.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Az arkusz tangens / x, y radiánban kifejezett fő értékét adja vissza.  
  
 **Syntax**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa alapján számítja ki a ATN2 a megadott x és y összetevőket.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> FELSŐ HATÁR  
 A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés.  
  
 **Syntax**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja a pozitív szám, negatív és nulla érték a függvény a felső HATÁRT.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 A megadott kifejezést az radiánban megadott szög, trigonometriai koszinuszát adja vissza.  
  
 **Syntax**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa a megadott szög COS számítja ki.  
  
```  
SELECT COS(14.78)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 A megadott szög trigonometriai kotangensét adja vissza radiánban, a megadott numerikus kifejezés.  
  
 **Syntax**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa alapján számítja ki a COT a megadott szög.  
  
```  
SELECT COT(124.1332)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> FOK  
 A megfelelő szöget adja vissza fokban a egy radiánban megadott szög.  
  
 **Syntax**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példában a-PI/2 radián szöge fok számát adja vissza.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a> EMELET  
 Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott numerikus kifejezés.  
  
 **Syntax**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja a pozitív szám, negatív és nulla érték az EMELET funkcióval.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Az exponenciális a megadott numerikus kifejezés értékét adja vissza.  
  
 **Syntax**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Megjegyzések**  
  
 Az állandó **e** (2.718281...), a természetes logaritmusokat alapját.  
  
 A kitevő, egy szám az állandó **e** hatványára szám. Például EXP(1.0) = e ^ 1.0-s = 2.71828182845905 és EXP(10) = e ^ 10 = 22026.4657948067.  
  
 Az exponenciális egy szám természetes alapú logaritmus alapja az a szám maga: EXP (napló (n)) = n. Az exponenciális egy szám természetes algoritmusát az a szám, és maga: LOG (EXP (n)) = n.  
  
 **Példák**  
  
 Az alábbi példa egy változó deklarálja, és a megadott változó (10) exponenciális értékét adja vissza.  
  
```  
SELECT EXP(10)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 Az alábbi példa a natural logarithm legfeljebb 20-at és a természetes alapú logaritmusát az exponenciális 20 exponenciális értékét adja vissza. Mivel ezek a függvények inverz függvények, egy másik, a visszatérési kerekítési lebegőpontos értékek mindkét esetben az értéke 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a> NAPLÓ  
 A megadott numerikus kifejezés természetes alapú logaritmusát adja vissza.  
  
 **Syntax**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
-   `base`  
  
     Olyan nem kötelező numerikus argumentum, amely beállítja a logaritmus alapjának a.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Megjegyzések**  
  
 Alapértelmezés szerint LOG() a természetes alapú logaritmusát adja vissza. A logaritmus alapja a választható alap paraméter használatával módosíthatja egy másik értéket.  
  
 A természetes alapú logaritmus alapja az alap-es alapú logaritmusa **e**, ahol **e** megegyezik egy irrational állandó körülbelül 2.718281828.  
  
 Az exponenciális egy szám természetes algoritmusát az a szám maga: LOG (EXP (n)) = n. Az exponenciális egy szám természetes alapú logaritmus alapja az a szám, és maga: EXP (napló (n)) = n.  
  
 **Példák**  
  
 Az alábbi példa egy változó deklarálja, és a megadott változó (10) alapú logaritmus értékét adja vissza.  
  
```  
SELECT LOG(10)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 Az alábbi példa a naplófájl a szám az exponens számítja ki.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 A megadott numerikus kifejezés 10-es alapú logaritmusát adja vissza.  
  
 **Syntax**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Megjegyzések**  
  
 A LOG10 és a POWER függvény intenzitásfokozatok kapcsolódnak egymáshoz. Ha például 10 ^ LOG10(n) = n.  
  
 **Példák**  
  
 Az alábbi példa egy változó deklarálja, és a megadott változó (100) LOG10 értékét adja vissza.  
  
```  
SELECT LOG10(100)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Az állandó a PI értékét adja vissza.  
  
 **Syntax**  
  
```  
PI ()  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa a PI értékét adja vissza.  
  
```  
SELECT PI()  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> ENERGIAGAZDÁLKODÁSI  
 A megadott hatványra a megadott kifejezés értékét adja vissza.  
  
 **Syntax**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
-   `y`  
  
     A teljesítmény, amelyhez emelése `numeric_expression`.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa bemutatja egy számot (a szám adatkockájának) 3 hatékonyságát előléptetése.  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Adja vissza radiánban, ha egy numerikus kifejezés, fokban is meg kell adni.  
  
 **Syntax**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa néhány szögek fogadja bemeneti adatként, és a hozzájuk tartozó radián értékeket ad vissza.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> KEREKÍTÉS  
 Egy numerikus értéket, kerekítve a legközelebbi egész értéket ad vissza.  
  
 **Syntax**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa a következő pozitív és negatív számokat a legközelebbi egész számra kerekít.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a> BEJELENTKEZÉS  
 A pozitív (+ 1), a nulla (0) vagy a megadott numerikus kifejezés mínuszjel (-1) adja vissza.  
  
 **Syntax**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa értékeit adja vissza a bejelentkezési számok, -2 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 A megadott kifejezést az radiánban megadott szög, trigonometriai szinuszát adja vissza.  
  
 **Syntax**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa a a megadott szög Szinusz számítja ki.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 A megadott numerikus érték négyzetgyökét adja vissza.  
  
 **Syntax**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa 1 – 3. számok négyzetgyökét adja vissza.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> NÉGYSZÖG  
 A megadott számérték négyzetét adja vissza.  
  
 **Syntax**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa 1-3 szám négyzetének adja vissza.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a> BARACKSZÍNŰ  
 A megadott szög tangensét adja vissza radiánban, a megadott kifejezésben.  
  
 **Syntax**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa PI () tangensét számítja ki / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> CSONK  
 Egy numerikus érték, csonkolva, a legközelebbi egész értéket ad vissza.  
  
 **Syntax**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     A numerikus kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa csonkolja a következő pozitív és negatív számokat a legközelebbi egész értékre.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> Funkciók ellenőrzése típusa  
 Az alábbi funkciókat támogatja a típus ellenőrzése a bemeneti értékek szemben, és minden egyes logikai értéket eredményül.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy tömböt ad vissza.  
  
 **Syntax**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_ARRAY funkció használatával nem definiált típusok.  
  
```  
SELECT   
 IS_ARRAY(true),   
 IS_ARRAY(1),  
 IS_ARRAY("value"),  
 IS_ARRAY(null),  
 IS_ARRAY({prop: "value"}),   
 IS_ARRAY([1, 2, 3]),  
 IS_ARRAY({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy logikai érték visszaadása.  
  
 **Syntax**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_BOOL funkció használatával nem definiált típusok.  
  
```  
SELECT   
    IS_BOOL(true),   
    IS_BOOL(1),  
    IS_BOOL("value"),   
    IS_BOOL(null),  
    IS_BOOL({prop: "value"}),   
    IS_BOOL([1, 2, 3]),  
    IS_BOOL({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása.  
  
 **Syntax**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi, hogy a megadott JSON-dokumentum-tulajdonság. Az első igaz értéket ad vissza, mert "a", de a második hamis értéket ad vissza, mert hiányzik a "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Adja vissza egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa null.  
  
 **Syntax**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_NULL funkció használatával nem definiált típusok.  
  
```  
SELECT   
    IS_NULL(true),   
    IS_NULL(1),  
    IS_NULL("value"),   
    IS_NULL(null),  
    IS_NULL({prop: "value"}),   
    IS_NULL([1, 2, 3]),  
    IS_NULL({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy számot ad vissza.  
  
 **Syntax**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_NULL funkció használatával nem definiált típusok.  
  
```  
SELECT   
    IS_NUMBER(true),   
    IS_NUMBER(1),  
    IS_NUMBER("value"),   
    IS_NUMBER(null),  
    IS_NUMBER({prop: "value"}),   
    IS_NUMBER([1, 2, 3]),  
    IS_NUMBER({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy JSON-objektumot ad vissza.  
  
 **Syntax**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_OBJECT funkció használatával nem definiált típusok.  
  
```  
SELECT   
    IS_OBJECT(true),   
    IS_OBJECT(1),  
    IS_OBJECT("value"),   
    IS_OBJECT(null),  
    IS_OBJECT({prop: "value"}),   
    IS_OBJECT([1, 2, 3]),  
    IS_OBJECT({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Visszaad egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy primitívet (string, logikai értéket, numerikus vagy null értékű).  
  
 **Syntax**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_PRIMITIVE funkció használatával nem definiált típusok.  
  
```  
SELECT   
           IS_PRIMITIVE(true),   
           IS_PRIMITIVE(1),  
           IS_PRIMITIVE("value"),   
           IS_PRIMITIVE(null),  
           IS_PRIMITIVE({prop: "value"}),   
           IS_PRIMITIVE([1, 2, 3]),  
           IS_PRIMITIVE({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy karakterláncot ad vissza.  
  
 **Syntax**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_STRING funkció használatával nem definiált típusok.  
  
```  
SELECT   
       IS_STRING(true),   
       IS_STRING(1),  
       IS_STRING("value"),   
       IS_STRING(null),  
       IS_STRING({prop: "value"}),   
       IS_STRING([1, 2, 3]),  
       IS_STRING({prop: "value"}.prop2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a> Karakterlánc-függvények  
 A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[TARTALMAZ](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[HOSSZA](#bk_length)|  
|[ALACSONYABB](#bk_lower)|[LTRIM](#bk_ltrim)|[CSERÉLJE LE](#bk_replace)|  
|[REPLIKÁLÁS](#bk_replicate)|[FORDÍTOTT](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[ToString](#bk_tostring)|[FELSŐ](#bk_upper)|||  
  
####  <a name="bk_concat"></a> CONCAT  
 Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza.  
  
 **Syntax**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa a megadott értékek összefűzött karakterláncot adja vissza.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> TARTALMAZ  
 Visszaadja egy logikai arról a második-e az első karakterlánc-kifejezést tartalmaz.  
  
 **Syntax**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi, ha az "abc" tartalmaz "ab", és tartalmazza a "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik.  
  
 **Syntax**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa adja vissza, az "abc" a "b" és "bc" végződik.  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 A második első előfordulásának kezdőpozícióját adja vissza karakterlánc-kifejezés található a megadott karakterlánc első kifejezés, vagy a -1, ha a karakterlánc nem található.  
  
 **Syntax**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa az "abc" belül különböző karakterláncrészleteket indexét adja vissza.  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a> BALRA  
 A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza.  
  
 **Syntax**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa a különböző hosszúságú értékek az "abc" bal oldali részét adja vissza.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a> HOSSZA  
 A megadott karakterlánc-kifejezés karakterek számát adja vissza.  
  
 **Syntax**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa egy karakterlánc hosszát adja vissza.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a> ALACSONYABB  
 Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza.  
  
 **Syntax**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan CSÖKKENTHETI használandó lekérdezést.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a vezető üres.  
  
 **Syntax**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan LTRIM használata egy lekérdezésbe.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> CSERÉLJE LE  
 A megadott karakterlánc értéket az összes előfordulását lecseréli egy másik karakterláncérték.  
  
 **Syntax**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan használatához cserélje le a lekérdezésben.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKÁLÁSA  
 Egy karakterláncértéket a megadott számú alkalommal ismétlődik.  
  
 **Syntax**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan REPLIKÁLHAT használja a lekérdezésben.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> FORDÍTOTT  
 A karakterlánc-érték megfelelő sorrendben adja vissza.  
  
 **Syntax**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan fordított használja a lekérdezésben.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a> JOBBRA  
 A megadott számú karaktert a karakterlánc jobb oldali részét adja vissza.  
  
 **Syntax**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa a különböző hosszúságú értékek az "abc" megfelelő részét adja vissza.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a záró szóközöket.  
  
 **Syntax**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan RTRIM használata egy lekérdezésbe.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második kezdődik.  
  
 **Syntax**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa ellenőrzi, hogy ha a karakterlánc "abc" kezdődik "b" és "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a> KARAKTERLÁNCRÉSZLET  
 Már a megadott karakter számolt helyzetét megadja egy karakterlánc-kifejezés részét adja vissza, és továbbra is fennáll, a megadott időtartam, illetve a karakterlánc végén.  
  
 **Syntax**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa részét adja vissza, az "abc" kezdődően: 1 és 1 karakter hosszúságú.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Egy skaláris kifejezés karakterláncként adja vissza. 
  
 **Syntax**  
  
```  
ToString(<expr>)
```  
  
 **Argumentumok**  
  
-   `expr`  
  
     Ez bármilyen érvényes skaláris kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogy több különböző típusú ToString működését.   
  
```  
SELECT ToString(1.0000), ToString("Hello World"), ToString(NaN), ToString(Infinity),
ToString(IS_STRING(ToString(undefined))), IS_STRING(ToString(0.1234), ToString(false), ToString(undefined))
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "1", "$2": "Hello World", "$3": "NaN", "$4": "Infinity", "$5": "false", "$6": true, "$7": "false"}]  
```  
 Adja meg a következő bemenet:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 Az alábbi példa bemutatja, hogyan használható ToString más karakterlánc-függvények, például a CONCAT.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

 Íme az eredményhalmaz.  
  
```  
[{"$1":"4lb" },
 {"$1":"32kg"},
 {"$1":"400g" },
 {"$1":"8999mg" }]

```  
A következő bemeneti megadott.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
 Az alábbi példa bemutatja, hogyan használható ToString más karakterlánc-függvények, például cserélje le.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
 Íme az eredményhalmaz.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
 ```  
####  <a name="bk_upper"></a> FELSŐ  
 Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza.  
  
 **Syntax**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Návratové Typy**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan használja a felső a lekérdezésben  
  
```  
SELECT UPPER("Abc")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> Tömb funkciók  
 A következő skaláris függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a visszaadandó numerikus, logikai és a tömb értéket  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Egy tömb, amely az eredménye, összefűzi a két vagy több tömb értéket adja vissza.  
  
 **Syntax**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     A bármilyen érvényes tömbnek kifejezés.  
  
 **Návratové Typy**  
  
 Egy tömböt megadó kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa hogyan fűzze össze két tömb.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Jelzi, hogy a tömb tartalmazza-e a megadott érték logikai érték beolvasása. Megadhatja, ha az egyezés-e a teljes vagy részleges. 

 **Syntax**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     A bármilyen érvényes tömbnek kifejezés.  
  
-   `expr`  
  
     Ez bármilyen érvényes kifejezés.  

-   `bool_expr`  
  
     Van a logikai kifejezés.       
  
 **Návratové Typy**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 Az alábbi példa használatával ARRAY_CONTAINS tömbben tagsági ellenőrzése.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": true, "$2": false}]  
```  

 Az alábbi példa egy részleges egyezéssel a JSON használatával ARRAY_CONTAINS tömbben ellenőrzése.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 A megadott tömb kifejezés elemek számát adja vissza.  
  
 **Syntax**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     A bármilyen érvényes tömbnek kifejezés.  
  
 **Návratové Typy**  
  
 A numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa egy tömb ARRAY_LENGTH használatával hossza beszerzése.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Egy tömböt megadó kifejezést részét adja vissza.
  
 **Syntax**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     A bármilyen érvényes tömbnek kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 Az alábbi példa egy része egy tömb ARRAY_SLICE használatával beszerzése.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a> Térbeli funkciók  
 A következő skaláris függvények végrehajtania egy műveletet a egy térbeli objektum bemeneti érték, és a egy numerikus vagy logikai értéket adja vissza.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.  
  
 **Syntax**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
  
 **Návratové Typy**  
  
 Egy numerikus kifejezés tartalmazó távolságot adja vissza. Ez az alapértelmezett referenciarendszer mérőszámai van megadva.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan, amelyek a megadott hely a ST_DISTANCE beépített függvény használatával 30 km családi dokumentumokat. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Egy logikai kifejezés, amely azt jelzi, hogy a második argumentum GeoJSON (pont, Polygon vagy LineString) belül van-e a GeoJSON-objektum (pont, Polygon vagy LineString) az első argumentumban megadott adja vissza.  
  
 **Syntax**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
 
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan található összes családi dokumentuma egy sokszög ST_WITHIN használatával.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Egy logikai kifejezés jelzi-e a GeoJSON-objektum (pont, Polygon vagy LineString) az első argumentumban megadott metszi a GeoJSON (pont, Polygon vagy LineString) a második argumentumban adja vissza.  
  
 **Syntax**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
 
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan keresse meg az összes terület, amely a megadott sokszög-metszi saját.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Jelzi, hogy a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen egy logikai értéket ad vissza.  
  
 **Syntax**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString kifejezés.  
  
 **Návratové Typy**  
  
 Egy logikai kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa bemutatja, hogyan ellenőrizze, hogy egy pont érvényes ST_VALID használatával.  
  
 Például ezen a ponton rendelkezik egy földrajzi szélességének értéke, amely nem szerepel az érvényes tartomány értékkel [-90 és 90], ezért a lekérdezés hamis értéket adja vissza.  
  
 A GeoJSON specifikációnak poligonok, szükséges, hogy a megadott utolsó koordináta pár legyen ugyanaz, mint az első zárt alakzatot hozhat létre. Sokszög belül pontok óramutató járásával ellentétes irányban sorrendben kell adni. Egy megadott óramutató sorrendben sokszög benne a régió inverzét jelöli.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Egy JSON-értéket tartalmazó logikai értéket, ha a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen, és ha érvénytelen értéket ad vissza, továbbá egy olyan karakterláncértéket, az oka.  
  
 **Syntax**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez bármilyen érvényes GeoJSON pontot vagy a sokszög kifejezés.  
  
 **Návratové Typy**  
  
 Egy JSON-értéket tartalmazó logikai értéket, ha a megadott GeoJSON pontot vagy a sokszög kifejezés érvénytelen, és ha érvénytelen értéket ad vissza, továbbá egy olyan karakterláncértéket, az oka.  
  
 **Példák**  
  
 Az alábbi példa ST_ISVALIDDETAILED használatával (a részleteket) érvényességének ellenőrzése.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>További lépések  
 [SQL-szintaxis és az Azure Cosmos DB SQL-lekérdezés](sql-api-sql-query.md)   
 [Azure Cosmos DB – dokumentáció](https://docs.microsoft.com/azure/cosmos-db/)  
  
  
