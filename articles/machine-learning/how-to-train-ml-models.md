---
title: ML modellek betanítása a becslések
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hajthat végre egycsomópontos és elosztott képzéseket hagyományos gépi tanulási és Deep learning-modellekben Azure Machine Learning kalkulátor osztály használatával
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 3205f853088245461e854562999164f9813f3bd5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536628"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellek betanítása Azure Machine Learning a kalkulátor használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Azure Machine Learning segítségével könnyedén elküldheti a betanítási szkriptet [különféle számítási célokba](how-to-set-up-training-targets.md#compute-targets-for-training)egy [RunConfiguration-objektum](how-to-set-up-training-targets.md#whats-a-run-configuration) és egy [ScriptRunConfig objektum](how-to-set-up-training-targets.md#submit)használatával. Ez a minta sok rugalmasságot és maximális szabályozást biztosít.

A részletes tanulási modell tanításának elősegítése érdekében a Azure Machine Learning Python SDK egy alternatív, magasabb szintű absztrakciót, a kalkulátor osztályt biztosít, amely lehetővé teszi a felhasználók számára a futtatási konfigurációk egyszerű összeállítását. Létrehozhat és használhat egy általános számítást, amellyel betaníthatja [az oktatási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) szkriptet bármely kiválasztott képzési keretrendszer (például a scikit-Learn) bármely kiválasztott számítási célra, legyen az a helyi gép, egyetlen virtuális gép az Azure-ban vagy egy Azure-beli GPU-fürt. A PyTorch, a TensorFlow és a láncolási feladatok esetében a Azure Machine Learning a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [láncolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) becslések is biztosít, hogy egyszerűbbé váljon ezen keretrendszerek használata.

## <a name="train-with-an-estimator"></a>Betanítás kalkulátorral

Miután létrehozta a [munkaterületet](concept-workspace.md) , és beállította a [fejlesztési környezetet](how-to-configure-environment.md), a modell betanítása Azure Machine learning a következő lépésekkel jár:  
1. Hozzon létre egy [távoli számítási célt](how-to-set-up-training-targets.md) (vegye figyelembe, hogy a helyi számítógépet számítási célként is használhatja)
2. [Betanítási adatok](how-to-access-data.md) feltöltése az adattárba (opcionális)
3. A [betanítási parancsfájl](tutorial-train-models-with-aml.md#create-a-training-script) létrehozása
4. Egy `Estimator`-objektum létrehozása
5. A kalkulátor beküldése a munkaterület alá tartozó kísérleti objektumra

Ez a cikk az 4-5. lépésre koncentrál. Az 1-3-as lépéseknél tekintse meg a [Train a Model oktatóanyagot](tutorial-train-models-with-aml.md) példaként.

### <a name="single-node-training"></a>Egy csomópontos képzés

Az Azure-ban a scikit-Learn modellben egy egycsomópontos képzéshez `Estimator` használhat. Létre kell hoznia a [számítási cél](how-to-set-up-training-targets.md#amlcompute) objektumot `compute_target` és az [adattár](how-to-access-data.md) -objektumot `ds`.

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

Ez a kódrészlet a következő paramétereket adja meg a `Estimator` konstruktorhoz.

Paraméter | Leírás
--|--
`source_directory`| A betanítási feladatokhoz szükséges összes kódot tartalmazó helyi könyvtár. Ezt a mappát a rendszer átmásolja a helyi gépről a távoli számításba.
`script_params`| A szótár adja meg a betanítási parancsfájlnak `entry_script``<command-line argument, value>` pár formájában átadandó parancssori argumentumokat. A `script_params`ban található részletes jelző megadásához használja a `<command-line argument, "">`.
`compute_target`| Távoli számítási cél, amelyet a képzési parancsfájl fog futni, ebben az esetben egy Azure Machine Learning számítási ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)-) fürt. (Vegye figyelembe, hogy bár a AmlCompute-fürt a leggyakrabban használt cél, más számítási célpontok is kiválaszthatók, például az Azure-beli virtuális gépek vagy akár a helyi számítógép is.)
`entry_script`| A távoli számításon futtatandó betanítási szkript filepath (`source_directory`képest). Ez a fájl és az attól függő további fájlok a mappában találhatók.
`conda_packages`| Azoknak a Python-csomagoknak a listája, amelyeket a betanítási szkriptnek szüksége van a Conda-on keresztül.  

A konstruktor egy másik, `pip_packages` nevű paraméterrel rendelkezik, amelyet minden szükséges pip-csomaghoz használhat.

