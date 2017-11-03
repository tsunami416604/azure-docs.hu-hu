---
title: "A modell Azure Machine Learning a hibakeresési |} Microsoft Docs"
description: "Hibák hibakeresése tanítási és pontszám modell által előállított az Azure Machine Learning modulok."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bradsev;garye
ms.openlocfilehash: e6e9e1a3b30f84d634592581ea24fb308dcb478e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="debug-your-model-in-azure-machine-learning"></a>A modell hibáinak keresése az Azure Machine Learning rendszerben

Ez a cikk azt ismerteti, miért vagy a következő két hibákat tapasztalhat a modell futtatásakor a lehetséges okok:

* a [tanítási modell] [ train-model] modul hibát hoz létre. 
* a [Score Model] [ score-model] modul helytelen eredményt ad. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Train Model-modul hibát hoz létre.

![image1](./media/debug-models/train_model-1.png)

A [tanítási modell] [ train-model] modul vár a két bemenet:

1. A gépi tanulási modellt az Azure Machine Learning által biztosított modellek a gyűjtemény típusa.
2. A betanítási adatok, adja meg a változó előre megadott címke oszlophoz (a többi oszlop feltételezhető, hogy szolgáltatásokat lehet).

Ez a modul is létrehozhat a hiba a következő esetekben:

1. A címke oszlop helytelenül van megadva. Ez akkor fordulhat elő, ha egynél több oszlop van kijelölve, a címke, vagy helytelen index van kiválasztva. Például a második esetben akkor vonatkozik, ha egy oszlop indexe 30 csak 25 oszlopot egy bemeneti adatkészletet használják.

2. A dataset nem tartalmaz szolgáltatás oszlopot. Például ha a bemeneti dataset adatkészletben csak egy oszlop, amely a címke oszlop van megjelölve, nem lenne nincs funkció, amellyel a modell létrehozása. Ebben az esetben a [tanítási modell] [ train-model] modul hibát eredményez.

3. A bemeneti (szolgáltatások vagy címke) az adatkészletben végtelen értékként.

## <a name="score-model-module-produces-incorrect-results"></a>Score Model-modul helytelen eredményt ad.

![image2](./media/debug-models/train_test-2.png)

Egy tipikus képzési/tesztelési kísérletben a felügyelt tanítás során a [Split Data] [ split] modul két részre oszt meg az eredeti adathalmazból: egyrészről a modell betanításához szolgál, és egy rész segítségével pontozása hogyan a betanított modell is végez. A betanított modell ezután történik az adatok, amely után az eredmények pontosságának a modell kiértékelése pontozása céljából.

A [Score Model] [ score-model] modul két bemeneti értéket igényel:

1. A betanított modell kimenetét a [tanítási modell] [ train-model] modul.
2. A pontozási adatkészletet, amely eltér a modell betanításához használandó adatkészlet.

Lehetséges, hogy akkor is, ha a kísérlet sikeres, a [Score Model] [ score-model] modul helytelen eredményt ad. Több forgatókönyv szerint, hogy ez okozhatja:

1. Ha a megadott címke kategorikus, és egy regressziós modell betanítása az adatokon, helytelen kimenetnek volna elő a [Score Model] [ score-model] modul. Ez azért van így, mert regressziós megköveteli a folyamatos válasz változó. Ebben az esetben lenne megfelelőbbek, a besorolási modell használatára. 

2. Hasonlóképpen, ha a besorolási modell betanítása egy adatkészlethez, hogy a címke oszlopban szereplő számok lebegőpontos, azt nemkívánatos eredményeket hozhat. Ez azért van így, mert besorolás megköveteli, hogy az csak értékek tartományt egy véges, és általában némileg kis készleten osztályok diszkrét válasz változó.

3. Ha a pontozási a dataset nem tartalmaz a modell betanításához használandó összes funkcióját a [Score Model] [ score-model] hibát eredményez.

4. Ha pontozási adatkészlet egy sort tartalmaz a hiányzó értékeket vagy egy végtelen értéket az összes szolgáltatását, a [Score Model] [ score-model] nem hoznak létre a sorhoz megfelelő kimenetet.

5. A [Score Model] [ score-model] készíthet azonos kimeneti pontozási adatkészlet összes sort. Ez akkor fordulhat, például döntési erdők használatával, a minta / Levélcsomópont minimális száma nagyobb, mint a rendelkezésre álló példák legyen választása besorolás megkísérlése során.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

