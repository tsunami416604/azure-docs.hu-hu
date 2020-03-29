---
title: Ml-modellek betanítása becsléssel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan végezhet egycsomópontos és elosztott képzést a hagyományos gépi tanulási és mélytanulási modellekről az Azure Machine Learning Estimator osztály használatával
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078477"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellek betanítása az Azure Machine Learning használatával a becslés használatával
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning segítségével könnyedén elküldheti a betanítási parancsfájlt [különböző számítási célokba](how-to-set-up-training-targets.md#compute-targets-for-training)egy [RunConfiguration objektum](how-to-set-up-training-targets.md#whats-a-run-configuration) és egy [ScriptRunConfig objektum](how-to-set-up-training-targets.md#submit)használatával. Ez a minta sok rugalmasságot és maximális kontrollt biztosít.

A deep learning modell betanításának megkönnyítése érdekében az Azure Machine Learning Python SDK egy alternatív magasabb szintű absztrakciót biztosít, a estimator osztályt, amely lehetővé teszi a felhasználók számára, hogy könnyen létrefutnak konfigurációk. Létrehozhat és használhat egy általános [estimatort,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) hogy benyújtsa a képzési parancsfájlt bármely választott tanulási keretrendszer (például scikit-learn) használatával bármely kiválasztott számítási célon, legyen az a helyi gép, egyetlen virtuális gép az Azure-ban, vagy egy GPU-fürt az Azure-ban. A PyTorch, TensorFlow és Chainer feladatok hoz, az Azure Machine Learning is rendelkezik a megfelelő [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)és [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) estimators, hogy egyszerűsítse ezeket a keretrendszereket.

## <a name="train-with-an-estimator"></a>Becslővel vonat

Miután létrehozta a [munkaterületet,](concept-workspace.md) és beállította a [fejlesztői környezetet,](how-to-configure-environment.md)a modell betanítása az Azure Machine Learningben a következő lépésekkel jár:  
1. Távoli [számítási cél](how-to-set-up-training-targets.md) létrehozása (megjegyzés: a helyi számítógépet számítási célként is használhatja)
2. A [betanítási adatok](how-to-access-data.md) feltöltése az adattárba (nem kötelező)
3. A [betanítási parancsfájl](tutorial-train-models-with-aml.md#create-a-training-script) létrehozása
4. Egy `Estimator`-objektum létrehozása
5. A becslés beküldése egy kísérleti objektumba a munkaterület alatt

Ez a cikk a 4-5. Az 1-3. [train a model tutorial](tutorial-train-models-with-aml.md)

### <a name="single-node-training"></a>Egycsomópontos képzés

Használjon `Estimator` egy egycsomópontos betanítási futtatást az Azure-ban egy scikit-learn modellhez. Már létre kellett volna [hoznia](how-to-set-up-training-targets.md#amlcompute) a számítási célobjektumot `compute_target` és a [FileDataset](how-to-create-register-datasets.md) objektumot. `ds`

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

Ez a kódrészlet a következő paramétereket `Estimator` adja meg a konstruktornak.

Paraméter | Leírás
--|--
`source_directory`| Helyi könyvtár, amely tartalmazza az összes szükséges kódot a betanítási feladathoz. Ez a mappa a helyi számítógépről a távoli számításra kerül átmásolva.
`script_params`| Szótár, amely párok formájában adja át a betanítási `entry_script` `<command-line argument, value>` parancsfájlnak átadandó parancssori argumentumokat. Ha részletes jelzőt szeretne `script_params`megadni `<command-line argument, "">`a alkalmazásban, használja a használatát.
`compute_target`| Távoli számítási cél, amelyen a betanítási parancsfájl futni fog, ebben az esetben egy Azure Machine Learning Compute[(AmlCompute)](how-to-set-up-training-targets.md#amlcompute)fürt. (Vegye figyelembe, annak ellenére, hogy az AmlCompute-fürt a leggyakrabban használt cél, más számítási céltípusokat is választhat, például az Azure virtuális gépeket vagy akár a helyi számítógépet is.)
`entry_script`| A távoli számítási `source_directory`számítógépen futtatandó betanítási parancsfájl fájlelérési útja .Filepath (a távoli számítási rendszerhez viszonyított) betanítási parancsfájlhoz viszonyítva. Ennek a fájlnak és a további fájloknak, amelyektől függ, ebben a mappában kell lennie.
`conda_packages`| A betanítási parancsfájl által szükséges conda-n keresztül telepítendő Python-csomagok listája.  

A konstruktor nak van egy másik paramétere, `pip_packages` amelyet a szükséges pip csomagokhoz használ.

Most, hogy létrehozta `Estimator` az objektumot, küldje el a betanítási feladatot, `submit` amelyet a `experiment`távoli számítási számítógépen futtat, a [Kísérlet](concept-azure-machine-learning-architecture.md#experiments) objektum függvényének hívásával. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciális mappák** Két mappa, *kimenetek* és *naplók,* különleges bánásmódban részesülnek az Azure Machine Learning. A betanítás során, amikor fájlokat ír *logs* a gyökérkönyvtárhoz (és `./logs``./outputs` a gyökérkönyvtárhoz) viszonyított, *nevű* mappákba, a fájlok automatikusan feltöltődnek a futtatási előzményekbe, így a futtatás befejezése után hozzáférhet hozzájuk.
>
> A betanítás során összetevők (például modellfájlok, ellenőrzőpontok, adatfájlok vagy ábrázolt képek) létrehozásához írja ezeket a `./outputs` mappába.
>
> Hasonlóképpen a betanítási futtatásból a naplót is írhat a `./logs` mappába. Az Azure Machine Learning [TensorBoard-integrációjának](https://aka.ms/aml-notebook-tb) használatához győződjön meg arról, hogy a TensorBoard-naplókat ebbe a mappába írja. Amíg a futás folyamatban van, akkor képes lesz arra, hogy indítson TensorBoard és patak ezeket a naplókat.  Később a korábbi futtatások bármelyikének naplóit is visszaállíthatja.
>
> Például a *kimeneti* mappába írt fájl letöltése a helyi számítógépre a távoli betanítás futtatása után:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Elosztott betanítás és egyéni Docker-lemezképek

Két további képzési forgatókönyvet végezhet el `Estimator`a következőkkel:
* Egyéni Docker-lemezkép használata
* Elosztott képzés többcsomópontos fürtön

A következő kód bemutatja, hogyan végezhet elosztott képzést egy Keras modellhez. Emellett az alapértelmezett Azure Machine Learning-rendszerképek használata helyett egy egyéni docker-lemezképet adocker hubról `continuumio/miniconda` a betanításhoz.

Már létre kellett volna [hoznia](how-to-set-up-training-targets.md#amlcompute) a számítási célobjektumot. `compute_target` A becslőt a következőképpen hozza létre:

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

A fenti kód a következő új `Estimator` paramétereket teszi ki a kivitelezőnek:

Paraméter | Leírás | Alapértelmezett
--|--|--
`custom_docker_image`| A használni kívánt kép neve. Csak a nyilvános docker-adattárakban (ebben az esetben a Docker Hub) elérhető képeket biztosít. A rendszerkép használata egy privát docker-tárházból, `environment_definition` használja a konstruktor paraméter helyett. [Lásd a példát.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb) | `None`
`node_count`| Az edzésfeladathoz használandó csomópontok száma. | `1`
`process_count_per_node`| Az egyes csomópontokon futtatandó folyamatok (vagy "dolgozók") száma. Ebben az esetben az `2` egyes csomópontokon elérhető GPU-kat használja.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objektum az MPI-háttérrendszer használatával elosztott betanítás elindításához.  | `None`


Végül nyújtsa be a képzési munkát:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Modell regisztrálása

Miután betanította a modellt, mentheti és regisztrálhatja azt a munkaterületre. A modellregisztráció lehetővé teszi a modellek tárolását és verziózását a munkaterületen a [modellkezelés és -telepítés egyszerűsítése érdekében.](concept-model-management-and-deployment.md)

A következő kód futtatása regisztrálja a modellt a munkaterületre, és elérhetővé teszi név szerint a távoli számítási környezetekben vagy központi telepítési parancsfájlokban. További [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) információt és további paramétereket a referenciadokumentumokban talál.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub nyomon követése és integrációja

Amikor elindítja a betanítási futtatás, ahol a forráskönyvtár egy helyi Git-tárház, a tárház adatait a futtatási előzmények tárolja. További információ: [Git integration for Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="examples"></a>Példák
A becslőminta alapjait bemutató jegyzetfüzetek a következő témakörökben láthatók:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

A scikit-learn modellt estimator segítségével bevonatozó jegyzetfüzetet lásd:
* [oktatóanyagok/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

A mélytanulási keretrendszer-specifikus becslések használatával a képzési modellekről szóló jegyzetfüzetek a következő témakörökben jelennek meg:

* [hogyan használható azureml/ml-keretrendszerek](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Futási mérőszámok nyomon követése edzés közben](how-to-track-experiments.md)
* [PyTorch-modellek betanítása](how-to-train-pytorch.md)
* [TensorFlow-modellek betanítása](how-to-train-tensorflow.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
* [Betanított modell telepítése](how-to-deploy-and-where.md)
* [Környezetek létrehozása és kezelése képzési és üzembe helyezési célokra](how-to-use-environments.md)