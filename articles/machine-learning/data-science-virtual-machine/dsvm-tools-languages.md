---
title: Támogatott nyelvek
titleSuffix: Azure Data Science Virtual Machine
description: A Data Science Virtual Machineon előre telepített támogatott programok és kapcsolódó eszközök.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615423"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>A Data Science Virtual Machine támogatott nyelvek 

A Data Science Virtual Machine (DSVM) számos előre elkészített nyelvet és fejlesztői eszközt tartalmaz a mesterséges intelligencia (AI) alkalmazások kiépítéséhez. Íme néhány a jelentősek közül.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 kiadás)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Python 2,7 és 3,7 |
| Támogatott DSVM-kiadások      | Windows Server 2016     |
| Hogyan lehet konfigurálni / telepíteni a DSVM-hez?  | Két globális `conda` környezet jön létre: <br /> * A `root` környezet `/anaconda/` a Python 3,7. <br/> * A `python2` környezet `/anaconda/envs/python2` a Python 2,7.       |
| Mintákra mutató hivatkozások      | A Pythonhoz készült minta Jupyter-jegyzetfüzetek is elérhetők.     |
| A DSVM kapcsolódó eszközök      | PySpark, R, Julia.      |

> [!NOTE]
> A 2018 márciusa előtt létrehozott Windows Server 2016-buildek Python 3,5 és Python 2,7-et tartalmaznak. A Python 2,7 a Conda **legfelső szintű** környezete, a **Py37** pedig a Python 3,7-környezet.

### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

* Futtassa a parancsot a parancssorban:

  Nyisson meg egy parancssort, és használja a következő módszerek egyikét a futtatni kívánt Python-verziótól függően:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Használat az IDE-ben:

  A Visual Studio Community Edition-ben telepített Python Tools for Visual Studio (PTVS) használata. Alapértelmezés szerint az PTVS-ben automatikusan beállított környezet a Python 3,6. 

    > [!NOTE]
    > Ahhoz, hogy a PTVS a Python 2,7-es verzióra mutasson, egyéni környezetet kell létrehoznia a PTVS-ben. A környezeti elérési út a Visual Studio Community kiadásban való beállításához nyissa meg a **tools** -> **python-eszközök** -> **Python-környezetek** elemet, és válassza a **+ Custom**lehetőséget. Ezután állítsa be a **c:\anaconda\envs\python2** helyet, és válassza az **automatikus észlelés**lehetőséget.

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát _Python [Conda root]_ -ként állíthatja be a Python 3,7 és a Python _[Conda env: python2]_ for Python 2,7 számára.

* Python-csomagok telepítése:

  A DSVM lévő alapértelmezett Python-környezetek az összes felhasználó által olvasható globális környezetek. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, aktiválja a-t a root vagy a python2 környezetre a `activate` parancs rendszergazdaként való használatával. Ezután használhat egy csomagkezelő eszközt, például `conda` vagy `pip` a csomagok telepítéséhez vagy frissítéséhez.

## <a name="python-linux-edition"></a>Python (Linux kiadás)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Python 2,7 és 3,5 |
| Támogatott DSVM-kiadások      | Linux   |
| Hogyan lehet konfigurálni / telepíteni a DSVM-hez?  | Két globális `conda` környezet jön létre: <br /> * `root` környezet `/anaconda/` a Python 2,7. <br/> * `py35` környezet `/anaconda/envs/py35`a Python 3,5.       |
| Mintákra mutató hivatkozások      | A Pythonhoz készült minta Jupyter-jegyzetfüzetek is elérhetők.     |
| A DSVM kapcsolódó eszközök      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

* Futtatás terminálon:

  Nyissa meg a terminált, és hajtsa végre az alábbi műveletek egyikét a futtatni kívánt Python-verziótól függően:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Használat az IDE-ben:

  A Visual Studio Community Edition kiadásban telepített Notebookshoz használata. 

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát **Python [Conda root]** -ként állíthatja be Python 2,7 és **Python [Conda env: py35]** értékre a Python 3,5 környezetben. 

* Python-csomagok telepítése:

  A DSVM lévő alapértelmezett Python-környezetek az összes felhasználó által olvasható globális környezetek. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, aktiváljon a root vagy a py35 környezetben a `source activate` parancs használatával rendszergazdaként vagy sudo-engedélyekkel rendelkező felhasználóként. Ezután használhat egy csomagkezelő eszközt, például `conda` vagy `pip` a csomagok telepítéséhez vagy frissítéséhez.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Microsoft R Open 3. x (100%-kompatibilis a CRAN-R-vel)<br /> Microsoft R Server 9. x Developer Edition (skálázható, nagyvállalati használatra kész R platform)|
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan lehet konfigurálni / telepíteni a DSVM-hez?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Mintákra mutató hivatkozások      | Az R-hez készült minta Jupyter-jegyzetfüzeteket tartalmazza.     |
| A DSVM kapcsolódó eszközök      | Sparker, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

