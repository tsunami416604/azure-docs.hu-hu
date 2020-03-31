---
title: Támogatott nyelvek
titleSuffix: Azure Data Science Virtual Machine
description: A támogatott program nyelvek és a kapcsolódó eszközök előre telepített adatelemzési virtuális gép.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283653"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Az adatelemzési virtuális gépen támogatott nyelvek 

Az adatelemzési virtuális gép (DSVM) számos előre elkészített nyelvet és fejlesztői eszközt tartalmaz a mesterséges intelligencia (AI) alkalmazások létrehozásához. Íme néhány a figyelemre méltó is.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016-os kiadás)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Python 2.7 és 3.7 |
| Támogatott DSVM-kiadások      | Windows Server 2016     |
| Hogyan van beállítva / telepítve van a DSVM?  | Két `conda` globális környezet jön létre: <br /> * `root` A környezet `/anaconda/` található Python 3.7. <br/> * `python2` A környezet `/anaconda/envs/python2` található Python 2.7.       |
| A mintákra mutató hivatkozások      | Minta Jupyter notebookok Python tartalmazza.     |
| Kapcsolódó eszközök a DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> A 2018 márciusa előtt létrehozott Windows Server 2016-buildek python 3.5-ös és Python 2.7-es környezetet tartalmaznak. A Python 2.7 a conda **gyökérkörnyezet,** a **py37** pedig a Python 3.7-es környezet.

### <a name="how-to-use-and-run-it"></a>Hogyan kell használni és futtatni    

* Futtatás parancssorból:

  Nyisson meg egy parancssort, és használja az alábbi módszerek egyikét, a futtatni kívánt Python-verziótól függően:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* IDE-ben való használat:

  Használja a Visual Studio (PTVS) Python Tools for Visual Studio (PTVS) alkalmazást, amely a Visual Studio Community kiadásában van telepítve. Alapértelmezés szerint a PTVS-ben automatikusan beállított egyetlen környezet a Python 3.6. 

    > [!NOTE]
    > A PTVS-t a Python 2.7-en való ponthoz létre kell hoznia egy egyéni környezetet a PTVS-ben. Ha ezt a környezeti útvonalat a Visual Studio Community Edition programban szeretné beállítani, nyissa meg a **Tools** -> **Python Tools** -> **Python-környezetek** lehetőséget, és válassza a **+ Egyéni**lehetőséget. Ezután állítsa a helyet **c:\anaconda\envs\python2** értékre, és válassza az **Automatikus észlelés**lehetőséget.

* Használja a Jupyter:

  Új jegyzetfüzet létrehozásához nyissa meg a Jupyter t, és válassza az **Új** lehetőséget. A kernel típusát _Python [Conda Root]_ néven állíthatja be a Python 3.7-hez és _python [Conda env:python2]_ python-ként a Python 2.7-hez.

* Python-csomagok telepítése:

  A DSVM alapértelmezett Python-környezetei olyan globális környezetek, amelyek et minden felhasználó számára olvashatók. De csak a rendszergazdák írhatnak és telepíthetnek globális csomagokat. Csomagok telepítése a globális környezetben, aktiválja a root vagy `activate` python2 környezetben a parancs rendszergazdaként. Ezt követően használhatja a `conda` `pip` csomagkezelőt a csomagok kedveléséhez, illetve telepítéséhez vagy frissítéséhez.

## <a name="python-linux-edition"></a>Python (Linux kiadás)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Python 2.7 és 3.5 |
| Támogatott DSVM-kiadások      | Linux   |
| Hogyan van beállítva / telepítve van a DSVM?  | Két `conda` globális környezet jön létre: <br /> * `root`python 2.7-es környezetben `/anaconda/` található. <br/> * `py35`python 3.5-ös környezetben `/anaconda/envs/py35`található.       |
| A mintákra mutató hivatkozások      | Minta Jupyter notebookok Python tartalmazza.     |
| Kapcsolódó eszközök a DSVM      | Pyspark, R, Júlia      |
### <a name="how-to-use-and-run-it"></a>Hogyan kell használni és futtatni    

* Terminálon való futtatás:

  Nyissa meg a terminált, és a futtatni kívánt Python-verziótól függően tegye a következők egyikét:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* IDE-ben való használat:

  Használja a Visual Studio Community kiadásban telepített PyCharm programot. 

* Használja a Jupyter:

  Új jegyzetfüzet létrehozásához nyissa meg a Jupyter t, és válassza az **Új** lehetőséget. A Kernel típusát **Python [Conda Root]** néven állíthatja be a Python 2.7-hez és a **Python [Conda env:py35]** a Python 3.5 környezetben. 

* Python-csomagok telepítése:

  A DSVM alapértelmezett Python-környezetei az összes felhasználó által olvasható globális környezetek. De csak a rendszergazdák írhatnak és telepíthetnek globális csomagokat. Csomagok globális környezetbe történő telepítéséhez aktiválja a gyökér- `source activate` vagy py35-ös környezetet rendszergazdaként vagy sudo-engedélyekkel rendelkező felhasználóként. Ezt követően használhatja a `conda` `pip` csomagkezelőt a csomagok kedveléséhez, illetve telepítéséhez vagy frissítéséhez.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Microsoft R Open 3.x (100%-ban kompatibilis a CRAN-R-rel)<br /> Microsoft R Server 9.x Developer edition (méretezhető, nagyvállalati használatra kész R platform)|
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan van beállítva / telepítve van a DSVM?  | Windows:`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux:`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| A mintákra mutató hivatkozások      | Minta Jupyter notebookok R tartalmazza.     |
| Kapcsolódó eszközök a DSVM      | Sparkr, Python, Júlia      |
### <a name="how-to-use-and-run-it"></a>Hogyan kell használni és futtatni    

