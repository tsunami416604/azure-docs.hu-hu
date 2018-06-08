---
title: Az Azure Machine Learning kísérletezhet szolgáltatás konfigurációs fájlok
description: Ez a dokumentum az Azure ML kísérletezhet szolgáltatás konfigurációs beállításait részletezi.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 1a4b6b803687b2c433ad94a54f076f23fe63c350
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831312"
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Az Azure Machine Learning kísérletezhet szolgáltatás konfigurációs fájlok

A parancsfájl az Azure Machine Learning (Azure ML) munkaterület futtatásakor a végrehajtási viselkedése vezérli a fájlok a **aml_config** mappa. Ez a mappa a project mappa gyökere alatt áll. Fontos tudni, ezek a fájlok tartalmát optimálisan a végrehajtásra a kívánt eredmény elérése érdekében.

Az alábbiakban a megfelelő fájlokat ebben a mappában:
- conda_dependencies.yml
- spark_dependencies.yml
- számítási fájljaira
    - \<számítási cél neve > .compute
- Futtassa a konfigurációs fájlok
    - \<Futtassa a konfiguráció neve > .runconfig

>[!NOTE]
>Általában egy számítási célfájl rendelkezik, és futtassa a konfigurációs fájl minden számítási cél hoz létre. Azonban létrehozhat egymástól függetlenül ezeket a fájlokat és számítási egyazon célobjektum mutató több futtatási konfigurációs fájlt.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Ez a fájl egy [conda környezet fájl](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) , amely megadja, hogy a Python-futtatókörnyezet verziója és a csomagok, amelyek elengedhetetlenek a kódot. Azure ML munkaterület parancsfájl egy Docker-tároló vagy a HDInsight-fürt hajt végre, amikor létrehoz egy [conda környezet](https://conda.io/docs/using/envs.html) a parancsfájl futtatásához. 

Ebben a fájlban adja meg, amelyet a parancsfájl végrehajtása a Python-csomagokat. Az Azure ML kísérletezhet szolgáltatás függőségek a listájának megfelelően conda környezete hoz létre. Az itt felsorolt csomagok elérhetőnek kell lennie a végrehajtó motorja csatornákon keresztül, mint:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* a nyilvánosan elérhető végponton (URL)
* vagy egy helyi fájl elérési útját
* mások számára elérhető-e a végrehajtási motor

>[!NOTE]
>A HDInsight-fürtök futtatásakor Azure ML munkaterület környezetet hoz létre conda az adott futtatáshoz. Ez lehetővé teszi a különböző felhasználók különböző python-környezetek ugyanazon a fürtön futtatni.  

Íme egy példa egy tipikus **conda_dependencies.yml** fájlt.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local Docker target)
     - C:\temp\my_private_python_pkg.whl
