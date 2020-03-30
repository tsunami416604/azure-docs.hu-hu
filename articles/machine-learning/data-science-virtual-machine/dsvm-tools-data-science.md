---
title: Gépi tanulási és adatelemzési eszközök
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg a gépi tanulási eszközök és keretrendszerek, amelyek előre telepítve vannak az adatelemzési virtuális gép.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282307"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Gépi tanulási és adatelemzési eszközök az Azure Data Science virtuális gépeken
Az Azure Data Science virtuális gépek (DSVM-ek) számos eszköz- és kódtárakat tartalmaznak a gépi tanuláshoz, amelyek népszerű nyelveken érhetők el, például a Python, az R és a Julia.

Íme néhány gépi tanulási eszközök és könyvtárak DSVMs.

## <a name="azure-machine-learning-sdk-for-python"></a>Pythonhoz készült Azure Machine Learning SDK

Tekintse meg az [Azure Machine Learning SDK python-hoz](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)teljes körű hivatkozását.

|    |           |
| ------------- | ------------- |
| Mi ez?   |   Az Azure Machine Learning egy felhőalapú szolgáltatás, amely segítségével gépi tanulási modelleket fejleszthet és helyezhet üzembe. Nyomon követheti a modelleket, ahogy épít, betanítása, méretezése, és kezelheti őket a Python SDK használatával. Modellek tárolóként való üzembe helyezése és futtatása a felhőben, a helyszínen vagy az Azure IoT Edge-en.   |
| Támogatott kiadások     | Windows (conda környezet: AzureML), Linux (conda környezet: py36)    |
| Tipikus felhasználások      | Általános gépi tanulási platform      |
| Hogyan van konfigurálva vagy telepítve?      |  GPU-támogatással telepítve   |
| Hogyan kell használni vagy futtatni      | Python SDK-ként és az Azure CLI-ben. Aktiválja a conda környezetben `AzureML` a Windows kiadás *vagy* `py36` a Linux kiadás.      |
| Hivatkozás mintákra      | Minta Jupyter notebookok `AzureML` szerepelnek a könyvtárban a notebookok alatt.  |
| Kapcsolódó eszközök      | Visual Studio kód, Jupyter   |

## <a name="h2o"></a>H2o

|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú AI platform, amely támogatja a memóriában, elosztott, gyors és méretezhető gépi tanulást.  |
| Támogatott verziók      | Linux   |
| Tipikus felhasználások      | Általános célú elosztott, méretezhető gépi tanulás   |
| Hogyan van konfigurálva vagy telepítve?      | H2O telepítve `/dsvm/tools/h2o`van a.      |
| Hogyan kell használni vagy futtatni      | Csatlakozzon a virtuális géphez az X2Go használatával. Indítson el egy új `java -jar /dsvm/tools/h2o/current/h2o.jar`terminált, és futtassa a futtassa a Ezután indítsa el a `http://localhost:54321`webböngészőt, és csatlakozzon a alkalmazáshoz.      |
| Hivatkozás mintákra      | Minták érhetők el a virtuális gép Jupyter a `h2o` könyvtár alatt.      |
| Kapcsolódó eszközök      | Apache Spark, MXNet, XGBoost, Pezsgő, Mélyvíz    |

