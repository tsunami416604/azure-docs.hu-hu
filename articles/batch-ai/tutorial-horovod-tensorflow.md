---
title: Oktatóanyag – Elosztott betanítás az Azure Batch AI és a Horovod használatával | Microsoft Docs
description: Oktatóanyag – Elosztott modell betanítása a Horovod használatával, az Azure Batch AI szolgáltatás és az Azure CLI segítségével.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: de19b20865127fd37cd7bc1ac854288a95a68091
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058138"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Oktatóanyag: Elosztott modell betanítása a Horovod használatával

Ebben az oktatóanyagban elosztott mélytanulási modellt fog betanítani, párhuzamosan futtatva azt egy Batch AI-fürt több csomópontján. A Batch AI egy felügyelt szolgáltatás gépi tanulási és mesterségesintelligencia-modellek nagy léptékben történő betanítására Azure GPU-fürtökön. 

Az oktatóanyag egy általános Batch AI-munkafolyamatot mutat be, illetve azt, hogyan kezelheti a Batch AI-erőforrásokat az Azure CLI-n keresztül. A tárgyalt témakörök:

> [!div class="checklist"]
> * Batch AI-munkaterület, -kísérlet és -fürt beállítása
> * Be- és kimeneti Azure-fájlmegosztás beállítása
> * Mélytanulási modell párhuzamosítása a Horovod használatával
> * Betanítási feladat elküldése
> * A feladat figyelése
> * A betanítás eredményeinek lekérése

