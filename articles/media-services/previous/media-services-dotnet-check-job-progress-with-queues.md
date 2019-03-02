---
title: Azure Queue storage használata .NET-keretrendszerrel történő Media Services feladatértesítések figyelése |} A Microsoft Docs
description: Ismerje meg az Azure Queue storage használata a Media Services feladatértesítések figyelésére használt. A kódminta nyelven van megírva C# , és használja a Media Services SDK a .NET-hez.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 8bf2452afe3f9b11287848fb1e3880f7fac506ff
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243125"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Azure Queue storage használata .NET-keretrendszerrel történő Media Services feladatértesítések figyelése 

Kódolási feladatok futtatásakor gyakran igényelnek olyan módon, a feladat előrehaladásának nyomon követéséhez. Beállíthatja, hogy az értesítések a Media Services [Azure Queue storage](../../storage/storage-dotnet-how-to-use-queues.md). A feladat előrehaladásának figyelemmel értesítések fogadása a Queue storage-ból. 

A Queue storage üzenetet elérhetők bárhol a világon. A Queue storage üzenetkezelési architektúra, megbízható és nagy mértékben skálázható. A Queue storage üzenetek lekérdezési ajánlott egyéb módszerekhez képest.

A Media Services értesítéseket figyeli az egyik gyakori esetben egy tartalomkezelő rendszeren, amely kell végeznie néhány további tevékenység után fejleszt egy kódolási feladat befejeződik, (például, hogy a következő lépés egy munkafolyamatot, vagy pedig közzé eseményindító tartalom).

Ez a cikk bemutatja, hogyan tehet szert az értesítési üzenetek Queue storage-ból.  

## <a name="considerations"></a>Megfontolandó szempontok
A Queue storage használata a Media Services-alkalmazások fejlesztéséhez használható vegye figyelembe a következőket:

* A Queue storage nem biztosít garantálja az első-first out (FIFO) kézbesítési rendezve. További információkért lásd: [Azure-üzenetsorok és Azure Service Bus üzenetsorok képest és Contrasted](https://msdn.microsoft.com/library/azure/hh767287.aspx).
* A Queue storage nem egy leküldéses szolgáltatás. A várólista lekérdezésére rendelkezik.
* Tetszőleges számú üzenetsort rendelkezhet. További információkért lásd: [Queue szolgáltatás REST API](https://docs.microsoft.com/rest/api/storageservices/Queue-Service-REST-API).
* A Queue storage van néhány korlátozás és a részletekről érdemes figyelembe vennie. Ezekről az [Azure-üzenetsorok és Azure Service Bus üzenetsorok képest és Contrasted](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

## <a name="net-code-example"></a>.NET-példakód

Ebben a szakaszban a példakód a következőket teszi:

1. Meghatározza a **EncodingJobMessage** osztály, amely az értesítési üzenet formátumra. A kód deserializes objektumához be az üzenetsorból érkezett üzeneteket a **EncodingJobMessage** típusa.
2. A Media Services és a Storage-fiók adatait az app.config fájlt tölt be. A példakód ezen információk segítségével hozzon létre a **CloudMediaContext** és **CloudQueue** objektumokat.
3. Hoz létre, amely megkapja a kódolási feladat kapcsolatos értesítési üzeneteket az üzenetsorba.
4. A notification-végpontot a várólista leképezett hoz létre.
5. A notification végpont csatol a feladatot, és elküldi a kódolási feladatot. Akkor is csatlakozik egy feladat több értesítési végpontok.
6. Pass **NotificationJobState.FinalStatesOnly** , a **AddNew** metódust. (Ebben a példában tudjuk csak végső állapotok feladat feldolgozása iránt.)

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. Ha a felhasználó **NotificationJobState.All**, az összes alábbi állapot módosítási értesítést kap: az aszinkron, ütemezett, feldolgozási és befejeződött. Azonban mivel korábban feljegyzett a Queue storage nem garantálja a rendezett kézbesítési. Az üzenetek order használja a **időbélyeg** tulajdonság (definiálva a **EncodingJobMessage** írja be az alábbi példában). Ismétlődő üzenetek lehetségesek. A duplikált elemek ellenőrzéséhez használja a **ETag tulajdonság** (definiálva a **EncodingJobMessage** típusa). Lehetőség arra is, hogy bizonyos állapot változási értesítések lekérése kihagyva.
8. Megvárja, amíg a feladat 10 másodpercenként a várólista ellenőrzésével a Befejezett állapot beolvasásához. Ezek feldolgozása után törli az üzeneteket.
9. Az üzenetsor és az értesítési végpont törlése.

> [!NOTE]
> Az ajánlott módszer a figyelő a feladat állapota értesítési üzeneteket, úgy van, az alábbi példában látható módon:
>
> Azt is megteheti, hogy sikerült ellenőrizni a feladat állapotát a használatával a **IJob.State** tulajdonság.  Egy feladat befejezésének megvárását kapcsolatos értesítési üzenet érkezik előtt az állapot a **IJob** értékre van állítva **befejezett**. A **IJob.State** tulajdonság némi késéssel pontos állapotát tükrözi.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

1. Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 
2. Hozzon létre egy új mappát (mappa lehet bárhol a helyi meghajtón), és másolja egy .mp4-fájlt, amelyet szeretne kódolni vagy fokozatosan letölteni. Ebben a példában a "C:\Media" elérési utat használja.
3. Vegyen fel egy hivatkozást a **System.Runtime.Serialization** könyvtár.

### <a name="code"></a>Kód

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Az előző példában a következő kimenet előállítása: Az értékek változhatnak.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
