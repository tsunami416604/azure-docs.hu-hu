---
title: Az Azure Notification Hubs regisztrációinak exportálása és importálása tömegesen | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Értesítési központok tömeges támogatását nagyszámú művelet végrehajtásához egy értesítési központban, vagy exportálhatja az összes regisztrációt.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212397"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Az Azure Notification Hubs regisztrációinak exportálása és importálása tömegesen
Vannak olyan forgatókönyvek, amelyekben nagy számú regisztrációt kell létrehozni vagy módosítani egy értesítési központban. Ezek a forgatókönyvek a címke frissítések et követően kötegelt számítások, vagy áttelepítése egy meglévő leküldéses megvalósítási értesítési központok használatához.

Ez a cikk bemutatja, hogyan hajthatja végre a nagyszámú műveletet egy értesítési központ, vagy exportálja az összes regisztrációk, ömlesztve.

## <a name="high-level-flow"></a>Magas szintű áramlás
A kötegtámogatás több millió regisztrációval járó, hosszú ideig futó feladatok támogatására szolgál. A méretezés eléréséhez a kötegelt támogatás az Azure Storage-t használja a feladat részleteinek és kimenetének tárolására. Tömeges frissítési műveletek esetén a felhasználónak létre kell hoznia egy fájlt egy blobtárolóban, amelynek tartalma a regisztrációs frissítési műveletek listája. A feladat indításakor a felhasználó egy URL-címet biztosít a bemeneti blob, valamint egy URL-t egy kimeneti könyvtár (is egy blob tárolóban). A feladat elindítása után a felhasználó ellenőrizheti az állapotot a feladat indításakor megadott URL-cím lekérdezésével. Egy adott feladat csak meghatározott típusú műveleteket hajthat végre (létrehoz, frissít vagy töröl). Az exportálási műveletek et hasonlóan hajtják végre.

## <a name="import"></a>Importálás

### <a name="set-up"></a>Beállítás
Ez a szakasz feltételezi, hogy a következő entitásokkal rendelkezik:

- Egy kiépített értesítési központ.
- Egy Azure Storage blob tároló.
- Hivatkozások az [Azure Storage NuGet csomagra](https://www.nuget.org/packages/windowsazure.storage/) és az [Értesítési központok NuGet csomagjára.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

### <a name="create-input-file-and-store-it-in-a-blob"></a>Bemeneti fájl létrehozása és tárolása blobban
A bemeneti fájl tartalmazza az XML-ben szerializált regisztrációk listáját, soronként egyet. Az Azure SDK használatával a következő kód példa bemutatja, hogyan szerializálhatja a regisztrációkat, és töltse fel őket a blob tárolóba.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Az előző kód szerializálja a regisztrációkat a memóriában, majd feltölti a teljes adatfolyamot egy blobba. Ha néhány megabájtnál többet meghaladó fájlt töltött fel, tekintse meg az Azure blob útmutatóját a lépések végrehajtásához; például [blokkblobok](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>URL-tokenek létrehozása
A bemeneti fájl feltöltése után hozza létre az URL-eket, hogy az értesítési központ számára mind a bemeneti fájlt, mind a kimeneti könyvtárat biztosítsa. Két különböző blobtárolót használhat a bemenethez és a kimenethez.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Feladat küldése
A két bemeneti és kimeneti URL-címekkel most már elindíthatja a kötegelt feldolgozást.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

A bemeneti és kimeneti URL-ek mellett `NotificationHubJob` ez a `JobType` példa egy objektumot is tartalmazó objektumot hoz létre, amely a következő típusok egyike lehet:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

A hívás befejezése után a feladatot az értesítési központ folytatja, és ellenőrizheti annak állapotát a [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet)hívással.

A feladat befejezésekor megvizsgálhatja az eredményeket a kimeneti könyvtárban található következő fájlok megtekintésével:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Ezek a fájlok tartalmazzák a kötegből származó sikeres és sikertelen műveletek listáját. A fájlformátum `.cvs`az , amelyben minden sor az eredeti bemeneti fájl sorszáma és a művelet kimenete (általában a létrehozott vagy frissített regisztrációs leírás).

### <a name="full-sample-code"></a>Teljes mintakód
A következő mintakód a regisztrációkat egy értesítési központba importálja.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exportálás
A nyilvántartásba vétel exportálása hasonló az importáláshoz, a következő különbségekkel:

- Csak a kimeneti URL-címre van szüksége.
- Hozzon létre egy NotificationHubJob típusú ExportRegistrations.

### <a name="sample-code-snippet"></a>Mintakódrészlet
Itt van egy minta kódrészlet a java-regisztrációk exportálásához:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>További lépések
A regisztrációkról az alábbi cikkekben olvashat bővebben:

- [Regisztráció kezelése](notification-hubs-push-notification-registration-management.md)
- [Regisztrációs címkék](notification-hubs-tags-segment-push-message.md)
- [Sablonregisztrációk](notification-hubs-templates-cross-platform-push-messages.md)
