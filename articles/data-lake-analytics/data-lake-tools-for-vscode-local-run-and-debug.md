---
title: Futtassa a U-SQL és hibakeresése helyileg az Azure Data Lake Tools for Visual Studio Code
description: Ismerje meg, hogyan használható az Azure Data Lake Tools for Visual Studio Code futtatásához, és helyben a U-SQL-feladatok hibakereséséhez.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 42982e3fa0a854109a6b887640604498ac68847c
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632259"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>Futtassa a U-SQL és a helyi hibakeresése a Visual Studio Code-ban
Ez a cikk ismerteti, hogyan futtathat U-SQL-feladatok kódolási korai szakaszában felgyorsítása érdekében vagy a Visual Studio Code helyi kódok hibakeresése egy helyi fejlesztői gépen. A Visual Studio Code az Azure Data Lake eszköz utasításokért lásd: [használata Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Csak Windows telepítését az Azure Data Lake Tools for Visual Studio támogatja a műveletet az U-SQL helyi futtatása és a U-SQL helyi hibakereséséhez. Telepítés macOS és Linux-alapú operációs rendszerek nem támogatják ezt a szolgáltatást.

## <a name="set-up-the-u-sql-local-run-environment"></a>A U-SQL helyi futtatási környezet beállítása

1. Válassza ki a parancskatalógus megnyitásához, és adja meg a Ctrl + Shift + P **ADL: helyi futtatása csomag letöltése** letöltése a csomagok.  

   ![Az ADL LocalRun függőség-csomagok letöltése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Keresse meg a megjelenő elérési útról a függőségcsomagokat a **kimeneti** ablaktáblán, majd telepítse BuildTools és Win10SDK 10240. Íme egy példa az elérési útra:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![Keresse meg a függőségi csomagokat](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1-es verziójának telepítése a **BuildTools**, kattintson a visualcppbuildtools_full.exe LocalRunDependency mappában, majd kövesse a varázsló utasításait.   

    ![BuildTools telepítése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2-es telepítendő **Win10SDK 10240**, kattintson a sdksetup.exe LocalRunDependency/Win10SDK_10.0.10240_2 mappában, majd kövesse a varázsló utasításait.  

    ![Telepítse a Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. A környezeti változó beállítása. Állítsa be a **SCOPE_CPP_SDK** környezeti változót:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Indítsa el a helyi Futtatás szolgáltatást, és küldje el a U-SQL-feladatot az helyi fiók 
A felhasználó első alkalommal használja **ADL: helyi futtatása csomag letöltése** helyi futtatási csomag letöltése, ha még nem [U-SQL helyi futtatási környezetet](#set-up-the-u-sql-local-run-environment).

1. Válassza ki a parancskatalógus megnyitásához, és adja meg a Ctrl + Shift + P **ADL: helyi futtatása szolgáltatás indítása**.   
2. Válassza ki **elfogadás** először a Microsoft szoftverlicenc-feltételeket fogadására. 

   ![A Microsoft szoftverlicenc-feltételek elfogadása](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Megnyílik a cmd-konzolon. A felhasználók első alkalommal meg kell adnia **3**, és keresse meg az adatok bemeneti és kimeneti a helyi mappa elérési útját. További lehetőségekért használhatja az alapértelmezett értékeket. 

   ![A Data Lake Tools for Visual Studio Code helyi futtatási cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Válassza ki a parancskatalógus megnyitásához, írja be a Ctrl + Shift + P **ADL: feladat elküldése**, majd válassza ki **helyi** az helyi fiók a feladat elküldéséhez.

   ![A Data Lake Tools for Visual Studio Code helyi kiválasztása](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. A feladat elküldése után megtekintheti a küldésének részleteit. A beküldés részleteinek megtekintéséhez válassza ki a **jobUrl** a a **kimeneti** ablak. A feladatállapot beküldése a cmd-konzolról is megtekintheti. Adja meg **7** a cmd-konzolon, ha azt szeretné tudni, hogy további részleteket.

   ![Data Lake Tools for Visual Studio Code helyi futtatáskor a kimeneti](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code helyi futtatáskor a cmd állapota](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Egy helyi hibakeresési a U-SQL-feladat indítása  
Az első felhasználó:

1. Használat **ADL: helyi futtatása csomag letöltése** helyi futtatási csomag letöltése, ha még nem [U-SQL helyi futtatási környezetet](#set-up-the-u-sql-local-run-environment).
2. Telepítse a .NET Core SDK 2.0 javasolt üzenet mezőjében, ha nincs telepítve.
 
  ![Emlékeztető Dotnet telepíti](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Telepítés C# nyelv, a Visual Studio Code, az üzenet mezőbe javasolt, ha nincs telepítve. Kattintson a **telepítése** a folytatáshoz, majd indítsa újra a VSCode.

    ![Emlékeztető telepítése C#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Kövesse az alábbi helyi hibakeresési lépéseket:
  
1. Válassza ki a parancskatalógus megnyitásához, és adja meg a Ctrl + Shift + P **ADL: helyi futtatása szolgáltatás indítása**. Megnyílik a cmd-konzolon. Győződjön meg arról, hogy a **DataRoot** van beállítva.
2. A C# tartozó kódot állítson be egy töréspontot.
3. Vissza a parancsprogram-szerkesztő, kattintson a jobb gombbal, és válassza ki a **ADL: helyi hibakeresése**.
    
   ![A Data Lake Tools for Visual Studio Code helyi hibakeresési eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>További lépések
* [Az Azure Data Lake Tools for Visual Studio kód használata](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL, Python, R és a vscode-ban az Azure Data Lake Analytics CSharp fejlesztése](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [PowerShell-lel a Data Lake Analytics használatának első lépései](data-lake-analytics-get-started-powershell.md)
* [Ismerkedés a Data Lake Analytics az Azure portal használatával](data-lake-analytics-get-started-portal.md)
* [A Data Lake Tools for Visual Studio használata a U-SQL-alkalmazások fejlesztéséhez](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake használata Analytics(U-SQL) katalógus](data-lake-analytics-use-u-sql-catalog.md)
