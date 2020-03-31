---
title: Mi az Azure Machine Learning számítási példány?
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learning számítási példányát, egy teljes körűen felügyelt felhőalapú munkaállomást.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283926"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Mi az Azure Machine Learning számítási példány?

Az Azure Machine Learning számítási példány (előzetes verzió) egy teljes körűen felügyelt felhőalapú munkaállomás adatszakértők számára. 

A számítási példányok megkönnyítik az Azure Machine Learning fejlesztésének megkezdését, valamint felügyeleti és vállalati készenléti képességeket biztosítanak a rendszergazdák számára.  

Számítási példány használata a felhőben teljesen konfigurált és felügyelt fejlesztői környezetként.

Számítási példányok általában fejlesztési környezetben használják.  Számítási célként is használhatók a képzéshez és a fejlesztési és tesztelési következtetésekhez.  Nagy feladatok esetén egy többcsomópontos skálázási képességekkel rendelkező [Azure Machine Learning-számítási fürt](how-to-set-up-training-targets.md#amlcompute) jobb számítási célválasztás.


## <a name="why-use-a-compute-instance"></a>Miért érdemes számítási példányt használni?

A számítási példány egy teljesen felügyelt felhőalapú munkaállomás, amely a gépi tanulási fejlesztési környezetre van optimalizálva. A következő előnyöket biztosítja:

|Főbb előnyök||
|----|----|
|Termelékenység|Az adatszakértők integrált jegyzetfüzetek és a következő eszközök segítségével hozhatnak létre és helyezhetnek üzembe modelleket a webböngészőben:<br/>- Jupyter között.<br/>- JupyterLab<br/>- RStudio|
|Felügyelt & biztonságos|Csökkentse a biztonsági lábnyomát, és adjon a vállalati biztonsági követelményeknek való megfelelést. A számítási példányok robusztus felügyeleti házirendeket és biztonságos hálózati konfigurációkat biztosítanak, például:<br/><br/>- Automatikus kiépítés az Erőforrás-kezelő sablonokból vagy az Azure Machine Learning SDK-ból<br/>- [Szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)<br/>- [Virtuális hálózat támogatása](how-to-enable-virtual-network.md#compute-instance)<br/>- SSH házirend az SSH-hozzáférés engedélyezéséhez/letiltásához|
|Előre&nbsp;konfigurált vagy&nbsp;ML|Az előre konfigurált és naprakész gépelt gépelt gépi tanulási csomagokkal, mélytanulási keretrendszerekkel és GPU-illesztőprogramokkal időt takaríthat meg a beállítási feladatokban.|
|Teljesen testreszabható|Az Azure virtuálisgép-típusok, köztük a GPU-k széles körű támogatása és a megőrzött alacsony szintű testreszabás, például a csomagok és az illesztőprogramok telepítése, a speciális forgatókönyveket gyerekjátéksá teszi. |

## <a name="tools-and-environments"></a><a name="contents"></a>Eszközök és környezetek

Az Azure Machine Learning számítási példány lehetővé teszi, hogy a munkaterületen teljesen integrált jegyzetfüzet-élményben képes modelleket gyártani, betanítani és üzembe helyezni.


Ezek az eszközök és környezetek a számítási példányra vannak telepítve: 

|Általános eszközök & környezetben|Részletek|
|----|:----:|
|Illesztőprogramok|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-könyvtár||
|Azure CLI ||
|Azure Machine Learning-minták ||
|Azure Machine Learning EDAT motor ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf között|| 

|**R** eszközök & környezetben|Részletek|
|----|:----:|
|RStudio Server nyílt forráskódú kiadás||
|R kernel||
|Azure Machine Learning SDK r-hez|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-minták|

|**PYTHON-eszközök** & környezetek|Részletek|
|----|----|
|Anaconda Python||
|Jupyter és kiterjesztések||
|Jupyterlab és kiterjesztések||
|Visual Studio Code ||
[Pythonhoz készült Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>innen: PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Egyéb PyPI csomagok|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda csomagok|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep learning csomagok|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX csomagok|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Az Azure Machine Learning Python & R SDK-minták||

A Python-csomagok mind telepítve vannak a **Python 3.6 – AzureML** környezetben.  

Számítási példányok általában fejlesztési környezetben használják.  Számítási célként is használhatók a képzéshez és a fejlesztési és tesztelési következtetésekhez.  Nagy feladatok esetén egy többcsomópontos skálázási képességekkel rendelkező [Azure Machine Learning-számítási fürt](how-to-set-up-training-targets.md#amlcompute) jobb számítási célválasztás.

### <a name="installing-packages"></a>Csomagok telepítése

A csomagokat közvetlenül a Jupyter-jegyzetfüzetbe vagy az Rstudio-ba telepítheti:

* RStudio Használja a **Csomagok** lapot a jobb alsó sarokban, vagy a **Konzol** lapot a bal felső sarokban.  
* Python: Telepítési kód hozzáadása és végrehajtása egy Jupyter notebook cellában.

Vagy a terminálablakot az alábbi módokon érheti el:

* RStudio: Válassza a **Terminál** fület a bal felső sarokban.
* Jupyter Lab: Válassza ki a **terminál** csempét az **Indítógomb Másik** fejléce alatt.
* Jupyter: Válassza az **Új>terminál lehetőséget** a Fájlok lap jobb felső részén.
* SSH-t a géphez.  Ezután telepítse a Python-csomagokat a **Python 3.6 – AzureML** környezetbe.  Telepítse az R csomagokat az **R** környezetbe.

## <a name="accessing-files"></a>Fájlok elérése

A jegyzetfüzetek és az R-parancsfájlok a munkaterület alapértelmezett tárfiókjában tárolódnak az Azure-fájlmegosztásban.  Ezek a fájlok a "Felhasználói fájlok" könyvtárban találhatók. Ez a tároló megkönnyíti a jegyzetfüzetek megosztását a számítási példányok között. A tárfiók is tartja a notebookbiztonságosan megőrzött, ha leállít vagy töröl egy számítási példány.

A munkaterület Azure-fájlmegosztási fiókja meghajtóként van csatlakoztatva a számítási példányon. Ez a meghajtó a Jupyter, a Jupyter Labs és az RStudio alapértelmezett munkakönyvtára.

A fájlmegosztásban lévő fájlok az ugyanazon a munkaterületen lévő összes számítási példányból elérhetők. A számítási példányon lévő fájlok módosításai megbízhatóan megmaradnak a fájlmegosztáshoz.

A legújabb Azure Machine Learning-mintákat is klónozhatja a mappába a munkaterületi fájlmegosztás felhasználói fájlok könyvtára alatt.

Kis fájlok írása lassabb lehet a hálózati meghajtókon, mint a virtuális gép maga írása.  Ha sok kis fájlt ír, próbáljon meg egy könyvtárat közvetlenül a számítási példányon, például egy `/tmp` könyvtárat használni. Kérjük, vegye figyelembe, hogy ezek a fájlok nem érhetők el a munkaterület más számítási példányaiból.

## <a name="managing-a-compute-instance"></a>Számítási példány kezelése

Az Azure Machine Learning-stúdióban a munkaterületen válassza **a Számítási**lehetőséget, majd a tetején válassza a **Számítási példány** lehetőséget.

![Számítási példány kezelése](./media/concept-compute-instance/manage-compute-instance.png)

A következő műveleteket hajthatja végre:

* Új számítási példány létrehozása. Adja meg a nevet, az Azure VM-típus gpu-kat is beleértve (kérjük, vegye figyelembe, hogy a virtuális gép típusa nem módosítható a létrehozás után), engedélyezni/letiltani az SSH-hozzáférést, és szükség esetén konfigurálni a virtuális hálózati beállításokat. Közvetlenül az integrált jegyzetfüzetekből, az Azure Portalról, a Resource Manager-sablonból vagy az Azure Machine Learning SDK-ból is létrehozhat példányt. A számítási példányok létrehozására vonatkozó dedikált magok régiónkénti kvótája egységes és megosztott az Azure Machine Learning számítási fürtkvótával.
* A számítási példányok lap frissítése
* Számítási példány indítása, leállítása és újraindítása
* Számítási példány törlése

A munkaterület minden számítási példányához a következőket teheti:

* Hozzáférés Jupyter, JupyterLab, RStudio a számítási példány
* SSH számítási példányba. Az SSH-hozzáférés alapértelmezés szerint le van tiltva, de a számítási példány létrehozási időpontjában engedélyezhető. Az SSH-hozzáférés nyilvános/titkos kulcsmechanizmuson keresztül történik. A lap az SSH-kapcsolat részleteit tartalmazza, például az IP-címet, a felhasználónevet és a portszámot.
* Egy adott számítási példány, például az IP-cím és a régió részleteinek beszerezése.

[Az RBAC](/azure/role-based-access-control/overview) lehetővé teszi annak szabályozását, hogy a munkaterületen lévő felhasználók mely felhasználók hozhatnak létre, törölhetnek, indíthatnak, állíthatnak le, indíthatnak újra egy számítási példányt. A munkaterület közreműködői és tulajdonosi szerepkörének minden felhasználója létrehozhat, törölhet, indíthat, leállíthat és újraindíthat számítási példányokat a munkaterületen. Azonban csak egy adott számítási példány létrehozója férhet hozzá a Jupyter, a JupyterLab és az RStudio számára az adott számítási példányon. A számítási példány létrehozója rendelkezik a nekik dedikált számítási példány, root hozzáféréssel rendelkezik, és a Jupyter-en keresztül is bejuthat. Számítási példány lesz egyfelhasználós bejelentkezést alkotó felhasználó és az összes művelet fogja használni, hogy a felhasználó identitását RBAC és hozzárendelése kísérlet fut. Az SSH-hozzáférés nyilvános/titkos kulcsmechanizmussal történik.

Létrehozhat egy példányt is
* Közvetlenül az integrált notebookok élményéből
* Az Azure Portalon
* Az Azure Resource Manager sablonból
* Az Azure Machine Learning SDK-val

A dedikált magok régiónkénti kvótát, amely a számítási példány létrehozása egységes és megosztott az Azure Machine Learning képzési fürt kvótával. 

## <a name="compute-target"></a>Számítási cél

Számítási példányok az Azure Machine Learning számítási képzési fürtjeihez hasonló [betanítási számítási célként](concept-compute-target.md#train) is használhatók. Több GPU-s virtuális gép kiépítése elosztott betanítási feladatok futtatásához a TensorFlow/PyTorch becslések használatával. Létrehozhat egy futtatási konfigurációt is, és használhatja a kísérlet futtatásához a számítási példányon. A számítási példány t a helyi következtetésközponti telepítési célként használhatja tesztelési/hibakeresési forgatókönyvekhez.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Mi történt a notebook virtuális géppel?

Számítási példányok helyett a notebook virtuális gép.  

A munkaterületi fájlmegosztásban és a munkaterületi adattárakban tárolt jegyzetfüzetfájlok egy számítási példányból lesznek elérhetők. Azonban a notebook virtuális gépre korábban telepített egyéni csomagokat újra kell telepíteni a számítási példányra. A számítási fürtök létrehozására vonatkozó kvótakorlátozások a számítási példányok létrehozására is vonatkoznak. 

Nem hozhatók létre új jegyzetfüzet-virtuális gépek. Azonban továbbra is elérheti és használhatja a létrehozott notebook virtuális gépeket, teljes funkcionalitással. Számítási példányok hozhatnak létre ugyanabban a munkaterületen, mint a meglévő notebook virtuális gépek. 


## <a name="next-steps"></a>További lépések

 * [Oktatóanyag: Az első gépi tanulási modell betanítása](tutorial-1st-experiment-sdk-train.md) bemutatja, hogyan használhat számítási példányt egy integrált notebookkal.
