---
title: 'Oktatóanyag: a Machine learning használatának első lépései – Python'
titleSuffix: Azure Machine Learning
description: Ebből az oktatóanyagból megtudhatja, hogyan futtathatja a Pythonhoz készült Azure Machine Learning SDK-t a személyes fejlesztési környezetben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: c0fe3c3808709de732bec8ce0599d380094405e8
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368481"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Oktatóanyag: a Azure Machine Learning első lépései a fejlesztési környezetben (4. rész)

Ebben a *négy részes oktatóanyag-sorozatban*megismerheti a Azure Machine learning alapjait, és az Azure Cloud platformon végezheti el a feladatok alapú Python gépi tanulás feladatait. Ezek a feladatok többek között a következők:

1. Hozzon létre egy munkaterületet és a helyi fejlesztői környezetet a gépi tanuláshoz.
2. A Pythonhoz készült Azure Machine Learning SDK használatával futtasson kódot a felhőben.
3. Kezelheti a modell betanításához használt Python-környezetet.
4. Adatok feltöltése az Azure-ba, és az adatok felhasználása a képzésben.

Az oktatóanyag-sorozat 1. részében a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Telepítse a Azure Machine Learning SDK-t.
> * Állítsa be a kód címtár-struktúráját.
> * Hozzon létre egy Azure Machine Learning munkaterületet.
> * Konfigurálja a helyi fejlesztési környezetet.
> * Hozzon létre egy számítási fürtöt.

>[!NOTE]
> Ez az oktatóanyag-sorozat a Python- *alapú* , nagy számítási igényű és/vagy reprodukálhatóságot igénylő gépi tanulási feladatokhoz igazított Azure Machine learning fogalmakat ismerteti. Ha a gépi tanulási feladatok nem férnek hozzá ehhez a profilhoz, a [Jupyter vagy a RStudio függvényt használja egy Azure Machine learning számítási példányon](tutorial-1st-experiment-sdk-setup.md) , hogy átlépjen a Azure Machine Learningra.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálkozzon [Azure Machine learning](https://aka.ms/AMLFree).
- A Python és a [Machine learning fogalmak](concept-azure-machine-learning-architecture.md)ismerete. Ilyenek például a környezetek, a képzések és a pontozások.
- Helyi fejlesztési környezet: egy olyan laptop, amelyen telepítve van a Python és a kedvenc IDE (például a Visual Studio Code, a Notebookshoz vagy a Jupyter).

## <a name="install-the-azure-machine-learning-sdk"></a>Az Azure Machine Learning SDK telepítése

Az oktatóanyag során a Pythonhoz készült Azure Machine Learning SDK-t használjuk.

A leginkább ismerős eszközök (például a Conda és a pip) használatával beállíthatja az oktatóanyag során használni kívánt környezetet. Telepítse a környezetet a Pythonhoz készült Azure Machine Learning SDK-val a pip használatával:

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>Könyvtár-struktúra létrehozása kód számára
Javasoljuk, hogy az oktatóanyaghoz az alábbi egyszerű címtár-struktúrát állítsa be:

```markdown
tutorial
└──.azureml
```

- `tutorial`: A projekt legfelső szintű könyvtára.
- `.azureml`: Rejtett alkönyvtár a Azure Machine Learning konfigurációs fájljainak tárolásához.

## <a name="create-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület létrehozása

A munkaterület a Azure Machine Learning legfelső szintű erőforrása, és központi hely a következőhöz:

- Kezelheti az erőforrásokat, például a számítást.
- Tárolja az eszközöket, például a jegyzetfüzeteket, a környezeteket, az adatkészleteket, a folyamatokat, a modelleket és a végpontokat.
- Együttműködés a csapattagok más tagjaival.

A legfelső szintű címtárban `tutorial` adjon hozzá egy nevű új Python-fájlt `01-create-workspace.py` a következő kóddal. A paramétereket (név, előfizetés-azonosító, erőforráscsoport és [hely](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) a beállításokkal igazíthatja.

A kódot egy interaktív munkamenetben vagy Python-fájlként is futtathatja.

>[!NOTE]
> Ha helyi fejlesztési környezetet (például laptopot) használ, a rendszer arra kéri, hogy a következő kód első futtatásakor az *eszköz kódjával* hitelesítse a munkaterületet. Kövesse a képernyőn megjelenő utasításokat.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Futtassa ezt a kódot a `tutorial` címtárból:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

Az előző kódrészlet futtatása után a mappa szerkezete a következőképpen fog kinézni:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

A fájl `.azureml/config.json` tartalmazza a Azure Machine learning-munkaterülethez való kapcsolódáshoz szükséges metaadatokat. Ez tartalmazza az előfizetés-AZONOSÍTÓját, az erőforráscsoportot és a munkaterület nevét. 

> [!NOTE]
> A tartalma `config.json` nem titok. Ezeket a részleteket részletesen megoszthatja.
>
> A Azure Machine Learning-munkaterülettel való interakcióhoz továbbra is szükséges a hitelesítés.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning számítási fürt létrehozása

Hozzon létre egy Python-szkriptet a `tutorial` legfelső szintű címtárban `02-create-compute.py` . Töltse fel a következő kóddal, hogy létrehozzon egy Azure Machine Learning számítási fürtöt, amely a nulla és négy csomópont között automatikusan méretezhető:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Futtassa a Python-fájlt:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> A fürt létrehozásakor 0 csomópont lesz kiépítve. A fürt *nem* jár költségekkel, amíg el nem küld egy feladatot. Ez a fürt leskálázást végez, ha 2 400 másodperc (40 perc) üresjáratban van.

A mappa szerkezete mostantól a következőképpen fog megjelenni:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Következő lépések

Ebben a beállítási oktatóanyagban a következőket teheti:

- Létrehozott egy Azure Machine Learning munkaterületet.
- Állítsa be a helyi fejlesztési környezetet.
- Létrehozott egy Azure Machine Learning számítási fürtöt.

A következő oktatóanyagban végigvezeti egy parancsfájlnak a Azure Machine Learning számítási fürtbe való beküldésének lépésein.

> [!div class="nextstepaction"]
> [Oktatóanyag: a "Helló világ!" futtatása Python-szkript az Azure-ban](tutorial-1st-experiment-hello-world.md)
