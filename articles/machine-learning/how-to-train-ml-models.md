---
title: ML modellek betanítása a becslések
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hajthat végre egycsomópontos és elosztott képzéseket hagyományos gépi tanulási és Deep learning-modellekben Azure Machine Learning kalkulátor osztály használatával
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 70e965e26d3b82cdc63a3c0e147919b8b40585af
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146589"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellek betanítása Azure Machine Learning a kalkulátor használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Azure Machine Learning segítségével könnyedén elküldheti a betanítási szkriptet [különféle számítási célokba](how-to-set-up-training-targets.md)egy [RunConfiguration-objektum](how-to-set-up-training-targets.md#whats-a-run-configuration) és egy [ScriptRunConfig objektum](how-to-set-up-training-targets.md#submit)használatával. Ez a minta sok rugalmasságot és maximális szabályozást biztosít.


A kalkulátorok osztály megkönnyíti a modellek betanítását a mély tanulással és a megerősítő tanulással. Magas szintű absztrakciót biztosít, amely lehetővé teszi a futtatási konfiguráció egyszerű összeállítását. Létrehozhat és használhat egy általános számítást, amellyel betaníthatja [az oktatási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) szkriptet bármely kiválasztott képzési keretrendszer (például a scikit-Learn) bármely kiválasztott számítási célra, legyen az a helyi gép, egyetlen virtuális gép az Azure-ban vagy egy Azure-beli GPU-fürt. A PyTorch, a TensorFlow, a láncolás és a megerősítő tanulási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), [láncolást](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)és a [megerősítő tanulási](how-to-use-reinforcement-learning.md) becslések is biztosít, hogy egyszerűbbé váljon ezek a keretrendszerek.

## <a name="train-with-an-estimator"></a>Betanítás kalkulátorral

