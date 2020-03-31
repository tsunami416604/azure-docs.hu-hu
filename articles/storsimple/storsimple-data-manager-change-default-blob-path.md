---
title: A blob elérési útjának módosítása az alapértelmezett
description: Megtudhatja, hogy miként állíthat be egy Azure-függvényt a blobfájl elérési útjának átnevezéséhez
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 5ba1709ae195631371e4ea72667ba9b2a4bf279e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76270631"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Blob elérési útjának módosítása az alapértelmezett elérési útról

Amikor a StorSimple Data Manager szolgáltatás átalakítja az adatokat, alapértelmezés szerint az átalakított blobokat egy tárolótárolóba helyezi a céltár létrehozása során megadott módon. Ahogy a blobok érkeznek erre a helyre, érdemes lehet áthelyezni ezeket a blobokat egy másik helyre. Ez a cikk ismerteti, hogyan állíthat be egy Azure-függvényt egy alapértelmezett blobfájl elérési útjának átnevezéséhez, és így a blobok áthelyezése egy másik helyre.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik egy megfelelően konfigurált feladatdefinícióval a StorSimple Data Manager szolgáltatásban.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Azure-függvény létrehozásához hajtsa végre a következő lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com/)

2. Kattintson **a + Erőforrás létrehozása gombra.** A **Keresőmezőbe** írja be a **Function App** billentyűt, és nyomja le az **Enter billentyűt.** A megjelenített alkalmazások listájában válassza a **Függvény alkalmazást,** és kattintson a elemre.

    ![Írja be a "Function App" kifejezést a Keresőmezőbe](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kattintson **a Létrehozás gombra.**

    ![A Függvényalkalmazás ablak "Létrehozás" gomb](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. A **Function App** konfigurációs panelen hajtsa végre a következő lépéseket:

    1. Adjon meg egy egyedi **alkalmazásnevet.**
    2. A legördülő listában válassza az **Előfizetés**lehetőséget. Ennek az előfizetésnek meg kell egyeznie a StorSimple Data Manager szolgáltatáshoz társított előfizetéssel.
    3. Válassza az Új erőforráscsoport **létrehozása lehetőséget.**
    4. A **Tárhelycsomag** legördülő listához válassza a **Felhasználási terv**lehetőséget.
    5. Adja meg azt a helyet, ahol a függvény fut. Azt szeretné, hogy ugyanazt a régiót, ahol a StorSimple Data Manager szolgáltatás és a feladatdefinícióhoz társított tárfiók található.
    6. Válasszon meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot. A függvény belső tárolófiókja.

        ![Új függvényalkalmazás konfigurációs adatainak megadása](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kattintson **a Létrehozás gombra.** A függvényalkalmazás létrejön.
     
        ![Függvényalkalmazás létrehozva](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Válassza a **Funkciók**lehetőséget, majd kattintson **a + Új függvény gombra.**

    ![Kattintson + Új funkció](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Válassza a **C#** lehetőséget a nyelvhez. A sabloncsempék tömbjében válassza a **C#** elemet a **QueueTrigger-CSharp** csempén.

7. A **várólista eseményindítójában:**

    1. Adja meg a függvény **nevét.**
    2. A **Várólista neve** mezőbe írja be az adatátalakítási feladatdefiníció nevét.
    3. A **Tárfiók-kapcsolat**csoportban kattintson az **új**gombra. A tárfiókok listájából válassza ki a feladatdefinícióhoz társított fiókot. Jegyezze fel a kapcsolat nevét (kiemelve). A név az Azure függvény későbbi részében szükséges.

        ![Új C# függvény létrehozása](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kattintson **a Létrehozás gombra.** Létrejön **a függvény.**

     
10. A Függvény ablakban futtassa a _.csx_ fájlt.

    ![Új C# függvény létrehozása](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
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

    2. Cserélje le **STORAGE_CONNECTIONNAME** 11-es vonalon a tárfiók-kapcsolatra (lásd a 7c. lépést).

        ![Tárolókapcsolat nevének másolása](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Mentse** a funkciót.

        ![Mentés funkció](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. A funkció végrehajtásához adjon hozzá még egy fájlt az alábbi lépésekkel:

    1. Kattintson **a Fájlok megtekintése gombra.**

       ![A "Fájlok megtekintése" hivatkozás](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kattintson a **+Hozzáadás** gombra.
        
        ![A "Fájlok megtekintése" hivatkozás](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Írja be **a project.json ,** majd az **Enter billentyűt.** A **project.json** fájlba illessze be a következő kódot:

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

    
    4. Kattintson a **Mentés** gombra.

        ![A "Fájlok megtekintése" hivatkozás](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Létrehozott egy Azure-függvényt. Ez a függvény minden alkalommal aktiválódik, amikor az adatátalakítási feladat új blobot hoz létre.

## <a name="next-steps"></a>További lépések

[Az adatok átalakítása a StorSimple Data Manager felhasználói felületén](storsimple-data-manager-ui.md)
