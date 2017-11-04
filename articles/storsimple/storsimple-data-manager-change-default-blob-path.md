---
title: "Az alapértelmezett blob útvonal módosítása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Azure függvény nevezze át a blob elérési út (magán előnézetben)"
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
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Egy blob elérési utat módosítsa az alapértelmezett elérési úttal (magán előnézetben)

A cikkből megtudhatja, hogyan állíthat be egy Azure függvény átnevezése egy alapértelmezett blob fájl elérési útját. 

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik-e olyan feladatdefinícióban erőforráscsoporton belül egy hibrid adatforrás, melyhez a megfelelően konfigurált.

## <a name="create-an-azure-function"></a>Egy Azure-függvény létrehozása

Egy Azure-függvény létrehozása, tegye a következőket:

1. Nyissa meg az [Azure Portal](http://portal.azure.com/).

2. Kattintson a bal oldali ablaktábla tetején **új**. 

3. Az a **keresési** mezőbe írja be **függvény App**, és nyomja le az ENTER billentyűt.

    ![A keresőmezőbe írja be a "Függvény alkalmazás"](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. Az a **eredmények** listában, kattintson **függvény App**.

    ![Válassza ki a függvény alkalmazás-erőforrást az eredménylistában](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    A **függvény App** ablak nyílik meg.

5. Kattintson a **Create** (Létrehozás) gombra.

    ![A függvény App ablak "Létrehozás" gombra](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. Az a **függvény App** konfigurációs panelen tegye a következőket:

    a. Az a **alkalmazásnév** mezőbe írja be az alkalmazás nevét.
    
    b. Az a **előfizetés** mezőbe írja be az előfizetés nevét.

    c. A **erőforráscsoport**, kattintson a **hozzon létre új**, és írja be az erőforráscsoport nevét.

    d. Az a **üzemeltetési terv** mezőbe írja be **fogyasztás megtervezése**.

    e. Az a **hely** mezőbe írja be a helyet.

    f. A **tárfiók**, válasszon egy meglévő tárfiókot, vagy hozzon létre egy új tárfiókot. A tárfiók belső használatra készült a funkciót a.

    ![Adja meg az új függvény alkalmazás-konfigurációs adatokat](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Kattintson a **Create** (Létrehozás) gombra.  
    A függvény alkalmazás létrehozása.

8. Kattintson a bal oldali ablaktáblában **további szolgáltatások**, majd tegye a következőket:
    
    a. Az a **szűrő** mezőbe írja be **alkalmazásszolgáltatások**.
    
    b. Kattintson a **alkalmazásszolgáltatások**. 

    ![A bal oldali ablaktáblán "Szolgáltatás" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. Az app Service szolgáltatások listájában kattintson a függvény alkalmazás nevét.  
    Megnyílik a függvény app lap.

10. Kattintson a bal oldali ablaktáblában **új függvény**, majd tegye a következőket: 

    a. Az a **nyelvi** listáról válassza ki **C#**.
    
    b. A tömb sablon csempék, válassza ki a **QueueTrigger-c Sharp**.

    c. Az a **a függvény neve** mezőbe írja be a függvény nevét.

    d. Az a **üzenetsornév** mezőbe írja be az adat-átalakítási feladat definition nevét.

    e. A **fiók tárolókapcsolat**, kattintson a **új**, majd válassza ki a fiókot, amely megfelel az adat-átalakítási feladat.  
        Jegyezze fel a kapcsolat neve. Később az Azure függvényben meg kell adni a nevet.

       ![Egy új C#-függvény létrehozása](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Kattintson a **Create** (Létrehozás) gombra.  
    A **függvény** ablak nyílik meg.

11. Az a **függvény** ablakban futtassa _.csx_ fájlt, és tegye a következőket:

    a. Illessze be a következő kódot:

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. Cserélje le **STORAGE_CONNECTIONNAME** a sor a tárolási fiók kapcsolattal 11 (lásd a pont 8 c).

    c. Kattintson a bal felső, **mentése**.

    ![Függvény mentése](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. A függvény befejezéséhez adjon hozzá egy további fájlt a következő módon:

    a. Kattintson a **-fájlokat tekinthetnek meg**.

       ![A "-fájlokat tekinthetnek meg" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Kattintson az **Add** (Hozzáadás) parancsra.
    
    c. Típus **project.json**, és nyomja le az ENTER billentyűt.
    
    d. Az a **project.json** fájlt, az alábbi kódot:

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. Kattintson a **Save** (Mentés) gombra.

Egy Azure függvény hozott létre. Ez a funkció akkor váltódik ki, minden alkalommal, amikor egy új blob létrejön az adat-átalakítási feladat által.

## <a name="next-steps"></a>Következő lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md)
