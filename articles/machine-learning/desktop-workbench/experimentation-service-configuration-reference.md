---
title: Az Azure Machine Learning-kísérletezés szolgáltatás konfigurációs fájlok
description: Ez a dokumentum ismerteti az Azure Machine Learning-Kísérletezési szolgáltatás beállításai.
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
ms.openlocfilehash: abce80528479ba180783dbab604d4c836ddb7f1e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950777"
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Az Azure Machine Learning-kísérletezés szolgáltatás konfigurációs fájlok

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Az Azure Machine Learning (az Azure ML) Workbench alkalmazásban egy szkript futtatásakor a végrehajtását viselkedését vezérli a fájlok a **aml_config** mappát. Ezt a mappát a projekt mappa gyökérkönyvtárában alatt áll. Fontos tudni, ezek a fájlok tartalmát optimálisan a végrehajtásához a kívánt eredmények elérése érdekében.

Az alábbiakban a kapcsolódó fájlokat ebben a mappában:
- conda_dependencies.yml
- spark_dependencies.yml
- COMPUTE fájljaira
    - \<számítási cél neve > .compute
- Futtassa a konfigurációs fájlok
    - \<Futtassa a konfiguráció neve > .runconfig

>[!NOTE]
>Általában egy számítási tároló fájlt, és futtassa a konfigurációs fájlt az egyes számítási célnak hoz létre. Azonban hozzon létre külön ezeket a fájlokat, és több futtatási konfigurációs fájlt az azonos számítási célnak mutat.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Ez a fájl egy [conda-környezet fájl](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) , amely meghatározza, hogy a Python-futtatókörnyezet verziója és a csomagok, amelyek a kód függ. Az Azure ML Workbench szkriptet egy Docker-tároló vagy a HDInsight-fürtön hajt végre, amikor létrehoz egy [conda-környezet](https://conda.io/docs/using/envs.html) a parancsfájl futtatását. 

Ebben a fájlban adja meg a szkript végrehajtása van szüksége a Python-csomagokat. Az Azure Machine Learning-Kísérletezési szolgáltatás függőségek a listájának megfelelően conda-környezetet hoz létre. Az itt felsorolt csomagok elérhetőnek kell lennie a végrehajtó motor csatornákon keresztül például:

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* a nyilvánosan elérhető végpontot (URL)
* vagy egy helyi fájlelérési út
* mások a végrehajtó motor számára elérhető

>[!NOTE]
>HDInsight-fürtön futó, az Azure Machine Learning Workbench az adott Futtatás olyan conda környezetet hoz létre. Ez lehetővé teszi a különböző felhasználóknak különböző python-környezetek ugyanazon a fürtön futnak.  

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

Az Azure Machine Learning Workbench használja ugyanabban a conda-környezetben nélkül a lehető leghosszabbak újraépítése a **conda_dependencies.yml** változatlan marad. A környezet azt fogja újraépítése, ha módosítja a függőségek.

>[!NOTE]
>Ha a futtatást a megcélzott _helyi_ számítási környezetet, **conda_dependencies.yml** fájl **nem** használt. Manuálisan telepíteni kell a helyi Azure ML Workbench Python-környezet csomagfüggőségek.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Ezt a fájlt a Spark-alkalmazás nevét adja meg, egy PySpark-szkriptet és a Spark-csomagokat kell telepíteni. Megadhat egy nyilvános Maven tárházból, valamint az ilyen Maven tárházak tekintheti meg a Spark-csomagokat is.

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
>Fürt paraméterek, például a feldolgozó mérete és a magok hangolása "konfiguráció" szakasz a spark_dependecies.yml fájlban kell belépni 

>[!NOTE]
>Ha a Python-környezetet, futtatja a szkriptet *spark_dependencies.yml* fájlt a rendszer figyelmen kívül hagyja. Csak akkor, ha a Spark (akár a Docker vagy a HDInsight-fürt) futtat szolgál.

## <a name="run-configuration"></a>Futtatási konfigurációt
Adjon meg egy adott futtatási konfigurációt, szüksége van egy .compute fájlt és egy .runconfig. Ezek általában jönnek létre a CLI-parancsok használatával. Klónozza a kiépítettektől kilép, nevezze át őket, és szerkeszthetők.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Ez a parancs két fájlt a megadott számítási célnak alapján hoz létre. Tegyük fel, a számítási célnak elnevezett _foo_. Ez a parancs létrehoz _foo.compute_ és _foo.runconfig_ a a **aml_config** mappát.

>[!NOTE]
> _helyi_ vagy _docker_ a futtatási konfigurációs fájljait tetszőleges nevet. Az Azure ML Workbench hozzáadja, ez a két futtatási konfigurációkat, a felhasználók kényelme érdekében üres projekt létrehozásakor. Átnevezheti "<run configuration name>.runconfig" fájlok, amelyek a projektsablon jár, vagy hozzon létre egy nevet, az újakat.

### <a name="compute-target-namecompute"></a>\<számítási cél neve > .compute
_\<számítási cél neve > .compute_ fájl kapcsolat és a konfigurációs adatait a számítási célnak adja meg. Ez a név-érték párok listáját. A támogatott beállítások a következők:

**típus**: a számítási környezet típusát. Támogatott értékei a következők:
  - helyi
  - távoli
  - Docker
  - remotedocker
  - fürt

**baseDockerImage**: A Docker-rendszerképet használja a Python-/ PySpark-szkript futtatásához. Az alapértelmezett érték _microsoft/mmlspark:plus-0.7.91_. Egy kép is támogatja: _microsoft/mmlspark:plus-gpu-0.7.91_, amely hozzáférést biztosít a GPU gazdaszámítógépen (ha GPU található).

**cím**: IP-cím, vagy teljes tartománynév (teljesen minősített tartománynevét) a virtuális gép vagy HDInsight-fürt fő csomópontot.

**felhasználónév**: az SSH-felhasználónév a virtuális gép vagy a HDInsight fő csomópont eléréséhez.

**jelszó**: A titkosított jelszót az SSH-kapcsolat számára.

**sharedVolumes**: jelzőjét, hogy jelezze, hogy végrehajtóprogramja kell használnia a Docker megosztott kötet funkció soubory projektu oda-vissza szállításra. Ez a jelző van kapcsolva kellene meggyorsíthatja a végrehajtási óta Docker férhet hozzá közvetlenül anélkül, hogy másolja őket kellene projektek. Célszerű beállítani _hamis_ hogy a Docker-motor fut-e a Windows mennyiségi megosztása a Docker a Windows lehet flaky óta. Állítsa be _igaz_ Ha macOS vagy Linux rendszeren fut.

**nvidiaDocker**: ezt a jelzőt, ha a beállítása _igaz_, az Azure Machine Learning Kísérletezési szolgáltatás használatára utasítja _nvidia-docker_ parancsot, ellentétben a normál _docker_paranccsal, indítsa el a Docker-rendszerképet. A _nvidia-docker_ motor lehetővé teszi, hogy a hozzáférés GPU-hardveres a Docker-tárolót. A beállításra akkor szükség, ha szeretné futtatni a GPU-végrehajtás a Docker-tárolóban. Csak a Linux rendszerű gazdagép támogatja _nvidia-docker_. Ha például az Azure-ban a Linux-alapú DSVM tartalmaz _nvidia-docker_. _NVIDIA-docker_ jelen pillanatban nem támogatott a Windows.

**nativeSharedDirectory**: Ez a tulajdonság határozza meg a alapkönyvtárának (például: _~/.azureml/share/_) szeretné fájlokat is menteni ahhoz, hogy kell-e osztva az azonos számítási célnak futtat. Ha ezt a beállítást használja a Docker-tároló az _sharedVolumes_ állítsa igaz értékre. Ellenkező esetben a végrehajtás sikertelen lesz.

**userManagedEnvironment**: A tulajdonság határozza meg, hogy a számítási célnak közvetlenül a felhasználó által felügyelt vagy Kísérletezési szolgáltatás kezeli.  

**pythonLocation**: Ez a tulajdonság esetében a számítási célnak felhasználó program végrehajtásához használható a python-futtatókörnyezet helyét adja meg. 

### <a name="run-configuration-namerunconfig"></a>\<Futtassa a konfiguráció neve > .runconfig
_\<Futtassa a konfiguráció neve > .runconfig_ adja meg az Azure ML-kísérletezés végrehajtási viselkedése. Konfigurálhatja a végrehajtási működését, például a futtatási előzmények követési, vagy milyen számítási célként használandó sok más mellett. A futtatási konfigurációs fájlok nevét a végrehajtási környezet legördülő menüben, az Azure Machine Learning Workbench asztali alkalmazás feltöltésére szolgálnak.

**ArgumentVector**: Ez a szakasz meghatározza a parancsfájl futtatásához a végrehajtási és a parancsfájl paramétereit részeként. Ha rendelkezik az alábbi kódrészlet például a "<run configuration name>.runconfig" fájl 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_"az gépi tanulási kísérlet elküldése foo.runconfig"_ automatikusan futtatja a parancsot a _myscript.py_ fájl egy paraméterként és a csoportok 234 ad át a--részletes jelző.

**Cél**: ezt a paramétert annak a neve, a _.compute_ fájlt, amely a _runconfig_ fájl hivatkozik. Általában mutat a _foo.compute_ fájl, de szerkesztheti azt, hogy a különböző számítási céloknak mutasson.

**A környezeti változók**: Ez a szakasz lehetővé teszi a felhasználók környezeti változók beállítása a futtatások részeként. Felhasználói környezeti változók használatával név-érték párokat a következő formátumban adhatja meg:
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Ezeket a környezeti változókat a felhasználó-code-ban elérhető lesz. Ha például a Python-kód jelenít meg a "EXAMPLE_ENV_VAR" nevű környezeti változó
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Keretrendszer**: Ez a tulajdonság határozza meg, ha az Azure ML Workbench indítsa el a szkript futtatásához egy Spark-munkamenetet. Az alapértelmezett érték _PySpark_. Állítsa be _Python_ nem futtatásakor PySpark-kód, amely megkönnyíti a gyorsabb, kisebb terhelés a feladat elindítása.

**CondaDependenciesFile**: Ez a tulajdonság a fájlra mutat, amely meghatározza a conda környezet függőségeket a *aml_config* mappát. Ha beállítása _null_, az alapértelmezett mutat **conda_dependencies.yml** fájlt.

**SparkDependenciesFile**: Ez a tulajdonság a fájlra mutat, amely meghatározza a Spark függőségeket a **aml_config** mappát. Értékre van állítva _null_ alapértelmezés szerint és azt az alapértelmezett mutat **spark_dependencies.yml** fájlt.

**PrepareEnvironment**: ezt a tulajdonságot, ha a beállítása _igaz_, arra utasítja a conda-függőségeket, a kezdeti futtatása részeként megadott alapján a conda-környezet előkészítése a Kísérletezési szolgáltatással. Ez a tulajdonság csak egy Docker-környezetben hajtja végre esetén érvényes. Ez a beállítás nem lesz hatása, ha futtat egy _helyi_ környezetben. 

**TrackedRun**: Ez a jelölő jelzi a Kísérletezési szolgáltatással hoztam létre e nyomon követni a Futtatás az Azure ML Workbenchben előzmények infrastruktúra futtatásához. Az alapértelmezett érték _igaz_. 

**UseSampling**: _UseSampling_ Megadja, hogy használja-e az aktív mintaadatkészletek adatforrásokhoz tartozó a futtatáshoz. Ha beállítása _hamis_, adatforrások fogadni, és használja a teljes adatokat olvasni az adatokat az adattárból. Ha beállítása _igaz_, aktív mintát használják. A felhasználók használhatják a **DataSourceSettings** mely adott mintaadatkészletek használhat, ha az aktív minta felülbírálása szeretnének megadásához. 

**DataSourceSettings**: Ez a konfiguráció szakasz meghatározza az adatforrás-beállítások. Ebben a szakaszban a felhasználó adja meg, melyik meglévő adatok minta egy adott adatforrást a Futtatás részeként használatos. 

A következő konfigurációs beállítás megadja, hogy "MySample" nevű minta "MyDataSource" nevű adatforrás
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**: Data source helyettesítések is használható, amikor a felhasználó szeretne egy adatforrásból egy másik eszközre váltva a programkód módosítása nélkül. Például felhasználók is válthat egy mintát válassza ki, a helyi fájl az eredeti, nagyobb adatkészletet úgy módosítja az adatforrás-hivatkozás az Azure Blobban tárolt. Helyettesítés használata esetén az Azure ML Workbench futtathatja az adatforrások és az adatelőkészítési csomagok hivatkozik az helyére írja be az adatforrás.

Az alábbi példa kicseréli "myremote.dsource" a "mylocal.datasource" hivatkozásokat az Azure ML-adatforrások és adatelőkészítési csomagok. 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

A fenti helyettesítés alapján, az alábbi kódmintában most beolvassa a "myremote.dsource" helyett "mylocal.dsource" felhasználók a programkód módosítása nélkül.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>További lépések
Tudjon meg többet [Kísérletezési szolgáltatás konfigurációja](experimentation-service-configuration.md).
