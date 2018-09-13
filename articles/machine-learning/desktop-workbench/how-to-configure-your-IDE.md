---
title: Hogyan konfigurálása az Azure Machine Learning Workbench működéséhez IDE-vel?  | Microsoft Docs
description: Egy útmutató, amellyel konfigurálása az Azure Machine Learning Workbench használata ide használatával.
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 18692fe631a7e1349ead6bc68a87934e6d030913
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645679"
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Az Azure Machine Learning Workbench használata az integrált fejlesztői Környezetig konfigurálása 

Az Azure Machine Learning Workbench beállítható úgy, hogy a népszerű Python ide-(integrált fejlesztőkörnyezetet) működik. Lehetővé teszi a zökkenőmentes adatelemzés fejlesztési környezetet biztosít adat-előkészítés, a kód szerzői műveletek, a futtatási követési és a operacionalizálás közötti áthelyezése. A támogatott Ide-k jelenleg a következők:
- A Microsoft Visual Studio Code-ot 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Munkaterület konfigurálása
1. Kattintson a **fájl** menü felső sarokban az alkalmazás left. 
2. Válassza ki a **konfigurálása projekt IDE** lehetőségét a úszó menü 
3. Írja be a `VS Code` vagy `PyCharm` a a **neve** mezőben (a név a tetszőleges)
4. Adja meg a helyet az IDE végrehajtható fájlt (a végrehajtható fájl neve és a bővítmény kész) a **végrehajtási elérési útja**

### <a name="default-install-path-for-visual-studio-code"></a>Alapértelmezett elérési útját a Visual Studio Code  

* 32 bites – Windows `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* A Windows 64 bites `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS – válassza ki a .app elérési útját, például `/Applications/Visual Studio Code.app`, és az alkalmazás elérési útja, a többi fűzi hozzá. Alapértelmezés szerint a végrehajtható fájl teljes elérési útja `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Ha végrehajtott a `Shell Command: Install 'code' command in PATH` parancsot a VS Code-ban, akkor is hivatkozhat a VS Code található szkriptet `/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>Alapértelmezett elérési útját a PyCharm 

* A Windows 32-bit - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* A Windows 64 bites - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS - válassza ki a .app elérési útját, például "/ Applications/PyCharm CE.app", és az alkalmazás elérési útja, a többi fűzi hozzá. Alapértelmezés szerint a végrehajtható fájl teljes elérési útja `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Is hasznosnak találhatja, a bin mappa PyCharm `/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>Megnyitja a projektet IDE-ben 
A konfiguráció befejezése után a megnyithatja az Azure Machine Learning-projekt megnyitása a **fájl** menü az Azure Machine Learning Workbenchben, majd kattintson a **nyissa meg a projekt (< IDE_Name >)**. Ez a művelet megnyitja az aktuális aktív projekt a konfigurált ide. _Megjegyzés: Ha nem a projektben a **megnyitja a projektet (< IDE_Name >)** le lesz tiltva._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Az integrált terminálon konfigurálása a Visual Studio Code-ban

### <a name="windows"></a>Windows 
Alapértelmezett rendszerhéját helyett PowerShell cmd kell azt kell felülbírálni. Ha úgy kattint, a **megnyitja a projektet (< IDE_Name >)**, egy üzenet jelenik meg akkor: 

_Rendszerhéj engedélyezi: `C:\windows\System32\cmd.exe` (a munkaterület beállításai definiált) parancsot a terminálon indítható?_

Válasz `yes` , hogy a rendszerhéj gördülékenyen együttműködnek az Azure Machine Learning Workbench parancssori felület konfigurálása.

### <a name="mac"></a>Mac
Futtatásához egy `az` parancs használatával a Visual Studio Code integrált terminálon Mac gépen, akkor manuálisan kell beállítania a `PATH` stejnou hodnotu jako kell `PATH` a projekt `.vscode/settings.json` fájlt a kulcs alatt `terminal.integrated.env.osx`. Ehhez futtassa a következő parancsot a terminálon: `PATH=<PATH in .vscode/settings>`
