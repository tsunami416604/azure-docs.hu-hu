---
title: 'Az Azure Cosmos DB DocumentDB API: SQL-szintaxis |} Microsoft Docs'
description: "Az Azure Cosmos DB DocumentDB API SQL lekérdező nyelve dokumentációját."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/18/2017
ms.author: mimig
ms.openlocfilehash: 4907df15fddfb7d8d6128dc994b0920ca601f2c7
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="azure-cosmos-db-documentdb-api-sql-syntax-reference"></a>Az Azure Cosmos DB DocumentDB API: SQL-szintaxis referencia

Az Azure Cosmos DB DocumentDB API támogatja a dokumentumok lekérdezését a megszokott SQL (Structured Query Language) használatával például nyelvtan hierarchikus JSON-dokumentumok keresztül anélkül, hogy explicit séma vagy a másodlagos indexek létrehozását. Ez a témakör a DocumentDB API SQL lekérdező nyelve dokumentációját.

A DocumentDB API SQL lekérdező nyelve bemutatóért lásd: [Azure Cosmos DB DocumentDB API SQL-lekérdezések](documentdb-sql-query.md).  
  
Azt is hívhat meg, hogy keresse fel a [Tesztlekérdezéseket](http://www.documentdb.com/sql/demo) ahol Azure Cosmos DB próbálja és SQL-lekérdezések futtatása az adatkészletet.  
  
## <a name="select-query"></a>SELECT lekérdezés  
Lekéri a JSON-dokumentumokat az adatbázisból. Kifejezések kiértékelésével, olyan leképezések szűrés támogatja, és csatlakozik.  A SELECT utasítás leíró jelölések megjelennének a szintaxis egyezmények szakaszban.  
  
**Szintaxis**  
  
```
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **Megjegyzések**  
  
 Lásd az alábbi szakaszok további részletek az egyes záradékban:  
  
-   [SELECT záradékban](#bk_select_query)  
  
-   [FROM záradékban](#bk_from_clause)  
  
-   [A WHERE záradék](#bk_where_clause)  
  
-   [ORDER BY záradék](#bk_orderby_clause)  
  
A SELECT utasításban záradékok kell lehetnek rendezve, ahogy fent látható. A választható záradékok valamelyik elhagyható. Azonban nem kötelező záradék használata esetén azok szerepelnie kell a megfelelő sorrendben.  
  
**A SELECT utasítástípusnál logikai a feldolgozási sorrendben**  
  
Ahol záradékok feldolgozásának sorrendje:  

1.  [FROM záradékban](#bk_from_clause)  
2.  [A WHERE záradék](#bk_where_clause)  
3.  [ORDER BY záradék](#bk_orderby_clause)  
4.  [SELECT záradékban](#bk_select_query)  

Vegye figyelembe, hogy ez nem azonos a szintaxisa látható sorrendben. A rendezés van, úgy, hogy a feldolgozott záradék által bevezetett új szimbólumok láthatóak, és később feldolgozott záradékban használható. Például egy FROM záradékban megadott aliasok érhetők el, ha és SELECT záradékban.  

**Az elválasztó karakterek és megjegyzések**  

Összes szóköz részei idézőjeles karakterláncot vagy idézőjelek között azonosítója nem részei a nyelvi szintaxis és elemzése során figyelmen kívül lesznek hagyva.  

A lekérdezési nyelv támogatja például a T-SQL stílus megjegyzések  

-   SQL-utasításban`-- comment text [newline]`  

Bár a szóköz karakter és megjegyzéseket nincs bármely többszörösére a nyelvtanban, ezeket kell használják külön jogkivonatokat. Például: `-1e5` számú token, igénybe van`: – 1 e5` mínusz jogkivonat követi 1-es számú azonosítóval e5.  

##  <a name="bk_select_query"></a>SELECT záradékban  
A SELECT utasításban záradékok kell lehetnek rendezve, ahogy fent látható. A választható záradékok valamelyik elhagyható. Azonban nem kötelező záradék használata esetén azok szerepelnie kell a megfelelő sorrendben.  

**Szintaxis**  
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
  
 Tulajdonságok vagy -érték az eredménykészlet kell jelölni.  
  
 `'*'`  
  
Meghatározza, hogy az érték változtatás nélkül kell-e olvasni. Kifejezetten a feldolgozott értéke egy objektumot, ha az összes tulajdonság veszi.  
  
 `<object_property_list>`  
  
Felsorolja a Tulajdonságok kérhető. Minden visszaadott érték lesz az objektum a megadott tulajdonságokkal.  
  
`VALUE`  
  
Meghatározza, hogy a JSON-érték lekérni a teljes JSON-objektum helyett. Ezzel szemben, `<property_list>` nem törik több sorba a tervezett érték egy objektumban.  
  
`<scalar_expression>`  
  
A kifejezés ki értékét képviselő. Lásd: [skaláris kifejezések](#bk_scalar_expressions) című szakaszban talál információt.  
  
**Megjegyzések**  
  
A `SELECT *` szintaxis csak akkor érvényes, ha a FROM záradékban deklarálva van: pontosan egy aliast. `SELECT *`Itt egy identitás-leképezés, amely lehet hasznos, ha nincs leképezés van szükség. Válassza ki * csak akkor érvényes, ha a FROM záradékban megadott és bevezetett csak egyetlen bemenetet.  
  
Vegye figyelembe, hogy `SELECT <select_list>` és `SELECT *` "szintaktikai cukor", és azt is megteheti lehetnek a lent látható módon egyszerű SELECT utasítás használatával.  
  
1.  `SELECT * FROM ... AS from_alias ...`  
  
     az egyenértékű:  
  
     `SELECT from_alias FROM ... AS from_alias ...`  
  
2.  `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
     az egyenértékű:  
  
     `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**Lásd még:**  
  
[Skaláris kifejezések](#bk_scalar_expressions)  
[SELECT záradékban](#bk_select_query)  
  
##  <a name="bk_from_clause"></a>FROM záradékban  
Adja meg a forrás vagy a csatlakoztatott adatforrások. A FROM záradék használata nem kötelező. Ha nem a megadott, más záradékok továbbra is hajtható végre, mint ha a FROM záradékban megadott egyetlen dokumentum.  
  
**Szintaxis**  
  
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
  
Meghatározza egy adatforrást, vagy az alias nélkül. Ha nincs megadva alias, azt fogja következtethető ki a `<collection_expression>` következő szabályok segítségével:  
  
-   Ha a kifejezés a lekérdezésnév, majd a lekérdezésnév használható aliasként.  
  
-   Ha a kifejezés `<collection_expression>`, akkor property_name, majd property_name használja a rendszer aliasként. Ha a kifejezés a lekérdezésnév, majd a lekérdezésnév használható aliasként.  
  
MIVEL`input_alias`  
  
Megadja, hogy a `input_alias` az alapul szolgáló gyűjtemény kifejezés által visszaadott értékek készlete.  
 
`input_alias`IN  
  
Megadja, hogy a `input_alias` értékeket a tömb összes tagjának minden az alapul szolgáló gyűjtemény kifejezés által visszaadott tömb keresztül léptetés készletét képviseli. Minden alapul szolgáló gyűjtemény-kifejezés nem tömb által visszaadott értéket a rendszer figyelmen kívül hagyja.  
  
`<collection_expression>`  
  
Meghatározza azt a gyűjteményt a dokumentumok beolvasása használható.  
  
`ROOT`  
  
Meghatározza, hogy a dokumentum olvassa be az alapértelmezés szerint a jelenleg csatlakoztatott gyűjtemény.  
  
`collection_name`  
  
Meghatározza, hogy a dokumentum olvassa be a megadott gyűjtemény. A gyűjtemény nevét meg kell egyeznie a jelenleg csatlakoztatott gyűjtemény nevét.  
  
`input_alias`  
  
Meghatározza, hogy a dokumentum be kell olvasni a megadott alias által meghatározott a más forrásból.  
  
`<collection_expression> '.' property_`  
  
Meghatározza, hogy a dokumentum be kell olvasni elérésével a `property_name` tulajdonság vagy tömbindex tömbelem által beolvasott minden dokumentumhoz megadott gyűjtemény kifejezés.  
  
`<collection_expression> '[' "property_name" | array_index ']'`  
  
Meghatározza, hogy a dokumentum be kell olvasni elérésével a `property_name` tulajdonság vagy tömbindex tömbelem által beolvasott minden dokumentumhoz megadott gyűjtemény kifejezés.  
  
**Megjegyzések**  
  
Összes alias megadott vagy következtetni a `<from_source>(`s) egyedinek kell lennie. A szintaxis `<collection_expression>.`property_name megegyezik a `<collection_expression>' ['"property_name"']'`. Azonban az utóbbi szintaxist használhat, ha az egyik tulajdonságnév nem azonosító karakternél.  
  
**Hiányzó tulajdonságok hiányzik a tömb elemei nem definiált értékek kezelése**  
  
Ha egy gyűjtemény kifejezés fér hozzá a Tulajdonságok vagy tömb elemeinek és, hogy az érték nem létezik, ezt az értéket figyelmen kívül hagyva, és további nincs feldolgozva.  
  
**Gyűjtemény kifejezés környezetben hatókörének beállítása**  
  
Egy gyűjtemény kifejezés gyűjtemény hatóköre vagy dokumentum-hatóköre lehet:  
  
-   Egy kifejezés a gyűjtemény hatóköre, ha az alapul szolgáló gyűjtemény kifejezés forrása vagy legfelső szintű vagy `collection_name`. Ilyen kifejezés közvetlenül a gyűjtemény lekért dokumentumok készletét reprezentálja, és nincs más gyűjtemény kifejezések feldolgozása függ.  
  
-   Egy kifejezés dokumentum hatókörű, ha az alapul szolgáló gyűjtemény kifejezés forrása `input_alias` bevezetett korábban a lekérdezésben. Ilyen kifejezés azon dokumentumok révén a fióknévnek gyűjteményhez társított készlethez tartozó dokumentumok hatókörében gyűjtemény kifejezés kiértékelése jelöli.  Eredő egy révén az egyes a dokumentumok az alapul szolgáló készlet gyűjtemény kifejezés kiértékelése készlet unióját lesz.  
  
**Illesztése**  
  
A jelenlegi kiadásban Azure Cosmos DB belső illesztések támogatja. További illesztési képességek érkeznek.

A belső illesztések egy teljes a a illesztésben részt vevő készlet keresztszorzatát eredményez. Az eredmény az N-módon csatlakozási olyan N-elem listának, ahol minden egyes érték szerepel a rekordban a résztvevő beállítása a csatlakozás a fióknévnek hozzárendelve, és ez az alias más záradékban Vezérlőpultjának érhető el.  
  
Az illesztés értékelése a programban részt vevő készletek környezetben hatókörétől függ:  
  
-  Illesztés közötti gyűjtemény- és a gyűjtemény hatóköre a B kiszolgálóra, egy határokon termékben eredményeit A és b készlet összes elemének beállítása
  
-   Készlet és B, set dokumentum hatókörbe tartozó illesztést egy dokumentum hatókörű set nyilvántartott egyes dokumentumok, a B beállítása A. kiértékelésével kapott összes készlet unióját eredményez.  
  
 A jelenlegi kiadásban egy gyűjtemény hatókörbe tartozó kifejezés maximum a lekérdezésfeldolgozó által támogatott.  
  
**Illesztések példái:**  
  
Vizsgáljuk meg FROM záradék a következő:`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Így minden forrás megadása `input_alias1, input_alias2, …, input_aliasN`. A FROM záradék N-listának (rekordot N értékekkel) adja vissza. A táblakonstruktor minden rekordjának összes gyűjtemény alias léptetés alatt az megfelelő készletek által visszaadott érték tartozik.  
  
*CSATLAKOZZON például 1, 2 forrásokat:*  
  
- Így `<from_source1>` kell gyűjtemény-hatókörű, és megfelelnek csoportjának {A, B, C}.  
  
- Így `<from_source2>` dokumentum hatókörű input_alias1 hivatkozó és képviselő beállítása:  
  
    {1, 2} számára`input_alias1 = A,`  
  
    a {3}`input_alias1 = B,`  
  
    {4, 5} számára`input_alias1 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2>` a következő rekordokat eredményezi:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
*CSATLAKOZZON például 2, 3 forrásokat:*  
  
- Így `<from_source1>` kell gyűjtemény-hatókörű, és megfelelnek csoportjának {A, B, C}.  
  
- Lehetővé teszik `<from_source2>` kell a dokumentum-hatókörű hivatkozó `input_alias1` készletek tartalmazzák:  
  
    {1, 2} számára`input_alias1 = A,`  
  
    a {3}`input_alias1 = B,`  
  
    {4, 5} számára`input_alias1 = C,`  
  
- Lehetővé teszik `<from_source3>` kell a dokumentum-hatókörű hivatkozó `input_alias2` készletek tartalmazzák:  
  
    {100, 200} számára`input_alias2 = 1,`  
  
    a {300}`input_alias2 = 3,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordokat eredményezi:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200-AS), (B, 3, 300)  
  
> [!NOTE]
> Más értékek a rekordokat hiánya `input_alias1`, `input_alias2`, amelynek a `<from_source3>` nem adott vissza semmilyen értéket.  
  
*KAPCSOLÓDÁS a példa 3, 3 forrásokat:*  
  
- < From_source1 > kell a gyűjtemény hatóköre, és megfelelnek {A, B, C} csoportjának segítségével.  
  
- Így `<from_source1>` kell gyűjtemény-hatókörű, és megfelelnek csoportjának {A, B, C}.  
  
- < From_source2 > hivatkozó input_alias1 dokumentum hatókörű, és készletek képviselő segítségével:  
  
    {1, 2} számára`input_alias1 = A,`  
  
    a {3}`input_alias1 = B,`  
  
    {4, 5} számára`input_alias1 = C,`  
  
- Így `<from_source3>` tartozni `input_alias1` készletek tartalmazzák:  
  
    {100, 200} számára`input_alias2 = A,`  
  
    a {300}`input_alias2 = C,`  
  
- A FROM záradék `<from_source1> JOIN <from_source2> JOIN <from_source3>` a következő rekordokat eredményezi:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200-AS) (A, 2, 100), (A, 2, 200-AS), C, 4, 300, (C, 5, 300)  
  
> [!NOTE]
> Ennek következtében határokon termék közötti `<from_source2>` és `<from_source3>` mert mindkét hatóköre azonos `<from_source1>`.  Ez 4 (2 x 2) eredményezett a érték 0 rekordokat B (1 x 0) értékkel rendelkező rekordokat és (2 x 1) 2 c-értékkel rekordokat  
  
**Lásd még:**  
  
 [SELECT záradékban](#bk_select_query)  
  
##  <a name="bk_where_clause"></a>A WHERE záradék  
 Adja meg a keresési feltételt a lekérdezés által visszaadott a dokumentumokhoz.  
  
 **Szintaxis**  
  
```  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **Argumentumok**  
  
-   `<filter_condition>`  
  
     Adja meg az állapotot, amelyet a dokumentumok vissza kell teljesülniük.  
  
-   `<scalar_expression>`  
  
     A kifejezés ki értékét képviselő. Tekintse meg a [skaláris kifejezések](#bk_scalar_expressions) című szakaszban talál információt.  
  
 **Megjegyzések**  
  
 Ahhoz, hogy a dokumentum vissza kell adni a megadott szűrő kifejezés feltétel igaz értéket kell adnia. Csak az IGAZ logikai értéket eleget tesz a feltételt, semmilyen más érték: nincs megadva, null, hamis, számot, tömb vagy objektum nem eleget tesz a feltétel.  
  
##  <a name="bk_orderby_clause"></a>ORDER BY záradék  
 Megadja a rendezési sorrendjét a lekérdezés által visszaadott eredmények.  
  
 **Szintaxis**  
  
```  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **Argumentumok**  
  
-   `<sort_specification>`  
  
     Megadja egy tulajdonság vagy a lekérdezés eredményhalmazából rendezéshez használandó kifejezést. A rendezési oszlop neve vagy oszlop aliasként adható meg.  
  
     Több rendezési oszlop adható meg. Nevének egyedinek kell lennie. A rendezési oszlopok az ORDER BY záradékban sorrendje határozza meg a szervezet a rendezett eredményhalmaz. Ez azt jelenti, hogy az eredménykészlet az első tulajdonság szerint van rendezve, és majd a rendezett lista van rendezve, a második tulajdonság, és így tovább.  
  
     Az ORDER BY záradékban hivatkozott oszlop nevét meg kell felelnie a kiválasztási listán bármelyik oszlop, vagy egy oszlop a tábla bármely kétértelműséget nélkül a FROM záradékban megadott.  
  
-   `<sort_expression>`  
  
     Megadja egy egyetlen tulajdonság vagy a lekérdezés eredményhalmazából rendezéshez használandó kifejezést.  
  
-   `<scalar_expression>`  
  
     Tekintse meg a [skaláris kifejezések](#bk_scalar_expressions) című szakaszban talál információt.  
  
-   `ASC | DESC`  
  
     Meghatározza, hogy a megadott oszlop értékeit rendezni kell növekvő vagy csökkenő sorrendben. ASC rendezi a legalacsonyabb értékét a legmagasabb érték. DESC rendezi a legmagasabb érték kisebb értékre. ASC az alapértelmezett rendezési sorrend. A legkisebb lehetséges értékek NULL értéket kell kezelni.  
  
 **Megjegyzések**  
  
 A lekérdezési szintaxis támogatja több sorrend tulajdonságai, amíg az Azure Cosmos DB lekérdezés futásidejű támogatja a rendezést csak egyetlen tulajdonság, és csak elleni tulajdonságnevek, azaz nem számított tulajdonságokhoz. Rendezés is szükséges, hogy az indexelési házirendet tartalmazza-e a tartományindexszel a tulajdonság és a megadott típus, a maximális pontosság. Tekintse meg a további részleteket az indexelési házirend dokumentációját.  
  
##  <a name="bk_scalar_expressions"></a>Skaláris kifejezések  
 Egy skaláris kifejezés szimbólumok és az beszerzése egyetlen érték kiértékelhető operátorok. Egyszerű kifejezések állandók, tulajdonsághivatkozást, tömb hivatkozásokkal, alias hivatkozik, vagy lehet függvényhívásokat. Egyszerű kifejezések összetett kifejezések operátorok használatával való egyesíthetők.  
  
 További részletek a értékek melyik skaláris kifejezést lehet: [állandók](#bk_constants) szakasz.  
  
 **Szintaxis**  
  
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
  
     Egy állandó értékét jelöli. Lásd: [állandók](#bk_constants) című szakaszban talál információt.  
  
-   `input_alias`  
  
     Által megadott értéket jelöli a `input_alias` bevezetett a `FROM` záradékban.  
    Ez az érték nem lehet garantáltan **nem definiált** –**nem definiált** kimarad a bemeneti értékeket.  
  
-   `<scalar_expression>.property_name`  
  
     A tulajdonság az objektum olyan értékét jelöli. Ha a tulajdonság nem létezik vagy tulajdonság egy értéket, amely nem objektum hivatkozik, akkor a kifejezés eredménye **nem definiált** érték.  
  
-   `<scalar_expression>'['"property_name"|array_index']'`  
  
     A tulajdonság neve képviseli `property_name` vagy az index tömbelem `array_index` objektum vagy tömb. Ha a/tulajdonságtömb-index nem létezik, vagy egy érték, amely nem objektum vagy tömb a/tulajdonságtömb-index hivatkozott, majd a kifejezés eredménye nem definiált érték.  
  
-   `unary_operator <scalar_expression>`  
  
     Egyetlen értéket alkalmazott operátor jelöli. Lásd: [operátorok](#bk_operators) című szakaszban talál információt.  
  
-   `<scalar_expression> binary_operator <scalar_expression>`  
  
     Két érték alkalmazott operátor jelöli. Lásd: [operátorok](#bk_operators) című szakaszban talál információt.  
  
-   `<scalar_function_expression>`  
  
     Egy adott hívás eredménye által megadott értéket jelöli.  
  
-   `udf_scalar_function`  
  
     A felhasználó nevét meghatározott skaláris függvényt.  
  
-   `builtin_scalar_function`  
  
     A beépített skaláris függvény neve.  
  
-   `<create_object_expression>`  
  
     Által megadott tulajdonságokkal rendelkező új objektum létrehozása és azok értékei jelöli.  
  
-   `<create_array_expression>`  
  
     Hozzon létre egy új tömb elemként megadott értékekkel kapott értéket jelöli.  
  
-   `parameter_name`  
  
     A megadott paraméternév egy értékét jelöli. Paraméterek nevei rendelkeznie kell egyetlen @ karaktert.  
  
 **Megjegyzések**  
  
 Amikor egy beépített vagy felhasználói függvény skaláris összes argumentumot meg kell határozni. Ha bármelyik argumentum nincs megadva, a függvény nem hívható, és az eredmény nem definiált.  
  
 Egy objektum létrehozásakor bármely tulajdonság nem definiált érték hozzárendelt kihagyja, és nem szerepel a létrehozott objektum.  
  
 Ha egy tömb, bármely elemérték létrehozható, amely hozzá van rendelve **nem definiált** kihagyja és a létrehozott objektum nem szerepel érték. Ennek hatására a meghatározott ezt az elemet a elvégezze úgy, hogy a létrehozott tömb fog nem a kihagyott indexek.  
  
##  <a name="bk_operators"></a>Operátorok  
 Ez a szakasz ismerteti a támogatott operátorok. Minden egyes operátor pontosan egy kategóriát is hozzárendelhető.  
  
 Lásd: **operátor kategóriák** részletes, az alábbi táblázatban kezelésére vonatkozó **nem definiált** érték található, a bemeneti értékek és kezelésére vonatkozó értékek nem egyező típusok szemben támasztott követelményeit.  
  
 **Operátor kategóriák:**  
  
|**Kategória**|**Részletek**|  
|-|-|  
|**aritmetikai**|Operátor száma kell input(s) vár. Kimeneti az a szám. Ha a bemeneti adatok bármelyike **nem definiált** vagy típustól eltérő, majd az eredmények számát **nem definiált**.|  
|**Bitenkénti**|Operátor vár input(s) 32 bites előjeles egész száma kell lennie. Kimeneti is 32 bites, előjeles egész szám.<br /><br /> Nem egész értéket a rendszer kerekíti. Pozitív értéket lefelé, negatív értékeket kerekíti.<br /><br /> Bármely érték, amely a 32 bites egész tartományon kívül esik a két tartozó hexadecimális utolsó 32-bites megtételével konvertálja.<br /><br /> Ha a bemeneti adatok bármelyike **nem definiált** vagy adjon meg másik számot, akkor az eredmény **nem definiált**.<br /><br /> **Megjegyzés:** fenti a rendszer nem kompatibilis a JavaScript bitenkénti operátor viselkedését.|  
|**logikai**|Operátor Boolean(s) kell input(s) vár. Kimeneti is olyan logikai érték.<br />Ha a bemeneti adatok bármelyike **nem definiált** vagy adjon meg eltérő logikai érték, akkor az eredmény lesz **nem definiált**.|  
|**összehasonlítása**|Operátor azonos típusú és nem lehet nem definiált input(s) vár. Olyan logikai érték eredménye.<br /><br /> Ha a bemeneti adatok bármelyike **nem definiált** vagy a bemeneti adatok különböző rendelkezik, majd az eredmény **nem definiált**.<br /><br /> Lásd: **összehasonlított értékek rendezési** tábla értékhez rendelés részleteit.|  
|**karakterlánc**|Operátor karakterlánc(ok) kell input(s) vár. Kimenet: karakterlánc.<br />Ha a bemeneti adatok bármelyike **nem definiált** vagy írja be a másik karakterláncot, majd az eredmény **nem definiált**.|  
  
 **Az egyoperandusú operátorral:**  
  
|**Name (Név)**|**Operátor**|**Részletek**|  
|-|-|-|  
|**aritmetikai**|+<br /><br /> -|A szám értékét adja vissza.<br /><br /> Bitenkénti negálást. Számú értéket ad vissza negated.|  
|**Bitenkénti**|~|Egyesek komplemens számnak. Az érték egy szám kiegészítése adja vissza.|  
|**Logikai**|**NEM**|Tagadásának. Beolvasása negated logikai érték.|  
  
 **Bináris operátor:**  
  
|**Name (Név)**|**Operátor**|**Részletek**|  
|-|-|-|  
|**aritmetikai**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|Hozzáadását.<br /><br /> Kivonás.<br /><br /> Szorzást végezhet.<br /><br /> Osztás.<br /><br /> Modulációs.|  
|**Bitenkénti**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|Bitenkénti vagy.<br /><br /> Bitenkénti és művelet<br /><br /> Bitenkénti kizáró vagy.<br /><br /> Balra Tolást.<br /><br /> Jobbra Tolást.<br /><br /> Nulla-Kitöltés jobbra Tolást.|  
|**logikai**|**ÉS**<br /><br /> **VAGY**|Logikai együtt. Visszaadja **igaz** , ha mindkét argumentuma **igaz**, adja vissza **hamis** ellenkező esetben.<br /><br /> Logikai együtt. Visszaadja **igaz** , ha mindkét argumentuma **igaz**, adja vissza **hamis** ellenkező esetben.|  
|**összehasonlítása**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|Egyenlő. Visszaadja **igaz** Ha az argumentum értéke, akkor adja vissza **hamis** egyéb.<br /><br /> Nem egyenlő. Visszaadja **igaz** Ha az argumentum nem egyenlő, adja vissza **hamis** egyéb.<br /><br /> Nagyobb, mint. Beolvasása **igaz** első argumentum értéke nagyobb, mint a második, ha vissza **hamis** más módon.<br /><br /> Nagyobb vagy egyenlő. Beolvasása **igaz** első argumentum értéke nagyobb vagy egyenlő irányából a második, ha vissza **hamis** más módon.<br /><br /> Kisebb, mint. Beolvasása **igaz** Ha első argumentum nem kisebb, mint a második egy visszatérési **hamis** más módon.<br /><br /> Kisebb vagy egyenlő, mint. Beolvasása **igaz** első argumentum értéke kisebb vagy egyenlő, mint a második érték, ha vissza **hamis** más módon.<br /><br /> A Coalesce. A második argumentum adja vissza, ha az első argumentum egy **nem definiált** érték.|  
|**Karakterlánc**|**&#124;&#124;**|Kapott. Mindkét argumentumot összefűzése adja vissza.|  
  
 **Ternáris kezelők:**  
  
|Ternáris operátor|?|A második argumentum adja vissza, ha az első argumentum értéke **igaz**; ellenkező esetben térjen vissza a harmadik argumentum.|  
|-|-|-|  
  
 **Az összehasonlított értékek sorrendje**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Nincs definiálva**|Nem hasonlítható össze.|  
|**NULL értékű**|Egyetlen érték: **null értékű**|  
|**Szám**|Természetes valós szám.<br /><br /> Negatív végtelen értéke kisebb, mint bármely más számértéket.<br /><br /> Pozitív végtelen értéke nagyobb, mint bármely más számértéket. **NaN** értéke nem hasonlítható össze. Összehasonlítva az **NaN** eredményez **nem definiált** érték.|  
|**Karakterlánc**|Lexicographical sorrendje.|  
|**A tömb**|Nincs rendezést, de egyenlő.|  
|**Objektum**|Nincs rendezést, de egyenlő.|  
  
 **Megjegyzések**  
  
 Az Azure Cosmos Adatbázisba a típusú értékeket gyakran nem ismert amíg ténylegesen az adatbázisból beolvasott. A lekérdezések hatékony végrehajtási támogatásához az operátorok többsége a szigorú szemben támasztott követelményeit. Operátorok önmagában is ne hajtsa végre implicit konverzió.  
  
 Ez azt jelenti, hogy a lekérdezés, például: válasszon * a ROOT r WHERE r.Age = 21 csak adja vissza tulajdonság kora dokumentumok akkora 21. Tulajdonság kora egyenlő a karakterlánc a "21" vagy "0021" karakterlánc-dokumentumok nem fog egyezni, mint a kifejezés "21" = 21 kiértékeli a nincs megadva. Ez lehetővé teszi a hatékonyabb felhasználása indexek, mert a keresés egy adott érték (azaz számú 21) gyorsabb, mint lehetséges megegyezik (21 szám vagy karakterlánc "21", "021", "21.0"...) határozatlan számú keresése. Ez eltér hogyan értékeli ki a JavaScript a felügyelői eltérő típusú értékeket.  
  
 **Tömbök és objektumok egyenlőség és összehasonlítása**  
  
 A tartomány operátorral egymáshoz csatolt tömb vagy objektum értékek összehasonlításával (>, > =, <, < =) eredményez, mivel nem az objektum és tömb meghatározásának sorrendje nincs definiálva. Azonban a egyenlőség operátorral egymáshoz csatolt (=,! =, <>) támogatott és értékek szerkezetileg össze.  
  
 Tömbök azonosak, ha mindkét értéktömbök azonos számú elemből állnak, és megfelelő pozíciók elemekben is egyenlő. Ha összehasonlítja a kulcspár elemek eredményezi nincs definiálva, array összehasonlítása eredménye nem definiált.  
  
 Objektumok azonosak, ha mindkét objektum definiált azonos jellemzőkkel rendelkezik, és a megfelelő tulajdonságainak értékei is egyenlő. Ha nincs definiálva a tulajdonság értékek eredményez a bármely két összehasonlítása, objektum összehasonlítás eredménye nincs definiálva.  
  
##  <a name="bk_constants"></a>Állandók  
 Egy konstans, más néven szövegkonstans vagy skaláris, egy meghatározott értéket jelölő szimbólumot. Egy konstans formátumát a ezt az értéket függ.  
  
 **Skaláris adattípusokat támogatja:**  
  
|**Típus**|**Értékek sorrendje**|  
|-|-|  
|**Nincs definiálva**|Egyetlen érték: **nincs megadva**|  
|**NULL értékű**|Egyetlen érték: **null értékű**|  
|**Logikai érték**|Értékek: **hamis**, **igaz**.|  
|**Szám**|Egy kétszeres pontosságú lebegőpontos számnál, szabványos IEEE 754.|  
|**Karakterlánc**|Nulla vagy több Unicode-karaktereket sorozata. Karakterláncok egyetlen vagy dupla idézőjelek között kell foglalni.|  
|**A tömb**|Nulla vagy több elemek sorrendjét. Minden elem meghatározatlan kivételével minden skaláris adattípusú érték lehet.|  
|**Objektum**|Egy nulla vagy több név/érték párok rendezetlen készlete. Értéke a Unicode-karakterláncot, kivéve értéke lehet bármely skaláris adattípusú, **meghatározatlan**.|  
  
 **Szintaxis**  
  
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
  
     Meghatározatlan típusú érték nincs definiálva jelöli.  
  
2.  `<null_constant>; null`  
  
     Jelöli **null** típusú érték **Null**.  
  
3.  `<boolean_constant>`  
  
     Logikai érték típusú konstans jelöli.  
  
4.  `false`  
  
     Jelöli **hamis** logikai típusú érték.  
  
5.  `true`  
  
     Jelöli **igaz** logikai típusú érték.  
  
6.  `<number_constant>`  
  
     Egy konstans jelöli.  
  
7.  `decimal_literal`  
  
     Decimális literálok képviselt decimális jelöléssel, vagy tudományos jelölés használatával számok.  
  
8.  `hexadecimal_literal`  
  
     Hexadecimális literálok értékét "0 x", egy vagy több hexadecimális számjegy követ előtag számok.  
  
9. `<string_constant>`  
  
     Egy karakterlánc típusú konstans jelöli.  
  
10. `string _literal`  
  
     A szövegkonstansok olyan Unicode karakterláncok sorozatát nulla vagy több Unicode-karaktereket vagy escape-karaktersorozatokat. A szövegkonstansok vannak szimpla zárójelek között (aposztróf: ") vagy dupla idézőjel (idézőjel:").  
  
 Következő escape-karaktersorozatokat engedélyezettek:  
  
|**Escape-karaktersorozatot**|**Leírás**|**Unicode-karakter**|  
|-|-|-|  
|\\'|aposztróf (')|U + 0027|  
|\\"|az idézőjel (")|U + 0022|  
|\\\|fordított solidus (\\)|U + 005C|  
|\\/|solidus (/)|U + 002F|  
|\b|BACKSPACE|U + 0008|  
|\f|Lapdobás|U + 000C|  
|\n|soremelés|U + 000A|  
|\r|kocsivissza|U + 000D|  
|\t|Lap|U + 0009|  
|\uXXXX|4 hexadecimális számjegy által megadott Unicode-karakter.|U + XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a>Lekérdezés teljesítmény irányelvek  
 Ahhoz, hogy a lekérdezés egy nagy méretű gyűjtemény hatékony futtatását akkor használjon szűrőket, amelyek egy vagy több indexek keresztül szolgálhatók ki.  
  
 A következő szűrőket veszi figyelembe a keresési index:  
  
-   Egyenlő operátor (=) használata a dokumentum az elérésiút-kifejezés és egy konstans.  
  
-   Tartomány operátorokkal (<, \<=, >, > =) számú állandók és elérésiút-kifejezésben.  
  
-   A dokumentum az elérésiút-kifejezés egy kifejezést, amely azonosítja a hivatkozott adatbázis gyűjteményből dokumentumok állandó elérési útját jelöli.  
  
 **A dokumentum az elérésiút-kifejezés**  
  
 Dokumentum elérési kifejezések kifejezések szerepelnek, amelyek egy elérési utat az indexelő tulajdonság vagy tömb vizsgáztatók egy dokumentumot, az adatbázis gyűjtemény dokumentumok érkező keresztül. Az elérési út segítségével adja meg a szűrő közvetlenül a dokumentumok az adatbázis gyűjteményben belül hivatkozott értékek helyét.  
  
 Kifejezés figyelembe kell venni az elérésiút-kifejezésben, a következőket:  
  
1.  A gyűjtemény legfelső szintű közvetlenül hivatkozik.  
  
2.  Hivatkozási tulajdonság vagy állandó tömb indexelő néhány dokumentum elérési út kifejezés  
  
3.  Az alias, amely az egyes dokumentum elérésiút-kifejezés hivatkozik.  
  
     **Szintaxis konvenciók**  
  
     Az alábbi táblázat a DocumentDB API lekérdezési nyelv hivatkozási szintaxist leíró konvenciókat ismerteti.  
  
    |**Egyezmény**|**A használt**|  
    |-|-|    
    |NAGYBETŰK|Nem betűérzékeny kulcsszavakat.|  
    |kisbetűk|Kis-és nagybetűket kulcsszavakat.|  
    |\<nonterminal >|Nem, külön definiált.|  
    |\<nonterminal >:: =|A nonterminal szintaxis meghatározása.|  
    |other_terminal|Terminálszolgáltatások (jogkivonat) részletesen szavakat.|  
    |Azonosítója|Azonosítója. Lehetővé teszi, hogy a következő karaktereket csak: a – z A – Z 0 – 9 _First karakter nem lehet egy számjegy.|  
    |"karakterlánc"|Idézőjelek közé zárt karakterlánc. Lehetővé teszi, hogy minden érvényes karakterláncot. Tekintse meg a string_literal leírása.|  
    |"szimbólum"|A szintaxis részét képező literális szimbólum.|  
    |&#124; (a függőleges vonal)|Alternatívák szintaxis elemekhez. Csak a megadott elemek egyikét használhatja.|  
    |[] /(brackets)|Zárójelek közé egy vagy több választható elemek.|  
    |[ ,...n ]|Azt jelzi, hogy az előző elemet lehet ismételt n számú alkalommal. Az előfordulások vesszővel kell elválasztani.|  
    |[ ...n ]|Azt jelzi, hogy az előző elemet lehet ismételt n számú alkalommal. Az előfordulások üres cellákat el egymástól.|  
  
##  <a name="bk_built_in_functions"></a>Beépített funkciók  
 Azure Cosmos-adatbázis SQL számos beépített funkciót biztosít. A beépített függvények kategóriák listája látható.  
  
|Függvény|Leírás|  
|--------------|-----------------|  
|[Matematikai funkciók](#bk_mathematical_functions)|A matematikai funkciók hajtsa végre a számítás, rendszerint bemeneti értékeket, mint szerepkör argumentumokban szolgálnak, és a visszaadandó numerikus érték alapján.|  
|[Írja be az ellenőrzési funkciók](#bk_type_checking_functions)|A típus ellenőrzési funkciók lehetővé teszik az SQL-lekérdezések lévő kifejezés típusa.|  
|[Karakterlánc-függvények](#bk_string_functions)|A karakterlánc funkciók végrehajtania egy műveletet a bemeneti karakterlánc-értékkel, és egy karakterlánc, a numerikus és logikai értéket adja vissza.|  
|[A tömb funkciók](#bk_array_functions)|A tömb funkciók egy logikai érték vagy tömb érték, egy tömb bemeneti érték és a numerikus visszatérési művelet végrehajtása.|  
|[Térbeli funkciók](#bk_spatial_functions)|A térbeli funkciók végrehajtania egy műveletet a olyan térbeli objektum beviteli értéket, és numerikus vagy logikai érték visszaadása.|  
  
###  <a name="bk_mathematical_functions"></a>Matematikai funkciók  
 Az alábbi feladatokat hajtsa végre a számítás, rendszerint bemeneti értékeket, mint szerepkör argumentumokban szolgálnak, és a visszaadandó numerikus érték alapján.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ARCCOS](#bk_acos)|[ARCSIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[FELSŐ HATÁR](#bk_ceiling)|  
|[COS](#bk_cos)|[TŰZ](#bk_cot)|[FOK](#bk_degrees)|  
|[EXP](#bk_exp)|[EMELET](#bk_floor)|[NAPLÓ](#bk_log)|  
|[LOG10](#bk_log10)|[A PI](#bk_pi)|[ENERGIAGAZDÁLKODÁSI](#bk_power)|  
|[RADIÁNBAN MEGADOTT SZÖG](#bk_radians)|[CIKLIKUS](#bk_round)|[EG](#bk_sin)|  
|[SQRT](#bk_sqrt)|[NÉGYZETES](#bk_square)|[BEJELENTKEZÉS](#bk_sign)|  
|[TAN](#bk_tan)|[CSONK](#bk_trunc)||  
  
####  <a name="bk_abs"></a>ABS  
 A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza.  
  
 **Szintaxis**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példában három eltérő számú ABS funkciójával eredményeit jeleníti meg.  
  
```  
SELECT ABS(-1), ABS(0), ABS(1)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 1, $2: 0, $3: 1}]  
```  
  
####  <a name="bk_acos"></a>ARCCOS  
 A szöget adja vissza, az radiánban megadott szög, amelynek koszinusza a megadott numerikus kifejezés; más néven koszinuszát.  
  
 **Szintaxis**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a-1 ARCCOS adja vissza.  
  
```  
SELECT ACOS(-1)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a>ARCSIN  
 A szög radiánban megadott szög, amelynek szinusza a megadott numerikus kifejezést ad vissza. Ez rövidítése szinuszát.  
  
 **Szintaxis**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a-1 ARCSIN adja vissza.  
  
```  
SELECT ASIN(-1)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a>ATAN  
 A szög radiánban megadott szög, amelynek tangense a megadott numerikus kifejezést ad vissza. Ezt arkusz is nevezik.  
  
 **Szintaxis**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a megadott érték ATAN adja vissza.  
  
```  
SELECT ATAN(-45.01)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a>ATN2  
 Az arkusz tangens / x, y radiánban kifejezett tag értékét adja vissza.  
  
 **Szintaxis**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa alapján számítja ki a megadott ATN2 x és y összetevőket.  
  
```  
SELECT ATN2(35.175643, 129.44)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a>FELSŐ HATÁR  
 A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés.  
  
 **Szintaxis**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa bemutatja a pozitív szám, negatív és nulla érték a felső határ függvénnyel.  
  
```  
SELECT CEILING(123.45), CEILING(-123.45), CEILING(0.0)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 124, $2: -123, $3: 0}]  
```  
  
####  <a name="bk_cos"></a>COS  
 Koszinuszát trigonometric a megadott szög radiánban, a megadott kifejezésben.  
  
 **Szintaxis**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a megadott szög COS számítja ki.  
  
```  
SELECT COS(14.78)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a>TŰZ  
 A megadott szög trigonometric kotangensét adja meg a megadott numerikus kifejezés radiánban.  
  
 **Szintaxis**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a megadott szög tűz számítja ki.  
  