A jelen oktatóanyagban a [Horovod](https://github.com/uber/horovod) segítségével fog módosítani egy objektumészlelési modellt a párhuzamos futtatáshoz. A modell betanítása a [CIFAR-10 adatkészlet](https://www.cs.toronto.edu/~kriz/cifar.html) képeivel történik. A betanítási feladat 24 vCPU-t és 4 GPU-t tartalmazó fürtön fut, végrehajtása körülbelül 60 percet vesz igénybe.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.38-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Miért érdemes a Horovodot használni?

Az oktatóanyagban használt Horovod egy elosztott betanítási keretrendszer Tensorflow, Keras és PyTorch rendszerekhez. A Horovod lehetővé teszi, hogy mindössze néhány kódsor módosításával az egyetlen GPU-hoz készült betanítási szkript elosztott rendszeren is hatékonyan futtatható legyen.

A Horovod mellett a Batch AI számos további népszerű, nyílt forráskódú keretrendszeren is támogatja az elosztott betanítást. Mindenképpen tekintse át a modellek éles környezetben való betanításához használt keretrendszerek licencfeltételeit.

## <a name="prepare-the-batch-ai-environment"></a>A Batch AI-környezet előkészítése

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az `az group create` paranccsal hozzon létre egy `batchai.horovod` nevű erőforráscsoportot az `eastus` régióban. Erre az erőforráscsoportra a Batch AI-erőforrások üzembe helyezéséhez lesz szükség.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Munkaterület létrehozása

Az `az batchai workspace create` paranccsal hozzon létre egy Batch AI-munkaterületet. A munkaterület az egyéb Batch AI-erőforrások legfelső szintű gyűjteménye. Az alábbi parancs egy `batchaidev` nevű munkaterületet hoz létre az erőforráscsoporthoz.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Kísérlet létrehozása

A Batch AI-kísérletek egy vagy több, együttesen futtatandó lekérdezési és kezelési feladatot foglalnak csoportba. Az alábbi `az batchai experiment create` parancs egy `cifar` nevű kísérletet hoz létre a munkaterülethez és az erőforráscsoporthoz.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>GPU-fürt beállítása

A következő lépésben egy GPU-fürtöt fog beállítani a kísérlet futtatásához. A Batch AI rugalmas lehetőségeket biztosít a fürtök adott igények szerint történő testreszabásához.

Az alábbi `az batchai cluster create` parancs egy `nc6cluster` nevű négy csomópontos fürtöt hoz létre a munkaterülethez és az erőforráscsoporthoz. Alapértelmezés szerint a fürt virtuális gépei tárolóalapú alkalmazások üzemeltetéséhez készült Ubuntu Server-rendszerképet futtatnak. Ebben a példában a fürtcsomópontok a `Standard_NC6` mérettartományban találhatók, amely egyetlen NVIDIA Tesla K80 GPU-t tartalmaz.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

A fürt állapotát az `az batchai cluster show` parancs futtatásával tekintheti meg. A fürt teljes kiépítése általában néhány percet vesz igénybe.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

A fürt a létrehozási folyamat elején `resizing` állapotban van. A fürt állapota folyamatosan változik, ahogy végrehajtja az alábbi lépéseket. A fürt akkor áll készen a betanítási feladat futtatására, ha az állapota `steady`, a csomópontok pedig `idle` állapotúak. Például:

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Tároló beállítása

Használja az `az storage account create` parancsot egy tárfiók létrehozásához a betanítási szkript és a betanítási anyag kimenetének tárolásához.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Az `az storage share create` paranccsal hozzon létre egy `myshare` nevű Azure-fájlmegosztást a fiókban:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

A gyakorlatban ez a tároló több feladathoz és kísérlethez is használható. A rendezett környezet biztosításához hozzon létre egy könyvtárat a fájlmegosztáson belül az adott kísérlethez kapcsolódó fájlok tárolásához. Az alábbi `az storage directory create` parancs egy `cifar` nevű könyvtárat hoz létre.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

A következő lépés a tényleges betanítási szkript előkészítése, amelyet ezt követően fel fog tölteni az újonnan létrehozott könyvtárba.

## <a name="create-the-training-script"></a>A betanítási szkript létrehozása

Ebben a kísérletben egy olyan Python-szkriptet fog futtatni, amelyen néhány módosítást kell elvégezni ahhoz, hogy párhuzamosan lehessen futtatni az objektumészlelési modellt a Horovod használatával. Az [eredeti modell](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) Kerast és egy TensorFlow-háttérrendszert használ. 

Egy tetszőleges szövegszerkesztővel hozzon létre egy `cifar_cnn_distributed.py` nevű fájlt a felület egyik munkakönyvtárában az alábbi tartalommal. Az eredeti forráskód módosításait `HOROVOD` előtag jelöli.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Ahogy ebben a példában is látható, a Horovod keretrendszer használatával történő elosztott betanítás engedélyezéséhez csupán néhány módosításra van szükség a modellben. 

Vegye figyelembe, hogy ez a bemutató célú szkript viszonylag kis méretű modellt és adatkészletet használ, ezért itt az elosztott modell alkalmazása nem feltétlenül jár jelentős teljesítménynövekedéssel. Az elosztott betanítás valódi hatékonysága jóval nagyobb modell és adatkészlet esetében érzékelhető igazán.

## <a name="upload-the-training-script"></a>A betanítási szkript feltöltése

A kész szkriptet a korábban létrehozott fájlmegosztási könyvtárba kell feltölteni. Az alábbi `az storage file upload` parancs a helyi munkakönyvtárból a megfelelő helyre tölti fel a szkriptet.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>A betanítási feladat elküldése

Ha végzett az előző lépésekkel, hozzon létre egy betanítási feladatot. A Batch AI szolgáltatásban a feladat futtatási paramétereit egy `job.json` fájlban fogja megadni. Egy tetszőleges szövegszerkesztővel hozzon létre egy `job.json` nevű feladatkonfigurációs fájlt az alábbi tartalommal.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Magyarázatok az egyes tulajdonságokhoz:

| Tulajdonság | Leírás |
| --------- | --------- |
| `nodeCount` | A feladathoz kijelölt csomópontok száma. Ebben az esetben a feladat párhuzamosan `4` csomóponton fog futni. |
| `horovodSettings` | A `pythonScriptFilePath` mezőben a korábban létrehozott `cifar` könyvtárban található Horovod-szkript elérési útja szerepel. A `commandLineArgs` mező a szkript futtatásához szükséges parancssori argumentumokat tartalmazza. Ebben a kísérletben egyetlen argumentumra van szükség, amely megadja, hogy melyik könyvtárba mentse a rendszer a modellt. A `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` a fájlmegosztás csatlakoztatási helyének elérési útja. | 
| `stdOutErrPathPrefix` | A feladat kimeneteinek és naplóinak elérési útja, amely ebben az esetben ugyanaz a `cifar` könyvtár. |
| `jobPreparation` | A környezetet a feladat futtatására előkészítő esetleges speciális utasítások. A szkript használatához telepíteni kell a megadott MPI- és Horovod-csomagokat. |
| `containerSettings` | Annak a tárolónak a beállításai, amelyen a feladat fut. Ez a feladat `tensorflow` használatával létrehozott Docker-tárolót használ.

Ezt a konfigurációt használva hozza létre a feladatot az `az batchai job create` paranccsal. Az alábbi parancs az összes eddig beállított erőforrás használatával várólistára helyez egy `cifar_distributed` nevű új feladatot. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Ha a csomópontok éppen foglaltak, eltelhet némi idő, amíg a feladat futtatása elkezdődik. A feladat aktuális végrehajtási állapotát az `az batchai job show` paranccsal tekintheti meg.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Az elosztott betanítás megjelenítése

Ha már elkezdődött a feladat futtatása, a fürtcsomópontok állapotát az `az batchai cluster show` parancs ismételt futtatásával kérdezheti le. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

A kimenet az alábbihoz hasonló lesz. Ez esetben mind a négy csomópont „fut” állapotban van. Az eredményből az látható, hogy az elosztott betanítás mind a négy csomópontot használja.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>A feladat figyelése

### <a name="list-output-files"></a>Kimeneti fájlok listázása

A feladat futtatása közben az `az batchai job file list` paranccsal listázhatja a feladat által előállított kimeneti fájlokat.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

Ebben a konkrét kísérletben a kimenet az alábbihoz hasonló lesz. A feladat teljes kimenete a `stdout.txt` fájlban lesz naplózva, míg a `stderr.txt` fájl a fő feladat végrehajtása során bekövetkezett esetleges hibákat tartalmazza. A többi fájl az egyes csomópontokhoz tartozó kimeneti, hiba- és feladat-előkészítési naplókat tartalmazza.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Kimeneti fájl streamelése

Egy fájl tartalmát az `az batchai job file stream` paranccsal streamelheti. Az alábbi példában a fő kimeneti napló streamelése látható.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

A feladat végrehajtása közben a parancs a betanítási feladat standard kimenetét streameli, amelynek kimenete az alábbihoz hasonló lesz.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

A betanítási szkript 25 alapidőszakon keresztül fut le, vagy végighalad a betanítási adatkészleten. A folyamat körülbelül 60 percig tart. 

## <a name="retrieve-the-results"></a>Az eredmény lekérése

Ha a szkript futtatása sikeresen befejeződött, az ellenőrzési pontosságnak 70–75% körüli értéknek kell lennie, a betanított modell pedig a fájlmegosztásba lesz mentve a következő helyre: `cifar/saved_models/keras_cifar10_trained_model.h5`. 

A modellek betanítása általában egy nagyobb munkafolyamat része. A betanított modellt például más alkalmazásokban is közzéteheti. A betanított modell helyi letöltéséhez használja az `az storage file download` parancsot. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a feladatok futtatása befejeződött, a számítási költségek csökkentése érdekében ajánlott az összes fürt méretét `0 nodes` értékre csökkenteni, hogy tétlen állapotban ne kelljen fizetnie a használatért. Ehhez az alábbi `az batchai cluster resize` parancsot használhatja. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Később méretezze át őket 1 vagy több csomópontra a feladatok futtatásához. 

Amennyiben sem a munkaterületet, sem a tárfiókot nem szeretné használni a jövőben, törölje az erőforráscsoportot az `az group delete` paranccsal. Az erőforráscsoport törlésével a benne található erőforrásokat is törli.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket sajátította el:

> [!div class="checklist"]
> * Batch AI-munkaterület, -kísérlet és -fürt beállítása
> * Be- és kimeneti Azure-fájlmegosztás beállítása
> * Modell párhuzamosítása a Horovod használatával
> * Betanítási feladat elküldése
> * A feladat figyelése
> * A betanítás eredményeinek lekérése

A Batch AI különböző keretrendszerekben történő használatára a GitHubon találhat recepteket.

> [!div class="nextstepaction"]
> [Batch AI-receptek](https://github.com/Azure/BatchAI/tree/master/recipes)
