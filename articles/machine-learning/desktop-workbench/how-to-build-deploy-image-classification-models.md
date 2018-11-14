---
title: Fejleszthet és telepíthet egy kép osztályozási modell a Computer Vision Azure Machine Learning-csomag használatával.
description: Ismerje meg, hogyan hozhat létre, betanítás, teszteléséhez, modell üzembe helyezése számítógép vision kép besorolás az Azure Machine Learning csomagot használ a Computer Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ROBOTS: NOINDEX
ms.openlocfilehash: f5917cd7a5e4fcc2733765f642ad0958092372c1
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616214"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Létrehozása és üzembe helyezése az Azure Machine Learning képbesorolási modellek

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Ebből a cikkből megtudhatja, hogyan használhatja az Azure Machine Learning csomagot a számítógép Vision (AMLPCV) betanításához, tesztelése és üzembe helyezése egy kép osztályozási modell. Ezt a csomagot és annak részletes műszaki útmutatóját áttekintését [Itt](https://aka.ms/aml-packages/vision).

A számítógép vision tartomány problémák nagy számú kép a fájlbesorolás segítségével megoldhatók. Ezeket a problémákat, amelyek például egyéb kérdésre modellek létrehozásához a következők:
+ _Az ezen az ábrán egy objektum? Például "kutya", "autó", "szállítási" és így tovább_
+ _Milyen osztályba tartozó szemmel betegségek súlyosság szerint a betegek retinal vizsgálat van érintő?_

Összeállításakor, és ez AMLPCV a modell üzembe helyezésével, végigvesszük az alábbi lépéseket:
1. Adatkészlet létrehozása
2. Kép megjelenítési és jegyzet
3. Kép kiegészítését.
4. Neurális hálózat (DNN) modell definíciója
5. Osztályozó által igénybe vett képzés
6. Kipróbálási és Vizualizáció
7. Webszolgáltatás üzembe helyezés
8. Terheléses tesztelés webszolgáltatás

[CNTK](https://www.microsoft.com/cognitive-toolkit/) használja, a deep learning-keretrendszerek képzési helyileg kell elvégezni a GPU-alapú gépek például a ([Deep learning-adatelemzési virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), és üzembe helyezés az Azure Machine Learning Operacionalizálás CLI használja.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

1. A következő fiókok és az alkalmazás kell hozni és telepítve:
   - Egy Azure Machine Learning kísérletezési fiókra 
   - Az Azure Machine Learning Modellkezelés-fiók
   - Egy telepített Azure Machine Learning Workbenchre

   Ha három vannak létrehozott vagy még nincs telepítve, kövesse a [Azure Machine Learning gyors üzembe helyezés és a Workbench telepítési](../desktop-workbench/quickstart-installation.md) cikk. 

1. Az Azure Machine Learning csomag számítógépes Látástechnológiai telepítve kell lennie. Ismerje meg, hogyan [telepíti ezt a csomagot Itt](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Mintaadatok és notebook

### <a name="get-the-jupyter-notebook"></a>A Jupyter notebook beszerzése

Letöltés a notebookot a minta futtatásához az itt leírtak szerint saját magának.

> [!div class="nextstepaction"]
> [A Jupyter notebook beszerzése](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>A mintaadatok betöltése

Az alábbi példában egy adatkészletet, amely 63 asztali rendszerképek. Minden egyes képe címkével négy különböző osztályokhoz (bowl, cup, evőeszközök, lemezt) valamelyikéhez. Az ebben a példában képek számát azért kis, hogy ez a minta gyorsan hajthatók végre. A gyakorlatban meg kell adni legalább 100 képenként osztály. Az összes rendszerkép a következő helyen találhatók *".. /sample_data/imgs_recycling / "*, az alkönyvtárak"bowl"nevű,"cup","evőeszközök"és"lemez".

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix
import matplotlib.pyplot as plt

from classification.notebook.ui_utils.ui_annotation import AnnotationUI
from classification.notebook.ui_utils.ui_results_viewer import ResultsUI
from classification.notebook.ui_utils.ui_precision_recall import PrecisionRecallUI

%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)
```



## <a name="create-a-dataset"></a>Adatkészlet létrehozása

Importálja a függőségeket, és a tárolási környezet beállítása után az adatkészlet-objektumot is létrehozhat.

Az objektum létrehozása az Azure Machine Learning-csomag a Computer Vision, adja meg a gyökérkönyvtár a rendszerképek a helyi lemezen. Ebben a könyvtárban kell, hogy az asztali adatkészletként azonos általános struktúrát követni, a tényleges képekkel alkönyvtárakat tartalmaz:
- legfelső szintű
    - 1. címke
    - 2. címke
    - ...
    - címke n
  
Egy rendszerkép osztályozási modell betanítása egy másik adatkészlet van olyan egyszerű, mintha a gyökér elérési útvonalának megváltoztatása `dataset_location` az alábbi kódot a különböző rendszerképek mutassanak.


```python
# Root image directory
dataset_location = os.path.abspath("classification/sample_data/imgs_recycling")

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

Az adatkészlet-objektum töltse le a rendszerképeket az funkciókat biztosít a [a Bing Image Search API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Kétféle keresési lekérdezések támogatottak: 
+ Hagyományos szövegfájlt lekérdezések
+ Kép URL-lekérdezések

Ezeket a lekérdezéseket a osztály címke együtt kell adni egy JSON kódolású szövegfájl. Példa:

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Továbbá explicit módon létre kell hoznia a Context objektumot, a Bing Image Search API-kulcsot tartalmaz. Ez a Bing Image Search API-előfizetést igényel.

## <a name="visualize-and-annotate-images"></a>Megjelenítheti és jegyzettel láthatja el a rendszerképek

A képek és a megfelelő címkéket az adatkészlet-objektum a következő widget használatával jelenítheti meg. 

Ha a "Widget Javascript nem található" hibát tapasztal, megoldhatja a problémát az alábbi paranccsal:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Képek bővítésével

A [ `augmentation` modul](https://docs.microsoft.com/python/api/cvtk.augmentation) , mivel megvédi a leírt átalakításokat használja egy adatkészlet-objektum funkciókat biztosít a [imgaug](https://github.com/aleju/imgaug) könyvtár. Kép átalakítások csoportosíthatók az egy folyamatot, ebben az esetben a folyamat minden átalakítások alkalmazott egyszerre minden egyes képe. 

Ha másik kiegészítését lépéseket hajtjuk végre külön-külön szeretné, vagy másik oly módon, több folyamatot meghatározása és továbbítja őket a *augment_dataset* függvény. További információk és példák a lemezkép kiegészítését, tekintse meg a [imgaug dokumentáció](https://github.com/aleju/imgaug).

A kibővített lemezképek hozzáadása a gyakorlókészlethez nem különösen hasznos kis adatkészletek esetében. Mivel a DNN betanítási folyamat lassabb betanító kép megnövekedett száma miatt, javasoljuk a Kísérletezési kiegészítését nélkül indítsa el.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>A DNN modellek meghatározása

Ez a csomag a következő pretrained részletes Neurális hálózat-modellek támogatottak: 
+ Resnet-18-ra
+ Resnet-34
+ A Resnet-50
+ Resnet-101
+ Resnet-152

A dnn-eket is használható, besorolás, vagy featurizer. 

A hálózatokkal kapcsolatos további információ található [Itt](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), és átvitel Learning alapszintű bevezetést [Itt](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

A csomag alapértelmezett lemezkép besorolásparaméterek 224 x 224 képpont felbontású és a egy Resnet-18 DNN. Ezek a paraméterek kijelölve számos különböző feladatokat is jól működik. Pontosság gyakran javítható, például a lemezkép felbontás 500 x 500 képpont növelése, és/vagy egy mélyebb modellt (Resnet-50) kiválasztása. Azonban a paraméterek módosítása származhatnak, képzési idő jelentősen növeli. Tekintse meg a cikket a [pontosságának növelése](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Az osztályozó betanítása

A következő módszerek egyikét választhat az előre betanított DNN számára.

  - **A DNN pontosítás**, amely betanítja a DNN közvetlenül a besorolás végrehajtásához. DNN képzési túl lassú, bár általában vezet a legjobb eredmények elérése érdekében óta az összes hálózati súlyok növelhető a legpontosabb biztosíthat betanítás során.

  - **A DNN featurization**, amely fut, mint a DNN-lemezkép alacsonyabb dimenziós reprezentációját kéri (512, 2048 bites és 4096 képest az előtérben). E ábrázolás majd külön besorolás betanítására használja bemenetként. A DNN változatlan marad, mivel ez a módszer sokkal gyorsabban összehasonlítja DNN pontosítás, azonban pontossága nem olyan jó. Mindazonáltal egy külső osztályozó például lineáris SVM betanítása (ahogyan az alábbi kódban látható) is adjon meg egy erős alapkonfiguráció, és segít megérteni a problémát, a megvalósíthatósági.
  
TensorBoard segítségével megjelenítheti a betanítási folyamat állapotát. TensorBoard aktiválása:
1. A paraméter hozzáadása `tensorboard_logdir=PATH` az alábbi kódban látható módon
1. Indítsa el a TensorBoard ügyfelet, a parancs `tensorboard --logdir=PATH` az új konzolon.
1. Nyisson meg egy webböngészőt, utasításai szerint TensorBoard, amely alapértelmezés szerint localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![PNG](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Értékelje ki és modellek teljesítményének megjelenítése

Kiértékelheti a betanított modell egy független vizsgálati adatkészleten az értékelés modullal teljesítményét. Az értékelési mérőszámok, kiszámítja a következők:
 
+ Pontosság (alapértelmezés szerint osztály átlagoláshoz használni)
+ Lekéréses kérelem görbévé
+ ROC-görbét
+ Terület a görbe alatt
+ Keveredési mátrix


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![PNG](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operacionalizálás: üzembe helyezése és felhasználása

Operacionalizálás közzétételi modelleket és webszolgáltatásként kódot és az ezeket a szolgáltatásokat az üzleti eredményeket felhasználását. 

A modell tanítása, miután a modellek webszolgáltatásként, amely a használatalapú telepíthet [Azure Machine Learning parancssori](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). A modellek is üzembe helyezhetők a helyi számítógépen vagy az Azure Container Service (ACS) fürthöz. ACS használatával a manuális skálázása a webszolgáltatás vagy az automatikus skálázási funkciók használatához.

**Jelentkezzen be az Azure CLI**

Használatával egy [Azure](https://azure.microsoft.com/) fiók és a egy érvényes előfizetést, jelentkezzen be a következő CLI-paranccsal:
<br>`az login`

+ Váltson egy másik Azure-előfizetéshez, használja a parancsot:
<br>`az account set --subscription [your subscription name]`

+ Az aktuális modellkezelési fiók megtekintéséhez használja a parancsot:
  <br>`az ml account modelmanagement show`

**Hozzon létre, és állítsa be a fürt üzembe helyezési környezet**

Csak egyszer beállítani az üzembehelyezési környezetet kell. Ha Ön még nem rendelkezik, állítsa be az üzembe helyezési környezet most [ezek az utasítások](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Az aktív központi telepítés környezet megtekintéséhez használja a következő CLI-parancsot:
<br>`az ml env show`
   
Azure CLI-paranccsal minta létrehozása és üzembe helyezési környezet beállítása

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Web services és a központi telepítések kezelése

A következő API-kat helyezhet üzembe modelleket webszolgáltatásként, ezek a webszolgáltatások kezelése és központi telepítések felügyeletéhez használható.

|Tevékenység|API|
|----|----|
|Központi telepítési objektum létrehozása|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Webszolgáltatás üzembe helyezése|`deploy_obj.deploy()`|
|Pontszám kép|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Webszolgáltatás törlése|`deploy_obj.delete()`|
|Webszolgáltatás nélkül docker-rendszerkép létrehozásához|`deploy_obj.build_docker_image()`|
|Meglévő üzemelő példány listázása|`AMLDeployment.list_deployment()`|
|Ha a szolgáltatás már létezik, a központi telepítés nevű törlése|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API-dokumentáció:** tekintse meg a [csomag dokumentációja](https://aka.ms/aml-packages/vision) minden egyes modul és osztály a részletes vonatkozó.

**CLI-referenciáját:** speciális műveletek kapcsolódó központi telepítését, tekintse meg a [modellkezelési parancssori felület referenciája](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**KözpontiTelepítés-felügyelet az Azure Portalon**: nyomon követheti és kezelheti a központi telepítések a [az Azure portal](https://ms.portal.azure.com/). Az Azure Portalról a Machine Learning Modellkezelés-fiók nevének lapon találja. Keresse meg a Modellkezelési fiók lap > Modellkezelési > szolgáltatások.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>A webszolgáltatás használata 

Miután telepíti a modellt webszolgáltatásként, a webszolgáltatással, ezek a módszerek egyikének használatával is pontszámot rendelni a lemezképek:

- A web service közvetlenül a központi telepítési objektum használatával a pontszám `deploy_obj.score_image(image_path_or_url)`

- A végpont URL-címet és szolgáltatási kulcs (nincs helyi telepítés) használata: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- A HTTP-kérések közvetlenül a pontszám a webszolgáltatási végpontot alkotnak. Ez a beállítás akkor tapasztalt felhasználók számára.

### <a name="score-with-existing-deployment-object"></a>A meglévő központi telepítési objektum pontszám

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added parameters. Add softmax to score.
print("Score image url with added parameters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParameters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParameters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>A végpont URL-címe és a szolgáltatáskulcs pontszám

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>HTTP-kérést közvetlenül a pontszám-végpont

Az alábbi példakód a HTTP-kérést közvetlenül a Pythonban képezi. Azonban ezt megteheti a más programozási nyelvek.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional parameters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Webszolgáltatás szerializált eredménye elemzése

A web service a kimenete JSON-karakterláncot. A JSON-karakterlánc, a másik DNN modell osztályok elemezhetők.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>További lépések

További információ az Azure Machine Learning-csomagot a Computer Vision ezekben a cikkekben:

+ Ismerje meg, hogyan [Ez a modell pontosságának javítása](how-to-improve-accuracy-for-computer-vision-models.md).

+ Olvassa el a [csomag áttekintése](https://aka.ms/aml-packages/vision).

+ Fedezze fel a [referenciadokumentációt](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) a csomag számára.

+ Ismerje meg [egyéb Python-csomagokat az Azure Machine Learning](reference-python-package-overview.md).
