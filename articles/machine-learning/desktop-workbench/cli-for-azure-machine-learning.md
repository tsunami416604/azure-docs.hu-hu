---
title: Telepítheti és használhatja a parancssori felület a legfontosabb feladatok - Azure Machine Learning
description: Ismerje meg, hogyan kell telepíteni, és a parancssori felület használata a leggyakrabban használt gépi tanulási feladatok az Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 4b6ed5c70201b918a5bfa252719b2673303b38e9
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830860"
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Telepíti, és gépi tanulási CLI Azure Machine Learning felső feladataihoz

Az Azure Machine Learning-szolgáltatások rendszer integrált, végpontok közötti adattudomány és speciális elemzési megoldások. Professional adatszakértőkön Azure Machine Learning szolgáltatás segítségével készítse elő az adatokat, kísérletek fejlesztéséhez és a felhőbeli skálázással modellek telepítése. 

Az Azure Machine Learning egy parancssori felület (CLI) biztosít, amely képes:
+ A munkaterület és a projektek kezelése
+ Számítási célok beállítása
+ Futtassa a képzés kísérletek
+ Előzmények megtekintése és a múltbeli kísérletekhez metrikák
+ Modellek éles üzembe helyezés webszolgáltatásként
+ Gyártás és szolgáltatások kezelése

Ez a cikk bemutatja a leghasznosabb CLI parancsainak az Ön kényelme. 

![Azure Machine Learning parancssori felület](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez

Azure-előfizetés vagy egy olyan erőforráscsoport, ahol központilag telepíthető a modellek közreműködői hozzáférés szükséges. Emellett telepítendő Azure Machine Learning-munkaterület a CLI futtatásához. 

>[!IMPORTANT]
>A CLI Azure Machine Learning szolgáltatás fel eltér a [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), amely Azure-erőforrások kezelésére szolgál.

## <a name="get-and-start-cli"></a>GET, és indítsa el a parancssori felület

Ahhoz, hogy a parancssori felület, telepítse a letölthető innen Azure Machine Learning munkaterület:
    + Windows – https://aka.ms/azureml-wb-msi 
    + MacOS- https://aka.ms/azureml-wb-dmg 

A parancssori felület indítása:
+ Az Azure Machine Learning-munkaterület, indítsa el a menüből CLI **fájl -> Nyissa meg a parancssort.**

## <a name="get-command-help"></a>A parancs kapcsolatos súgó megjelenítése 

További információ a parancssori felület parancsai használatával kaphat `--debug` vagy `--help` többek között a parancsok után `az ml <xyz> --debug` ahol `<xyz>` parancs neve. Példa:
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Az Azure Machine Learning közös CLI feladatok 

További tudnivalók a leggyakoribb feladatokat hajthat végre egy ebben a szakaszban a CLI többek között:
+ [Számítási célok beállítása](#target)
+ [Távoli végrehajtás feladatok elküldése](#jobs)
+ [Jupyter notebookok használata](#jupyter)
+ [Futtatási alábbi előzményeinek való interakció](#history)
+ [Azok a környezet konfigurálása](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Állítson be egy számítási cél

A gépi tanulási a különböző célok, beleértve a modell számíthatja ki:
+ helyi módban
+ a virtuális gép (DSVM) adatok tudományos
+ a HDInsight-fürtök

Adatok tudományos VM target csatolása:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Egy HDInsight célhoz csatolása:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

Belül a **aml_config** mappa, módosíthatja a conda függőségek. 

Emellett működtetheti PySpark, Python vagy a GPU DSVM a Python. 

A Python működési mód meghatározása:
+ Python, vegye fel az `Framework:Python` a `<target name>.runconfig` 

+ PySpark, vegye fel az `Framework:PySpark` a `<target name>.runconfig` 

+ A GPU DSVM a Python-hez
    1. Adja hozzá `Framework:Python` a `<target name>.runconfig` 

    1. Továbbá adja hozzá `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` a `<target name>.compute`

A számítási cél előkészítése:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Az előfizetés megjelenítése:<br/>
>`az account show`<br/>
>
>Az előfizetés beállításához:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Távoli feladatok elküldéséhez

A távoli célnak feladat elküldése:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Jupyter notebookok használata

Jupyter notebook indítása:
```azurecli
az ml notebook start
```

Ez a parancs Jupyter notebook localhost indítja el. A kernel Python 3 kiválasztásával helyi dolgozik, vagy a távoli virtuális Gépet a kernel kiválasztásával használhatók `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Kommunikál, és vizsgálja meg a futtatási előzményei

A futtatási előzményei listázásához:
```azurecli
az ml history list -o table
```

A listában az összes befejezett fut:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Található legpontosabb futtatja:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

Minden egyes futtatásához által előállított fájlokat is letöltheti. 

A mappa kimenetek mentett modell elősegítése:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

A modell letöltése:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Konfigurálja a környezetet a azok

Állítsa be a operationalization környezetet, kell létrehoznia:

> [!div class="checklist"]
> * Egy erőforráscsoportot 
> * a storage-fiók
> * Egy Azure-tárolót beállításjegyzék (ACR)
> * Egy alkalmazás insight fiók
> * Egy Azure tároló szolgáltatás (ACS) fürt Kubernetes központi telepítés


Egy Docker-tároló a tesztelés helyi telepítésének beállítása:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Az ACS-fürthöz az Kubernetes beállítása:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

A telepítés állapotának figyelésére:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

A környezetben használandó beállítása:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>További lépések

Első lépések egy, a következő cikkeket: 
+ [Telepítse és indítsa el az Azure Machine Learning segítségével](../service/quickstart-installation.md)
+ [Sorolásához Iris adatok oktatóanyag: 1. rész](tutorial-classifying-iris-part-1.md)

Mélyebben elmerülne a rendszer egy, a következő cikkeket:
+ [Parancssori felület parancsait modellek kezeléséhez](model-management-cli-reference.md)