```  
SELECT COT(124.1332)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a>FOK  
 A megfelelő szöget adja vissza, az a radiánban megadott szög fokban megadva.  
  
 **Szintaxis**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a szög radiánban PI/2 fok számát adja vissza.  
  
```  
SELECT DEGREES(PI()/2)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 90}]  
```  
  
####  <a name="bk_floor"></a>EMELET  
 A legnagyobb egész számot ad vissza kisebb vagy egyenlő, mint a megadott numerikus kifejezés.  
  
 **Szintaxis**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa bemutatja a pozitív szám, negatív és nulla érték a EMELET függvénnyel.  
  
```  
SELECT FLOOR(123.45), FLOOR(-123.45), FLOOR(0.0)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 123, $2: -124, $3: 0}]  
```  
  
####  <a name="bk_exp"></a>EXP  
 Az exponenciális a megadott numerikus kifejezés értékét adja vissza.  
  
 **Szintaxis**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Megjegyzések**  
  
 A konstans **e** (2.718281...), a természetes logaritmus alapja van.  
  
 Egy szám hatványát a állandó **e** számának állandó. Például EXP(1.0) = e ^ 1.0 = 2.71828182845905 és EXP(10) = e ^ 10 = 22026.4657948067.  
  
 Egy szám természetes alapú logaritmus exponenciális az a szám saját magát: EXP (napló (n)) = n. Az exponenciális egy szám természetes alapú logaritmusát az a szám, és saját magát: napló (EXP (n)) = n.  
  
 **Példák**  
  
 A következő példa egy változót deklarál, és a változóhoz (10) exponenciális értékét adja vissza.  
  
```  
SELECT EXP(10)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 22026.465794806718}]  
```  
  
 Az alábbi példa 20 natural logarithm és a természetes alapú logaritmusát 20 exponenciális exponenciális értékét adja vissza. Mivel ezek a funkciók inverz feladatai egymástól, az eredményül kapott értéket a lebegőpontos értékek mindkét esetben kerekítési 20.  
  
```  
SELECT EXP(LOG(20)), LOG(EXP(20))  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 19.999999999999996, $2: 20}]  
```  
  
####  <a name="bk_log"></a>NAPLÓ  
 A megadott numerikus kifejezés a természetes alapú logaritmusát adja vissza.  
  
 **Szintaxis**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
-   `base`  
  
     Nem kötelező numerikus argumentum beállítja a logaritmus alapja.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Megjegyzések**  
  
 Alapértelmezés szerint LOG() a természetes alapú logaritmusát adja vissza. A logaritmus alapja. a választható alap paraméter segítségével módosíthatja egy másik értéket.  
  
 A természetes alapú logaritmus alapja a logaritmus alapja **e**, ahol **e** megegyezik egy ésszerűtlen állandó körülbelül 2.718281828.  
  
 Az exponenciális egy szám természetes alapú logaritmusát az a szám saját magát: napló (EXP (n)) = n. Egy szám természetes alapú logaritmus exponenciális az a szám, és saját magát: EXP (napló (n)) = n.  
  
 **Példák**  
  
 A következő példa egy változót deklarál, és a változóhoz (10) logaritmusát értékét adja vissza.  
  
```  
SELECT LOG(10)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 2.3025850929940459}]  
```  
  
 A következő példa a naplófájl kiszámítja az egy szám hatványát.  
  
```  
SELECT EXP(LOG(10))  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a>LOG10  
 A megadott numerikus kifejezés a 10-es alapú logaritmusát adja vissza.  
  
 **Szintaxis**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Megjegyzések**  
  
 A LOG10 és a TÁPKÁBELEK funkciók intenzitásfokozatok kapcsolódik egy másik. Például 10 ^ LOG10(n) = n.  
  
 **Példák**  
  
 A következő példa egy változót deklarál, és a változóhoz (100) LOG10 értékét adja vissza.  
  
