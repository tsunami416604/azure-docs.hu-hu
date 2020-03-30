---
title: Mi az elosztott képzés?
titleSuffix: Azure Machine Learning
description: Ismerje meg az elosztott oktatást, és azt, hogy az Azure Machine Learning hogyan támogatja azt.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385544"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Elosztott képzés az Azure Machine Learning segítségével

Ebben a cikkben megismerheti az elosztott oktatást, és azt, hogy az Azure Machine Learning hogyan támogatja azt a deep learning-modellekhez. 

Elosztott betanítása során a számítási feladatok a modell betanításához felvan osztva, és több miniprocesszor, az úgynevezett munkavégző csomópontok között oszlik meg. Ezek a munkavégző csomópontok párhuzamosan dolgoznak a modellbetanítás felgyorsítása érdekében. Az elosztott betanítás hagyományos ML-modellekhez használható, de jobban megfelel a számítási és időigényes feladatokhoz, [például](concept-deep-learning-vs-machine-learning.md) a mély neurális hálózatok betanításához.

## <a name="deep-learning-and-distributed-training"></a>Mélytanulás és elosztott képzés 

Az elosztott képzésnek két fő típusa van: [az adatpárhuzamosság](#data-parallelism) és [a modell párhuzamosság](#model-parallelism). A deep learning modelleken elosztott képzéshez az [Azure Machine Learning SDK python-ban](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) támogatja a népszerű keretrendszerekkel, a PyTorch és a TensorFlow integrációt. Mindkét keretrendszer adatpárhuzamosságot alkalmaz az elosztott betanításhoz, és a [horovod-ot](https://horovod.readthedocs.io/en/latest/summary_include.html) a számítási sebesség optimalizálásához használhatja. 

* [Elosztott képzés a PyTorch-val](how-to-train-pytorch.md#distributed-training)

* [Elosztott képzés a TensorFlow-val](how-to-train-tensorflow.md#distributed-training)

Az elosztott betanítást nem igénylő ML-modellek esetében tekintse meg az [Azure Machine Learning gel rendelkező modellek betanítását](concept-train-machine-learning-model.md#python-sdk) a Python SDK használatával a modellek betanításának különböző módjairól.

## <a name="data-parallelism"></a>Adatpárhuzamosság

Az adatok párhuzamossága a két elosztott képzési megközelítés közül a legkönnyebben valósítható meg, és a legtöbb használati esethez elegendő.

Ebben a megközelítésben az adatok partíciókra vannak osztva, ahol a partíciók száma megegyezik a számítási fürtben rendelkezésre álló csomópontok teljes számával. A modell minden ilyen munkavégző csomópontban másolódik, és minden dolgozó az adatok saját részhalmazán működik. Ne feledje, hogy minden csomópontnak rendelkeznie kell a betanított modell támogatásához szükséges kapacitással, azaz a modellnek teljes mértékben el kell férnie az egyes csomópontokon.

Minden csomópont egymástól függetlenül kiszámítja a hibákat a betanítási minták és a címkézett kimenetek közötti hibákat. Viszont minden csomópont frissíti a modell a hibák alapján, és közölnie kell az összes módosítást a többi csomópontgal a megfelelő modellek frissítéséhez. Ez azt jelenti, hogy a munkavégző csomópontoknak szinkronizálniuk kell a modellparamétereket vagy színátmeneteket a kötegszámítás végén, hogy konzisztens modellt képezzenek be. 

## <a name="model-parallelism"></a>Modell párhuzamosság

A modell párhuzamosság, más néven hálózati párhuzamosság, a modell vannak szegmentálva különböző részekre, amelyek egyidejűleg futtathatók a különböző csomópontok, és mindegyik fog futni ugyanazon az adaton. A módszer méretezhetősége az algoritmus feladatpárhuzamosságának mértékétől függ, és bonyolultabb az adatpárhuzamosságnál. 

A modell párhuzamossága esetén a munkavégző csomópontoknak csak a megosztott paramétereket kell szinkronizálniuk, általában egyszer minden előre- vagy visszamenőleges propagálási lépésnél. Emellett a nagyobb modellek nem jelentenek problémát, mivel minden csomópont a modell ugyanazon a betanítási adatokon lévő alszakaszán működik.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan állíthatja be a python SDK-val a [képzési környezeteket.](how-to-set-up-training-targets.md)
* Technikai példát a [referenciaarchitektúra-forgatókönyvben láthat.](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)
* [Az ML modellek betanítása a TensorFlow segítségével.](how-to-train-tensorflow.md)
* [Vonat ML modellek PyTorch](how-to-train-pytorch.md). 