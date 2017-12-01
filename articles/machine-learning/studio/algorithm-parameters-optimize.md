---
title: "Az Azure Machine Learning a algoritmusok optimalizálása |} Microsoft Docs"
description: "Válassza ki az optimális paraméterkészlet az az Azure Machine Learning algoritmus ismerteti."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: 664ab97cdfb663d9c8a4cc6c7b748eebfbdf580c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Válassza ki a paraméterek az Azure Machine Learning a algoritmusok optimalizálása
Ez a témakör ismerteti a jobb oldali hyperparameter beállítása az Azure Machine Learning algoritmus kiválasztása. A legtöbb gépi tanulási algoritmusok tartozhat paraméter beállítása. Amikor egy modell betanításához kell e paraméterek értékének megadására. A betanított modell hatékonyságát attól függ, hogy a modell paraméterek, választott. A folyamat, hogy a rendszer az optimális beállítása olyan paraméterek összessége *kijelölési minta*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A kijelölési minta különböző módja van. A gépi tanulásban kereszt-ellenőrzési a legszélesebb körben használt módszerek egyikét a modell kiválasztása, pedig az alapértelmezett modell kijelölés mechanizmus az Azure Machine Learning. Azure Machine Learning támogatja az R és Python, mert mindig megvalósítható a saját modell kijelölés mechanizmusok R vagy Python használatával.

A legjobb paraméterhalmaz keresése folyamatban négy lépésben történik:

1. **Adja meg a paraméter terület**: algoritmust, először döntse el, akkor érdemes megfontolni a pontos paraméterértékeket.
2. **A kereszt-ellenőrzési beállítások megadása**: döntse el, a kereszt-ellenőrzési modellrészt az adatkészlet kiválasztása.
3. **Adja meg a mérték**: milyen metrika használandó paraméterek, például pontossága legjobb készletének meghatározása mellett dönt, a legfelső szintű közepét négyzet hiba, pontosság, visszaírási vagy f-pontszám.
4. **Betanítása, értékelje ki, és hasonlítsa össze**: a paraméterértékek egyedi kombinációit kereszt-ellenőrzési által végzett, és adhat meg a hiba a metrika alapján. Értékelési és összehasonlító után válassza ki a legjobban teljesítő modell.

Az alábbi ábrán látható, hogy ez elérhető az Azure Machine Learning.

![A legjobb paraméterhalmaz keresése](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Adja meg a paraméter terület
Megadhatja, hogy a paraméter, állítsa be megfelelően a modell alkalmazásinicializálási lépéshez. Minden gépi tanulási algoritmusok paraméter ablaktábla trainer két módja van: *egyetlen paraméter* és *paraméter tartomány*. Válassza ki a paraméter tartomány mód. Paraméter tartomány módban mindegyik paraméterhez több értékeket adhat meg. A szövegmezőben vesszővel elválasztott értékeket adhat meg.

![Két osztályú súlyozott döntési fa, egyetlen paraméter](./media/algorithm-parameters-optimize/fig2.png)

 Alternatív megoldásként megadhatja a rács és pontok létrehozását a teljes száma a maximális és minimális pontjait **használata tartomány jelentéskészítő**. Alapértelmezés szerint a paraméterértékek skálán lineáris jönnek létre. Ha azonban **logaritmikus skála** be van jelölve, az értékek létrejönnek a Logaritmikus skála (Ez azt jelenti, hogy a szomszédos pontok aránya az állandó helyett a különbség). Egész paraméterrel meghatározhatja széles kötőjellel. Például "1 – 10" azt jelenti, hogy az összes egész számok 1 és 10 (mind a két szélsőértéket beleértve) közötti a paraméter készletet alkotnak. A kevert üzemmód is támogatott. Például a paramétert állítsa "1 – 10, 20, 50" tartalmazhat egész számok 1-10, 20, 50 és.

![Két osztályú súlyozott döntési fa, paraméter](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Kereszt-ellenőrzési modellrészt meghatározása
A [partíció és minta] [ partition-and-sample] modul segítségével modellrészt véletlenszerűen hozzárendelése az adatokat. A következő példa a modul konfigurációja, hogy meg kell határozni öt modellrészt és véletlenszerűen számot a modellrészek a minta-példányokban kívánja.

![Partíció és minta](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>A mérőszám meghatározása
A [Finomhangolhatják a modell-Hiperparamétereket] [ tune-model-hyperparameters] modul támogatást biztosít az egy adott algoritmus és a dataset paraméterek legjobb készletét empirically kiválasztása. Egyéb információk mellett a modell betanítása vonatkozó a **tulajdonságok** ablaktábla ehhez a modul tartalmazza a metrikát a legjobb paraméter készletének meghatározása. Két különböző legördülő listák a besorolás és regressziós algoritmus, illetve rendelkezik. Ha a szóban forgó algoritmus egy osztályozó algoritmus, a regressziós metrika rendszer figyelmen kívül hagyja, és ez fordítva is igaz. Ebben a példában a mérőszáma **pontossága**.   

![Ismétlés paraméterek](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>A vonat, kiértékeléséhez és összehasonlítása
Azonos [Finomhangolhatják a modell-Hiperparamétereket] [ tune-model-hyperparameters] modul az modellek, amelyek megfelelnek a paraméterhalmaz betanítja, különböző metrikák kiértékeli, és létrehozza a legjobban betanított modell úgy dönt, a mérték alapján. Ez a modul két kötelező bemeneti rendelkezik:

* A kellő tanuló
* Az adatkészlet

A modul is rendelkezik egy nem kötelező bemeneti adatkészletet. Csatlakozás a dataset modellrészek információ a kötelező dataset bemeneti. Ha a DataSet adatkészlet bármely modellrészek információ nem hozzá van rendelve, majd a 10-szeres kereszt-ellenőrzési automatikusan végre alapértelmezés szerint. Ha a modellrészek-hozzárendelés nem végezték el, és egy érvényesítési dataset szerepel. a választható dataset port, egy tanítási-teszt mód van kiválasztva, és az első adatkészletet használt paraméter kombinációjához a modell betanításához.

![Súlyozott döntési fa osztályozó](./media/algorithm-parameters-optimize/fig6a.png)

A modell majd kiértékelése az érvényesítési adatkészlettel. A modul bal oldali kimeneti portját különböző metrikákat a paraméterértékek funkciók szerint jeleníti meg. A jobb oldali kimeneti portjára biztosít a betanított modell, amely megfelel a kiválasztott metrika szerint a legjobban teljesítő modell (**pontossága** ebben az esetben).  

![Érvényesítési adatkészlet](./media/algorithm-parameters-optimize/fig6b.png)

A pontos paraméterek megjelenítése a jobb oldali kimeneti portjára által választott tekintheti meg. Ez a modell használható TesztKészlet pontozási vagy egy operationalized webszolgáltatás a modell betanítását, a mentés után.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
