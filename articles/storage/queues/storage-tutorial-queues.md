---
title: Oktatóanyag – az Azure storage üzenetsorok Work – Azure Storage
description: Ismertető az Azure Queue szolgáltatás használatával hozzon létre a várólisták és a Beszúrás, beolvasása, és törli az üzenetet.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 6b833ef56b890eb4ea0db6b48fe8c2622e211498
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233872"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Oktatóanyag: Azure Storage-üzenetsorok használata

Az Azure Queue storage valósítja meg a felhőalapú üzenetsorok elosztott alkalmazások összetevői közötti kommunikáció engedélyezése. Minden egyes üzenetsorhoz tart fenn a küldő összetevője által hozzáadott, és egy fogadó összetevő által feldolgozott üzenetek listája. Az üzenetsor, az alkalmazás képes azonnal igény szerint méretezhető. Ez a cikk bemutatja az Azure storage üzenetsorába használatának alapvető lépéseit.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Azure-tárfiók létrehozása
> - Az alkalmazás létrehozása
> - A kód aszinkron támogatásának hozzáadása
> - Üzenetsor létrehozása
> - Üzenet beszúrása egy üzenetsorba
> - Üzenet eltávolítása a sorból
> - Üres várólistában törlése
> - Parancssori argumentumok keresése
> - Az alkalmazás létrehozása és futtatása

## <a name="prerequisites"></a>Előfeltételek