```

Az Azure ML munkaterület a azonos conda környezetet használ újból összeállítani a lehető leghosszabbak nélkül a **conda_dependencies.yml** változatlan marad. Ha módosítja a függőségek azt fogja újraépítése a környezetben.

>[!NOTE]
>Ha célozhat meg végrehajtásának _helyi_ számítási környezet **conda_dependencies.yml** fájl **nem** használt. A helyi Azure ML munkaterület Python környezetnek csomagfüggőségek kell manuálisan kell telepíteni.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Ez a fájl neve a Spark alkalmazás PySpark parancsfájlt és a külső csomagok, amelyek telepítve kell lennie. Egy nyilvános Maven-tárházat, valamint az adott Maven tárházak találhatók Spark csomagokat is megadható.

Például:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Fürt paraméterek például munkavégző méret és -magok hangolása kell kísérhet "konfiguráció" című szakaszában a spark_dependecies.yml fájl 

>[!NOTE]
>Ha a parancsfájl Python-környezetben, amelyek végrehajtása *spark_dependencies.yml* fájlt a rendszer figyelmen kívül hagyja. Csak akkor, ha futtatja elleni Spark (vagy a Docker vagy HDInsight-fürtök) használható.

## <a name="run-configuration"></a>Futtassa a konfiguráció
Adja meg az adott futtatási konfigurációját, a .compute fájlt és egy .runconfig kell. Ezek általában jönnek létre CLI parancs használatával. Klónozza a Kilépés azokat, nevezze át őket, és szerkeszthetők.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Ezzel a paranccsal létrejön egy meghatározott számítási cél alapuló pár. Tegyük fel, a számítási célként elnevezett _PEL_. A parancs létrehozza _foo.compute_ és _foo.runconfig_ a a **aml_config** mappa.

>[!NOTE]
> _helyi_ vagy _docker_ a futtatási konfigurációs fájlokat a következők tetszőleges nevet. Az Azure ML munkaterület ad hozzá, ez a két konfigurációk futtatását, amikor a felhasználók kényelme érdekében hozzon létre egy üres projektet. Átnevezheti "<run configuration name>.runconfig" projektsablon rendelkeznek, vagy hozzon létre újakat tetszőleges nevet a fájlok.

### <a name="compute-target-namecompute"></a>\<számítási cél neve > .compute
_\<számítási cél neve > .compute_ fájl határozza meg a számítási cél a kapcsolat és konfigurációs információt. A név-érték párok listáját is. Az alábbiakban a támogatott beállítások:

**típus**: a számítási környezet típusú. Támogatott értékek a következők:
  - helyi
  - Távoli
  - Docker
  - remotedocker
  - fürt

**baseDockerImage**: a Python/PySpark parancsfájl futtatásához használt a Docker-lemezképet. Az alapértelmezett érték _microsoft/mmlspark:plus-0.7.91_. Egy másik lemezképet is támogatja: _microsoft/mmlspark:plus-gpu-0.7.91_, amely hozzáférést biztosít a GPU gazdaszámítógépen (ha jelen a GPU).

**cím**: IP-cím vagy a teljes Tartománynevet (teljesen minősített tartománynevét) a virtuális gép vagy a HDInsight fürt átjárócsomópontjához.

**felhasználónév**: az SSH felhasználónév a virtuális gép vagy a HDInsight-átjárócsomópont eléréséhez.

**jelszó**: az SSH-kapcsolat a titkosított jelszót.

**sharedVolumes**: jelezze, hogy végrehajtó motorja kell használnia a Docker jelző megosztott kötet szolgáltatást, hogy küldje el a projektfájlok oda-vissza. Ez a jelző engedélyezve van a rendelkező felgyorsíthatja a végrehajtási óta Docker férhetnek hozzá projektek közvetlenül nélkül másolja őket. Célszerű beállítani _hamis_ hogy a Docker-motorhoz fut a Windows mennyiségi megosztása lehet, hogy a Windows a Docker flaky óta. Állítsa az értékét _igaz_ macOS vagy Linux rendszeren fut. Ha.

**nvidiaDocker**: ezt a jelzőt, ha beállítása _igaz_, ismerteti az Azure ML kísérletezhet szolgáltatás használatára _nvidia-docker_ parancsot, szemben a normál _docker_parancs, a Docker lemezkép elindításához. A _nvidia-docker_ motor lehetővé teszi, hogy a Docker-tároló hozzáférés GPU hardverre. A beállításra akkor szükség, ha azt szeretné, hogy a Docker-tároló GPU végrehajtási futtatásához. Csak a Linux-állomáshoz támogatja _nvidia-docker_. Például az Azure Linux-alapú DSVM részét képező _nvidia-docker_. _NVIDIA-docker_ mostantól nem támogatott a Windows.

**nativeSharedDirectory**: Ez a tulajdonság meghatározza a alapkönyvtárának (például: _~/.azureml/share/_) számítási egyazon célobjektum futó fájlok is menteni ahhoz, hogy oszthatók meg. Ha ezt a beállítást használják egy Docker-tároló használatakor _sharedVolumes_ , meg kell igaz értékre. Ellenkező esetben végrehajtása meghiúsul.

**userManagedEnvironment**: Ez a tulajdonság meghatározza, hogy a számítási cél közvetlenül kezeli a felhasználó vagy kísérleti szolgáltatáson keresztül felügyelt.  

**pythonLocation**: Ez a tulajdonság meghatározza a python-futtatókörnyezet használatát a számítási célszámítógépen felhasználó program végrehajtásához helyét. 

### <a name="run-configuration-namerunconfig"></a>\<Futtassa a konfiguráció neve > .runconfig
_\<Futtassa a konfiguráció neve > .runconfig_ határozza meg, az Azure ML végrehajtási viselkedésének kipróbálásához. Konfigurálhatja a végrehajtási működését, például a nyomon követési futtatási előzményei, vagy mi számítási cél-és sok más használhatja. A végrehajtási környezet legördülő lista az Azure ML munkaterület asztali alkalmazás feltöltésére használatos olyan futtatási konfigurációs fájl nevét.

**ArgumentVector**: Ebben a szakaszban adja meg a parancsfájl futtatásához a végrehajtása és a parancsfájl paramétereit részeként. Például, ha vannak a következő példában a "<run configuration name>.runconfig" fájl 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az ml kísérlet submit foo.runconfig"_ automatikusan futtatja a parancsot _myscript.py_ paraméter és a készletek 234 benyújtása fájl a--részletes jelzőt.

**Cél**: A paraméter nevét a _.compute_ fájlt, amely a _runconfig_ fájl hivatkozik. Általában mutat a _foo.compute_ fájlt, de szerkesztheti úgy, hogy egy másik számítási cél mutasson.

**Környezeti változók**: Ez a szakasz lehetővé teszi a felhasználók a környezeti változókat beállítani, azok futtatása. Felhasználó megadhatja a környezeti változók név-érték párok használatával a következő formátumban:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Ezek a környezeti változók felhasználói kód érhetők el. Például a Python kódját kiírja a "EXAMPLE_ENV_VAR" nevű környezeti változó
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Keretrendszer**: Ez a tulajdonság határozza meg, ha Azure ML munkaterület kell nyissa meg a parancsfájl futtatása Spark munkamenetet. Az alapértelmezett érték _PySpark_. Állítsa az értékét _Python_ Ha nem futtatja a PySpark kód, amely segíthet a gyorsabb kisebb terhelés a feladat elindítása.

**CondaDependenciesFile**: Ez a tulajdonság a fájlra mutat, amely meghatározza a conda környezet függőségeit a *aml_config* mappa. Ha beállítása _null_, az alapértelmezett mutat **conda_dependencies.yml** fájlt.

**SparkDependenciesFile**: Ez a tulajdonság a fájlra mutat, amely meghatározza a külső függőségeit a **aml_config** mappa. Érték _null_ alapértelmezett és mutat, az alapértelmezett **spark_dependencies.yml** fájlt.

**PrepareEnvironment**: ezt a tulajdonságot, ha beállítása _igaz_, közli az alapján, az első végrehajtása megadott conda függőségek conda környezet előkészítése a kísérleti szolgáltatást. Ez a tulajdonság csak egy Docker-környezet ellen végrehajtása esetén hatékony. Ez a beállítás nincs hatása, ha elleni futtat egy _helyi_ környezetben. 

**TrackedRun**: Ez a jelző jelzi a kísérleti szolgáltatás Azure ML munkaterület előzmények infrastruktúra futtassa a Futtatás nyomon kell-e. Az alapértelmezett érték _igaz_. 

**UseSampling**: _UseSampling_ meghatározza, hogy használja-e az aktív mintaként használható adathalmazt adatforrások futtatáshoz. Ha beállítása _hamis_, adatforrások betöltési, és használja a teljes adatokat olvasni az adattárból. Ha beállítása _igaz_, aktív minták szolgálnak. A felhasználók használhatják a **DataSourceSettings** adhatja meg, mely adott mintaként használható adathalmazt Ha bírálja felül az aktív minta szeretnék használni. 

**DataSourceSettings**: Ez a konfigurációs szakasz megadja az adatforrás-beállítások. Ebben a szakaszban a felhasználó határozza meg, melyik meglévő adatok mintát eredményez, amely egy adott adatforrást a Futtatás részeként használatos. 

A következő konfigurációs beállítás megadja, hogy "MySample" nevű minta "MyDataSource" nevű adatforrás
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: adatok forrás helyettesítések is használható, ha a felhasználó szeretne váltani egy adatforrás másik a programkód módosítása nélkül. Például felhasználók átválthatnak mintát le, a helyi fájlból az Azure Blob tárolja az adatforrás-hivatkozás módosításával eredeti, nagyobb adatkészletet. Helyettesítés használata esetén Azure ML munkaterület fut. az adatforrások és az adatok előkészítése csomagok helyettesítő adatforrás Vezérlőpultjának

Az alábbi példában a "mylocal.datasource" hivatkozást, az Azure ML adatforrások és az adatok előkészítése csomagok cseréli "myremote.dsource". 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

A fenti helyettesítés alapján, a következő példakód most olvassa be az "myremote.dsource" helyett "mylocal.dsource" a felhasználók a programkód módosítása nélkül.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>További lépések
További információ [kísérletezhet szolgáltatáskonfiguráció](experimentation-service-configuration.md).
