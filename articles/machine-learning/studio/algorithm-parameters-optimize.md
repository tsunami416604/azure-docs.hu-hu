---
title: Algoritmusok optimalizálása
titleSuffix: ML Studio (classic) - Azure
description: Bemutatja, hogyan választhatja ki az optimális paraméterkészletet egy algoritmushoz az Azure Machine Learning Studio (klasszikus) alkalmazásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218268"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Paraméterek kiválasztása az algoritmusok optimalizálásához az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez a témakör ismerteti, hogyan válassza ki a megfelelő hiperparaméter-készlet egy algoritmus az Azure Machine Learning Studio (klasszikus). A legtöbb gépi tanulási algoritmusok paramétereket kell beállítani. Modell betanításakor meg kell adnia a paraméterek értékeit. A betanított modell hatékonysága a választott modellparaméterektől függ. Az optimális paraméterkészlet megtalálásának folyamatát *modellválasztásnak*nevezzük.



A modellválasztást többféleképpen is el lehet végezni. A gépi tanulásban a keresztérvényesítés az egyik legszélesebb körben használt módszer a modellkiválasztásához, és ez az alapértelmezett modellkiválasztási mechanizmus az Azure Machine Learning Studio (klasszikus) alkalmazásban. Mivel az Azure Machine Learning Studio (klasszikus) támogatja az R és a Python, mindig megvalósíthatja a saját modell kiválasztási mechanizmusok segítségével R vagy Python használatával.

A legjobb paraméterkészlet megtalálásának folyamatában négy lépés ből áll:

1. **Adja meg a paraméterterületet:** Az algoritmusesetében először döntse el a figyelembe venni kívánt paraméterértékeket.
2. **Adja meg a keresztérvényesítési beállításokat:** Döntse el, hogyan válassza ki az adatkészlet keresztérvényesítési hajtásait.
3. **Adja meg a metrikát:** Döntse el, hogy milyen metrikát kell használni a legjobb paraméterek meghatározásához, például a pontossághoz, a gyöker átlagszögű négyzetes hibához, pontossághoz, visszahíváshoz vagy f-pontszámhoz.
4. **Betanítás, kiértékelés és összehasonlítás:** A paraméterértékek minden egyes egyedi kombinációjához a keresztellenőrzést a megadott hibametrika végzi el, és azok alapján. Az értékelés és az összehasonlítás után kiválaszthatja a legjobban teljesítő modellt.

Az alábbi képen bemutatja, hogyan érhető el ez az Azure Machine Learning Studio (klasszikus).

![A legjobb paraméterkészlet megkeresése](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>A paramétertér meghatározása
A modell inicializálási lépésénél megadhatja a paraméterkészletet. Az összes gépi tanulási algoritmus paraméterablaktábláján két oktatómód található: *Egy paraméter* és *paramétertartomány*. Válassza a Paramétertartomány módot. Paramétertartomány módban minden paraméterhez több értéket is megadhat. A szövegmezőbe vesszővel tagolt értékeket írhat be.

![Kétosztályos kiemelt döntési fa, egyetlen paraméter](./media/algorithm-parameters-optimize/fig2.png)

 Másik lehetőségként megadhatja a rács maximális és minimális pontjait, valamint a **Tartományszerkesztővel**létrehozandó pontok teljes számát. Alapértelmezés szerint a paraméterértékek lineáris skálán jönnek létre. Ha azonban a **Log Scale** be van jelölve, az értékek a log léptékben jönnek létre (azaz a szomszédos pontok aránya állandó a különbség helyett). Egész paraméterekhez kötőjellel határozhat meg tartományt. Például az "1-10" azt jelenti, hogy az 1 és 10 közötti egész számok (mindkettő bezárólag) alkotják a paraméterkészletet. A vegyes mód is támogatott. Az "1-10, 20, 50" paraméterkészlet például 1–10, 20 és 50 egész számokat tartalmaz.

![Kétosztályos kiemelt döntési fa, paramétertartomány](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Keresztérvényesítési ráncok meghatározása
A [Partíció és a Minta][partition-and-sample] modul segítségével véletlenszerűen rendelhet idát az adatokhoz. A modul következő mintakonfigurációjában öt redőt határozunk meg, és véletlenszerűen hozzárendelünk egy hajtásszámot a mintapéldányokhoz.

![Partíció és minta](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>A metrika meghatározása
A [Tune Model Hyperparameters][tune-model-hyperparameters] modul támogatja az empirikusan egy adott algoritmus és adatkészlet legjobb paraméterkészletének kiválasztását. A modell betanításával kapcsolatos egyéb információk mellett a modul **Tulajdonságok** ablaktáblája tartalmazza a legjobb paraméterkészlet meghatározásához szükséges metrikát is. Két különböző legördülő listával rendelkezik a besorolási és regressziós algoritmusokhoz. Ha a vizsgált algoritmus egy besorolási algoritmus, a regressziós metrika figyelmen kívül hagyja, és fordítva. Ebben a konkrét példában a metrika a **Pontosság**.   

![Söprési paraméterek](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Vonat, értékelés és összehasonlítás
Ugyanaz [a Tune Model Hyperparameters][tune-model-hyperparameters] modul betanítja az összes modellt, amely megfelel a paraméterkészletnek, kiértékeli a különböző metrikákat, majd létrehozza a legjobban betanított modellt a kiválasztott metrika alapján. Ez a modul két kötelező bemenettel rendelkezik:

* A képzetlen tanuló
* Az adatkészlet

A modul is rendelkezik egy opcionális adatkészlet bemenet. Csatlakoztassa az adatkészletet a hajtásadatokkal a kötelező adatkészlet-bevitelhez. Ha az adatkészlet hez nincs hozzárendelve hajtásinformáció, akkor alapértelmezés szerint a rendszer automatikusan végrehajtja a 10-szeres keresztérvényesítést. Ha a hajtás-hozzárendelés nem történik meg, és egy érvényesítési adatkészlet et biztosít a választható adatkészletport, majd egy betanítási-teszt módot választ, és az első adatkészletet használja a modell betanításához minden paraméterkombinációhoz.

![Kiemelt döntési faosztályozó](./media/algorithm-parameters-optimize/fig6a.png)

A modell ezután kiértékeli az érvényesítési adatkészletet. A modul bal oldali kimeneti portja különböző metrikákat jelenít meg a paraméterértékek függvényeiként. A megfelelő kimeneti port adja a betanított modell, amely megfelel a legjobban teljesítő modell szerint a kiválasztott metrika (**pontosság** ebben az esetben).  

![Érvényesítési adatkészlet](./media/algorithm-parameters-optimize/fig6b.png)

A megfelelő kimeneti port megjelenítésével megtekintheti a kiválasztott pontos paramétereket. Ez a modell használható egy tesztkészlet vagy egy működőképes webszolgáltatás mentés után egy betanított modell.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