```  
SELECT LOG10(100)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 2}]  
```  
  
####  <a name="bk_pi"></a>A PI  
 A konstans PI értékét adja vissza.  
  
 **Szintaxis**  
  
```  
PI ()  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a PI értékét adja vissza.  
  
```  
SELECT PI()  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a>ENERGIAGAZDÁLKODÁSI  
 A megadott kifejezés értékét adja vissza a megadott hatványát.  
  
 **Szintaxis**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
-   `y`  
  
     A teljesítmény, amelyhez indíthat `numeric_expression`.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa azt mutatja be egy számot (az a szám adatkocka) 3 hatványra emelése.  
  
```  
SELECT POWER(2, 3), POWER(2.5, 3)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 8, $2: 15.625}]  
```  
  
####  <a name="bk_radians"></a>RADIÁNBAN MEGADOTT SZÖG  
 Vissza a radiánban megadott szög, ha egy numerikus kifejezés fokban, is meg kell adni.  
  
 **Szintaxis**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa néhány szögek fogadja bemeneti adatként, és visszaadja a hozzájuk tartozó radián értékek.  
  
```  
SELECT RADIANS(-45.01), RADIANS(-181.01), RADIANS(0), RADIANS(0.1472738), RADIANS(197.1099392)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{  
       "$1": -0.7855726963226477,  
       "$2": -3.1592204790349356,  
       "$3": 0,  
       "$4": 0.0025704127119236249,  
       "$5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a>CIKLIKUS  
 Egy numerikus érték, a legközelebbi egész értéket kerekítve adja vissza.  
  
 **Szintaxis**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példában a következő pozitív és negatív számokat a legközelebbi egész számra kerekít.  
  
```  
SELECT ROUND(2.4), ROUND(2.6), ROUND(2.5), ROUND(-2.4), ROUND(-2.6)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 2, $2: 3, $3: 3, $4: -2, $5: -3}]  
```  
  
####  <a name="bk_sign"></a>BEJELENTKEZÉS  
 A pozitív (+ 1), nulla (0) vagy a megadott numerikus kifejezés mínuszjel (-1) adja vissza.  
  
 **Szintaxis**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa ad vissza a bejelentkezési értékek számának -2 2.  
  
```  
SELECT SIGN(-2), SIGN(-1), SIGN(0), SIGN(1), SIGN(2)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: -1, $2: -1, $3: 0, $4: 1, $5: 1}]  
```  
  
####  <a name="bk_sin"></a>EG  
 Szinuszát trigonometric a megadott szög radiánban, a megadott kifejezésben.  
  
 **Szintaxis**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a megadott szög Szinusz számítja ki.  
  
```  
SELECT SIN(45.175643)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a>SQRT  
 A megadott numerikus érték négyzetgyökét adja vissza.  
  
 **Szintaxis**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa a szögletes gyökerek számok 1-3 adja vissza.  
  
