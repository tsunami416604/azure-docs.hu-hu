---
title: A .NET SDK használata a Microsoft Azure StorSimple Data Manager-feladatok |} A Microsoft Docs
description: Ismerje meg, hogyan indítsa el a StorSimple Data Manager-feladatok a .NET SDK használatával
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
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885050"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Adatátalakítás kezdeményezni a .NET SDK használata

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használhatja a StorSimple Data Manager szolgáltatásban az adatok átalakítása funkció a StorSimple eszköz adatok átalakításához. Az átalakított adatok majd használja fel más Azure-szolgáltatások a felhőben.

Egy Adatátalakítási feladatot kétféle módon indíthatja el:

- A .NET SDK használata
- Használja az Azure Automation-runbook
 
  Ez a cikk részletesen, hogyan hozhat létre a mintául szolgáló .NET-Konzolalkalmazás kezdeményezéséhez egy Adatátalakítási feladatot, és nyomon követheti azt a végrehajtására. Automation-n keresztül átalakítását indítása kapcsolatos további tudnivalókért keresse fel [eseményindító Adatátalakítási feladatok használata az Azure Automation runbook](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:
*   Rendszert futtató számítógép:

    - A Visual Studio 2012, 2013, 2015 vagy 2017.

    - Azure PowerShell-lel. [Az Azure Powershell letöltése](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Egy megfelelően konfigurált feladatdefiníció StorSimple Data Manager erőforráscsoporton belül.
*   Az összes szükséges dll. Töltse le a DLL-eket a [GitHub-adattár](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) a szkript a GitHub-adattárból.

## <a name="step-by-step-procedure"></a>Lépésről lépésre

Hajtsa végre az alábbi lépéseket egy Adatátalakítási feladatot indítsa el a .NET használatával.

1. A konfigurációs paramétereket lekéréséhez tegye a következőket:
    1. Töltse le a `Get-ConfigurationParams.ps1` a GitHub adattár parancsfájl `C:\DataTransformation` helyét.
    1. Futtassa a `Get-ConfigurationParams.ps1` parancsfájlt a GitHub-adattárból. Írja be a következő parancsot:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        A ActiveDirectoryKey és AppName továbbíthatja bármely értékeket.

2. Ez a parancsfájl kimenete a következő értékeket:
    * Ügyfél-azonosító
    * Bérlőazonosító
    * Az Active Directory-kulcs (ugyanaz, mint a fent megadott)
    * Előfizetés azonosítója

        ![Konfigurációs paraméterek parancsprogram kimenete](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Használja a Visual Studio 2012, 2013 vagy 2015, hozzon létre egy C# .NET-konzolalkalmazást.

    1. Indítsa el a **Visual Studio 2012/2013/2015**.
    1. Válassza a **Fájl > Új projekt** lehetőséget.

        ![Hozzon létre egy projektet 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Válassza ki **telepített > sablonok > Visual C# > Konzolalkalmazás**.
    3. Adja meg **DataTransformationApp** számára a **neve**.
    4. Válassza ki **C:\DataTransformation** számára a **hely**.
    6. A projekt létrehozásához kattintson az **OK** gombra.

        ![Hozzon létre egy projektet 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Ezután adja hozzá az összes DLL-fájl megtalálható a [DLL-ek mappa](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) , **hivatkozások** a létrehozott projekt. Adja hozzá a DLL-fájlokat, hajtsa végre a következőket:

   1. A Visual Studióban nyissa meg **Nézet > Megoldáskezelőben**.
   2. Kattintson a Data Transformation App-projekt melletti nyílra. Kattintson a **hivatkozások** , és kattintson a jobb gombbal a **hivatkozás hozzáadása**.
    
       ![Adja hozzá a dll-EK 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Keresse meg a packages mappa helyét, válassza ki a DLL fájlok, kattintson a **Hozzáadás**, és kattintson a **OK**.

       ![Adja hozzá a DLL-ek 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adja hozzá az alábbi **using** utasításokat a projekt forrásfájljához (Program.cs).

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. A következő kódot az átalakítási feladat példány inicializálja. Adja hozzá ezt a a **Main metódushoz**. Cserélje le a konfigurációs paraméterek értékeit, korábban szerzett be. Beépülő modul értékeit **erőforráscsoport-név** és **ResourceName**. A **ResourceGroupName** a kapcsolódó az a StorSimple Data Manager, amelyen a feladat definíciója adható meg lett konfigurálva. A **ResourceName** a StorSimple Data Manager szolgáltatás neve.

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
   
7. Adja meg a paramétereket, amellyel a feladat definíciója adható meg kell futtatni

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (OR)

    Ha azt szeretné, a feladat definíciója paraméterek módosításához futtatási idő alatt, majd adja hozzá a következő kódot:

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

8. Az inicializálása után adja hozzá a következő kódot a-trigger egy Adatátalakítási feladatot a feladat definíciója adható meg. Beépülő modul a megfelelő **feladatdefiníció neve**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    A kód beillesztése, miután a megoldás felépítéséhez. Íme egy Képernyőkép a kódrészletet az átalakítási feladat példány inicializálásához.

   ![Fragment kódu Adatátalakítási feladatot inicializálása](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Ez a feladat alakítja át az adatokat, amely megfelel a gyökérkönyvtár, és a fájl szűri a StorSimple-kötet belül, és elhelyezi azokat a megadott tárolófájl-megosztás. Ha egy fájl alakította, egy üzenet bekerül egy tárolási sorba, (az ugyanabban a tárfiókban, a tároló-vagy fájlmegosztás) neve megegyezik a feladatdefiníció. Ez az üzenet kezdeményezni a fájl további feldolgozás eseményindítóként is használható.

10. Miután a feladat elindult, a következő kód segítségével nyomon követheti a feladat befejezésére. Esetén nem kötelező, ez a feladat futtatása a kód hozzáadása.

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
    Íme egy Képernyőkép a teljes kódmintát a .NET használatával feladat indítható.

    ![Teljes kódrészletet elindítható egy .NET-feladat](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>További lépések

[Használja a StorSimple Data Manager felhasználói Felületét, hogy az adatok átalakítása](storsimple-data-manager-ui.md).
