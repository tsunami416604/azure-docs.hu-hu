---
title: "Az Azure Machine Learning kísérletezhet szolgáltatás konfigurálása |} Microsoft Docs"
description: "Ez a cikk magas szintű áttekintést nyújt az Azure Machine Learning kísérletezhet szolgáltatás utasításokat tartalmaz a konfigurálásának."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 5635ef890537a2f37d9d6e9066d0258fc0cb346e
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Az Azure Machine Learning kísérletezhet szolgáltatás konfigurálása

## <a name="overview"></a>Áttekintés
Az Azure Machine Learning kísérletezhet szolgáltatás lehetővé teszi, hogy az adatszakértőkön át a használatával az Azure Machine Learning végrehajtási kísérletek hajtható végre, és futtassa a felügyeleti képességek. A gyors ismétlési gyors kísérletezés keretet biztosít. Az Azure Machine Learning-munkaterület lehetővé teszi kezdődnie helyi fut a gépen, és egy egyszerű útvonalat biztosít be és ki más környezetekben, például a távoli adatok tudományos rendelkező virtuális gépek GPU vagy futtatása Spark on HDInsight-fürtök méretezhetők.

Kísérletezhet szolgáltatás biztosítása elkülönített, megismételhető és egységes futtatása a kísérletek lett tervezve. Segít a számítási célkitűzések, a végrehajtási környezet kezelése és konfigurációk futtatása. Az Azure Machine Learning-munkaterület végrehajtási és futtatási felügyeleti képességek segítségével egyszerűen áthelyezheti különböző környezetek között. 

Helyileg vagy a felhőben léptékű hajthat végre egy Python vagy PySpark parancsfájl munkaterület projektben. 

Futtathatja a parancsfájlokat: 

