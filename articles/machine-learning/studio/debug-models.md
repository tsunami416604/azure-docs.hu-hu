---
title: A modell hibakeresése
titleSuffix: Azure Machine Learning Studio (classic)
description: A betanítási modell által létrehozott hibák hibakeresése a Azure Machine Learning Studio (klasszikus) modulban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 053bb75bb7beea86215397bdfd81a1dbc9d1bcb9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493065"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>A modell hibakeresése Azure Machine Learning Studio (klasszikus)

A modellek futtatásakor a következő hibák léphetnek fel:

* a [Train Model][train-model] modul hibát eredményez 
* a [pontszám modell][score-model] modul helytelen eredményeket hoz létre 

Ez a cikk a hibák lehetséges okait ismerteti.


## <a name="train-model-module-produces-an-error"></a>A betanítási modell modulja hibát eredményez

![image1](./media/debug-models/train_model-1.png)

A [Train Model][train-model] modul két bemenetet vár:

1. A gépi tanulási modell típusa a Azure Machine Learning Studio (klasszikus) által biztosított modellek gyűjteményéből.
2. A betanítási érték egy megadott feliratú oszloppal, amely meghatározza az előre jelzett változót (a többi oszlop funkcióknak kell lennie).

Ez a modul hibát eredményezhet a következő esetekben:

1. A Label oszlop helytelenül van megadva. Ez akkor fordulhat elő, ha egynél több oszlop van kiválasztva, mert a címke vagy a helytelen oszlop index van kiválasztva. A második eset például akkor érvényes, ha egy 30 oszlopos indexet használ egy olyan bemeneti adatkészlettel, amely csak 25 oszlopot tartalmaz.

2. Az adatkészlet nem tartalmaz funkció-oszlopokat. Ha például a bemeneti adatkészlet csak egy oszloppal van megjelölve, amely felirat oszlopként van megjelölve, akkor nem lesz olyan szolgáltatás, amellyel a modellt fel kell építeni. Ebben az esetben a [betanítási modell][train-model] modul hibát eredményez.

3. A bemeneti adatkészlet (funkciók vagy címke) végtelen értéket tartalmaz.

## <a name="score-model-module-produces-incorrect-results"></a>A pontszám modell modul helytelen eredményeket eredményez

![image2](./media/debug-models/train_test-2.png)

A felügyelt tanulásra jellemző tanítási/tesztelési kísérlet során a [felosztott][split] adatmodul két részre osztja az eredeti adatkészletet: az egyik rész a modell betanítására szolgál, az egyik rész pedig a betanított modell teljesítményének értékelésére szolgál. A rendszer ezután a betanított modellt használja a tesztelési adatok kiértékelésére, amely után a rendszer kiértékeli az eredményeket a modell pontosságának meghatározásához.

A [pontszám modell][score-model] modulhoz két bemenet szükséges:

1. Egy betanított modell kimenete a [betanítási modell][train-model] modulból.
2. Egy olyan pontozási adatkészlet, amely eltér a modell betanításához használt adatkészlettől.

Lehetséges, hogy bár a kísérlet sikeres, a [pontszám modell][score-model] modul helytelen eredményeket hoz létre. A probléma több esetben is előfordulhat:

1. Ha a megadott címke kategorikus, és egy regressziós modell van betanítva az adatmennyiségre, a [pontszám modell][score-model] modul helytelen kimenetet állít elő. Ennek az az oka, hogy a regresszióhoz folytonos válasz változó szükséges. Ebben az esetben megfelelőbb lenne a besorolási modell használata. 

2. Hasonlóképpen, ha egy besorolási modellt olyan adatkészleten tanítanak, amely lebegőpontos számmal rendelkezik a Label (címke) oszlopban, akkor nem kívánatos eredményeket eredményezhet. Ennek az az oka, hogy a besoroláshoz egy különálló válasz-változóra van szükség, amely csak a véges és a kis méretű, osztályokból álló értékeket engedi meg.

3. Ha a pontozási adatkészlet nem tartalmazza a modell betanításához használt összes funkciót, a [pontszám modell][score-model] hibát eredményez.

4. Ha a pontozási adatkészlet egyik sora hiányzó értéket vagy végtelen értéket tartalmaz valamelyik funkcióhoz, a [pontszám modell][score-model] nem hoz létre az adott sorhoz tartozó kimenetet.

5. A [pontszám modell][score-model] azonos kimeneteket hozhat létre a pontozási adatkészlet összes sorában. Ez például akkor fordulhat elő, ha döntési erdőket használó besorolást próbál meg használni, ha a leküldéses csomópontok minimális száma nagyobb, mint az elérhető képzési példák száma.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

