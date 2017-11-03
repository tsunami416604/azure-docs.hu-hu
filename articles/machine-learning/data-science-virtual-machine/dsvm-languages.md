---
title: "Az adatok tudományos virtuális gépet az Azure nyelvek |} Microsoft Docs"
description: "Az adatok tudományos virtuális gépet az Azure nyelvek"
keywords: "adatok tudományos eszközök, adatok tudományos virtuális gép, adattudomány, linux adattudomány eszközei"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 2f2125e739b738847e03ce429d65801969611685
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Az adatok tudományos virtuális gép támogatott nyelvek 

Az adatok tudományos virtuális gép (DSVM) több előre elkészített nyelvek és fejlesztői eszközök a AI alkalmazások tartalmaz. Az alábbiakban néhány, a következő néhányat a meglévők közül. 

## <a name="python"></a>Python

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | 2.7 és 3.5-ös verzióját |
| Támogatott DSVM kiadás      | Linux, Windows     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | A két globális `conda` környezetek jönnek létre. <br /> * `root`környezet helyen `/anaconda/` Python 2.7 van. <br/> * `py35`környezet helyen `/anaconda/envs/py35`Python 3.5 van       |
| Minták mutató hivatkozások      | A minta Jupyter notebookok Python tartoznak.     |
| A DSVM a kapcsolódó eszközök      | PySpark, R, Ágnes      |
### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?    

**Windows**:

* Parancssor futtatása

Nyissa meg a parancssort, és tegye a következőket a futtatni kívánt Python verziójától függően. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Az IDE használatával

Használja a Python Tools a Visual Studio (PTVS) a Visual Studio Community Edition telepítve. A csak környezet telepítése automatikusan a PTVS a Python 2.7. 
> [!NOTE]
> A PTVS pont Python 3.5, hozzon létre egy egyéni környezetet a PTVS kell. Ha a környezet elérési utak a Visual Studio Community Edition, keresse meg **eszközök** -> **Python Tools** -> **Python-környezetek** majd **+ egyéni**. Ezután állítsa be annak helyét `c:\anaconda\envs\py35` majd _az automatikus észlelés_. 

* A Jupyter használatával

Nyissa meg a Jupyter, majd kattintson a a `New` gombra kattintva létrehozhat egy újat. Ezen a ponton, kiválaszthatja a kernel típusú _Python [Conda gyökér]_ Python 2.7 és _Python [Conda env:py35]_ Python 3.5 környezethez. 

* Python-csomagok telepítése

Az alapértelmezett Python-környezetek a DSVM a rendszer minden felhasználók által is olvasható globális környezet. Azonban csak a rendszergazdák is írási és -csomagok globális. A legfelső szintű vagy py35 környezet használatával aktiválja a globális környezetben csomag telepítéséhez a `activate` parancsot rendszergazdaként. Használhatja például a package manager `conda` vagy `pip` telepíteni vagy frissíteni a csomagokat. 


**Linux**:

* A Terminálszolgáltatások fut

Nyissa meg a terminált, és tegye a következőket a futtatni kívánt Python verziójától függően. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Az IDE használatával

A Visual Studio Community Edition telepítve PyCharm használja. 

* A Jupyter használatával

Nyissa meg a Jupyter, majd kattintson a a `New` gombra kattintva létrehozhat egy újat. Ezen a ponton, kiválaszthatja a kernel típusú _Python [Conda gyökér]_ Python 2.7 és _Python [Conda env:py35]_ Python 3.5 környezethez. 

* Python-csomagok telepítése

