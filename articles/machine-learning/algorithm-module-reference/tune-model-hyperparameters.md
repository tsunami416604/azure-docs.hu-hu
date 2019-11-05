---
title: Modell Hiperparaméterek beállítása hangolása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban a Model Hiperparaméterek beállítása modult az optimális paraméter-beállítások meghatározásához a modellben a paraméteres lesöpörés végrehajtásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515654"
---
# <a name="tune-model-hyperparameters"></a>Modell Hiperparaméterek beállítása hangolása

Ez a cikk azt ismerteti, hogyan használható a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md) -modulja Azure Machine learning Designerben (előzetes verzió) az adott gépi tanulási modell optimális hiperparaméterek beállítása meghatározásához. A modul több modellt is felépít és tesztel a beállítások különböző kombinációi alapján, és összehasonlítja az összes modell mérőszámait a beállítások kombinációjának lekéréséhez. 

A feltételek *paraméter* és a *hiperparaméter* zavaró lehet. A modell *paramétereit* a Tulajdonságok ablaktáblán állíthatja be. Alapvetően ez a modul egy *paramétert* hajt végre a megadott paraméter-beállításokon, és megtanulja a _hiperparaméterek beállítása_optimális készletét, amely az egyes döntési fák, adatkészletek vagy regressziós módszerek esetében eltérő lehet. Az optimális konfiguráció megtalálásának folyamatát más néven *hangolásnak*nevezzük. 

A modul a következő módszert támogatja a modell optimális beállításainak megkereséséhez 

**Integrált betanítás és hangolás**: beállíthatja a használni kívánt paraméterek készletét, majd a modult több kombináción is megismételheti, amíg meg nem találja a "legjobb" modellt. A legtöbb tanulói modul esetében kiválaszthatja, hogy mely paramétereket kell módosítani a betanítási folyamat során, és melyeket továbbra is rögzíteni kell.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Ez a metódus egy betanított modellt hoz létre, amelyet újból felhasználhat.  

### <a name="related-tasks"></a>Kapcsolódó feladatok

+ A hangolás előtt alkalmazza a szolgáltatás kijelölését a legmagasabb adatértékkel rendelkező oszlopok vagy változók meghatározásához.

## <a name="how-to-configure-tune-model-hyperparameters"></a>A Tune Model Hiperparaméterek beállítása konfigurálása  

Általánosságban elsajátítható, hogy egy adott gépi tanulási modell optimális hiperparaméterek beállítása jelentős folyamatokra van szükség.

### <a name="train-a-model-using-a-parameter-sweep"></a>Modell betanítása paraméter-megtakarítással  

Ez a szakasz azt ismerteti, hogyan hajtható végre egy alapszintű paraméteres sweep, amely a modell Hiperparaméterek beállítása-moduljának [finomhangolását](tune-model-hyperparameters.md) végzi.

1.  Adja hozzá a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modult a folyamathoz a tervezőben.

2.  Csatlakoztasson egy nem betanított modellt a bal szélső bemenethez. 

3. Állítsa az **oktatói mód létrehozása** lehetőséget a **paraméter-tartomány** értékre, és a tartomány- **Builder** használatával adja meg a megjelenő paraméterben használandó értékek tartományát.  

    Szinte az összes besorolási és regressziós modul támogatja az integrált paramétereket. Azon tanulók esetében, amelyek nem támogatják a paraméterek tartományának konfigurálását, csak az elérhető paramétereket lehet tesztelni.

    Manuálisan is beállíthatja egy vagy több paraméter értékét, majd elsöpörheti a többi paramétert. Ez eltarthat egy ideig.

4.  Adja hozzá a betanításhoz használni kívánt adatkészletet, és kapcsolja össze a [Hiperparaméterek beállítása hangolási modell](tune-model-hyperparameters.md)középső bemenetével.  

    Ha címkézett adatkészlettel rendelkezik, a jobb szélső bemeneti porthoz (**opcionális ellenőrzési adatkészlet**) is csatlakoztatható. Ez lehetővé teszi a pontosság mérését a képzés és a hangolás során.

