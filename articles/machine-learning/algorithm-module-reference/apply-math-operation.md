---
title: Matematikai művelet alkalmazása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Matematikai műveletek alkalmazása modult az Azure Machine Learningben matematikai művelet alkalmazásával egy folyamat oszlopértékeire.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456743"
---
# <a name="apply-math-operation"></a>Matematikai művelet alkalmazása

Ez a cikk ismerteti az Azure Machine Learning designer modulját (előzetes verzió).

A Matematikai művelet alkalmazása segítségével számításokat hozhat létre, amelyek a bemeneti adatkészlet numerikus oszlopaira vonatkoznak. 

A támogatott matematikai műveletek közé tartoznak a gyakori aritmetikai függvények, például a szorzás és a osztás, a trigonometriai függvények, a különböző kerekítési függvények, valamint az adatelemzésben használt speciális függvények, például a gamma- és hibafüggvények.  

Miután definiált egy műveletet, és futtatta a folyamatot, az értékek hozzáadódnak az adatkészlethez. A modul konfigurálásától függően a következőket teheti:

+ Fűzze hozzá az eredményeket az adatkészlethez. Ez különösen akkor hasznos, ha a művelet eredményét ellenőrzi.
+ Az oszlopok értékeinek lecserélése az új, számított értékekre.
+ Hozzon létre egy új oszlopot az eredményekhez, és ne jelenítse meg az eredeti adatokat. 

Keresse meg a szükséges műveletet az alábbi kategóriákban:  

