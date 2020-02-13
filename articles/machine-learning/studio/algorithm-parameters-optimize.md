---
title: Algoritmusok optimalizálása
titleSuffix: ML Studio (classic) - Azure
description: A cikk azt ismerteti, hogyan választhatja ki az algoritmushoz beállított optimális paramétereket Azure Machine Learning Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 33705d0c9f2f405be733fd21c88cb83e96aec781
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168969"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Paraméterek kiválasztása az algoritmusok optimalizálásához Azure Machine Learning Studio (klasszikus)

Ez a témakör azt ismerteti, hogyan választhatja ki a megfelelő hiperparaméter-készletet Azure Machine Learning Studio (klasszikus) algoritmushoz. A legtöbb gépi tanulási algoritmusok meg a paraméterek beállításához. Amikor egy modell betanításához e paraméterek értékének megadására van szükség. A betanított modell hatékonyságát a modell paraméterek, választott függ. A paraméterek optimális készletének megkeresésének folyamata modell- *kiválasztás*néven ismert.



Különböző módon modellezheti kiválasztása. A gépi tanulásban a kereszt-ellenőrzés az egyik legszélesebb körben használt módszer a modell kiválasztásához, és ez az alapértelmezett modell-kiválasztási mechanizmus a Azure Machine Learning Studio (klasszikus). Mivel a Azure Machine Learning Studio (klasszikus) az R és a Python használatát is támogatja, az R vagy a Python használatával mindig saját modell-kiválasztási mechanizmusokat alkalmazhat.

A legjobb paraméterkészletet keresése folyamatban négy lépésben történik:

1. **Adja meg a paraméter területét**: az algoritmushoz először döntse el, hogy pontosan milyen paramétereket szeretne figyelembe venni.
2. **Az átellenőrzési beállítások megadása**: döntse el, hogy az adatkészlethez hogyan kell kiválasztania a több érvényesítést.
3. **A metrika meghatározása**: döntse el, hogy melyik mérőszámot kell használni a legjobb paraméterek meghatározásához, például a pontosságot, a legfelső szintű négyzetes hibát, a pontosságot, a visszahívás vagy az f-score értékét.
4. **Betanítási, kiértékelési és összehasonlítási**lehetőségek: a paraméterek értékeinek minden egyedi kombinációja esetén a rendszer az Ön által megadott hiba alapján hajtja végre a kereszt-érvényesítést. Értékelés és összehasonlítása, után kiválaszthatja a legjobban teljesítő modell.

Az alábbi ábrán látható, hogyan érhető el a Azure Machine Learning Studio (klasszikus).

![A legjobb paraméterkészletet keresése](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>A paraméter tér definiálása
A paraméter a modell alkalmazásinicializálási lépéshez beállítva határozhatja meg. A gépi tanulási algoritmusok paraméter panelje két oktatói móddal rendelkezik: *egyetlen paraméter és egy* *paraméter tartománya*. Válassza ki a paraméter tartomány módban. Paraméter tartomány módban az egyes paraméterekhez tartozó több értékeket adhat meg. A szövegmezőben vesszővel elválasztott értékeket adhat meg.

![Kétosztályos gyorsított döntési fa, egyetlen paramétert](./media/algorithm-parameters-optimize/fig2.png)

 Másik lehetőségként megadhatja a rács maximális és minimális pontjait, valamint a **használati tartomány-szerkesztővel**előállított pontok teljes számát. Alapértelmezés szerint a paraméterértékek lineáris skála jönnek létre. Ha azonban a **naplózási skála be** van jelölve, az értékek a naplózási skálán jönnek létre (azaz a szomszédos pontok aránya állandó a különbség helyett). Egész szám paramétert meghatározhatja egy tartományt kötőjellel. Például "1-10" azt jelenti, hogy az összes egész számok 1 és 10 (mindkét határokat is beleértve) közötti alkotnak a paraméterkészletet. A kevert üzemmód is támogatott. Ha például a paraméterkészlet "1 – 10, 20, 50" műveletekre egész számok 1-10, 20, és 50.

![Kétosztályos gyorsított döntési fa, paraméter tartomány](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Kereszt-ellenőrzési modellrész definiálása
A [partíció és a minta][partition-and-sample] modul használatával véletlenszerűen lehet kiosztani az adategységeket. A következő minta a konfigurációban a modul azt definiálása öt modellrész és véletlenszerűen hozzárendelése a modellrészek számát a minta-példányokhoz.

![Partíció és minta](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>A mérőszám meghatározása
A [Tune Model hiperparaméterek beállítása][tune-model-hyperparameters] modul támogatást nyújt az adott algoritmus és adatkészlet legjobb paramétereinek kiválasztásához. A modell betanításával kapcsolatos egyéb információk mellett a modul **Tulajdonságok** ablaktáblája tartalmazza a mérőszámot, amely meghatározza a legjobb paraméter-készletet. Két különböző legördülő lista szövegbeviteli besorolási és regressziós algoritmus, illetve rendelkezik. Ha a szóban forgó algoritmus egy osztályozó algoritmus, regressziós metrika rendszer figyelmen kívül hagyja, és ez fordítva is igaz. Ebben az adott példában a metrika **pontossággal**van elfoglalva.   

![Szögtartomány paraméterek](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Betanítását, kiértékelheti és összehasonlítása
Ugyanez a [hiperparaméterek beállítása][tune-model-hyperparameters] -modul az összes olyan modellt betanítja, amelyek megfelelnek a beállított paramétereknek, kiértékeli a különböző metrikákat, majd a kiválasztott mérőszám alapján létrehoz egy legjobban képzett modellt. Ez a modul rendelkezik két kötelező bemenet:

* A kellő learner
* Az adatkészlet

A modul rendelkezik egy nem kötelező, bemeneti adatkészlet is. Jelentkezzen be az adatkészlet a kötelező adatkészlet bemeneti modellrészek információkat. Ha az adatkészlet nincs hozzárendelve modellrészek információkat, majd 10-szeres kereszt-ellenőrzés automatikusan végrehajtja alapértelmezés szerint. Ha a modellrészek-hozzárendelés nem történik, és egy érvényesítési adatkészlet szerepel. a választható adatkészlet port, egy tanítási és tesztelési mód van kiválasztva, és az első adatkészletet betanítja a modellt az egyes paraméterkombináció szolgál.

![Gyorsított döntési fa osztályozó](./media/algorithm-parameters-optimize/fig6a.png)

A modell Ezután kiértékeli az érvényesítési adatkészlethez. A bal oldali kimeneti portjára, a modul más érdekes mérőszám a paraméterértékek funkciókként jeleníti meg. A megfelelő kimeneti port biztosítja a betanított modellt, amely megfelel a legjobban teljesítő modellnek a kiválasztott metrika (ebben az esetben a**pontosság** ) szerint.  

![Érvényesítési adatkészlet](./media/algorithm-parameters-optimize/fig6b.png)

A pontos paraméterek megjelenítése a jobb oldali kimeneti portjára által választott látható. Ez a modell használható egy teszt set pontozási vagy webszolgáltatásig a betanított modell, a mentés után.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