* Python (3.5.2) környezetben munkaterület telepítve a helyi számítógépen.
* Egy Docker-tároló a helyi számítógépen belül Conda Python-környezet
* Egy Docker-tároló egy távoli számítógépen Linux belül Conda Python-környezetben. Például egy [DSVM Ubuntu-alapú Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [A Spark on HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) az Azure-on

>[!IMPORTANT]
>Az Azure Machine Learning kísérletezhet szolgáltatás jelenleg Python 3.5.2 és Spark 2.1.11 verzióként Python és Spark runtime, illetve. 


### <a name="key-concepts-in-experimentation-service"></a>Alapfogalmak kísérletezhet szolgáltatás
Fontos tudni, hogy az Azure Machine Learning kísérlet végrehajtása a következő fogalmakat. A következő szakaszokban arról lesz szó ezekről a fogalmakról használata részletesen. 

#### <a name="compute-target"></a>Számítási cél
A _cél számítási_ Megadja, hogy a program, például az asztal, távoli Docker végrehajtásának egy virtuális Gépet, vagy egy fürtön. Számítási céljának kell lennie a megcímezhető és elérhető-e meg. Munkaterület lehetővé teszi a számítási célok létrehozását és kezelését a munkaterületet üzemeltető alkalmazás és a parancssori felület használatával. 

_Csatlakoztassa az ml computetarget_ parancsot a CLI teszi lehetővé, melyekkel a futtatása a számítási cél létrehozásához.

Támogatott számítási célok a következők:
* Helyi Python (3.5.2) környezetben munkaterület telepítve a számítógépen.
* A számítógép helyi Docker
* Távoli Docker Linux-Ubuntu virtuális gépeken. Például egy [DSVM Ubuntu-alapú Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight Spark-fürt](https://azure.microsoft.com/services/hdinsight/apache-spark/) az Azure-on

Kísérletezhet szolgáltatás jelenleg támogatja a Python 3.5.2 és 2.1.11 Python, Spark, és futásidejű verzióit, illetve Spark. 

>[!IMPORTANT]
> Windows virtuális gépeken futó Docker vannak **nem** távoli számítási célként támogatott.

#### <a name="execution-environment"></a>Végrehajtási környezet
A _végrehajtási környezet_ határozza meg a futási idő konfigurációs és a függőségek munkaterület a program futtatásához szükséges.

Kezelheti a helyi végrehajtási környezet a kedvenc eszközök és a csomag kezelők használata, ha a munkaterületet üzemeltető alapértelmezett futásidejű futtatja. 

Conda Docker helyi és távoli Docker-végrehajtások, valamint a HDInsight-alapú végrehajtások kezelésére szolgál. Ezek a számítási célok, az a végrehajtási környezet konfigurációjának kezeli **Conda_dependencies.yml** és **Spark_dependencies.yml fájlok**. A rendszer ezeket a fájlokat a **aml_config** mappa a projekthez.

**A végrehajtási környezetekben támogatott futtatókörnyezetek a következők:**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Futtassa a konfiguráció
A számítási cél és a végrehajtási környezet, valamint Azure Machine Learning keretrendszerében definiálására, és módosítsa *konfigurációk futtatása*. A kísérlet különböző végrehajtások iteratív kísérletezhet részeként különböző konfigurálásra lehet szükség. Akkor lehet, hogy lehet abszolút eltérő tartományok, különböző forrásokból használatával, és spark paraméterek beállítása. Kísérletezhet szolgáltatás keretrendszert biztosít futtatási konfigurációk kezelése.

Futó _az ml computetarget csatolása_ a két fájlt hoz létre a **aml_config** a projekt mappájára: egy .compute és a következő az egyezmény .runconfig: _< your_ computetarget_name > .compute_ és _< your_computetarget_name > .runconfig_. A .runconfig fájl automatikusan megtörténik az Ön kényelme számítási cél létrehozásához. Létrehozhat és egyéb futtatási konfiguráció használata _az ml runconfigurations_ CLI parancsot. Is létrehozhat és szerkessze azokat a fájlrendszerben.

Futtatási konfigurációs munkaterület lehetővé teszi a környezeti változók megadását is. Környezeti változók megadását, és adja hozzá az alábbi szakasz a .runconfig fájlban a kódban használhatja őket. 

```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Ezek a környezeti változók a kód érhetők el. Például a phyton kódrészletet kiírja a "EXAMPLE_ENV_VAR1" nevű környezeti változó
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**Az alábbi ábrán a kezdeti kísérlet futtatása magas szintű folyamata.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Kísérlet végrehajtási forgatókönyvek
Ebben a részben azt mélyedjen el végrehajtási forgatókönyvek és további Azure Machine Learning működésével kapcsolatos kísérleteket, kifejezetten a kísérlet helyben fut, a távoli virtuális gép, és a HDInsight-fürtök. Ez a szakasz a forgatókönyv indítása hozzon létre egy számítási cél-és a kísérletek végrehajtása.

>[!NOTE]
>Ez a cikk a többi használjuk a fogalmakat és a képességek a CLI (parancssori felület) parancsokat. Az itt ismertetett képességek a munkaterületet üzemeltető is használható.

## <a name="launching-the-cli"></a>A parancssori felület megnyitása
Indítsa el a parancssori felület segítségével egyszerűen projekt megnyitása a munkaterületet, és lépjen az **fájl--> Nyissa meg a parancssort**.

![](media/experimentation-service-configuration/opening-cli.png)

Ez a parancs elindítja a jelenlegi projekt mappában található parancsfájlok végrehajtandó, parancsokat adhat meg egy terminálablakot. A Python 3.5.2 környezetet, amelybe a munkaterületet üzemeltető van telepítve ez terminálablakot van konfigurálva.

>[!NOTE]
> Ha hajtható végre a _az ml_ parancsot a parancssorablakba Azure hitelesíteni kell. Parancssori felület egy független hitelesítési gyorsítótárat, majd az asztali alkalmazást használja, és így bejelentkezést a munkaterületet üzemeltető nem jelenti azt Ön hitelesítve a CLI-környezetben. Hitelesítést végezni, kövesse az alábbi lépéseket. Hitelesítési jogkivonat a helyi gyorsítótárba helyezi az adott időszakban, csak akkor kell ismételje ezeket a lépéseket, amikor a jogkivonat lejár. Amikor a jogkivonat lejár, vagy ha hitelesítési hibák, hajtsa végre a következő parancsokat:

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
>Amikor futtatja _az ml_ parancsot egy projekt mappában található, győződjön meg arról, hogy a projekt egy Azure Machine Learning kísérletezhet fiókhoz tartozzon a a _aktuális_ Azure-előfizetés. Ellenkező esetben előfordulhat, hogy hibákba végrehajtása.


## <a name="running-scripts-and-experiments"></a>Kísérletek-parancsfájlok futtatásakor
A munkaterület, Ön is végrehajthatja a Python és PySpark parancsfájlok különböző számítási tárolókra a a _az ml kísérlet nyújt_ parancsot. Ehhez a parancshoz egy futtatási konfiguráció definíciója. 

Munkaterület fájlt hoz létre a megfelelő .runconfig számítási cél létrehozásához, de további futtatási konfigurációk használatával hozhat létre _az ml runconfiguration létrehozása_ parancsot. Manuálisan is szerkesztheti a futtatási konfigurációs fájlokat.

Futtassa a konfigurációk megjelenítése kísérlet futtatása élmény munkaterület részeként. 

>[!NOTE]
>Ön talál további információt a futtatási konfigurációs fájlt a [kísérlet végrehajtási hivatkozás](experimentation-service-configuration-reference.md) szakasz.

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>A parancsfájl helyileg futó futásidejű munkaterület telepítve
Munkaterület lehetővé teszi, hogy a parancsfájlok futtatását közvetlenül a munkaterület-telepítve Python 3.5.2 futásidejű szemben. Az alapértelmezett futásidejű munkaterület beállításról időpontban telepítve van, és Azure Machine Learning könyvtárai és függőségei tartalmazza. Futtatási eredmények és a helyi végrehajtások összetevők továbbra is menti a előzmények szolgáltatás futtatásához a felhőben.

Docker-alapú végrehajtások eltérően ez a konfiguráció nincs _nem_ Conda kezeli. Kell manuálisan telepíteni az csomag függőségeinek a helyi munkaterület Python-környezetben.

A következő parancsot a parancsfájl futtatásához helyi a munkaterület-telepítve Python-környezetben hajthat végre. 

```
$az ml experiment submit -c local myscript.py
```

A munkaterület parancssori ablakban a következő parancs beírásával elérési útját az alapértelmezett Python-környezetben található.
```
$ conda env list
```

>[!NOTE]
>Jelenleg futtatott PySpark helyileg közvetlenül helyi Spark környezetek **nem** támogatott. Munkaterület támogatja a helyi Docker futó PySpark parancsfájlokat. Az Azure Machine Learning Docker alapjául szolgáló lemezképhez előtelepített 2.1.11 Spark részeként elérhető. 

_**A Python-parancsfájl végrehajtása helyi áttekintése:**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>A helyi Docker parancsprogram futtatása
Futtathatja a projektek egy Docker-tároló a helyi gépen kísérletezhet szolgáltatáson keresztül. Munkaterület egy alap Docker-lemezkép részeként elérhető az Azure Machine Learning könyvtárakkal és is biztosít, Spark 2.1.11 futásidejű megkönnyítése helyi Spark végrehajtások. Docker pedig már a helyi számítógépen futnia kell.

A helyi Docker a Python vagy PySpark parancsfájlt futtat, a következő parancsokat hajthat végre a parancssori felület.

```
$az ml experiment submit -c docker myscript.py
```
vagy
```
az ml experiment submit --run-configuration docker myscript.py
```

A helyi Docker a végrehajtási környezet készen áll a testreszabásra az Azure Machine Learning alap Docker-lemezkép használata. Munkaterület letölti a lemezképet, amikor első alkalommal és átfedések is fut a conda_dependencies.yml fájlban megadott csomagokkal. Ez a művelet lehetővé teszi a kezdeti futtatása lassabban, de utólagosan lényegesen gyorsabban újból felhasználja a gyorsítótárazott rétegek munkaterület környezetnek köszönhetően. 

>[!IMPORTANT]
>Futtatásához szükséges _az ml kísérlet készítse elő a - c docker_ parancs először a Docker lemezképének előkészítése az első alkalommal történő futtatásakor. Azt is beállíthatja a **PrepareEnvironment** paraméter igaz értékű a docker.runconfig fájlban. Ez a művelet automatikusan előkészíti a környezetben a futtatási végrehajtásának részeként.  

>[!NOTE]
>A Spark PySpark parancsprogram futtatása, ha spark_dependencies.yml conda_dependencies.yml mellett is használható.

A parancsfájlok futtatását egy Docker-lemezképet a következő a következő előnyöket biztosítja:

1. Biztosítja, hogy a parancsfájl megbízhatóan végrehajthatók más végrehajtási környezetekben. Egy Docker-tároló futó segít felderíteni, és minden helyi hivatkozást, amely hatással lehet a hordozhatóság elkerülése érdekében. 

2. Lehetővé teszi futtatókörnyezetek és, amelyek összetett telepítése és konfigurálása, például az Apache Spark, anélkül, hogy a telepítéshez saját kezűleg a keretrendszereket kód gyors teszteléséhez.


_**A Python-parancsfájl végrehajtása helyi Docker áttekintése:**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>A távoli Docker parancsprogram futtatása
Bizonyos esetekben a helyi számítógépen elérhető erőforrások nem lehet elegendő a kívánt modell betanításához. Ebben a helyzetben kísérletezhet szolgáltatás lehetővé teszi, hogy egyszerűen a Python vagy PySpark parancsfájlok futtathatók a nagyobb teljesítményű virtuális gépeket Docker távoli végrehajtás. 

Távoli VM az alábbi követelményeknek kell megfelelnie:
* Távoli VM-nek kell futnia a Linux-Ubuntu és SSH-n keresztül érhető el. 
* Távoli virtuális gép futó Docker rendelkeznie kell.

>[!IMPORTANT]
> Windows virtuális gépeken futó Docker van **nem** távoli számítási célként támogatott


A következő parancs segítségével a számítási cél definíció létrehozása és futtatása a Docker-alapú távoli végrehajtások konfigurációját.

```
az ml computetarget attach --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" --type remotedocker
```

Ha a számítási cél megfelelően konfigurált, a következő paranccsal futtassa a parancsfájlt.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>Ne feledje, hogy a végrehajtási környezet konfigurálva van a specifikációk conda_dependencies.yml használatával. spark_dependencies.yml is használatos, ha a PySpark keretrendszer .runconfig fájlban megadott. 

A Docker konstrukció távoli virtuális gépek pontosan ugyanúgy történik, a helyi Docker folyamat fut, így olyan hasonló végrehajtási környezetet kell látnia.

>[!TIP]
>Ha a Tiltás késése a Docker-lemezkép az első futtatáshoz bevezetett elkerülése érdekében inkább a következő paranccsal készítse elő a számítási célja a parancsfájl végrehajtása előtt. az ml kísérlet - c előkészítése<remotedocker>


_**A Python-parancsfájl végrehajtása távoli vm áttekintése:**_
![](media/experimentation-service-configuration/remote-vm-run.png)


## <a name="running-a-script-on-an-hdinsight-cluster"></a>A HDInsight-fürtök parancsprogram futtatása
HDInsight egy népszerű big data elemzésre szolgáló Apache Spark támogató platform. Munkaterület lehetővé teszi, hogy a big Data típusú adatok használata a HDInsight Spark-fürtjei kísérletezhet. 

Számítási cél létrehozásához, és futtassa a konfiguráció a HDInsight Spark-fürtök az alábbi paranccsal:

```
$ az ml computetarget attach --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword" --type cluster 
```

>[!NOTE]
>Ha a teljes tartománynév használatára egy IP-cím helyett, és a HDI Spark-fürt neve _PEL_, az SSH-végpont esetében az illesztőprogram csomóponton nevű _PEL-ssh.azurehdinsight.net_. Ne feledje a **-ssh** teljes Tartománynevét használata esetén a kiszolgáló nevét az utólagos javítás _--cím_ paraméter.


Miután a számítási környezet, az alábbi parancsot a PySpark parancsfájlt is futtathatja.

```
$ az ml experiment submit -c myhdi myscript.py
```

Munkaterület előkészíti, és a HDInsight-fürtjéhez Conda végrehajtási környezet kezeli. Konfigurációs kezeli _conda_dependencies.yml_ és _spark_dependencies.yml_ konfigurációs fájlok. 

A HDInsight-fürthöz az SSH-elérést kell kísérletek végrehajtásához ebben a módban. 

>[!NOTE]
>A jelenleg támogatott konfiguráció HDInsight Spark-fürtök futó Linux (Python/PySpark 3.5.2 és Spark 2.1.11 Ubuntu).

_**A HDInsight-alapú végrehajtás PySpark parancsfájl áttekintése**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>A GPU parancsprogram futtatása
Az útmutató követésével GPU a parancsfájlok futtatásához:[GPU használata az Azure gépi tanulás](how-to-use-gpu.md)


## <a name="next-steps"></a>Következő lépések
* [Hozzon létre és telepítse az Azure gépi tanulás](quickstart-installation.md)
* [Modell kezelése](model-management-overview.md)
