---
title: 'Oktatóanyag: az első Machine learning-modell betanítása – Python'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning első lépések sorozatának 3. része bemutatja, hogyan lehet betanítani a Machine learning-modellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: a267231dd447b114c69e6ead20c8ab5252f85d0e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896734"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Oktatóanyag: az első gépi tanulási modell betanítása (4. rész)

Ez az oktatóanyag bemutatja, hogyan végezheti el a gépi tanulási modellek betanítását Azure Machine Learning.

Ez az oktatóanyag **egy négy részből álló oktatóanyag-sorozat harmadik része** , amelyben megismerheti a Azure Machine learning és az Azure-ban végzett feladatok-alapú gépi tanulási feladatok alapjait. Ez az oktatóanyag kiépíti az 1. [rész: beállítás](tutorial-1st-experiment-sdk-setup-local.md) és 2. rész: az adatsorozat [""Helló világ!"alkalmazás" futtatása](tutorial-1st-experiment-hello-world.md) során végzett munkát.

Ebben az oktatóanyagban a következő lépést kell elvégeznie egy gépi tanulási modellt bemutató parancsfájl elküldésével. Ez a példa segít megérteni, hogyan könnyíti meg a Azure Machine Learning a helyi hibakeresés és a távoli futtatások közötti konzisztens viselkedést.

Ebben az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Hozzon létre egy betanítási parancsfájlt.
> * Azure Machine Learning-környezet definiálásához használja a Conda.
> * Vezérlő parancsfájl létrehozása
> * Azure Machine Learning osztályok (környezet, Futtatás, metrikák) megismerése.
> * A betanítási szkript elküldése és futtatása.
> * A kód kimenetének megtekintése a felhőben.
> * Azure Machine Learningra vonatkozó naplózási mérőszámok.
> * Megtekintheti a mérőszámokat a felhőben.

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be az [1. részt](tutorial-1st-experiment-sdk-setup-local.md) , ha még nem rendelkezik Azure Machine learning munkaterülettel.
* A Python nyelv és a gépi tanulási munkafolyamatok bevezető ismerete.
* Helyi fejlesztési környezet. Ez magában foglalja a következőket: de nem korlátozódik a Visual Studio Code, a Jupyter vagy a Notebookshoz.
* Python (3.5-3.7-es verzió).

## <a name="create-training-scripts"></a>Képzési parancsfájlok létrehozása

Először meg kell határoznia a neurális hálózati architektúrát egy `model.py` fájlban. Az összes betanítási kód bekerül az `src` alkönyvtárba, beleértve a következőket is: `model.py` .

Az alábbi kód a PyTorch [ebből a bevezető példából](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) származik. Vegye figyelembe, hogy a Azure Machine Learning fogalmak minden gépi tanulási kódra érvényesek, nem csak a PyTorch.

```python
# tutorial/src/model.py
import torch.nn as nn
import torch.nn.functional as F


class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x
```

Ezután adja meg a betanítási parancsfájlt. Ez a szkript letölti a CIFAR10 adatkészletet a PyTorch `torchvision.dataset` API-k használatával, beállítja a hálózatban definiált hálózatot, és két időpontra, a `model.py` standard SGD és a több entrópia elleni adatvesztést használva.

Hozzon létre egy `train.py` parancsfájlt az `src` alkönyvtárban:

```python
# tutorial/src/train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(
    root="./data",
    train=True,
    download=True,
    transform=torchvision.transforms.ToTensor(),
)
trainloader = torch.utils.data.DataLoader(
    trainset, batch_size=4, shuffle=True, num_workers=2
)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

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
                print(f"epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}")
                running_loss = 0.0

    print("Finished Training")

```

Most már az alábbi címtár-struktúrával rendelkezik:

```txt
tutorial
└──.azureml
|  └──config.json
└──src
|  └──hello.py
|  └──model.py
|  └──train.py
└──01-create-workspace.py
└──02-create-compute.py
└──03-run-hello.py
```

## <a name="define-a-python-environment"></a>Python-környezet definiálása

Demonstrációs célokra a Conda-környezetet fogjuk használni (a pip virtuális környezet lépései majdnem azonosak).

Hozzon létre egy nevű fájlt `pytorch-env.yml` a `.azureml` rejtett könyvtárban:

```yml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
```

Ez a környezet minden, a modellhez és a képzési parancsfájlhoz szükséges függőséget tartalmaz. Figyelje meg, hogy nincs függőség a Azure Machine Learning Python SDK-val.

## <a name="test-locally"></a>Helyi tesztelés

Ellenőrizze, hogy a parancsfájl helyileg fut-e az alábbi környezettel:

```bash
conda env create -f .azureml/pytorch-env.yml    # create conda environment
conda activate pytorch-env             # activate conda environment
python src/train.py                    # train model
```

A parancsfájl futtatása után a rendszer egy nevű könyvtárba fogja látni a letöltött információt `tutorial/data` .

## <a name="create-the-control-script"></a>A vezérlő parancsfájl létrehozása

Az alábbi vezérlő parancsfájl és a ""Helló világ!"alkalmazás" beküldésére használt különbség az, hogy a környezet beállításához több további sort is felvesz.

Hozzon létre egy új Python-fájlt a (z) `tutorial` nevű könyvtárban `04-run-pytorch.py` :

```python
# tutorial/04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='src', script='train.py', compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(name='pytorch-env', file_path='.azureml/pytorch-env.yml')
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>A kód módosításainak megismerése

:::row:::
   :::column span="":::
      `env = Environment.from_conda_specification( ... )`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning egy olyan [környezet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) koncepcióját mutatja be, amely egy reprodukálható, verziószámmal rendelkező Python-környezetet jelöl a kísérletek futtatásához. Könnyen létrehozhat környezetet helyi Conda vagy pip-környezetből.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Hozzáadja a környezetet a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true).
   :::column-end:::
:::row-end:::

## <a name="submit-run-to-azure-machine-learning"></a>Futtatás beküldése Azure Machine Learningre

Ha helyi környezeteket váltott be, győződjön meg arról, hogy a Azure Machine Learning Python SDK-val telepített és futtatott környezetre vált vissza:

```bash
python 04-run-pytorch.py
```

>[!NOTE] 
> Amikor először futtatja ezt a parancsfájlt, Azure Machine Learning egy új Docker-rendszerképet hoz létre a PyTorch-környezetből. A teljes Futtatás akár 5-10 percet is igénybe vehet. A Docker-Build naplóit a Azure Machine Learning Studioban tekintheti meg: kövesse a Machine learning Studióra mutató hivatkozást > válassza a "kimenetek + naplók" fület > válassza ki `20_image_build_log.txt` .
Ezt a rendszerképet újra felhasználjuk a későbbi futtatásokban, így sokkal gyorsabban futnak.

A rendszerkép létrehozása után válassza ki a `70_driver_log.txt` betanítási szkript kimenetét.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Ha hibaüzenet jelenik meg, az azt `Your total snapshot size exceeds the limit` jelzi, hogy a könyvtár a alkalmazásban `data` használt helyen található `source_directory` `ScriptRunConfig` .
> Ügyeljen arra, hogy `data` a alkalmazáson kívülre lépjen `src` .

A környezetek regisztrálhatók egy munkaterületre a használatával `env.register(ws)` , így könnyen megoszthatók, újra felhasználhatók és verziószámozást végeznek. A környezetek megkönnyítik a korábbi eredmények újbóli előállítását és a csapattal való együttműködést.

Azure Machine Learning is karbantartja a kurátori környezetek gyűjteményét. Ezek a környezetek a gyakori gépi tanulási forgatókönyvekre vonatkoznak, és a gyorsítótárazott Docker-rendszerképekből állnak. A gyorsítótárazott Docker-rendszerképek teszik lehetővé az első távoli futtatást.

Röviden, a regisztrált környezetek használatával időt takaríthat meg! További részletek a [környezetek dokumentációjában](./how-to-use-environments.md) találhatók.

## <a name="log-training-metrics"></a>A betanítási mérőszámok naplózása

Most, hogy már rendelkezik a modellekkel kapcsolatos képzéssel Azure Machine Learningban, kezdje nyomon követni a teljesítmény mérőszámait.
Az aktuális betanítási szkript mérőszámokat nyomtat a terminálra. Azure Machine Learning a metrikák naplózására szolgáló mechanizmust biztosít a további funkciókkal. Néhány sornyi kód hozzáadásával lehetősége nyílik a mérőszámok megjelenítésére a Studióban, illetve a metrikák összehasonlítására több Futtatás között.

### <a name="modify-trainpy-to-include-logging"></a>Módosítás `train.py` a naplózás belefoglalásához

Módosítsa a `train.py` parancsfájlt úgy, hogy további két sornyi kódot tartalmazzon:

```python
# train.py
import torch
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms

from model import Net
from azureml.core import Run


# ADDITIONAL CODE: get Azure Machine Learning run from the current context
run = Run.get_context()

# download CIFAR 10 data
trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=torchvision.transforms.ToTensor())
trainloader = torch.utils.data.DataLoader(trainset, batch_size=4, shuffle=True, num_workers=2)

if __name__ == "__main__":

    # define convolutional network
    net = Net()

    # set up pytorch loss /  optimizer
    criterion = torch.nn.CrossEntropyLoss()
    optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)

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
                run.log('loss', loss) # ADDITIONAL CODE: log loss metric to Azure Machine Learning
                print(f'epoch={epoch + 1}, batch={i + 1:5}: loss {loss:.2f}')
                running_loss = 0.0

    print('Finished Training')
```

#### <a name="understand-the-additional-two-lines-of-code"></a>A kód további két sorának megismerése

A-ben a `train.py` metódus használatával érheti el _within_ a Run objektumot a betanítási parancsfájlból, és a következő módon `Run.get_context()` használhatja a metrikák naplózására:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

A Azure Machine Learning metrikái a következők:

- A kísérlet és a Futtatás alapján rendezi, így könnyen nyomon követheti és összehasonlíthatja a metrikákat.
- Egy felhasználói felülettel rendelkezik, így megjelenítheti a képzések teljesítményét a Studióban.
- Úgy tervezték, hogy méretezhető legyen, így Ön is megőrizheti ezeket az előnyöket, még akkor is, ha több száz kísérletet futtat.

### <a name="update-the-conda-environment-file"></a>A Conda-környezet fájljának frissítése

A `train.py` szkript csak új függőséget vett igénybe `azureml.core` . A `pytorch-env.yml` módosítást tükröző frissítés:

```yaml
# tutorial/.azureml/pytorch-env.yml
name: pytorch-env
channels:
    - defaults
    - pytorch
dependencies:
    - python=3.6.2
    - pytorch
    - torchvision
    - pip
    - pip:
        - azureml-sdk
```

### <a name="submit-run-to-azure-machine-learning"></a>Futtatás beküldése Azure Machine Learningre
A szkript elküldése még egyszer:

```bash
python 04-run-pytorch.py
```

Ez alkalommal, amikor felkeresi a stúdiót, lépjen a "metrikák" lapra, ahol mostantól a modell betanítása lehetőségre kattintva megtekintheti az élő frissítéseket.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="Betanítási adatvesztési gráf a metrikák lapon":::

## <a name="next-steps"></a>Következő lépések

Ebben a munkamenetben egy alapszintű "Helló világból" frissítünk. parancsfájlt egy reálisabb betanítási parancsfájlhoz, amely egy adott Python-környezet futtatásához szükséges. Megismerte, hogyan végezheti el a helyi Conda-környezetet a felhőben Azure Machine Learning környezettel. Végül azt is láttuk, hogy a kódok néhány sorában hogyan naplózhatja a mérőszámokat Azure Machine Learningba.

Más módokon is létrehozhatók Azure Machine Learning környezetek, például [egy pip-requirements.txt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-pip-requirements-name--file-path-)vagy akár [egy meglévő helyi Conda-környezetből](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#from-existing-conda-environment-name--conda-environment-name-)is.

A következő munkamenetben láthatja, hogyan dolgozhat a Azure Machine Learningban lévő adatokat, ha feltölti a CIFAR10-adatkészletet az Azure-ba.

> [!div class="nextstepaction"]
> [Oktatóanyag: saját adatbevitel](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Ha itt szeretné befejezni az oktatóanyag-sorozatot, és nem halad a következő lépéssel, ne feledje, hogy [kiüríti az erőforrásokat](tutorial-1st-experiment-bring-data.md#clean-up-resources)