```  
SELECT SQRT(1), SQRT(2.0), SQRT(3)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 1, $2: 1.4142135623730952, $3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a>NÉGYZETES  
 Kiszámítja a megadott numerikus érték.  
  
 **Szintaxis**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa számok 1-3 négyzetének adja vissza.  
  
```  
SELECT SQUARE(1), SQUARE(2.0), SQUARE(3)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 1, $2: 4, $3: 9}]  
```  
  
####  <a name="bk_tan"></a>TAN  
 A megadott szög tangensét adja vissza radiánban, a megadott kifejezésben.  
  
 **Szintaxis**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa PI () tangensét számítja ki / 2.  
  
```  
SELECT TAN(PI()/2);  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a>CSONK  
 Egy numerikus érték, csak az a legközelebbi egész értéket ad vissza.  
  
 **Szintaxis**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
-   `numeric_expression`  
  
     Van egy numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példában a következő pozitív és negatív számokat a legközelebbi egész számra kerekít.  
  
```  
SELECT TRUNC(2.4), TRUNC(2.6), TRUNC(2.5), TRUNC(-2.4), TRUNC(-2.6)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{$1: 2, $2: 2, $3: 2, $4: -2, $5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a>Írja be az ellenőrzési funkciók  
 A következő funkciókat támogatja a bemeneti értékekkel ellenőrzése típust, és minden egyes logikai értéket adja vissza.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a>IS_ARRAY  
 Azt jelzi, hogy ha a megadott kifejezés típusú tömb egy logikai értéket ad vissza.  
  
 **Szintaxis**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_ARRAY funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: false, $6: true}]  
```  
  
