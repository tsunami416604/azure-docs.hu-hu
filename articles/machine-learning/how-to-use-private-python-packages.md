---
title: Privát Python-csomagok használata
titleSuffix: Azure Machine Learning
description: Biztonságosan férhet hozzá a privát Python-csomagokhoz Azure Machine Learning környezetekről.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 580525b2e8e408949ce1d8f2d1b8241c431fc755
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209554"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Privát Python-csomagok használata Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a privát Python-csomagok Azure Machine Learningon belül. A privát Python-csomagokhoz a következő esetekben használhat példákat:

 * Olyan privát csomagot fejlesztett ki, amelyet nyilvánosan nem szeretne megosztani.
 * A vállalati tűzfalon belül tárolt csomagok válogatott tárházát kívánja használni.

Az ajánlott módszer attól függ, hogy van-e néhány csomag egyetlen Azure Machine Learning-munkaterülethez, vagy a csomagok teljes tárháza a szervezeten belüli összes munkaterülethez.

A privát csomagokat a [környezeti](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) osztály használja. A környezeten belül deklarálhatja, hogy mely Python-csomagokat szeretné használni, beleértve a magánjellegűket is. Az általános Azure Machine Learning környezetének megismeréséhez tekintse meg a [környezetek használatát](how-to-use-environments.md)ismertető témakört. 

## <a name="prerequisites"></a>Előfeltételek

 * A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Azure Machine learning munkaterület](how-to-manage-workspace.md)

### <a name="use-small-number-of-packages-for-development-and-testing"></a>Kis számú csomagot használhat a fejlesztéshez és a teszteléshez

Ha kis mennyiségű privát csomagot használ egy adott munkaterülethez, használja a statikus [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) módszert. Ezzel a módszerrel gyorsan hozzáadhat egy privát csomagot a munkaterülethez, és kiválóan alkalmas fejlesztési és tesztelési célokra.

Mutasson a fájl elérési útja argumentumra egy helyi kerék fájlra, és futtassa a ```add_private_pip_wheel``` parancsot. A parancs egy URL-címet ad vissza, amellyel nyomon követheti a csomag helyét a munkaterületen belül. Rögzítse a tárolási URL-címet, és adja át a `add_pip_package()` metódusnak.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Belsőleg a Azure Machine Learning szolgáltatás a biztonságos SAS URL-cím alapján cseréli le az URL-címet, így a Wheel-fájl privát és biztonságos marad.

### <a name="consume-a-repository-of-packages-from-azure-devops-feed"></a>A csomagok tárházának felhasználása az Azure DevOps-hírcsatornából

Ha aktívan fejleszt Python-csomagokat a Machine learning-alkalmazáshoz, az Azure DevOps-tárházban tárolhatja őket, és hírcsatornáként közzéteheti őket. Ez a módszer lehetővé teszi, hogy integrálja a DevOps munkafolyamatot a csomagok létrehozásához a Azure Machine Learning-munkaterület. A Python-hírcsatornák Azure DevOps való beállításának megismeréséhez olvassa el [a Python-csomagok használatának első lépései az Azure-](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops) összetevőkben című témakört.

Ez a módszer személyes hozzáférési tokent használ a tárházon végzett hitelesítéshez. Ugyanez a módszer a jogkivonat-alapú hitelesítéssel, például a privát GitHub-adattárakkal rendelkező más adattárakra is érvényes. 

 1. [Hozzon létre egy személyes hozzáférési jogkivonatot (Pat)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) az Azure DevOps-példányhoz. Állítsa be a jogkivonat hatókörét __csomagolási > olvasásra__. 

 2. Adja hozzá az Azure DevOps URL-címét és a PAT as munkaterület-tulajdonságokat a [munkaterület. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) metódus használatával.

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Hozzon létre egy Azure Machine Learning-környezetet, és adjon hozzá Python-csomagokat a hírcsatornából.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

A környezet most már készen áll a futtatásra vagy a webszolgáltatás-végpontok üzembe helyezésére. A környezet létrehozásakor Azure Machine Learning szolgáltatás a PAT használatával hitelesíti a hírcsatornát a megfelelő alap URL-címmel.

### <a name="consume-a-repository-of-packages-from-private-storage"></a>Saját tárterületből származó csomagok tárházának felhasználása

Az Azure Storage-fiókból csomagokat használhat a szervezet tűzfalán belül. Ez a Storage-fiók a nagyvállalati használatra szánt csomagok, illetve a nyilvánosan elérhető csomagok belső tükrözése számára is rendelkezhet.

A magánhálózati tároló beállítása:

 1. [Helyezze el a munkaterületet egy virtuális hálózaton (VNET) belül](how-to-enable-virtual-network.md).
 2. Hozzon létre egy Storage-fiókot, és [engedélyezze a nyilvános hozzáférést](https://docs.microsoft.com/azure/storage/common/storage-network-security).
 2. Helyezze a használni kívánt Python-csomagokat a Storage-fiókban lévő tárolóba. 
 3. [A Storage-fiók hozzáférésének engedélyezése a munkaterület VNET](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) 

Ezután hivatkozhat a Azure Machine Learning környezeti definícióban található csomagokra az Azure Blob Storage teljes URL-címével.

## <a name="next-steps"></a>További lépések

 * További információ a [Azure Machine learning vállalati biztonságáról](concept-enterprise-security.md)
