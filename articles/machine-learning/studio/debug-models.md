---
title: A modell hibakeresése
titleSuffix: ML Studio (classic) - Azure
description: A Train Model és a Score Model modulok által az Azure Machine Learning Studio (klasszikus) szolgáltatásban létrehozott hibák hibakeresése.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218104"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>A modell hibakeresése az Azure Machine Learning Studióban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Modell futtatásakor a következő hibák léphetnek fel:

* a [Train Model][train-model] modul hibát jelez 
* A [Score Model][score-model] modul hibás eredményeket hoz 

Ez a cikk ismerteti a hibák lehetséges okait.


## <a name="train-model-module-produces-an-error"></a>A betanítási modellmodul hibát eredményez

![image1](./media/debug-models/train_model-1.png)

A [train modell][train-model] modul két bemenetet vár:

1. Az Azure Machine Learning Studio (klasszikus) által biztosított modellek gyűjteményéből származó gépi tanulási modell típusa.
2. A betanítási adatok egy megadott Címke oszlop, amely meghatározza a változó előrejelezni (a többi oszlop kell tekinteni funkciók).

Ez a modul a következő esetekben okozhat hibát:

1. A Címke oszlop helytelenül van megadva. Ez akkor fordulhat elő, ha egynél több oszlop van kijelölve címkeként, vagy helytelen oszlopindex van kijelölve. A második eset például akkor érvényes, ha egy 30-as oszlopindexet használ egy mindössze 25 oszlopból álló bemeneti adatkészlettel.

2. Az adatkészlet nem tartalmaz funkcióoszlopokat. Ha például a bemeneti adatkészlet csak egy oszlop, amely a Címke oszlopként van megjelölve, nem lennének olyan szolgáltatások, amelyekkel a modell létrehozásához. Ebben az esetben a [Betanítási modell][train-model] modul hibát hoz létre.

3. A bemeneti adatkészlet (Funkciók vagy Címke) az Infinity-t tartalmazza értékként.

## <a name="score-model-module-produces-incorrect-results"></a>A score model modul helytelen eredményeket hoz

![image2](./media/debug-models/train_test-2.png)

Egy felügyelt tanulásra vonatkozó tipikus betanítási/tesztelési kísérletben a [Split Data][split] modul az eredeti adatkészletet két részre osztja: az egyik rész a modell betanítására szolgál, a másik pedig a betanított modell teljesítményének pontozására szolgál. A betanított modell ezután a vizsgálati adatok pontozására szolgál, majd az eredmények kiértékelése a modell pontosságának meghatározásához.

A [Score Model][score-model] modulhoz két bemenet szükséges:

1. A [betanított][train-model] modell kimeneta a Train Model modulból.
2. A modell betanításához használt adatkészlettől eltérő pontozási adatkészlet.

Lehetséges, hogy annak ellenére, hogy a kísérlet sikeres, a [Score Model][score-model] modul helytelen eredményeket hoz létre. A probléma több esetben is előfordulhat:

1. Ha a megadott címke kategorikus, és egy regressziós modell van betanítva az adatokra, a [Score Model][score-model] modul helytelen kimenetet hoz létre. Ennek az az oka, hogy a regresszió folyamatos válaszváltozót igényel. Ebben az esetben megfelelőbb lenne egy osztályozási modell használata. 

2. Hasonlóképpen, ha egy besorolási modell be tanítása egy adatkészlet, amelynek lebegőpontos számok a Címke oszlopban, akkor nemkívánatos eredményeket eredményezhet. Ennek az az oka, hogy a besorolás hoz létre egy különálló válasz változó, amely csak lehetővé teszi, hogy az értékek tartományban egy véges, és a kis, osztályok halmaza.

3. Ha a pontozási adatkészlet nem tartalmazza a modell betanításához használt összes funkciót, a [score modell][score-model] hibát hoz létre.

4. Ha a pontozási adatkészlet egy sora hiányzó értéket vagy végtelen értéket tartalmaz bármely funkciójához, a [score modell][score-model] nem hoz létre az adott sornak megfelelő kimenetet.

5. A [pontmodell][score-model] azonos kimeneteket hozhat létre a pontozási adatkészlet összes sorához. Ez például akkor fordulhat elő, ha döntési erdők használatával kísérelik meg a besorolást, ha a levélcsomópontonkénti minták minimális számát úgy választják ki, hogy több legyen, mint a rendelkezésre álló képzési példák száma.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

