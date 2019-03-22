---
title: A PyTorch modelleket taníthat be
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan futtathat egy csomópontos és elosztott képzési PyTorch modellek a PyTorch estimator
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a5ddc17f6200ba2d43d67fcd2e4bcc35c224e6cb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004055"
---
# <a name="train-pytorch-models-with-azure-machine-learning-service"></a>PyTorch modellek Azure Machine Learning szolgáltatással

Neurális hálózat (DNN) képzést nyújt PyTorch, az Azure Machine Learning biztosít egyéni [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) osztályát az `Estimator`. Az Azure SDK `PyTorch` estimator lehetővé teszi, hogy könnyedén beküldhető PyTorch mind egyetlen csomópontot, és elosztott futtatások az Azure-beli számítási feladatok.

## <a name="single-node-training"></a>Egy csomópontos képzés
A képzés a `PyTorch` estimator hasonlít a használatával a [alap `Estimator` ](how-to-train-ml-models.md), ezért először olvassa el a cikkben található útmutató, és ellenőrizze, hogy tisztában van a bemutatott fogalmakkal.
  
Hozza létre a PyTorch feladatok futtatásához egy `PyTorch` objektum. Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#amlcompute) objektum `compute_target` és a [adattárolója](how-to-access-data.md) objektum `ds`.

```Python
from azureml.train.dnn import PyTorch

script_params = {
    '--data_dir': ds
}

pt_est = PyTorch(source_directory='./my-pytorch-proj',
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='train.py',
                 use_gpu=True)
```

Itt hogy adja meg az alábbi paramétereket a PyTorch konstruktor:

Paraméter | Leírás
--|--
`source_directory` |  Helyi könyvtár, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ez a mappa a távoli számítási átmásolódnak a helyi gépen
`script_params` |  A parancssori paraméterek, a tanítási szkriptet megadása szótárban `entry_script`, < parancssori argumentum, érték > formájában párok
`compute_target` |  Arról, hogy az a tanítási szkriptet, ebben az esetben az Azure Machine Learning Compute távoli számítási célnak ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)) fürt
`entry_script` |  Fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
`conda_packages` |  Szükség szerint a tanítási szkriptet conda-n keresztül kell telepíteni a Python-csomagok listáját. A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` használható az esetleges pip csomagokat szükséges
`use_gpu` |  Ezt a jelzőt `True` kihasználhatja a GPU, a betanításhoz. Az alapértelmezett érték `False`

Mivel használ a `PyTorch` estimator, a képzési használt tároló tartalmazza a PyTorch csomag és a kapcsolódó függőségeket, a processzorok és gpu-k képzéshez szükséges.

Ezután küldje el a PyTorch feladatot:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Elosztott betanítás
A `PyTorch` estimator lehetővé teszi, hogy ipari méretekben a modellek betanítása Azure-beli virtuális Processzor és GPU fürtök között. Könnyedén futtathat elosztott PyTorch képzési néhány API-hívások, amíg az Azure Machine Learning fogja kezelni a háttérben, az infrastruktúra és a vezénylési ilyen számítási feladat végrehajtásához szükséges.

Az Azure Machine Learning jelenleg támogatja a Horovod keretrendszerrel PyTorch elosztott képzésének MPI-alapú.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) Uber által fejlesztett elosztott betanítás egy kör-allreduce nyílt forráskódú keretrendszer van.

A Horovod keretrendszerrel elosztott PyTorch futtatásához a következőképpen hozhat létre a PyTorch objektum:

```Python
from azureml.train.dnn import PyTorch

pt_est = PyTorch(source_directory='./my-pytorch-project',
                 script_params={},
                 compute_target=compute_target,
                 entry_script='train.py',
                 node_count=2,
                 process_count_per_node=1,
                 distributed_backend='mpi',
                 use_gpu=True)
```

Ez a kód a következő új paraméterek a PyTorch konstruktor tünteti fel:

Paraméter | Leírás | Alapértelmezett
--|--|--
`node_count` |  A betanítási feladathoz használandó csomópontok száma. | `1`
`process_count_per_node` |  Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") száma. | `1`
`distributed_backend` |  Háttérbeli indításakor elosztott képzés, így az a Estimator MPI-n keresztül.  Párhuzamos és elosztott képzési elvégzésére (pl. `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkét) beállítása MPI (és Horovod) `distributed_backend='mpi'`. Az Azure Machine Learning által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/). | `None`

A fenti példában fognak futni az elosztott képzési két feldolgozó egy feldolgozó csomópontonkénti.

Horovod és annak függőségeit lesz telepítve, így egyszerűen importálhatja azt a tanítási szkriptet a `train.py` módon:
```Python
import torch
import horovod
```

Végül küldje el az elosztott PyTorch feladatot:
```Python
run = exp.submit(pt_est)
```

## <a name="examples"></a>Példák

Notebooks az elosztott deep learninget tekintse meg:
* [How-to-use-azureml/Training-with-deep-Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
