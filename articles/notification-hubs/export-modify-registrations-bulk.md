---
title: Exportálása és importálása az Azure Notification Hubs regisztrációk tömeges |} A Microsoft Docs
description: Ismerje meg, hogyan használja a Notification hubs szolgáltatás tömeges támogatási egy értesítési központ műveletek nagy számú végrehajtásához, illetve minden regisztrációk exportálása.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: c24fcd5f007b641bb594bb07348491f70c03ea41
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469131"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Exportálása és importálása az Azure Notification Hubs regisztrációk tömeges
Vannak olyan forgatókönyvek, amelyben létrehozása vagy módosítása egy értesítési központ regisztrációk nagy számú van szükség. Ezek a forgatókönyvek némelyike címke frissítések kötegelt számításokat a következő, vagy a Notification Hubs használata leküldéses meglévő implementációjával áttelepítés.

Ez a cikk bemutatja, hogyan hajtsa végre egy értesítési központ műveletek nagy számú, vagy minden regisztrációk tömeges exportálása.

## <a name="high-level-flow"></a>Magas szintű folyamat
Batch támogatás célja a regisztrációk millió hosszú ideig futó feladatok támogatása. Méretezhetőség, a batch támogatás használja az Azure Storage feladat részleteinek és kimenetének tárolásához. A tömeges frissítés műveletek a felhasználó nem szükséges, hozzon létre egy fájlt a blobtárolóba, amelynek tartalma látható regisztráció-frissítési műveletek listája. A feladat indításakor a felhasználó megadja a bemeneti blob és a egy URL-címet a kimeneti könyvtárba (is a blobtárolóban) URL-CÍMÉT. Miután a feladat elindult, a felhasználó állapota ellenőrizhető egy URL-címét adja meg a feladat kezdési lekérdezésével. Egy adott feladat csak az egy adott típusú műveleteket hajthat végre (hoz létre, frissít vagy törli). Exportálási műveleteket adatproblémák.

## <a name="import"></a>Importálás

### <a name="set-up"></a>Beállítás
Ez a szakasz feltételezi, hogy a következő entitásokat:

- A kiépített értesítési központban.
- Egy Azure Storage blob-tárolóba.
- A hivatkozik a [Azure Storage NuGet-csomagot](https://www.nuget.org/packages/windowsazure.storage/) és [Notification Hubs NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.9).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Hozzon létre a bemeneti fájlt, és tárolja a blob
Egy bemeneti fájlt az XML-ben, egy sor egy szerializált regisztrációk listáját tartalmazza. Az Azure SDK-val, az alábbi példakód bemutatja, hogyan szerializálni a regisztrációkat, és feltölteni őket a blob-tárolóba.

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
> A fenti kód szerializálja a regisztrációk a memóriában, és ezután feltölti a teljes streamet az egy blobot. Ha több mint néhány (MB) fájlt töltött fel, tekintse meg az Azure blob-útmutatót hogyan végezheti el ezeket a lépéseket; Ha például [blokkblobok](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>A jogkivonatok URL-cím létrehozása
A bemeneti fájl feltöltését követően hozzon létre a bemeneti fájl és a kimeneti könyvtárat az értesítési központ biztosítania az URL-címeket. A bemeneti és kimeneti két másik blob-tárolót is használhatja.

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
A két bemeneti és kimeneti URL-címekkel rendelkező most már megkezdheti a batch-feladat.

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

A bemeneti és kimeneti URL-címek mellett ez a példa létrehoz egy `NotificationHubJob` objektum, amely tartalmaz egy `JobType` objektum, amely a következők egyike lehet:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Miután a hívás befejeződött, a feladat most az értesítési központ által, és ellenőrizheti annak állapotát a hívást a [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

A feladat befejezése után vizsgálja meg az eredmények megtekintésével a következő fájlokat a kimeneti könyvtár:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Ezek a fájlok a batch sikeres és sikertelen műveleteinek listáját tartalmazzák. A fájl formátuma `.cvs`, mely minden egyes sor nullértékkel rendelkezik, a sor száma, a bemeneti fájlt, és a kimenet a művelet (általában a létrehozott vagy frissített regisztrációs leírása) a.

### <a name="full-sample-code"></a>A teljes minta kódja
Az alábbi mintakód egy értesítési központ importálja a regisztrációk.

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
Regisztrációs exportálása hasonlít az importálás a következő eltérésekkel:

- A kimeneti URL-cím csak kell.
- Létrehozhat egy NotificationHubJob ExportRegistrations típusú.

### <a name="sample-code-snippet"></a>Példa kódtöredékek
Itt látható egy minta kódrészlet, a Java regisztrációk exportálása:

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
Regisztrációk kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md)
- [Regisztrációs címkék](notification-hubs-tags-segment-push-message.md)
- [Sablonregisztrációk](notification-hubs-templates-cross-platform-push-messages.md)