**Windows**:

* Futtatás parancssorból:

  Nyisson meg egy parancssort, és írja be a következőt: `R`.

* IDE-ben való használat:

  Használja a Visual Studio (RTVS) programba telepített RTools for Visual Studio (RTVS) alkalmazást. Ezek a Start menüben vagy asztali ikonként érhetők el. 

* Használata jupyter

  Új jegyzetfüzet létrehozásához nyissa meg a Jupyter t, és válassza az **Új** lehetőséget. A rendszermag típusát **R-ként** állíthatja be a Jupyter R kernel (IRKernel) használatára.

* R csomagok telepítése:

  Az R a DSVM-re olyan globális környezetben van telepítve, amely minden felhasználó számára olvasható. De csak a rendszergazdák írhatnak és telepíthetnek globális csomagokat. Csomagok globális környezetbe történő telepítéséhez futtassa az R futtatást az előző módszerek egyikével. Ezután futtathatja az R `install.packages()` csomagkezelőt a csomagok telepítéséhez vagy frissítéséhez.

**Linux**:

* Futtatás a terminálon:

  Nyisson meg `R`egy terminált, és futtassa a futtassa a .  

* IDE-ben való használat:

  Használja a Linux DSVM-re telepített RStudio-t.  

* Használja a Jupyter:

  Új jegyzetfüzet létrehozásához nyissa meg a Jupyter t, és válassza az **Új** lehetőséget. A rendszermag típusát **R-ként** állíthatja be a Jupyter R kernel (IRKernel) használatára. 

* R csomagok telepítése:

  Az R a DSVM-re olyan globális környezetben van telepítve, amely minden felhasználó számára olvasható. De csak a rendszergazdák írhatnak és telepíthetnek globális csomagokat. Csomagok globális környezetbe történő telepítéséhez futtassa az R futtatást az előző módszerek egyikével. Ezután futtathatja az R `install.packages()` csomagkezelőt a csomagok telepítéséhez vagy frissítéséhez.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | 0,6 |
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan van beállítva / telepítve van a DSVM?  | Windows: Telepítve:`C:\JuliaPro-VERSION`<br /> Linux: Telepítve:`/opt/JuliaPro-VERSION`    |
| A mintákra mutató hivatkozások      | Minta Jupyter notebookok Julia tartalmazza.     |
| Kapcsolódó eszközök a DSVM      | Piton, R      |
### <a name="how-to-use-and-run-it"></a>Hogyan kell használni és futtatni    

**Windows**:

* Futtatás parancssorban

  Nyisson meg egy `julia`parancssort, és futtassa a parancsot.
* IDE-ben való használat:

  Használja `Juno` a DSVM-re telepített Julia IDE-vel, és asztali parancsikonként érhető el.

* Használja a Jupyter:

  Új jegyzetfüzet létrehozásához nyissa meg a Jupyter t, és válassza az **Új** lehetőséget. A kernel típusát **Júlia VERSION**néven állíthatja be.

* Julia csomagok telepítése:

  Az alapértelmezett Júlia hely egy globális környezet, amely minden felhasználó számára olvasható. De csak a rendszergazdák írhatnak és telepíthetnek globális csomagokat. Csomagok globális környezetbe történő telepítéséhez futtassa a Juliát az előző módszerek egyikével. Ezután futtathatja a Julia `Pkg.add()` csomagkezelő parancsait, például a csomagok telepítését vagy frissítését.


**Linux**:
* Terminálon való futtatás:

  Nyisson meg `julia`egy terminált, és futtassa a futtassa a .
* IDE-ben való használat:

  Használja `Juno`a , a Julia IDE telepítve a DSVM és a rendelkezésre álló **alkalmazás** menü parancsikont.

* Használja a Jupyter:

  Új jegyzetfüzet létrehozásához nyissa meg a Jupyter t, és válassza az **Új** lehetőséget. A kernel típusát **Júlia VERSION**néven állíthatja be.

* Julia csomagok telepítése:

  Az alapértelmezett Júlia hely egy globális környezet, amely minden felhasználó számára olvasható. De csak a rendszergazdák írhatnak és telepíthetnek globális csomagokat. Csomagok globális környezetbe történő telepítéséhez futtassa a Juliát az előző módszerek egyikével. Ezután futtathatja a Julia `Pkg.add()` csomagkezelő parancsait, például a csomagok telepítését vagy frissítését.

## <a name="other-languages"></a>Egyéb nyelvek

**C#**: Elérhető A Windows és elérhető a `Developer Command Prompt for Visual Studio`Visual Studio Közösségi `csc` kiadás, vagy a, ahol futtathatja a parancsot.

**Java**: OpenJDK elérhető mind a Linux és a Windows kiadása a DSVM, és be van állítva az útvonalon. A Java használatához `javac` írja `java` be a parancsot egy parancssorba a Windows rendszerben vagy a bash rendszerhéjon Linux alatt.

**Node.js**: Node.js érhető el mind a Linux és a Windows kiadása a DSVM, és be van állítva az útvonalon. A Node.js eléréséhez `node` `npm` írja be a parancsot egy parancssorba a Windows rendszerben vagy a Bash shell linuxos rendszerben. Windows rendszeren a Node.js eszközök Visual Studio bővítménye telepítve van, hogy grafikus IDE-t biztosítson a Node.js alkalmazás fejlesztéséhez.

**F#**: Elérhető A Windows és elérhető a `Developer Command Prompt for Visual Studio`Visual Studio Közösségi `fsc` kiadás, vagy egy, ahol futtathatja a parancsot.