Miután létrehozta a [munkaterületet](concept-workspace.md) , és beállította a [fejlesztési környezetet](how-to-configure-environment.md), a modell betanítása Azure Machine learning a következő lépésekkel jár:  
1. Hozzon létre egy [távoli számítási célt](how-to-create-attach-compute-sdk.md) (vagy helyi számítógépet is használhat számítási célként)
2. [Betanítási adatok](how-to-access-data.md) feltöltése az adattárba (opcionális)
3. A [betanítási parancsfájl](tutorial-train-models-with-aml.md#create-a-training-script) létrehozása
4. Egy `Estimator`-objektum létrehozása
5. A kalkulátor beküldése a munkaterület alá tartozó kísérleti objektumra

Ez a cikk az 4-5. lépésre koncentrál. Az 1-3-as lépéseknél tekintse meg a [Train a Model oktatóanyagot](tutorial-train-models-with-aml.md) példaként.

### <a name="single-node-training"></a>Egy csomópontos képzés

Az `Estimator` Azure-ban a scikit-Learn modell esetében egy egycsomópontos betanítást használhat a távoli számítási feladatokhoz. A [számítási cél](how-to-create-attach-compute-sdk.md#amlcompute) objektumot `compute_target` és a [FileDataset](how-to-create-register-datasets.md) objektumot már létrehozta `ds` .

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Ez a kódrészlet a következő paramétereket adja meg a `Estimator` konstruktor számára.

Paraméter | Leírás
--|--
`source_directory`| A betanítási feladatokhoz szükséges összes kódot tartalmazó helyi könyvtár. Ezt a mappát a rendszer átmásolja a helyi gépről a távoli számításba.
`script_params`| A szótárban adja meg azokat a parancssori argumentumokat, amelyeket át kell adni a betanítási parancsfájlnak `entry_script` `<command-line argument, value>` párok formájában. Ha részletes jelölőt szeretne megadni a alkalmazásban, használja a következőt: `script_params` `<command-line argument, "">` .
`compute_target`| Távoli számítási cél, amelyet a képzési parancsfájl fog futni, ebben az esetben egy Azure Machine Learning számítási ([AmlCompute](how-to-create-attach-compute-sdk.md#amlcompute)-) fürt. (Vegye figyelembe, hogy bár a AmlCompute-fürt a leggyakrabban használt cél, más számítási célpontok is kiválaszthatók, például az Azure-beli virtuális gépek vagy akár a helyi számítógép is.)
`entry_script`| A `source_directory` távoli számításon futtatandó betanítási szkript filepath (a-hez képest). Ez a fájl és az attól függő további fájlok a mappában találhatók.
`conda_packages`| Azoknak a Python-csomagoknak a listája, amelyeket a betanítási szkriptnek szüksége van a Conda-on keresztül.  

A konstruktornak van egy másik paramétere `pip_packages` , amelyet minden szükséges pip-csomaghoz használhat.

Most, hogy létrehozta az `Estimator` objektumot, küldje el a betanítási feladatot a távoli számításban, és hívja meg a `submit` függvényt a [kísérleti](concept-azure-machine-learning-architecture.md#experiments) objektumon `experiment` . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciális mappák** Két mappa, *kimenet* és *napló*, a Azure Machine learning speciális kezelést kap. Ha a betanítás során a rendszer a gyökérkönyvtárhoz viszonyított *kimeneteket* és *naplókat* tartalmazó mappákba ír fájlokat `./outputs` , `./logs` a fájlok automatikusan feltöltve lesznek a futtatási előzményekbe, hogy a Futtatás befejezése után hozzáférhessenek hozzájuk.
>
> Az összetevőknek a betanítás során történő létrehozásához (például a Model Files, az ellenőrzőpontok, az adatfájlok vagy a képek kirajzolása) írja ezeket a `./outputs` mappába.
>
> Hasonlóképpen bármilyen naplót írhat a betanításból a `./logs` mappába. Azure Machine Learning [TensorBoard-integrációjának](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) kihasználása érdekében ügyeljen arra, hogy a TensorBoard-naplókat a mappába írja. Amíg a Futtatás folyamatban van, el tudja indítani a TensorBoard, és továbbíthatja ezeket a naplókat.  Később is visszaállíthatja a naplókat az előző futtatások bármelyikéről.
>
> Ha például le szeretné tölteni a *kimenet* mappába a helyi gépre írt fájlt a távoli képzés futtatása után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott képzés és egyéni Docker-rendszerképek

Két további tanítási forgatókönyv is elvégezhető a következőkkel `Estimator` :
* Egyéni Docker-rendszerkép használata
* Elosztott képzés több csomópontos fürtön

A következő kód bemutatja, hogyan hajthat végre elosztott képzést egy kerasz-modellhez. Emellett az alapértelmezett Azure Machine Learning rendszerképek használata helyett a Docker hub egyéni Docker-rendszerképét adja meg `continuumio/miniconda` a betanításhoz.

Ehhez már létre kell hoznia a [számítási cél](how-to-create-attach-compute-sdk.md#amlcompute) objektumot `compute_target` . A következő módon hozhatja létre a kalkulátort:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

A fenti kód a következő új paramétereket teszi elérhetővé a `Estimator` konstruktor számára:

Paraméter | Leírás | Alapértelmezett
--|--|--
`custom_docker_image`| A használni kívánt rendszerkép neve. Csak nyilvános Docker-adattárakban (ebben az esetben a Docker hub-ban) elérhető lemezképek megadása. Ha privát Docker-tárházból szeretne képet használni, használja helyette a konstruktor `environment_definition` paraméterét.| `None`
`node_count`| A betanítási feladatokhoz használni kívánt csomópontok száma. | `1`
`process_count_per_node`| Az egyes csomópontokon futtatandó folyamatok (vagy "feldolgozók") száma. Ebben az esetben az `2` egyes csomópontokon elérhető GPU-k használhatók.| `1`
`distributed_training`| [MPIConfiguration ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektum az elosztott KÉPZÉSek MPI-háttér használatával történő elindításához.  | `None`


Végül küldje el a betanítási feladatot:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Modell regisztrálása

A modell kiképzése után mentheti és regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

A következő kód futtatásával regisztrálja a modellt a munkaterületre, és elérhetővé teszi a név alapján a távoli számítási környezetekben vagy az üzembe helyezési parancsfájlokban való hivatkozáshoz. [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)További információkat és további paramétereket a dokumentációban talál.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="examples"></a>Példák

Olyan jegyzetfüzetek esetében, amelyek a scikit-tanulási modellt a kalkulátor használatával végzik, lásd:
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

A betanítási modellekhez tartozó jegyzetfüzetek mélyreható tanulási keretrendszer-specifikus becslések való használatával kapcsolatban lásd:

* [használati útmutató – azureml/ml – keretrendszerek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [PyTorch-modellek betanítása](how-to-train-pytorch.md)
* [TensorFlow-modellek betanítása](how-to-train-tensorflow.md)
* [A megerősítő tanulási mély neurális hálózat betanítása](how-to-use-reinforcement-learning.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
* [Környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md)