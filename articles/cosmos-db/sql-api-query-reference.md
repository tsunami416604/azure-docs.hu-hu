---
title: Az Azure Cosmos DB SQL-nyelv szintaxisát
description: Ez a cikk ismerteti az Azure Cosmos DB, a másik operátorok és a kulcsszavak ezen a nyelven érhető el a használt SQL lekérdezési nyelvi szintaxisát.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 9625cb75bcae60f7f6eb2bae61e73066520037fc
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878267"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Az Azure Cosmos DB SQL nyelvi referencia 

Az Azure Cosmos DB támogatja a dokumentumok egy jól ismert SQL (Structured Query Language) lekérdezés nyelvtani például hierarchikus JSON-dokumentumokon végzett explicit séma vagy másodlagos indexek létrehozása nélkül. Ez a cikk az SQL lekérdezési nyelvi szintaxisát használja az SQL API-fiókok dokumentációját. Példa SQL-lekérdezések leírását lásd: [Cosmos DB-ben az SQL-lekérdezés példák](how-to-sql-query.md).  
  
Látogasson el a [Query Playground](https://www.documentdb.com/sql/demo), ahol Cosmos DB kipróbálása és SQL-lekérdezések futtatásához egy minta adatkészletig.  
  
## <a name="select-query"></a>SELECT-lekérdezésben  
Minden egyes lekérdezés SELECT záradékában és választható FROM áll és a WHERE záradék ANSI SQL előírások szerint. Általában az egyes lekérdezésekhez a forrás a FROM záradékban számbavétele megtörtént, majd a WHERE záradékban a szűrő a forrás JSON-dokumentumok részhalmazának beolvasásához a. Végül a SELECT záradékban szolgál a kért JSON-értékeit a kiválasztási listán. Példák: [SELECT-lekérdezésben példák](how-to-sql-query.md#SelectClause)
  
**Szintaxis**  
  
```sql
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
  
### <a name="logical-processing-order-of-the-select-statement"></a>A SELECT utasítás logikai feldolgozási sorrendje  
  
A rendelés feldolgozása szolgáló szerződéses klauzulák, amelyben a következő:  

1.  [FROM záradékban](#bk_from_clause)  
2.  [WHERE záradék](#bk_where_clause)  
3.  [ORDER BY záradék](#bk_orderby_clause)  
4.  [SELECT záradék](#bk_select_query)  

Vegye figyelembe, hogy ez eltér a megjelenítési sorrendjét a szintaxist. A rendezés van, hogy a feldolgozott záradék által bevezetett új szimbólumok láthatók, és későbbi feldolgozás záradékban használható. Például egy FROM záradékban megadott aliasok érhetők el, ha és a SELECT záradék.  

### <a name="whitespace-characters-and-comments"></a>Az elválasztó karakterek és megjegyzések  

Térközkaraktereket, amelyek nem részei egy Idézett karakterlánc vagy quoted azonosítója nem részei a nyelvi szintaxis és elemzés során figyelmen kívül hagyja.  

A lekérdezési nyelvet támogatja például a T-SQL stílus megjegyzések  

-   SQL Statement `-- comment text [newline]`  

Az elválasztó karakterek és a megjegyzések nincs semmilyen jelentőséggel a gramatice, amíg azok jogkivonatok külön kell használható. Például: `-1e5` egy egyetlen szám token, ideje van`: – 1 e5` egy mínusz tokent követi számának 1 és azonosító e5.  

##  <a name="bk_select_query"></a> SELECT záradék  
A SELECT utasítás záradékai kell következniük, ahogy fent látható. A választható záradékot bármelyike elhagyható. De amikor választható záradékot használják, azok kell megjelennie a helyes sorrendben. Példák: [SELECT-lekérdezésben példák](how-to-sql-query.md#SelectClause).

**Szintaxis**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **Argumentumok**  
  
- `<select_specification>`  

  Tulajdonságok vagy ki kell választania a az eredményhalmaz érték.  
  
- `'*'`  

  Itt adhatja meg, hogy az érték legyen beolvasva módosítása nélkül. Kifejezetten a feldolgozott értéke egy objektumot, ha a rendszer lekéri az összes tulajdonság.  
  
- `<object_property_list>`  
  
  Meghatározza a lekérdezni kívánt tulajdonságok listája. Minden egyes visszaküldött érték a megadott tulajdonságokkal rendelkező objektum lesz.  
  
- `VALUE`  

  Itt adhatja meg, hogy a JSON értéke legyen beolvasva a teljes JSON-objektum helyett. Ezzel ellentétben `<property_list>` nem wrap funkciót az előre jelzett érték egy objektumot.  
  
- `<scalar_expression>`  

  A kifejezés a következő időpontban számítja értéket jelölő. Lásd: [skaláris kifejezések](#bk_scalar_expressions) című szakasz részletezi.  
  
**Megjegyzések**  
  
A `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradékban van deklarálva pontosan egy aliast. `SELECT *` Itt egy identitás leképezése, amely lehet hasznos, ha nincs leképezés van szükség. Válassza ki * csak akkor érvényes, ha a FROM záradék van megadva, és csak egyetlen bemeneti forrás bevezetni.  
  
Mindkét `SELECT <select_list>` és `SELECT *` "szintaktikai cukor", és azt is megteheti egyszerű SELECT utasítás használatával, ahogy az alábbi jelöl.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   a következő azonos:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   a következő azonos:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Lásd még:**  
  
[Skaláris kifejezések](#bk_scalar_expressions)  
[SELECT záradék](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM záradékban  
Itt adhatja meg, a forrás vagy a csatlakoztatott források. A FROM záradék nem kötelező, kivéve, ha a forrás van szűrve, vagy előre jelzett költségről később a lekérdezésben. Az ehhez a záradékhoz célja, hogy adja meg az adatforrás, amelyre a lekérdezést kell működnie. A teljes tárolót gyakran a forrás, de ehelyett egy adhat meg a tároló egy részét. Nincs megadva ez a záradék, ha más záradékok továbbra is, ha a FROM záradékban megadott egyetlen dokumentum hajtani. Példák: [záradék PÉLDÁKBÓL](how-to-sql-query.md#FromClause)
  
**Szintaxis**  
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
**Argumentumok**  
  
- `<from_source>`  
  
  Egy adatforrás, itt adhatja meg, vagy az alias nélkül. Ha nincs megadva alias, azt fogja nelze odvodit z a `<container_expression>` szabályok a következő használatával:  
  
  -  Ha a kifejezés egy container_name, majd container_name használható aliasként.  
  
  -  Ha a kifejezés `<container_expression>`, akkor property_name, majd property_name használja a rendszer aliasként. Ha a kifejezés egy container_name, majd container_name használható aliasként.  
  
- AS `input_alias`  
  
  Megadja, hogy a `input_alias` olyan készlete, a mögöttes tároló-kifejezés által visszaadott értékek.  
 
- `input_alias` IN  
  
  Megadja, hogy a `input_alias` kell kívánt csoportjának megfelelő értékeket a léptetés keresztül minden egyes a mögöttes tároló-kifejezés által visszaadott tömb összes tömbelemek rekordsémáját. Bármely, amely nem tömb mögöttes tároló-kifejezés által visszaadott értéket a rendszer figyelmen kívül hagyja.  
  
- `<container_expression>`  
  
  Meghatározza azt a tárolót, lekérheti a dokumentumokat.  
  
- `ROOT`  
  
  Megadja, hogy a dokumentum az alapértelmezett beállítás, a jelenleg csatlakoztatott tároló lehet beolvasni.  
  
- `container_name`  
  
  Megadja, hogy a dokumentum a megadott tároló lehet beolvasni. A tároló neve meg kell egyeznie a jelenleg csatlakoztatott tároló nevét.  
  
- `input_alias`  
  
  Megadja, hogy a dokumentum határozzák meg a megadott alias a más forrásból kell olvasni.  
  
- `<container_expression> '.' property_`  
  
  Itt adhatja meg, a dokumentum elérésével legyen beolvasva a `property_name` által lekért összes dokumentum vlastnost nebo tömbindex tömbelem megadott tároló kifejezés.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Itt adhatja meg, a dokumentum elérésével legyen beolvasva a `property_name` által lekért összes dokumentum vlastnost nebo tömbindex tömbelem megadott tároló kifejezés.  
  
**Megjegyzések**  
  
Összes alias következtetni vagy megadott a `<from_source>(`s) egyedinek kell lennie. A szintaxist `<container_expression>.`property_name megegyezik a `<container_expression>' ['"property_name"']'`. Az utóbbi szintaxis azonban használható, ha egy tulajdonságnév nem azonosító karaktert tartalmaz.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hiányzó tulajdonságok, hiányzik a tömb elemei, és nem definiált értékek kezelése
  
Ha egy tároló kifejezés fér hozzá, tulajdonságok vagy a tömb elemeinek és, hogy az érték nem létezik, ezt az értéket figyelmen kívül hagyja, és további nincs feldolgozva.  
  
### <a name="container-expression-context-scoping"></a>Tároló kifejezés környezeti hatókör  
  
Egy tároló-kifejezés lehet tároló hatókörű vagy dokumentum hatókörű:  
  
-   Egy kifejezés, tároló-hatáskörű, ha a tároló kifejezés az alapul szolgáló forrás vagy a legfelső szintű vagy `container_name`. Az ilyen kifejezés dokumentumok, a tároló közvetlenül lekért adatok halmazai, és nem függ más tároló-kifejezések feldolgozása.  
  
-   Egy kifejezés, dokumentum-hatáskörű, ha az alapul szolgáló adatforrás a tároló kifejezés `input_alias` jelent meg a korábban a lekérdezésben. Ilyen egy kifejezés kiértékelése a fióknévnek tároló társított csoporthoz tartozó egyes dokumentumok hatókörében a tároló kifejezést kapott dokumentumokat egy halmazát jelölik.  Eredő beállítása során az egyes dokumentumok az alapul szolgáló készletben a tároló kifejezés által beszerzett egy Uniója fogja.  
  
### <a name="joins"></a>Illesztések 
  
A jelenlegi kiadásban a Cosmos DB támogatja a belső illesztések. További illesztési képességek érkeznek. 

Belső illesztések egy teljes körű a részt vesz a join készlet keresztszorzatát eredményez. Az N-módon való csatlakozás eredménye egy készletét N-elem rekordokat, ahol a rekord minden egyes érték társítva az aliasnevet a JOIN beállítása a résztvevő, és ez az alias más záradékban való hivatkozással érhető el. Példák: [ILLESZTÉSI kulcsszó példák](how-to-sql-query.md#Joins)
  
A kiértékelés a JOIN attól függ, hogy a környezet hatókörét a programban részt vevő készletek:  
  
- Illesztés között tároló-készlet és a tároló-hatáskörű B, több termék eredményezi, beállítja a és b szereplő összes elem beállítása
  
- A készlet és B, set dokumentum hatókörű közötti csatlakozzon kapott értékelése az egyes dokumentumok B beállítása. a dokumentum hatókörű beállított összes adatkészletek union eredményez.  
  
  Egy tároló-hatáskörű kifejezés legfeljebb a jelenlegi kiadásban, a lekérdezés-feldolgozó által támogatott.  
  
### <a name="examples-of-joins"></a>Példák illesztések  
  
Nézzük meg, a FROM záradék a következő: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Lehetővé teszik az egyes forrás megadása `input_alias1, input_alias2, …, input_aliasN`. A FROM záradék az N-rekordokat tartalmazó (N értékekkel rendelkező rekordot) adja vissza. Minden egyes rekord összes tároló-alias léptetés keresztül az adott csoportok által előállított értékkel rendelkezik.  
  
**1. példa** – 2 források  
  
- Lehetővé teszik `<from_source1>` tároló-tartozni, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source2>` dokumentum hatókörű input_alias1 hivatkozik, és csoportok képviselik:  
  
    {1, 2} számára `input_alias1 = A,`  
  
    {3} a `input_alias1 = B,`  
  
    {4, 5} számára `input_alias1 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2>` a következő rekordok felsorolásának eredményez:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**2. példa** – 3 források  
  
- Lehetővé teszik `<from_source1>` tároló-tartozni, és a set {A, B, C} képviseli.  
  
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
  
**3. példa** – 3 források  
  
- Tudassa < from_source1 > kell a tároló hatókörű, és a set {A, B, C} képviseli.  
  
- Lehetővé teszik `<from_source1>` tároló-tartozni, és a set {A, B, C} képviseli.  
  
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
  
**Lásd még**  
  
 [SELECT záradék](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> WHERE záradék  
 Adja meg a keresési feltétel, a lekérdezés által visszaadott dokumentumok számára. Példák: [WHERE záradék példák](how-to-sql-query.md#WhereClause)
  
 **Szintaxis**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentumok**  
  
- `<filter_condition>`  
  
   Itt adhatja meg az állapotot, a dokumentumok vissza kell teljesülniük.  
  
- `<scalar_expression>`  
  
   A kifejezés a következő időpontban számítja értéket jelölő. Tekintse meg a [skaláris kifejezések](#bk_scalar_expressions) című szakasz részletezi.  
  
  **Megjegyzések**  
  
  Ahhoz, hogy a dokumentum egy kifejezést a megadott kifejezés adja vissza a feltétel igaz értéket kell adnia. Csak az IGAZ logikai értéket eleget tesz a feltételt, semmilyen más érték: nem meghatározott, NULL értékű, false, szám, tömböt vagy objektumot nem teljesítik a feltételt.  
  
##  <a name="bk_orderby_clause"></a> ORDER BY záradék  
 Megadja a rendezési sorrendjét a lekérdezés által visszaadott eredményeket. Példák: [ORDER BY záradék példák](how-to-sql-query.md#OrderByClause)
  
 **Szintaxis**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentumok**  
  
- `<sort_specification>`  
  
   Megadja egy tulajdonságot vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés. A rendezési oszlop-név vagy oszlopban alias adható meg.  
  
   Több rendezési oszlop adható meg. Nevének egyedinek kell lennie. Az ORDER BY záradékban szereplő oszlopok rendezése sorrendje határozza meg, hogy a szervezet az rendezett eredményhalmaz. Azt jelenti az eredményhalmaz az első tulajdonság szerint van rendezve, és ezután a rendezett lista van rendezve, a második tulajdonságot, és így tovább.  
  
   Az ORDER BY záradékban hivatkozott oszlop nevét meg kell felelnie a kiválasztási listán vagy egy oszlop vagy egy oszlop definiálva a bármely kétértelműséget nélkül a FROM záradékban megadott táblában.  
  
- `<sort_expression>`  
  
   Megadja az egyetlen tulajdonságát vagy a lekérdezés eredményhalmazában rendezéshez használandó kifejezés.  
  
- `<scalar_expression>`  
  
   Tekintse meg a [skaláris kifejezések](#bk_scalar_expressions) című szakasz részletezi.  
  
- `ASC | DESC`  
  
   Itt adhatja meg, hogy a megadott oszlopban szereplő értékek növekvő vagy csökkenő sorrendben jelennek meg. ASC rendezi a legkisebb érték a legmagasabb érték. DESC rendezi a legmagasabb érték a legkisebb értéket. ASC az alapértelmezett rendezési sorrend. A legkisebb lehetséges értékek NULL értéket kell kezelni.  
  
  **Megjegyzések**  
  
  Bár a lekérdezési szintaxis támogatja a több sorrend tulajdonságai, a Cosmos DB lekérdezési modul támogatja a rendezést, csak egyetlen tulajdonság, és csak elleni tulajdonság neve (nem számított tulajdonságokhoz). Rendezés is megköveteli, hogy az indexelési házirendet egy tulajdonság és a megadott típus, a maximális pontosság tartományindexszel tartalmazza-e. Tekintse meg a további részletekért indexelési házirend dokumentációját.  
  
##  <a name="bk_scalar_expressions"></a> Skaláris kifejezések  
 Egy skaláris kifejezés szimbólumok és egyetlen értéket beszerzése kiértékelése operátorok kombinációja. Egyszerű kifejezések állandók, tulajdonság hivatkozik, tömb elem hivatkozásokat, alias hivatkozik, vagy lehet függvényhívások. Egyszerű kifejezések operátorok használatával összetett kifejezések egyesíthetők. Példák: [skaláris kifejezések példák](how-to-sql-query.md#scalar-expressions)
  
 További információ az értékeket, amelyek esetében előfordulhat, hogy a skaláris kifejezés: [állandók](#bk_constants) szakaszban.  
  
 **Szintaxis**  
  
```sql  
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
  
- `<constant>`  
  
   Egy állandó értéket jelöli. Lásd: [állandók](#bk_constants) című szakasz részletezi.  
  
- `input_alias`  
  
   Egy által definiált értéket képvisel a `input_alias` rendszerben bevezetett a `FROM` záradékban.  
  Ez az érték nem lehet garantáltan **nem definiált** –**nem definiált** a rendszer kihagyja a bemeneti értékeket.  
  
- `<scalar_expression>.property_name`  
  
   A tulajdonság az objektum olyan értékét jelöli. Ha a tulajdonság nem létezik vagy tulajdonság egy értéket, amely nem objektum hivatkozik, akkor a kifejezés eredménye **nem definiált** értéket.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Egy nevű tulajdonság értékét jelöli `property_name` vagy az index tömbelem `array_index` egy objektum vagy tömb. A tulajdonság/tömbindex nem létezik, vagy a tulajdonság/tömbindex hivatkozik egy érték alapján ez nem egy objektum vagy tömb, akkor a kifejezés eredménye nem meghatározott értéket eredményezne.  
  
- `unary_operator <scalar_expression>`  
  
   Az operátor alkalmazott egyetlen értéket jelöli. Lásd: [operátorok](#bk_operators) című szakasz részletezi.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Az operátor alkalmazott két értéket jelöli. Lásd: [operátorok](#bk_operators) című szakasz részletezi.  
  
- `<scalar_function_expression>`  
  
   Egy adott hívás eredménye által definiált értéket jelöli.  
  
- `udf_scalar_function`  
  
   A felhasználó által definiált skaláris függvény nevét.  
  
- `builtin_scalar_function`  
  
   A beépített skaláris függvény nevét.  
  
- `<create_object_expression>`  
  
   Egy új objektumot hoz létre a megadott tulajdonságokkal kapott értéket és az értéküket jelöli.  
  
- `<create_array_expression>`  
  
   Hozzon létre egy új tömbben elemként megadott értékekkel beszerzett értéket jelöli.  
  
- `parameter_name`  
  
   A megadott paraméternév egy értékét jelöli. A paraméter nevének rendelkeznie kell egy \@ az első karakter.  
  
  **Megjegyzések**  
  
  Beépített vagy felhasználó által definiált skalárfüggvény hívása során az összes argumentum definiálni kell. Ha bármelyik argumentum nem definiált, a függvény nem hívható, és az eredmény az lesz meghatározva.  
  
  Objektum létrehozásakor bármely vlastnost, amelyek nem definiált értéket kapja kihagyva, és nem tartalmazza a létrehozott objektum.  
  
  Ha egy tömb, bármely elem érték létrehozása, amely hozzá van rendelve **nem definiált** érték lesz kihagyva és nem tartalmazza a létrehozott objektum. Ennek hatására úgy, hogy a létrehozott tömb nem rendelkezik kihagyja az indexek a sor a következő meghatározott elemhez.  
  
##  <a name="bk_operators"></a> Operátorok  
 Ez a szakasz ismerteti a támogatott. Minden operátor pontosan egy kategória is hozzárendelhető.  
  
 Lásd: **operátor kategóriák** részleteiről az alábbi táblázat kezelésére vonatkozó **nem definiált** értékek, a bemeneti értékek és kezelését a nem egyező típusú értékeket adattípus-követelményekkel.  
  
 **Operátor kategóriák:**  
  
|**Kategória**|**Részletek**|  
|-|-|  
|**Aritmetikai**|Operátor input(s) szám(ok) kell megadni. Kimeneti is az a szám. Ha a bemeneti adatok bármelyike **nem definiált** típusa eltérő számú majd az eredményt vagy **nem definiált**.|  
|**Bitenkénti**|Operátor kell a 32 bites előjeles egész számokat szám(ok) input(s) vár. Kimenet is a 32 bites, előjeles egész szám.<br /><br /> Nem egész értéket a rendszer kerekíti. Pozitív értéket lefelé, az értéket felfelé kerekíti a negatív.<br /><br /> Bármilyen érték, amely a 32 bites egész tartományon kívül esik a legutóbbi 32-bit a két cég hexadecimális végrehajtásával konvertálja.<br /><br /> Ha a bemeneti adatok bármelyike **nem definiált** , vagy írjon be más, mint a szám, akkor az eredmény **nem definiált**.<br /><br /> **Megjegyzés:** A fenti viselkedése összeegyeztethető JavaScript bitenkénti operátor viselkedését.|  
|**Logikai**|Operátor input(s) Boolean(s) kell megadni. Kimeneti akkor is egy logikai érték.<br />Ha a bemeneti adatok bármelyike **nem definiált** , vagy írjon be másik logikai értéket, majd eredményül **nem definiált**.|  
|**Összehasonlítás**|Operátor azonos típusú, és nem lehet a nem definiált input(s) vár. A kimenete egy logikai érték.<br /><br /> Ha a bemeneti adatok bármelyike **nem definiált** vagy a bemeneti adatok különböző rendelkezik, majd az eredmény **nem definiált**.<br /><br /> Lásd: **összehasonlított értékek rendezés** érték rendezése részletei táblázatban.|  
|**sztring**|Operátor input(s) vágóelem kell megadni. Kimenet: karakterlánc.<br />Ha a bemeneti adatok bármelyike **nem definiált** , vagy írjon be másik karakterláncot, majd az eredmény **nem definiált**.|  
  
 **Az egyoperandusú operátorral:**  
  
|**Name (Név)**|**Művelet**|**Részletek**|  
|-|-|-|  
|**Aritmetikai**|+<br /><br /> -|A szám értéket ad vissza.<br /><br /> Bitenkénti negálást. Szám értéket ad vissza negated.|  
|**Bitenkénti**|~|Kiépítettektől kiegészítése. Egy halmaza egy számértéket ad vissza.|  
|**Logikai**|**NEM**|Negálás. Értéket ad vissza negated logikai érték.|  
  
 **Binární operátory:**  
  
|**Name (Név)**|**Művelet**|**Részletek**|  
|-|-|-|  
|**Aritmetikai**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Emellett.<br /><br /> Kivonás.<br /><br /> Szorzás.<br /><br /> Osztás.<br /><br /> Modulációs.|  
|**Bitenkénti**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitenkénti vagy.<br /><br /> Bitenkénti és művelet<br /><br /> Bitenkénti kizáró vagy.<br /><br /> Balra Tolást.<br /><br /> Jobbra Tolást.<br /><br /> Jobbra Tolást nulla ki.|  
|**Logikai**|**ÉS**<br /><br /> **VAGY**|Logikai és. Adja vissza **igaz** Ha mindkét argumentum **igaz**, adja vissza **hamis** más módon.<br /><br /> Logikai vagy műveletet. Adja vissza **igaz** , ha bármely argumentuma **igaz**, adja vissza **hamis** más módon.|  
|**Összehasonlítás**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Egyenlő. Adja vissza **igaz** , ha argumentumok egyenlő adja vissza **hamis** más módon.<br /><br /> Nem egyenlő. Adja vissza **igaz** argumentumok, amelyek nem egyeznek, ha függvény **hamis** ellenkező esetben.<br /><br /> Nagyobb, mint. Értéket ad vissza **igaz** első argumentum értéke nagyobb, mint a második, ha vissza **hamis** más módon.<br /><br /> Nagyobb vagy egyenlő. Értéket ad vissza **igaz** első argumentum értéke kisebb a másodiknál, ha vissza **hamis** más módon.<br /><br /> Kevesebb mint. Értéket ad vissza **igaz** Ha az első argumentum értéke kisebb, mint a második egy visszatérési **hamis** más módon.<br /><br /> Kisebb vagy egyenlő. Értéket ad vissza **igaz** első argumentum értéke kisebb vagy egyenlő, mint a második érték, ha vissza **hamis** más módon.<br /><br /> Coalesce. A második argumentum adja vissza, ha az első argumentum értéke egy **nem definiált** értéket.|  
|**String**|**&#124;&#124;**|Összefűzés. Összefűzésével mindkét argumentumot adja vissza.|  
  
 **Ternáris kezelők:**  

|**Name (Név)**|**Művelet**|**Részletek**| 
|-|-|-|  
|Ternáris operátor|?|A második argumentum adja vissza, ha az első argumentum értéke **igaz**; ellenkező esetben adja vissza a harmadik argumentum.|  

  
 **Az összehasonlított értékek rendezése**  
  
|**Typo**|**Értékek sorrendje**|  
|-|-|  
|**Nem definiált**|Nem összehasonlítható.|  
|**Null**|Egyetlen érték: **null értékű**|  
|**Szám**|Természetes valós szám.<br /><br /> Negatív végtelent értéke kisebb, mint bármely más érték szám.<br /><br /> Pozitív végtelen érték nagyobb, mint bármely más érték szám. **NaN** értéke nem összehasonlítható. Összehasonlítás a **NaN** eredményez **nem definiált** értéket.|  
|**String**|Lexicographical sorrend.|  
|**Tömb**|Nincs rendezése, de méltányos.|  
|**Objektum**|Nincs rendezése, de méltányos.|  
  
 **Megjegyzések**  
  
 A Cosmos dB-ben a típusú értékek gyakran nem ismert mindaddig, amíg az adatok lekérése az adatbázisból. Annak érdekében, hogy a hatékony lekérdezések végrehajtása támogatja, az operátorok a legtöbb szigorú adattípus-követelményekkel rendelkeznek. Operátorok önmagukban is hajt végre implicit konverzió.  
  
 Ez azt jelenti, hogy a lekérdezés, például: Válassza ki * a legfelső szintű r WHERE r.Age = 21 csak visszatér kora tulajdonsággal rendelkező dokumentumok egyezik a 21. Dokumentumok kora egyenlő a karakterlánc "21" vagy "0021" karakterlánc tulajdonsággal nem egyeznek meg, mint a "21" kifejezés = 21 kiértékeli a nem meghatározott. Ez lehetővé teszi egy jobban kihasználja a indexek, mert a keresés egy adott értéket (például a 21) gyorsabb, mint a határozatlan számú keresése lehetséges megegyezik (21 szám vagy karakterláncok "21", "021", "21,0"...). Ez eltér a hogyan értékeli ki a JavaScript a kezelők az eltérő típusú értékeket.  
  
 **Tömbök és objektumok egyenlőség és összehasonlítása**  
  
 A tartomány operátorral egymáshoz csatolt tömböt vagy objektumot értékek összehasonlítása (>, > =, <, < =) nincs definiálva a nem meghatározott objektum vagy tömb értékek sorrendje nem fog eredményezni. Azonban az egyenlőség operátorok használata (=,! =, <>) támogatott és az értékek szerkezetileg lesz összehasonlítva.  
  
 Tömbök, egyenlő, ha mindkét Pole mají azonos számú elemet, és megfelelő pozíciók található elemekben is egyenlő. Ha nincs definiálva a bármely virtuálisgép-pár elemek eredmények összehasonlítása, a tömb összehasonlítás eredménye nincs meghatározva.  
  
 Objektumok egyenlőek, ha mindkét objektum meghatározott azonos tulajdonságokkal rendelkezik, és megfelelő tulajdonságok értékei a következők is egyenlő. Ha nincs definiálva a bármely vlastnost értékeket eredményez párjai összehasonlítása, az objektum összehasonlítás eredménye nincs meghatározva.  
  
##  <a name="bk_constants"></a> Állandók  
 Egy állandó, más néven szövegkonstans vagy skaláris, egy meghatározott értéket jelölő szimbólumot. Egy állandó formátumát az érték azt jelenti, hogy függ.  
  
 **Skaláris adattípusokat támogatja:**  
  
|**Typo**|**Értékek sorrendje**|  
|-|-|  
|**Nem definiált**|Egyetlen érték: **nem definiált**|  
|**Null**|Egyetlen érték: **null értékű**|  
|**Logikai**|Értékek: **hamis**, **igaz**.|  
|**Szám**|Egy dupla pontosságú lebegőpontos számnál, szabvány IEEE 754.|  
|**String**|Nulla vagy több Unicode karakter sorozata. Karakterláncok egyszeres vagy kettős idézőjelek közé kell tenni.|  
|**Tömb**|Nulla vagy több elemek sorrendjét. Egyes elemek nincs megadva kivételével minden skaláris adattípusú érték lehet.|  
|**Objektum**|Egy nulla vagy több név/érték párok rendezetlen készlete. Név egy Unicode karakterlánc, kivéve értéke lehet bármely skaláris adattípusú **Undefined**.|  
  
 **Szintaxis**  
  
```sql  
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
  
* `<undefined_constant>; undefined`  
  
  Nincs megadva típusú érték nem definiált jelöli.  
  
* `<null_constant>; null`  
  
  Jelöli **null** típusú érték **Null**.  
  
* `<boolean_constant>`  
  
  Logikai érték típusú konstans jelöli.  
  
* `false`  
  
  Jelöli **hamis** logikai típusú érték.  
  
* `true`  
  
  Jelöli **igaz** logikai típusú érték.  
  
* `<number_constant>`  
  
  Egy állandó jelöli.  
  
* `decimal_literal`  
  
  Tizedes tört literálok lehetnek olyan szám vagy decimális jelölés, vagy tudományos jelölés használatával.  
  
* `hexadecimal_literal`  
  
  Hexadecimális literálok előtagja '0 x' legalább egy hexadecimális számjegy követ használatával értékek, amelyek.  
  
* `<string_constant>`  
  
  Egy karakterlánc típusú konstans jelöli.  
  
* `string _literal`  
  
  Karakterlánc-literálnak sorozata, nulla vagy több Unicode-karaktereket vagy escape-karaktersorozatokat által képviselt Unicode karakterláncokat is. Karakterlánc-literálnak aposztrófok közé kell tenni (aposztróf: ") vagy dupla idézőjel (idézőjel:").  
  
  Következő escape-karaktersorozatokat engedélyezettek:  
  
|**Escape-szekvencia**|**Leírás**|**Unicode-karakter**|  
|-|-|-|  
|\\'|aposztróf (')|U+0027|  
|\\"|idézőjel (")|U+0022|  
|\\\ |fordított solidus (\\)|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|vissza|U+0008|  
|\f|Lapdobás|U+000C|  
|\n|Sortörés|U+000A|  
|\r|kocsivissza|U+000D|  
|\t|tabulátor|U+0009|  
|\uXXXX|Egy 4 hexadecimális számjegy által meghatározott Unicode-karakter.|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> Lekérdezési teljesítmény irányelvek  
 Ahhoz, hogy egy lekérdezés hatékonyan kell végrehajtani egy nagy méretű tároló, a szűrőket, amelyek segítségével egy vagy több index kiszolgálható kell használnia.  
  
 A keresési index akkor veszi figyelembe a következő szűrőket:  
  
- Egyenlő operátor (=) használata egy dokumentum elérési út és a egy konstans.  
  
- Tartomány-operátor használható (<, \<=, >, > =) egy dokumentum elérési út és a szám állandó.  
  
- A dokumentum elérési út bármely kifejezés, amely azonosítja a dokumentumok a hivatkozott adatbázis tárolóból egy állandó elérési jelöli.  
  
  **A dokumentum az elérésiút-kifejezés**  
  
  A dokumentum elérésiút-kifejezések kifejezések, amelyek elérési útvonalát vlastnost nebo Pole indexelő assessors egy dokumentum-adatbázist tároló dokumentumok érkező keresztül is. Az elérési út hivatkozik egy szűrőt, közvetlenül a dokumentumok az adatbázis-tárolóban lévő értékek helyének azonosítására használható.  
  
  Figyelembe kell venni egy dokumentum elérési út kifejezése kifejezés akkor meg:  
  
1.  A tároló legfelső szintű közvetlenül hivatkozni.  
  
2.  Hivatkozás tulajdonság vagy állandó tömb indexelő néhány dokumentum elérési útját kifejezés  
  
3.  Alias, néhány dokumentum elérési út kifejezése jelölő hivatkozhat.  
  
     **Szintaxis konvenciók**  
  
     A következő táblázat ismerteti a konvenciók leírása a szintaxisa a következő SQL-hivatkozás.  
  
    |**Volání**|**Alkalmazási cél**|  
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
 A cosmos DB számos beépített SQL-funkciókat biztosít. A beépített függvénykategóriákat alább láthatók.  
  
|Függvény|Leírás|  
|--------------|-----------------|  
|[Matematikai függvények](#bk_mathematical_functions)|A matematika függvényekkel végezhet a számítást, általában argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.|  
|[Funkciók ellenőrzése típusa](#bk_type_checking_functions)|A típus ellenőrzése funkciók lehetővé teszik az SQL-lekérdezések belül egy kifejezés típusának ellenőrzése.|  
|[Sztringfüggvények](#bk_string_functions)|A karakterlánc-függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza.|  
|[Tömb funkciók](#bk_array_functions)|A tömb függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a visszaadott numerikus, a logikai vagy a tömb értéket.|  
|[Térbeli funkciók](#bk_spatial_functions)|A térbeli függvények végrehajtania egy műveletet a térbeli objektum bemeneti érték a, és a egy numerikus vagy logikai értéket adja vissza.|  
  
###  <a name="bk_mathematical_functions"></a> Matematikai függvények  
 A következő feladatokat végezhet a számítást, általában argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[FOK](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[KEREKÍTÉS](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza.  
  
 **Szintaxis**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
   A numerikus kifejezés.  
  
  **Návratové Typy**  
  
  A numerikus kifejezést ad vissza.  
  
  **Megjegyzések**  
  
  Az állandó **e** (2.718281...), a természetes logaritmusokat alapját.  
  
  A kitevő, egy szám az állandó **e** hatványára szám. Ha például EXP(1.0) = e ^ 1.0-s = 2.71828182845905 és EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Az exponenciális egy szám természetes alapú logaritmus alapja az a szám maga: EXP (LOG (n)) = n. Az exponenciális egy szám természetes algoritmusát az a szám, és maga: LOG (EXP (n)) = n.  
  
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
  
 **Szintaxis**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
   A numerikus kifejezés.  
  
- `base`  
  
   Olyan nem kötelező numerikus argumentum, amely beállítja a logaritmus alapjának a.  
  
  **Návratové Typy**  
  
  A numerikus kifejezést ad vissza.  
  
  **Megjegyzések**  
  
  Alapértelmezés szerint LOG() a természetes alapú logaritmusát adja vissza. A logaritmus alapja a választható alap paraméter használatával módosíthatja egy másik értéket.  
  
  A természetes alapú logaritmus alapja az alap-es alapú logaritmusa **e**, ahol **e** megegyezik egy irrational állandó körülbelül 2.718281828.  
  
  Az exponenciális egy szám természetes algoritmusát az a szám maga: LOG( EXP( n ) ) = n. Az exponenciális egy szám természetes alapú logaritmus alapja az a szám, és maga: EXP( LOG( n ) ) = n.  
  
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
  
 **Szintaxis**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
PI ()  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
   A numerikus kifejezés.  
  
- `y`  
  
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
  
 **Szintaxis**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
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
  
 **Szintaxis**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":false,"$2":false,"$3":false,"$4":false,"$5":false,"$6":true,"$7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy logikai érték visszaadása.  
  
 **Szintaxis**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":true,"$2":false,"$3":false,"$4":false,"$5":false,"$6":false,"$7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása.  
  
 **Szintaxis**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":true,"$2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Adja vissza egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa null.  
  
 **Szintaxis**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":false,"$2":false,"$3":false,"$4":true,"$5":false,"$6":false,"$7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy számot ad vissza.  
  
 **Szintaxis**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":false,"$2":true,"$3":false,"$4":false,"$5":false,"$6":false,"$7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy JSON-objektumot ad vissza.  
  
 **Szintaxis**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":false,"$2":false,"$3":false,"$4":false,"$5":true,"$6":false,"$7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Visszaad egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy primitívet (string, logikai típusú, numerikus vagy null értékű).  
  
 **Szintaxis**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
  
 **Szintaxis**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentumok**  
  
- `expression`  
  
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
[{"$1":false,"$2":false,"$3":true,"$4":false,"$5":false,"$6":false,"$7":false}] 
```  
  
###  <a name="bk_string_functions"></a> Karakterlánc-függvények  
 A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[TARTALMAZ](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[HOSSZA](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[CSERÉLJE LE](#bk_replace)|  
|[REPLIKÁLÁS](#bk_replicate)|[FORDÍTOTT](#bk_reverse)|[JOBBRA](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza.  
  
 **Szintaxis**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
   Van bármilyen érvényes karakterlánc-kifejezés.  
  
- `num_expr`  
  
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
  
 **Szintaxis**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
   Van bármilyen érvényes karakterlánc-kifejezés.  
  
- `num_expr`  
  
   Ez bármilyen érvényes numerikus kifejezés. Ha num_expr negatív, vagy nem véges, az eredmény nincs meghatározva.

  > [!NOTE]
  > Az eredmény hossza legfeljebb 10 000 karakternél azaz (length(str_expr) * num_expr) < 10 000.
  
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
  
 **Szintaxis**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
   Van bármilyen érvényes karakterlánc-kifejezés.  
  
- `num_expr`  
  
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
  
 **Szintaxis**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Egy tömb lefordítani kifejezést ad vissza. Ha a kifejezés nem fordítható le, nem definiált adja vissza.  
  
 **Szintaxis**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumentumok**  
  
- `expr`  
  
   Van bármilyen érvényes, a egy JSON-tömböt kifejezéssel kiértékelendő skaláris kifejezés. Vegye figyelembe, hogy beágyazott karakterlánc-értékeket kell megírni, az idézőjelekkel együtt, hogy érvényesek legyenek. A JSON-formátumban a részletekért lásd: [json.org](https://json.org/)
  
  **Návratové Typy**  
  
  Egy tömböt megadó kifejezést ad vissza, vagy nincs definiálva.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogy több különböző típusú StringToArray működését. 
  
 Az alábbi parancsok példák érvényes adatokkal.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

 Íme az eredményhalmaz.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

 Érvénytelen bemenet egy példát a következő: 
   
 A tömbön belüli szimpla idézőjelek között nem érvényes JSON.
Akkor is, ha a lekérdezés érvényes, azok fog nem elemezhető érvényes tömbökhöz. A tömb karakterláncon belüli karakterláncokat vagy escape-karakterrel "[\\"\\"]" vagy a környező ajánlatot egyetlen kell lennie. a(z) [""] ".

```
SELECT
    StringToArray("['5','6','7']")
```

 Íme az eredményhalmaz.

```
[{}]
```

 A következő példák érvénytelen bemenet.
   
 Az átadott kifejezés fog elemezhető, egy JSON-tömböt; a következő nem értékelik ki, írja be a tömb, és így a nem definiált visszaadása.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

 Íme az eredményhalmaz.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Egy logikai lefordítani kifejezést ad vissza. Ha a kifejezés nem fordítható le, nem definiált adja vissza.  
  
 **Szintaxis**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumentumok**  
  
- `expr`  
  
   Bármely érvényes skaláris kifejezés, egy logikai kifejezés értékelése van.  
  
  **Návratové Typy**  
  
  Visszaad egy logikai kifejezés, vagy nincs definiálva.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogy több különböző típusú StringToBoolean működését. 
 
 Az alábbi parancsok példák érvényes adatokkal.

 Elválasztó karakterek használata engedélyezett, csak előtt vagy után a "true"/ "false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

 Az alábbi példák a bemenet érvénytelen.
 
 Logikai kis-és nagybetűket, és az összes kisbetűs karaktereket, azaz "true" és "false" kellett készülnie.

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

 Íme az eredményhalmaz.  
  
```  
[{}]
``` 

 Az átadott kifejezés fog elemezhető, egy logikai kifejezés; Írja be a logikai érték, és így a nem definiált vissza nem értékelik ki ezeket a bemeneteket.

 ```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

 Íme az eredményhalmaz.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Null értékekké kifejezést ad vissza. Ha a kifejezés nem fordítható le, nem definiált adja vissza.  
  
 **Szintaxis**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumentumok**  
  
- `expr`  
  
   Bármely érvényes, null kifejezésként kiértékelendő skaláris kifejezés van.
  
  **Návratové Typy**  
  
  Egy NULL értékű kifejezést ad vissza, vagy nincs definiálva.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogy több különböző típusú StringToNull működését. 

 Az alábbi parancsok példák érvényes adatokkal.
 
 Szóköz kizárólag előtt vagy után "null" használata engedélyezett.

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

 Az alábbi példák a bemenet érvénytelen.

 NULL megkülönbözteti a kis-és nagybetűket, és kell megírni, azaz "null" kisbetűs karakterek.

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Íme az eredményhalmaz.  
  
```  
[{}]
```  

 Az átadott kifejezés fog elemezhető; null kifejezésként Írja be a NULL értékű, és így a nem definiált vissza nem értékelik ki ezeket a bemeneteket.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Egy szám fordítja le kifejezést ad vissza. Ha a kifejezés nem fordítható le, nem definiált adja vissza.  
  
 **Szintaxis**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumentumok**  
  
- `expr`  
  
   Van bármilyen érvényes skaláris kifejezés JSON-szám kifejezésként ki kell értékelni. A JSON-ban számok egész vagy lebegőpontos kell lennie. A JSON-formátumban a részletekért lásd: [json.org](https://json.org/)  
  
  **Návratové Typy**  
  
  Egy szám kifejezést ad vissza, vagy nincs definiálva.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogy több különböző típusú StringToNumber működését. 

 Elválasztó karakterek használata engedélyezett, a szám csak előtt vagy után.
 
```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Íme az eredményhalmaz.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

 A JSON érvényes számnak kell lennie vagy kell egész vagy lebegőpontos szám.
 
```  
SELECT   
    StringToNumber("0xF")
```  
  
 Íme az eredményhalmaz.  
  
```  
{{}}
```  

 Az átadott kifejezés fog elemezhető szám kifejezésként; Írja be a szám és így nem definiált nem értékelik ki ezeket a bemeneteket. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Íme az eredményhalmaz.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Az objektum-jogkivonattá alakítja kifejezést ad vissza. Ha a kifejezés nem fordítható le, nem definiált adja vissza.  
  
 **Szintaxis**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumentumok**  
  
- `expr`  
  
   Van bármilyen érvényes, a egy JSON-objektum kifejezéssel kiértékelendő skaláris kifejezés. Vegye figyelembe, hogy beágyazott karakterlánc-értékeket kell megírni, az idézőjelekkel együtt, hogy érvényesek legyenek. A JSON-formátumban a részletekért lásd: [json.org](https://json.org/)  
  
  **Návratové Typy**  
  
  Objektum kifejezést ad vissza, vagy nincs definiálva.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogy több különböző típusú StringToObject működését. 
  
 Az alábbi parancsok példák érvényes adatokkal.
 
``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

 Íme az eredményhalmaz.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```
 
 Az alábbi példák a bemenet érvénytelen.
Akkor is, ha a lekérdezés érvényes, azok fog nem elemezhető érvényes objektumra. Karakterláncok a karakterláncot, objektumot vagy escape-karakterrel "{\\" egy\\":\\" str\\"}", vagy a környező ajánlatot egyetlen kell lennie. a(z) "{"a":"str"}".

 A tulajdonságnevek körülvevő szimpla idézőjelek között nem érvényes JSON.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

 Íme az eredményhalmaz.

```  
[{}]
```  

 Körülvevő idézőjeleket tulajdonság nevében a rendszer nem érvényes JSON.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

 Íme az eredményhalmaz.

```  
[{}]
``` 

 Az alábbi példák a bemenet érvénytelen.
 
 Az átadott kifejezés fog elemezhető JSON-objektumként; Írja be az objektum, és így a nem definiált vissza nem értékelik ki ezeket a bemeneteket.
 
``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 Íme az eredményhalmaz.

```
[{}]
```

####  <a name="bk_substring"></a> KARAKTERLÁNCRÉSZLET  
 Már a megadott karakter számolt helyzetét megadja egy karakterlánc-kifejezés részét adja vissza, és továbbra is fennáll, a megadott időtartam, illetve a karakterlánc végén.  
  
 **Szintaxis**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
   Van bármilyen érvényes karakterlánc-kifejezés.  
  
- `num_expr`  
  
   Van bármilyen érvényes numerikus kifejezés jelölésére, a kezdő és záró karakter.    
  
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
  
 **Szintaxis**  
  
```  
ToString(<expr>)
```  
  
 **Argumentumok**  
  
- `expr`  
  
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
 
####  <a name="bk_trim"></a> TRIM  
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a kezdő és záró üres.  
  
 **Szintaxis**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
   Van bármilyen érvényes karakterlánc-kifejezés.  
  
  **Návratové Typy**  
  
  Egy karakterlánc-kifejezés adja vissza.  
  
  **Példák**  
  
  Az alábbi példa bemutatja a TRIM függvénnyel egy lekérdezésbe.  
  
```  
SELECT TRIM("   abc"), TRIM("   abc   "), TRIM("abc   "), TRIM("abc")   
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc", "$4": "abc"}]  
``` 
####  <a name="bk_upper"></a> FELSŐ  
 Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza.  
  
 **Szintaxis**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentumok**  
  
- `str_expr`  
  
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
  
 **Szintaxis**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentumok**  
  
- `arr_expr`  
  
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
Jelzi, hogy a tömb tartalmazza-e a megadott érték logikai érték beolvasása. Egy logikai kifejezés belül a parancs használatával ellenőrizheti az objektum teljes vagy részleges egyezést. 

 **Szintaxis**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentumok**  
  
- `arr_expr`  
  
   A bármilyen érvényes tömbnek kifejezés.  
  
- `expr`  
  
   Ez bármilyen érvényes kifejezés.  

- `bool_expr`  
  
   Van a logikai kifejezés. Ha a változó értéke "true'and, ha a megadott keresési érték egy olyan objektum, a parancs ellenőrzi egy részleges egyezéssel (Keresés Objekt egy objektum egy részét). Ha, "false" értékre van állítva, a parancs ellenőrzi a tömbön belüli összes objektum teljes megfelel. Ha nincs megadva az alapértelmezett érték a False (hamis). 
  
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
  
 **Szintaxis**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentumok**  
  
- `arr_expr`  
  
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
  
 **Szintaxis**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentumok**  
  
- `arr_expr`  
  
   A bármilyen érvényes tömbnek kifejezés.  
  
- `num_expr`  
  
   A tömb kezdőpontját nulláról induló numerikus indexszel. Adjon meg a kezdő indexet az utolsó elem a tömbben – azaz a 1 hivatkozások viszonyítva az utolsó elem a tömbben negatív értékeket is használható.  

- `num_expr`  

   Az eredményül kapott tömbben található elemek maximális számát.    

  **Návratové Typy**  
  
  Egy tömböt megadó kifejezést ad vissza.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogyan ARRAY_SLICE használatával tömbje különböző szeletet.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000),
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100)      
  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"],
           "$3": ["strawberries"],  
           "$4": ["strawberries", "bananas"], 
           "$5": [],
           "$6": ["strawberries", "bananas"],
           "$7": [] 
}]  
```  
 
###  <a name="bk_spatial_functions"></a> Térbeli funkciók  
 A következő skaláris függvények végrehajtania egy műveletet a térbeli objektum bemeneti érték a, és a egy numerikus vagy logikai értéket adja vissza.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.  
  
 **Szintaxis**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
- `spatial_expr`  
  
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
  
 **Szintaxis**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
- `spatial_expr`  
  
   Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
 
- `spatial_expr`  
  
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
  
 **Szintaxis**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
- `spatial_expr`  
  
   Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
 
- `spatial_expr`  
  
   Ez bármilyen érvényes GeoJSON-pont, Polygon vagy LineString objektum kifejezés.  
  
  **Návratové Typy**  
  
  Egy logikai értéket ad vissza.  
  
  **Példák**  
  
  Az alábbi példa bemutatja, hogyan keresse meg a megadott sokszög metsző minden terület.  
  
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
  
 **Szintaxis**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentumok**  
  
- `spatial_expr`  
  
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
  
 **Szintaxis**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentumok**  
  
- `spatial_expr`  
  
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

- [SQL-szintaxis és a Cosmos DB SQL-lekérdezés](how-to-sql-query.md)

- [Cosmos DB-dokumentáció](https://docs.microsoft.com/azure/cosmos-db/)  
