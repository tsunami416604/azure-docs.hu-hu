---
title: Algoritmusok optimalizálása
titleSuffix: Azure Machine Learning Studio
description: Válassza ki az optimális paraméter beállítása az Azure Machine Learning Studióban az algoritmusok ismerteti.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 6dc9476f603d5664b7ea23489042b69f86647cf5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123283"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio"></a>Válassza ki az Azure Machine Learning Studióban az algoritmusok optimalizálása paraméterekkel

Ez a témakör ismerteti a megfelelő hiperparaméter állítsa be az Azure Machine Learning Studióban az algoritmusok kiválasztása. A legtöbb gépi tanulási algoritmusok meg a paraméterek beállításához. Amikor egy modell betanításához e paraméterek értékének megadására van szükség. A betanított modell hatékonyságát a modell paraméterek, választott függ. Az optimális paraméterek készletét folyamata más néven *kijelölés modell*.



Különböző módon modellezheti kiválasztása. A gépi tanulásban a kereszt-ellenőrzés a leggyakrabban használt módszerek egyikét a modell kiválasztása, és azt az alapértelmezett modell kijelölés mechanizmus az Azure Machine Learning Studióban. Mivel az Azure Machine Learning Studio támogatja az R- és Python, mindig is alkalmazható a saját modell kijelölés mechanizmusok R vagy Python.

A legjobb paraméterkészletet keresése folyamatban négy lépésben történik:

1. **Adja meg a paraméter terület**: Az algoritmus először döntse el, érdemes figyelembe venni kívánt pontos paraméter értékét.
2. **Kereszt-ellenőrzési beállítások meghatározásához**: Annak eldöntése, hogyan válassza ki a kereszt-ellenőrzési modellrész az adatkészlet.
3. **A mérőszám meghatározása**: Döntse el, milyen metrikát alkalmasak a legjobb paraméterkészlet, mint a pontossága, root mean-készlet négyzet hiba, a pontosság, a visszaírási vagy az f-pontszám.
4. **Betanítását, kiértékelheti és hasonlítsa össze**: Minden egyéni kombinációja paraméter értékét, a kereszt-ellenőrzési által végzett és a határoz meg hiba-metrika alapján. Értékelés és összehasonlítása, után kiválaszthatja a legjobban teljesítő modell.

Az alábbi ábrán látható, hogy ez elérhető az Azure Machine Learning Studióban.

![A legjobb paraméterkészletet keresése](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>A paraméter tér definiálása
A paraméter a modell alkalmazásinicializálási lépéshez beállítva határozhatja meg. Az összes gépi tanulási algoritmusok paraméter ablaktábla kétféle trainer rendelkezik: *Egyetlen paraméter* és *paraméter tartomány*. Válassza ki a paraméter tartomány módban. Paraméter tartomány módban az egyes paraméterekhez tartozó több értékeket adhat meg. A szövegmezőben vesszővel elválasztott értékeket adhat meg.

![Kétosztályos gyorsított döntési fa, egyetlen paramétert](./media/algorithm-parameters-optimize/fig2.png)

 Azt is megteheti, meghatározhatja a maximális és minimális pontok a rács és létrehozását a pontok száma **használata tartomány Builder**. Alapértelmezés szerint a paraméterértékek lineáris skála jönnek létre. De ha **logaritmikus skála** be van jelölve, az értékek jönnek létre a log méretezési csoportban lévő (azt jelenti, arány a szomszédos pontok, állandó azok különbség helyett). Egész szám paramétert meghatározhatja egy tartományt kötőjellel. Például "1-10" azt jelenti, hogy az összes egész számok 1 és 10 (mindkét határokat is beleértve) közötti alkotnak a paraméterkészletet. A kevert üzemmód is támogatott. Ha például a paraméterkészlet "1 – 10, 20, 50" műveletekre egész számok 1-10, 20, és 50.

![Kétosztályos gyorsított döntési fa, paraméter tartomány](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Kereszt-ellenőrzési modellrész definiálása
A [partíciót és minta] [ partition-and-sample] modul modellrész véletlenszerűen hozzárendelése az adatok is használható. A következő minta a konfigurációban a modul azt definiálása öt modellrész és véletlenszerűen hozzárendelése a modellrészek számát a minta-példányokhoz.

![Partíció és minta](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>A mérőszám meghatározása
A [modell-Hiperparaméterek] [ tune-model-hyperparameters] modul támogatja az egy adott algoritmus és az adatkészlet ajánlott paraméterkészlet tapasztalati kiválasztása. Egyéb információk mellett a modell tanítása kapcsolatos a **tulajdonságok** ablaktáblán, ez a modul tartalmazza a mérőszám a legjobb paraméterkészletet meghatározásához. Két különböző legördülő lista szövegbeviteli besorolási és regressziós algoritmus, illetve rendelkezik. Ha a szóban forgó algoritmus egy osztályozó algoritmus, regressziós metrika rendszer figyelmen kívül hagyja, és ez fordítva is igaz. Ebben a példában a metrika az **pontossága**.   

![Szögtartomány paraméterek](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Betanítását, kiértékelheti és összehasonlítása
Azonos [modell-Hiperparaméterek] [ tune-model-hyperparameters] modul vonatok, a modellek, amelyek megfelelnek a paraméter beállítása, kiértékeli a különböző mérőszámokat és létrehozza a legjobban a betanított modell alapján a mérték, Válassza ki. Ez a modul rendelkezik két kötelező bemenet:

* A kellő learner
* Az adatkészlet

A modul rendelkezik egy nem kötelező, bemeneti adatkészlet is. Jelentkezzen be az adatkészlet a kötelező adatkészlet bemeneti modellrészek információkat. Ha az adatkészlet nincs hozzárendelve modellrészek információkat, majd 10-szeres kereszt-ellenőrzés automatikusan végrehajtja alapértelmezés szerint. Ha a modellrészek-hozzárendelés nem történik, és egy érvényesítési adatkészlet szerepel. a választható adatkészlet port, egy tanítási és tesztelési mód van kiválasztva, és az első adatkészletet betanítja a modellt az egyes paraméterkombináció szolgál.

![Gyorsított döntési fa osztályozó](./media/algorithm-parameters-optimize/fig6a.png)

A modell Ezután kiértékeli az érvényesítési adatkészlethez. A bal oldali kimeneti portjára, a modul más érdekes mérőszám a paraméterértékek funkciókként jeleníti meg. A jobb oldali kimeneti portjára hozzáférést biztosít a betanított modell, amely megfelel a kiválasztott metrika szerint a legjobban teljesítő modell (**pontossága** ebben az esetben).  

![Érvényesítési adatkészlet](./media/algorithm-parameters-optimize/fig6b.png)

A pontos paraméterek megjelenítése a jobb oldali kimeneti portjára által választott látható. Ez a modell használható egy teszt set pontozási vagy webszolgáltatásig a betanított modell, a mentés után.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
