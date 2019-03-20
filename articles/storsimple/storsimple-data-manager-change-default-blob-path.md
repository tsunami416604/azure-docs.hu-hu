---
title: Az alapértelmezett blob útvonal módosítása |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be egy Azure-függvényt egy blob elérési útja átnevezése
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
ms.openlocfilehash: cdaf991c25c23dee4f87b44142c1482bf892bcf2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011627"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Egy blob elérési utat módosítsa az alapértelmezett elérési útjáról

A StorSimple Data Manager szolgáltatás alakítja át az adatokat, ha alapértelmezés szerint helyezi az átalakított blobokat egy storage-tárolóban megadott a céladattárban létrehozása során. A blobok érkezésekor ezen a helyen, érdemes áthelyezni az ilyen blobok másik helyre. Ez a cikk ismerteti, hogyan állítható be egy Azure-függvényt egy alapértelmezett blob elérési útja átnevezése, és ezért a blobok áthelyezése egy másik helyre.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik-e egy megfelelően konfigurált feladatdefiníció a StorSimple Data Manager szolgáltatásban.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Hozzon létre egy Azure-függvényt, hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [Azure Portal](https://portal.azure.com/).

2. Kattintson a **+ erőforrás létrehozása**. Az a **keresési** mezőbe írja be **Függvényalkalmazás** nyomja le az ENTER **Enter**. Válassza ki, és kattintson a **függvényalkalmazás** a megjelenített alkalmazások listája.

    ![A keresőmezőbe írja be a "Függvényalkalmazás"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kattintson a **Create** (Létrehozás) gombra.

    ![A Függvényalkalmazás ablakban "Létrehozás" gombra](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Az a **Függvényalkalmazás** konfigurációs panelen hajtsa végre az alábbi lépéseket:

    1. Adjon meg egy egyedi **alkalmazásnév**.
    2. A legördülő listából válassza ki a **előfizetés**. Ez az előfizetés ugyanaz, mint az a StorSimple Data Manager szolgáltatással kapcsolatos kell lennie.
    3. Válassza ki **új létrehozása** erőforráscsoportot.
    4. Az a **csomag** legördülő listában válassza **Használatalapú csomagban**.
    5. Adjon meg egy helyet, ahol a függvény futásakor. Azt szeretné, hogy ugyanabban a régióban, ahol a StorSimple Data Manager szolgáltatás és a feladatdefiníciót társított storage-fiók találhatók.
    6. Válasszon meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot. A tárfiók belső használatú a függvényt.

        ![Új Függvényalkalmazás-konfigurációs adatok megadása](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kattintson a **Create** (Létrehozás) gombra. A függvényalkalmazás létrehozása.
     
        ![Függvényalkalmazás létrehozása](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Válassza ki **funkciók**, és kattintson a **+ új funkció**.

    ![Kattintson a + új funkció](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Válassza ki **C#** a nyelvhez. Válassza ki a sablont a csempék a tömbben, **C#** a a **QueueTrigger-CSharp** csempére.

7. Az a **üzenetsor eseményindító**:

    1. Adjon meg egy **neve** a függvény.
    2. Az a **üzenetsornév** mezőbe írja be a data transformation feladatdefiníció neve.
    3. A **Tárfiók kapcsolata**, kattintson a **új**. Storage-fiókok listájából válassza ki a feladatdefiníció tartozó fiókot. Jegyezze fel a kapcsolat neve (kiemelve). A név később az Azure-függvény szükséges.

        ![Hozzon létre egy új C# függvény](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kattintson a **Create** (Létrehozás) gombra. A **függvény** jön létre.

     
10. A függvény ablakban futtassa _.csx_ fájlt.

    ![Hozzon létre egy új C# függvény](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Hajtsa végre a következő lépéseket.

    1. Illessze be a következő kódot:

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

    2. Cserélje le **STORAGE_CONNECTIONNAME** a sor a tárfiók kapcsolata 11 (lásd 7 c. lépés).

        ![Másolja ki a storage-kapcsolat neve](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Mentés** a függvényt.

        ![Függvény mentése](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Végezze el a függvényt, adjon hozzá egy további fájlt a következő lépések végrehajtásával:

    1. Kattintson a **fájlok megtekintését**.

       ![A "Fájlok megtekintése" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kattintson a **+Hozzáadás** gombra.
        
        ![A "Fájlok megtekintése" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Típus **project.json**, és nyomja le az **Enter**. Az a **project.json** fájlt, az alábbi kódot:

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

    
    4. Kattintson a **Save** (Mentés) gombra.

        ![A "Fájlok megtekintése" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Létrehozott egy Azure-függvényt. Ez a funkció akkor aktiválódik, minden alkalommal, amikor az átalakítási feladat által létrehozott új blob.

## <a name="next-steps"></a>További lépések

[Használja a StorSimple Data Manager felhasználói Felületét, hogy az adatok átalakítása](storsimple-data-manager-ui.md)
