---
title: Fejleszthet és telepíthet egy Azure Machine Learning-csomagot használ a Computer Vision objektum modell.
description: Ismerje meg, hogyan hozhat létre, betanítását, tesztelése és üzembe helyezése egy számítógépes látástechnológiai objektum észlelési modellel az Azure Machine Learning csomagot használ a Computer Vision.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 166adc2b464ede5ba77d049075479e3b37a02a88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954858"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Hozhat létre, és az Azure Machine Learning objektumot észlelési modellek üzembe helyezése

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Ebből a cikkből megtudhatja, hogyan használható **Azure Machine Learning-csomagja Computer Vision** munkámnak, teszteléséhez és üzembe egy [gyorsabban R-CNN](https://arxiv.org/abs/1506.01497) észlelési hálózatiobjektum-modellt. 

A számítógép vision tartomány problémák nagy számú objektum észlelési használatával kell megoldani. Ezek a problémák közé tartozik a belső modelleket, hogy a lemezképben található objektumok változó számú található. 

Összeállításakor és helyezi üzembe a modellt a csomaggal, akkor lépjen az alábbi lépéseket:
1.  Adatkészlet létrehozása
2.  Neurális hálózat (DNN) modell definíciója
3.  Modell betanítása
4.  Kipróbálási és Vizualizáció
5.  Webszolgáltatás üzembe helyezés
6.  Terheléses tesztelés webszolgáltatás

Ebben a példában tensorflow-hoz, a deep learning-keretrendszert használ, a képzési helyileg kell elvégezni a GPU-alapú gépek például a [Deep learning-adatelemzési virtuális gép](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), és üzembe helyezés az Azure Machine Learning Operacionalizálás CLI használja.

Tekintse át a [csomag dokumentációja](https://aka.ms/aml-packages/vision) minden egyes modul és osztály a részletes vonatkozó.

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
> [A Jupyter notebook beszerzése](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>A mintaadatok betöltése

Ebben a bemutatóban pékséglánc elemek belül hűtőszekrények adatkészlet áll rendelkezésre, 30 képek és 8 osztályok (eggBox, joghurt, ketchup, gomba, ezredes, orange, squash és víz). Az egyes jpg-lemezképek van egy jegyzet xml-fájl hasonló nevű. 

A következő ábrán látható, a javasolt gyökérmappa-szerkezetében. 

![gyökérmappa-szerkezetében](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Kép jegyzet

Helyek betanítása és kiértékelése az objektum detector használatával szükséges objektum feliratozva. [LabelImg](https://tzutalin.github.io/labelImg) egy nyílt forráskódú jegyzet eszköz lemezképek megjegyzésekkel használható. LabelImg ír egy xml-fájl képenként Pascal-VOC formátumban, amely a csomag által is olvasható. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Adatkészlet létrehozása

Hozzon létre egy CVTK adatkészletet, amely a képek a megfelelő határoló box jegyzetekkel ellátott áll. Ebben a példában a hűtőszekrények lemezképeket, amelyek szerepelnek a ".. / sample_data/élelmiszerek/képzési"mappát használ. Csak a JPEG-képek használata támogatott.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![PNG](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>A modellek meghatározásához

Ebben a példában a gyorsabb R-CNN-modellt használja. Különböző paramétereket meghatározásakor az ebben a modellben adható meg. Ezeket a paramétereket, valamint oktatási (lásd a következő szakaszban) használt paraméterek szerinti található bármelyik CVTK API-dokumentumok, illetve a a [Tensorflow objektum észlelési webhely](https://github.com/tensorflow/models/tree/master/research/object_detection). További információ a gyorsabb R-CNN modell található [ezt a hivatkozást](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Ez a modell a gyors R-CNN-alapú, és további információ található [Itt](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>A modell betanítása

A COCO betanított gyorsabban R-CNN modell ResNet50 képzési kiindulási pontként szolgál. 

Az érzékelő betanításához képzési lépéseket és a kód 350, van beállítva, hogy képzési gyorsabban fut (~ 5 perc grafikus processzort tartalmaz). A gyakorlatban állítsa a betanítási készlet lemezképek száma legalább 10 alkalommal.

Ebben a példában detector használatával képzési lépéseket és gyors képzést 350 értéke. Viszont a gyakorlatban, jó tapasztalatok, hogy a lépéseket-ra állítja legalább 10 alkalommal képek számát a gyakorlókészlethez.

A betanításhoz két fő paraméterek a következők:
- Több betanítja a modellt, a num_seps argumentum által jelölt lépést. Az egyes lépések betanítja a modellt az egy-egy köteg mérete minibatch.
- Tanulás meg vannak határozva, amely is létrehozhatnak initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard segítségével megjelenítheti a betanítási folyamat állapotát. TensorBoard eseményeket a modellobjektumot train_dir attribútum által megadott mappában találhatók. TensorBoard megtekintéséhez kövesse az alábbi lépéseket:
1. Másolja a nyomtatást, amely "tensorboard--logdir" egy parancssorba kezdődik, és futtassa azt. 
2. Másolja a visszaadott URL-cím a parancssorból egy böngészőbe a TensorBoard megtekintéséhez. 

A TensorBoard a következő képernyőképhez hasonlóan kell kinéznie. Kell feltöltenie a betanítási mappa néhány percet vesz igénybe. Ezért ha TensorBoard nem jelenik meg a be megfelelően az első alkalommal próbálkozzon ismétlődő lépést 1 – 2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>A modell értékelése

A "kiértékelése" metódus a modell kiértékelésére használatos. Ez a függvény bemeneteként ObjectDetectionDataset objektum szükséges. Az értékelés adatkészlet használatával ugyanezt a funkciót, amelyet használ a betanítási adatkészletet is létrehozható. A támogatott metrika az átlagos pontosság meghatározottak szerint a [PASCAL VOC Challenge](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Az értékelési eredmények tiszta formátumú nyomtatható.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Hasonlóképpen képes számítási a gyakorlókészlethez a modell pontosságát. Ezzel lehetővé teszi a képzés jó megoldás konvergált. A sikeres képzési gyakran van közel 100 %-os után gyakorlókészletbe pontosságát.

Kiértékelés eredményeinek TensorBoard térkép értékek és a képek az előre jelzett határoló mezők is megtekinthetők. A nyomtatás másolása a következő kódot egy parancssori ablakot a TensorBoard ügyfél elindításához. Itt 8008 portérték használatos az alapértelmezett érték a 6006, amely használta a szolgáltatást, a képzési állapotának megtekintése való ütközés elkerülése érdekében.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Kép pontszám

Ha elégedett a betanított modell teljesítményét, a modellobjektumot "pontszám" függvény pontszámot rendelni az új képek használható. A visszaadott eredmények megjelenítése"függvény a jeleníthetők meg. 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![PNG](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>A modell mentése

A betanított modell lemezre menti, és betölti a memóriába vissza, ahogy az alábbi példakódok.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>A mentett a modell betöltése a kiértékelés

A mentett modell használatához töltse be a modell memóriát, valamint a "Betöltés" függvény. Csak egyszer betölteni kell. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

A modell betöltése után használat pontszámot rendelni egy képet vagy képek egy listája. Egy rendszerkép egy szótárban kulcsokkal, például a 'detection_boxes', "detection_scores" és "num_detections" adja vissza. Ha a bemenet-lemezképek listája szótár listáját adja vissza, a megfelelő egy kép egy szótárt. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

A fenti 0,5 pontszámok észlelt objektumok, például címkék, pontszámokat és koordináták nyomtatható.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

A pontszámok ugyanúgy, mint megjelenítése előtt.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![PNG](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

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
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>A webszolgáltatás használata

A webszolgáltatás létrehozása után, az üzembe helyezett webszolgáltatás rendszerképek is pontozása. Több lehetősége van:

   - Közvetlenül pontszámot rendelni a webszolgáltatás a központi telepítési objektum: deploy_obj.score_image(image_path_or_url) 
   - Vagy használhatja a végpont URL-címe és a szolgáltatáskulcs (nincs helyi telepítéshez): AMLDeployment.score_existing_service_with_image (image_path_or_url, service_endpoint_url, service_key = None)
   - A HTTP-kérések közvetlenül a pontszám a webszolgáltatás-végpont (a haladó felhasználók) alkotnak.

### <a name="score-with-existing-deployment-object"></a>A meglévő központi telepítési objektum pontszám
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>A végpont URL-címe és a szolgáltatáskulcs pontszám
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>HTTP-kérést közvetlenül a pontszám-végpont
Alábbiakban található néhány példa kód kialakításához a HTTP-kérést közvetlenül a Python. Ezt a más programozási nyelvek megteheti.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>A webszolgáltatás szerializált eredmény elemzése
Az eredmény a webszolgáltatásból elemezhető json-karakterlánc szerepel.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>További lépések

További információ az Azure Machine Learning-csomagot a Computer Vision ezekben a cikkekben:

+ Olvassa el a [csomag áttekintése](https://aka.ms/aml-packages/vision).

+ Fedezze fel a [referenciadokumentációt](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) a csomag számára.

+ Ismerje meg [egyéb Python-csomagokat az Azure Machine Learning](reference-python-package-overview.md).
