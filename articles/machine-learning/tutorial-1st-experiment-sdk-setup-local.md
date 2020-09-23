---
title: 'Oktatóanyag: Ismerkedés a Machine learning szolgáltatással – Python'
titleSuffix: Azure Machine Learning
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Machine Learning Python SDK-t, amely a személyes fejlesztési környezetben fut.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946568"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Oktatóanyag: Ismerkedés a Azure Machine Learning a fejlesztési környezettel (4. rész)

Ebben a **négy részes oktatóanyag-sorozatban**megismerheti a Azure Machine learning alapjait, és elvégezheti az Azure-Felhőbeli feladatok-alapú Python ml-feladatok végrehajtását, beleértve a következőket:

1. Hozzon létre egy munkaterületet és a helyi gépi tanulás fejlesztői környezetét.
2. Kód futtatása a felhőben Azure Machine Learning Python SDK használatával.
3. Kezelheti a modell betanításához használt Python-környezetet.
4. Adatok feltöltése az Azure-ba, és az adatok felhasználása a képzésben.

Az **oktatóanyag-sorozat 1. részében**a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Az Azure Machine Learning SDK telepítése
> * A kód címtár-struktúrájának beállítása
> * Azure Machine Learning-munkaterület létrehozása
> * A helyi fejlesztési környezet konfigurálása
> * Számítási fürt beállítása

>[!NOTE]
> Ez az oktatóanyag-sorozat a Python- __alapú__ , nagy számítási igényű és/vagy reprodukálhatóságot igénylő gépi tanulási feladatokhoz igazított Azure Machine learning fogalmakat ismerteti. Ha a gépi tanulási feladatok nem férnek hozzá ehhez a profilhoz, használja a [Azure Machine learning számítási példányon a Jupyter vagy az RStudio funkciót](tutorial-1st-experiment-sdk-setup.md) , hogy Azure Machine learning.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki [Azure Machine learning](https://aka.ms/AMLFree) még ma.
- A Python és a [Machine learning fogalmak](concept-azure-machine-learning-architecture.md)ismerete. Ilyenek például a környezetek, a képzések, a pontozás és így tovább.
- Helyi fejlesztési környezet – egy olyan laptop, amelyen telepítve van a Python és a kedvenc IDE (például: VSCode, Notebookshoz, Jupyter stb.).

## <a name="install-the-azure-machine-learning-sdk"></a>Az Azure Machine Learning SDK telepítése

Az oktatóanyag során a Azure Machine Learning Python SDK-t használjuk.

Használhatja a legismertebb eszközöket – például a Conda, a pip-et és így tovább – a jelen oktatóanyagban használt környezet beállításához. Telepítse a környezetet a Azure Machine Learning Python SDK-t a pip használatával:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>A kód címtár-struktúrájának létrehozása
Javasoljuk, hogy az oktatóanyaghoz az alábbi egyszerű könyvtárat állítsa be:

```markdown
tutorial
└──.azureml
```

- **oktatóanyag** (a projekt legfelső szintű könyvtára)
- **. azureml** (az oktatóanyag rejtett alkönyvtára): a `.azureml` könyvtár Azure Machine learning konfigurációs fájlok tárolására szolgál.

## <a name="create-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterület létrehozása

A munkaterület a Azure Machine Learning legfelső szintű erőforrása, és központi hely a következőhöz:

- Erőforrások, például a számítások kezelése
- Tárolók, például jegyzetfüzetek, környezetek, adatkészletek, folyamatok, modellek, végpontok stb.
- Együttműködés a csapattagok más tagjaival

A felső szülő címtárban – `tutorial` adjon hozzá egy nevű új Python-fájlt `01-create-workspace.py` az alábbi kóddal. A paramétereket (név, előfizetés-azonosító, erőforráscsoport és [hely](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) a beállításokkal) igazíthatja.

A kódot egy interaktív munkamenetben vagy Python-fájlként is futtathatja.

>[!NOTE]
> Helyi fejlesztési környezet (pl. laptop) használatakor a rendszer arra kéri, hogy hitelesítse a munkaterületet egy *eszköz kódjával* az alábbi kód első futtatásakor. Kövesse a képernyőn megjelenő utasításokat.

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

A fenti kódrészlet futtatása után a mappa szerkezete a következőképpen fog kinézni:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

A fájl `.azureml/config.json` tartalmazza a Azure Machine learning-munkaterülethez való kapcsolódáshoz szükséges metaadatokat, azaz az előfizetés-azonosítót, az erőforráscsoportot és a munkaterület nevét. 

> [!NOTE]
> A tartalma `config.json` nem titok – tökéletesen megoszthatja ezeket az adatokat.
> A Azure Machine Learning-munkaterülettel való interakcióhoz továbbra is szükséges a hitelesítés.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning számítási fürt létrehozása

Hozzon létre egy Python-szkriptet a `tutorial` legfelső szintű címtárban, `02-create-compute.py` és töltse fel a következő kóddal egy olyan Azure Machine learning számítási fürt létrehozásához, amely a nulla és négy csomópont között automatikusan méretezhető:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
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
> A fürt létrehozása után 0 csomópont van kiépítve. Ezért a fürt nem jár költségekkel, amíg el **nem** küld egy feladatot. Ez a fürt leskálázást végez, ha 2400 másodperc (40 perc) üresjáratban van.

A mappa szerkezete mostantól a következőképpen fog megjelenni:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Következő lépések

Ebben a beállítási oktatóanyagban a következőket látja el:

- Azure Machine Learning munkaterület létrehozva
- A helyi fejlesztési környezet beállítása
- Létrehozott egy Azure Machine Learning számítási fürtöt.

A következő oktatóanyagban végigvezeti egy parancsfájlnak a Azure Machine Learning számítási fürtbe való beküldésének lépésein.

> [!div class="nextstepaction"]
> [Oktatóanyag: ""Helló világ!"alkalmazás" Python-szkript futtatása az Azure-ban](tutorial-1st-experiment-hello-world.md)