Most, hogy létrehozta a `Estimator` objektumot, küldje el a betanítási feladatot úgy, hogy a távoli számításon fusson, és hívja meg a `submit` függvényt a [kísérlet](concept-azure-machine-learning-architecture.md#experiments) objektumon `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciális mappák** Két mappa, *kimenet* és *napló*, a Azure Machine learning speciális kezelést kap. Ha a betanítás során a rendszer a gyökérkönyvtárhoz viszonyított *kimenetek* és *naplók* nevű mappákba ír fájlokat (`./outputs` és `./logs`), a fájlok automatikusan feltöltve lesznek a futtatási előzményekbe, hogy a Futtatás befejezése után hozzáférhessenek hozzájuk.
>
> Az összetevők a betanítás során történő létrehozásához (például a Model Files, az ellenőrzőpontok, az adatfájlok vagy a képek kirajzolása) írja ezeket a `./outputs` mappába.
>
> Hasonlóképpen bármilyen naplót írhat a betanításból a `./logs` mappába. Azure Machine Learning [TensorBoard-integrációjának](https://aka.ms/aml-notebook-tb) kihasználása érdekében ügyeljen arra, hogy a TensorBoard-naplókat a mappába írja. Amíg a Futtatás folyamatban van, el tudja indítani a TensorBoard, és továbbíthatja ezeket a naplókat.  Később is visszaállíthatja a naplókat az előző futtatások bármelyikéről.
>
> Ha például le szeretné tölteni a *kimeneti* mappába írt fájlt a helyi gépre a távoli képzés futtatása után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott képzés és egyéni Docker-rendszerképek

Két további tanítási forgatókönyv is elvégezhető a `Estimator`:
* Egyéni Docker-rendszerkép használata
* Elosztott képzés több csomópontos fürtön

A következő kód bemutatja, hogyan hajthat végre elosztott képzést egy kerasz-modellhez. Emellett az alapértelmezett Azure Machine Learning rendszerképek használata helyett egyéni Docker-rendszerképet ad meg a Docker hub `continuumio/miniconda` a betanításhoz.

Létre kell hoznia a [számítási cél](how-to-set-up-training-targets.md#amlcompute) objektumot `compute_target`. A következő módon hozhatja létre a kalkulátort:

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
`custom_docker_image`| A használni kívánt rendszerkép neve. Csak nyilvános Docker-adattárakban (ebben az esetben a Docker hub-ban) elérhető lemezképek megadása. Ha privát Docker-tárházból szeretne képet használni, használja helyette a konstruktor `environment_definition` paraméterét. [Lásd: példa](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| A betanítási feladatokhoz használni kívánt csomópontok száma. | `1`
`process_count_per_node`| Az egyes csomópontokon futtatandó folyamatok (vagy "feldolgozók") száma. Ebben az esetben az egyes csomópontokon elérhető `2` GPU-ket használja.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektum az elosztott KÉPZÉSek MPI-háttér használatával történő elindításához.  | `None`


Végül küldje el a betanítási feladatot:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Modell regisztrálása

A modell kiképzése után mentheti és regisztrálhatja azt a munkaterületen. A modell regisztrálása lehetővé teszi a modellek tárolását és verzióját a munkaterületen a [modell kezelésének és üzembe helyezésének](concept-model-management-and-deployment.md)egyszerűsítése érdekében.

A következő kód futtatásával regisztrálja a modellt a munkaterületre, és elérhetővé teszi a név alapján a távoli számítási környezetekben vagy az üzembe helyezési parancsfájlokban való hivatkozáshoz. További információ és további paraméterek: [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) a dokumentációban.

```python
model = run.register_model(model_name='sklearn-sample')
```

## <a name="github-tracking-and-integration"></a>GitHub-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="examples"></a>Példák
A becslési minta alapjait megjelenítő jegyzetfüzetek esetében lásd:
* [használati útmutató – azureml/képzés – részletesen tanulás/használati útmutató – kalkulátor](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Olyan jegyzetfüzetek esetében, amelyek a scikit-tanulási modellt a kalkulátor használatával végzik, lásd:
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

A betanítási modellekhez tartozó jegyzetfüzetek mélyreható tanulási keretrendszer-specifikus becslések való használatával kapcsolatban lásd:

* [használati útmutató – azureml/ml – keretrendszerek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* [A futtatási metrikák nyomon követése a betanítás során](how-to-track-experiments.md)
* [PyTorch-modellek betanítása](how-to-train-pytorch.md)
* [TensorFlow-modellek betanítása](how-to-train-tensorflow.md)
* [Hiperparaméterek beállítása hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