####  <a name="bk_is_bool"></a>IS_BOOL  
 Azt jelzi, hogy ha a megadott kifejezés típusa olyan logikai érték logikai érték beolvasása.  
  
 **Szintaxis**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_BOOL funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{$1: true, $2: false, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_defined"></a>IS_DEFINED  
 Jelzi, ha a tulajdonság van rendelve egy érték logikai érték beolvasása.  
  
 **Szintaxis**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa ellenőrzi, hogy a megadott JSON-dokumentum tulajdonság. Az első igaz értéket ad vissza, mert "a" jelen, de a második hamis értéket ad vissza, mert hiányzik a "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a), IS_DEFINED({ "a" : 5 }.b)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{  
       "$1": true,    
       "$2": false   
   }]  
```  
  
####  <a name="bk_is_null"></a>IS_NULL  
 Visszaad egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa null.  
  
 **Szintaxis**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_NULL funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: false, $4: true, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_number"></a>IS_NUMBER  
 Azt jelzi, hogy ha a típus a megadott kifejezés több olyan logikai értéket ad vissza.  
  
 **Szintaxis**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_NULL funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{$1: false, $2: true, $3: false, $4: false, $5: false, $6: false}]  
```  
  
####  <a name="bk_is_object"></a>IS_OBJECT  
 Azt jelzi, hogy ha a megadott kifejezés típusa egy JSON-objektum egy logikai értéket ad vissza.  
  
 **Szintaxis**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_OBJECT funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: false, $4: false, $5: true, $6: false}]  