Az alapértelmezett Python-környezetek a DSVM a rendszer minden felhasználók által is olvasható globális környezetekben. Azonban csak a rendszergazdák is írási és -csomagok globális. A legfelső szintű vagy py35 környezet használatával aktiválja a globális környezetben csomag telepítéséhez a `source activate` parancsot rendszergazdaként vagy a felhasználó sudo engedéllyel. Használhatja például a Csomagkezelő `conda` vagy `pip` telepíteni vagy frissíteni a csomagokat. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | Microsoft R nyitott 3.x (100 %-os CRAN-R kompatibilis<br /> Microsoft R Server 9.x Developer edition (A méretezhető vállalati készen R platform)|
| Támogatott DSVM kiadás      | Linux, Windows     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Windows:`C:\Program Files\Microsoft\R Server\R_SERVER` <br />Linux:` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Minták mutató hivatkozások      | Az R minta Jupyter notebookok tartoznak.     |
| A DSVM a kapcsolódó eszközök      | SparkR, Python, Ágnes      |
### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?    

**Windows**:

* Parancssor futtatása

Nyissa meg a parancssort, és csak gépelje `R`.

* Az IDE használatával

Használja a Visual Studio (RTVS) a Visual Studio Community edition vagy az Rstudióból telepített RTools. Ezek a a start menüben, és egy asztali ikon. 

* A Jupyter használatával

Nyissa meg a Jupyter, majd kattintson a a `New` gombra kattintva létrehozhat egy újat. Ezen a ponton, kiválaszthatja a kernel típusú _R_ Jupyter R kernel (IRKernel) használatára. 

* R csomagok telepítése

R minden felhasználó a DSVM olvasható globális környezetben van telepítve. Azonban csak a rendszergazdák is írási és -csomagok globális. A globális környezetben csomag telepítéséhez futtassa a fenti módszerek egyikével R. Ezután futtassa az R Csomagkezelő `install.packages()` telepíteni vagy frissíteni a csomagokat. 

**Linux**:

* A Terminálszolgáltatások fut

Nyissa meg a terminál és csak futási `R`.  

* Az IDE használatával

A Linux DSVM telepített Rstudióból használja.  

* A Jupyter használatával

Nyissa meg a Jupyter, majd kattintson a a `New` gombra kattintva létrehozhat egy újat. Ezen a ponton, kiválaszthatja a kernel típusú _R_ Jupyter R kernel (IRKernel) használatára. 

* R csomagok telepítése

R minden felhasználó a DSVM olvasható globális környezetben van telepítve. Azonban csak a rendszergazdák is írási és -csomagok globális. A globális környezetben csomag telepítéséhez futtassa a fenti módszerek egyikével R. Ezután futtassa az R Csomagkezelő `install.packages()` telepíteni vagy frissíteni a csomagokat. 


## <a name="julia"></a>Ágnes

|    |           |
| ------------- | ------------- |
| Támogatott nyelvi verziók | 0.5 |
| Támogatott DSVM kiadás      | Linux, Windows     |
| Hogyan van azt konfigurált / a DSVM telepítve?  | Windows: Telepített`C:\JuliaPro-VERSION`<br /> Linux: Telepített`/opt/JuliaPro-VERSION`    |
| Minták mutató hivatkozások      | A minta Jupyter notebookok Ágnes a tartoznak.     |
| A DSVM a kapcsolódó eszközök      | Python, R      |
### <a name="how-to-use--run-it"></a>Hogyan használja az / futtatni?    

**Windows**:

* Parancssor futtatása

Nyissa meg a parancssort és futtassa az `julia`. 
* Az IDE használatával

Használjon `Juno` az Ágnes IDE a DSVM telepíthető és asztali gyors érhető el.

* A Jupyter használatával

Nyissa meg a Jupyter, majd kattintson a a `New` gombra kattintva létrehozhat egy újat. Ezen a ponton kiválaszthatja a kernel típus lehet`Julia VERSION` 

* Ágnes csomagok telepítése

Ágnes hely alapértelmezés szerint a globális környezetben olvasható összes felhasználó. Azonban csak a rendszergazdák is írási és -csomagok globális. A globális környezetben csomag telepítéséhez futtassa a fenti módszerek egyikével Ágnes. Ezután futtassa a Ágnes package manager parancsok például `Pkg.add()` telepíteni vagy frissíteni a csomagokat. 


**Linux**:
* Terminálszolgáltatások futnak.

Nyissa meg a terminál és csak futási `julia`. 
* Az IDE használatával

Használjon `Juno` az Ágnes IDE a DSVM telepíthető és alkalmazás menü gyors érhető el.

* A Jupyter használatával

Nyissa meg a Jupyter, majd kattintson a a `New` gombra kattintva létrehozhat egy újat. Ezen a ponton kiválaszthatja a kernel típus lehet`Julia VERSION` 

* Ágnes csomagok telepítése

Ágnes hely alapértelmezés szerint a globális környezetben olvasható összes felhasználó. Azonban csak a rendszergazdák is írási és -csomagok globális. A globális környezetben csomag telepítéséhez futtassa a fenti módszerek egyikével Ágnes. Ezután futtassa a Ágnes package manager parancsok például `Pkg.add()` telepíteni vagy frissíteni a csomagokat. 

## <a name="other-languages"></a>Egyéb nyelvek

**C#**: érhető el a Windows és a Visual Studio Community edition keresztül vagy a elérhető egy `Developer Command Prompt for Visual Studio` is ugyanúgy futtató `csc` parancsot. 

**Java**: OpenJDK a Linux és a Windows edition a DSVM és a útvonalon érhető el. Beírhatja `javac` vagy `java` parancsot a parancssorba a Windows vagy Linux Java használandó a rendszerhéjakba. 

**NODE.js**: node.js a Linux és a Windows edition a DSVM és a útvonalon érhető el. Beírhatja `node` vagy `npm` parancsot a parancssorba a Windows vagy Linux node.js eléréséhez a rendszerhéjakba. Windows a Node.js-eszközök Visual Studio bővítmény arra, hogy a node.js-alkalmazás fejlesztéséhez grafikus IDE telepítve van. 

**F #**: érhető el a Windows és a Visual Studio Community edition keresztül vagy a elérhető egy `Developer Command Prompt for Visual Studio` is ugyanúgy futtató `fsc` parancsot. 


