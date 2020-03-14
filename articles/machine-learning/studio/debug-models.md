---
title: A modell hibakeresése
titleSuffix: ML Studio (classic) - Azure
description: A betanítási modell által létrehozott hibák hibakeresése a Azure Machine Learning Studio (klasszikus) modulban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218104"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>A modell hibakeresése Azure Machine Learning Studio (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A modellek futtatásakor a következő hibák léphetnek fel:

* a [Train Model][train-model] modul hibát eredményez 
* a [pontszám modell][score-model] modul helytelen eredményeket hoz létre 

Ez a cikk a hibák lehetséges okait ismerteti.


## <a name="train-model-module-produces-an-error"></a>Train Model-modul hibát ad vissza.

![image1](./media/debug-models/train_model-1.png)

A [Train Model][train-model] modul két bemenetet vár:

1. A gépi tanulási modell típusa a Azure Machine Learning Studio (klasszikus) által biztosított modellek gyűjteményéből.
2. A betanítási érték egy megadott feliratú oszloppal, amely meghatározza az előre jelzett változót (a többi oszlop funkcióknak kell lennie).

Ez a modul hozhatnak, egy hibaüzenet a következő esetekben:

1. A címke oszlop helytelenül van megadva. Ez akkor fordulhat elő, ha egynél több oszlop van kijelölve a címkét, vagy egy helytelen oszlopindex van kiválasztva. A második eset például akkor érvényes, ha egy 30 oszlopos indexet használ egy olyan bemeneti adatkészlettel, amely csak 25 oszlopot tartalmaz.

2. Az adatkészlet nem tartalmaz oszlopokat funkció. Például ha a bemeneti adatkészlet csak egy oszlop, amely a címke oszlop van megjelölve, lenne nem szolgáltatások, amellyel a modell létrehozásához. Ebben az esetben a [betanítási modell][train-model] modul hibát eredményez.

3. A bemeneti adatkészlet (szolgáltatások vagy címke) Infinity értéket tartalmazza.

## <a name="score-model-module-produces-incorrect-results"></a>Score Model-modul nem megfelelő eredményt ad.

![image2](./media/debug-models/train_test-2.png)

A felügyelt tanulásra jellemző tanítási/tesztelési kísérlet során a [felosztott][split] adatmodul két részre osztja az eredeti adatkészletet: az egyik rész a modell betanítására szolgál, az egyik rész pedig a betanított modell teljesítményének értékelésére szolgál. A betanított modell pontozása a tesztadatok, amely után az eredmények értékeli ki a modell pontosságának majd szolgál.

A [pontszám modell][score-model] modulhoz két bemenet szükséges:

1. Egy betanított modell kimenete a [betanítási modell][train-model] modulból.
2. Pontozó adatkészletet, amely eltér az adatkészlet, a modell betanításához használja.

Lehetséges, hogy bár a kísérlet sikeres, a [pontszám modell][score-model] modul helytelen eredményeket hoz létre. A probléma több esetben is előfordulhat:

1. Ha a megadott címke kategorikus, és egy regressziós modell van betanítva az adatmennyiségre, a [pontszám modell][score-model] modul helytelen kimenetet állít elő. Ez azért van így, mert regressziós megköveteli egy folyamatos válasz változó. Ebben az esetben lenne megfelelő a besorolási modell. 

2. Hasonlóképpen, ha egy osztályozási modell egy adatkészleten, a címke oszlopban szereplő számok lebegőpontos kellene be van tanítva, nemkívánatos eredményeket hozhat. Ennek az az oka, hogy a besoroláshoz egy különálló válasz-változóra van szükség, amely csak a véges és a kis méretű, osztályokból álló értékeket engedi meg.

3. Ha a pontozási adatkészlet nem tartalmazza a modell betanításához használt összes funkciót, a [pontszám modell][score-model] hibát eredményez.

4. Ha a pontozási adatkészlet egyik sora hiányzó értéket vagy végtelen értéket tartalmaz valamelyik funkcióhoz, a [pontszám modell][score-model] nem hoz létre az adott sorhoz tartozó kimenetet.

5. A [pontszám modell][score-model] azonos kimeneteket hozhat létre a pontozási adatkészlet összes sorában. Fordulhat elő, ha például döntési erdő használata, ha ki van választva a minták levél csomópontok minimális száma nagyobb, mint a a rendelkezésre álló példák legyen besorolási megkísérlése során.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

