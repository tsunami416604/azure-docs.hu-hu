---
title: Hozza létre, és egy Azure Machine Learning csomag használ a számítógép átfogóan bemutató kép besorolási modell rendszerbe állítása.
description: Megtudhatja, hogyan felépítéséhez, betanítását, tesztelése és egy számítógép átfogóan bemutató kép besorolási modell az Azure Machine Learning csomagot használ a számítógép stratégiai rendszerbe.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Hozza létre és központi telepítése a lemezképet a besorolási modell Azure Machine Learning segítségével

Ebből a cikkből megtudhatja, hogyan használható **Azure Machine Learning csomag számítógép stratégiai** (AMLPCV) képzése, tesztelése és egy kép besorolási modell rendszerbe állítása. 

A számítógép stratégiai tartomány problémák nagy számú kép besorolásával megoldhatók. Ezek a problémák közé tartozik, létrehozási modelleket, mint például a kérdések megválaszolása:
+ _Az objektum megtalálható-e a kép? Például "kutya", "autó", "szállítási", és így tovább_
+ _Ez türelmet retinal vizsgálat során szem elleni súlyosságú mely osztály van érintő?_

Összeállításakor, és ez a modell a AMLPCV telepítése, végrehajtania az alábbi lépéseket:
1. Adatkészlet létrehozása
2. A képi megjelenítés és jegyzet kép
3. Kép bővítés
4. Model Definition mély Neurális hálózat (DNN)
5. Osztályozó képzési
6. Kiértékelési és -megjelenítésre
7. A webszolgáltatás telepítése
8. A webszolgáltatás terhelés tesztelése

