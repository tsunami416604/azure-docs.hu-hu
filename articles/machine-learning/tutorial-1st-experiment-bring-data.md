---
title: 'Oktatóanyag: saját adatai használata'
titleSuffix: Azure Machine Learning
description: Az Azure ML első lépések sorozatának 4. része azt mutatja be, hogyan használhatók a saját adatai egy távoli betanítási futtatásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: tracking-python
ms.openlocfilehash: 876ba76655572979a1d831a1ca07e5f3871a3283
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946574"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Oktatóanyag: saját adatai használata (4. rész)

Ebből az oktatóanyagból megtudhatja, hogyan tölthet fel és használhat saját adatait a gépi tanulási modellek Azure Machine Learningban való betanításához.

Ez az oktatóanyag **egy négy részből álló oktatóanyag-sorozat negyedik** része, amelyben megismerheti a Azure Machine learning és az Azure-ban végzett feladatok-alapú gépi tanulási feladatok alapjait. Ez az oktatóanyag kiépíti az [1. rész: beállítás](tutorial-1st-experiment-sdk-setup-local.md), [2. rész: a ""Helló világ!"alkalmazás"](tutorial-1st-experiment-hello-world.md)és a [3. rész: a modell betanítása](tutorial-1st-experiment-sdk-train.md)című részben leírtakat.

A [3. részben: a modell betanítása](tutorial-1st-experiment-sdk-train.md)az `torchvision.datasets.CIFAR10` PyTorch API beépített metódusának használatával töltődik le. Sok esetben azonban a saját adatait szeretné használni egy távoli képzési folyamatban. Ez a cikk azt a munkafolyamatot mutatja be, amelynek használatával a saját adataival dolgozhat Azure Machine Learningban.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Betanítási parancsfájl konfigurálása helyi címtárban tárolt adatfelhasználáshoz
> * A betanítási parancsfájl helyi tesztelése
> * Adatok feltöltése az Azure-ba
> * Vezérlő parancsfájl létrehozása
> * Ismerje meg az új Azure Machine Learning fogalmakat (paraméterek, adatkészletek, adattárolók átadása)
> * Betanítási szkript elküldése és futtatása
> * A kód kimenetének megtekintése a felhőben

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be a sorozat [3. részét](tutorial-1st-experiment-sdk-train.md) .
* A Python nyelv és a gépi tanulási munkafolyamatok bevezető ismerete.
* Helyi fejlesztési környezet. Ez magában foglalja a következőket: de nem korlátozódik a Visual Studio Code, a Jupyter vagy a Notebookshoz.
* Python (3.5-3.7-es verzió).

## <a name="adjust-the-training-script"></a>A betanítási parancsfájl módosítása
Most már rendelkezik a Azure Machine Learning futó betanítási szkripttel (oktatóanyag/src/Train. Másolás), és nyomon követheti a modell teljesítményét. Parametrize a betanítási szkriptet argumentumok bevezetésével. Az argumentumok használata lehetővé teszi a különböző hyperparmeters egyszerű összehasonlítását.

A betanítási szkript jelenleg úgy van beállítva, hogy a CIFAR10-adatkészletet minden futtatáskor letöltse. Az alábbi Python-kód úgy lett kialakítva, hogy beolvassa a címtárból származó adatok olvasását.

>[!NOTE] 
> A használatával `argparse` parametize a szkriptet.

```python
# tutorial/src/train.py
import os
import argparse
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run

run = Run.get_context()

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument('--data_path', type=str, help='Path to the training data')
    parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
    parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
    args = parser.parse_args()
    
    print("===== DATA =====")
    print("DATA PATH: " + args.data_path)
    print("LIST FILES IN DATA PATH...")
    print(os.listdir(args.data_path))
    print("================")
    
    # prepare DataLoader for CIFAR10 data
    transform = transforms.Compose([transforms.ToTensor(), transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))])
    trainset = torchvision.datasets.CIFAR10(
        root=args.data_path,
        train=True,
        download=False,
        transform=transform,
    )
    trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(
        net.parameters(),
        lr=args.learning_rate,
        momentum=args.momentum,
    )

    # train the network
    for epoch in range(2):

        running_loss = 0.0
        for i, data in enumerate(trainloader, 0):
            # unpack the data
            inputs, labels = data

            # zero the parameter gradients
            optimizer.zero_grad()

            # forward + backward + optimize
            outputs = net(inputs)
            loss = criterion(outputs, labels)
            loss.backward()
            optimizer.step()

            # print statistics
            running_loss += loss.item()
            if i % 2000 == 1999:
                loss = running_loss / 2000
                run.log('loss', loss) # log loss metric to AML
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

### <a name="understanding-the-code-changes"></a>A kód módosításainak megismerése

A-ben használt kód `train.py` kihasználta a `argparse` könyvtárat a, a és a beállításához `data_path` `learning_rate` `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

A `train.py` parancsfájl a felhasználó által definiált paraméterek használatára is alkalmazkodott az Optimizer frissítéséhez:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

## <a name="test-the-script-locally"></a>A parancsfájl helyi tesztelése

A szkript mostantól argumentumként fogadja az _adatelérési utat_ . A-től kezdődően tesztelje helyileg. Adja hozzá az oktatóanyag-címtár struktúrához egy nevű mappát `data` . A címtár struktúrájának a következőhöz hasonlóan kell kinéznie:

```txt
tutorial
└──.azureml
|  └──config.json
|  └──pytorch-env.yml
└──data
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
└──04-run-pytorch.py
```

Ha `train.py` az előző oktatóanyagban nem futtatta helyileg a szolgáltatást, nem lesz a `data/` könyvtára. Ebben az esetben futtassa a `torchvision.datasets.CIFAR10` metódust helyileg a `download=True` `train.py` parancsfájlban.

A módosított betanítási parancsfájl helyi futtatásához hívja a következőt:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Nem kell letöltenie a CIFAR10-adatkészletet az adat helyi elérési útjának átadásával. Emellett a _tanulási sebesség_ és a _lendület_ hiperparaméterek beállítása különböző értékeivel is kísérletezhet, anélkül, hogy a betanítási szkriptben kellene őket feldolgoznia.

## <a name="upload-the-data-to-azure"></a>Adatok feltöltése az Azure-ba

A szkript Azure Machine Learning-ben való futtatásához a betanítási adatait elérhetővé kell tenni az Azure-ban. A Azure Machine Learning munkaterület _alapértelmezett_ **adattárral** rendelkezik – egy Azure Blob Storage-fiókkal, amely a betanítási adatai tárolására használható.

>[!NOTE] 
> A Azure Machine Learning lehetővé teszi az adatai tárolására szolgáló más felhőalapú adattárolók összekapcsolását. További részletekért lásd az [adattár dokumentációját](./concept-data.md).  

Hozzon létre egy új `05-upload-data.py` , a címtárban nevű Python-vezérlő parancsfájlt `tutorial` :

```python
# tutorial/05-upload-data.py
from azureml.core import Workspace
ws = Workspace.from_config()
datastore = ws.get_default_datastore()
datastore.upload(src_dir='./data', target_path='datasets/cifar10', overwrite=True)
```

A `target_path` meghatározza az adattároló azon elérési útját, ahol a CIFAR10-adatbázis fel lesz töltve.

>[!TIP] 
> Míg az adatok feltöltéséhez Azure Machine Learningt használ, a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával ad-hoc fájlokat tölthet fel. Ha ETL-eszközre van szüksége, [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) használatával betöltheti adatait az Azure-ba.

Az adatok feltöltéséhez futtassa a Python-fájlt (Megjegyzés: a feltöltésnek gyorsnak, 60 másodpercnél rövidebbnek kell lennie.)

```bash
python 05-upload-data.py
```
A következő standard kimenetnek kell megjelennie:
```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```


## <a name="create-a-control-script"></a>Vezérlő parancsfájl létrehozása

Ahogy korábban elvégezte, hozzon létre egy új, nevű Python-vezérlő szkriptet `06-run-pytorch-data.py` :

```python
# tutorial/06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
        )
    
    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env',file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to an Azure Machine Learning compute cluster. Click on the link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>A kód módosításainak megismerése

A vezérlő parancsfájl hasonló a [sorozat 3. részéből](tutorial-1st-experiment-sdk-train.md) a következő új sorokkal:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Az [adatkészletek](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) az Azure Blob-tárolóba feltöltött adatokat használják. Az adatkészletek olyan absztrakt rétegek, amelyek a megbízhatóság és a megbízhatóság javítása érdekében lettek kialakítva.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      A [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) úgy módosul, hogy tartalmazza a számára átadott argumentumok listáját `train.py` . Az `dataset.as_named_input('input').as_mount()` argumentum azt jelenti, hogy a megadott könyvtár _csatlakoztatva_ lesz a számítási célhoz.
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Futtatás beküldése Azure Machine Learningre

Most küldje el újra a futtatást az új konfiguráció használatára:

```bash
python 06-run-pytorch-data.py
```

Ekkor a rendszer kinyomtatja a kísérlethez tartozó URL-címet Azure Machine Learning Studio. Ha erre a hivatkozásra kattint, megtekintheti a kód futását.

### <a name="inspect-the-70_driver_log-log-file"></a>A 70_driver_log naplófájl vizsgálata

A Azure Machine Learning Studióban navigáljon a kísérlet futtatásához (ehhez kattintson a fenti cella URL-címére), majd a **kimenetek + naplók**elemre. Kattintson a 70_driver_log.txt fájlra – a következő kimenetnek kell megjelennie:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Megjegyzčs

1. Azure Machine Learning automatikusan csatlakoztatta a BLOB-tárolót a számítási fürthöz.
2. A ``dataset.as_named_input('input').as_mount()`` vezérlő parancsfájlban használt megoldás a csatlakoztatási pontra lesz feloldva

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban láttuk, hogyan tölthetők fel adatok az Azure-ba a használatával `Datastore` . Az adattár Felhőbeli tárolóként szolgál a munkaterülethez, így megőrizheti az adatok megőrzésének állandó és rugalmas helyét.

Megismerte, hogyan módosíthatja a betanítási szkriptet az adatelérési út parancssoron keresztüli elfogadásához. A használatával `Dataset` csatlakoztathat egy könyvtárat a távoli futtatáshoz. 

Most, hogy már rendelkezik egy modellel, ismerkedjen meg a következőket:

* [Modellek üzembe helyezése Azure Machine learning](how-to-deploy-and-where.md)
