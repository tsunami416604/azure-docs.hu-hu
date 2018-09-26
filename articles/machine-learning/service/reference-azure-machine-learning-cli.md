---
title: Tudnivalók az Azure Machine Learning CLI-bővítmény
description: Ismerje meg a gépi tanulási CLI-bővítmény az Azure Machine Learning alkalmazásával.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 09/24/2018
ms.openlocfilehash: f8dae6de835173181430a98c19c7dd1fb3ebaa9f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47158903"
---
# <a name="what-is-the-azure-machine-learning-cli"></a>Mi az az Azure Machine Learning parancssori?

Az Azure Machine Learning parancssori felületének (CLI) kiterjesztés kínál adatelemző szakembereknek és fejlesztőknek az Azure Machine Learning szolgáltatással. Lehetővé teszi, hogy gyorsan machine learning-munkafolyamatok automatizálása, és elhelyezi azokat éles környezetben, mint például:
+ Futtasson kísérleteket a machine learning-modellek létrehozása

+ Regisztráljon machine learning-modellek a vásárlók általi használatra

+ Csomagolását, üzembe helyezése és a gépi tanulási modellek életciklusának nyomon követése

Ebben a machine learning CLI bővítményeként [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) és a rendszer a Python-alapú épülő <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> az Azure Machine Learning szolgáltatáshoz.

> [!NOTE]
> A CLI jelenleg a korai előzetes verzióban érhető el, és frissülni fog.

## <a name="installing-and-uninstalling"></a>Telepítése és eltávolítása

Ezzel a paranccsal az előzetes PyPi indexe verzióból a CLI telepítése:
```AzureCLI
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-0.1.50-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

A parancssori felület, ezzel a paranccsal távolíthatja el:
```AzureCLI
az extension remove -n azure-cli-ml
```

A CLI használatával is frissítheti a **eltávolítása** és **hozzáadása** a fenti lépéseket.

## <a name="using-the-cli-vs-the-sdk"></a>Az SDK-t vagy a parancssori felület használatával
A parancssori felület jobban illeszkednek az Automation szolgáltatásban egy fejlesztő-operátor személy, vagy egy folyamatos integrációt és teljesítést folyamat részeként. Alkalmi és magas paraméteres feladatok kezelésére lett optimalizálva. 

Példák erre vonatkozóan:
- COMPUTE-kiépítés
- a paraméteres kísérlet beküldése
- modell regisztrálását, a lemezkép létrehozása
- Szolgáltatástelepítés

Az adatszakértők számára ajánlott, hogy az Azure Machine Learning SDK-val.

## <a name="common-machine-learning-cli-commands"></a>Gyakori a machine learning CLI-parancsok
> [!NOTE]
> Minta fájlok sikeres végrehajtásához használhatja az alábbi parancsokkal tekintheti meg [itt.](https://github.com/Azure/MachineLearningNotebooks/tree/cli/cli)

Használjon széles skáláját `az ml` parancsokat használhatja a szolgáltatást minden olyan parancssori környezetben, beleértve a portál Azure cloud shellben.

Íme egy példa a gyakori parancsok:

### <a name="workspace-creation--compute-setup"></a>Munkaterület létrehozása és a számítási beállítása

+ Hozzon létre egy Azure Machine Learning-munkaterület, machine Learning szolgáltatás a legfelső szintű erőforrás.
   ```AzureCLI
   az ml workspace create -n myworkspace -g myresourcegroup
   ```

+ Állítsa be a parancssori felület alapértelmezés szerint ez a munkaterület használatára.
   ```AzureCLI
   az configure --defaults aml_workspace=myworkspace group=myresourcegroup
   ```

+ Hozzon létre egy adatelemző virtuális GÉPET (adatelemző virtuális gép). BatchAI fürtök elosztott képzéshez vagy üzembe helyezés az AKS-fürtök is létrehozhat.
  ```AzureCLI
  az ml computetarget setup dsvm -n mydsvm
  ```

### <a name="experiment-submission"></a>Kísérlet beküldése
+ Csatolhat egy kísérlet elküldésére szolgáló projekt (futtatási konfiguráció). Ez szolgál a Kísérletezési futtatások nyomon követéséhez.
  ```AzureCLI
  az ml project attach --experiment-name myhistory
  ```

+ Az Azure Machine Learning szolgáltatás a számítási cél tetszőleges ellen egy kísérlet elküldésére. Ebben a példában a rendszer a helyi számítási környezetben hajtja végre. Ellenőrizze, hogy a conda-környezet fájlt a python-függőségekhez rögzíti.

  ```AzureCLI
  az ml run submit -c local train.py
  ```

+ Elküldött kísérletek listájának megtekintéséhez.
```AzureCLI
az ml history list
```

### <a name="model-registration-image-ceation--deployment"></a>& Központi telepítési modell regisztrációs, kép ceation

+ Regisztrálja a modellt az Azure Machine Learning.
  ```AzureCLI
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Készítsen olyan rendszerképet, a gépi tanulási modell és függőségeket tartalmaznak. 
  ```AzureCLI
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ A csomagolt modell célnak, beleértve az ACI és az AKS üzembe helyezése.
  ```AzureCLI
  az ml service create aci -n myaciservice --image-id myimage:1
  ```
    
## <a name="full-command-list"></a>A parancs teljes lista
A teljes listát megtalálja parancsok a CLI-bővítmény (és a támogatott paraméterek) futtatásával ```az ml COMMANDNAME -h```. 
