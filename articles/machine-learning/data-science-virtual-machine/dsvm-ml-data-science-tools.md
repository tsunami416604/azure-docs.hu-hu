---
title: Gépi tanulási és adatelemzési eszközök – Azure |} A Microsoft Docs
description: Ismerje meg a machine learning-eszközöket és keretrendszereket előre telepítve van a Data Science virtuális gépen.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: d8c607b5f1d338ca3a2bd3844b26ef51d801c720
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086070"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine learning és adatelemzési eszközök
Az Azure Data Science Virtual Machines rendelkezik eszközök széles skáláját, és -kódtárak a machine learning (gépi tanulás) például Python, R és Julia népszerű nyelveken érhető el. 

Íme néhány, a gépi Tanulási eszközök és kódtárak Data Science Virtual Machines gépeken. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Az Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Az Azure Machine Learning egy felhőalapú szolgáltatás, amellyel fejleszthet és helyezhet üzembe gépi Tanulási modelleket. Nyomon követheti a modellek, fejleszthet, betanítását, méretezési és a Python SDK-val kezelheti azokat. Tárolókként modellek üzembe helyezése, és futtathatók a felhőben, a helyszínen, vagy az Azure IoT Edge-ben.   |
| Támogatott kiadások     | Windows (conda-környezet: AzureML), Linux (conda-környezet: py36)    |
| Gyakori használati      | Általános gépi Tanulási platform      |
| Hogyan azt konfigurálni vagy telepíteni?      |  GPU-támogatással rendelkező telepítve   |
| Használatával, vagy futtassa      | A Python SDK-t és az Azure parancssori felület. Aktiválja a conda-környezet `AzureML` Windows Edition *vagy* való `py36` Linux rendszeren.      |
| A minták mutató hivatkozás      | Mintául szolgáló Jupyter notebookok szerepelnek a `AzureML` notebookok a könyvtárban.  |
| Kapcsolódó eszközök      | A Visual Studio Code-ban Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |    XGBoost kiemelési (GBDT, GBRT vagy GBM) könyvtár a Python, R, Java, Scala, C++ és további gyors, hordozható és elosztott színátmenet. Egyetlen gép, Hadoop és Spark futtatja.    |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási kódtár      |
| Hogyan azt konfigurálni vagy telepíteni?      |  GPU-támogatással rendelkező telepítve   |
| Használatával, vagy futtassa      | Python, könyvtár (2.7-es és 3.5-ös), az R-csomaggal, és a parancssori eszköz elérési útját (`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows, a `/dsvm/tools/xgboost/xgboost` Linux)    |
| A minták mutató hivatkozások      | A minták szerepelnek a virtuális gépen, `/dsvm/tools/xgboost/demo` , linuxon és `C:\dsvm\tools\xgboost\demo` a Windows.   |
| Kapcsolódó eszközök      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Vowpal Wabbit (más néven "VW") egy nyílt forráskódú, a gyors, out-az-core rendszerkönyvtár tanulási.    |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási kódtár      |
| Hogyan azt konfigurálni vagy telepíteni?      |  Windows – msi-telepítőt, Linux – apt-get paranccsal |
| Használatával, vagy futtassa      | Az elérési út parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows, a `/usr/bin/vw` Linux rendszeren)    |
| A minták mutató hivatkozás      | [VowPal Wabbit minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Weka adatbányászati feladatokhoz gépi Tanulási algoritmusok gyűjteménye. Az algoritmusok kell közvetlenül egy adatkészlet a alkalmazni, vagy saját Java kódot meghívva. Weka előtti adatfeldolgozás, besorolás, regressziós, fürtözés, társítási szabályok és megjelenítési eszközöket tartalmaz. |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános gépi Tanulási eszköz     |
| Használatával, vagy futtassa      | A Windows, Weka keresése a Start menüben. Linuxon, jelentkezzen be X2Go, és folytassa a **alkalmazások** > **fejlesztési** > **Weka**. |
| A minták mutató hivatkozás      | [Weka minták](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Kapcsolódó eszközök      |LightGBM, Rattle, XGBoost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Rattle data szintű adatbányászatra grafikus felhasználói felületen az R.   |
| Támogatott kiadások     | Windows, Linux     |
| Gyakori használati      | Általános felhasználói felület adatbányászati eszköz az R    |
| Használatával, vagy futtassa      | UI-eszköz. A Windows, indítson el egy parancssort, R futtassa, és belül R futtassa `rattle()`. Linuxon, X2Go összekapcsolása, indítsa el a parancsot egy terminálban, R futtassa, és belül R futtassa `rattle()`. |
| A minták mutató hivatkozás      | [Rattle](https://togaware.com/onepager/) |
| Kapcsolódó eszközök      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Mi ez?   | LightGBM gyorsított döntési fa típusú algoritmusok alapján (GBDT, GBRT, GBM vagy ADATKÖZPONT) keretrendszer gyors, elosztott, nagy teljesítményű átmenetes. Ennek a területnek besorolási és sok más gépi Tanulási feladatok használatos.    |
| Támogatott verziók      | Windows, Linux    |
| Gyakori használati      | Általános célú átmenetes kiemelési keretrendszer      |
| Hogyan azt konfigurálni vagy telepíteni?      | A Windows Python-csomag LightGBM van telepítve. A Linux, a parancssori végrehajtható fájl szerepel `/opt/LightGBM/lightgbm`, az R-csomag telepítve van, és a Python-csomagok települnek.     |
| A minták mutató hivatkozás      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Mi ez?   | H2O egy nyílt forráskódú AI-platform, amely támogatja a memórián belüli, elosztott, gyors és skálázható gépi Tanulási.  |
| Támogatott verziók      | Linux   |
| Gyakori használati      | Általános célú elosztott, skálázható gépi tanulás   |
| Hogyan azt konfigurálni vagy telepíteni?      | A H2O települ `/dsvm/tools/h2o`.      |
| Használatával, vagy futtassa      | Csatlakozás a virtuális gép X2Go használatával. Indítsa el egy új terminálablakban, majd futtassa `java -jar /dsvm/tools/h2o/current/h2o.jar`. Ezután indítson el egy webböngészőt, és csatlakozhat `http://localhost:54321`.      |
| A minták mutató hivatkozás      | Minták érhetők el a virtuális gépen az Jupyter alatt a `h2o` könyvtár.      |
| Kapcsolódó eszközök      | Az Apache Spark, MXNet, XGBoost, pezsgő vízzel, mély víz    |

Nincsenek számos más gépi Tanulási függvénytárak Data Science virtuális gépeken, például a népszerű `scikit-learn` csomagot, amely a Data Science virtuális gépeken telepített Anaconda Python elosztási része. Tekintse meg a Python, R és Julia elérhető csomagok listájában, futtassa a megfelelő csomagkezelők.