5.  A [hiperparaméterek beállítása-modell hangolása](tune-model-hyperparameters.md) **Tulajdonságok** ablaktábláján válasszon egy értéket a **paraméter-megtakarítási mód**beállításnál. Ezzel a beállítással szabályozhatja, hogy a paraméterek hogyan legyenek kiválasztva.

    - **Teljes rács**: Ha ezt a beállítást választja, a modul hurkot mutat a rendszer által előre meghatározott rács fölé, hogy kipróbálja a különböző kombinációkat, és azonosítsa a legjobb tanulót. Ez a beállítás olyan esetekben hasznos, amikor nem tudja, mi a legjobb paraméter-beállítás, és szeretné kipróbálni az értékek összes lehetséges kombinációját.

    - **Véletlenszerű sweep**: Ha ezt a beállítást választja, a modul véletlenszerűen kiválasztja a paraméterek értékét a rendszer által meghatározott tartományon. Meg kell adnia, hogy legfeljebb hány futtatást kíván végrehajtani a modul végrehajtásához. Ez a beállítás olyan esetekben hasznos, amikor a modell teljesítményét a választott mérőszámok alapján szeretné bővíteni, de továbbra is megőrzi a számítástechnikai erőforrásokat.

    Ha véletlenszerű kihúzást választ, megadhatja a **futtatások maximális számát véletlenszerű söpörés**esetén, ami azt jelenti, hogy a modellnek hányszor kell kitanítania, a paraméterek véletlenszerű kombinációját használva.

6.  A **label (címke) oszlopban**indítsa el az oszlop-választót egyetlen feliratú oszlop kiválasztásához.

7.  **Futtatások maximális száma véletlenszerű húzáskor**: ha véletlenszerű sweepet választ, megadhatja, hogy a modell hány alkalommal legyen betanítva, a paraméterérték véletlenszerű kombinációját használva.

    A **véletlenszerűen kiválasztott rácson futó futtatások maximális száma**: Ez a beállítás azt is meghatározza, hogy az ismétlések száma véletlenszerű mintavételezéssel történjen-e, de az értékek nem a megadott tartományból véletlenszerűen jönnek létre. Ehelyett a rendszer létrehoz egy mátrixot a paraméterek összes lehetséges kombinációjára vonatkozóan, és véletlenszerű mintavételezést végez a mátrixban. Ez a módszer hatékonyabb és kevésbé hajlamos a regionális Túlmintavételezésre vagy a mintavételezésre.

8.  Válasszon ki egy mérőszámot, amelyet a modellek **rangsorolásához** kíván használni.

    Ha a paramétert futtatja, a rendszer a modell típusához tartozó összes mérőszámot kiszámítja, és visszaadja őket a **sweep Results** jelentésben. A regressziós és besorolási modellekhez külön mérőszámok használhatók.

    A kiválasztott mérőszám azonban meghatározza a modellek rangsorolásának módját. Csak a legfelső szintű modell, amelyet a kiválasztott metrika rangsorol, a kiképzésen alapuló modellként jeleníti meg az eredményt.

9.  A véletlenszerű mag mezőben adja meg a paraméter **elvetésének**inicializálásához használandó számot. 

10. A folyamat futtatása.

## <a name="results-of-hyperparameter-tuning"></a>A hiperparaméter hangolásának eredményei

A betanítás befejezése után:

+ A legjobb modellhez tartozó pontossági mérőszámok megtekintéséhez kattintson a jobb gombbal a modulra, válassza az **eredmények eltakarítása**, majd a **Megjelenítés**lehetőséget.

    A modell típusára vonatkozó összes pontossági metrika kimenet, de a rangsorolásra kiválasztott metrika határozza meg, hogy melyik modell a "legjobb".

+ Ha más folyamatokban szeretné használni a modellt, anélkül, hogy meg kellene ismételnie a hangolási folyamatot, kattintson a jobb gombbal a modell kimenetére, majd válassza a **Mentés Kitanítva modellként**lehetőséget. 


## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

### <a name="how-a-parameter-sweep-works"></a>A paraméter-elsöpörés működése

Ez a szakasz azt ismerteti, hogyan működik a paraméter-kitakarítás általánosságban, és hogyan hatnak a modul lehetőségei.

Amikor beállít egy paramétert, megadhatja a keresés hatókörét, hogy a véletlenszerűen kiválasztott véges számú paramétert vagy az Ön által definiált paraméterek teljes keresését használja.