- Az ingyenes példánya a többplatformos [Visual Studio Code](https://code.visualstudio.com/download) szerkesztő.
- Töltse le és telepítse a [.NET Core SDK](https://dotnet.microsoft.com/download).
- Ha nem rendelkezik egy jelenlegi Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Először hozzon létre egy Azure storage-fiókot. Egy lépésenkénti útmutató, amellyel egy storage-fiók létrehozásához, lásd: a [hozzon létre egy tárfiókot](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) rövid.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

Hozzon létre egy .NET Core-alkalmazást nevű **QueueApp**. Az egyszerűség kedvéért ez az alkalmazás fogja mind üzenetek küldése és fogadása végig az üzenetsoron.

1. A konzolablakban (például a cmd Parancsot, a PowerShell vagy az Azure parancssori felület) használata a `dotnet new` paranccsal hozzon létre egy új konzolalkalmazást nevű **QueueApp**. Ez a parancs létrehoz egy egyszerű "Hello World" C# a projekt egy forrásfájl: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Váltson át az újonnan létrehozott **QueueApp** mappát és annak ellenőrzéséhez, hogy minden jól az alkalmazást a build.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   A következőhöz hasonló eredményt kell megjelennie:

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

## <a name="add-support-for-asynchronous-code"></a>A kód aszinkron támogatásának hozzáadása

Mivel az alkalmazás által használt felhőbeli erőforrásokat, a kód aszinkron módon fut. Azonban C#a **aszinkron** és **await** nem érvényes kulcsszavak **fő** amíg módszerek C# 7.1-es. A fordító a azt a jelzőt keresztül egyszerűen lehet váltani a **csproj** fájlt.

1. A parancssorban a projekt könyvtárában írja be a `code .` megnyitásához a Visual Studio Code az aktuális könyvtárban található. Ne zárja be a parancssori ablakot. További parancsok végrehajtása később lesz. Ha az kéri, hogy adjon hozzá C# eszközök szükséges fejlesztése és hibakeresése, kattintson a **Igen** gombra.

2. Nyissa meg a **QueueApp.csproj** fájlt a szerkesztőben.

3. Adjon hozzá `<LangVersion>7.1</LangVersion>` do prvního **PropertyGroup** a build-fájlban. Győződjön meg arról, hogy csak akkor adjon a **LangVersion** megjelölheti a **TargetFramework** .NET melyik verziója van telepítve függően eltérő lehet.

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

5. Nyissa meg a **Program.cs** forrás-fájlt, és frissítse a **fő** metódus aszinkron módon futnak. Cserélje le **void** együtt egy **aszinkron feladat** vrácená hodnota.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Mentse a **Program.cs** fájlt.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

1. Telepítse a **WindowsAzure. Tárolási** csomagot a projekthez, az a `dotnet add package` parancsot. A projektmappa fájllistájának a konzolablakban hajtsa végre a következő dotnet parancsot.

   ```console
   dotnet add package WindowsAzure.Storage
   ```

2. Felső részén a **Program.cs** fájlt, adja hozzá a következő névterek közvetlenül a `using System;` utasítást. Ez az alkalmazás használ ezekre a névterekre való kapcsolódás az Azure Storage és az üzenetsorok használatához.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;
   ```

3. Mentse a **Program.cs** fájlt.

### <a name="get-your-connection-string"></a>A kapcsolati sztring beszerzése

Az ügyféloldali kódtár egy kapcsolati karakterláncot használ a kapcsolat létrehozásához. A kapcsolati karakterlánc megtalálható a **beállítások** szakasz a storage-fiók az Azure Portalon.

1. A böngészőben jelentkezzen be a [az Azure portal](https://portal.azure.com/).

2. Az Azure Portalon nyissa meg a tárfiókot.

3. Válassza ki **hozzáférési kulcsok**.

4. Kattintson a **másolási** jobbra található gombra a **kapcsolati karakterlánc** mező.

![Kapcsolati sztring](media/storage-tutorial-queues/get-connection-string.png)

A kapcsolati sztring formátuma a következő:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>A kapcsolati karakterlánc hozzáadása az alkalmazáshoz

Adja hozzá a kapcsolati karakterláncot az alkalmazásba, hogy hozzá tudjon férni a storage-fiókot.

1. Váltson vissza a Visual Studio Code-ot.

2. Az a **Program** osztály, adjon hozzá egy `private const string connectionString =` tag, amely tárolja a kapcsolati karakterláncot.

3. Az egyenlőségjel után illessze be a karakterlánc, amelyet korábban az Azure Portalon vágólapra másolt. A **connectionString** értéke egyedi a fiók lesz.

4. Távolítsa el a "Hello World" kódot az **fő**. A kód ehhez hasonlóan kell kinéznie a következő, de a egyedi kapcsolati karakterlánc értékét.

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

5. Frissítés **fő** hozhat létre egy **CloudQueue** objektum, amely később átad a küldése és fogadása módszerek.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Mentse a fájlt.

## <a name="insert-messages-into-the-queue"></a>A várólista üzenetek beszúrása

Hozzon létre egy új módszer üzenet küldése az üzenetsorba. Adja hozzá a következő metódust a **Program** osztály. Ez a metódus lekér egy várólista hivatkozást, majd létrehoz egy új üzenetsort, ha még nem létezik meghívásával [CreateIfNotExistsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync?view=azure-dotnet). Ezután hozzáadja az üzenetet az üzenetsorba meghívásával [AddMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync?view=azure-dotnet).

1. Adja hozzá a következő **SendMessageAsync** metódust a **Program** osztály.

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

## <a name="dequeue-messages"></a>Üzenet eltávolítása a sorból

Hozzon létre egy új módszer nevű **ReceiveMessageAsync**. Ez a módszer egy üzenetet kap a várólista meghívásával [GetMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync?view=azure-dotnet). Az üzenet jelenik meg sikeresen, ha fontos törlése az üzenetsorból, így azt nem egynél többször. Az üzenet fogadását követően törölje azt az üzenetsorból meghívásával [DeleteMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync?view=azure-dotnet).

1. Adja hozzá a következő **ReceiveMessageAsync** metódust a **Program** osztály.

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

## <a name="delete-an-empty-queue"></a>Üres várólistában törlése

Az ajánlott eljárás végén található egy projekt annak megállapítására, hogy a létrehozott erőforrások továbbra is szüksége. Erőforrások bal oldali futó is költséget takaríthat meg költséget. Ha a várólista létezik, de üres, kérje meg a felhasználót, hogy szeretné-e törölni.

1. Bontsa ki a **ReceiveMessageAsync** metódus az üres várólista törlése felszólítást tartalmazza.

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

Ha az alkalmazás átad a parancssori argumentumokat, azt feltételezik, egy üzenetet az üzenetsorba hozzáadandó zajlik. Csatlakozzon az argumentumok együtt egy karakterlánccá. Ez a karakterlánc hozzáadása az üzenetsorhoz meghívásával a **SendMessageAsync** korábban hozzáadott metódus.

Ha parancssori kapcsolók, hajtsa végre egy lekérési művelet. Hívja a **ReceiveMessageAsync** metódusának segítéségével lekérheti a várólista első üzenetébe.

Végül, Várakozás felhasználói adatbevitelre való kilépés meghívása előtt **Console.ReadLine**.

1. Bontsa ki a **fő** metódus parancssori argumentumok és várnak felhasználói bevitelre.

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

Itt látható a teljes kód a projekt listázása.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;

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

1. A parancssorból a projekt könyvtárában a következő paranccsal dotnet a projekt buildjének elkészítéséhez.

   ```console
   dotnet build
   ```

2. Miután a projekt sikeresen létrejött, a következő parancsot az első üzenet hozzáadása az üzenetsorhoz.

   ```console
   dotnet run First queue message
   ```

A kimenetnek kell megjelennie:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Futtassa az alkalmazást parancssori argumentumok nélkül kapnak, és távolítsa el az első üzenet a várólistában.

   ```console
   dotnet run
   ```

4. Továbbra is futtathatják az alkalmazást, amíg az összes az üzenetek el lesznek távolítva. Ha még egyszer futtatja azt, kap egy üzenet, hogy az üzenetsor üres, és a egy parancssort a várólista törlése.

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
2. Hozzáadhat és eltávolíthat az üzeneteket az üzenetsorból
3. Egy Azure storage-üzenetsor törlése

Tekintse meg az Azure-üzenetsorok a rövid útmutató további információt.

> [!div class="nextstepaction"]
> [Üzenetsorok a rövid útmutató](storage-quickstart-queues-portal.md)
