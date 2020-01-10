---
title: Gépi tanulási és adatelemzési eszközök
description: Ismerkedjen meg a Data Science Virtual Machine előtelepített gépi tanulási eszközökkel és keretrendszerekkel.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c77e1834804dba0d2e5fd6eaec574ffce19d1d45
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771206"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Gépi tanulási és adatelemzési eszközök az Azure adatelemzési Virtual Machines
Az Azure adatelemzési Virtual Machines (Dsvm) számos eszközt és kódtárat tartalmaz a gépi tanuláshoz, amely népszerű nyelveken érhető el, például a Python, az R és a Julia.

Íme néhány, a Dsvm-alapú gépi tanulási eszköz és könyvtár.

## <a name="azure-machine-learning-sdk-for-python"></a>Pythonhoz készült Azure Machine Learning SDK

Tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)teljes referenciáját.

|    |           |
| ------------- | ------------- |
| Mi ez?   |   A Azure Machine Learning egy felhőalapú szolgáltatás, amely gépi tanulási modellek fejlesztésére és üzembe helyezésére használható. A modelleket a Python SDK használatával követheti, betaníthatja, méretezheti és kezelheti. A modelleket tárolóként helyezheti üzembe, és a felhőben, a helyszínen vagy Azure IoT Edge futtathatja.   |
| Támogatott kiadások     | Windows (Conda-környezet: AzureML), Linux (Conda-környezet: py36)    |
| Jellemző felhasználások      | Általános gépi tanulási platform      |
| Hogyan van konfigurálva vagy telepítve?      |  GPU-támogatással telepítve   |
| Használat vagy Futtatás      | Python SDK-val és az Azure CLI-vel. Aktiválja a Conda-környezet `AzureML` a Windows Edition rendszeren, *vagy* `py36` Linux rendszeren.      |
| Mintákra mutató hivatkozás      | A minta Jupyter jegyzetfüzetek a `AzureML` könyvtárban találhatók a jegyzetfüzetek területen.  |
| Kapcsolódó eszközök      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy nyílt forráskódú AI platform, amely támogatja a memóriában, az elosztott, a gyors és a méretezhető gépi tanulást.  |
| Támogatott verziók      | Linux   |
| Jellemző felhasználások      | Általános célú elosztott, méretezhető gépi tanulás   |
| Hogyan van konfigurálva vagy telepítve?      | A H2O `/dsvm/tools/h2o`van telepítve.      |
| Használat vagy Futtatás      | Kapcsolódjon a virtuális géphez a X2Go használatával. Indítson el egy új terminált, és futtassa a `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ezután indítson el egy webböngészőt, és kapcsolódjon `http://localhost:54321`hoz.      |
| Mintákra mutató hivatkozás      | A minták a Jupyter virtuális gépen érhetők el a `h2o` könyvtár alatt.      |
| Kapcsolódó eszközök      | Apache Spark, MXNet, XGBoost, szénsavas víz, mélyvízi    |

A Dsvm számos más gépi tanulási kódtár található, például a népszerű `scikit-learn` csomag, amely a Dsvm-hez készült anaconda Python-disztribúció részét képezi. A Pythonban, az R-ben és a Julia-ben elérhető csomagok listájának megkereséséhez futtassa a megfelelő csomagkezelő-kezelőket.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy gyors, elosztott, nagy teljesítményű színátmenet-fokozó (GBDT, GBRT, GBM vagy MART) keretrendszer döntési fa algoritmusok alapján. Ez a rangsorolás, besorolás és sok más gépi tanulási feladat esetében használatos.    |
| Támogatott verziók      | Windows, Linux    |
| Jellemző felhasználások      | Általános célú átmenet – fellendítő keretrendszer      |
| Hogyan van konfigurálva vagy telepítve?      | Windows rendszeren a LightGBM Python-csomagként van telepítve. Linux rendszeren a parancssori végrehajtható fájl `/opt/LightGBM/lightgbm`, az R-csomag telepítve van, és a rendszer telepíti a Python-csomagokat.     |
| Mintákra mutató hivatkozás      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület az adatbányászathoz az R használatával.   |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Használat vagy Futtatás      | Felhasználói felületi eszközként. Windows rendszeren indítson el egy parancssort, futtassa az R-t, majd az R-ben futtassa `rattle()`. Linux rendszeren kapcsolódjon a X2Go, indítson el egy terminált, futtassa az R parancsot, majd az R-ben futtassa `rattle()`. |
| Mintákra mutató hivatkozás      | [Csörgő](https://togaware.com/onepager/) |
| Kapcsolódó eszközök      |LightGBM, WEKA, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, nyílt forráskódú, nem alapszintű tanulási rendszerkönyvtár    |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási könyvtár      |
| Hogyan van konfigurálva vagy telepítve?      |  Windows: msi-telepítő<br/>Linux: apt-get |
| Használat vagy Futtatás      | Elérési út parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows rendszeren, Linuxon `/usr/bin/vw`)    |
| Mintákra mutató hivatkozás      | [VowPal Wabbit-minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Az algoritmusokat közvetlenül egy adatkészletre lehet alkalmazni, vagy a saját Java-kódjából hívható meg. A WEKA az adatfeldolgozás, a besorolás, a regresszió, a fürtözés, a társítási szabályok és a vizualizációk eszközeit tartalmazza. |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási eszköz     |
| Használat vagy Futtatás      | Windows rendszeren keresse meg a WEKA a **Start** menüben. Linux rendszeren jelentkezzen be a X2Go-be, majd lépjen az **alkalmazások** > **fejlesztési** > **WEKA**. |
| Mintákra mutató hivatkozás      | [WEKA-minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Kapcsolódó eszközök      |LightGBM, csörgő, XGBoost   |

## <a name="xgboost"></a>Xgboost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, hordozható és elosztott gradiens-növelő (GBDT, GBRT vagy GBM) függvénytár a Python, az R, a Java, a Scala C++és más rendszerekhez. Egyetlen gépen fut, Apache Hadoop és Sparkon.    |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási könyvtár      |
| Hogyan van konfigurálva vagy telepítve?      |  GPU-támogatással telepítve   |
| Használat vagy Futtatás      | Python-függvénytárként (2,7 és 3,5), R Package és on-Path parancssori eszköz (`C:\dsvm\tools\xgboost\bin\xgboost.exe` a Windows és a Linux rendszerhez készült `/dsvm/tools/xgboost/xgboost`)    |
| Mintákra mutató hivatkozások      | A minták a virtuális gépen, `/dsvm/tools/xgboost/demo` Linux rendszeren, és a Windows `C:\dsvm\tools\xgboost\demo` is elérhetők.   |
| Kapcsolódó eszközök      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor big data    |
| Támogatott DSVM-verziók      | Windows 2019 (előzetes verzió), Linux  |
| Hogyan van konfigurálva és telepítve a DSVM?      |  Csak beágyazott módban telepített `/dsvm/tools/drill*`   |
| Jellemző felhasználások      |  Helyben történő adatfeltáráshoz kinyerési, átalakítási, betöltési (ETL) adatok nélkül. Különböző adatforrások és-formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a rokon táblákat és a Hadoop.     |
| Használat és Futtatás      | Asztali parancsikon  <br/> [Ismerkedés a részletezéssel 10 percen belül](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A DSVM kapcsolódó eszközök      |   Csörgő, WEKA, SQL Server Management Studio      |