```  
  
####  <a name="bk_is_primitive"></a>IS_PRIMITIVE  
 Azt jelzi, hogy ha a megadott kifejezés típusa egy primitív egy logikai értéket ad vissza (string, Boolean, numerikus vagy null értékű).  
  
 **Szintaxis**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_PRIMITIVE funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": true, "$2": true, "$3": true, "$4": true, "$5": false, "$6": false, "$7": false}]  
```  
  
####  <a name="bk_is_string"></a>IS_STRING  
 Azt jelzi, hogy ha a megadott kifejezés típusa karakterlánc egy logikai értéket ad vissza.  
  
 **Szintaxis**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumentumok**  
  
-   `expression`  
  
     Ez bármilyen érvényes kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa objektumok JSON logikai, számot, NULL értékű karakterlánc, objektum, a tömb és IS_STRING funkcióval nem definiált típusok ellenőrzi.  
  
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
  
 Ez az eredményhalmaz.  
  
```  
[{$1: false, $2: false, $3: true, $4: false, $5: false, $6: false}]  
```  
  
###  <a name="bk_string_functions"></a>Karakterlánc  
 A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-értékkel, és a karakterlánc, a numerikus és logikai értéket adja vissza.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[TARTALMAZZA](#bk_contains)|[MEGADOTT MÓDON VÉGZŐDŐ](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[BALRA](#bk_left)|[HOSSZA](#bk_length)|  
|[ALACSONYABB](#bk_lower)|[LTRIM](#bk_ltrim)|[CSERÉLJE LE](#bk_replace)|  
|[REPLIKÁLÁS](#bk_replicate)|[FORDÍTOTT](#bk_reverse)|[JOBBRA](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH ELEMNEK](#bk_startswith)|[SUBSTRING](#bk_substring)|  
|[FELSŐ](#bk_upper)|||  
  
####  <a name="bk_concat"></a>CONCAT  
 Karakterlánc, amely legalább két karakterlánc-értékek hozzáfűzésével eredményét adja vissza.  
  
 **Szintaxis**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa a megadott értékek összefűzött karakterláncot ad vissza.  
  
```  
SELECT CONCAT("abc", "def")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "abcdef"}  
```  
  
####  <a name="bk_contains"></a>TARTALMAZZA  
 Visszaadja egy logikai, amely jelzi, hogy az első karakterlánc-kifejezés tartalmazza a második.  
  
 **Szintaxis**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa ellenőrzi, ha az "abc" tartalmazza az "ab", és tartalmazza a "d".  
  
```  
SELECT CONTAINS("abc", "ab"), CONTAINS("abc", "d")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": true, "$2": false}]  
```  
  
####  <a name="bk_endswith"></a>MEGADOTT MÓDON VÉGZŐDŐ  
 Adja vissza egy logikai, amely jelzi, hogy az első karakterlánc-kifejezés a második végződik.  
  
 **Szintaxis**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa adja vissza, az "abc" karakterlánccal végződik-e a "b" és "bc".  
  
```  
SELECT ENDSWITH("abc", "b"), ENDSWITH("abc", "bc")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_index_of"></a>INDEX_OF  
 A második első előfordulásának kezdőpozícióját adja vissza karakterlánc-kifejezés az első megadott karakterlánc-kifejezés vagy -1, ha a karakterlánc nem található.  
  
 **Szintaxis**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példában az "abc" belül különböző karakterláncrészletek indexét adja vissza.  
  
```  
SELECT INDEX_OF("abc", "ab"), INDEX_OF("abc", "b"), INDEX_OF("abc", "c")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 0, "$2": 1, "$3": -1}]  
```  
  
####  <a name="bk_left"></a>BALRA  
 A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza.  
  
 **Szintaxis**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa a különböző hosszúságú értékek "abc" bal oldali részét adja vissza.  
  
```  
SELECT LEFT("abc", 1), LEFT("abc", 2)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "a", "$2": "ab"}]  
```  
  
####  <a name="bk_length"></a>HOSSZA  
 A megadott karakterlánc-kifejezés karakterek számát adja vissza.  
  
 **Szintaxis**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példában a karakterlánc hosszát adja vissza.  
  
```  
SELECT LENGTH("abc")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_lower"></a>ALACSONYABB  
 Egy karakterlánc-kifejezés után nagybetűt adatok kisbetűssé alakításával adja vissza.  
  
 **Szintaxis**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan ALACSONYABB használandó a lekérdezésben.  
  
