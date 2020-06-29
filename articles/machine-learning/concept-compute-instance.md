---
title: Mi az az Azure Machine Learning számítási példány?
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning számítási példányával, amely egy teljes körűen felügyelt felhőalapú munkaállomás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 8713e7538ae65fc8e750d5b3244482a5503b0e37
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483261"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Mi az az Azure Machine Learning számítási példány?

A Azure Machine Learning számítási példány (előzetes verzió) egy teljes körűen felügyelt felhőalapú munkaállomás az adatszakértők számára. 

A számítási példányok megkönnyítik a Azure Machine Learning fejlesztésének megkezdését, valamint a rendszergazdák számára a felügyeleti és a vállalati készültségi képességek biztosítását.  

Számítási példányt használhat a felhőben teljes mértékben konfigurált és felügyelt fejlesztési környezetként.

A számítási példányokat általában fejlesztési környezetként használják.  A fejlesztéshez és a teszteléshez is használható számítási célként.  A nagyméretű feladatokhoz a többcsomópontos skálázási képességekkel rendelkező [Azure Machine learning számítási fürt](how-to-set-up-training-targets.md#amlcompute) jobb számítási célként szolgál.


## <a name="why-use-a-compute-instance"></a>Miért érdemes számítási példányt használni?

A számítási példány egy teljes körűen felügyelt felhőalapú munkaállomás, amely a gépi tanulási fejlesztési környezethez van optimalizálva. A következő előnyöket biztosítja:

|Főbb előnyök||
|----|----|
|Termelékenység|Az adatszakértők a modelleket integrált jegyzetfüzetekkel és a böngészőben a következő eszközökkel hozhatják létre és helyezhetik üzembe:<br/>– Jupyter<br/>- JupyterLab<br/>– RStudio (előzetes verzió)|
|Felügyelt & biztonságos|Csökkentse a biztonsági lábnyomot, és adja hozzá a vállalati biztonsági követelményeknek való megfelelést. A számítási példányok robusztus felügyeleti házirendeket és biztonságos hálózati konfigurációkat biztosítanak, például:<br/><br/>– Automatikus kiépítés Resource Manager-sablonokból vagy Azure Machine Learning SDK-ból<br/>- [Szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)<br/>- [Virtuális hálózatok támogatása](how-to-enable-virtual-network.md#compute-instance)<br/>– SSH-szabályzat az SSH-hozzáférés engedélyezéséhez vagy letiltásához|
|Előre konfigurált &nbsp; vagy &nbsp; ml|Időt takaríthat meg a telepítési feladatokban előre konfigurált és naprakész ML-csomagokkal, mély tanulási keretrendszerekkel, GPU-illesztővel.|
|Teljes mértékben testreszabható|Az Azure-beli virtuálisgép-típusok széles körű támogatása, beleértve a GPU-ket és a megőrzött alacsony szintű testreszabásokat, például a csomagok és illesztőprogramok telepítésének speciális forgatókönyveit. |

## <a name="tools-and-environments"></a><a name="contents"></a>Eszközök és környezetek

A Azure Machine Learning számítási példány lehetővé teszi, hogy a munkaterületen teljes mértékben integrált jegyzetfüzet-felülettel készítse el a modelleket, betanítsa és üzembe helyezzen.


Ezek az eszközök és környezetek a számítási példányra vannak telepítve: 

|Általános eszközök & környezetekben|Részletek|
|----|:----:|
|Illesztőprogramok|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-könyvtár||
|Azure CLI ||
|Azure Machine Learning minták ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protopuf|| 

|**R** -eszközök & környezetek|Részletek|
|----|:----:|
|RStudio Server nyílt forráskódú kiadás (előzetes verzió)||
|R kernel||
|Az R-hez készült Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK-minták|

|**PYTHON** -eszközök & környezetekben|Részletek|
|----|----|
|Anaconda Python||
|Jupyter és-bővítmények||
|Jupyterlab és-bővítmények||
[Pythonhoz készült Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>PyPI|A legtöbb azureml extra csomagot tartalmazza.  A teljes lista megtekintéséhez nyisson meg [egy terminál ablakot a számítási példányon](how-to-run-jupyter-notebooks.md#terminal) , és futtassa a következőt: <br/> `conda list -n azureml_py36 azureml*` |
|Egyéb PyPI-csomagok|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-csomagok|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep learning-csomagok|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-csomagok|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK-minták||

A Python-csomagok mind a **python 3,6-AzureML** környezetben települnek.  

A számítási példányokat általában fejlesztési környezetként használják.  A fejlesztéshez és a teszteléshez is használható számítási célként.  A nagyméretű feladatokhoz a többcsomópontos skálázási képességekkel rendelkező [Azure Machine learning számítási fürt](how-to-set-up-training-targets.md#amlcompute) jobb számítási célként szolgál.

### <a name="installing-packages"></a>Csomagok telepítése

A csomagokat közvetlenül Jupyter jegyzetfüzetbe vagy Rstudio is telepítheti:

* A RStudio a jobb alsó sarokban található **csomagok** fület vagy a bal felső sarokban található **konzol** fület használják.  
* Python: telepítési kód hozzáadása és végrehajtás Jupyter jegyzetfüzet-cellában.

Vagy a következő módokon érheti el a terminál-ablakokat:

* RStudio: válassza a bal felső sarokban található **terminál** fület.
* Jupyter labor: válassza ki a **terminál** csempét az indító lap **másik** címsorában.
* Jupyter: a fájlok lapon válassza a jobb felső sarokban található **új>terminál** elemet.
* SSH-t a gépre.  Ezután telepítse a Python-csomagokat a **python 3,6-AzureML-** környezetbe.  Telepítse az R-csomagokat az **r** -környezetbe.

## <a name="accessing-files"></a>Fájlok elérése

A jegyzetfüzeteket és R-parancsfájlokat az Azure-fájlmegosztás munkaterületének alapértelmezett Storage-fiókjában tárolja a rendszer.  Ezek a fájlok a "felhasználói fájlok" könyvtár alatt találhatók. Ez a tároló megkönnyíti a jegyzetfüzetek megosztását a számítási példányok között. A Storage-fiók azt is megőrzi, hogy a jegyzetfüzetek biztonságosan megmaradnak a számítási példány leállításakor vagy törlésekor.

A munkaterület Azure-fájlmegosztás fiókja a számítási példány meghajtójának megfelelően van csatlakoztatva. Ez a meghajtó a Jupyter, a Jupyter Labs és a RStudio alapértelmezett munkakönyvtára.

A fájlmegosztás fájljai a munkaterületen lévő összes számítási példányból elérhetők. A rendszer a számítási példányon a fájlok minden módosítását megbízhatóan megőrzi a fájlmegosztás számára.

A legújabb Azure Machine Learning mintákat a munkaterület fájlmegosztás felhasználói fájlok könyvtára alatt is klónozott mappába helyezheti.

A kisméretű fájlok írása lassabb lehet a hálózati meghajtókon, mint a virtuális gépre való írás.  Ha sok kis fájlt ír, próbálkozzon közvetlenül a számítási példányon, például a `/tmp` címtárban. Vegye figyelembe, hogy ezek a fájlok nem lesznek elérhetők a munkaterület más számítási példányaiból.

## <a name="managing-a-compute-instance"></a>Számítási példány kezelése

Azure Machine Learning Studio munkaterületén válassza a **számítás**, majd a felül található **számítási példány** elemet.

![Számítási példány kezelése](./media/concept-compute-instance/manage-compute-instance.png)

A következő műveleteket hajthatja végre:

* Új számítási példány létrehozása. Adja meg a nevet, az Azure-beli virtuális gép típusát, beleértve a GPU-ket (vegye figyelembe, hogy a virtuális gép típusa a létrehozás után nem módosítható), az SSH-hozzáférés engedélyezése/letiltása és a virtuális hálózati beállítások konfigurálása. A példányokat közvetlenül is létrehozhatja az integrált jegyzetfüzetek, Azure Portal, Resource Manager-sablonok vagy Azure Machine Learning SDK használatával. A számítási példányok létrehozásához szükséges dedikált magok régiónként a rendszer egységesíti és megosztotta Azure Machine Learning számítási fürt kvótáját.
* A számítási példányok lap frissítése
* Számítási példány elindítása, leállítása és újraindítása. Állítsa le a virtuális gépet, ha nem használja azt a Cost csökkentése érdekében. Ezután indítsa el újra, amikor szüksége van rá.
* Számítási példány törlése

A munkaterületen minden számítási példány esetében a következőket teheti:

* Hozzáférés Jupyter, JupyterLab, RStudio a számítási példányon
* Az SSH-t a számítási példányba. Az SSH-hozzáférés alapértelmezés szerint le van tiltva, de a számítási példány létrehozási idején is engedélyezhető. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül történik. A lapon megadhatja az SSH-kapcsolat adatait, például az IP-címet, a felhasználónevet és a portszámot.
* Egy adott számítási példány, például az IP-cím és a régió részletes adatainak beolvasása.

A [RBAC](/azure/role-based-access-control/overview) lehetővé teszi annak szabályozását, hogy a munkaterület mely felhasználói hozhatnak létre, törölhetnek, indíthatnak le, állíthatnak le vagy indíthatnak újra egy számítási példányt. A munkaterület közreműködői és tulajdonosi szerepkörben lévő összes felhasználó létrehozhatja, törölheti, elindíthatja, leállíthatja és újraindíthatja a számítási példányokat a munkaterületen. Azonban csak egy adott számítási példány létrehozója jogosult a Jupyter, a JupyterLab és a RStudio elérésére a számítási példányon. A számítási példány létrehozója rendelkezik a hozzájuk rendelt számítási példánnyal, rendelkezik rendszergazdai hozzáféréssel, és a Jupyter keresztül tud terminált. A számítási példány a létrehozó felhasználó egyfelhasználós bejelentkezési felhasználónevével fog rendelkezni, és minden művelet a felhasználó identitását fogja használni a RBAC és a kísérlet futtatásához. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül vezérelhető.

Létrehozhat egy példányt is
* Közvetlenül az integrált jegyzetfüzetek felületéről
* Azure Portal
* Azure Resource Manager sablonból
* Azure Machine Learning SDK-val

A kiszámított példányok létrehozására vonatkozó dedikált magok régiónként, amely a számítási példányok létrehozására vonatkozik, egységesítve van, és megosztva Azure Machine Learning betanítási fürt kvótáját. 

## <a name="compute-target"></a>Számítási cél

A számítási példányok a Azure Machine Learning számítási képzési fürtökhöz hasonló [képzési számítási célként](concept-compute-target.md#train) használhatók. Több GPU-t használó virtuális gép kiépítése elosztott betanítási feladatok futtatásához a TensorFlow/PyTorch becslések használatával. Létrehozhat egy futtatási konfigurációt is, és használhatja a kísérlet számítási példányon való futtatásához. A számítási példányokat helyi központi telepítési célként használhatja tesztelési és hibakeresési helyzetekben.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Mi történt a notebook virtuális géppel?

A számítási példányok a notebook virtuális gépet cserélik le.  

A munkaterület-fájlmegosztás és a munkaterület-adattárakban tárolt adatok egy számítási példányból lesznek elérhetők. Azonban a notebook virtuális gépen korábban telepített egyéni csomagokat újra kell telepíteni a számítási példányon. A számítási fürtök létrehozására vonatkozó kvóta-korlátozások a számítási példányok létrehozására is érvényesek lesznek. 

Nem hozhatók létre új virtuális gépek a notebookon. Azonban továbbra is elérheti és használhatja a létrehozott notebook-alapú virtuális gépeket a teljes funkcionalitással. A számítási példányok a meglévő notebook virtuális gépekkel megegyező munkaterületen hozhatók létre. 


## <a name="next-steps"></a>További lépések

 * [Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.
