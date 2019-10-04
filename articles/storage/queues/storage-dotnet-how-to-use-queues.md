---
title: Az Azure üzenetsor-tárolás használatának első lépései a .NET-Azure Storage használatával
description: Az Azure-üzenetsorok megbízható, aszinkron üzenetkezelést biztosítanak az alkalmazások összetevői között. A felhőbeli üzenetkezelésnek köszönhetően az alkalmazások összetevői függetlenül méretezhetők.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: aa92b72b09ed28b41d85ac7c7605077761657d40
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721567"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Áttekintés

Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése

Ez az oktatóanyag bemutatja, hogyan írhat .NET-kódot néhány, az Azure Queue Storage szolgáltatást használó általános forgatókönyvhöz. Az ismertetett forgatókönyvek az üzenetsorok létrehozására és törlésére, valamint az üzenetsor üzeneteinek hozzáadására, olvasására és törlésére vonatkoznak.

**Várható befejezési idő:** 45 perc

### <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Az Azure Storage közös ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Azure Storage-üzenetsor .NET-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* Egy [Azure-tárfiók](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A következő lépésként állítsa be a fejlesztési környezetet a Visual Studióban, hogy kipróbálhassa az útmutatóban megadott példákat.

### <a name="create-a-windows-console-application-project"></a>Windows-konzolalkalmazás projekt létrehozása

Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések bemutatják, hogyan hozhat létre egy Console-alkalmazást a Visual Studio 2019-ben. A lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. **Platform** > **Windows** kiválasztása
3. Válassza a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer) lehetőséget
4. Kattintson a **Tovább** gombra.
5. A **projekt neve** mezőben adja meg az alkalmazás nevét
6. Kattintson a **Létrehozás** elemre.

Az oktatóanyagban szereplő összes példa a konzol alkalmazás **program.cs** fájljának **Main ()** metódusához adható hozzá.

Az Azure Storage ügyféloldali kódtárait bármilyen típusú .NET-alkalmazásban használhatja, beleértve az Azure Cloud Service-t vagy a webalkalmazást, valamint az asztali és mobil alkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel

A következő három csomagra kell hivatkoznia a projektben az oktatóanyag elvégzéséhez:

* [A .net-hez készült általános ügyféloldali függvénytár Microsoft Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Ez a csomag programozott hozzáférést biztosít a Storage-fiókban található adaterőforrásokhoz.
* [Microsoft Azure Storage üzenetsor-függvénytár a .net-hez](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Ez az ügyféloldali kódtár lehetővé teszi, hogy az ügyfél által elérhető üzenetek tárolásához a Microsoft Azure Storage Queue szolgáltatás is működjön.
* [Microsoft Azure Configuration Manager .net-függvénytár](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban lévő kapcsolatok karakterláncának elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGet a csomagok beszerzéséhez használható. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.
2. **Tallózás** kiválasztása
3. Keressen rá az interneten a "Microsoft. Azure. Storage. üzenetsor" kifejezésre, és válassza a **telepítés** lehetőséget a Storage ügyféloldali kódtár és függőségeinek telepítéséhez. Ez a Microsoft. Azure. Storage. Common könyvtárat is telepíti, amely a várólista-függvénytár függősége.
4. Keressen rá az interneten a "Microsoft. Azure. ConfigurationManager" kifejezésre, és válassza a **telepítés** lehetőséget az Azure Configuration Manager telepítéséhez.

> [!NOTE]
> A Storage ügyféloldali kódtárainak csomagjai a [.net-hez készült Azure SDK](https://azure.microsoft.com/downloads/)-ban is szerepelnek. Javasoljuk azonban, hogy a Storage ügyféloldali kódtárait a NuGet-ből is telepítse, hogy mindig a legújabb verzióval rendelkezzen.
>
> A .NET-hez készült Storage ügyféloldali kódtárak ODataLib-függőségeit a NuGet-on elérhető ODataLib-csomagok oldják meg, nem pedig WCF Data Services. Az ODataLib-kódtárak letölthetők közvetlenül, vagy a kódprojektje hivatkozhat rájuk a NuGeten keresztül. A Storage ügyféloldali kódtárai által használt konkrét ODataLib-csomagok a következők: [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)és [térbeli](https://nuget.org/packages/System.Spatial/). Habár ezeket a kódtárakat az Azure Table Storage osztályai használják, a Storage ügyféloldali kódtárakkal való programozáshoz szükséges függőségek.

### <a name="determine-your-target-environment"></a>A célkörnyezet meghatározása

Az útmutatóban lévő példákat kétféle környezetben futtathatja:

* A kódot futtathatja a felhőben, egy Azure Storage-fiókban.
* A kódot futtathatja az Azure Storage Emulatorban is. A Storage Emulator egy helyi környezet, amely egy Azure Storage-fiókot emulál a felhőben. Az emulátor ingyenes lehetőséget biztosít a kódja tesztelésére és hibakeresésére, amíg az alkalmazása fejlesztés alatt áll. Az emulátor egy jól ismert fiókot és kulcsot használ. További információkért lásd: [Fejlesztés és tesztelés az Azure Storage Emulatorral](../common/storage-use-emulator.md).

Ha egy felhőbeli tárfiókot céloz meg, akkor másolja ki a tárfiók elsődleges hívóbetűjét az Azure Portalról. További információ eléréséhez lásd: [Hozzáférési kulcsok](../common/storage-account-manage.md#access-keys).

> [!NOTE]
> A Storage Emulator megcélzásával elkerülheti az Azure Storage-hoz kapcsolódó költségeket. Ha azonban mégis egy Azure Storage-fiókot céloz meg a felhőben, az oktatóanyag végrehajtásával járó költségek elhanyagolhatóak.

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az Azure Storage ügyféloldali kódtárai a .NET-támogatáshoz egy Storage-kapcsolódási karakterlánc használatával konfigurálja a végpontokat és a hitelesítő adatokat a tárolási szolgáltatások eléréséhez. A tárolási kapcsolati sztring egy konfigurációs fájlban tartható fenn a legjobban.

A kapcsolati sztringekkel kapcsolatos további információkért lásd: [Az Azure Storage kapcsolati sztringjének konfigurálása](../common/storage-configure-connection-string.md).

> [!NOTE]
> A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a tárfiók kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen és ne mentse egy mások számára elérhető egyszerű szöveges fájlban. Ha azt gyanítja, hogy a kulcs biztonsága sérült, az Azure portál segítségével generálja újra.

A kapcsolódási karakterlánc konfigurálásához nyissa meg az **app. config** fájlt megoldáskezelő a Visual Studióban. Adja hozzá az alább látható **\<appSettings\>** elem tartalmát. Cserélje le a *Account-Name nevet* a Storage-fiók nevére, és a fiókhoz tartozó hozzáférési kulcshoz *tartozó Account-Key* :

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

A konfiguráció beállítása például hasonló lesz a következőhöz:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

A Storage Emulator célzásához használhat egy hivatkozást, amely leképezi a jól ismert fióknevet és kulcsot. Ebben az esetben a kapcsolati sztring beállítása a következő:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel

Adja hozzá a következő `using` irányelveket a `Program.cs` fájl elejéhez:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

A mintakódnak hitelesítenie kell a tárfiókhoz való hozzáférést. A hitelesítéshez meg kell adni az alkalmazás számára a tárfiók hitelesítő adatait egy kapcsolati sztring formájában. A tárfiók hitelesítő adatainak megtekintéséhez a következőt kell tennie:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Kapcsolati sztring elemzése

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>A Queue szolgáltatásügyfél létrehozása

A [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) osztály segítségével lekérheti a Queue Storage-ban tárolt üzenetsorokat. A szolgáltatásügyfél létrehozásának egyik módja:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Most már készen áll a Queue Storage-ból adatokat olvasó és abba adatokat író kód írására.

## <a name="create-a-queue"></a>Üzenetsor létrehozása

A példa bemutatja, hogyan hozhat létre üzenetsort, ha még nem rendelkezik vele:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba

Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet) elemet. Ezután hívja meg az [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet) módszert. A **CloudQueueMessage** egy sztringből (UTF-8 formátumban) vagy egy **bájttömbből** hozható létre. Az alábbi kód létrehoz egy üzenetsort (ha még nem létezik), és beszúrja a „Hello, World” üzenetet:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe

Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) módszert.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása

Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>A következő üzenet kivétele az üzenetsorból

A kód két lépésben távolít el egy üzenetet az üzenetsorból. A [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet) meghívásával lekéri az üzenetsor következő üzenetét. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Ha végleg el szeretné távolítani az üzenetet az üzenetsorból, meg kell hívnia a [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet) módszert is. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód a **DeleteMessage** módszert rögtön az üzenet feldolgozása után hívja meg.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Async-Await mintázat használata közös Queue Storage API-kkal

Ez a példa bemutatja, hogyan használható az Async-Await mintázat a közös Queue Storage API-kkal. A minta meghívja az adott módszerek aszinkron verzióját. Az aszinkronitást az egyes módszerek *Async* utótagja jelöli. Ha Async módszert használ, az Async-Await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más feladatokkal foglalkozzon. Ennek segítségével elkerülhetők a szűk keresztmetszetek a teljesítményben, és az alkalmazás általános válaszkészsége is javul. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async és Await (C# és Visual Basic)).

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Egyéb lehetőségek az üzenetek eltávolításához az üzenetsorból

Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból. Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden üzenetnél ugyanakkor kezdődik, tehát 5 perccel a **GetMessages** hívása után a nem törölt üzenetek újra láthatóvá válnak.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése

Megbecsülheti egy üzenetsorban található üzenetek számát. A [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) módszer lekéri a Queue szolgáltatásból az üzenetsorra vonatkozó attribútumokat, amelyek között megtalálható az üzenetek száma is. Az [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) tulajdonság visszaadja a **FetchAttributes** módszer által lekért legutóbbi értéket a Queue szolgáltatás hívása nélkül.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Üzenetsor törlése

Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) módszert az üzenetsor-objektumhoz.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>További lépések

Most, hogy már megismerte a Queue Storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről is.

* A Queue szolgáltatás elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:
  * [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API – referencia](https://msdn.microsoft.com/library/azure/dd179355)
* Megtudhatja, hogyan egyszerűsítheti az [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) használatával az Azure Storage használatához írt kódot.
* Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
  * [Get started with Azure Table Storage using .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel) a strukturált adatok tárolásához.
  * [Get started with Azure Blob storage using .NET](../blobs/storage-dotnet-how-to-use-blobs.md) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel) a strukturálatlan adatok tárolásához.
  * [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../../sql-database/sql-database-connect-query-dotnet-core.md) a relációs adatok tárolásához.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