```  
SELECT LOWER("Abc")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a>LTRIM  
 Egy karakterlánc-kifejezés adja vissza, után eltávolítja a kezdő üres.  
  
 **Szintaxis**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan LTRIM használható egy lekérdezésben.  
  
```  
SELECT LTRIM("  abc"), LTRIM("abc"), LTRIM("abc   ")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "abc", "$2": "abc", "$3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a>CSERÉLJE LE  
 Megadott karakterlánc-érték összes előfordulását lecseréli egy másik karakterlánc.  
  
 **Szintaxis**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan használja a név FELÜLÍRANDÓ a lekérdezésben.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a>REPLIKÁLÁS  
 A megadott számú alkalommal megismétel egy karakterlánc-érték.  
  
 **Szintaxis**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan használja REPLIKÁLJA a lekérdezésben.  
  
```  
SELECT REPLICATE("a", 3)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a>FORDÍTOTT  
 A Fordított sorrend egy karakterlánc értékét adja vissza.  
  
 **Szintaxis**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan NÉVKERESÉSI használatára a lekérdezésben.  
  
```  
SELECT REVERSE("Abc")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "cbA"}]  
```  
  
####  <a name="bk_right"></a>JOBBRA  
 A megadott számú karakterből álló karakterlánc jobb oldali részét adja vissza.  
  
 **Szintaxis**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa a különböző hosszúságú értékek "abc" jobb oldali részét adja vissza.  
  
```  
SELECT RIGHT("abc", 1), RIGHT("abc", 2)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "c", "$2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a>RTRIM  
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a záró szóközöket.  
  
 **Szintaxis**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan RTRIM használható egy lekérdezésben.  
  
