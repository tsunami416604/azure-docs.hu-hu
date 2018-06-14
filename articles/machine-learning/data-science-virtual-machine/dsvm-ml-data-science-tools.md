---
title: Számítógép-tanulási és adatok tudományos eszközök - Azure |} Microsoft Docs
description: Machine learning és az adatok tudományos eszközök
keywords: adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei
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
ms.openlocfilehash: cf73f792bcce906a673da0c6fb2eec099239c1c2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409550"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine learning és az adatok tudományos eszközök
Az adatok tudományos virtuális gép (DSVM) rendelkezik széles választékának eszközök és a könyvtárak a machine Learning szolgáltatáshoz hasonlóan a Python, R, Ágnes népszerű nyelveken érhető el. 

Az alábbiakban néhány gépi tanulási eszközök és a DSVM könyvtáraiban. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Mi ez?   |    Gyors, hordozható, és elosztott átmenetes kiemelése (GBDT, GBRT vagy GBM) könyvtárba, Python, R, Java, Scala, C++ és több. Egyetlen számítógépen, a Hadoop, Spark fut.    |
| Támogatott DSVM kiadás     | Windows, Linux     |
| A gyakori felhasználási      | Általános ML-könyvtár      |
| Hogyan van azt konfigurált / a DSVM telepítve?      |  GPU-támogatással rendelkező telepítve   |
| Hogyan használja az / futtatni?      | Python kódtár (2.7 és 3.5-ös verzióját), mint R csomagot és a parancssori eszköz elérési útját (`C:\dsvm\tools\xgboost\bin\xgboost.exe` Windows, `/dsvm/tools/xgboost/xgboost` Linux)    |
| Minták mutató hivatkozások      | Minták szerepelnek a virtuális Gépre, `/dsvm/tools/xgboost/demo` Linux rendszeren és `C:\dsvm\tools\xgboost\demo` Windows rendszeren   |
| A DSVM a kapcsolódó eszközök      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Vowpal Wabbit (más néven "VW") egy nyílt forráskódú gyors out-az-core tanulási rendszerkönyvtár    |
| Támogatott DSVM kiadás     | Windows, Linux     |
| A gyakori felhasználási      | Általános ML-könyvtár      |
| Hogyan van azt konfigurált / a DSVM telepítve?      |  Windows - msi telepítő, Linux - apt-get |
| Hogyan használja az / futtatni?      | Elérési út a parancssori eszköz (`C:\Program Files\VowpalWabbit\vw.exe` Windows, a `/usr/bin/vw` Linux)    |
| Minták mutató hivatkozások      | [VowPal Wabbit minták](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| A DSVM a kapcsolódó eszközök      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Mi ez?   |  Weka gépi tanulási algoritmusok adatok adatbányászati tevékenységek gyűjteménye. Az algoritmusok kell közvetlenül a DataSet adatkészlet alkalmazni, vagy saját Java kód hívása. Weka előtti adatfeldolgozási, besorolást, regressziós, fürtözés, társítási szabályok és a képi megjelenítés eszközöket tartalmazza. |
| Támogatott DSVM kiadás     | Windows, Linux     |
| A gyakori felhasználási      | Általános ML eszköz     |
| Hogyan használja az / futtatni?      | A Windowsban, keressen rá a Start menüben Weka. Linux, jelentkezzen be X2Go, majd keresse meg az alkalmazások fejlesztése -> -> Weka. |
| Minták mutató hivatkozások      | [Weka minták](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| A DSVM a kapcsolódó eszközök      |LightGBM, Rattle, XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Mi ez?   |   Az adatbányászati az R nyelv használatát a grafikus felhasználói felület   |
| Támogatott DSVM kiadás     | Windows, Linux     |
| A gyakori felhasználási      | Az R általános UI-adatbányászat eszköz    |
| Hogyan használja az / futtatni?      | Felhasználói felületi eszköz. A Windows, egy parancssor elindításához, futtassa az R, majd futtassa R belül `rattle()`. Linux, X2Go kapcsolattartásnak, indítsa el a Terminálszolgáltatások, futtassa az R, majd futtassa R belül `rattle()`. |
| Minták mutató hivatkozások      | [Rattle](https://togaware.com/onepager/) |
| A DSVM a kapcsolódó eszközök      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Mi ez?   | A döntési fa algoritmusokkal, rangsorolási besorolási és sok más gép tanulási feladatok alapján (GBDT, GBRT, GBM vagy ADATKÖZPONT) keretrendszer kiemelése gyors, elosztott, nagy teljesítményű átmenetekhez.    |
| Támogatott DSVM verziók      | Windows, Linux    |
| A gyakori felhasználási      | Általános célú átmenetes kiemelési keretrendszer      |
| Hogyan van azt konfigurált / a DSVM telepítve?      | Windows-LightGBM Python-csomag telepítve van. Linux, a parancssori végrehajtható fájl van `/opt/LightGBM/lightgbm`, az R csomag telepítve van, és telepítve vannak a Python-csomagokat.     |
| Minták mutató hivatkozások      | [LightGBM útmutató](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| A DSVM a kapcsolódó eszközök      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Mi ez?   | Egy nyílt forráskódú AI platform támogatása a memóriában, elosztott, gyors és méretezhető gépi tanulás  |
| Támogatott DSVM verziók      | Linux   |
| A gyakori felhasználási      | Általános célú elosztott, méretezhető gépi tanulás   |
| Hogyan van azt konfigurált / a DSVM telepítve?      | H2O telepítve van-e `/dsvm/tools/h2o`.      |
| Hogyan használja az / futtatni?      | Csatlakoztassa a virtuális Gépet X2Go használatával. Egy új terminál elindításához és `java -jar /dsvm/tools/h2o/current/h2o.jar`. Majd indítsa el a webböngészőt, és csatlakozzon `http://localhost:54321`.      |
| Minták mutató hivatkozások      | Minták érhetők el a virtuális gép a Jupyter `h2o` könyvtár.      |
| A DSVM a kapcsolódó eszközök      | Apache Spark, MXNet, XGBoost, habzó vízjel, mély vízjel    |

Vannak-e több más ML szalagtárak hasonlóan a népszerű DSVM `scikit-learn` csomagot, amely a DSVM telepített Anaconda Python eloszlását részeként elérhető lesz. Mindenképpen tekintse meg a Python, R és Ágnes csomagok listáját, a megfelelő csomag kezelők futtatásával. 