+ **Véletlenszerű sweep**: ezzel a beállítással a modell egy meghatározott számú ismétléssel van betanítva. 

     Megadhatja az értékek tartományát, és a modul az értékek véletlenszerűen kiválasztott részhalmazát használja.  Az értékek helyettesítéssel vannak kiválasztva, ami azt jelenti, hogy az előzőleg véletlenszerűen kiválasztott számok nem törlődnek a rendelkezésre álló számok készletéből. Így az is lehetséges, hogy bármely kiválasztott érték ugyanaz marad az összes pass-ben.  

+ **Teljes rács**: a teljes rács használatának lehetősége csak azt jelenti, hogy az összes és minden kombináció tesztelés alatt áll. Ez a lehetőség a legalaposabbnak tekinthető, de a legtöbb időt igényli. 

### <a name="controlling-the-length-and-complexity-of-training"></a>A képzés hosszának és összetettségének szabályozása

A beállítások több kombinációjának megismétlése időigényes lehet, így a modul számos módot biztosít a folyamat korlátozására:

+ A modell teszteléséhez használt iterációk számának korlátozása
+ A paraméter területének korlátozása
+ Az ismétlések számának és a paraméter területének korlátozása

Azt javasoljuk, hogy az adott adatkészletre és modellre vonatkozóan a leghatékonyabb képzési módszer meghatározásához a beállításokkal folyamatokat.

### <a name="choosing-an-evaluation-metric"></a>Értékelési metrika kiválasztása

Az egyes modellek pontosságát tartalmazó jelentést a rendszer a végén mutatja be, így áttekintheti a metrikák eredményeit. A rendszer az összes bináris besorolási modellhez egységes mérőszámot használ, és az összes többosztályos besorolási modell pontosságát használja, a regressziós modellekhez pedig a metrikák különböző készletét használja. A képzés során azonban **egyetlen** mérőszámot kell kiválasztania, amelyet a hangolási folyamat során generált modellek rangsorolásához kíván használni. Előfordulhat, hogy a legjobb metrika az üzleti problémától függ, valamint a téves pozitív és a hamis negatív értéktől függően.

#### <a name="metrics-used-for-binary-classification"></a>A bináris besoroláshoz használt metrikák

-   **Pontosság** A valódi eredmények aránya az összes esetben.  

-   **Pontosság** A valódi eredmények aránya a pozitív eredményekhez képest.  

-   **Visszahívás** Az összes eredmény minden helyes eredményének töredéke.  

-   **F-pontszám** A pontosságot és a visszahívást kiegyensúlyozó mérték.  

-   **AUC** Egy érték, amely a görbe alatti területnek felel meg, ha a hamis pozitív értékek az x tengelyen vannak ábrázolva, és az igaz pozitív értékek az y tengelyen vannak ábrázolva.  

-   **Átlagos naplózási veszteség** A két valószínűségi eloszlás közötti különbség: az igaz, a modellben pedig egy.  

#### <a name="metrics-used-for-regression"></a>Regresszióhoz használt metrikák

-   **Abszolút érték:** a modellben lévő összes hiba átlaga, ahol a hiba azt jelenti, hogy az előre jelzett érték távolsága igaz. Gyakran **Mae**-ként rövidítve.  

-   Az **átlagosan négyzetes hiba gyökere** a hibák négyzetének átlagát méri, majd az érték gyökerét veszi át. Gyakran rövidítve **gyökátlagos**  

-   A **relatív abszolút hiba** a True érték százalékaként jeleníti meg a hibát.  

-   A **relatív négyzetes hiba** normalizálja a teljes négyzetes hibát úgy, hogy az előre jelzett értékek teljes négyzetes hibáját elosztja.  

-   **Meghatározási együttható** Egyetlen szám, amely azt jelzi, hogy milyen jól illeszkedik az adatmodell. Az egyik érték azt jelenti, hogy a modell pontosan megfelel az adatmennyiségnek. a nulla érték azt jelenti, hogy az adatmennyiség véletlenszerű, vagy más módon nem fér hozzá a modellhez. Gyakran nevezik **r<sup>2</sup>** , **r<sup>2</sup>** vagy **r-Squared**értéknek.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Paramétert nem támogató modulok

A Azure Machine Learning szinte minden tanuló támogatja a többtényezős hitelesítést egy integrált paraméteres sweeptel, amely lehetővé teszi, hogy kiválassza a folyamathoz használandó paramétereket. Ha a tanuló nem támogatja az értékek tartományának beállítását, akkor továbbra is használhatja azt több érvényesítéssel. Ebben az esetben a rendszer bizonyos megengedett értékeket választ ki a sweep értékhez. 


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

