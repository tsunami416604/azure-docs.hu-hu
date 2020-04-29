---
title: Gépi tanulási és adatelemzési eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerkedjen meg a Data Science Virtual Machine előtelepített gépi tanulási eszközökkel és keretrendszerekkel.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282307"
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
| Használat vagy Futtatás      | Python SDK-val és az Azure CLI-vel. Aktiválja a Conda környezetet `AzureML` Windows Edition *vagy* `py36` Linux rendszeren.      |
| Mintákra mutató hivatkozás      | A minta Jupyter jegyzetfüzetek a jegyzetfüzetek `AzureML` alatt találhatók a könyvtárban.  |
| Kapcsolódó eszközök      | Visual Studio Code, Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy nyílt forráskódú AI platform, amely támogatja a memóriában, az elosztott, a gyors és a méretezhető gépi tanulást.  |
| Támogatott verziók      | Linux   |
| Jellemző felhasználások      | Általános célú elosztott, méretezhető gépi tanulás   |
| Hogyan van konfigurálva vagy telepítve?      | A H2O telepítve van `/dsvm/tools/h2o`a-ben.      |
| Használat vagy Futtatás      | Kapcsolódjon a virtuális géphez a X2Go használatával. Indítson el egy új terminált `java -jar /dsvm/tools/h2o/current/h2o.jar`, és futtassa a parancsot. Ezután indítson el egy webböngészőt `http://localhost:54321`, és kapcsolódjon a következőhöz:.      |
| Mintákra mutató hivatkozás      | A minták a Jupyter található virtuális gépen érhetők el a `h2o` könyvtárban.      |
| Kapcsolódó eszközök      | Apache Spark, MXNet, XGBoost, szénsavas víz, mélyvízi    |

A Dsvm-on számos más gépi tanulási kódtár található, például a népszerű `scikit-learn` csomag, amely a dsvm-hez készült anaconda Python-eloszlás részét képezi. A Pythonban, az R-ben és a Julia-ben elérhető csomagok listájának megkereséséhez futtassa a megfelelő csomagkezelő-kezelőket.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy gyors, elosztott, nagy teljesítményű színátmenet-fokozó (GBDT, GBRT, GBM vagy MART) keretrendszer döntési fa algoritmusok alapján. Ez a rangsorolás, besorolás és sok más gépi tanulási feladat esetében használatos.    |
| Támogatott verziók      | Windows, Linux    |
| Jellemző felhasználások      | Általános célú átmenet – fellendítő keretrendszer      |
| Hogyan van konfigurálva vagy telepítve?      | Windows rendszeren a LightGBM Python-csomagként van telepítve. Linux rendszeren a parancssori végrehajtható fájl, az R- `/opt/LightGBM/lightgbm`csomag telepítve van, a Python-csomagok pedig telepítve vannak.     |
| Mintákra mutató hivatkozás      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület az adatbányászathoz az R használatával.   |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Használat vagy Futtatás      | Felhasználói felületi eszközként. Windows rendszeren indítson el egy parancssort, futtassa az R-t, majd az `rattle()`r-ben futtassa a parancsot. Linux rendszeren kapcsolódjon a X2Go, indítson el egy terminált, futtassa az R parancsot, majd `rattle()`az r-ben futtassa a parancsot. |
| Mintákra mutató hivatkozás      | [Rattle](https://togaware.com/onepager/) |
| Kapcsolódó eszközök      |LightGBM, WEKA, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, nyílt forráskódú, nem alapszintű tanulási rendszerkönyvtár    |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási könyvtár      |
| Hogyan van konfigurálva vagy telepítve?      |  Windows: msi-telepítő<br/>Linux: apt-get |
| Használat vagy Futtatás      | Elérési út parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows rendszeren, `/usr/bin/vw` Linux rendszeren)    |
| Mintákra mutató hivatkozás      | [VowPal Wabbit-minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Az algoritmusokat közvetlenül egy adatkészletre lehet alkalmazni, vagy a saját Java-kódjából hívható meg. A WEKA az adatfeldolgozás, a besorolás, a regresszió, a fürtözés, a társítási szabályok és a vizualizációk eszközeit tartalmazza. |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási eszköz     |
| Használat vagy Futtatás      | Windows rendszeren keresse meg a WEKA a **Start** menüben. Linux rendszeren jelentkezzen be a X2Go-be, majd nyissa meg az **alkalmazások** > **fejlesztői** > **WEKA**. |
| Mintákra mutató hivatkozás      | [WEKA-minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Kapcsolódó eszközök      |LightGBM, csörgő, XGBoost   |

## <a name="xgboost"></a>Xgboost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |   A Python, az R, a Java, a Scala, a C++ és más rendszerekhez készült gyors, hordozható és elosztott gradiens-növelő (GBDT, GBRT vagy GBM) kódtár. Egyetlen gépen fut, Apache Hadoop és Sparkon.    |
| Támogatott kiadások     | Windows, Linux     |
| Jellemző felhasználások      | Általános gépi tanulási könyvtár      |
| Hogyan van konfigurálva vagy telepítve?      |  GPU-támogatással telepítve   |
| Használat vagy Futtatás      | Python-függvénytárként (2,7 és 3,5), R Package és on-Path parancssori eszköz (`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows és `/dsvm/tools/xgboost/xgboost` Linux rendszerekhez)    |
| Mintákra mutató hivatkozások      | A minták a virtuális gépen, `/dsvm/tools/xgboost/demo` Linuxon és `C:\dsvm\tools\xgboost\demo` Windowson is elérhetők.   |
| Kapcsolódó eszközök      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor big data    |
| Támogatott DSVM-verziók      | Windows 2019, Linux  |
| Hogyan van konfigurálva és telepítve a DSVM?      |  `/dsvm/tools/drill*` Csak beágyazott módban van telepítve   |
| Jellemző felhasználások      |  Helyben történő adatfeltáráshoz kinyerési, átalakítási, betöltési (ETL) adatok nélkül. Különböző adatforrások és-formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a rokon táblákat és a Hadoop.     |
| Használat és Futtatás      | Asztali parancsikon  <br/> [Ismerkedés a részletezéssel 10 percen belül](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A DSVM kapcsolódó eszközök      |   Csörgő, WEKA, SQL Server Management Studio      |


