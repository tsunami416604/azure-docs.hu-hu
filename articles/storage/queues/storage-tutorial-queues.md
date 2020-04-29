---
title: Oktatóanyag – Azure Storage-várólisták használata – Azure Storage
description: Útmutató az Azure Queue szolgáltatás a várólisták létrehozásához, valamint üzenetek beszúrásához, lekéréséhez és törléséhez való használatáról.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75968201"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Oktatóanyag: az Azure Storage-várólisták használata

Az Azure Queue Storage olyan felhőalapú várólistákat valósít meg, amelyek lehetővé teszik az elosztott alkalmazások összetevői közötti kommunikációt. Minden üzenetsor fenntart egy listát azokról az üzenetekről, amelyeket a küldő összetevő hozzáadhat, és amelyet egy fogadó összetevő dolgoz fel. A várólista használatával az alkalmazás azonnal méretezhető az igények kielégítése érdekében. Ez a cikk az Azure Storage-üzenetsor használatának alapvető lépéseit mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Azure Storage-fiók létrehozása
> - Az alkalmazás létrehozása
> - Aszinkron kód támogatásának hozzáadása
> - Üzenetsor létrehozása
> - Üzenetek beszúrása egy várólistába
> - Üzenetek várólistára helyezése
> - Üres üzenetsor törlése
> - Parancssori argumentumok keresése
> - Az alkalmazás létrehozása és futtatása

## <a name="prerequisites"></a>Előfeltételek

- Szerezze be a platformfüggetlen [Visual Studio Code](https://code.visualstudio.com/download) Editor ingyenes példányát.
- Töltse le és telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).
- Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először hozzon létre egy Azure Storage-fiókot. A Storage-fiók létrehozásával kapcsolatos részletes útmutatóért lásd: [Storage-fiók létrehozása](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) .

## <a name="create-the-app"></a>Az alkalmazás létrehozása

Hozzon létre egy **QueueApp**nevű .net Core-alkalmazást. Az egyszerűség kedvéért ez az alkalmazás az üzenetsor használatával küldi el és fogadja az üzeneteket.

1. A konzol ablakban (például a CMD, a PowerShell vagy az Azure CLI) a `dotnet new` paranccsal hozzon létre egy új, **QueueApp**nevű Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: **program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Váltson az újonnan létrehozott **QueueApp** mappára, és hozza létre az alkalmazást, és ellenőrizze, hogy minden rendben van-e.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   A következőhöz hasonló eredményeknek kell megjelennie:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Aszinkron kód támogatásának hozzáadása

Mivel az alkalmazás Felhőbeli erőforrásokat használ, a kód aszinkron módon fut. A C# **aszinkron** és a **várakozási** sor azonban nem volt érvényes kulcsszó a **fő** metódusokban a c# 7,1-ig. Egyszerűen átválthat erre a fordítóra a **csproj** -fájlban lévő jelzővel.

1. A projekt könyvtárának parancssorában írja be `code .` a Visual Studio Code megnyitása az aktuális könyvtárban. Tartsa megnyitva a parancssori ablakot. Később további parancsokat is végrehajthat. Ha a rendszer arra kéri, hogy adjon hozzá C#-eszközöket a létrehozáshoz és a hibakereséshez, kattintson az **Igen** gombra.

2. Nyissa meg a **QueueApp.csproj** fájlt a szerkesztőben.

3. Adja `<LangVersion>7.1</LangVersion>` hozzá a Build fájl első **PropertyGroup** . Győződjön meg arról, hogy csak a **LangVersion** címkét adja hozzá, mert a **TargetFramework** attól függően eltérő lehet, hogy melyik .NET-verziót telepítette.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Mentse a **QueueApp. csproj** fájlt.

5. Nyissa meg a **program.cs** forrásfájlt, és frissítse a **Main** metódust aszinkron módon történő futtatásra. Cserélje le az **Void** értéket egy **aszinkron feladat** visszatérési értékére.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Mentse a **program.cs** fájlt.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

1. Telepítse a **Microsoft. Azure. Storage. Common** és a **Microsoft. Azure. Storage. Queuing** csomagokat a projektbe `dotnet add package` a paranccsal. Hajtsa végre a következő DotNet-parancsokat a Project mappából a konzol ablakában.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. A **program.cs** fájl elején adja hozzá a következő névtereket közvetlenül az `using System;` utasítás után. Az alkalmazás ezekből a névterekről származó típusokat használ az Azure Storage-hoz való kapcsolódáshoz és a várólistákkal való munkavégzéshez.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Mentse a **program.cs** fájlt.

### <a name="get-your-connection-string"></a>A kapcsolati sztring beszerzése

Az ügyfélkódtár kapcsolati sztring használatával hozza létre a kapcsolatot. A kapcsolatok karakterlánca a Azure Portal Storage-fiókjának **Beállítások** szakaszában érhető el.

