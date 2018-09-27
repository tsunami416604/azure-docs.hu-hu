---
title: Gépi tanulási és adatelemzési eszközök – Azure |} A Microsoft Docs
description: Machine learning és adatelemzési eszközök
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9a9dc868c4f22f95ca5027e3c95513d176c69eac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392399"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine learning és adatelemzési eszközök
Az adatelemzési virtuális gép (DSVM) rendelkezik egy gazdag eszköz és könyvtár a machine Learning R, Python, Julia népszerű nyelveken érhető el. 

Íme néhány, a gépi tanulási eszközökkel és -kódtárak a a dsvm-hez. 

## <a name="azure-machine-learning-servicehttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Az Azure Machine Learning szolgáltatás](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Az Azure Machine Learning szolgáltatás egy felhőszolgáltatás, amely segítségével fejleszthet és helyezhet üzembe a machine learning-modellek.  A modellek hozhat létre, betanítását, méretezése és kezelheti azokat a Python SDK használatával követheti nyomon. Tárolókként modellek üzembe helyezése, és futtathatók a felhőben, rendszert vagy az IoT Edge-ben.   |
| Támogatott DSVM-kiadások     | Windows (Conda-környezet: AzureML), Linux (Conda-környezet: py36)    |
| Gyakori használati      | Általános gépi Tanulási Platform      |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      |  GPU-támogatással rendelkező telepítve   |
| Hogyan futtathatja / használata?      | Python SDK-t, valamint az Azure parancssori eszközét (AZ parancssori felület). Aktiválja conda-környezethez `AzureML` Windows rendszeren, vagy `py36` Linux rendszeren.      |
| A minták mutató hivatkozások      | A minták Jupyter notebookok szerepelnek a `AzureML` könyvtár alatt notebookok  |
| A dsvm-hez kapcsolódó eszközök      | A Visual Studio Code-ban Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |    Gyors, hordozható, és elosztott átmenetes kiemelési (GBDT, GBRT vagy GBM) könyvtárában, Python, R, Java, Scala, C++ és egyéb. Egyetlen gép, Hadoop, Spark fut.    |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási kódtár      |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      |  GPU-támogatással rendelkező telepítve   |
| Hogyan futtathatja / használata?      | Python-kódtár (2.7-es és a 3.5-ös), R-csomag és a parancssori eszköz elérési útját (`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows, a `/dsvm/tools/xgboost/xgboost` Linux rendszeren)    |
| A minták mutató hivatkozások      | A minták szerepelnek a virtuális gépen, `/dsvm/tools/xgboost/demo` Linux rendszeren és `C:\dsvm\tools\xgboost\demo` a Windows   |
| A dsvm-hez kapcsolódó eszközök      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Vowpal Wabbit (más néven "VW") egy nyílt forráskódú gyors out-az-core tanulási rendszerkönyvtár    |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási kódtár      |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      |  Windows - msi-telepítőt, a Linux - apt-get paranccsal |
| Hogyan futtathatja / használata?      | Elérési út a parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows, a `/usr/bin/vw` Linux rendszeren)    |
| A minták mutató hivatkozások      | [VowPal Wabbit minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Weka adatbányászati feladatokhoz gépi tanulási algoritmusok gyűjteménye. Az algoritmusok közvetlenül egy adatkészlet a alkalmazni, vagy saját Java kódot meghívva. Weka előtti adatfeldolgozás, besorolás, regressziós, fürtözés, társítási szabályok és megjelenítési eszközöket tartalmaz. |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási eszköz     |
| Hogyan futtathatja / használata?      | A Windows, a Start menü Weka keresése. A Linux-alapú bejelentkezés az X2Go, majd nyissa meg alkalmazások -> fejlesztés Weka ->. |
| A minták mutató hivatkozások      | [Weka minták](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, Rattle, XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Egy grafikus felhasználói felületet Data Mining az R használata   |
| Támogatott DSVM-kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felület Data szintű adatbányászatra eszköz az R    |
| Hogyan futtathatja / használata?      | UI-eszköz. A Windows, indítson el egy parancssort, és futtassa az R, majd futtassa az R belül `rattle()`. Linux rendszeren X2Go összekapcsolása, indítsa el a parancsot egy terminálban, R, majd futtassa az R belül futtassa `rattle()`. |
| A minták mutató hivatkozások      | [Rattle](https://togaware.com/onepager/) |
| A dsvm-hez kapcsolódó eszközök      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Mi ez?   | Gyorsított döntési fa típusú algoritmusokkal, ennek a területnek, besorolás, illetve számos más gépi tanulási feladatok alapján (GBDT, GBRT, GBM vagy ADATKÖZPONT) keretrendszer gyors, elosztott, nagy teljesítményű átmenetekhez.    |
| A DSVM támogatott verziói      | Windows, Linux    |
| Gyakori használati      | Általános célú átmenetes kiemelési keretrendszer      |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      | A Windows Python-csomag LightGBM van telepítve. A Linux, a parancssori végrehajtható fájl szerepel `/opt/LightGBM/lightgbm`, az R-csomag telepítve van, és a Python-csomagok települnek.     |
| A minták mutató hivatkozások      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| A dsvm-hez kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy memórián belüli, elosztott, gyors és skálázható gépi tanulási támogató nyílt forráskódú AI-platform  |
| A DSVM támogatott verziói      | Linux   |
| Gyakori használati      | Általános célú elosztott, skálázható gépi tanulás   |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?      | A H2O települ `/dsvm/tools/h2o`.      |
| Hogyan futtathatja / használata?      | Csatlakozzon a virtuális Géphez X2Go használatával. Indítsa el egy új terminálablakban, majd futtassa `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ezután indítson el egy webböngészőt, és csatlakozhat `http://localhost:54321`.      |
| A minták mutató hivatkozások      | Minták érhetők el a virtuális gépen az alatt Jupyter `h2o` könyvtár.      |
| A dsvm-hez kapcsolódó eszközök      | Az Apache Spark, MXNet, XGBoost, pezsgő vízzel, mély víz    |

A dsvm-hez hasonlóan a népszerű találhatók számos más gépi Tanulási függvénytárak `scikit-learn` csomagot, amely a dsvm-hez a telepített Anaconda Python terjesztési része. Győződjön meg arról, tekintse át a Python, R és Julia elérhető csomagok listájában a megfelelő csomagkezelők futtatásával. 
