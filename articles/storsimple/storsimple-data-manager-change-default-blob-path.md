---
title: "Az alapértelmezett blob útvonal módosítása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Azure függvény nevezze át a blob-fájl elérési útja"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: f73d9dcedee5165af752b9e10fb70de860e8e98b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="change-a-blob-path-from-the-default-path"></a>Egy blob elérési utat módosítsa az alapértelmezett elérési útjáról

A StorSimple adatkezelő szolgáltatás átalakítja az adatokat, ha alapértelmezés szerint helyezi az átalakított blobot a megadott tárolót a cél-tárház létrehozása során. A blobok érkezésekor ezen a helyen, érdemes lehet a blobok áthelyezése egy másik helyre. A cikkből megtudhatja, hogyan állíthat be egy Azure működnek, nevezze át egy alapértelmezett blob fájl elérési útját, és ezért a blobok áthelyezése egy másik helyre.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik-e a megfelelően konfigurált feladatdefiníció a StorSimple Data Manager szolgáltatásban.

## <a name="create-an-azure-function"></a>Egy Azure-függvény létrehozása

Egy Azure függvény létrehozásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az [Azure Portal](http://portal.azure.com/).

2. Kattintson a **+ hozzon létre egy erőforrást**. Az a **keresési** mezőbe írja be **függvény App** nyomja le az ENTER **Enter**. Válassza ki, és kattintson a **függvény app** megjelenített alkalmazások listája.

    ![A keresőmezőbe írja be a "Függvény alkalmazás"](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Kattintson a **Create** (Létrehozás) gombra.

    ![A függvény App ablak "Létrehozás" gombra](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Az a **függvény App** konfigurációs panelen, hajtsa végre a következő lépéseket:

    1. Adjon meg egy egyedi **alkalmazásnév**.
    2. A legördülő listából válassza ki a **előfizetés**. Ez az előfizetés ugyanaz, mint a StorSimple adatokat kezelő szolgáltatással kapcsolatos kell lennie.
    3. Válassza ki **hozzon létre új** erőforráscsoportot.
    4. Az a **üzemeltetési terv** legördülő listában válassza ki **fogyasztás megtervezése**.
    5. Adjon meg egy helyet, ahol a függvény futásakor. Azt szeretné, hogy ugyanabban a régióban, ahol a StorSimple adatkezelő szolgáltatás és a tárfiókot, a feladat definíciójához társított találhatók.
    6. Válasszon meglévő Storage-fiókot, vagy hozzon létre egy új Storage-fiókot. A tárfiók belső használatra készült a funkciót a.

        ![Adja meg az új függvény alkalmazás-konfigurációs adatokat](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Kattintson a **Create** (Létrehozás) gombra. A függvény alkalmazás létrehozása.
     
        ![Függvény-alkalmazás létrehozása](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Válassza ki **funkciók**, és kattintson a **+ új függvény**.

    ![Kattintson a kívánt új funkció](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Válassza ki **C#** a nyelvhez. A tömb sablon csempék, válassza ki a **C#** a a **QueueTrigger-c Sharp** csempére.

7. Az a **várólista eseményindító**:

    1. Adjon meg egy **neve** a függvénynél.
    2. Az a **üzenetsornév** mezőbe írja be a data transformation feladatdefiníció nevét.
    3. A **fiók tárolókapcsolat**, kattintson a **új**. A storage-fiókok listában jelölje ki a feladat definíciójához tartozó fiókot. Jegyezze fel a kapcsolat neve (kiemelt). Később az Azure függvényben meg kell adni a nevet.

        ![Egy új C#-függvény létrehozása](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Kattintson a **Create** (Létrehozás) gombra. A **függvény** jön létre.

     
10. A függvény-ablakban futtassa _.csx_ fájlt.

    ![Egy új C#-függvény létrehozása](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    A következő lépésekkel.

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

    2. Cserélje le **STORAGE_CONNECTIONNAME** a sor a tárolási fiók kapcsolattal 11 (lásd 7/c. lépés).

        ![Másolja a tárolási kapcsolat neve](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Mentés** a függvényt.

        ![Függvény mentése](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. A függvény befejezéséhez adjon hozzá egy további fájlt a következő lépések végrehajtásával:

    1. Kattintson a **-fájlokat tekinthetnek meg**.

       ![A "-fájlokat tekinthetnek meg" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Kattintson a **+ Hozzáadás**.
        
        ![A "-fájlokat tekinthetnek meg" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Típus **project.json**, majd nyomja le az **Enter**. Az a **project.json** fájlt, az alábbi kódot:

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

        ![A "-fájlokat tekinthetnek meg" hivatkozásra](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Egy Azure függvény hozott létre. Ez a funkció akkor váltódik ki, minden alkalommal, amikor egy új blob az átalakítási feladat által generált.

## <a name="next-steps"></a>További lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md)