1. A böngészőben jelentkezzen be a [Azure Portalba](https://portal.azure.com/).

2. Az Azure Portalon nyissa meg a tárfiókot.

3. Válassza a **hozzáférési kulcsok**elemet.

4. A **kapcsolódási karakterlánc** mező jobb oldalán kattintson a **Másolás** gombra.

![Kapcsolati sztring](media/storage-tutorial-queues/get-connection-string.png)

A kapcsolati sztring formátuma a következő:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Adja hozzá a kapcsolódási karakterláncot az alkalmazáshoz

Adja hozzá a kapcsolati karakterláncot az alkalmazáshoz, hogy hozzáférhessen a Storage-fiókhoz.

1. Váltson vissza a Visual Studio Code-ra.

2. A **program** osztályban adjon hozzá egy `private const string connectionString =` tagot a kapcsolódási karakterlánc tárolásához.

3. Az egyenlőségjel után illessze be a Azure Portal korábban másolt karakterlánc-értéket. A **ConnectionString** érték egyedi lesz a fiókjában.

4. Távolítsa el a ""Helló világ!"alkalmazás" kódot a **Main**-ból. A kódnak a következőhöz hasonlóan kell kinéznie, de az egyedi kapcsolódási sztring értékével.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. A **Main** frissítésével hozzon létre egy **CloudQueue** objektumot, amelyet később a küldési és fogadási metódusok továbbítanak.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Mentse a fájlt.

## <a name="insert-messages-into-the-queue"></a>Üzenetek beszúrása a várólistába

Hozzon létre egy új metódust, amely üzenetet küld a várólistába. Adja hozzá a következő metódust a **program** osztályhoz. Ez a metódus egy üzenetsor-hivatkozást kap, majd egy új várólistát hoz létre, ha az [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync)meghívásával még nem létezik. Ezután hozzáadja az üzenetet a várólistához a [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)meghívásával.

1. Adja hozzá a következő **SendMessageAsync** metódust a **program** osztályhoz.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Mentse a fájlt.

Az üzenetnek olyan formátumúnak kell lennie, amely UTF-8 kódolású XML-kérelemben is szerepelhet, és akár 64 KB méretű is lehet. Ha egy üzenet bináris adatfájlt tartalmaz, azt javasoljuk, hogy Base64 kódolással kódolja az üzenetet.

Alapértelmezés szerint az üzenetekhez tartozó maximális élettartam 7 nap. Megadhatja az üzenet élettartamának pozitív számát. Ha nem lejáró üzenetet szeretne felvenni, használja `Timespan.FromSeconds(-1)` a **AddMessageAsync**-hívását.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Üzenetek várólistára helyezése

Hozzon létre egy új, **ReceiveMessageAsync**nevű metódust. Ez a metódus a [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync)meghívásával kap üzenetet a várólistáról. Az üzenet sikeres fogadása után fontos, hogy törölje azt a sorból, hogy ne dolgozza fel egynél többször. Az üzenet fogadása után törölje azt a sorból a [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync)meghívásával.

1. Adja hozzá a következő **ReceiveMessageAsync** metódust a **program** osztályhoz.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Mentse a fájlt.

## <a name="delete-an-empty-queue"></a>Üres üzenetsor törlése

Az ajánlott eljárás a projekt végén annak azonosítása, hogy továbbra is szüksége van-e a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Ha a várólista létezik, de üres, kérje meg a felhasználót, ha törölni szeretné.

1. Bontsa ki a **ReceiveMessageAsync** metódust, hogy az üres várólista törlésére vonatkozó kérést tartalmazzon.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Mentse a fájlt.

## <a name="check-for-command-line-arguments"></a>Parancssori argumentumok keresése

Ha vannak olyan parancssori argumentumok, amelyek bekerültek az alkalmazásba, tegyük fel, hogy a várólistába kerülő üzenet jelenik meg. Összekapcsolja az argumentumokat egy karakterlánc létrehozásához. Adja hozzá ezt a karakterláncot az üzenetsor-sorhoz a korábban hozzáadott **SendMessageAsync** metódus meghívásával.

Ha nincsenek parancssori argumentumok, hajtson végre egy lekérési műveletet. Hívja meg a **ReceiveMessageAsync** metódust az üzenetsor első üzenetének lekéréséhez.

Végül várjon a felhasználói bevitelre, mielőtt kilép a **Console. readline**hívásával.

1. Bontsa ki a **Main** metódust a parancssori argumentumok kereséséhez, és várjon a felhasználói adatbevitelre.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Mentse a fájlt.

## <a name="complete-code"></a>Teljes kód

Itt látható a projekt teljes kódjának listája.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. A projekt könyvtárának parancssorában futtassa a következő DotNet-parancsot a projekt felépítéséhez.

   ```console
   dotnet build
   ```

2. Miután a projekt sikeresen létrejött, a következő parancs futtatásával adja hozzá az első üzenetet a várólistához.

   ```console
   dotnet run First queue message
   ```

A következő kimenet jelenik meg:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Futtassa az alkalmazást parancssori argumentum nélkül, hogy fogadja és eltávolítsa az első üzenetet a várólistában.

   ```console
   dotnet run
   ```

4. Folytassa az alkalmazás futtatását, amíg az összes üzenetet el nem távolítja. Ha még egyszer futtatja, egy üzenet jelenik meg arról, hogy a várólista üres, és a várólista törlésére vonatkozó kérés.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

1. Üzenetsor létrehozása
2. Üzenetek hozzáadása és eltávolítása egy várólistából
3. Azure Storage-üzenetsor törlése

További információkért tekintse meg az Azure Queues rövid útmutatóját.

> [!div class="nextstepaction"]
> [Üzenetsor-kezdés](storage-quickstart-queues-portal.md)
