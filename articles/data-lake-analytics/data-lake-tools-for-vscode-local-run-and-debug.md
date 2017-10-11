---
title: "Az Azure Data Lake Tools: U-SQL helyi futtatásával és a Visual Studio Code helyi hibakeresési |} Microsoft Docs"
description: "Útmutató: Azure Data Lake Tools használandó Visual Studio Code helyi Futtatás és helyi hibakeresési."
Keywords: "VScode, az Azure Data Lake Tools, a helyi futtatáskor, helyi hibakeresése a helyi debug preview tárolófájl feltölteni. tárolási elérési útja"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.openlocfilehash: 367e4ba792f83d6ee246208306e4c09b69cb49ef
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>U-SQL helyi futtatásával és a helyi hibakeresése a Visual Studio Code

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy már működik a következő előfeltételek vonatkoznak a ezek az eljárások megkezdése előtt:
- Az Azure Data Lake Visual Studio Code eszköz. Útmutatásért lásd: [használata Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- C# a Visual Studio Code (Ha el kíván végezni egy U-SQL helyi hibakeresési).

   ![Telepítse C# a Data Lake Tools for Visual Studio kód](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > A U-SQL helyi futtatása és a hibakeresési szolgáltatások jelenleg csak Windows felhasználók támogatására. 


## <a name="set-up-the-u-sql-local-run-environment"></a>A U-SQL helyi futtatási környezet beállítása

1. Válassza ki a Ctrl + Shift + P nyissa meg a parancs palettát, és írja be a **ADL: letöltése LocalRun függőségi** letöltése a csomagok.  

   ![Töltse le az ADL LocalRun függőségi csomagok](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. Keresse meg a függőségi csomagok látható elérési útjáról a **kimeneti** ablaktáblán, majd telepítse BuildTools és Win10SDK 10240. Íme egy példa elérési útja:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![Keresse meg a függőségi csomagok](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   a. BuildTools telepítéséhez kövesse a varázsló utasításait.   

  ![BuildTools telepítése](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   b. Win10SDK 10240 telepítéséhez kövesse a varázsló utasításait.  

  ![Telepítse a Win10SDK 10240](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. A környezeti változó beállítása. Állítsa be a **SCOPE_CPP_SDK** környezeti változót:  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
4. Az operációs rendszer, győződjön meg arról, hogy a környezetiváltozó-beállításainak érvénybe léptetéséhez indítsa újra.  

   ![Győződjön meg arról, a SCOPE_CPP_SDK környezeti változó telepítve van](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Indítsa el a helyi Futtatás szolgáltatást, és küldje el a U-SQL helyi fiók feladat 
Az első felhasználó kéri töltse le az ADL: letöltése LocalRun függőségi csomagok, ha még nincs telepítve.
1. Válassza ki a Ctrl + Shift + P nyissa meg a parancs palettát, és írja be a **ADL: helyi futtatása szolgáltatás indítása**.
2. Válassza ki **elfogadás** először a Microsoft szoftverlicenc-szerződést fogadásához. 

   ![A Microsoft szoftverlicenc-feltételek elfogadása](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. Az cmd-konzol megnyitása. A felhasználók első alkalommal meg kell adnia **3**, és keresse meg a bemeneti és kimeneti a helyi mappa elérési útját. Egyéb lehetőségek használhatja az alapértelmezett értékeket. 

   ![A Data Lake Tools for Visual Studio Code helyi futtatási cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Válassza ki a Ctrl + Shift + P nyissa meg a parancs palettát, adja meg **ADL: feladat elküldése**, majd válassza ki **helyi** elküldeni a feladatot a helyi fiókba.

   ![A Data Lake Tools for Visual Studio Code helyi kiválasztása](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. A feladat elküldése után megtekintheti a küldésének részleteit. A küldése részleteinek kiválasztása nézetre **jobUrl** a a **kimeneti** ablak. A feladatállapot küldésének a cmd-konzolról is megtekintheti. Adja meg **7** a cmd-konzolon, ha szeretné tudni, hogy több feladat részleteit.

   ![A Data Lake Visual Studio Code helyi futtatáskor a kimeneti eszközei](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code helyi futtatáskor a cmd állapota](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Egy helyi hibakeresési a U-SQL-feladat indítása  
Az első felhasználó kéri töltse le az ADL: letöltése LocalRun függőségi csomagok, ha még nincs telepítve.
  
1. Válassza ki a Ctrl + Shift + P nyissa meg a parancs palettát, és írja be a **ADL: helyi futtatása szolgáltatás indítása**. Az cmd-konzol megnyitása. Győződjön meg arról, hogy a **DataRoot** van beállítva.
3. A C# háttérkód állítható be töréspont.
4. Vissza a parancsprogram-szerkesztő, válassza ki a Ctrl + Shift + P a parancs konzol megnyitásához, és írja be **helyi hibakeresése** a helyi hibakeresési szolgáltatás elindításához.

![A Data Lake Tools for Visual Studio Code helyi hibakeresési eredménye](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Következő lépések
- Azure Data Lake Tools használatával a Visual Studio Code, lásd: [használata Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).
- A Data Lake Analytics elindított adatainak lekérése, lásd: [oktatóanyag: Ismerkedés az Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md).
- A Data Lake Tools for Visual Studio információ: [oktatóanyag: fejlesztése U-SQL-parancsfájlok Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md).
- A szerelvények fejlesztésével információkért lásd: [fejlesztése U-SQL-szerelvények Azure Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-assemblies.md).