Számos más gépi tanulási kódtárak DSVMs, `scikit-learn` például a népszerű csomag, amely része az Anaconda Python terjesztési dsvms. A Pythonban, R-ben és Juliában elérhető csomagok listájának ellenőrzéséhez futtassa a megfelelő csomagkezelőket.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Mi ez?   | Gyors, elosztott, nagy teljesítményű színátmenet-kiemelés (GBDT, GBRT, GBM vagy MART) keretrendszer döntési fa algoritmusok alapján. Rangsoroláshoz, besoroláshoz és sok más gépi tanulási feladathoz használják.    |
| Támogatott verziók      | Windows, Linux    |
| Tipikus felhasználások      | Általános célú gradiens-fokozó keretrendszer      |
| Hogyan van konfigurálva vagy telepítve?      | Windows rendszerben a LightGBM Python-csomagként van telepítve. Linuxon a parancssori végrehajtható `/opt/LightGBM/lightgbm`fájl a alkalmazásban található, az R csomag telepítve van, a Python-csomagok pedig telepítve vannak.     |
| Hivatkozás mintákra      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Grafikus felhasználói felület az R használatával történő adatbányászathoz.   |
| Támogatott kiadások     | Windows, Linux     |
| Tipikus felhasználások      | Általános felhasználói felületi adatbányászati eszköz R-hez    |
| Hogyan kell használni vagy futtatni      | Felhasználói felület eszközként. Windows rendszerben indítsa el a parancssort, futtassa `rattle()`az R, majd az R, fuss . Linuxon, csatlakozzon az X2Go-hoz, indítson el egy `rattle()`terminált, futtassa az R-t, majd az R-en belül, fusson . |
| Hivatkozás mintákra      | [Rattle](https://togaware.com/onepager/) |
| Kapcsolódó eszközök      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, nyílt forráskódú, alapvetően kívüli tanulási rendszerkönyvtár    |
| Támogatott kiadások     | Windows, Linux     |
| Tipikus felhasználások      | Általános gépi tanulási könyvtár      |
| Hogyan van konfigurálva vagy telepítve?      |  Windows: msi telepítő<br/>Linux: apt-get |
| Hogyan kell használni vagy futtatni      | Mint egy on-path parancssori eszköz `/usr/bin/vw` (Windows,`C:\Program Files\VowpalWabbit\vw.exe` Linux)    |
| Hivatkozás mintákra      | [VowPal Wabbit minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Gépi tanulási algoritmusok gyűjteménye adatbányászati feladatokhoz. Az algoritmusok közvetlenül alkalmazhatók egy adatkészletre, vagy meghívhatók a saját Java-kódjából. A Weka az adatok előfeldolgozásához, besorolásához, regressziós, fürtözési, társítási szabályokhoz és vizualizációhoz szükséges eszközöket tartalmaz. |
| Támogatott kiadások     | Windows, Linux     |
| Tipikus felhasználások      | Általános gépi tanulási eszköz     |
| Hogyan kell használni vagy futtatni      | Windows rendszerben keresse meg a Weka kifejezést a **Start** menüben. Linuxon jelentkezzen be az X2Go segítségével, majd lépjen az **Applications** > **Development** > **Weka**-ra. |
| Hivatkozás mintákra      | [Weka minták](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Kapcsolódó eszközök      |LightGBM, Csörgő, XGBoost   |

## <a name="xgboost"></a>Xgboost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Gyors, hordozható és elosztott színátmenet-kiemelés (GBDT, GBRT vagy GBM) python, R, Java, Scala, C++ és egyebek. Egyetlen gépen, valamint Apache Hadoop on és Spark-on fut.    |
| Támogatott kiadások     | Windows, Linux     |
| Tipikus felhasználások      | Általános gépi tanulási könyvtár      |
| Hogyan van konfigurálva vagy telepítve?      |  GPU-támogatással telepítve   |
| Hogyan kell használni vagy futtatni      | Python-könyvtárként (2.7 és 3.5), R csomagként és elérési`C:\dsvm\tools\xgboost\bin\xgboost.exe` úton `/dsvm/tools/xgboost/xgboost` lévő parancssori eszközként (Windows és Linux esetén)    |
| A mintákra mutató hivatkozások      | Minták szerepelnek a virtuális `/dsvm/tools/xgboost/demo` gép, a `C:\dsvm\tools\xgboost\demo` Linux és a Windows.   |
| Kapcsolódó eszközök      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Mi ez?   | Nyílt forráskódú SQL-lekérdezési motor big data-adatokon    |
| Támogatott DSVM-verziók      | Windows 2019, Linux  |
| Hogyan van beállítva és telepítve a DSVM?      |  `/dsvm/tools/drill*` Csak beágyazott módban telepítve   |
| Tipikus felhasználások      |  A helyben lévő adatok feltárása nélkül kivonat, átalakítás, terhelés (ETL). Különböző adatforrások és formátumok lekérdezése, beleértve a CSV-t, a JSON-t, a relációs táblákat és a Hadoop-ot.     |
| Hogyan kell használni és futtatni      | Asztal parancsikonja  <br/> [A Drill 10 perc alatt elkezdődik](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Kapcsolódó eszközök a DSVM      |   Csörgő, Weka, SQL Server Management Studio      |


