---
title: Az Azure Machine Learning-kísérletezés szolgáltatás konfigurálása |} A Microsoft Docs
description: Ez a cikk utasításokat tartalmazó Azure Machine Learning-kísérletezés szolgáltatás magas szintű áttekintést nyújt a konfigurálásával.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 3c5084e548bbb72fa38aae8b60aa46fb4d462dca
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990348"
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Az Azure Machine Learning-kísérletezés szolgáltatás konfigurálása

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

## <a name="overview"></a>Áttekintés
Az Azure Machine Learning-kísérletezés szolgáltatás a kísérleteket az Azure Machine Learning végrehajtási használatával, és felügyeleti képességek futtatása adatszakértők számára lehetővé teszi. A gyors ismétlésének Agilis kísérletezéssel keretet biztosít. Az Azure Machine Learning Workbench lehetővé teszi a kezdéshez helyi fut a gépen, és egy egyszerű elérési út megadását és méretezési lehetőségek érhetők el más környezetekben, például a távoli adatelemzési virtuális gépek a GPU-n és a Spark futtatása HDInsight-fürtök.

Kísérletezési szolgáltatással hoztam létre, amelyek biztosítják az elkülönített, reprodukálható és következetes fut, a kísérletek épül. Ez segít a számítási célokhoz, végrehajtási környezeteket, kezelése és futtatása a konfigurációk. Az Azure Machine Learning Workbench végrehajtási és futtatási felügyeleti képességek segítségével könnyedén áthelyezheti különböző környezetek között. 

A Workbench-projekt hajthat végre egy Python- vagy PySpark-szkript, helyileg vagy a felhő. 

Futtathatók a parancsprogramok: 

* Python (3.5.2-es) környezetet a helyi számítógépen telepítette a Workbenchben
* Conda Python-környezetet a helyi számítógépen a Docker-tároló belül
* Egy Python-környezetben, amely a saját és a egy távoli Linux gépen kezelése
* Conda Python-környezetet egy Docker-tároló egy távoli Linux gépen belül. Ha például egy [Ubuntu-alapú dsvm-hez az Azure-ban])https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [A Spark for HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) az Azure-ban

>[!IMPORTANT]
>Az Azure Machine Learning-kísérletezés szolgáltatás jelenleg támogatja a Python 3.5.2-es verzióját és a Spark 2.1.11, Python és a Spark futtatókörnyezet-verzió, jelölik. 


### <a name="key-concepts-in-experimentation-service"></a>A Kísérletezési szolgáltatással hoztam létre fő fogalmak
Fontos tudni, hogy az Azure Machine Learning-kísérlet végrehajtása a következő fogalmak. Az ezt követő szakaszokban tárgyaljuk részletesen ezek a fogalmak használatával. 

#### <a name="compute-target"></a>Számítási célt
A _számítási célt_ Megadja, hogy a program, például az asztal, távoli Docker hajthat végre egy virtuális Gépet, vagy a fürt hol. Egy számítási célnak kell lennie a megcímezhető és az Ön által elérhető. Workbench révén a számítási célokhoz létrehozásához és kezeléséhez őket a Workbench alkalmazás és a CLI használatával. 

_az ml computetarget csatolása_ parancsot a parancssori felület lehetővé teszi, hogy hozzon létre egy számítási célnak, amelyek segítségével használhatja a futtatások.