```  
SELECT RTRIM("  abc"), RTRIM("abc"), RTRIM("abc   ")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "   abc", "$2": "abc", "$3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a>STARTSWITH ELEMNEK  
 Visszaadja egy logikai, amely jelzi, hogy az első karakterlánc-kifejezés kezdődik-e a második.  
  
 **Szintaxis**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa ellenőrzi, hogy ha kezdődik-e az "abc" karakterlánc a "b" és "a".  
  
```  
SELECT STARTSWITH("abc", "b"), STARTSWITH("abc", "a")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": false, "$2": true}]  
```  
  
####  <a name="bk_substring"></a>SUBSTRING  
 A megadott karakter nulla pozíciótól kezdődően karakterlánc-kifejezés részét adja vissza, és továbbra is fennáll, a megadott időtartam, illetve a karakterlánc végén.  
  
 **Szintaxis**  
  
```  
SUBSTRING(<str_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 Az alábbi példa részét adja vissza, az "abc" kezdődően: 1 és 1 karakter hosszúságú.  
  
```  
SELECT SUBSTRING("abc", 1, 1)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "b"}]  
```  
  
####  <a name="bk_upper"></a>FELSŐ  
 Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza.  
  
 **Szintaxis**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumentumok**  
  
-   `str_expr`  
  
     Van bármilyen érvényes karakterlánc-kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy karakterlánc-kifejezés adja vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan felső használható egy lekérdezésben  
  
```  
SELECT UPPER("Abc")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a>A tömb funkciók  
 A következő skaláris függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a numerikus visszatérési, a logikai érték vagy tömb érték  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a>ARRAY_CONCAT  
 Olyan tömb, amely két vagy több tömb értékek hozzáfűzésével eredményét adja vissza.  
  
 **Szintaxis**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     Van bármilyen érvényes tömböt megadó kifejezést.  
  
 **Visszatérési típusokat**  
  
 Egy tömböt megadó kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példában két tömböket összefűzésére módját.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"])  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a>ARRAY_CONTAINS  
Jelzi, hogy a tömb tartalmaz-e a megadott érték logikai érték beolvasása. Ha az egyezés-e a teljes vagy részleges adhat meg. 

 **Szintaxis**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     Van bármilyen érvényes tömböt megadó kifejezést.  
  
-   `expr`  
  
     Ez bármilyen érvényes kifejezés.  

-   `bool_expr`  
  
     A logikai kifejezés van.       
  
 **Visszatérési típusokat**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 Az alábbi példa használatával ARRAY_CONTAINS tömbben tagsági ellenőrzéséhez.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples"),  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes")  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": true, "$2": false}]  
```  

 A következő példa egy részleges egyezéssel a ARRAY_CONTAINS használatával tömbben JSON ellenőrzéséhez.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true), 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}),
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) 
```  
  
 Ez az eredményhalmaz.  
  
```  
[{
  "$1": true,
  "$2": false,
  "$3": false
}] 
```  
  
####  <a name="bk_array_length"></a>ARRAY_LENGTH  
 A megadott tömb kifejezés elemek számát adja vissza.  
  
 **Szintaxis**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     Van bármilyen érvényes tömböt megadó kifejezést.  
  
 **Visszatérési típusokat**  
  
 Egy numerikus kifejezést ad vissza.  
  
 **Példák**  
  
 Az alábbi példa használatával ARRAY_LENGTH tömb hosszának beszerzése.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"])  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{"$1": 3}]  
```  
  
####  <a name="bk_array_slice"></a>ARRAY_SLICE  
 Egy tömböt megadó kifejezést részét adja vissza.
  
 **Szintaxis**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumentumok**  
  
-   `arr_expr`  
  
     Van bármilyen érvényes tömböt megadó kifejezést.  
  
-   `num_expr`  
  
     Ez bármilyen érvényes numerikus kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 Az alábbi példa használatával ARRAY_SLICE tömb részét beszerzése.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1),  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1)  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{  
           "$1": ["strawberries", "bananas"],   
           "$2": ["strawberries"]  
       }]  
```  
  
###  <a name="bk_spatial_functions"></a>Térbeli funkciók  
 A következő skaláris függvények végrehajtania egy műveletet a olyan térbeli objektum beviteli értéket, és numerikus vagy logikai érték visszaadása.  
  
||||  
|-|-|-|  
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|  
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)|||  
  
####  <a name="bk_st_distance"></a>ST_DISTANCE  
 Csoport távolságát adja vissza a két GeoJSON-pont, sokszög vagy LineString kifejezések között.  
  
 **Szintaxis**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON-pont, sokszög vagy LineString objektum kifejezés.  
  
 **Visszatérési típusokat**  
  
 A távolságot tartalmazó numerikus kifejezést ad vissza. Ez az alapértelmezett referenciarendszer mérőszámok van megadva.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan 30 km-ST_DISTANCE beépített funkcióval a megadott helyen belüli termékcsalád dokumentumokat adja vissza. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a>ST_WITHIN  
 Egy logikai kifejezés, amely azt jelzi hogy a GeoJSON objektum (pont, sokszög vagy LineString) első argumentumban megadott belül a GeoJSON a második argumentum (pont, sokszög vagy LineString) adja vissza.  
  
 **Szintaxis**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON-pont, sokszög vagy LineString objektum kifejezés.  
 
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON-pont, sokszög vagy LineString objektum kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan ST_WITHIN használatával sokszög minden termékcsalád dokumentumot kereséséhez.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a>ST_INTERSECTS  
 Egy logikai kifejezés, amely azt jelzi, hogy az első argumentumban megadott GeoJSON objektum (pont, Polygon, vagy LineString) metszi a GeoJSON a második argumentum (pont, sokszög vagy LineString) adja vissza.  
  
 **Szintaxis**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON-pont, sokszög vagy LineString objektum kifejezés.  
 
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON-pont, sokszög vagy LineString objektum kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai értéket ad vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan található minden területet engedélyez, amelyek az adott sokszög metszi.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a>ST_ISVALID  
 Azt jelzi, hogy, hogy a megadott GeoJSON-pont, sokszög vagy LineString kifejezés érvényes logikai érték beolvasása.  
  
 **Szintaxis**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON-pont, sokszög vagy LineString kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy logikai kifejezést ad vissza.  
  
 **Példák**  
  
 A következő példa bemutatja, hogyan annak ellenőrzésére, ha a pont ST_VALID használatával.  
  
 Például a pont értéke szélesség, amely nem szerepel az érvényes tartomány értékkel [-90 és 90], ezért a lekérdezés hamis értéket adja vissza.  
  
 Sokszögek az a GeoJSON specifikációja megköveteli, hogy a megadott utolsó koordináta pár legyen ugyanaz, mint az első zárt alakzat létrehozásához. Egy sokszögön belül pontok balra érdekében meg kell adni. A sokszög jobbra sorrendben megadva a régióját inverzét jelöli.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{ "$1": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a>ST_ISVALIDDETAILED  
 Egy olyan logikai érték tartalmazó JSON-érték. Ha a megadott GeoJSON-pont, sokszög vagy LineString kifejezés érvényes, és ha érvénytelen értéket adja vissza, továbbá karakterláncként okát.  
  
 **Szintaxis**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumentumok**  
  
-   `spatial_expr`  
  
     Ez az egyetlen érvényes GeoJSON pontot vagy a sokszög kifejezés.  
  
 **Visszatérési típusokat**  
  
 Egy olyan logikai érték tartalmazó JSON-érték. Ha a megadott GeoJSON pontot vagy a sokszög kifejezés érvényes, és ha érvénytelen értéket adja vissza, továbbá karakterláncként okát.  
  
 **Példák**  
  
 Az alábbi példa használatával ST_ISVALIDDETAILED (adatokkal) érvényességi ellenőrzése.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
})  
```  
  
 Ez az eredményhalmaz.  
  
```  
[{  
  "$1": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>Következő lépések  
 [SQL-szintaxis és Azure Cosmos adatbázis SQL-lekérdezés](documentdb-sql-query.md)   
 [Az Azure Cosmos DB dokumentációja](https://docs.microsoft.com/en-us/azure/cosmos-db/)  
  
  
