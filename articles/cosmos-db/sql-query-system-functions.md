---
title: Rendszer-funkciók
description: Ismerje meg az Azure Cosmos DB SQL rendszerfunkciók.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342836"
---
# <a name="system-functions"></a>Rendszer-funkciók

 A cosmos DB számos beépített SQL-funkciókat biztosít. A beépített függvénykategóriákat alább láthatók.  
  
|Függvény|Leírás|  
|--------------|-----------------|  
|[Matematikai függvények](#mathematical-functions)|A matematika függvényekkel végezhet a számítást, általában argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.|  
|[Funkciók ellenőrzése típusa](#type-checking-functions)|A típus ellenőrzése funkciók lehetővé teszik az SQL-lekérdezések belül egy kifejezés típusának ellenőrzése.|  
|[Sztringfüggvények](#string-functions)|A karakterlánc-függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza.|  
|[Tömb funkciók](#array-functions)|A tömb függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a visszaadott numerikus, a logikai vagy a tömb értéket.|
|[Date és Time függvények](#date-time-functions)|A date és time függvények lehetővé teszik az aktuális UTC-dátum és idő lekérése két formában; egy numerikus időbélyeget, amelynek értéke a Unix alapidőpont ezredmásodpercben vagy egy karakterlánc, amely megfelel az ISO 8601 formátumot.|
|[Térbeli funkciók](#spatial-functions)|A térbeli függvények végrehajtania egy műveletet a térbeli objektum bemeneti érték a, és a egy numerikus vagy logikai értéket adja vissza.|  

Az alábbiakban az egyes kategóriákon belül függvények listáját:

| Csoport | Műveletek |
|---------|----------|
| Matematikai függvények | ABS, FELSŐ HATÁR, EXP, EMELET, LOG, LOG10, POWER, CIKLIKUS, BEJELENTKEZÉSI, SQRT, SZÖGLETES, CSONK, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| A functions típus ellenőrzése | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Sztringfüggvények | CONCAT, TARTALMAZ, ENDSWITH, A INDEX_OF, A BAL OLDALON, A HOSSZA, A LOWER, LTRIM, CSERÉLJE LE, REPLIKÁLÁSA, FORDÍTOTT JOBB OLDALI RTRIM, STARTSWITH, FELSŐ KARAKTERLÁNCRÉSZLETET |
| Tömb funkciók | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH és ARRAY_SLICE |
| Date és Time függvények | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| Térbeli funkciók | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Ha jelenleg használja a felhasználói függvény (UDF), amelynek beépített függvény már elérhető, a megfelelő beépített függvény nem futtatásához gyorsabb és hatékonyabb.

ANSI SQL és Cosmos DB-függvények közötti fő különbség az, Cosmos DB-függvények tervezték, hogy jól működnek a séma- és vegyes séma adatok. Például ha egy tulajdonság hiányzik, vagy egy nem numerikus értéket, például `unknown`, a rendszer kihagyta az elem helyett hibát adnak vissza.

##  <a name="mathematical-functions"></a> Matematikai függvények  

A matematika függvényekkel hajtsa végre a számítási, amelyek argumentumként szolgálnak, és a egy numerikus értéket adja vissza a bemeneti értékek alapján.

Az alábbi példához hasonlóan lekérdezéseket is futtathat:

```sql
    SELECT VALUE ABS(-4)
```

Az eredmény a következő:

```json
    [4]
```

A következő támogatott beépített matematikai függvények tábláját.
  
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
  
- `numeric_expression`  
  
   A numerikus kifejezés.  
  
  **Návratové Typy**  
  
  A numerikus kifejezést ad vissza.  
  
  **Példák**  
  
  Az alábbi példa három eltérő számú ABS funkciójával eredményeket mutatja.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Adja vissza a szög radiánban, amelynek a koszinusza a megadott numerikus kifejezés; egy szám arkusz koszinusza néven is ismert.  
  
 **Syntax**  
  
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
SELECT ACOS(-1) AS acos 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Adja vissza a szög radiánban, amelynek szinusza a megadott numerikus kifejezés. Arkuszszinusz ezt is nevezik.  
  
 **Syntax**  
  
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
SELECT ASIN(-1) AS asin  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Adja vissza a szög radiánban, amelynek tangense a megadott numerikus kifejezés. Arkusztangens ezt is nevezik.  
  
 **Syntax**  
  
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
SELECT ATAN(-45.01) AS atan  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Az arkusz tangens / x, y radiánban kifejezett fő értékét adja vissza.  
  
 **Syntax**  
  
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
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> FELSŐ HATÁR  
 A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés.  
  
 **Syntax**  
  
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
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 A megadott kifejezést az radiánban megadott szög, trigonometriai koszinuszát adja vissza.  
  
 **Syntax**  
  
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
SELECT COS(14.78) AS cos  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 A megadott szög trigonometriai kotangensét adja vissza radiánban, a megadott numerikus kifejezés.  
  
 **Syntax**  
  
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
SELECT COT(124.1332) AS cot  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> FOK  
 A megfelelő szöget adja vissza fokban a egy radiánban megadott szög.  
  
 **Syntax**  
  
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
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> EMELET  
 Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott numerikus kifejezés.  
  
 **Syntax**  
  
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
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Az exponenciális a megadott numerikus kifejezés értékét adja vissza.  
  
 **Syntax**  
  
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
SELECT EXP(10) AS exp  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 Az alábbi példa a natural logarithm legfeljebb 20-at és a természetes alapú logaritmusát az exponenciális 20 exponenciális értékét adja vissza. Mivel ezek a függvények inverz függvények, egy másik, a visszatérési kerekítési lebegőpontos értékek mindkét esetben az értéke 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> NAPLÓ  
 A megadott numerikus kifejezés természetes alapú logaritmusát adja vissza.  
  
 **Syntax**  
  
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
SELECT LOG(10) AS log  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 Az alábbi példa a naplófájl a szám az exponens számítja ki.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 A megadott numerikus kifejezés 10-es alapú logaritmusát adja vissza.  
  
 **Syntax**  
  
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
SELECT LOG10(100) AS log10 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Az állandó a PI értékét adja vissza.  
  
 **Syntax**  
  
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
SELECT PI() AS pi 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> ENERGIAGAZDÁLKODÁSI  
 A megadott hatványra a megadott kifejezés értékét adja vissza.  
  
 **Syntax**  
  
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
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 Adja vissza radiánban, ha egy numerikus kifejezés, fokban is meg kell adni.  
  
 **Syntax**  
  
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
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  Íme az eredményhalmaz.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> KEREKÍTÉS  
 Egy numerikus értéket, kerekítve a legközelebbi egész értéket ad vissza.  
  
 **Syntax**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumentumok**  
  
- `numeric_expression`  
  
   A numerikus kifejezés.  
  
  **Návratové Typy**  
  
  A numerikus kifejezést ad vissza.  
  
  **Megjegyzések**
  
  A kerekítés végrehajtott művelet távolodó kerekítési középpont követi. Ha a bemenet egy numerikus kifejezés, amely pontosan két egész szám közé esik az eredmény a legközelebbi egész szám nullától távolabbi lesz.  
  
  |<numeric_expression>|Kerekített|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
  **Példák**  
  
  Az alábbi példa a következő pozitív és negatív számokat a legközelebbi egész számra kerekít.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Íme az eredményhalmaz.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> BEJELENTKEZÉS  
 A pozitív (+ 1), a nulla (0) vagy a megadott numerikus kifejezés mínuszjel (-1) adja vissza.  
  
 **Syntax**  
  
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
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 A megadott kifejezést az radiánban megadott szög, trigonometriai szinuszát adja vissza.  
  
 **Syntax**  
  
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
SELECT SIN(45.175643) AS sin  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 A megadott numerikus érték négyzetgyökét adja vissza.  
  
 **Syntax**  
  
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
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> NÉGYSZÖG  
 A megadott számérték négyzetét adja vissza.  
  
 **Syntax**  
  
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
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> BARACKSZÍNŰ  
 A megadott szög tangensét adja vissza radiánban, a megadott kifejezésben.  
  
 **Syntax**  
  
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
SELECT TAN(PI()/2) AS tan 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> CSONK  
 Egy numerikus érték, csonkolva, a legközelebbi egész értéket ad vissza.  
  
 **Syntax**  
  
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
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>Funkciók ellenőrzése típusa

A típus ellenőrzése függvények ellenőrizheti egy SQL-lekérdezésben lévő kifejezés típusa. Típus ellenőrzése funkciók segítségével határozhatja meg a tulajdonságokat elemek menet közben, ha változó vagy ismeretlen. A következő típus ellenőrzése támogatott beépített függvények táblázatát:

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
  
- `expression`  
  
   Ez bármilyen érvényes kifejezés.  
  
  **Návratové Typy**  
  
  Egy logikai kifejezés adja vissza.  
  
  **Példák**  
  
  Az alábbi példa ellenőrzi az objektumok JSON logikai, szám, NULL értékű karakterlánc, objektum, tömb és IS_ARRAY funkció használatával nem definiált típusok.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy logikai érték visszaadása.  
  
 **Syntax**  
  
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
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása.  
  
 **Syntax**  
  
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
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Adja vissza egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa null.  
  
 **Syntax**  
  
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
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy számot ad vissza.  
  
 **Syntax**  
  
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
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy JSON-objektumot ad vissza.  
  
 **Syntax**  
  
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
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Visszaad egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy primitívet (string, logikai típusú, numerikus vagy null értékű).  
  
 **Syntax**  
  
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
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy karakterláncot ad vissza.  
  
 **Syntax**  
  
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
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Karakterlánc-függvények

A következő skaláris függvények végrehajtania egy műveletet a bemeneti karakterlánc-érték, és a egy karakterlánc, numerikus vagy logikai értéket adja vissza:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[TARTALMAZ](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[HOSSZA](#bk_length)|  
|[ALACSONYABB](#bk_lower)|[LTRIM](#bk_ltrim)|[CSERÉLJE LE](#bk_replace)|  
|[REPLIKÁLÁS](#bk_replicate)|[FORDÍTOTT](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[FELSŐ](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza.  
  
 **Syntax**  
  
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
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> TARTALMAZ  
 Visszaadja egy logikai arról a második-e az első karakterlánc-kifejezést tartalmaz.  
  
 **Syntax**  
  
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
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik.  
  
 **Syntax**  
  
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
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 A második első előfordulásának kezdőpozícióját adja vissza karakterlánc-kifejezés található a megadott karakterlánc első kifejezés, vagy a -1, ha a karakterlánc nem található.  
  
 **Syntax**  
  
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
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> BALRA  
 A megadott számú karakterből álló karakterlánc bal oldali részét adja vissza.  
  
 **Syntax**  
  
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
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> HOSSZA  
 A megadott karakterlánc-kifejezés karakterek számát adja vissza.  
  
 **Syntax**  
  
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
SELECT LENGTH("abc") AS len 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> ALACSONYABB  
 Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza.  
  
 **Syntax**  
  
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
SELECT LOWER("Abc") AS lower
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a vezető üres.  
  
 **Syntax**  
  
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
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> CSERÉLJE LE  
 A megadott karakterlánc értéket az összes előfordulását lecseréli egy másik karakterláncérték.  
  
 **Syntax**  
  
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
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKÁLÁSA  
 Egy karakterláncértéket a megadott számú alkalommal ismétlődik.  
  
 **Syntax**  
  
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
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> FORDÍTOTT  
 A karakterlánc-érték megfelelő sorrendben adja vissza.  
  
 **Syntax**  
  
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
SELECT REVERSE("Abc") AS reverse  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> JOBBRA  
 A megadott számú karaktert a karakterlánc jobb oldali részét adja vissza.  
  
 **Syntax**  
  
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
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 Egy karakterlánc-kifejezés adja vissza, miután eltávolítja a záró szóközöket.  
  
 **Syntax**  
  
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
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második kezdődik.  
  
 **Syntax**  
  
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
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Egy tömb lefordítani kifejezést ad vissza. Ha a kifejezés nem fordítható le, nem definiált adja vissza.  
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Egy skaláris kifejezés karakterláncként adja vissza. 
  
 **Syntax**  
  
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
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
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
  
 **Syntax**  
  
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
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> FELSŐ  
 Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza.  
  
 **Syntax**  
  
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
SELECT UPPER("Abc") AS upper  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Tömb funkciók

A következő skaláris függvények végrehajtania egy műveletet a egy tömb bemeneti érték és a visszaadandó numerikus, logikai és a tömb értéket:
  
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
  
- `arr_expr`  
  
   A bármilyen érvényes tömbnek kifejezés.  
  
  **Návratové Typy**  
  
  Egy tömböt megadó kifejezést ad vissza.  
  
  **Példák**  
  
  Az alábbi példa hogyan fűzze össze két tömb.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Jelzi, hogy a tömb tartalmazza-e a megadott érték logikai érték beolvasása. Egy logikai kifejezés belül a parancs használatával ellenőrizheti az objektum teljes vagy részleges egyezést. 

**Syntax**  
  
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
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"b1": true, "b2": false}]  
```  

Az alábbi példa egy részleges egyezéssel a JSON használatával ARRAY_CONTAINS tömbben ellenőrzése.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 A megadott tömb kifejezés elemek számát adja vissza.  
  
 **Syntax**  
  
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
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Egy tömböt megadó kifejezést részét adja vissza.
  
 **Syntax**  
  
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
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Dátum és idő függvény

A következő skaláris függvények lehetővé teszik az aktuális UTC-dátum és idő lekérése két formában; egy numerikus időbélyeget, amelynek értéke a Unix alapidőpont ezredmásodpercben vagy egy karakterlánc, amely megfelel az ISO 8601 formátumot. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 A jelenlegi UTC szerinti dátuma és ideje ISO 8601 karakterláncként adja vissza.
  
 **Syntax**
  
```
GETCURRENTDATETIME ()
```
  
  **Návratové Typy**
  
  Az aktuális UTC dátum és idő ISO 8601 karakterlánc értékét adja vissza. 

  Ez a következő formátumban: éééé-hh-DDThh:mm:ss.sssZ fejezzük ahol:
  
  |||
  |-|-|
  |ÉÉÉÉ|négyjegyű év|
  |MM|kétjegyű hónappal (01 = January, stb.)|
  |DD|hónap (01. és 31) kétjegyű napja|
  |T|kezdő idő elemek signifier|
  |hh|két számjegyű óra (00 és 23 közötti)|
  |mm|két számjegyet perc (00 és 59 közötti)|
  |ss|két számjegyet másodperc (00 és 59 közötti)|
  |.sss|tizedes törtek másodperc három számjegye|
  |Z|UTC (egyezményes világidő) várt||
  
  Az ISO 8601 formátumú további részletekért lásd: [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Megjegyzések**

  GETCURRENTDATETIME determinált függvény. 
  
  A visszaadott eredmény az UTC (egyezményes világidő).

  **Példák**  
  
  Az alábbi példa bemutatja, hogyan beolvasni a jelenlegi UTC szerinti dátuma ideje a GetCurrentDateTime beépített függvény használatával.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Íme egy példa eredményhalmaz.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 00:00:00 csütörtök, 1. január 1970 óta eltelt idő (MS) számát adja vissza. 
  
 **Syntax**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Návratové Typy**  
  
  Indítás óta eltelt idő óta az Unix alapidőpont pl. 00:00:00 csütörtök, 1. január 1970 óta eltelt idő milliszekundumban megadva aktuális száma számértéket ad vissza.

  **Megjegyzések**

  GETCURRENTTIMESTAMP determinált függvény.
  
  A visszaadott eredmény az UTC (egyezményes világidő).

  **Példák**  
  
  Az alábbi példa bemutatja, hogyan lekérni az aktuális timestamp a GetCurrentTimestamp beépített függvény használatával.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Íme egy példa eredményhalmaz.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Térbeli funkciók

A cosmos DB a következő nyissa meg a földrajzi Consortium (OGC) beépített függvények támogatja a térinformatikai lekérdezéséhez. A következő skaláris függvények végrehajtania egy műveletet a térbeli objektum bemeneti érték a, és a egy numerikus vagy logikai értéket adja vissza.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 A két GeoJSON-pont, Polygon vagy LineString kifejezések között adja vissza a távolságot.  
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
  
 **Syntax**  
  
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
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Egy JSON-értéket tartalmazó logikai értéket, ha a megadott GeoJSON-pont, Polygon vagy LineString kifejezés érvénytelen, és ha érvénytelen értéket ad vissza, továbbá egy olyan karakterláncértéket, az oka.  
  
 **Syntax**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
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
}) AS b 
```  
  
 Íme az eredményhalmaz.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>További lépések

- [Az Azure Cosmos DB bemutatása](introduction.md)
- [UDFs](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)