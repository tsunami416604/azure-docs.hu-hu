---
title: Debug U-SQL feladatok – Azure Data Lake Tools for Visual Studio Code
description: Megtudhatja, hogy az Azure Data Lake Tools for Visual Studio Code használatával helyileg futtatható és debugu-SQL-feladatokat futtathat.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72030032"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Az U-SQL futtatása és a hibakeresés helyi futtatása a Visual Studio-kódban
Ez a cikk azt ismerteti, hogyan futtatható az U-SQL-feladatok egy helyi fejlesztői gépen a korai kódolási fázisok felgyorsítása vagy a kód helyi hibakeresése a Visual Studio-kódban. Az Azure Data Lake Tool for Visual Studio Code alkalmazásról az [Azure Data Lake tools for Visual Studio Code (Azure Data Lake eszközök használata a Visual Studio-kódhoz) alkalmazásban található.](data-lake-analytics-data-lake-tools-for-vscode.md)

Csak az Azure Data Lake Tools for Visual Studio Windows-telepítései támogatják az U-SQL helyi futtatását és az U-SQL helyi hibakeresését. A macOS és Linux alapú operációs rendszereken futó telepítések nem támogatják ezt a funkciót.

## <a name="set-up-the-u-sql-local-run-environment"></a>Az U-SQL helyi futtatási környezetének beállítása

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt, majd írja be az **ADL: Letöltési helyi futtatási csomag lehetőséget** a csomagok letöltéséhez.  

   ![Az ADL LocalRun függőségi csomagok letöltése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Keresse meg a függőségi csomagokat a **Kimenet** ablaktáblán látható elérési útról, majd telepítse a BuildTools és a Win10SDK 10240 alkalmazást. Íme egy példa elérési út:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![A függőségi csomagok megkeresése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 A **BuildTools**telepítéséhez kattintson a visualcppbuildtools_full.exe gombra a LocalRunDependency mappában, majd kövesse a varázsló utasításait.   

    ![BuildTools telepítése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 A **Win10SDK 10240**telepítéséhez kattintson az sdksetup.exe fájlra a LocalRunDependency/Win10SDK_10.0.10240_2 mappában, majd kövesse a varázsló utasításait.  

    ![Win10SDK 10240 telepítése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Állítsa be a környezeti változót. Állítsa a **SCOPE_CPP_SDK** környezeti változót a következőre:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Indítsa el a helyi futtatási szolgáltatást, és küldje el az U-SQL feladatot egy helyi fiókba 
Az első alkalommal felhasználó, használja **ADL: Letöltés helyi futtatási csomag** letölthető helyi futtatási csomagok, ha még nem [állította be az U-SQL helyi futtatási környezetben](#set-up-the-u-sql-local-run-environment).

1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt, majd írja be az **ADL: Start Local Run Service parancsot**.   
2. Válassza az **Elfogadás** lehetőséget a Microsoft szoftverlicenc-szerződés első elfogadásához. 

   ![A Microsoft szoftverlicenc-szerződésének elfogadása](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Megnyílik a cmd konzol. Az első felhasználók számára meg kell adnia a **3**értéket, majd meg kell találnia az adatbevitel és -kimenet helyi mappaelérési útját. Ha nem sikerült az elérési utat fordított perjelekkel definiálni, próbálja meg a perjelek et előremutatóval. Más beállításokhoz használhatja az alapértelmezett értékeket.

   ![Data Lake Tools a Visual Studio Code helyi futtatási cmd-hez](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt, írja be az **ADL: Submit Job**parancsot, majd válassza a **Helyi** lehetőséget a feladat helyi fiókba küldéséhez.

   ![A Data Lake Tools for Visual Studio Code helyi](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Miután elküldte a feladatot, megtekintheti a beküldés részleteit. A beküldés részleteinek megtekintéséhez válassza a **jobUrl** lehetőséget a **Kimenet ablakban.** Az állásbenyújtás állapotát a cmd konzolról is megtekintheti. Ha többet szeretne tudni a munka részleteiről, írja be a **7-et** a cmd konzolba.

   ![Data Lake Tools for Visual](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Studio Code helyi futtatási kimeneti Data Lake Tools for Visual Studio Code helyi futtatási cmd állapota](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Helyi hibakeresés indítása az U-SQL feladathoz  
Az első felhasználó számára:

1. **ADL használata: Helyi futtatási csomag letöltése** helyi futtatási csomagok letöltéséhez, ha nem [állította be az U-SQL helyi futtatási környezetet.](#set-up-the-u-sql-local-run-environment)
2. Telepítse a .NET Core SDK 2.0-t az üzenetpanelen javasoltak szerint, ha nincs telepítve.
 
  ![emlékeztető telepíti a Dotnet-et](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Telepítse a C# alkalmazást a Visual Studio-kódhoz az üzenetpanelen javasoltak szerint, ha nincs telepítve.A folytatáshoz kattintson a **Telepítés** gombra, majd indítsa újra a VSCode programot.

    ![Emlékeztető a C telepítéséhez #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

A helyi hibakeresés végrehajtásához kövesse az alábbi lépéseket:
  
1. A parancspaletta megnyitásához válassza a Ctrl+Shift+P billentyűkombinációt, majd írja be az **ADL: Start Local Run Service parancsot**. Megnyílik a cmd konzol. Győződjön meg arról, hogy a **DataRoot** be van állítva.
2. Állítson be egy töréspontot a C# kód mögött.
3. Vissza a parancsfájlszerkesztőhöz, kattintson a jobb gombbal, és válassza **az ADL: Local Debug parancsot.**
    
   ![Data Lake Tools for Visual Studio Code helyi hibakeresési eredmény](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Az U-SQL fejlesztése Pythonnal, R-rel és CSharp-mal az Azure Data Lake Analytics szolgáltatáshoz a VSCode-ban](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Ismerkedés a Data Lake Analytics szolgáltatással a PowerShell használatával](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics szolgáltatással az Azure Portal használatával](data-lake-analytics-get-started-portal.md)
* [A Data Lake Tools for Visual Studio használata U-SQL alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
* [A Data Lake Analytics (U-SQL) katalógus használata](data-lake-analytics-use-u-sql-catalog.md)
