---
title: "Hogyan konfigurálható az Azure Machine Learning munkaterület egy IDE használható?  | Microsoft Docs"
description: "Együttműködik az IDE-Azure Machine Learning munkaterület konfigurálása útmutatóját."
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 4e18a413a0559b1ddebecf1b29722d21ef35c337
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>Azure Machine Learning munkaterület szeretne dolgozni egy IDE konfigurálása 

Az Azure Machine Learning-munkaterület beállítható úgy, hogy dolgozni a népszerű Python IDEs (integrált fejlesztési környezet). Ez lehetővé teszi a zökkenőmentes adatok tudományos fejlesztési adatok előkészítése, a kód szerzői, a futtatási nyomon követését és a operationalization közötti áthelyezése. A támogatott IDEs jelenleg:
- A Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>Munkaterület konfigurálása
1. Kattintson a **fájl** menü felső sarkában az alkalmazás balra. 
2. Válassza ki a **konfigurálása projekt IDE** kapcsolót a menü 
3. Írja be a `VS Code` vagy `PyCharm` a a **neve** mező (a név tetszőleges)
4. Adja meg a helyet IDE végrehajtható fájl (a végrehajtható fájl nevét és kiterjesztését a teljes) a **végrehajtási elérési útja**

### <a name="default-install-path-for-visual-studio-code"></a>A Visual Studio Code alapértelmezett telepítési útvonala  

* A Windows 32-bit-`C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* A Windows 64 bites-`C:\Program Files\Microsoft VS Code\Code.exe`
* macOS - válassza az .app elérési útját, például `/Applications/Visual Studio Code.app`, és az alkalmazás fűzi hozzá az elérési út részeinek meg. Alapértelmezés szerint a végrehajtható fájl teljes elérési útja `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`. Ha rendelkezik hajtja végre a `Shell Command: Install 'code' command in PATH` parancs Visual STUDIO Code, akkor is is hivatkozni lehessen a Visual STUDIO Code parancsfájlt:`/usr/local/bin/code`

### <a name="default-install-path-for-pycharm"></a>PyCharm az alapértelmezett telepítési útvonala 

* A Windows 32-bit - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`. 
* A Windows 64 bites - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`.
* macOS - válassza .app elérési útját, például "/ Applications/PyCharm CE.app", és az alkalmazás fűzi hozzá az elérési út részeinek meg. Alapértelmezés szerint a végrehajtható fájl teljes elérési útja `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`. Is találhat, a bin mappa PyCharm`/usr/local/bin/charm`

## <a name="open-project-in-ide"></a>A IDE projekt megnyitása 
A konfigurálásának befejezését követően úgy is megnyithatja az Azure Machine Learning projekt megnyitása a **fájl** menü Azure Machine Learning munkaterület, majd kattintson a **nyissa meg a projekt (< IDE_Name >)**. Ez a művelet megnyitja az aktuális aktív projekt a konfigurált ide. _Megjegyzés: Ha nem a projektben a **projekt megnyitása (< IDE_Name >)** le lesz tiltva._

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>Az integrált Terminálszolgáltatások konfigurálása a Visual Studio Code

### <a name="windows"></a>Windows 
Alapértelmezett rendszerhéját helyett PowerShell cmd kell azt kell felülbírálni. Az kattint, a **projekt megnyitása (< IDE_Name >)**, megjelenik egy értesítés: 

_Teszik rendszerhéj: `C:\windows\System32\cmd.exe` (megadott munkaterület-beállításként) a terminálban indítható?_

Válasz `yes` konfigurálása az Azure ML munkaterület parancssori felülettel problémamentesen működjön a rendszerhéj engedélyezéséhez.

### <a name="mac"></a>Mac
Futtatásához egy `az` parancs használatával a Visual Studio Code integrált Terminálszolgáltatások Mac gépen, és manuálisan állítsa be a `PATH` megegyező értékűnek kell `PATH` a projekt `.vscode/settings.json` fájl a kulcs alatt `terminal.integrated.env.osx`. Ezt a terminálban a következő parancs futtatásával teheti meg:`PATH=<PATH in .vscode/settings>`
