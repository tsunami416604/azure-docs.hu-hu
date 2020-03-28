---
title: Oktatóanyag – Az Azure storage-várólisták kezelése – Azure Storage
description: Oktatóanyag arról, hogyan hozhat létre várólistákat az Azure Queue szolgáltatáshasználatával, és hogyan szúrhat be, kaphat be és törölhet üzeneteket.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968201"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Oktatóanyag: Az Azure storage-várólisták kezelése

Az Azure Queue storage felhőalapú várólistákat valósít meg az elosztott alkalmazások összetevői közötti kommunikáció engedélyezéséhez. Minden várólista listát tart fenn a küldő összetevő által hozzáadható és a fogadó összetevő által feldolgozható üzenetek ről. Egy várólista esetén az alkalmazás azonnal skálázható az igényeknek megfelelően. Ez a cikk az Azure storage-várólisták használatával kapcsolatos alapvető lépéseket mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Azure Storage-fiók létrehozása
> - Az alkalmazás létrehozása
> - Az aszinkron kód támogatásának hozzáadása
> - Üzenetsor létrehozása
> - Üzenetek beszúrása várólistába
> - Üzenetek várólistán stalegének törlése
> - Üres várólista törlése
> - Parancssori argumentumok ellenőrzése
> - Az alkalmazás létrehozása és futtatása

## <a name="prerequisites"></a>Előfeltételek