- [Basic](#basic-math-operations)  
  
     Az **Alap** kategória függvényei egyetlen érték vagy értékoszlop kezelésére használhatók. Előfordulhat például, hogy egy oszlopban lévő összes szám abszolút értékét kapja meg, vagy kiszámítja az oszlop egyes értékeinek négyzetgyökét.  
  
-   [Összehasonlítás](#comparison-operations)  
  
      Az Összehasonlítás kategória **függvényei** összehasonlításra szolgálnak: két oszlopban lévő értékek párban történő összehasonlításával, vagy egy oszlop egyes értékeit egy megadott állandóval hasonlíthatja össze. Például összehasonlíthatja az oszlopokat annak meghatározásához, hogy az értékek azonosak voltak-e két adatkészletben. Vagy használhat egy állandót, például egy maximálisan engedélyezett értéket, hogy megkeresse a kiugró értékeket egy numerikus oszlopban.  
  
-   [Műveletek](#arithmetic-operations)  
  
     A **Műveletek** kategória tartalmazza az alapvető matematikai függvényeket: összeadás, kivonás, szorzás és osztás. Oszlopokkal vagy állandókkal dolgozhat. Hozzáadhatja például az A oszlop értékét a B oszlopban szereplő értékhez. Az A oszlop minden értékéből kivonhat egy állandót, például egy korábban számított átlagot.  
  
-   [Kerekítés](#rounding-operations)  
  
     A **Kerekítés** kategória számos funkciót tartalmaz az olyan műveletek elvégzéséhez, mint a kerekítés, a mennyezet, a padló és a csonkolás különböző pontossági szintekre. Megadhatja a pontosság szintjét mind a decimális, mind az egész számokhoz.  
  
-   [Különleges](#special-math-functions)  
  
     A **Speciális** kategória olyan matematikai függvényeket tartalmaz, amelyeket különösen az adattudományban használnak, például az elliptikus integrálokat és a Gauss-hibafüggvényt.  
  
-   [Trigonometriai](#trigonometric-functions)  
  
     A **trigonometriai** kategória tartalmazza az összes szabványos trigonometriai függvényt. Például a sugarakat fokokká konvertálhatja, vagy olyan függvényeket is kiszámíthat, mint például az érintő radiánban vagy fokban.
     Ezek a függvények nem tartoznak, ami azt jelenti, hogy egyetlen oszlopnyi értéket vesznek fel bemenetként, alkalmazzák a trigonometriai függvényt, és ennek eredményeként egy értékoszlopot adnak vissza.  Ezért meg kell győződnie arról, hogy a bemeneti oszlop a megfelelő típusú, és tartalmazza a megfelelő típusú értékeket a megadott művelethez.   

## <a name="how-to-configure-apply-math-operation"></a>A Matematikai művelet alkalmazása beállítás  

A **Matematikai művelet alkalmazása** modulhoz olyan adatkészletszükséges, amely legalább egy, csak számokat tartalmazó oszlopot tartalmaz. A számok lehetnek diszkrétek vagy folytonosak, de numerikus adattípusúaknak kell lenniük, nem karakterláncnak.

Ugyanazt a műveletet több numerikus oszlopra is alkalmazhatja, de minden oszlopnak ugyanabban az adatkészletben kell lennie. 

A modul minden példánya egyszerre csak egy művelettípust hajthat végre. Összetett matematikai műveletek végrehajtásához szükség lehet a **Matematikai művelet alkalmazása** modul több példányának összeláncolnia.  
  
1.  Adja hozzá a **Matematikai művelet alkalmazása** modult a folyamathoz.

1. Legalább egy numerikus oszlopot tartalmazó adatkészlet összekapcsolása.  

1.  Jelöljön ki egy vagy több forrásoszlopot, amelyen a számítást végre szeretné hajtani.   
  
    - A kiválasztott oszlopoknak numerikus adattípusnak kell lenniük. 
    - Az adattartománynak érvényesnek kell lennie a kiválasztott matematikai művelethez. Ellenkező esetben hiba vagy NaN (nem szám) eredmény léphet fel. Az Ln(-1.0) érték például érvénytelen művelet, amelynek `NaN`értéke .
  
1.  Kattintson a **Kategória** gombra a végrehajtandó matematikai művelet **típusának** kiválasztásához.
    
1. Válasszon ki egy adott műveletet az adott kategória listájából.
  
1.  Az egyes művelettípusokhoz szükséges további paraméterek beállítása.  
  
1.  A **Kimeneti mód** beállítással jelezheti, hogyan szeretné létrehozni a matematikai műveletet: 

    - **Fűzés .** A bemenetként használt összes oszlop szerepel a kimeneti adatkészletben, és egy további oszlop hozzáfűzve, amely tartalmazza a matematikai művelet eredményeit.
    - **Helyben.** A bemenetként használt oszlopok értékeit a program az új számított értékekre cseréli. 
    - **ResultOnly**. A program egyetlen oszlopot ad vissza, amely a matematikai művelet eredményeit tartalmazza.
  
1.  Küldje el a folyamatot.  
  
## <a name="results"></a>Results (Eredmények)

Ha az eredményeket a **Hozzáfűzés** vagy az **Eredménycsak** beállítással hozza létre, a visszaadott adatkészlet oszlopfejlécei jelzik a műveletet és a használt oszlopokat. Ha például két oszlopot hasonlít össze az **Egyenlő operátorral,** az eredmények a következőkre néznek ki:  
  
-   **Egyenlő(Col2_Col1)** érték, jelezve, hogy a Col2-t col1 ellen tesztelte.  
-   **Egyenlő (Col2_10 $),** ami azt jelzi, hogy a 2.  

Még ha az **Inplace opciót** is használja, a forrásadatok nem törlődnek vagy módosulnak; az eredeti adatkészlet oszlopa továbbra is elérhető a tervezőben. Az eredeti adatok megtekintéséhez csatlakoztassa az [Oszlopok hozzáadása](add-columns.md) modult, és csatlakoztassa őket a Matematikai **művelet alkalmazása**kimenetéhez.  
    
## <a name="basic-math-operations"></a>Alapvető matematikai műveletek 

**Az Alap** kategóriában lévő függvények általában egyetlen értéket vesznek fel egy oszlopból, végrehajtják az előre definiált műveletet, és egyetlen értéket adnak vissza. Egyes függvények esetében második argumentumként megadhat egy állandót vagy egy oszlopkészletet.  
  
 Az Azure Machine Learning a következő függvényeket támogatja az **Alapszintű** kategóriában:  

### <a name="abs"></a>Abs

A kijelölt oszlopok abszolút értékét adja eredményül.  
  
### <a name="atan2"></a>Atan2

Négykvadráns-inverz érintőt ad eredményül.  

Jelölje ki a pontkoordinátákat tartalmazó oszlopokat. A második argumentumhoz, amely az x koordinátának felel meg, állandót is megadhat.  

Megfelel a Matlab ATAN2 függvényének.  

### <a name="conj"></a>Conj között

A kijelölt oszlop ban lévő értékek konjugátumát adja eredményül.  

### <a name="cuberoot"></a>Kockagyökér

Kiszámítja a kockagyökeret a kijelölt oszlop értékeihez.  

### <a name="doublefactorial"></a>DoubleFactorial között  
 Kiszámítja a kijelölt oszlop ban lévő értékek kettős faktoriálisát. A kettős faktoriális a normál faktoriális funkció kiterjesztése, és x-ként jelölik!!.  

### <a name="eps"></a>Eps

Az aktuális érték és a következő legmagasabb, kétszeres pontosságú szám közötti rés méretét adja eredményül. Megfelel a Matlab EPS funkciójának.  
  
### <a name="exp"></a>Exp

A kijelölt oszlopban lévő érték teljesítményére emelt e értéket adja vissza. Ez megegyezik az Excel EXP függvénnyel.  

### <a name="exp2"></a>Exp2

Az argumentumok 2.-es alapértékét adja eredményül, az y = x * 2<sup>t</sup> értéket megoldva, ahol t a kitevőket tartalmazó értékek oszlopa.  

Az **Oszlopkészletben**jelölje ki a t kitevő értékeket tartalmazó oszlopot.

**Az Exp2** esetében megadhat egy második x argumentumot, amely lehet állandó vagy másik értékoszlop. A **Második argumentum típusa mezőben**adja meg, hogy az x szorzót állandóként, vagy oszlopban lévő értéket ad-e meg.  

Ha például kiválaszt egy oszlopot, amely mind a szorzó, mind a kitevő értékeit {0,1,2,3,4,5} bejelöli, a függvény {0, 2, 8, 24, 64 160 értéket ad eredményül.  

### <a name="expminus1"></a>ExpMinus1 

A kijelölt oszlop értékeinek negatív kitevőjének értékét adja eredményül.  

### <a name="factorial"></a>Faktoriális
A kijelölt oszlop értékeinek faktoriálisát adja eredményül.  

### <a name="hypotenuse"></a>Hipotenzis
Kiszámítja egy háromszög hipotenzusait, amelyben az egyik oldal hossza értékoszlopként van megadva, a második oldal hossza pedig állandóként vagy két oszlopként van megadva.  

### <a name="ln"></a>Természetes logaritmus

A kijelölt oszlop ban lévő értékek természetes logaritmusát adja eredményül.  

### <a name="lnplus1"></a>LnPlus1

A kijelölt oszlopban lévő értékek hez a természetes logaritmust és egy értéket adja eredményül.  

### <a name="log"></a>Napló

A kijelölt oszlopban lévő értékek naplóját adja eredményül a megadott alapot adva.  

Az alapot (a második argumentumot) állandóként vagy egy másik értékoszlop kijelölésével adhatja meg.  

### <a name="log10"></a>Log10

A kijelölt oszlop 10 logaritmusértékét adja eredményül.  

### <a name="log2"></a>Log2

A kijelölt oszlop 2 logaritmus-értékét adja eredményül.  

### <a name="nthroot"></a>NthRoot
Az érték n-edik gyökerét adja eredményül a megadott n használatával.  

A **ColumnSet** beállítással jelölje ki azokat az oszlopokat, amelyekből ki szeretné számítani a gyökeret.  

A **Második argumentum típusa mezőben**jelöljön ki egy másik oszlopot, amely a gyökeret tartalmazza, vagy adjon meg egy állandót, amelyet gyökérként szeretne használni.  

Ha a második argumentum oszlop, akkor az oszlop minden értéke n értékként lesz használva a megfelelő sorhoz. Ha a második argumentum állandó, írja be az n értékét a **Második argumentum** mezőbe.
### <a name="pow"></a>Pow

Kiszámítja az Y hathatósteljesítményére emelt X-et a kijelölt oszlop ban szereplő értékek mindegyikére.  

Először jelölje ki az okat az oszlopokat, amelyek az **alapot**tartalmazzák , amelynek úszónak kell lennie a **ColumnSet** beállítással.  

A **Második argumentum típusa mezőben**jelölje ki a kitevőt tartalmazó oszlopot, vagy adjon meg egy állandót, amelyet kitevőként szeretne használni.  

Ha a második argumentum oszlop, akkor az oszlop minden egyes értéke lesz a megfelelő sor kitevője. Ha a második argumentum állandó, írja be a kitevő értékét a **Második argumentum** mezőbe.  

### <a name="sqrt"></a>Sqrt

A kijelölt oszlop értékeinek négyzetgyökét adja eredményül.  

### <a name="sqrtpi"></a>SqrtPi

A kijelölt oszlop minden egyes értékéhez megszorozza az értéket pi-vel, majd visszaadja az eredmény négyzetgyökét.  

### <a name="square"></a>Square

A kijelölt oszlop értékeit négyzetek.  

## <a name="comparison-operations"></a>Összehasonlító műveletek  

Az Azure Machine Learning tervezőben bármikor használhatja az összehasonlító függvényeket, amelyek két értékhalmazt tesztelnek egymás ellen. Előfordulhat például, hogy egy folyamatban a következő összehasonlítási műveleteket kell végeznie:  

- A valószínűségi pontszámok modell oszlopának kiértékelése egy küszöbértékhez.
- Határozza meg, hogy két eredményhalmaz azonos-e. Minden különböző sorhoz adjon hozzá egy HAMIS jelzőt, amely további feldolgozáshoz vagy szűréshez használható.  

### <a name="equalto"></a>Egyenlő

Igaz értéket ad vissza, ha az értékek megegyeznek.  

### <a name="greaterthan"></a>GreaterThan

Igaz értéket ad vissza, ha az **Oszlopkészlet** értékei nagyobbak, mint a megadott állandó, vagy nagyobbak, mint az összehasonlító oszlopmegfelelő értékei.  

### <a name="greaterthanorequalto"></a>Greaterthanorequalto között

Igaz értéket ad vissza, ha az **Oszlopkészlet** értékei nagyobbak vagy egyenlők a megadott állandóval, vagy nagyobbak vagy egyenlők az összehasonlító oszlop megfelelő értékeivel.  

### <a name="lessthan"></a>LessThan

Igaz értéket ad vissza, ha az **Oszlopkészlet** értékei kisebbek, mint a megadott állandó, vagy kisebbek, mint az összehasonlító oszlopmegfelelő értékei.  
  
### <a name="lessthanorequalto"></a>lessthanorequalto

Igaz értéket ad vissza, ha az **Oszlopkészlet** értékei kisebbek vagy egyenlők a megadott állandóval, vagy kisebbek vagy egyenlőek az összehasonlító oszlop megfelelő értékeivel.  

### <a name="notequalto"></a>Notequalto között

Igaz értéket ad vissza, ha az **Oszlopkészlet** értékei nem egyeznek meg az állandó vagy összehasonlító oszloppal, és hamis értéket ad vissza, ha egyenlőek.  

### <a name="pairmax"></a>PárosításMax

A nagyobb értéket adja eredményül – az **Oszlopkészlet** értékét vagy az állandó vagy összehasonlító oszlop értékét.  

### <a name="pairmin"></a>PárMin

A kisebb értéket adja eredményül – az **Oszlopkészlet** értékét vagy az állandó vagy összehasonlító oszlop értékét.  
  
##  <a name="arithmetic-operations"></a>Aritmetikai műveletek   

Tartalmazza az alapvető aritmetikai műveleteket: összeadás és kivonás, osztás és szorzás.  Mivel a legtöbb művelet bináris, két számot igényel, először ki kell választania a műveletet, majd ki kell választania az első és a második argumentumban használni kívánt oszlopot vagy számokat.

A felosztás és kivonás oszlopainak sorrendje ösztönellenesnek tűnhet; az eredmények megértésének megkönnyítése érdekében azonban az oszlopfejléc megadja a művelet nevét és az oszlopok használatának sorrendjét.

Művelet|Szám1|Szám2|Eredmény oszlop|Eredmény értéke|
----|----|----|----|----
|Összeadás|1|5|Hozzáadás(Num2_Num1)| 4|
|Szorzás|1|5|Többszörös(Num2_Num1)|5|
|Kivonás|1|5|Kivonás(Num2_Num1)|4|
|Kivonás|0|1|Kivonás(Num2_Num1)|0|
|Osztás|1|5|Osztás(Num2_Num1)|5|
|Osztás|0|1|Osztás(Num2_Num1)|Végtelen|

### <a name="add"></a>Hozzáadás

Adja meg a forrásoszlopokat az **Oszlopkészlet**segítségével, majd adjon hozzá ezekhez az értékekhez a **Második argumentumban**megadott számot.  

Ha két oszlopban szeretné hozzáadni az értékeket, válasszon egy oszlopot vagy oszlopokat **az Oszlopkészlet**használatával, majd válasszon egy második oszlopot a **Második argumentum**használatával.  

### <a name="divide"></a>Osztás

Az Oszlop ban **megadott** értékeket állandóval vagy a **Második argumentumban**megadott oszlopértékekkel osztja el.  Más szóval, először az osztót választod, majd az osztalékot. A kimeneti érték a hányados.

### <a name="multiply"></a>Szorzás

Megszorozza az **Oszlopban megadott** értékeket a megadott állandó vagy oszlopértékekkel.  

### <a name="subtract"></a>Kivonás

Adja meg a működéshez használt értékek oszlopát (a *minuendet*), ha egy másik oszlopot választ az **Oszlopkészlet beállítással.** Ezután adja meg a kivonni kívánt számot (a *kimondott*számot) a **Második argumentum** legördülő lista segítségével. Választhat egy állandó vagy oszlop érték.

##  <a name="rounding-operations"></a>Kerekítési műveletek 

Az Azure Machine Learning tervezője számos kerekítési műveletet támogat. Számos művelet esetén meg kell adnia a kerekítéshez használandó pontosság ot. Használhat egy állandóként megadott statikus pontossági szintet, vagy alkalmazhat egy értékoszlopból kapott dinamikus pontossági értéket.  

- Ha állandót használ, állítsa a **Pontosság típusa** értékre **Állandó,** majd írja be a számjegyek számát egész számként az **Állandó pontosság** szövegmezőbe. Ha nem egész számra ír, a modul nem okoz hibát, de az eredmények váratlanok lehetnek.  

- Ha az adatkészlet minden egyes sorához más pontosságú értéket szeretne használni, állítsa a **Pontosság típusa** értéket **ColumnSet**értékre, majd válassza ki a megfelelő pontossági értékeket tartalmazó oszlopot.  

### <a name="ceiling"></a>Ceiling

Az Oszlopkészlet értékeinek **plafonát adja eredményül.**  

### <a name="ceilingpower2"></a>Mennyezeti teljesítmény2

Az Oszlopkészlet értékeinek négyzetes **mennyezetét adja eredményül.**  

### <a name="floor"></a>Floor

Az **Oszlopkészlet**értékeinek padlóját adja vissza a megadott pontossággal.  

### <a name="mod"></a>Mod

Az Oszlopkészlet értékeinek törtrészét adja **eredményül**a megadott pontossággal.  

### <a name="quotient"></a>Hányados

Az Oszlopkészlet értékeinek törtrészét adja **eredményül**a megadott pontossággal.  

### <a name="remainder"></a>Fennmaradó

Az Oszlopkészlet értékeinek maradékát adja **eredményül.**  

### <a name="rounddigits"></a>Kerek számok

Az Oszlopkészlet értékeit adja **vissza,** amelyet a 4/5 szabály kerekít a megadott számjegyszámra kerekítve.  

### <a name="rounddown"></a>RoundDown

Az Oszlopkészlet értékeit adja **vissza,** lefelé kerekítve a megadott számjegyek re.  

### <a name="roundup"></a>RoundUp

Az **Oszlopkészlet**értékeit adja eredményül, a megadott számjegyszámra felfelé kerekítve.  

### <a name="toeven"></a>ToEven között

Az **Oszlopkészlet**értékeit adja eredményül, a legközelebbi egész, páros számra kerekítve.  

### <a name="toodd"></a>Toodd között

Az **Oszlopkészlet**értékeit adja eredményül, a legközelebbi egész, páratlan számra kerekítve.  

### <a name="truncate"></a>Truncate

Csonkolja az Oszlop **készletben megadott** értékeket úgy, hogy eltávolítja a megadott pontosság által nem engedélyezett összes számjegyet.  
  
## <a name="special-math-functions"></a>Speciális matematikai függvények

Ez a kategória az adatelemzésben gyakran használt speciális matematikai függvényeket tartalmazza. Ha nincs más feltüntetve, a függvény nem számít, és a kijelölt oszlop ban vagy oszlopokban szereplő egyes értékekre megadott számítást adja vissza.  

### <a name="beta"></a>Beta

Euler bétafüggvényének értékét adja eredményül.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
A hiányos elliptikus integrál értékét adja eredményül.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

A teljes elliptikus integrál (K) értékét adja eredményül.  

### <a name="erf"></a>Erf

A hibafüggvény értékét adja eredményül.  

A hibafüggvény (más néven Gauss hibafüggvény) a szigmoid alakzat egy speciális függvénye, amelyet a diffúzió leírására használnak.  

### <a name="erfc"></a>Erfc között

A kiegészítő hibafüggvény értékét adja eredményül.  

Az Erfc meghatározása: 1 – erf(x).  

### <a name="erfscaled"></a>ErfScaled között

A méretezett hibafüggvény értékét adja eredményül.  

A hibafüggvény méretezett változata a számtani alulfolyás elkerülésére használható.  

### <a name="erfinverse"></a>ErfInverse

Az inverz erf függvény értékét adja eredményül.  

### <a name="exponentialintegralein"></a>Exponenciális IntegralEin

Az Exponenciális Ei integrál értékét adja eredményül.  

### <a name="gamma"></a>Gamma

A gamma függvény értékét adja eredményül.  

### <a name="gammaln"></a>GammaLn között

A gamma függvény természetes logaritmusát számítja ki.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

A szabályosan izált gammafüggvény értékét adja eredményül.  

Ez a függvény egy második argumentumot vesz fel, amely állandóként vagy értékoszlopként is megadható.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverzum

Az inverz regisztereizált inverz gamma függvény értékét adja eredményül.  

Ez a függvény egy második argumentumot vesz fel, amely állandóként vagy értékoszlopként is megadható.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

A szabályosan izált gammafüggvény értékét adja eredményül.  

Ez a függvény egy második argumentumot vesz fel, amely állandóként vagy értékoszlopként is megadható.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInververzum

Az inverz generalizált inverz izált incomplete gamma függvény értékét adja eredményül.

Ez a függvény egy második argumentumot vesz fel, amely állandóként vagy értékoszlopként is megadható.  

### <a name="polygamma"></a>PolyGamma (poligamma)

A poligamma függvény értékét számítja ki.  

Ez a függvény egy második argumentumot vesz fel, amely állandóként vagy értékoszlopként is megadható.

##  <a name="trigonometric-functions"></a>Trigonometriai függvények 

Ez a kategória tartalmazza a legtöbb fontos trigonometriai és inverz trigonometriai függvények. Minden trigonometriai függvény nem számít, és nem igényel további argumentumokat.  

### <a name="acos"></a>Acos

Kiszámítja az oszlopértékek arkózus értékét.  

### <a name="acosdegree"></a>AcosDegree

Kiszámítja az oszlopértékek arkkózusát fokban.  

### <a name="acosh"></a>Acosh között

Kiszámítja az oszlopértékek arkikus arkuszkinuszát.  

### <a name="acot"></a>Acot

Kiszámítja az oszlopértékek arkuszkotansát.  

### <a name="acotdegrees"></a>Acot Fokok

Kiszámítja az oszlopértékek arkuszkotansát fokban.  

### <a name="acoth"></a>Acoth között

Kiszámítja az oszlopértékek arkusz konkutangens hiperbolikuszát.  

### <a name="acsc"></a>Acsc között

Kiszámítja az oszlopértékek ívkodottát.  

### <a name="acscdegrees"></a>AcscFok

Kiszámítja az oszlopértékek ívkodottát fokban.  
### <a name="asec"></a>Asec

Kiszámítja az oszlopértékek ívsesztelitását.  

### <a name="asecdegrees"></a>AsecFokok

Kiszámítja az oszlopértékek íveskántását fokokban.  

### <a name="asech"></a>Asech között

Kiszámítja az oszlopértékek hiperbolikusív-ívét.  

### <a name="asin"></a>Asin

Kiszámítja az oszlopértékek arkuszszinját.  

### <a name="asindegrees"></a>AsinFok

Kiszámítja az oszlopértékek arkuszszinját fokban.  

### <a name="asinh"></a>Asinh között

Kiszámítja az oszlopértékek arkikus arkusz értékét.  

### <a name="atan"></a>Atan

Kiszámítja az oszlopértékek arkérintőértékét.  

### <a name="atandegrees"></a>AtanFok

Kiszámítja az oszlopértékek arkusztangensét fokban.  

### <a name="atanh"></a>Atanh között

Kiszámítja az oszlopértékek hiperbolikus zsúbívtangentét.  

### <a name="cos"></a>Cos

Kiszámítja az oszlopértékek koszinuszát.  

### <a name="cosdegrees"></a>CosFokok

Kiszámítja az oszlopértékek koszinuszát fokban.  

### <a name="cosh"></a>Cosh

Kiszámítja az oszlopértékek koszinusz hiperbolikuszát.  

### <a name="cot"></a>Cot

Kiszámítja az oszlopértékek kotangensét.  

### <a name="cotdegrees"></a>CotFok

Kiszámítja az oszlopértékek kotangensét fokban.  

### <a name="coth"></a>Coth között
Kiszámítja az oszlopértékek kotangens hiperbolikuszát.  

### <a name="csc"></a>Csc

Kiszámítja az oszlopértékek kosekánsát.  

### <a name="cscdegrees"></a>CscFok

Kiszámítja az oszlopértékek kosekánsát fokokban.  

### <a name="csch"></a>Csch között

Kiszámítja az oszlopértékek kosekáns hiperbolikuszát.  

### <a name="degreestoradians"></a>DegreesToRadians

Fokot radiánná alakít.  

### <a name="sec"></a>Sec

Kiszámítja az oszlopértékek szekánsát.  

### <a name="asecdegrees"></a>aSecFokok

Kiszámítja az oszlopértékek szekánsát fokokban.  

### <a name="asech"></a>aSech között

Kiszámítja az oszlopértékek szekáns hiperbolikuszát.  

### <a name="sign"></a>Előjel

Az oszlopértékek előjelét adja eredményül.  

### <a name="sin"></a>Sin

Kiszámítja az oszlopértékek szinuszát.  

### <a name="sinc"></a>Cinc

Kiszámítja az oszlopértékek szinusz-koszinusz értékét.  

### <a name="sindegrees"></a>SinFok

Kiszámítja az oszlopértékek szinuszát fokban.  

### <a name="sinh"></a>Sinh

Kiszámítja az oszlopértékek szinusz hiperbolikuszát.  

### <a name="tan"></a>Tan

Kiszámítja az oszlopértékek érintőit.  

### <a name="tandegrees"></a>Tan Fokok

Kiszámítja az argumentum érintőit fokban.  

### <a name="tanh"></a>Tanh

Kiszámítja az oszlopértékek tangens hiperbolikuszát.  
  
## <a name="technical-notes"></a>Technikai megjegyzések

Legyen óvatos, ha egynél több oszlopot jelöl ki második operátorként. Az eredmények könnyen érthetők, ha a művelet egyszerű, például állandó hozzáadása az összes oszlophoz. 

Tegyük fel, hogy az adatkészlet több oszlopot tartalmaz, és saját magához adja az adatkészletet. Az eredményekben minden oszlop ot hozzáad magának, az alábbiak szerint:  
  
|Szám1|Szám2|Szám3|Hozzáadás(Num1_Num1)|Hozzáadás(Num2_Num2)|Hozzáadás(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Ha összetettebb számításokat kell végeznie, a Matematikai **művelet alkalmazása**több példányát is láncolhatja. Hozzáadhat például két oszlopot a **Matematikai művelet alkalmazása**egy példányával, majd a Matematikai művelet **alkalmazása** egy másik példányát használva az összeg állandóval való felosztásához az átlag eléréséhez.  
  
Másik lehetőségként használja az alábbi modulok egyikét az összes számítás egyszerre történő elvégzéséhez, SQL, R vagy Python parancsfájl használatával:
 
+ [R-szkript végrehajtása](execute-r-script.md)
+ [Python-szkript futtatása](execute-python-script.md)
+ [SQL-átalakítás alkalmazása](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
