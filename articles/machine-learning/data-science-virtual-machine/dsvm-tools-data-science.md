---
title: Machine learning és adatelemzési eszközök
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
ms.openlocfilehash: c6b7e3da6cb6fd87e3b43d6f310e3b76f8fc4d30
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390635"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Gépi tanulási és adatelemzési eszközök az Azure adatelemzési Virtual Machines
Az Azure adatelemzési Virtual Machines (Dsvm) számos eszközt és kódtárat tartalmaz a gépi tanuláshoz, amely népszerű nyelveken érhető el, például a Python, az R és a Julia.

Íme néhány, a Dsvm-alapú gépi tanulási eszköz és könyvtár.

## <a name="azure-machine-learning-sdk-for-python"></a>Pythonhoz készült Azure Machine Learning SDK

Tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)teljes referenciáját.

|    |           |
| ------------- | ------------- |
| Mi ez?   |   A Azure Machine Learning egy felhőalapú szolgáltatás, amely gépi tanulási modellek fejlesztésére és üzembe helyezésére használható. Nyomon követheti a modellek, fejleszthet, betanítását, méretezési és a Python SDK-val kezelheti azokat. Tárolókként modellek üzembe helyezése, és futtathatók a felhőben, a helyszínen, vagy az Azure IoT Edge-ben.   |
| Támogatott kiadások     | Windows (conda-környezet: AzureML), Linux (conda-környezet: py36)    |
| Gyakori használati      | Általános gépi tanulási platform      |
| Hogyan azt konfigurálni vagy telepíteni?      |  GPU-támogatással rendelkező telepítve   |
| Használatával, vagy futtassa      | Python SDK-val és az Azure CLI-vel. Aktiválja a Conda-környezet `AzureML` a Windows Edition rendszeren, *vagy* `py36` Linux rendszeren.      |
| A minták mutató hivatkozás      | A minta Jupyter jegyzetfüzetek a `AzureML` könyvtárban találhatók a jegyzetfüzetek területen.  |
| Kapcsolódó eszközök      | A Visual Studio Code-ban Jupyter   |

## <a name="h2o"></a>H2O

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy nyílt forráskódú AI platform, amely támogatja a memóriában, az elosztott, a gyors és a méretezhető gépi tanulást.  |
| Támogatott verziók      | Linux   |
| Gyakori használati      | Általános célú elosztott, méretezhető gépi tanulás   |
| Hogyan azt konfigurálni vagy telepíteni?      | A H2O `/dsvm/tools/h2o`van telepítve.      |
| Használatával, vagy futtassa      | Csatlakozás a virtuális gép X2Go használatával. Indítson el egy új terminált, és futtassa a `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ezután indítson el egy webböngészőt, és kapcsolódjon `http://localhost:54321`hoz.      |
| A minták mutató hivatkozás      | A minták a Jupyter virtuális gépen érhetők el a `h2o` könyvtár alatt.      |
| Kapcsolódó eszközök      | Az Apache Spark, MXNet, XGBoost, pezsgő vízzel, mély víz    |

A Dsvm számos más gépi tanulási kódtár található, például a népszerű `scikit-learn` csomag, amely a Dsvm-hez készült anaconda Python-disztribúció részét képezi. Tekintse meg a Python, R és Julia elérhető csomagok listájában, futtassa a megfelelő csomagkezelők.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy gyors, elosztott, nagy teljesítményű színátmenet-fokozó (GBDT, GBRT, GBM vagy MART) keretrendszer döntési fa algoritmusok alapján. Ez a rangsorolás, besorolás és sok más gépi tanulási feladat esetében használatos.    |
| Támogatott verziók      | Windows, Linux    |
| Gyakori használati      | Általános célú átmenet – fellendítő keretrendszer      |
| Hogyan azt konfigurálni vagy telepíteni?      | A Windows Python-csomag LightGBM van telepítve. Linux rendszeren a parancssori végrehajtható fájl `/opt/LightGBM/lightgbm`, az R-csomag telepítve van, és a rendszer telepíti a Python-csomagokat.     |
| A minták mutató hivatkozás      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület az adatbányászathoz az R használatával.   |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Használatával, vagy futtassa      | Felhasználói felületi eszközként. Windows rendszeren indítson el egy parancssort, futtassa az R-t, majd az R-ben futtassa `rattle()`. Linux rendszeren kapcsolódjon a X2Go, indítson el egy terminált, futtassa az R parancsot, majd az R-ben futtassa `rattle()`. |
| A minták mutató hivatkozás      | [Csörgő](https://togaware.com/onepager/) |
| Kapcsolódó eszközök      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, nyílt forráskódú, nem alapszintű tanulási rendszerkönyvtár    |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási könyvtár      |
| Hogyan azt konfigurálni vagy telepíteni?      |  Windows: msi-telepítő<br/>Linux: apt-get |
| Használatával, vagy futtassa      | Elérési út parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows rendszeren, Linuxon `/usr/bin/vw`)    |
| A minták mutató hivatkozás      | [VowPal Wabbit-minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Az algoritmusok kell közvetlenül egy adatkészlet a alkalmazni, vagy saját Java kódot meghívva. Weka előtti adatfeldolgozás, besorolás, regressziós, fürtözés, társítási szabályok és megjelenítési eszközöket tartalmaz. |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási eszköz     |
| Használatával, vagy futtassa      | Windows rendszeren keresse meg a WEKA a **Start** menüben. Linux rendszeren jelentkezzen be a X2Go-be, majd lépjen az **alkalmazások** > **fejlesztési** > **WEKA**. |
| A minták mutató hivatkozás      | [WEKA-minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Kapcsolódó eszközök      |LightGBM, Rattle, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, hordozható és elosztott gradiens-növelő (GBDT, GBRT vagy GBM) függvénytár a Python, az R, a Java, a Scala C++és más rendszerekhez. Egyetlen gépen fut, Apache Hadoop és Sparkon.    |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási könyvtár      |
| Hogyan azt konfigurálni vagy telepíteni?      |  GPU-támogatással rendelkező telepítve   |
| Használatával, vagy futtassa      | Python-függvénytárként (2,7 és 3,5), R Package és on-Path parancssori eszköz (`C:\dsvm\tools\xgboost\bin\xgboost.exe` a Windows és a Linux rendszerhez készült `/dsvm/tools/xgboost/xgboost`)    |
| A minták mutató hivatkozások      | A minták a virtuális gépen, `/dsvm/tools/xgboost/demo` Linux rendszeren, és a Windows `C:\dsvm\tools\xgboost\demo` is elérhetők.   |
| Kapcsolódó eszközök      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor big data    |
| Támogatott DSVM-verziók      | Windows 2019 (előzetes verzió), Linux  |
| Hogyan van konfigurálva és telepítve a DSVM?      |  Csak beágyazott módban telepített `/dsvm/tools/drill*`   |
| Gyakori használati      |  Helyben történő adatfeltáráshoz kinyerési, átalakítási, betöltési (ETL) adatok nélkül. Különböző adatforrások és-formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a rokon táblákat és a Hadoop.     |
| Használat és Futtatás      | Asztali parancsikon  <br/> [Ismerkedés a részletezéssel 10 percen belül](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| A DSVM kapcsolódó eszközök      |   Rattle, Weka, SQL Server Management Studio      |


