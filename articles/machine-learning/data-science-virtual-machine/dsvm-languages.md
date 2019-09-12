---
title: Támogatott nyelvek
titleSuffix: Azure Data Science Virtual Machine
description: A Data Science Virtual Machineon előre telepített támogatott programok és kapcsolódó eszközök.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 0cedc9ede43d18d0b94b8a516170db53e3a27910
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885629"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>A Data Science virtuális gépen támogatott nyelvek 

A Data Science Virtual Machine (DSVM) számos előre elkészített nyelvet és fejlesztői eszközt tartalmaz a mesterséges intelligencia (AI) alkalmazások kiépítéséhez. Íme néhány a jelentősek közül.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 kiadás)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Python 2,7 és 3,6 |
| Támogatott DSVM-kiadások      | Windows Server 2016     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Két globális `conda` környezet jön létre: <br /> * Az `root` a környezet `/anaconda/` , amely a Python 3,6. <br/> * Az `python2` a környezet `/anaconda/envs/python2` , amely a Python 2,7.       |
| A minták mutató hivatkozások      | A Pythonhoz készült minta Jupyter-jegyzetfüzetek is elérhetők.     |
| A DSVM kapcsolódó eszközök      | PySpark, R, Julia.      |

> [!NOTE]
> A 2018 márciusa előtt létrehozott Windows Server 2016-buildek Python 3,5 és Python 2,7-et tartalmaznak. A Python 2,7 a Conda **legfelső szintű** környezete, a **Py35** pedig a Python 3,5-környezet.

### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

