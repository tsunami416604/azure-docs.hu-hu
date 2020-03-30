---
title: 'Lineáris regresszió: modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a lineáris regressziós modult az Azure Machine Learningben egy lineáris regressziós modell létrehozásához egy folyamathoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477561"
---
# <a name="linear-regression-module"></a>Lineáris regressziós modul
Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal lineáris regressziós modellt hozhat létre egy csővezetékben való használatra.  Lineáris regressziós kísérletek egy vagy több független változó és egy numerikus kimenetel vagy függő változó közötti lineáris kapcsolat létrehozására. 

Ezzel a modullal lineáris regressziós módszert definiál, majd betanítása egy modell tikított adatkészlet használatával. A betanított modell ezután előrejelzéseket készíthet.

## <a name="about-linear-regression"></a>Lineáris regresszió –

A lineáris regresszió egy közös statisztikai módszer, amelyet a gépi tanulásban fogadtak el, és amelyet számos új módszerrel bővítettek a vonal felszerelésére és a mérési hibára. A legalapvetőbb értelemben a regresszió egy numerikus cél előrejelzésére utal. Lineáris regresszió továbbra is jó választás, ha egy egyszerű modell egy alapvető prediktív feladat. Lineáris regresszió is általában jól működik a nagy dimenziós, ritka adatkészletek hiányzik a komplexitás.

Az Azure Machine Learning a lineáris regressziómellett számos regressziós modellt támogat. A "regresszió" kifejezés azonban lazán értelmezhető, és a más eszközökben biztosított regresszió bizonyos típusai nem támogatottak.

+ A klasszikus regressziós probléma egyetlen független és egy függő változót foglal magában. Ezt *egyszerű regressziónak nevezzük.*  Ez a modul támogatja az egyszerű regressziót.

+ *A többszörös lineáris regresszió* két vagy több független változót foglal magában, amelyek egyetlen függő változóhoz járulnak hozzá. Azokat a problémákat, amelyek során több bemenetet használnak egyetlen numerikus kimenetel előrejelzésére, *többváltozós lineáris regressziónak*is nevezik.

    A **lineáris regressziós** modul képes megoldani ezeket a problémákat, akárcsak a legtöbb más regressziós modul.

+ *A többcímkés regresszió* az a feladat, hogy egyetlen modellen belül több függő változót előre jelezz. A többcímkés logisztikai regresszióban például több különböző címkéhez is hozzárendelhető egy minta. (Ez eltér attól a feladattól, hogy egyetlen osztályváltozón belül több szintet is előre kell jelezni.)

    Az azure Machine Learning nem támogatja az ilyen típusú regressziós nem támogatott. Több változó előrejelzéséhez hozzon létre egy külön tanulót minden olyan kimenethez, amelyet előre szeretne jelezni.

A statisztikusok évek óta fejlesztik a regressziós módszereket. Ez még a lineáris regresszióra is igaz. Ez a modul két módszert támogat a hiba mérésére és a regressziós vonal illesztésére: a szokásos legkisebb négyzetek módszerét és a gradiensesést.

- **A gradiens esés** egy olyan módszer, amely minimálisra csökkenti a hiba mennyiségét a modell betanítási folyamatának minden egyes lépésénel. Sok változata a gradiens süllyedés és optimalizálása a különböző tanulási problémák már alaposan tanulmányozták. Ha ezt a lehetőséget **választja**a Megoldás módszerhez, számos paramétert állíthat be a lépésméret, a tanulási sebesség és így tovább szabályozásához. Ez a beállítás támogatja az integrált paraméteres söprés használatát is.

- **A hagyományos legkisebb négyzetek** a lineáris regresszió egyik leggyakrabban használt technikái. A legkisebb négyzetek például a Microsoft Excel Elemzési eszköztárában használt módszer.

    A szokásos legkisebb négyzetek a veszteségfüggvényre utalnak, amely a hibát a tényleges érték és az előre jelzett vonal közötti távolság négyzetének összegeként számítja ki, és a négyzetes hiba minimalizálásával illeszkedik a modellhez. Ez a módszer erős lineáris kapcsolatot feltételez a bemenetek és a függő változó között.

## <a name="configure-linear-regression"></a>Lineáris regresszió konfigurálása

Ez a modul két módszert támogat a regressziós modell felszerelésére, különböző beállításokkal:

+ [Regressziós modell illesztése a szokásos legkisebb négyzetek használatával](#create-a-regression-model-using-ordinary-least-squares)

    Kis adatkészletek esetén a legjobb, ha a szokásos legkisebb négyzeteket választja ki. Ez hasonló eredményeket ad az Excel-hez.
    
+ [Regressziós modell létrehozása online színátmenetes ereszkedéssel](#create-a-regression-model-using-online-gradient-descent)

    A gradiens jobb veszteségfunkció az összetettebb vagy túl kevés betanítási adatokkal rendelkező modellek esetében a változók száma miatt.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Regressziós modell létrehozása a szokásos legkisebb négyzetek használatával

1. Adja hozzá a **lineáris regressziós modell** modult a tervező ben lévő folyamathoz.

    Ez a modul a **Machine Learning** kategóriában található. **Bontsa ki a Modell inicializálása**, a **Regresszió kibontása**, majd húzza a lineáris **regressziós modell** modult a folyamathoz.

2. A **Tulajdonságok** ablaktábla **Megoldás metódus legördülő** listájában válassza a **Szokásos legkisebb négyzetek**lehetőséget. Ez a beállítás a regressziós vonal megkereséséhez használt számítási módszert adja meg.

3. Az **L2 regisztikáta súlymezőbe**írja be az L2 regularizáláshoz használt értéket. Azt javasoljuk, hogy a túlszerelés elkerülése érdekében ne legyen nulla érték.

     Ha többet szeretne tudni arról, hogy a szabályosítás milyen hatással van a modellillesztésre, olvassa el ezt a cikket: [L1 és L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Válassza a **(Intercept kifejezés) lehetőséget,** ha meg szeretné tekinteni az elfogás kifejezését.

    Törölje a jelet a jelölőnégyzetből, ha nem kell áttekintenie a regressziós képletet.

5. A **véletlenszám mag,** akkor opcionálisan írja be az értéket a vetőmag a véletlenszám-generátor által használt modell.

    A kezdőérték használata akkor hasznos, ha ugyanazt az eredményt szeretné karbantartani ugyanazon folyamat különböző futtatásai között. Ellenkező esetben az alapértelmezett érték a rendszeróra egy értéke.


7. Adja hozzá a [Modell betanítása](./train-model.md) modult a folyamathoz, és csatlakoztasson egy címkézett adatkészletet.

8. Küldje el a folyamatot.

### <a name="results-for-ordinary-least-squares-model"></a>Eredmények a szokásos legkisebb négyzetek modellhez

A képzés befejezése után:


+ Előrejelzések et, csatlakoztassa a betanított modellt a [Score Model](./score-model.md) modul, valamint egy adatkészlet az új értékek. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Regressziós modell létrehozása online színátmenetes ereszkedéssel

1. Adja hozzá a **lineáris regressziós modell** modult a tervező ben lévő folyamathoz.

    Ez a modul a **Machine Learning** kategóriában található. A **Modell inicializálása,** a **Regresszió kibontása**és a lineáris **regressziós modell** modul húzása a folyamathoz

2. A **Tulajdonságok** ablaktábla **Megoldás metódus legördülő** listájában válassza az **Online gradiens esés** lehetőséget a regressziós vonal kereséséhez használt számítási módszerként.

3. Az **Oktató létrehozása módban**adja meg, hogy a modellt egy előre meghatározott paraméterkészlettel szeretné-e betanítani, vagy a modellt paraméteres söpréssel szeretné optimalizálni.

    + **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a lineáris regressziós hálózatot, argumentumként megadhat egy adott értékkészletet.
    
    + **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.  

   
4. A **tanulási sebesség,** adja meg a kezdeti tanulási sebesség a sztocastikus gradiens süllyedés-optimalizáló.

5. A **Betanítási időszakok száma**mezőbe írjon be egy értéket, amely azt jelzi, hogy az algoritmus nak hányszor kell példákon keresztül végighaladnia. Kis számú példával rendelkező adatkészletek esetén ennek a számnak nagynak kell lennie a konvergencia eléréséhez.

6. **Jellemzők normalizálása**: Ha már normalizálta a modell betanításához használt numerikus adatokat, törölje a jelet a jelölőnégyzetből. Alapértelmezés szerint a modul az összes numerikus bemenetet 0 és 1 közötti tartományba normalizálja.

    > [!NOTE]
    > 
    > Ne felejtse el ugyanazt a normalizálási módszert alkalmazni a pontozáshoz használt új adatokra.

7. Az **L2 regisztikáta súlymezőbe**írja be az L2 regularizáláshoz használt értéket. Azt javasoljuk, hogy a túlszerelés elkerülése érdekében ne legyen nulla érték.

    Ha többet szeretne tudni arról, hogy a szabályosítás milyen hatással van a modellillesztésre, olvassa el ezt a cikket: [L1 és L2 Regularization for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Válassza ki a **tanulási arány csökkentése (Tanulás irásának csökkentése)** lehetőséget, ha azt szeretné, hogy a tanulási arány az ismétlések előrehaladtával csökkenjen.  

10. A **véletlenszám mag,** akkor opcionálisan írja be az értéket a vetőmag a véletlenszám-generátor által használt modell. A kezdőérték használata akkor hasznos, ha ugyanazt az eredményt szeretné karbantartani ugyanazon folyamat különböző futtatásai között.


12. Adjon hozzá egy címkézett adatkészletet és az egyik betanítási modult.

    Ha nem használ paraméteres söprést, használja a [Vonatmodell](train-model.md) modult.

13. Küldje el a folyamatot.

### <a name="results-for-online-gradient-descent"></a>Eredmények az online gradiens ereszkedéshez

A képzés befejezése után:

+ Előrejelzések et, csatlakoztassa a betanított modellt a [Score Model](./score-model.md) modul, az új bemeneti adatokkal együtt.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 