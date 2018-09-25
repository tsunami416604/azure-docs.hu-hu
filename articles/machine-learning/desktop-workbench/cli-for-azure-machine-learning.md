---
title: Telepítse, és a legfontosabb feladatok – az Azure Machine Learning a parancssori felület használata
description: Ismerje meg, hogyan telepítheti és használhatja a parancssori felület a leggyakrabban használt gépi tanulási feladatok az Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 06e85845d41b240638a5b5b4d75d64fd460a99bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953327"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Telepítése és a gépi tanulási CLI a leggyakoribb feladatokat az Azure Machine Learning használata

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Az Azure Machine Learning szolgáltatásnak egy integrált, teljes körű adatelemzési és fejlett adatelemzési megoldás. A hivatásos adatszakértők Azure Machine Learning szolgáltatás segítségével adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőméretű üzembe helyezése. 

Az Azure Machine Learning a parancssori felület (CLI) amely is biztosít:
+ Munkaterület és projektek felügyelete
+ Állítsa be a számítási célnak
+ Betanítási kísérlet futtatása
+ Előzmények és az elmúlt futtatások metrikáinak megtekintése
+ A modellek éles üzembe webszolgáltatásként üzembe helyezése
+ Üzemi modellek és szolgáltatások kezelése

Ez a cikk bemutatja a leghasznosabb a CLI-parancsok a felhasználók kényelme érdekében. 

![Az Azure Machine Learning parancssori felület](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez

Azure-előfizetéssel, vagy egy erőforráscsoport, ahol a modellek telepíthet közreműködői hozzáférés szükséges. Emellett szüksége annak érdekében, hogy futtassa a CLI az Azure Machine Learning Workbench telepítése. 

>[!IMPORTANT]
>A parancssori felület elérhető az Azure Machine Learning szolgáltatás eltér a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), használt Azure-erőforrások kezeléséhez.

## <a name="get-and-start-cli"></a>GET, és indítsa el a parancssori felület

Ez a CLI beszerzéséhez telepítése Azure Machine Learning Workbench, amely letölthető innen:
    + Windows – https://aka.ms/azureml-wb-msi 
    + MacOS – https://aka.ms/azureml-wb-dmg 

A parancssori felület elindítása:
+ Az Azure Machine Learning Workbenchben, indítsa el a parancssori felület a menüből **File -> parancssor megnyitása.**

## <a name="get-command-help"></a>A parancs segítség kérése 

CLI-parancsok használatával kapcsolatos további információkat szerezhet a `--debug` vagy `--help` a parancsok, mint például után `az ml <xyz> --debug` ahol `<xyz>` parancs neve. Példa:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Általános CLI-feladatok az Azure Machine Learning 

Ismerje meg a leggyakoribb feladatokat hajthat végre az ebben a szakaszban a CLI-vel többek között:
+ [Számítási célnak beállítása](#target)
+ [Távoli végrehajtás céljából feladatok beküldése](#jobs)
+ [A Jupyter notebookok használata](#jupyter)
+ [Futtatási előzményeket használata](#history)
+ [A környezet üzembe helyezés konfigurálása](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Állítsa be egy számítási célnak

A gépi tanulási modell a különböző teljesítménycéljait, beleértve a képes számítási:
+ helyi módban
+ a rendszerű adatelemzési virtuális gép (DSVM)
+ egy HDInsight-fürtön

Csatlakoztassa az adatelemző virtuális gép cél:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Csatolása egy HDInsight-cél:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

Belül a **aml_config** mappában, módosíthatja a conda-függőségeket. 

Is hogy műveleteket lehessen végezni a PySpark, Python vagy egy GPU dsvm-hez a Python. 

A Python üzemmódja definiálása:
+ Python, vegye fel az `Framework:Python` a `<target name>.runconfig` 

+ PySpark, vegye fel az `Framework:PySpark` a `<target name>.runconfig` 

+ A GPU dsvm-hez, a Python
    1. Adjon hozzá `Framework:Python` a `<target name>.runconfig` 

    1. Adja hozzá `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` a `<target name>.compute`

A számítási célnak előkészítése:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Az előfizetés megjelenítéséhez:<br/>
>`az account show`<br/>
>
>Az előfizetés beállítása:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Távoli feladatok elküldése

Egy távoli cél-feladatok elküldése:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>A Jupyter notebookok használata

Jupyter notebook indítása:
```azurecli
az ml notebook start
```

Ez a parancs egy Jupyter notebookot localhost indítja el. A Python 3 kernel kiválasztásával helyi működik, vagy a kernel kiválasztásával a távoli virtuális gépen működik `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Használhatnak, és ismerje meg a futtatási előzmények

A futtatási előzmények megjelenítése:
```azurecli
az ml history list -o table
```

Az összes befejezett futtatások listája:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Található a legpontosabb fut:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Az egyes futtatások által létrehozott fájlokat is letölthető. 

Az a mappa kimenetek mentett modell előléptetése:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

A modell letöltése:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>A környezet üzembe helyezés konfigurálása

Állítsa be az üzembehelyezési környezetet, létre kell hoznia:

> [!div class="checklist"]
> * Erőforráscsoport 
> * Storage-fiók
> * Egy Azure Container Registryt (ACR)
> * Egy Application insight-fiók
> * Egy üzemelő Kubernetes-példányt az Azure Container Service (ACS) fürtön


Egy helyi üzembe helyezés teszteléséhez a Docker-tároló beállításához:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

A Kubernetes ACS-fürt beállítása:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

A központi telepítési állapotának figyelése:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

A használandó környezet beállítása:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>További lépések

Első lépések a egyet az alábbi cikkek: 
+ [Telepítse és indítsa el az Azure Machine Learning segítségével](quickstart-installation.md)
+ [Írisz osztályozása adatok oktatóanyag: 1. rész](tutorial-classifying-iris-part-1.md)

Ásson mélyebbre egyet az alábbi cikkek:
+ [CLI-parancsokat modellek kezelése](model-management-cli-reference.md)