* Futtassa a parancsot a parancssorban:

  Nyisson meg egy parancssort, és használja a következő módszerek egyikét a futtatni kívánt Python-verziótól függően:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
    ```
    
* Használat az IDE-ben:

  A Visual Studio Community Edition-ben telepített Python Tools for Visual Studio (PTVS) használata. Alapértelmezés szerint az PTVS-ben automatikusan beállított környezet a Python 3,6. 

    > [!NOTE]
    > Ahhoz, hogy a PTVS a Python 2,7-es verzióra mutasson, egyéni környezetet kell létrehoznia a PTVS-ben. A környezeti útvonal a Visual Studio Community kiadásban történő beállításához nyissa meg az **eszközök** -> **Python-eszközök** -> **Python-környezetek** elemet, és válassza a **+ Egyéni**lehetőséget. Ezután állítsa be a **c:\anaconda\envs\python2** helyet, és válassza az **automatikus észlelés**lehetőséget.

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát _Python [Conda root]_ -ként állíthatja be a Python 3,6 és a Python _[Conda env: python2]_ for Python 2,7 számára.

* Python-csomagok telepítése:

  A DSVM lévő alapértelmezett Python-környezetek az összes felhasználó által olvasható globális környezetek. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, aktiválja a parancsot rendszergazdaként a `activate` root vagy a python2 környezetbe. Ezt követően használhatja a Package Managert (például `conda` vagy `pip` ) a csomagok telepítéséhez vagy frissítéséhez.

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux és Windows Server 2012 Edition)

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Python 2,7 és 3,5 |
| Támogatott DSVM-kiadások      | Linux, Windows Server 2012    |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Két globális `conda` környezet jön létre: <br /> * `root`a-ben `/anaconda/` található környezet a Python 2,7. <br/> * `py35`a-ben `/anaconda/envs/py35`található környezet a Python 3,5.       |
| A minták mutató hivatkozások      | A Pythonhoz készült minta Jupyter-jegyzetfüzetek is elérhetők.     |
| A DSVM kapcsolódó eszközök      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

**Linux**
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

  A dsvm-hez a Python alapértelmezett környezet összes felhasználó által olvasható globális környezetben. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, aktiválja a `source activate` parancsot rendszergazdaként, vagy a sudo engedélyekkel rendelkező felhasználóként a root vagy a py35 környezetre. Ezt követően használhatja a Package Managert (például `conda` vagy `pip` ) a csomagok telepítéséhez vagy frissítéséhez.

**Windows 2012**
* Futtassa a parancsot a parancssorban:

  Nyisson meg egy parancssort, és hajtsa végre az alábbi műveletek egyikét a futtatni kívánt Python-verziótól függően:

     ```
    # To run Python 2.7
    activate 
    python --version
    
    # To run Python 3.5
    activate py35
    python --version
    
    ```
* Használat az IDE-ben:

  Használható Python Tools for Visual Studio (PTVS) a Visual Studio Community Edition telepítve van. A PTVS-ben automatikusan beállított környezet a Python 2,7.
    > [!NOTE]
    > Ahhoz, hogy a PTVS a Python 3,5-es verzióra mutasson, létre kell hoznia egy egyéni környezetet a PTVS-ben. A környezeti útvonal a Visual Studio Community kiadásban történő beállításához nyissa meg az **eszközök** -> **Python-eszközök** -> **Python-környezetek** elemet, és válassza a **+ Egyéni**lehetőséget. Ezután állítsa be a helyet `c:\anaconda\envs\py35` a következőre, és válassza az _automatikus észlelés_lehetőséget.

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát **Python [Conda root]** -ként állíthatja be a Python 2,7 és a Python **[Conda env: py35]** for Python 3,5 számára. 

* Python-csomagok telepítése:

  A DSVM lévő alapértelmezett Python-környezetek az összes felhasználó által olvasható globális környezetek. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, aktiválja a parancsot rendszergazdaként a `activate` root vagy a py35 környezetbe. Ezt követően használhatja a Package Managert (például `conda` vagy `pip` ) a csomagok telepítéséhez vagy frissítéséhez.

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Microsoft R Open 3. x (100%-kompatibilis a CRAN-R-vel)<br /> Microsoft R Server 9. x Developer Edition (skálázható, nagyvállalati használatra kész R platform)|
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| A minták mutató hivatkozások      | Az R-hez készült minta Jupyter-jegyzetfüzeteket tartalmazza.     |
| A DSVM kapcsolódó eszközök      | SparkR, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

**Windows**:

* Futtassa a parancsot a parancssorban:

  Nyisson meg egy parancssort, `R`és írja be a parancsot.

* Használat az IDE-ben:

  Használat RTools a Visual Studio (RTVS) a Visual Studio Community edition vagy az RStudio telepítése. Ezek a Start menüben vagy asztali ikonként érhetők el. 

* Használat a Jupyter

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A Jupyter R kernel (IRKernel) használatához a rendszermag típusát **r** értékre állíthatja.

* R-csomagok telepítése:

  Az R az összes felhasználó által olvasható globális környezetben van telepítve a DSVM. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa az R-t az előző módszerek egyikének használatával. Ezután futtathatja az R Package Managert `install.packages()` a csomagok telepítéséhez vagy frissítéséhez.

**Linux**:

* Futtatás a (z) terminálban:

  Nyisson meg egy terminált `R`, és futtassa a parancsot.  

* Használat az IDE-ben:

  A Linux DSVM telepített RStudio használata.  

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A Jupyter R kernel (IRKernel) használatához a rendszermag típusát **r** értékre állíthatja. 

* R-csomagok telepítése:

  Az R az összes felhasználó által olvasható globális környezetben van telepítve a DSVM. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa az R-t az előző módszerek egyikének használatával. Ezután futtathatja az R Package Managert `install.packages()` a csomagok telepítéséhez vagy frissítéséhez.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | a 0.6-os |
| Támogatott DSVM-kiadások      | Linux, Windows     |
| Hogyan van azt konfigurálni / telepíteni a dsvm-hez?  | Windows: Telepítve:`C:\JuliaPro-VERSION`<br /> Linux: Telepítve:`/opt/JuliaPro-VERSION`    |
| A minták mutató hivatkozások      | A Julia-hez készült minta Jupyter-jegyzetfüzetek beletartoznak.     |
| A DSVM kapcsolódó eszközök      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Használat és Futtatás    

**Windows**:

* Futtatás parancssorból

  Nyisson meg egy parancssort, `julia`és futtassa a parancsot.
* Használat az IDE-ben:

  A DSVM-ben telepített Julia ide- velésasztaliparancsikonkénthasználható.`Juno`

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát **Júlia-verzióként**állíthatja be.

* Julia-csomagok telepítése:

  Az alapértelmezett Julia-hely az összes felhasználó által olvasható globális környezet. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa a Julia-t az előző módszerek egyikének használatával. Ezután a Julia Package Manager parancsait is futtathatja `Pkg.add()` , például a csomagok telepítéséhez vagy frissítéséhez.


**Linux**:
* Futtatás terminálon:

  Nyisson meg egy terminált `julia`, és futtassa a parancsot.
* Használat az IDE-ben:

  Használja `Juno`a (z) és a (z) Julia ide-t a DSVM, és elérhető az **alkalmazás** menüje parancsikonként.

* Használat a Jupyter-ben:

  Nyissa meg a Jupyter, és válassza az **új** lehetőséget egy új jegyzetfüzet létrehozásához. A kernel típusát **Júlia-verzióként**állíthatja be.

* Julia-csomagok telepítése:

  Az alapértelmezett Julia-hely az összes felhasználó által olvasható globális környezet. Csak rendszergazdák írhatnak és telepíthetnek globális csomagokat. Ha a csomagokat a globális környezetbe szeretné telepíteni, futtassa a Julia-t az előző módszerek egyikének használatával. Ezután a Julia Package Manager parancsait is futtathatja `Pkg.add()` , például a csomagok telepítéséhez vagy frissítéséhez.

## <a name="other-languages"></a>Más nyelveken

**C#** : Windows rendszeren érhető el, és a Visual Studio Community Edition vagy a `Developer Command Prompt for Visual Studio`rendszeren elérhető, ahol futtathatja a `csc` parancsot.

**Java**: A OpenJDK a DSVM Linux-és Windows-kiadásaiban is elérhető, és az elérési útra van beállítva. A Java használatához írja be a `javac` vagy `java` a parancsot a parancssorba a Windows vagy a Linux rendszerű bash-rendszerhéjba.

**Node.js**: A Node. js a DSVM Linux-és Windows-kiadásaiban is elérhető, és az elérési útra van beállítva. A Node. js eléréséhez írja be `node` a `npm` vagy a parancsot a parancssorba a Windows rendszerben vagy a Linux bash-rendszerhéjon. Windows rendszeren a Node. js-eszközökhöz készült Visual Studio-bővítmény telepítve van a Node. js-alkalmazás fejlesztéséhez szükséges grafikus IDE létrehozásához.

**F#** : Windows rendszeren érhető el, és a Visual Studio Community Edition vagy egy `Developer Command Prompt for Visual Studio`rendszeren elérhető, ahol futtathatja a `fsc` parancsot.
