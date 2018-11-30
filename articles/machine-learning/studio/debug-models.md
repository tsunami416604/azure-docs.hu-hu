---
title: Az Azure Machine Learning Studio a modell hibáinak keresése |} A Microsoft Docs
description: Hibák hibakeresése tanítási és pontszám modell által előállított modulok az Azure Machine Learning Studióban.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.openlocfilehash: bb1a3e778dcbcfacbc9e605ebf2d855786d71510
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52312595"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Az Azure Machine Learning Studio a modell hibáinak keresése

Ez a cikk azt ismerteti, miért vagy a következő két hibák előfordulhat, hogy közben fut egy modellt a lehetséges okok:

* a [tanítási modell] [ train-model] modul hibát ad vissza. 
* a [Score Model] [ score-model] modul helytelen eredményt ad. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>Train Model-modul hibát ad vissza.

![image1](./media/debug-models/train_model-1.png)

A [tanítási modell] [ train-model] modul két bemenetet vár:

1. A gyűjtemény biztosítja az Azure Machine Learning-modellek gépi tanulási modell típusa.
2. Az egy megadott címke oszlop, amely azt adja meg a változó megjósolni a betanítási adatok (a többi oszlopot is azt feltételezi, hogy funkciók).

Ez a modul hozhatnak, egy hibaüzenet a következő esetekben:

1. A címke oszlop helytelenül van megadva. Ez akkor fordulhat elő, ha egynél több oszlop van kijelölve a címkét, vagy egy helytelen oszlopindex van kiválasztva. Például a második eset lenne a alkalmazni, ha egy oszlopindex 30 csak 25 oszlopot tartalmaz, amely a bemeneti adatkészletek együtt használják.

2. Az adatkészlet nem tartalmaz oszlopokat funkció. Például ha a bemeneti adatkészlet csak egy oszlop, amely a címke oszlop van megjelölve, lenne nem szolgáltatások, amellyel a modell létrehozásához. Ebben az esetben a [tanítási modell] [ train-model] modul hibát ad vissza.

3. A bemeneti adatkészlet (szolgáltatások vagy címke) Infinity értéket tartalmazza.

## <a name="score-model-module-produces-incorrect-results"></a>Score Model-modul nem megfelelő eredményt ad.

![image2](./media/debug-models/train_test-2.png)

Felügyelt tanítás, a tipikus képzési tesztelési kísérletben a [Split Data] [ split] modul osztja fel az eredeti adathalmazból két részből áll: az egyik rész a modell betanításához szolgál, és az egyik rész szolgál pontozása hogyan a betanított modell is végez. A betanított modell pontozása a tesztadatok, amely után az eredmények értékeli ki a modell pontosságának majd szolgál.

A [Score Model] [ score-model] modulnak szüksége van a két bemenet:

1. A betanított modell kimenete a [tanítási modell] [ train-model] modul.
2. Pontozó adatkészletet, amely eltér az adatkészlet, a modell betanításához használja.

Lehetséges, hogy akkor is, ha a kísérlet sikeres, a [Score Model] [ score-model] modul helytelen eredményt ad. Számos forgatókönyv okozhatja, hogy ez:

1. Ha a megadott címkével kategorikus és a egy regressziós modell tanítása az adatokon, helytelen kimenet által előállított lenne a [Score Model] [ score-model] modul. Ez azért van így, mert regressziós megköveteli egy folyamatos válasz változó. Ebben az esetben lenne megfelelő a besorolási modell. 

2. Hasonlóképpen, ha egy osztályozási modell egy adatkészleten, a címke oszlopban szereplő számok lebegőpontos kellene be van tanítva, nemkívánatos eredményeket hozhat. Ez azért van így, mert besorolási megköveteli egy különálló válasz változót, hogy az csak értékek tartományban lévő osztályok véges, és általában valamivel kisebb csoportja keresztül.

3. Ha a pontozási adatkészlet nem tartalmazza a modell betanításához használja minden funkció a [Score Model] [ score-model] hibát ad vissza.

4. Ha a pontozási adatkészlet egy sort tartalmaz a hiányzó értékeket vagy egy végtelen érték összes funkcióját, a [Score Model] [ score-model] nem hoznak létre a sorhoz tartozó kimenetet.

5. A [Score Model] [ score-model] pontozási adathalmazban szereplő összes sor azonos kimenetek eredményezhet. Fordulhat elő, ha például döntési erdő használata, ha ki van választva a minták levél csomópontok minimális száma nagyobb, mint a a rendelkezésre álló példák legyen besorolási megkísérlése során.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

