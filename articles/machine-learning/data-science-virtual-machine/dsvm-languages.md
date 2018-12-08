---
title: A Data Science virtuális gép esetében. a támogatott nyelvek
titleSuffix: Azure
description: Ismerje meg a program nyelveket és a kapcsolódó eszközök, előre telepített a Data Science virtuális gépen.
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 8cc5d1a2d78179624ee1ba17482e9d1892625d6f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104288"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>A Data Science virtuális gépen támogatott nyelvek 

Az adatelemzési virtuális gép (DSVM) tartalmaz számos előre elkészített nyelvek és fejlesztői eszközök, az AI-alkalmazások készítéséhez. Íme néhány, a következő azokat. 

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | 2.7-es és 3.6-os |
| Támogatott DSVM-kiadások      | Windows Server 2016     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Globális két `conda` környezetek jönnek létre. <br /> * `root` környezetben található `/anaconda/` Python 3.6-os verziója van. <br/> * `python2` környezetben található `/anaconda/envs/python2`Python 2.7-es van       |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter-notebookok Python szerepelnek.     |
| A dsvm-hez kapcsolódó eszközök      | PySpark, R, Julia      |

> [!NOTE]
> 2018. március előtt létrehozott Windows Server 2016 tartalmazza a Python 3.5-ös és a Python 2.7-t. Python 2.7-t is a conda **legfelső szintű** környezet és **py35** a Python 3.5-ös környezetben van. 

### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?    

* A parancssor futtatása

Nyissa meg a parancssort, és tegye a következőket futtatni kívánt Python verziójától függően. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Egy IDE használatával

Használható Python Tools for Visual Studio (PTVS) a Visual Studio Community Edition telepítve van. Az egyetlen megengedett környezete telepítés automatikusan projektek készítése PTVS alapértelmezés szerint a Python 3.6-os. 

> [!NOTE]
> Python 2.7-t a PTVS mutassanak, egy egyéni környezet létrehozása a PTVS kell. A környezet elérési útvonalak beállítása a Visual Studio Community Edition kiadást a, navigáljon a **eszközök** -> **Python Tools** -> **Python-környezetek** majd **+ egyéni**. Ezután állítsa be annak helyét `c:\anaconda\envs\python2` majd _automatikus észlelése_. 

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típusként _Python [Conda gyökér]_ a Python 3.6 és _Python [Conda env:python2]_ Python 2.7-es környezetben. 

* Python-csomagok telepítése

A dsvm-hez a Python alapértelmezett környezet összes felhasználó által olvasható globális környezetben. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, a legfelső szintű vagy a python2-környezet használatával aktiválásához a `activate` parancsot rendszergazdaként. Használhatja például a package manager `conda` vagy `pip` telepíteni vagy frissíteni a csomagokat. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (a Linux és Windows Server 2012 kiadás)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | 2.7-es és a 3.5. |
| Támogatott DSVM-kiadások      | Linux, Windows Server 2012    |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Globális két `conda` környezetek jönnek létre. <br /> * `root` környezetben található `/anaconda/` van a Python 2.7-t. <br/> * `py35` környezetben található `/anaconda/envs/py35`Python 3.5       |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter-notebookok Python szerepelnek.     |
| A dsvm-hez kapcsolódó eszközök      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?    

**Linux**
* Fut a terminálon

Nyissa meg a terminált, és tegye a következőket futtatni kívánt Python verziójától függően. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Egy IDE használatával

A Visual Studio Community Edition telepítve PyCharm használja. 

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típusként _Python [Conda gyökér]_ for Python 2.7-es és _Python [Conda env:py35]_ Python 3.5-ös környezetben. 

* Python-csomagok telepítése

A dsvm-hez a Python alapértelmezett környezet összes felhasználó által olvasható globális környezetben. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, a legfelső szintű vagy py35 környezet használatával aktiválásához a `source activate` parancsot rendszergazdaként vagy a sudo engedéllyel rendelkező felhasználó. Használhatja például a Csomagkezelő `conda` vagy `pip` telepíteni vagy frissíteni a csomagokat. 

**Windows 2012**
* A parancssor futtatása

Nyissa meg a parancssort, és tegye a következőket futtatni kívánt Python verziójától függően. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Egy IDE használatával

Használható Python Tools for Visual Studio (PTVS) a Visual Studio Community Edition telepítve van. A csak környezet beállítása automatikusan a PTVS a Python 2.7-t. 
> [!NOTE]
> A projektek készítése PTVS mutassanak Python 3.5, egy egyéni környezet létrehozása a PTVS kell. A környezet elérési útvonalak beállítása a Visual Studio Community Edition kiadást a, navigáljon a **eszközök** -> **Python Tools** -> **Python-környezetek** majd **+ egyéni**. Ezután állítsa be annak helyét `c:\anaconda\envs\py35` majd _automatikus észlelése_. 

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típusként _Python [Conda gyökér]_ for Python 2.7-es és _Python [Conda env:py35]_ Python 3.5-ös környezetben. 

* Python-csomagok telepítése

