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
ms.date: 06/22/2020
ms.openlocfilehash: 3edb8da6d535ac7817c29db254aa84efd92321b6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012977"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Mi az az Azure Machine Learning számítási példány?

Az Azure Machine Learning számítási példány egy felügyelt felhőalapú munkaállomás adatszakértők számára.

A számítási példányok megkönnyítik a Azure Machine Learning fejlesztésének megkezdését, valamint a rendszergazdák számára a felügyeleti és a vállalati készültségi képességek biztosítását.  

Számítási példányt használhat a felhőben a gépi tanuláshoz teljes mértékben konfigurált és felügyelt fejlesztési környezetként. Emellett számítási célokként is használhatók a fejlesztési és tesztelési célú képzéshez és következtetésekhez.  

Az üzemi szintű modell képzéséhez használjon [Azure Machine learning számítási fürtöt](how-to-set-up-training-targets.md#amlcompute) több csomópontos skálázási képességekkel. Az üzemi szintű modell üzembe helyezéséhez használja az [Azure Kubernetes Service-fürtöt](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Miért érdemes számítási példányt használni?

A számítási példány egy teljes körűen felügyelt felhőalapú munkaállomás, amely a gépi tanulási fejlesztési környezethez van optimalizálva. A következő előnyöket biztosítja:

|Főbb előnyök|Description|
|----|----|
|Termelékenység|A modelleket integrált jegyzetfüzetekkel és a Azure Machine Learning Studio következő eszközeivel hozhatja létre és helyezheti üzembe:<br/>– Jupyter<br/>- JupyterLab<br/>– RStudio (előzetes verzió)<br/>A számítási példány teljes mértékben integrálva van Azure Machine Learning munkaterülettel és Studióval. A jegyzetfüzeteket és az egyéb adatszakértőket a munkaterületen is megoszthatja. A VS Code-távfelügyeleti szolgáltatást [SSH](how-to-set-up-vs-code-remote.md) -val is beállíthatja |
|Felügyelt & biztonságos|Csökkentse a biztonsági lábnyomot, és adja hozzá a vállalati biztonsági követelményeknek való megfelelést. A számítási példányok robusztus felügyeleti házirendeket és biztonságos hálózati konfigurációkat biztosítanak, például:<br/><br/>– Automatikus kiépítés Resource Manager-sablonokból vagy Azure Machine Learning SDK-ból<br/>- [Szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview)<br/>- [Virtuális hálózatok támogatása](how-to-enable-virtual-network.md#compute-instance)<br/>– SSH-szabályzat az SSH-hozzáférés engedélyezéséhez vagy letiltásához<br/>TLS 1,2 engedélyezve |
|Előre konfigurálva &nbsp; ( &nbsp; ml)|Időt takaríthat meg a telepítési feladatokban előre konfigurált és naprakész ML-csomagokkal, mély tanulási keretrendszerekkel, GPU-illesztővel.|
|Teljes mértékben testreszabható|Az Azure-beli virtuálisgép-típusok széles körű támogatása, beleértve a GPU-ket és a megőrzött alacsony szintű testreszabásokat, például a csomagok és illesztőprogramok telepítésének speciális forgatókönyveit. |

## <a name="tools-and-environments"></a><a name="contents"></a>Eszközök és környezetek

> [!IMPORTANT]
> Az alábbi megjelölésű eszközök (előzetes verzió) jelenleg nyilvános előzetes verzióban érhetők el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

A munkaterület Azure-fájlmegosztás fiókja a számítási példány meghajtójának megfelelően van csatlakoztatva. Ez a meghajtó a Jupyter, a Jupyter Labs és a RStudio alapértelmezett munkakönyvtára. Ez azt jelenti, hogy a Jupyter, JupyterLab vagy RStudio-ben létrehozott jegyzetfüzetek és egyéb fájlok automatikusan tárolódnak a fájlmegosztás során, és más számítási példányokban is használhatók.

A fájlmegosztás fájljai a munkaterületen lévő összes számítási példányból elérhetők. A rendszer a számítási példányon a fájlok minden módosítását megbízhatóan megőrzi a fájlmegosztás számára.

A legújabb Azure Machine Learning mintákat a munkaterület fájlmegosztás felhasználói fájlok könyvtára alatt is klónozott mappába helyezheti.

A kisméretű fájlok írása a hálózati meghajtókon lassabb lehet, mint a számítási példány helyi lemezének írásakor.  Ha sok kis fájlt ír, próbálkozzon közvetlenül a számítási példányon, például a `/tmp` címtárban. Vegye figyelembe, hogy ezek a fájlok nem lesznek elérhetők más számítási példányokból. 

Az `/tmp` ideiglenes adatokhoz használhatja a számítási példány könyvtárát is.  A számítási példány operációsrendszer-lemezén azonban ne írjon nagy mennyiségű adatfájlt.  Használjon [datastores](concept-azure-machine-learning-architecture.md#datasets-and-datastores) helyette adattárolókat. Ha telepítette a JupyterLab git bővítményt, akkor a számítási példányok teljesítményének lassulásához is vezethet.

## <a name="managing-a-compute-instance"></a>Számítási példány kezelése

Azure Machine Learning Studio munkaterületén válassza a **számítás**, majd a felül található **számítási példány** elemet.

![Számítási példány kezelése](./media/concept-compute-instance/manage-compute-instance.png)

A következő műveleteket hajthatja végre:

* [Hozzon létre egy számítási példányt](#create). 
* Frissítse a számítási példányok lapot.
* Számítási példány elindítása, leállítása és újraindítása.  A példányért kell fizetnie, amikor fut. Állítsa le a számítási példányt, ha nem használja azt a Cost csökkentése érdekében. Egy számítási példány leállítása felszabadítja azt. Ezután indítsa el újra, amikor szüksége van rá. 
* Számítási példány törlése.
* A számítási példányok listájának szűrése a létrehozott értékekre.  Ezek az Ön által elérhető számítási példányok.

A munkaterület minden olyan számítási példányához, amelyhez hozzáfér, a következőket teheti:

* Hozzáférés Jupyter, JupyterLab, RStudio a számítási példányon
* Az SSH-t a számítási példányba. Az SSH-hozzáférés alapértelmezés szerint le van tiltva, de a számítási példány létrehozási idején is engedélyezhető. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül történik. A lapon megadhatja az SSH-kapcsolat adatait, például az IP-címet, a felhasználónevet és a portszámot.
* Egy adott számítási példány, például az IP-cím és a régió részletes adatainak beolvasása.

A [RBAC](/azure/role-based-access-control/overview) lehetővé teszi annak szabályozását, hogy a munkaterület mely felhasználói hozhatnak létre, törölhetnek, indíthatnak le, állíthatnak le vagy indíthatnak újra egy számítási példányt. A munkaterület közreműködői és tulajdonosi szerepkörben lévő összes felhasználó létrehozhatja, törölheti, elindíthatja, leállíthatja és újraindíthatja a számítási példányokat a munkaterületen. Azonban csak egy adott számítási példány létrehozója jogosult a Jupyter, a JupyterLab és a RStudio elérésére a számítási példányon. A számítási példány létrehozója rendelkezik a hozzájuk rendelt számítási példánnyal, rendelkezik rendszergazdai hozzáféréssel, és a Jupyter/JupyterLab/RStudio-en keresztül terminált is biztosít. A számítási példány a létrehozó felhasználó egyfelhasználós bejelentkezési felhasználónevével fog rendelkezni, és minden művelet a felhasználó identitását fogja használni a RBAC és a kísérlet futtatásához. Az SSH-hozzáférés a nyilvános/titkos kulcs mechanizmusán keresztül vezérelhető.

Ezeket a műveleteket a RBAC is vezérelheti:
* *Microsoft. MachineLearningServices/munkaterületek/számítások/olvasás*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/írás*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/törlés*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/indítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/leállítás/művelet*
* *Microsoft. MachineLearningServices/munkaterületek/számítások/újraindítás/művelet*

### <a name="create-a-compute-instance"></a><a name="create"></a>Számítási példány létrehozása

A Azure Machine Learning Studio munkaterületén hozzon létre egy új számítási példányt a **számítási** szakaszból vagy a **jegyzetfüzetek** szakaszban, amikor készen áll az egyik jegyzetfüzet futtatására.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Új számítási példány létrehozása":::


|Mező  |Leírás  |
|---------|---------|
|Számítási név     |  <li>A név megadása kötelező, és legfeljebb 3 – 24 karakter hosszúságú lehet.</li><li>Az érvényes karakterek a kis-és nagybetűk, a számjegyek és a karakterek **-** .</li><li>A névnek betűvel kell kezdődnie</li><li>A névnek egyedinek kell lennie az Azure-régióban lévő összes számításban. Ha a választott név nem egyedi, akkor riasztás jelenik meg.</li><li>Ha **-** a karakter használatban van, akkor a névben legalább egy betűt kell követnie.</li>     |
|Virtuális gép típusa |  Válassza a CPU vagy a GPU lehetőséget. Ez a típus nem módosítható a létrehozás után     |
|Virtuális gép mérete     |  A támogatott virtuálisgép-méretek a régióban korlátozottak lehetnek. Tekintse meg a [rendelkezésre állási listát](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|SSH-hozzáférés engedélyezése/letiltása     |   Az SSH-hozzáférés alapértelmezés szerint le van tiltva.  Az SSH-hozzáférés nem lehet. a létrehozás után módosult. Győződjön meg arról, hogy engedélyezi a hozzáférést, ha interaktívan szeretne hibakeresést végezni a [vs Code Remote](how-to-set-up-vs-code-remote.md) használatával   |
|Speciális beállítások     |  Választható. Virtuális hálózat konfigurálása. Válassza ki az **erőforráscsoportot**, a **virtuális hálózatot**és az **alhálózatot** , hogy létrehozza a számítási példányt egy Azure-Virtual Network (vnet) belül. További információ: a vnet vonatkozó [hálózati követelmények](how-to-enable-virtual-network.md#compute-instance) .        |

Létrehozhat egy példányt is
* Közvetlenül az [integrált jegyzetfüzetek felületéről](tutorial-1st-experiment-sdk-setup.md#azure)
* Azure Portal
* Azure Resource Manager sablonból
* [Azure Machine learning SDK](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb) -val
* A [CLI-bővítményből Azure Machine learning](reference-azure-machine-learning-cli.md#computeinstance)

A dedikált magok régiónként/virtuálisgép-család kvótája és a teljes regionális kvóta, amely a számítási példányok létrehozására vonatkozik. egységesített és közös Azure Machine Learning képzés számítási fürt kvótája. A számítási példány leállítása nem mentesíti a kvótát, hogy biztosan újra tudja indítani a számítási példányt.

## <a name="compute-target"></a>Számítási cél

A számítási példányok a Azure Machine Learning számítási képzési fürtökhöz hasonló [képzési számítási célként](concept-compute-target.md#train) használhatók. 

Egy számítási példány:
* Rendelkezik a feladatok várólistával.
* A feladatokat a virtuális hálózati környezetben biztonságosan futtatja anélkül, hogy a vállalatoknak SSH-portot kellene megnyitnia. A feladatot egy tároló környezetben hajtja végre a rendszer, és a modell függőségeit egy Docker-tárolóban csomagolja.
* Több kisebb feladatot is futtathat párhuzamosan (előzetes verzió).  Az alapszintű két feladat párhuzamosan futtatható, míg a többi feladat várólistára kerül.

A számítási példányokat a tesztelési és hibakeresési forgatókönyvekhez helyi előállítási célként is használhatja.

> [!NOTE]
> Az elosztott betanítási feladatok nem támogatottak a számítási példányokon.  Az elosztott képzéshez használja a (számítási fürtök] (útmutató – set-up-Training-Targets. MD # amlcompute).

További részletek: notebook [Train-on-computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Ez a jegyzetfüzet a *Training/Train-on-computeinstance*Studio **Samples** mappában is elérhető.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Mi történt a notebook virtuális géppel?

A számítási példányok a notebook virtuális gépet cserélik le.  

A munkaterület-fájlmegosztás és a munkaterület-adattárakban tárolt adatok egy számítási példányból lesznek elérhetők. Azonban a notebook virtuális gépen korábban telepített egyéni csomagokat újra kell telepíteni a számítási példányon. A számítási fürtök létrehozására vonatkozó kvóta-korlátozások a számítási példányok létrehozására is érvényesek lesznek.

Nem hozhatók létre új virtuális gépek a notebookon. Azonban továbbra is elérheti és használhatja a létrehozott notebook-alapú virtuális gépeket a teljes funkcionalitással. A számítási példányok a meglévő notebook virtuális gépekkel megegyező munkaterületen hozhatók létre.


## <a name="next-steps"></a>További lépések

 * [Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.