**Windows**:

* Futtassa a parancsot a parancssorban:

  Nyisson meg egy parancssort, és írja be a következőt: `R`.

* Használat az IDE-ben:

  A Visual Studio Community Edition vagy a RStudio alkalmazásban telepített RTools for Visual Studio (RTVS) használata. Ezek a Start menüben vagy asztali ikonként érhetők el. 

* Használat a Jupyter

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A Jupyter R kernel (IRKernel) használatához a rendszermag típusát **r** értékre állíthatja.

* R-csomagok telepítése:

  Az R az összes felhasználó által olvasható globális környezetben van telepítve a DSVM. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa az R-t az előző módszerek egyikének használatával. Ezután futtathatja az R Package Manager `install.packages()`t a csomagok telepítéséhez vagy frissítéséhez.

**Linux**:

* Futtatás a (z) terminálban:

  Nyisson meg egy terminált, és futtassa `R`.  

* Használat az IDE-ben:

  A Linux DSVM telepített RStudio használata.  

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A Jupyter R kernel (IRKernel) használatához a rendszermag típusát **r** értékre állíthatja. 

* R-csomagok telepítése:

  Az R az összes felhasználó által olvasható globális környezetben van telepítve a DSVM. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa az R-t az előző módszerek egyikének használatával. Ezután futtathatja az R Package Manager `install.packages()`t a csomagok telepítéséhez vagy frissítéséhez.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | 0,6 |
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan lehet konfigurálni / telepíteni a DSVM-hez?  | Windows: telepítve `C:\JuliaPro-VERSION`<br /> Linux: telepítve `/opt/JuliaPro-VERSION`    |
| Mintákra mutató hivatkozások      | A Julia-hez készült minta Jupyter-jegyzetfüzetek beletartoznak.     |
| A DSVM kapcsolódó eszközök      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

**Windows**:

* Futtatás parancssorból

  Nyisson meg egy parancssort, és futtassa a `julia`.
* Használat az IDE-ben:

  Használja a `Juno`t a DSVM telepített Julia IDE-vel, és asztali parancsikonként elérhető.

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát **Júlia-verzióként**állíthatja be.

* Julia-csomagok telepítése:

  Az alapértelmezett Julia-hely az összes felhasználó által olvasható globális környezet. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa a Julia-t az előző módszerek egyikének használatával. Ezután a Julia Package Manager-parancsokat futtathatja, például a `Pkg.add()`t a csomagok telepítéséhez vagy frissítéséhez.


**Linux**:
* Futtatás terminálon:

  Nyisson meg egy terminált, és futtassa `julia`.
* Használat az IDE-ben:

  Használja a `Juno`, a DSVM telepített Julia IDE-val, és az **alkalmazás** menü parancsikonként elérhető.

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát **Júlia-verzióként**állíthatja be.

* Julia-csomagok telepítése:

  Az alapértelmezett Julia-hely az összes felhasználó által olvasható globális környezet. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa a Julia-t az előző módszerek egyikének használatával. Ezután a Julia Package Manager-parancsokat futtathatja, például a `Pkg.add()`t a csomagok telepítéséhez vagy frissítéséhez.

## <a name="other-languages"></a>Más nyelveken

**C#** : Elérhető Windows rendszeren, és elérhető a Visual Studio Community Edition vagy a `Developer Command Prompt for Visual Studio`, ahol futtathatja a `csc` parancsot.

**Java**: a OPENJDK a DSVM Linux-és Windows-kiadásaiban érhető el, és az elérési útra van beállítva. A Java használatához írja be a `javac` vagy `java` parancsot a parancssorba a Windowsban vagy a Linux bash-rendszerhéjban.

**Node. js**: a Node. js a DSVM Linux-és Windows-kiadásaiban is elérhető, és az elérési útra van beállítva. A Node. js eléréséhez írja be a `node` vagy `npm` parancsot a parancssorba a Windows vagy a Linux bash-rendszerhéjon. Windows rendszeren a Node. js-eszközökhöz készült Visual Studio-bővítmény telepítve van a Node. js-alkalmazás fejlesztéséhez szükséges grafikus IDE létrehozásához.

**F#** : Elérhető Windows rendszeren, és elérhető a Visual Studio Community Edition vagy egy `Developer Command Prompt for Visual Studio`, ahol futtathatja a `fsc` parancsot.