[CNTK](https://www.microsoft.com/cognitive-toolkit/) szolgál, mély oktatási keretrendszer képzési helyileg kell elvégezni az alkalmazás bekapcsolja GPU gépen, mint a ([részletes adatok tudományos VM tanulási](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)), és a telepítés használ az Azure ML Operationalization parancssori felület.

Tekintse át a [referenciadokumentációt csomag](https://aka.ms/aml-packages/vision) minden modul és osztály részletes referenciaként.

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

1. A következő fiókok és az alkalmazás kell hozni és telepítve:
   - Egy Azure Machine Learning kísérletezési fiókra 
   - Az Azure Machine Learning modell felügyeleti fiók
   - Egy telepített Azure Machine Learning Workbenchre

   Ha három még nincs létrehozva, és telepítve, kövesse a [Azure Machine Learning gyors üzembe helyezés és a munkaterületet üzemeltető telepítési](../service/quickstart-installation.md) cikk. 

1. Az Azure Machine Learning csomag számítógép stratégiai telepítve kell lennie. Megtudhatja, hogyan [Itt a csomag telepítéséhez](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Mintaadatokat és notebook

### <a name="get-the-jupyter-notebook"></a>A Jupyter notebook beolvasása

A minta futtatásához a notebook letöltési itt leírt magát.

> [!div class="nextstepaction"]
> [A Jupyter notebook beolvasása](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>A mintaadatok betöltése

Az alábbi példában egy 63 asztali képek álló adatkészlet. Egyes lemezképek címkézve négy különböző osztályú (keverőedény, cup, evőeszközök, lemez) valamelyikéhez. Ebben a példában csomópontképek száma nem nagy, hogy ez a minta gyorsan hajtható végre. A gyakorlatban legalább 100 kép / osztály kell megadni. Összes lemezkép találhatók *"... /sample_data/imgs_recycling / "*, alkönyvtárak"keverőedény"nevű,"cup","evőeszközök"és"lemez".

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Adattároló-környezet

Az adattároló-környezet különböző kimeneti fájlok például a kibővített képek vagy DNN modell fájlok tárolására szolgáló meghatározására szolgál. A tárolási környezetek további információkért lásd: a [StorageContext dokumentáció](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Általában a tárolási tartalmat nem kell explicit módon kell beállítani. Azonban a 25 MB-os korlát az Azure Machine Learning-munkaterület által előírt projekt mérete elkerülése érdekében állítsa be a kimeneti könyvtár az Azure Machine Learning csomag számítógép stratégiai kívülre az Azure Machine Learning project (".. /.. /.. /.. / cvtk_output "). Győződjön meg arról, hogy a "cvtk_output" könyvtár eltávolítása után már nem szükséges.


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
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Adatkészlet létrehozása

Miután importált függőségeket és állítsa be az adattároló-környezet, az adatkészlet-objektumot is létrehozhat.

Szeretne létrehozni, hogy az objektum az Azure Machine Learning csomag számítógép stratégiai, adja meg a helyi lemezen a képek gyökérkönyvtárában. Ebben a könyvtárban kell hajtsa végre, amely azonos általános szerkezetét, az asztali adatkészletet, a tényleges lemezképekkel alkönyvtárakat tartalmaz:
- legfelső szintű
    - címke 1
    - Címke 2
    - ...
    - címke-n
  
Egy kép besorolási modell betanítása egy másik dataset érték egyszerű módon, a legfelső szintű elérési útjának módosítása `dataset_location` a következő kódot a különböző képek helyen található.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

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

A dataset objektum lemezképeket letölteni a funkcionalitást biztosítja a [Bing kép Search API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

A keresési lekérdezések két típusok támogatottak: 
+ Rendszeres szöveges lekérdezések
+ Kép URL-lekérdezések

Ezeket a lekérdezéseket a osztály címke együtt belső JSON-kódolású szövegfájl meg kell adni. Példa:

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

Továbbá explicit módon kell létrehoznia egy környezeti objektumot magában foglalja a Bing kép keresési API-kulcsot. Ez a Bing kép Search API előfizetést igényel.

## <a name="visualize-and-annotate-images"></a>Megjelenítheti és lemezképek megjegyzésekkel

A lemezképek és a megfelelő címkéket az adatkészlet objektum használatával a következő widgettel jelenítheti meg. 

Ha a "Widget Javascript nem található" hibát észlel, futtassa, oldja meg ezt a parancsot:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Lemezképek választhasson

A [ `augmentation` modul](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) egy dataset objektum leírt összes átalakítások használata révén a funkcionalitást biztosítja a [imgaug](https://github.com/aleju/imgaug) könyvtárban. Kép átalakítások csoportosíthatók csővezetéket, ebben az esetben a folyamat minden átalakítások alkalmazott egyidejűleg minden kép. 

Ha szeretné alkalmazni a különböző javasolt lépéseket külön-külön, illetve különböző oly módon, határozzon meg a több adatcsatornákat és továbbítsa azokat a *augment_dataset* függvény. További útmutatást és példákat a kép növelését, tekintse meg a [imgaug dokumentáció](https://github.com/aleju/imgaug).

Kibővített lemezképek hozzáadása a gyakorlókészlethez akkor különösen hasznos kisebb adatkészletek esetében. Mivel a DNN képzési folyamat lassabb a megnövekedett számú képzési kép miatt, javasoljuk, kísérletezhet bővítés nélkül indítsa el.


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

## <a name="define-dnn-models"></a>Adja meg a DNN modellek

A következő pretrained mély Neurális hálózat modellek csomaggal támogatottak: 
+ AlexNet
+ Resnet 18.
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

Ezek DNNs osztályozó, vagy featurizer használható. 

További információ a hálózatok található [Itt](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), és átviteli tanulási alapszintű bevezetést [Itt](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

A csomag alapértelmezett kép besorolásparaméterek 224 x 224 képpont felbontású és Resnet-18-DNN. Ezek a paraméterek számos feladatot is jól működik sincs kijelölve. Pontosság gyakran növelhető, például a lemezkép névfeloldást 500 x 500-as képponttal növelését, és/vagy egy mélyebb modell (Resnet-50) kiválasztása. Azonban a paraméterek módosítása származhatnak időben képzési jelentősen növelheti a. Olvassa el a [pontosságának javítására](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


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
    

## <a name="train-the-classifier"></a>A besorolás képzése

Választhatja a következő módszerek egyikét a előre képzett DNN.

  - **DNN pontosítás**, amely a besorolási közvetlenül végrehajtásához DNN betanítja. Míg a DNN képzési lassú, általában vezet a legjobb eredmények elérése érdekében óta az összes hálózati súlyok növelhető a legpontosabb adhat betanítás során.

  - **DNN featurization**, amely fut, mint a DNN-lemezkép alsó dimenziós ábrázolását le (512, 2048 vagy 4096 képest az előtérben). Adott ábrázolását majd szolgál egy külön osztályozó képzése érdekében. A DNN változatlan marad, mert ez a módszer sokkal gyorsabb, összeveti DNN pontosítás, azonban pontossága nem a helyes. Mindazonáltal egy külső osztályozó, például egy lineáris SVM betanítása (ahogy az alábbi kód) is erős alapértékek biztosítása, és segítenek megérteni a problémát megvalósíthatóságának.
  
TensorBoard segítségével jelenítheti meg a képzés folyamatban van. TensorBoard aktiválása:
1. A paraméter hozzáadása `tensorboard_logdir=PATH` az alábbi kódban látható módon
1. Indítsa el a parancs TensorBoard ügyfélre `tensorboard --logdir=PATH` új konzolban.
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


## <a name="evaluate-and-visualize-model-performance"></a>Értékelje ki és jelenítheti meg modell teljesítmény

Kiértékelheti a betanított modell egy önálló vizsgálati adatkészlethez az értékelés modullal teljesítményét. A kiértékelési metrikák azt kiszámítja a következők:
 
+ A pontosság (osztály átlagosan alapértelmezés)
+ Törlés a ka görbévé
+ : ROC-görbe
+ Terület a görbe alatt
+ Zavart mátrix


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

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Az Azure Machine Learning-adatkészlet](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Operationalization: telepítheti, és felhasználása

Operationalization közzétételi modellek és kód webszolgáltatásként, és ezek a szolgáltatások üzleti eredmények eredményezett fogyasztásának. 

Ha a modell betanítása, telepítése, hogy a modell fogyasztás használatára vonatkozó webszolgáltatásként [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). A modellek is telepíthető a helyi számítógépen vagy Azure tároló szolgáltatás (ACS) fürthöz. ACS használatával manuálisan méretezheti a webszolgáltatás vagy az automatikus skálázás funkcióival.

**Jelentkezzen be az Azure parancssori felület**

Használatával egy [Azure](https://azure.microsoft.com/) egy érvényes előfizetéssel fiókot, jelentkezzen be a következő parancssori parancsot:
<br>`az login`

+ Egy másik Azure-előfizetésre váltani, használja a parancsot:
<br>`az account set --subscription [your subscription name]`

+ Az aktuális modell felügyeleti fiók megtekintéséhez használja a parancsot:
  <br>`az ml account modelmanagement show`

**Hozzon létre, és a telepítési környezet beállítása**

Csak a telepítési környezet egyszer be kell. Ha egy még nem rendelkezik, állítsa be a telepítési környezet megismerésére [ezeket az utasításokat](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Kövesse a helyi vagy a fürt telepítési beállítási lépéseket az igényeknek megfelelően alapján.
+ Helyi telepítések támogatottak, a Linux és Windows 10 gépek, de nem Windows adatok tudományos a virtuális gép vagy a mély tanulási virtuális Gépet. 
+ Linux és a Windows fürt környezetben történő telepítések esetén támogatottak. 

Az aktív központi telepítéssel környezet, használja a következő parancssori parancsot:
<br>`az ml env show`
   
A minta Azure CLI parancs létrehozása és központi telepítési környezet beállítása

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Webszolgáltatások és központi telepítések kezelése

A következő API-k segítségével telepítheti a modellek webszolgáltatásként, webes szolgáltatások kezelése és központi telepítések felügyeletéhez szükséges.

|Tevékenység|API|
|----|----|
|Központi telepítési objektum létrehozása|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Webszolgáltatás telepítése|`deploy_obj.deploy()`|
|Pontszám kép|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Webszolgáltatás törlése|`deploy_obj.delete()`|
|Docker lemezkép webszolgáltatás nélkül|`deploy_obj.build_docker_image()`|
|Meglévő központi telepítési felsorolása|`AMLDeployment.list_deployment()`|
|Ha a szolgáltatás létezik-e a központi telepítési nevű törlése|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API-JÁNAK dokumentációja:** további részleteket a [referenciadokumentációt csomag](https://aka.ms/aml-packages/vision) minden modul és osztály részletes referenciaként.

**Parancssori felület hivatkozás:** a speciális kapcsolatos műveletek központi telepítését, tekintse meg a [modellhez tartozó felügyeleti CLI hivatkozás](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**KözpontiTelepítés-felügyelet az Azure portálon**: nyomon követése, és a központi telepítések kezelése a [Azure-portálon](https://ms.portal.azure.com/). Azure-portálról a Machine Learning modell felügyeleti-fiók nevének lapon található. Folytassa a modell kezelése fióklapját > modell kezelése > szolgáltatások.


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

### <a name="consume-the-web-service"></a>A webszolgáltatás felhasználása 

Miután telepíti a modell webszolgáltatásként, a képek is pontozása a a webszolgáltatással, ezek a módszerek egyikének használatával:

- A webszolgáltatás közvetlenül a központi telepítési objektum használatával pontozása `deploy_obj.score_image(image_path_or_url)`

- A szolgáltatás URL-cím és a szolgáltatás végpontkulcs (nincs helyi telepítés) használata: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- A HTTP-kérelmek közvetlenül a webszolgáltatási végpontot pontozása céljából alkotnak. Ez a beállítás akkor a tapasztalt felhasználók számára.

### <a name="score-with-existing-deployment-object"></a>A meglévő központi telepítési objektum pontozása

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

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
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

### <a name="score-with-service-endpoint-url-and-service-key"></a>Pontszám végpont URL-címe és szolgáltatási kulcs

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

### <a name="score-endpoint-with-http-request-directly"></a>Pontszám végpont közvetlenül a http-kérelem

Az alábbi példakód a HTTP-kérést közvetlenül a Python képezi. Azonban ezt megteheti más programozási nyelven.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


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

### <a name="parse-serialized-result-from-web-service"></a>A webszolgáltatás szerializált eredmény elemzése

A webes szolgáltatás eredménye egy JSON-karakterláncban. A JSON-karakterláncban a különböző DNN modell osztályok tudja értelmezni.


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

További Azure Machine Learning-csomaggal kapcsolatban az számítógép stratégiai a cikkeiben:

+ Megtudhatja, hogyan [javítja ezt a modellt a](how-to-improve-accuracy-for-computer-vision-models.md).

+ Olvassa el a [csomag áttekintése, és megtudhatja, hogyan telepítheti](https://aka.ms/aml-packages/vision).

+ Megismerkedhet a [dokumentáció](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) a csomag számára.

+ További tudnivalók [egyéb Python-csomagokat, az Azure Machine Learning](reference-python-package-overview.md).
