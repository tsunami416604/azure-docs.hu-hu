---
title: A .NET SDK használata a Microsoft Azure StorSimple Data Manager-feladatokhoz
description: A StorSimple Data Manager-feladatok indításához használható .NET SDK
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b7cf1d3b9d4a9d751348c4792f904062b00ac104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270722"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Adatátalakítás kezdeményezése a .NET SDK segítségével

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használhatja az adatátalakítási szolgáltatás a StorSimple Data Manager szolgáltatás storSimple eszközadatok átalakításához. Az átalakított adatokat ezután a felhőben más Azure-szolgáltatások is felhasználják.

Az adatátalakítási feladatot kétféleképpen indíthatja el:

- A .NET SDK használata
- Az Azure Automation runbook használata
 
  Ez a cikk bemutatja, hogyan hozhat létre egy minta .NET konzolalkalmazást egy adatátalakítási feladat kezdeményezéséhez, majd befejezésének nyomon követéséhez. Ha többet szeretne megtudni arról, hogyan kezdeményezhet adatátalakítást az Automation segítségével, olvassa el [az Azure Automation runbook használatával az adatátalakítási feladatok indítását](storsimple-data-manager-job-using-automation.md)című.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:
*   A következő:

    - Visual Studio 2012, 2013, 2015 vagy 2017.

    - Azure Powershell. [Töltse le az Azure Powershellt.](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
*   Megfelelően konfigurált feladatdefiníció a StorSimple Data Manager ben egy erőforráscsoporton belül.
*   Az összes szükséges dll. Töltse le ezeket a dll-eket a [GitHub-tárházból.](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)parancsfájlt a GitHub-tárházból.

## <a name="step-by-step-procedure"></a>Lépésről lépésre

Az adatátalakítási feladat elindításához hajtsa végre az alábbi lépéseket a .NET használatával.

1. A konfigurációs paraméterek beolvasásához tegye a következő lépéseket:
    1. Töltse `Get-ConfigurationParams.ps1` le a GitHub-tárház-parancsfájl helyből. `C:\DataTransformation`
    1. Futtassa a `Get-ConfigurationParams.ps1` parancsfájlt a GitHub-tárházból. Írja be a következő parancsot:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Az ActiveDirectoryKulcs és az AppName bármely értékét átadhatja.

2. Ez a parancsfájl a következő értékeket adja ki:
    * Ügyfél-azonosító
    * Bérlőazonosító
    * Active Directory kulcs (megegyezik a fent megadottakkal)
    * Előfizetés azonosítója

        ![Konfigurációs paraméterek parancsfájl kimenete](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. A Visual Studio 2012, 2013 vagy 2015 használatával hozzon létre egy C# .NET konzolalkalmazást.

    1. Indítsa el **a Visual Studio 2012/2013/2015 alkalmazást.**
    1. Válassza a **Fájl > Új projekt** lehetőséget.

        ![Projekt létrehozása 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Válassza **a Telepített > sablonok > Visual C# > konzolalkalmazás lehetőséget.**
    3. Írja be a **DataTransformationApp alkalmazást** a **névhez.**
    4. Válassza a **C:\DataTransformation lehetőséget** a **helyhez.**
    6. A projekt létrehozásához kattintson az **OK** gombra.

        ![Projekt létrehozása 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Most adja hozzá a [dlls mappában](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) lévő összes dll-t **hivatkozásként** a létrehozott projektben. A dll fájlok hozzáadásához hajtsa végre az alábbi műveleteket:

   1. A Visual Studióban nyissa meg **a > Solution Explorer nézetét.**
   2. Kattintson az Adatátalakítási alkalmazás projekttől balra lévő nyílra. Kattintson **a Hivatkozások parancsra,** majd kattintson a jobb gombbal a **Hivatkozás hozzáadása parancsra.**
    
       ![1. dll hozzáadása](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Tallózással keresse meg a csomagok mappa helyét, jelölje ki az összes dll-t, és kattintson a **Hozzáadás**gombra, majd kattintson az **OK**gombra.

       ![2. dll hozzáadása](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Adja hozzá az alábbi **using** utasításokat a projekt forrásfájljához (Program.cs).

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. A következő kód inicializálja az adatátalakítási feladatpéldányt. Adja hozzá ezt a **Fő metódushoz.** Cserélje le a korábban kapott konfigurációs paraméterek értékeit. Csatlakoztassa az **Erőforráscsoport neve** és az **Erőforrásnév**értékét. A **ResourceGroupName** az a StorSimple-adatkezelő, amelyen a feladatdefiníció konfigurálva volt. A **ResourceName** a StorSimple Data Manager szolgáltatás neve.

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
   
7. Adja meg azokat a paramétereket, amelyekkel a feladatdefiníciót futtatni kell

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (1)

    Ha a futási idő alatt módosítani szeretné a feladatdefiníció paramétereit, adja hozzá a következő kódot:

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

8. Az inicializálás után adja hozzá a következő kódot egy adatátalakítási feladat elindításához a feladatdefiníción. Csatlakoztassa a megfelelő **feladatdefiníciós nevet.**

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    A kód beillesztése után építse fel a megoldást. Itt van egy képernyőkép a kódrészlet inicializálásához az adatátalakítási feladat példány.

   ![Kódrészlet az adatátalakítási feladat inicializálásához](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Ez a feladat átalakítja a gyökérkönyvtárnak és a StorSimple-köteten belüli fájlszűrőknek megfelelő adatokat, és a megadott tároló/fájlmegosztásba helyezi azokat. Amikor egy fájl átalakításra kerül, egy üzenet kerül a tárolóvárólistába (ugyanabban a tárfiókban, mint a tároló/fájlmegosztás) a feladatdefinícióval azonos nevű. Ez az üzenet a fájl további feldolgozásának kezdeményezésére használható.

10. A feladat aktiválása után a következő kóddal nyomon követheti a feladat befejezését. Nem kötelező hozzáadni ezt a kódot a feladat futtatásához.

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
    Itt van egy képernyőkép a teljes kódmintáról, amely a .NET használatával indítja el a feladatot.

    ![Teljes kódrészlet a .NET feladat elindításához](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>További lépések

[Az adatok átalakításához használja a StorSimple Data Manager felhasználói felületét.](storsimple-data-manager-ui.md)
