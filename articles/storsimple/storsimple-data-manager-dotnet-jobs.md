---
title: A Microsoft Azure StorSimple adatkezelő feladatokhoz használhatja a .NET SDK |} Microsoft Docs
description: Megtudhatja, hogyan használhatja a .NET SDK StorSimple adatkezelő feladatok elindítása
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d15a5cbda2f0c2a363b40e94c38fed6631aa81b5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27928236"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Adatok átalakítása kezdeményezni a .net SDK használatával

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használhatja a StorSimple adatkezelő szolgáltatáson belül az adatok átalakítása szolgáltatás StorSimple eszközön tárolt adatok átalakítását. Az átalakított adatok majd fel más Azure-szolgáltatásokkal a felhőben.

Egy átalakítási feladatot két módon indíthatja el:

 - A .NET SDK használata
 - Azure Automation-forgatókönyv használata
 
 Ez a cikk részletesen létrehozása egy minta .NET-Konzolalkalmazás kezdeményezéséhez egy átalakítási feladatot, és nyomon követheti azt befejezésére. Adatok átalakítása keresztül Automation indítása kapcsolatos további tudnivalókért keresse fel [használata Azure Automation-runbook eseményindító adatok átalakítási feladat](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:
*   Rendszert futtató számítógép:

    - A Visual Studio 2012, 2013, 2015-öt vagy 2017.

    - Az Azure Powershell. [Töltse le az Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Egy megfelelően konfigurált feladatdefiníció StorSimple adatkezelő erőforráscsoporton belül.
*   Minden a szükséges dll-fájl. Töltse le a DLL-eket a [GitHub-tárházban](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)a GitHub-tárházban parancsfájlt.

## <a name="step-by-step-procedure"></a>Lépésről lépésre

A következő lépésekkel .NET használatával a data transformation feladat elindítása.

1. Beolvasni a konfigurációs paraméterek, tegye a következőket:
    1. Töltse le a `Get-ConfigurationParams.ps1` GitHub-tárház parancsprogramból a `C:\DataTransformation` helyét.
    1. Futtassa a `Get-ConfigurationParams.ps1` a GitHub-tárházban parancsfájlt. Írja be a következő parancsot:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        A ActiveDirectoryKey és AppName átadhatók az értékeket.

2. Ez a parancsfájl kimenete a következő értékeket:
    * Ügyfél-azonosító
    * Bérlőazonosító
    * Active Directory-kulcs (ugyanaz, mint a fent megadott)
    * Előfizetés azonosítója

        ![Konfigurációs paraméterek parancsfájl kimenetében](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Visual Studio 2012 használ, 2013 vagy 2015, hozzon létre egy C# .NET konzolalkalmazást.

    1. Indítsa el **Visual Studio 2012 2013 vagy2015/**.
    1. Válassza ki **fájl > Új > projekt**.

        ![1-projekt létrehozása](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Válassza ki **telepítve > sablonok > Visual C# > Konzolalkalmazás**.
    3. Adja meg **DataTransformationApp** a a **neve**.
    4. Válassza ki **C:\DataTransformation** a a **hely**.
    6. A projekt létrehozásához kattintson az **OK** gombra.

        ![2-projekt létrehozása](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4.  Ezután adja hozzá az összes DLL-fájl megtalálható a [DLL-ek mappa](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) , **hivatkozások** a létrehozott projektben. A dll-fájlok hozzáadásához tegye az alábbiakat:

    1. A Visual Studio Ugrás **Nézet > Megoldáskezelőben**.
    2. Kattintson a Data Transformation alkalmazásprojektet balra mutató nyílra. Kattintson a **hivatkozások** , és kattintson a jobb gombbal a **hivatkozás hozzáadása**.
    
        ![Adja hozzá a dll-EK 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

    3. Keresse meg a helyet, a csomagok mappa, válassza ki az összes DLL-fájl, és kattintson a **Hozzáadás**, és kattintson a **OK**.

        ![Adja hozzá a 2 dll](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adja hozzá az alábbi **using** utasításokat a projekt forrásfájljához (Program.cs).

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. A következő kódot az átalakítási feladat példánya inicializál. Adja hozzá ezt a a **fő metódus**. Konfigurációs paraméterek változtatása korábban beszerzett. Beépülő modul értékének **erőforráscsoport-név** és **ResourceName**. A **ResourceGroupName** a kapcsolódó, a StorSimple adatkezelő feladatdefinícióban konfigurálása. A **ResourceName** a StorSimple adatkezelő szolgáltatás neve.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Adja meg a paramétereket, amellyel a feladat definíciójához futtatnia kell

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (VAGY)

    Ha a feladat definíciójának paramétereit futásidőben módosítani kívánja, majd adja hozzá a következő kódot:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Az inicializálás után adja hozzá a következő kódot a feladat definíciójához a data transformation feladatot indít. Csatlakoztassa a megfelelő **feladatdefiníció nevét**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    A beillesztett a kódot, ha a megoldás felépítéséhez. Íme egy Képernyőkép a kódrészletet az átalakítási feladat példánya inicializálása.

   ![Adatok átalakítása feladat inicializálása kódrészletet](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Ez a feladat átalakítja az adatokat, amely megfelel a gyökérkönyvtár, és a fájl szűrők belül a StorSimple-kötet, és elhelyezi azokat a megadott tárolófájl-megosztás. Amikor egy fájl alakította, egy üzenet tárolási üzenetsorból (ugyanazt a tárfiókot, a tároló vagy fájlmegosztás) a neve megegyezik a feladat definíciójához kerül. Ez az üzenet kezdeményezheti a fájl további feldolgozás egy eseményindító is használható.

10. Ha a feladat lett elindítva, az alábbi kód segítségével nyomon követheti a feladat befejezésére. Nincs kötelező ez a feladat futtatása kódját hozzáadni.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
 Íme egy Képernyőkép a a teljes kódminta, a .NET használatával feladat elindítása a használatával.

 ![Teljes kódrészletét .NET feladatot indít](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>További lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md).
