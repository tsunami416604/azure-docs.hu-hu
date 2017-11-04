---
title: "A Microsoft Azure StorSimple adatkezelő feladatokhoz használhatja a .NET SDK |} Microsoft Docs"
description: "Útmutató a .NET SDK használatával indítsa el a StorSimple adatkezelő feladatok (magán előnézetben)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>A .net SDK használatával kezdeményezni a data transformation (magán előnézetben)

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használhatja a StorSimple adatkezelő szolgáltatáson belül az adatok átalakítása szolgáltatás StorSimple eszközön tárolt adatok átalakítását. Az átalakított adatok majd fel más Azure-szolgáltatásokkal a felhőben. A cikk a forgatókönyv segítségével hozzon létre egy minta .NET konzolalkalmazást kezdeményezéséhez egy adatok átalakítási feladat is rendelkezik, és nyomon követheti azt befejezésére.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy:
*   A rendszer a Visual Studio 2012, 2013, 2015-öt vagy telepített 2017.
*   Az Azure Powershell telepítése. [Töltse le az Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   A Data Transformation feladat inicializálni a konfigurációs beállításokat (a beállítások átvételéhez utasítások ide tartoznak).
*   A feladat definíciójához erőforráscsoporton belül egy hibrid adatforrás, melyhez a megfelelően konfigurált.
*   Minden a szükséges dll-fájl. Töltse le a DLL-eket a [GitHub-tárházban](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   `Get-ConfigurationParams.ps1`[parancsfájl](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) a github-tárházban lévő.

## <a name="step-by-step"></a>Lépésről lépésre

A következő lépésekkel .NET használatával a data transformation feladat elindítása.

1. Beolvasni a konfigurációs paraméterek, tegye a következőket:
    1. Töltse le a `Get-ConfigurationParams.ps1` github-tárház parancsprogramból a `C:\DataTransformation` helyét.
    1. Futtassa a `Get-ConfigurationParams.ps1` a github-tárházban parancsfájlt. Írja be a következő parancsot:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        A ActiveDirectoryKey és AppName átadhatók az értékeket.


2. Ez a parancsfájl kimenete a következő értékeket:
    * Ügyfél-azonosító
    * Bérlőazonosító
    * Active Directory-kulcs (ugyanaz, mint a fent megadott)
    * Előfizetés azonosítója

3. Visual Studio 2012 használ, 2013 vagy 2015, hozzon létre egy C# .NET konzolalkalmazást.

    1. Indítsa el **Visual Studio 2012 2013 vagy2015/**.
    1. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre.
    2. Bontsa ki a **Sablonok** lehetőséget, és válassza a **Visual C#** lehetőséget.
    3. A projekttípusok jobb oldalon megjelenő listájában válassza a **Konzolalkalmazás** elemet.
    4. Adja meg **DataTransformationApp** a a **neve**.
    5. Válassza ki **C:\DataTransformation** a a **hely**.
    6. A projekt létrehozásához kattintson az **OK** gombra.

4.  Ezután adja hozzá az összes DLL-fájl megtalálható a [DLL-ek](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) mappában **hivatkozások** a létrehozott projektben. A dll-fájlok letöltéséhez, tegye a következőket:

    1. A Visual Studio Ugrás **Nézet > Megoldáskezelőben**.
    1. Kattintson a Data Transformation alkalmazásprojektet balra mutató nyílra. Kattintson a **hivatkozások** , és kattintson a jobb gombbal a **hivatkozás hozzáadása**.
    2. Keresse meg a helyet, a csomagok mappa, válassza ki az összes DLL-fájl, és kattintson a **Hozzáadás**, és kattintson a **OK**.

5. Adja hozzá az alábbi **using** utasításokat a projekt forrásfájljához (Program.cs).

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. A következő kódot az átalakítási feladat példánya inicializál. Adja hozzá ezt a a **fő metódus**. Konfigurációs paraméterek változtatása korábban beszerzett. Beépülő modul értékének **erőforráscsoport-név** és **hibrid adatok erőforrásnév**. A **erőforráscsoport-név** az, amelyet a hibrid adatforrás, melyhez a feladat definíciójához konfigurálása üzemelteti.

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

    ```

9. Ez a feladat a StorSimple-kötet a gyökérkönyvtárban található egyező fájlok feltöltése a megadott tárolóhoz. Ha egy fájl feltöltése, egy üzenet megszakad a várólista (a tárfiókon, a tároló) a neve megegyezik a feladat definíciójához. Ez az üzenet kezdeményezheti a fájl további feldolgozás egy eseményindító is használható.

10. Ha a feladat lett elindítva, adja hozzá a következő kódot a nyomon követheti a feladat befejezésére.

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


## <a name="next-steps"></a>Következő lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md).
