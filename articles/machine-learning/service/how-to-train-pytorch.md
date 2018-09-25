---
title: Az Azure Machine Learning PyTorch modelleket taníthat be
description: Ismerje meg, hogyan futtathat egy csomópontos és elosztott képzési PyTorch modellek a PyTorch estimator
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 09/24/2018
ms.openlocfilehash: e569b63f676fb750bcbab88dda6cda39156d41f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977032"
---
# <a name="how-to-train-pytorch-models"></a>Hogyan PyTorch modelleket taníthat be

Azure Machine Learning Neurális hálózat (DNN) képzést nyújt PyTorch, itt a Estimator egyéni PyTorch osztályát. Az Azure SDK PyTorch Estimator lehetővé teszi, hogy könnyedén beküldhető PyTorch mind egyetlen csomópontot, és elosztott futtatások az Azure-beli számítási feladatok.

## <a name="single-node-training"></a>Egy csomópontos képzés
A PyTorch Estimator képzést hasonlít a használatával a [Estimator kiinduló](how-to-train-ml-models.md), ezért először olvassa el a cikkben található útmutató, és ellenőrizze, hogy tisztában van a bemutatott fogalmakkal.
  
Hozza létre a PyTorch feladatok futtatásához egy `PyTorch` objektum. Már létrehozott kell a [számítási célt](how-to-set-up-training-targets.md#batch) objektum `compute_target` és a [adattárolója](how-to-access-data.md) objektum `ds`.

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
* `source_directory`: A helyi könyvtárban, amely tartalmazza az összes a betanítási feladathoz szükséges kódot. Ez a mappa a távoli számítási átmásolódnak a helyi gépen
* `script_params`: Egy a tanítási szkriptet a parancssori argumentumok megadása szótárban `entry_script`, < parancssori argumentum, érték > formájában párok
* `compute_target`: A távoli számítási arról, hogy az a tanítási szkriptet, ebben az esetben egy [Batch AI](how-to-set-up-training-targets.md#batch) fürt
* `entry_script`: A fájl elérési útja (viszonyítva a `source_directory`), a tanítási szkriptet futtatandó távoli számítási. Ezt a fájlt, és a további fájlokat attól függ, ebben a mappában kell elhelyezni.
* `conda_packages`: Python-csomagokat a tanítási szkriptet által igényelt conda-n keresztül kell telepíteni a listája.
A konstruktor rendelkezik egy másik nevű paramétert `pip_packages` használható az esetleges pip csomagokat szükséges
* `use_gpu`: Ezt a jelzőt `True` kihasználhatja a GPU, a betanításhoz. Az alapértelmezett érték `False`

Mivel a PyTorch estimator használ, a képzési használt tároló alapértelmezés szerint a PyTorch csomag és a szükséges képzés a processzorok és gpu-k kapcsolódó függőségeket tartalmaznak.

Ezután küldje el a PyTorch feladatot:
```Python
run = exp.submit(pt_est)
```

## <a name="distributed-training"></a>Elosztott betanítás
A PyTorch Estimator emellett lehetővé teszi különböző Azure-beli virtuális Processzor és GPU fürtök ipari méretekben a modellek betanítása. Könnyedén futtathat elosztott PyTorch képzési néhány API-hívások, amíg az Azure Machine Learning fogja kezelni a háttérben, az infrastruktúra és a vezénylési ilyen számítási feladat végrehajtásához szükséges.

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

A fenti kód a következő új paraméterek a PyTorch konstruktor tünteti fel:
* `node_count`: A betanítási feladathoz használandó csomópontok száma. Ennek az argumentumnak az alapértelmezett `1`
* `process_count_per_node`: Minden egyes csomóponton futtatandó folyamatok (vagy "dolgozó szakemberek") a száma. Ennek az argumentumnak az alapértelmezett `1`
* `distributed_backend`: A háttérrendszer indításakor elosztott képzés, így az a Estimator MPI-n keresztül. Ennek az argumentumnak az alapértelmezett `None`. Ha szeretné-e a párhuzamos és elosztott képzési végez (például `node_count`> 1 vagy `process_count_per_node`> 1 vagy mindkét) beállítása MPI (és Horovod), `distributed_backend='mpi'`. Az Azure Machine Learning által használt MPI végrehajtása [nyílt MPI](https://www.open-mpi.org/).

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
Egy csomópontos PyTorch képzési oktatóanyagért lásd:
* `training/01.train-tune-deploy-pytorch/01.train-tune-deploy-pytorch.ipynb`

Elosztott PyTorch Horovod az oktatóanyagot tekintse meg:
* `training/02.distributed-pytorch-with-horovod/02.distributed-pytorch-with-horovod.ipynb`

Ezeket a notebookokat lekérése:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
* [Metrikák futtatása a betanítás során nyomon követése](how-to-track-experiments.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [A betanított modell üzembe helyezése](how-to-deploy-and-where.md)