A dsvm-hez a Python alapértelmezett környezet összes felhasználó által olvasható globális környezetben. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, a legfelső szintű vagy py35 környezet használatával aktiválásához a `activate` parancsot rendszergazdaként. Használhatja például a package manager `conda` vagy `pip` telepíteni vagy frissíteni a csomagokat. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | A Microsoft R Open 3.x (100 %-os kompatibilis a CRAN-R<br /> A Microsoft R Server 9.x Developer edition (egy méretezhető nagyvállalati készen áll az R platform)|
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter-notebookok R szerepelnek.     |
| A dsvm-hez kapcsolódó eszközök      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?    

**Windows**:

* A parancssor futtatása

Nyissa meg a parancssort, és csak írja be `R`.

* Egy IDE használatával

Használat RTools a Visual Studio (RTVS) a Visual Studio Community edition vagy az RStudio telepítése. Ezek érhetők el a start menüben, vagy egy asztali ikonra. 

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típusként _R_ Jupyter R kernel (IRKernel) használatára. 

* R-csomagok telepítése

Az R telepítve van a dsvm-hez egy globális környezetben olvasható összes felhasználó által. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, futtassa a fenti módszerek egyikével R. Ezután futtassa az R Csomagkezelő `install.packages()` telepíteni vagy frissíteni a csomagokat. 

**Linux**:

* Fut a terminálon

Nyissa meg a Terminálszolgáltatások és a csak futtatási `R`.  

* Egy IDE használatával

A Linux-DSVM telepítve RStudio használja.  

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típusként _R_ Jupyter R kernel (IRKernel) használatára. 

* R-csomagok telepítése

Az R telepítve van a dsvm-hez egy globális környezetben olvasható összes felhasználó által. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, futtassa a fenti módszerek egyikével R. Ezután futtassa az R Csomagkezelő `install.packages()` telepíteni vagy frissíteni a csomagokat. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | a 0.6-os |
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Windows: Telepített `C:\JuliaPro-VERSION`<br /> Linux: Telepített `/opt/JuliaPro-VERSION`    |
| A minták mutató hivatkozások      | Mintául szolgáló Jupyter-notebookok Julia szerepelnek.     |
| A dsvm-hez kapcsolódó eszközök      | Python, R      |
### <a name="how-to-use--run-it"></a>Hogyan futtathatja / használata?    

**Windows**:

* A parancssor futtatása

Nyissa meg a parancssort, és futtassa az `julia`. 
* Egy IDE használatával

Használat `Juno` telepíthető a DSVM- és asztali parancsikonja elérhető az Julia IDE.

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típus szerint `Julia VERSION` 

* Julia csomagok telepítése

Julia hely alapértelmezés szerint egy globális környezetben olvasható összes felhasználó. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, futtassa a fenti módszerek egyikével Julia. Ezután futtassa a Julia package manager parancsok például `Pkg.add()` telepíteni vagy frissíteni a csomagokat. 


**Linux**:
* Fut a terminálon.

Nyissa meg a Terminálszolgáltatások és a csak futtatási `julia`. 
* Egy IDE használatával

Használat `Juno` telepíthető a dsvm-hez és az alkalmazás menü parancsikonja elérhető az Julia IDE.

* A Jupyter használatával

Nyissa meg a Jupyter és a kattintson a `New` gombra az új notebook létrehozásához. Ezen a ponton kiválaszthatja a kernel típus szerint `Julia VERSION` 

* Julia csomagok telepítése

Julia hely alapértelmezés szerint egy globális környezetben olvasható összes felhasználó. Azonban csak a rendszergazdák is írási és globális csomagok telepítéséhez. Annak érdekében, hogy telepítse a csomagot a globális környezetet, futtassa a fenti módszerek egyikével Julia. Ezután futtassa a Julia package manager parancsok például `Pkg.add()` telepíteni vagy frissíteni a csomagokat. 

## <a name="other-languages"></a>Más nyelveken

**C#**: Elérhető a Windows és a Visual Studio Community edition keresztül vagy a elérhető egy `Developer Command Prompt for Visual Studio` ahol ugyanúgy futtathatja `csc` parancsot. 

**Java**: openjdk csomagját érhető el a dsvm-hez és a készlet az elérési utat a Linux és a Windows kiadását. Beírhatja `javac` vagy `java` parancsot a Windows parancssorból vagy a Java használata Linux bash felületet. 

**NODE.js**: node.js érhető el a dsvm-hez és a készlet az elérési utat a Linux és a Windows kiadását. Beírhatja `node` vagy `npm` parancsot a parancssorban a Windows vagy Linux node.js eléréséhez a bash felületet. Windows a Node.js-eszközök Visual Studio-bővítmény biztosít egy grafikus IDE való fejlesztés a node.js-alkalmazás telepítve van. 

**F#**: Elérhető a Windows és a Visual Studio Community edition keresztül vagy a elérhető egy `Developer Command Prompt for Visual Studio` ahol ugyanúgy futtathatja `fsc` parancsot. 