- Szerezd meg az ingyenes példányt a platformfüggetlen [Visual Studio Code](https://code.visualstudio.com/download) editor.
- Töltse le és telepítse a [.NET Core SDK -t](https://dotnet.microsoft.com/download).
- Ha nem rendelkezik aktuális Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először hozzon létre egy Azure-tárfiókot. A tárfiók létrehozásához részletes útmutatót a [Tárfiók létrehozása](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) rövid útmutató.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

Hozzon létre egy **QueueApp**nevű .NET Core alkalmazást. Az egyszerűség kedvéért ez az alkalmazás üzeneteket küld és fogad a várólistán keresztül.

1. Egy konzolablakban (például CMD, PowerShell vagy Azure `dotnet new` CLI) a paranccsal hozzon létre egy új konzolalkalmazást **QueueApp**névvel. Ez a parancs egy egyszerű "Hello World" C# projektet hoz létre egyetlen forrásfájllal: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Váltson át az újonnan létrehozott **QueueApp** mappára, és építse fel az alkalmazást, hogy ellenőrizze, minden rendben van-e.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Az alábbihoz hasonló eredményeket kell látnia:

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

## <a name="add-support-for-asynchronous-code"></a>Az aszinkron kód támogatásának hozzáadása

Mivel az alkalmazás felhőalapú erőforrásokat használ, a kód aszinkron módon fut. Azonban a C#'s **async** and **wait** nem volt érvényes kulcsszavak a **Fő** metódusokban a C# 7.1-ig. Könnyedén válthat, hogy a fordító egy zászló a **csproj** fájlt.

1. A projektkönyvtár parancssorából írja `code .` be a Visual Studio-kód megnyitásához az aktuális könyvtárban. Tartsa nyitva a parancssori ablakot. Később még több parancsot kell végrehajtani. Ha a rendszer kéri, hogy adja hozzá a C# eszközök létrehozásához és hibakereséséhez szükséges, kattintson az **Igen** gombra.

2. Nyissa meg a **QueueApp.csproj** fájlt a szerkesztőben.

3. Adja `<LangVersion>7.1</LangVersion>` hozzá a buildfájl első **Tulajdonságcsoportjához.** Győződjön meg arról, hogy csak a **LangVersion** címkét adja hozzá **targetFramework** keretében, attól függően, hogy a .NET melyik verzióját telepítette.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Mentse a **QueueApp.csproj** fájlt.

5. Nyissa meg a **Program.cs** forrásfájlt, és frissítse a **Fő** metódust az aszinkron futtatáshoz. Cserélje le az **void-ot** **egy aszinkron feladat** visszatérési értékére.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Mentse a **Program.cs** fájlt.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

1. Telepítse a **Microsoft.Azure.Storage.Common** és a **Microsoft.Azure.Storage.Storage** `dotnet add package` csomagokat a projektbe a paranccsal. Hajtsa végre a következő dotnet parancsokat a projektmappából a konzolablakban.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. A **Program.cs** fájl tetején adja hozzá a következő `using System;` névtereket közvetlenül az utasítás után. Ez az alkalmazás az ilyen névterekből származó típusokat használ az Azure Storage-hoz való csatlakozáshoz és a várólisták hozásához.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Mentse a **Program.cs** fájlt.

### <a name="get-your-connection-string"></a>A kapcsolati sztring beszerzése

Az ügyfélkódtár kapcsolati sztring használatával hozza létre a kapcsolatot. A kapcsolati karakterlánc az Azure Portalon a tárfiók **Beállítások** szakaszában érhető el.

1. A webböngészőben jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Az Azure Portalon nyissa meg a tárfiókot.

3. Válassza **az Access-kulcsok lehetőséget**.

4. Kattintson a **Kapcsolati karakterlánc** mező jobb oldalán található **Másolás** gombra.

![Kapcsolati sztring](media/storage-tutorial-queues/get-connection-string.png)

A kapcsolati sztring formátuma a következő:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>A kapcsolati karakterlánc hozzáadása az alkalmazáshoz

Adja hozzá a kapcsolati karakterláncot az alkalmazásba, hogy hozzáférhessen a tárfiókhoz.

1. Váltás vissza a Visual Studio-kódra.

2. A **Program** osztályban `private const string connectionString =` adjon hozzá egy tagot a kapcsolati karakterlánc megtartásához.

3. Az egyenlőségjel után illessze be a korábban az Azure Portalon másolt karakterlánc-értéket. A **connectionString** érték egyedi lesz a fiókjában.

4. Távolítsa el a "Hello World" kódot a **Main**. A kódnak a következőhöz hasonlóan kell kinéznie, de az egyedi kapcsolati karakterlánc értékével.

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

5. Frissítse **a Main** alkalmazást egy **CloudQueue** objektum létrehozásához, amely később átkerül a küldési és fogadási módszerekbe.

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

Hozzon létre egy új módszert, amelyből üzenetet küldhet a várólistába. Adja hozzá a következő módszert a **Program** osztályhoz. Ez a módszer várólista-hivatkozást kap, majd létrehoz egy új várólistát, ha az még nem létezik a [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync)hívásával. Ezután hozzáadja az üzenetet a várólistához az [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)hívásával.

1. Adja hozzá a következő **SendMessageAsync** metódust a **Program** osztályhoz.

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

Az üzenetnek utf-8 kódolású XML-kérelemben olyan formátumban kell lennie, amely legfeljebb 64 KB méretű lehet. Ha egy üzenet bináris adatokat tartalmaz, javasoljuk, hogy a Base64-kódolja az üzenetet.

Alapértelmezés szerint az üzenet maximális ideig tart-ig 7 nap. Az üzenethez az élő höz bármilyen pozitív számot megadhat. Ha olyan üzenetet szeretne hozzáadni, `Timespan.FromSeconds(-1)` amely nem jár le, használja az **AddMessageAsync**hívásban.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Üzenetek várólistán stalegének törlése

Hozzon létre egy új metódust, a **ReceiveMessageAsync metódust.** Ez a módszer a [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync)hívásával kap üzenetet a várólistából. Az üzenet sikeres fogadása után fontos, hogy törölje azt a várólistából, hogy a rendszer csak egyszer dolgozza fel. Az üzenet fogadása után törölje azt a várólistából a [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync)hívásával.

1. Adja hozzá a következő **ReceiveMessageAsync** metódust a **Program** osztályhoz.

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

## <a name="delete-an-empty-queue"></a>Üres várólista törlése

A projekt végén ajánlott megállapítani, hogy szükség van-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Ha a várólista létezik, de üres, kérdezze meg a felhasználót, hogy törölni szeretné-e.

1. Bontsa ki a **ReceiveMessageAsync** metódust az üres várólista törlésére irányuló kérdés felvételéhez.

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

## <a name="check-for-command-line-arguments"></a>Parancssori argumentumok ellenőrzése

Ha az alkalmazásnak parancssori argumentumok vannak, tegyük fel, hogy egy üzenet, amelyet hozzá kell adni a várólistához. Az argumentumok egyesítése a karakterlánc hozásához. Adja hozzá ezt a karakterláncot az üzenetvárólistához a korábban hozzáadott **SendMessageAsync** metódus hívásával.

Ha nincsenek parancssori argumentumok, hajtson végre egy beolvasási műveletet. Hívja meg a **ReceiveMessageAsync metódust** a várólista első üzenetének beolvasásához.

Végül a **Console.ReadLine**.

1. Bontsa ki a **Fő** metódust a parancssori argumentumok ellenőrzéséhez és a felhasználói bevitelre való várakozáshoz.

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

Itt van a projekt teljes kódlistája.

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

1. A projektkönyvtár parancssorából futtassa a következő dotnet parancsot a projekt létrehozásához.

   ```console
   dotnet build
   ```

2. A projekt sikeres létrehozása után futtassa a következő parancsot az első üzenet várólistához való hozzáadásához.

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

3. Futtassa az alkalmazást parancssori argumentumok nélkül a várólista első üzenetének fogadásához és eltávolításához.

   ```console
   dotnet run
   ```

4. Folytassa az alkalmazás futtatását, amíg az összes üzenetet el nem távolítja. Ha még egyszer futtatja, egy üzenet jelenik meg arról, hogy a várólista üres, és a várólista törlésére irányuló üzenet jelenik meg.

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
2. Üzenetek hozzáadása és eltávolítása a várólistából
3. Azure-tárolási várólista törlése

További információkért tekintse meg az Azure-várólisták rövid útmutatóját.

> [!div class="nextstepaction"]
> [Várólisták rövid útmutatója](storage-quickstart-queues-portal.md)