Támogatott számításicsomópont-célok a következők:
* A Workbench által telepített számítógép helyi Python (3.5.2-es) környezetben.
* A számítógép helyi Docker
* Felhasználó által felügyelt, Python-környezetet távoli Linux-Ubuntu virtuális gépeken. Ha például egy [Ubuntu-alapú DSVM az Azure-ban](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Távoli Docker Linux-Ubuntu virtuális gépeken. Ha például egy [Ubuntu-alapú DSVM az Azure-ban](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight Spark-fürt](https://azure.microsoft.com/services/hdinsight/apache-spark/) az Azure-ban

Kísérletezési szolgáltatás jelenleg támogatja a Python 3.5.2-es verzióját és Spark-, Python 2.1.11, és Spark-futtatókörnyezet-verzió, illetve. 

>[!IMPORTANT]
> Windows virtuális gépeken futó Docker vannak **nem** támogatott, mert a távoli számítási célokhoz.

#### <a name="execution-environment"></a>Végrehajtási környezet
A _végrehajtási környezetet_ meghatározása az üzemidőt és a függőségeket, a Workbench a program futtatásához szükséges.

Kezelheti a helyi végrehajtási környezet a kedvenc eszközeit és csomagkezelők használatával, ha futtatja a Workbench alapértelmezett futtatókörnyezetet. 

Conda helyi Docker és a távoli Docker-végrehajtások, valamint a HDInsight-alapú végrehajtás kezelésére szolgál. Ezek a tárolók számítási, a végrehajtási környezet konfigurációjának kezelhető **Conda_dependencies.yml** és **Spark_dependencies.yml fájlok**. A rendszer ezeket a fájlokat a **aml_config** mappát a projekt belül.

**A végrehajtási környezetekben támogatott futtatókörnyezet a következők:**
* Python 3.5.2-es verzióját
* Spark 2.1.11

### <a name="run-configuration"></a>Futtatási konfigurációt
A számítási célt és a végrehajtás környezeten kívül Azure Machine Learning határozza meg, és módosítsa a keretrendszert biztosít *futtatási konfigurációkat*. A kísérlet különböző végrehajtások iteratív kísérletezés részeként különböző konfigurálását teheti szükségessé. Előfordulhat, hogy lehet abszolút különböző paraméter-tartományok, más adatforrások felhasználásával, és Finomhangolás a spark paramétereket. Kísérletezési szolgáltatás keretrendszert biztosít futtatási konfigurációk kezelése.

Futó _az ml computetarget csatolása_ parancs két fájlt hoz létre a **aml_config** mappát a projektbe: ".compute" és a egy ".runconfig" egyezmény a következő: _< your_ computetarget_name > .compute_ és _< your_computetarget_name > .runconfig_. Amikor létrehoz egy számítási célnak .runconfig fájl automatikusan létrejön a felhasználók kényelme érdekében. Hozhat létre és más futtatási konfiguráció való kezelése _az ml runconfigurations_ parancs CLI-ben. Is létrehozhat, és szerkessze azokat a fájlrendszerben.

A Workbench futtatási konfigurációs is lehetővé teszi, hogy a környezeti változók megadása. Környezeti változók megadása, és adja hozzá a következő szakasz a .runconfig fájl kódját használni őket. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Ezeket a környezeti változókat a kód érhetők el. Például ez a kódrészlet phyton jelenít meg a "EXAMPLE_ENV_VAR1" nevű környezeti változó
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**A következő ábrán az első kísérlet futtatása magas szintű folyamata látható.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Kísérlet-végrehajtás forgatókönyvek
Ebben a szakaszban azt mélyedjen végrehajtási forgatókönyveket, és ismerje meg az Azure Machine Learning működésével kapcsolatos kísérleteket, kifejezetten kísérlet helyben fut, egy távoli virtuális gépen, és a egy HDInsight-fürtön. Ez a szakasz egy forgatókönyv indítása hozzon létre egy számítási célnak, a kísérletek végrehajtása.

>[!NOTE]
>Ez a cikk a többi használjuk a fogalmak és a képességek megjelenítése a CLI (parancssori felület) parancsokat. Képességek az itt leírtak szerint a Workbench is használható.

## <a name="launching-the-cli"></a>A parancssori felület elindítása
Egyszerűen indítsa el a parancssori felület projekt megnyitása a Workbench, és ellenőrizheti, hogy **fájl--> parancssor megnyitása**.

![](media/experimentation-service-configuration/opening-cli.png)

Ez a parancs elindítja a szkriptek végrehajtása az aktuális projektmappában-parancsokat adhat meg egy terminálablakot. Ezt a terminálablakot a Python 3.5.2-es verzióját környezetet, amely telepítve van az Workbench van konfigurálva.

>[!NOTE]
> Ha bármelyik végrehajtása _az ml_ parancs Azure hitelesíteni kell a parancsablakból. Parancssori felület használ egy független hitelesítési gyorsítótárat, majd az asztali alkalmazás, és így jelentkezik be a Workbench nem jelenti azt, a hitelesítés a parancssori felület környezetében. Hitelesítést végezni, használja a következő lépéseket. A hitelesítési jogkivonat a helyi gyorsítótárba egy ideig, csak meg kell ismételnie ezeket a lépéseket, ha a jogkivonat lejár. Ha a jogkivonat lejár, vagy ha hitelesítési hibák jelennek meg, hajtsa végre a következő parancsokat:

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Futtatásakor _az ml_ parancsot egy projekt mappában található, győződjön meg arról, hogy a projekt tartozik egy Azure Machine Learning-kísérletezés-fiókot a a _aktuális_ Azure-előfizetést. Ellenkező esetben végrehajtási hibák fordulhatnak.


## <a name="running-scripts-and-experiments"></a>Kísérletek-parancsfájlok futtatásakor
A Workbench, hajtsa végre a Python és PySpark szkripteket a különböző számítási tárolók használatával a _az gépi tanulási kísérlet elküldése_ parancsot. Ez a parancs egy futtatási konfigurációt definíció igényel. 

Workbench egy megfelelő runconfig fájlt hoz létre, amikor létrehoz egy számítási célnak, de további futtatási konfigurációs beállítások használatával is létrehozhat _az ml runconfiguration létrehozása_ parancsot. A futtatási konfigurációs fájlokat manuálisan is szerkesztheti.

Futtatási konfigurációkat jelennek meg, része a kísérlet futtatásával a Workbenchben. 

>[!NOTE]
>További információ a futtatási konfigurációs fájlt a [végrehajtási kísérlet hivatkozás](experimentation-service-configuration-reference.md) szakaszban.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Helyileg futó parancsfájl Runtime Workbench telepítése
Workbench lehetővé teszi a szkriptek futtatása közvetlenül a Workbench-telepített Python 3.5.2-es verzióját futásidejű szemben. Az alapértelmezett futtatókörnyezetet Workbench telepítési időpontban telepítve van, és tartalmazza az Azure Machine Learning könyvtárai és függőségei. Futtatási eredmények és a helyi végrehajtást összetevők továbbra is menteni előzmények szolgáltatás futtatása a felhőben.

Végrehajtás Docker-alapú eltérően ez a konfiguráció van _nem_ Conda kezeli. Manuálisan üzembe helyezése a helyi Workbench Python-környezetében csomagfüggőségek kell.

A következő parancsot a szkript futtatásához helyi a Workbench-telepített Python környezetben hajthat végre. 

```
$az ml experiment submit -c local myscript.py
```

Python-környezetet alapértelmezett elérési útja a következő parancs beírásával a Workbench parancssori felület ablakában találja.
```
$ conda env list
```

>[!NOTE]
>PySpark helyileg futó közvetlenül a helyi Spark szemben környezetek jelenleg **nem** támogatott. Workbench támogatja a helyi Docker futó PySpark szkripteket. Az Azure Machine Learning alap Docker-rendszerkép tartalmaz Spark 2.1.11 előre telepítve van. 

_**Egy Python-szkriptet a helyi végrehajtásra áttekintése:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Egy szkript futtatása helyi Docker-a
Is futtathatja a projektek a Docker-tároló a helyi gépen Kísérletezési szolgáltatáson keresztül. Workbench egy alap Docker-rendszerképet, amely az Azure Machine Learning-kódtárakkal és is biztosít, a Spark 2.1.11 futásidejű megkönnyítése helyi Spark végrehajtások. Docker már a helyi számítógépen futnia kell.

A Python- vagy PySpark-szkript futtatása helyi Docker, hajtsa végre az alábbi parancsokat a parancssori felület.

```
$az ml experiment submit -c docker myscript.py
```
vagy
```
az ml experiment submit --run-configuration docker myscript.py
```

A végrehajtási környezetet a helyi Docker készítenek az Azure Machine Learning alap Docker-rendszerkép használatával. Workbench letölti a lemezképet, futtatásakor az első idő-és hangátfedések, a csomagok conda_dependencies.yml fájlban megadott. Ez a művelet lehetővé teszi a kezdeti futtatási lassabb, de későbbi futtatások lényegesen gyorsabban újbóli felhasználása a gyorsítótárazott rétegek Workbench köszönhetően. 

>[!IMPORTANT]
>Való futtatásához szükséges _az gépi tanulási kísérletet, készítse elő a docker - c_ először készítse elő a Docker-rendszerkép az első futtatáskor a parancsot. Is beállíthat a **PrepareEnvironment** paraméter igaz értékre a docker.runconfig fájlban. Ez a művelet automatikusan előkészíti a környezet a futtatási végrehajtási részeként.  

>[!NOTE]
>Ha egy PySpark-szkriptet futtat a Spark-spark_dependencies.yml conda_dependencies.yml mellett is használható.

A parancsfájlok futtatását a Docker-rendszerkép kínál fel a következő előnyökkel jár:

1. Biztosítja, hogy a parancsfájl megbízhatóan hajtható végre más végrehajtási környezetben. Docker-tárolóban futó segít felderíteni, és minden helyi hivatkozást, amely hatással lehet a hordozhatóság elkerülése érdekében. 

2. Ez lehetővé teszi, hogy gyorsan tesztelje a kódot a modulok és keretrendszereket, amelyek összetett telepítése és konfigurálása, például az Apache Spark-, anélkül, hogy telepítse őket saját maga.


_**Egy Python-szkript végrehajtása helyi Docker áttekintése:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>A parancsfájl futtatásához egy távoli Docker
Bizonyos esetekben a helyi gépen rendelkezésre álló erőforrások nem feltétlenül elegendő a kívánt modell betanításához. Ebben a helyzetben Kísérletezési szolgáltatás lehetővé teszi, hogy könnyedén nagyobb teljesítményű virtuális gépeken a Pythonból vagy a Pysparkból szkriptek futtatása távoli Docker-végrehajtás. 

Távoli virtuális az alábbi követelményeknek kell megfelelnie:
* Távoli virtuális Gépen kell futnia a Linux-Ubuntu és ssh-n keresztül érhető el. 
* Távoli virtuális Gépen futó Docker rendelkeznie kell.

>[!IMPORTANT]
> A Docker egy Windows virtuális gépeken futó **nem** támogatott, mert a távoli számítási célnak


Az alábbi parancs segítségével a számítási célt definíció létrehozása és futtatása távoli Docker-alapú végrehajtás konfigurációját.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Miután konfigurálta a számítási célnak, használhatja a következő parancsot a szkript futtatásához.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Ne feledje, hogy a végrehajtási környezet lett konfigurálva, a leírások segítségével a conda_dependencies.yml. spark_dependencies.yml is használatos, ha a keretrendszer PySpark .runconfig fájlban megadott. 

A Docker konstrukció távoli virtuális gépek pontosan ugyanúgy történik, a folyamat a helyi Docker fut, így olyan hasonló végrehajtási környezetet kell látnia.

>[!TIP]
>Ha inkább a késést az első futtatáskor a Docker-rendszerkép létrehozásával elkerülése érdekében, a következő paranccsal készítse elő a számítási célnak a szkript végrehajtása előtt. az ml kísérlet - c remotedocker előkészítése

_**Egy Python-szkriptet a távoli virtuális gép végrehajtási áttekintése:**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>A szkript futtatása a felhasználó által felügyelt környezetekben célzó távoli virtuális gép
Kísérletezési szolgáltatás is támogatja, a szkript futtatásakor a felhasználó saját Python-környezetet egy távoli Ubuntu virtuális gépen. Ez lehetővé teszi a saját környezetben a végrehajtása kezelését, és továbbra is használja az Azure Machine Learning lehetőségeit. 

Kövesse az alábbi lépéseket a saját környezetében a szkript futtatásához.
* Készítse elő a távoli Ubuntu virtuális gép vagy egy adatelemző virtuális GÉPET, telepíti a függőségeket a Python-környezetében.
* Azure Machine Learning-követelményeket a következő paranccsal telepítse.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>Bizonyos esetekben szüksége lehet, hogy sudo módban a jogosultságok függően az alábbi paranccsal. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* A következő parancs használatával hozza létre a számítási célt definícióját és a virtuális gép távoli végrehajtás a felhasználó által felügyelt futtatások futtatási konfigurációt.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>"UserManagedEnvironment" paraméter a .compute konfigurációs fájlban true értékre állítja.

* Állítsa helyre a Python-futtatókörnyezet végrehajtható a .compute fájlban. A python végrehajtható fájl teljes elérési útja hivatkozzon. 
```
pythonLocation: python3
```

Miután konfigurálta a számítási célnak, használhatja a következő parancsot a szkript futtatásához.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> Ha egy adatelemző virtuális GÉPET futtat, a következő parancsokat kell használnia

Ha szeretné a DSVM globális python-környezetet közvetlenül a következő parancs futtatásával.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>A szkript futtatása egy HDInsight-fürtön
HDInsight az Apache Spark támogató big data-elemzés a népszerű platform. Workbench lehetővé teszi, hogy a HDInsight Spark-fürtök használatával a big data Kísérletezési. 

>[!NOTE]
>A HDInsight-fürtnek az Azure-blobot kell elsődleges tárolóként használnia. Az Azure Data Lake-tároló használata jelenleg nem támogatott.

Hozzon létre egy számítási célnak, és futtassa a következő parancsot egy HDInsight Spark-fürt konfigurációját:

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Ha IP-cím helyett a teljes tartománynév használatára, és a HDI Spark-fürt neve _foo_, az SSH-végpont található a vezérlő csomópont nevű _foo-ssh.azurehdinsight.net_. Ne felejtse el a **-ssh** a kiszolgáló nevét utótaggal vannak ellátva, a teljes Tartománynevét használatakor _--cím_ paraméter.


Miután a számítási környezet, hajtsa végre a PySpark-szkriptet az alábbi parancsot futtathatja.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench előkészít és kezeli a HDInsight-fürtön Conda használatával végrehajtási környezetben. Konfiguráció által kezelt _conda_dependencies.yml_ és _spark_dependencies.yml_ konfigurációs fájlokat. 

A HDInsight-fürthöz az SSH-hozzáférést annak érdekében, hogy a kísérleteket ebben a módban kell. 

>[!NOTE]
>Konfigurációs, HDInsight Spark-fürtön futó Linux (Ubuntu a Python-/ PySpark 3.5.2-es és a Spark 2.1.11).

_**Egy PySpark-szkriptet a HDInsight-alapú végrehajtási áttekintése**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>A szkript futtatása a GPU-alapú
A szkriptek futtatása a GPU-alapú, kövesse az ebben a cikkben szereplő útmutatást:[GPU használata az Azure Machine Learningben](how-to-use-gpu.md)

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>SSH-kulcs alapú hitelesítés használatával létrehozásáról és használatáról a számítási célnak
Az Azure Machine Learning Workbench alkalmazásban hozhat létre és használhat SSH-kulcs alapú hitelesítés mellett a felhasználónév/jelszó-alapú rendszer használata számítási célnak teszi lehetővé. Ezt a funkciót is használhatja, mint a számítási célnak remotedocker vagy -fürt használata esetén. Ez a séma használata esetén a Workbench egy nyilvános/titkos kulcspárt hoz létre, és jelentést küld vissza a nyilvános kulcsot. A felhasználónév hozzáfűzése a nyilvános kulcsot a ~/.ssh/authorized_keys fájlokhoz. Az Azure Machine Learning Workbench majd használ ssh-alapú hitelesítés elérésére, és a számítási célnak végrehajtása. A számítási célnak titkos kulcsát a kulcstároló, a munkaterület menti a rendszer, mivel a munkaterület más felhasználók használhatja a számítási célnak ugyanúgy, mint a számítási célnak létrehozásához a felhasználónév megadásával.  

Kövesse a leírtakat, ez a funkció használatához. 

- Hozzon létre egy számítási célnak, az alábbi parancsok egyikével.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
vagy
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Fűzze hozzá a nyilvános kulcsot a csatolt számítási célnak azon ~/.ssh/authorized_keys fájl a Workbench által generált. 

>[!IMPORTANT]
>Jelentkezzen be a felhasználónevet a számítási tároló létrehozásához használt a számítási célt kell. 

- Most készítse elő, és használja a számítási célnak, SSH-kulcs alapú hitelesítés használatával.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>További lépések
* [Hozzon létre és telepítse az Azure Machine Learning](quickstart-installation.md)
* [Modellkezelés](model-management-overview.md)
