---
title: Machine learning és adatelemzési eszközök
description: Ismerkedjen meg a Data Science Virtual Machine előtelepített gépi tanulási eszközökkel és keretrendszerekkel.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: dec1589898cf6cc239dbe737519e078f1c4bc7b1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744928"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Gépi tanulási és adatelemzési eszközök az Azure adatelemzési Virtual Machines
Az Azure adatelemzési Virtual Machines (Dsvm) számos eszközt és kódtárat tartalmaz a gépi tanuláshoz, amely népszerű nyelveken érhető el, például a Python, az R és a Julia.

Íme néhány, a Dsvm-alapú gépi tanulási eszköz és könyvtár.

## <a name="azure-machine-learning-sdk-for-python"></a>A Pythonhoz készült Azure Machine Learning SDK
Tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)teljes referenciáját.

|    |           |
| ------------- | ------------- |
| Mi ez?   |   A Azure Machine Learning egy felhőalapú szolgáltatás, amely gépi tanulási modellek fejlesztésére és üzembe helyezésére használható. Nyomon követheti a modellek, fejleszthet, betanítását, méretezési és a Python SDK-val kezelheti azokat. Tárolókként modellek üzembe helyezése, és futtathatók a felhőben, a helyszínen, vagy az Azure IoT Edge-ben.   |
| Támogatott kiadások     | Windows (Conda-környezet: AzureML), Linux (Conda Environment: py36)    |
| Gyakori használati      | Általános gépi tanulási platform      |
| Hogyan azt konfigurálni vagy telepíteni?      |  GPU-támogatással rendelkező telepítve   |
| Használatával, vagy futtassa      | Python SDK-val és az Azure CLI-vel. Aktiválja a conda-környezet `AzureML` Windows Edition *vagy* való `py36` Linux rendszeren.      |
| A minták mutató hivatkozás      | Mintául szolgáló Jupyter notebookok szerepelnek a `AzureML` notebookok a könyvtárban.  |
| Kapcsolódó eszközök      | A Visual Studio Code-ban Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, hordozható és elosztott gradiens-növelő (GBDT, GBRT vagy GBM) függvénytár a Python, az R, a Java, a Scala C++és más rendszerekhez. Egyetlen gépen fut, Apache Hadoop és Sparkon.    |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási könyvtár      |
| Hogyan azt konfigurálni vagy telepíteni?      |  GPU-támogatással rendelkező telepítve   |
| Használatával, vagy futtassa      | Python-függvénytárként (2,7 és 3,5), R Package és on-Path parancssori eszköz (`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows és `/dsvm/tools/xgboost/xgboost` Linux rendszerekhez)    |
| A minták mutató hivatkozások      | A minták szerepelnek a virtuális gépen, `/dsvm/tools/xgboost/demo` , linuxon és `C:\dsvm\tools\xgboost\demo` a Windows.   |
| Kapcsolódó eszközök      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, nyílt forráskódú, nem alapszintű tanulási rendszerkönyvtár    |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási könyvtár      |
| Hogyan azt konfigurálni vagy telepíteni?      |  Windows: msi-telepítő<br/>Linux: apt-get |
| Használatával, vagy futtassa      | Az elérési út parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows, a `/usr/bin/vw` Linux rendszeren)    |
| A minták mutató hivatkozás      | [VowPal Wabbit minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Az algoritmusok kell közvetlenül egy adatkészlet a alkalmazni, vagy saját Java kódot meghívva. Weka előtti adatfeldolgozás, besorolás, regressziós, fürtözés, társítási szabályok és megjelenítési eszközöket tartalmaz. |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi tanulási eszköz     |
| Használatával, vagy futtassa      | Windows rendszeren keresse meg a WEKA a **Start** menüben. Linuxon, jelentkezzen be X2Go, és folytassa a **alkalmazások** > **fejlesztési** > **Weka**. |
| A minták mutató hivatkozás      | [Weka minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Kapcsolódó eszközök      |LightGBM, Rattle, XGBoost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület az adatbányászathoz az R használatával.   |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felületi adatbányászati eszköz az R-hez    |
| Használatával, vagy futtassa      | Felhasználói felületi eszközként. Windows rendszeren indítson el egy parancssort, futtassa az R-t, majd az `rattle()`r-ben futtassa a parancsot. Linux rendszeren kapcsolódjon a X2Go, indítson el egy terminált, futtassa az R parancsot, majd `rattle()`az r-ben futtassa a parancsot. |
| A minták mutató hivatkozás      | [Rattle](https://togaware.com/onepager/) |
| Kapcsolódó eszközök      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy gyors, elosztott, nagy teljesítményű színátmenet-fokozó (GBDT, GBRT, GBM vagy MART) keretrendszer döntési fa algoritmusok alapján. Ez a rangsorolás, besorolás és sok más gépi tanulási feladat esetében használatos.    |
| Támogatott verziók      | Windows, Linux    |
| Gyakori használati      | Általános célú átmenet – fellendítő keretrendszer      |
| Hogyan azt konfigurálni vagy telepíteni?      | A Windows Python-csomag LightGBM van telepítve. A Linux, a parancssori végrehajtható fájl szerepel `/opt/LightGBM/lightgbm`, az R-csomag telepítve van, és a Python-csomagok települnek.     |
| A minták mutató hivatkozás      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy nyílt forráskódú AI platform, amely támogatja a memóriában, az elosztott, a gyors és a méretezhető gépi tanulást.  |
| Támogatott verziók      | Linux   |
| Gyakori használati      | Általános célú elosztott, méretezhető gépi tanulás   |
| Hogyan azt konfigurálni vagy telepíteni?      | A H2O települ `/dsvm/tools/h2o`.      |
| Használatával, vagy futtassa      | Csatlakozás a virtuális gép X2Go használatával. Indítsa el egy új terminálablakban, majd futtassa `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ezután indítson el egy webböngészőt, és csatlakozhat `http://localhost:54321`.      |
| A minták mutató hivatkozás      | Minták érhetők el a virtuális gépen az Jupyter alatt a `h2o` könyvtár.      |
| Kapcsolódó eszközök      | Az Apache Spark, MXNet, XGBoost, pezsgő vízzel, mély víz    |

A dsvm-on számos más gépi tanulási kódtár található, például a népszerű `scikit-learn` csomag, amely a dsvm-hez készült anaconda Python-eloszlás részét képezi. Tekintse meg a Python, R és Julia elérhető csomagok listájában, futtassa a megfelelő csomagkezelők.
