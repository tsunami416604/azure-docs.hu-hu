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
ms.openlocfilehash: aedaa194a9667f1b89a3370c39c74424f2d01734
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208072"
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
* Use in an IDE:

  Use Python Tools for Visual Studio (PTVS), installed in the Visual Studio Community edition. By default, the only environment that's set up automatically in PTVS is Python 3.6. 

    > [!NOTE]
    > To point PTVS at Python 2.7, you must create a  custom environment in PTVS. To set this environment path in the Visual Studio  Community Edition, go to **Tools** -> **Python Tools** -> **Python Environments** and select **+ Custom**. Then, set the location to **c:\anaconda\envs\python2** and select **Auto Detect**.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as _Python [Conda Root]_ for Python 3.6 and _Python [Conda env:python2]_ for Python 2.7.

* Install Python packages:

  The default Python environments on the DSVM are global environments that are readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or python2 environment by using the `activate` command as an administrator. Then, you can use a package manager like `conda` or `pip` to install or update packages.

## Python (Linux and Windows Server 2012 edition)

|    |           |
| ------------- | ------------- |
| Language versions supported | Python 2.7 and 3.5 |
| Supported DSVM editions      | Linux, Windows Server 2012    |
| How is it configured / installed on the DSVM?  | Two global `conda` environments are created: <br /> * `root` environment located at `/anaconda/` is Python 2.7. <br/> * `py35` environment located at `/anaconda/envs/py35`is Python 3.5.       |
| Links to samples      | Sample Jupyter notebooks for Python are included.     |
| Related tools on the DSVM      | PySpark, R, Julia      |
### How to use and run it    

**Linux**
* Run in a terminal:

  Open the terminal and do one of the following, depending on the version of Python you want to run:

    ```
    # <a name="to-run-python-27"></a>A Python 2,7 futtatása
    forrás aktiválása Python-verzió
    
    # <a name="to-run-python-35"></a>A Python 3,5 futtatása
    forrás aktiválása py35 Python-verzió
    
    ```
* Use in an IDE:

  Use PyCharm, installed in the Visual Studio Community edition. 

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Python [Conda Root]** for Python 2.7 and **Python [Conda env:py35]** for the Python 3.5 environment. 

* Install Python packages:

  The default Python environments on the DSVM are global environments readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or py35 environment by using the `source activate` command as an administrator or as a user with sudo permissions. Then, you can use a package manager like `conda` or `pip` to install or update packages.

**Windows 2012**
* Run at a command prompt:

  Open a command prompt and do one of the following, depending on the version of Python you want to run:

     ```
    # <a name="to-run-python-27"></a>A Python 2,7 futtatása
    a Python aktiválása – verzió
    
    # <a name="to-run-python-35"></a>A Python 3,5 futtatása
    az py35 Python aktiválása – verzió
    
    ```
* Use in an IDE:

  Use Python Tools for Visual Studio (PTVS) installed in the Visual Studio Community edition. The only environment that's set up automatically in PTVS is Python 2.7.
    > [!NOTE]
    > To point PTVS at Python 3.5, you need to create a custom environment in PTVS. To set this environment path in the Visual Studio Community edition, go to **Tools** -> **Python Tools** -> **Python Environments** and select **+ Custom**. Then, set the location to `c:\anaconda\envs\py35` and select _Auto Detect_.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Python [Conda Root]** for Python 2.7 and **Python [Conda env:py35]** for Python 3.5. 

* Install Python packages:

  The default Python environments on the DSVM are global environments that are readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or py35 environment by using the `activate` command as an administrator. Then, you can use a package manager like `conda` or `pip` to install or update packages.

## R

|    |           |
| ------------- | ------------- |
| Language versions supported | Microsoft R Open 3.x (100% compatible with CRAN-R)<br /> Microsoft R Server 9.x Developer edition (a scalable enterprise-ready R platform)|
| Supported DSVM editions      | Linux, Windows     |
| How is it configured / installed on the DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links to samples      | Sample Jupyter notebooks for R are included.     |
| Related tools on the DSVM      | SparkR, Python, Julia      |
### How to use and run it    

**Windows**:

* Run at a command prompt:

  Open a command prompt and type `R`.

* Use in an IDE:

  Use RTools for Visual Studio (RTVS) installed in the Visual Studio Community edition or RStudio. These are available on the Start menu or as a desktop icon. 

* Use in Jupyter

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **R** to use the Jupyter R kernel (IRKernel).

* Install R packages:

  R is installed on the DSVM in a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run R by using one of the preceding methods. Then, you can run the R package manager `install.packages()` to install or update packages.

**Linux**:

* Run in terminal:

  Open a terminal and run `R`.  

* Use in an IDE:

  Use RStudio, installed on the Linux DSVM.  

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **R** to use the Jupyter R kernel (IRKernel). 

* Install R packages:

  R is installed on the DSVM in a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run R by using one of the preceding methods. Then, you can run the R package manager `install.packages()` to install or update packages.


## Julia

|    |           |
| ------------- | ------------- |
| Language versions supported | 0.6 |
| Supported DSVM editions      | Linux, Windows     |
| How is it configured / installed on the DSVM?  | Windows: Installed at `C:\JuliaPro-VERSION`<br /> Linux: Installed at `/opt/JuliaPro-VERSION`    |
| Links to samples      | Sample Jupyter notebooks for Julia are included.     |
| Related tools on the DSVM      | Python, R      |
### How to use and run it    

**Windows**:

* Run at a command prompt

  Open a command prompt and run `julia`.
* Use in an IDE:

  Use `Juno` with the Julia IDE installed on the DSVM and available as a desktop shortcut.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Julia VERSION**.

* Install Julia packages:

  The default Julia location is a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run Julia by using one of the preceding methods. Then, you can run Julia package manager commands like `Pkg.add()` to install or update packages.


**Linux**:
* Run in a terminal:

  Open a terminal and run `julia`.
* Use in an IDE:

  Use `Juno`, with the Julia IDE installed on the DSVM and available as an **Application** menu shortcut.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Julia VERSION**.

* Install Julia packages:

  The default Julia location is a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run Julia by using one of the preceding methods. Then, you can run Julia package manager commands like `Pkg.add()` to install or update packages.

## Other languages

**C#**: Available on Windows and accessible through the Visual Studio Community edition or at the `Developer Command Prompt for Visual Studio`, where you can run the `csc` command.

**Java**: OpenJDK is available on both the Linux and Windows editions of the DSVM and is set on the path. To use Java, type the `javac` or `java` command at a command prompt in Windows or on the bash shell in Linux.

**Node.js**: Node.js is available on both the Linux and Windows editions of the DSVM and is set on the path. To access Node.js, type the `node` or `npm` command at a command prompt in Windows or on the bash shell in Linux. On Windows, the Visual Studio extension for the Node.js tools is installed to provide a graphical IDE to develop your Node.js application.

**F#**: Available on Windows and accessible through the Visual Studio Community edition or at a `Developer Command Prompt for Visual Studio`, where you can run the `fsc` command.
