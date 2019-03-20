---
title: Hozzon létre, és a egy közös hozzáférésű jogosultságkód (SAS) használata az Azure Blob storage |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre közös hozzáférésű jogosultságkódok használata a Blob storage-, és hogyan lehet az azokat az ügyfélalkalmazásokban.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.devlang: dotnet
ms.date: 05/15/2017
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 5dcb9f16b589b8332d5fcf35c9d8b4cd914460f2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012552"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>A közös hozzáférésű Jogosultságkódot, 2. rész: Hozzon létre, és használhatja az SAS-Blob-tárolóval

[1. rész](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ebben az oktatóanyagban bemutattuk az a közös hozzáférésű jogosultságkódot (SAS), és azok használatával kapcsolatos ajánlott eljárások ismertetése. 2. rész bemutatja, hogyan hozhat létre, illetve majd közös hozzáférésű jogosultságkódok használata Blob storage. A példák C# nyelven íródtak, és használja az Azure Storage ügyféloldali kódtára a .NET-hez. Ebben az oktatóanyagban szereplő példák:

* Egy tároló közös hozzáférésű jogosultságkód létrehozása
* A blob közös hozzáférésű jogosultságkód létrehozása
* Hozzon létre egy tárolt hozzáférési szabályzatot, az aláírások egy tároló-erőforrások kezelése
* A közös hozzáférésű jogosultságkódok tesztelése az ügyfélalkalmazás

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ebben az oktatóanyagban két konzolalkalmazást, amelyek bemutatják, létrehozása és a tárolók és blobok közös hozzáférésű jogosultságkódok használata hozunk létre:

**1 alkalmazás**: A management alkalmazás. Létrehoz egy tárolót és a egy blob közös hozzáférésű jogosultságkód. Magában foglalja a tárfiók hozzáférési kulcsát a forráskódban.

**2 alkalmazás**: Az ügyfélalkalmazás. Hozzáfér tároló és blobnév erőforrásokat a létrehozott az első alkalmazás közös hozzáférési aláírások használatával. Csak a közös hozzáférésű jogosultságkódok hozzáférés tároló és a blob-erőforrások – használja ezt *nem* tartalmazzák a tárfiók hozzáférési kulcsát.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>1. rész: Hozzon létre egy konzolalkalmazást, hogy közös hozzáférésű jogosultságkódok létrehozása
Először is győződjön meg arról, hogy rendelkezik-e az Azure Storage ügyféloldali kódtára a .NET-hez telepítve. Telepítheti a [NuGet-csomag](https://nuget.org/packages/WindowsAzure.Storage/ "NuGet-csomag") az ügyféloldali kódtár a legfrissebb szerelvényeket tartalmazó. Ez az az ajánlott módszer annak biztosítására, hogy rendelkezik-e a legújabb javításokat. Az ügyféloldali kódtár legújabb verziójának részeként is letöltheti a [Azure SDK for .NET](https://azure.microsoft.com/downloads/).

A Visual Studióban hozzon létre egy új Windows-konzolalkalmazást, és adja neki **GenerateSharedAccessSignatures**. Adja hozzá hivatkozásokat [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) és [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) a következő megközelítések egyikének használatával:

* Használja a [NuGet-Csomagkezelő](https://docs.nuget.org/consume/installing-nuget) a Visual Studióban. Válassza ki **projekt** > **NuGet-csomagok kezelése**, keressen rá az interneten az egyes csomagok (Microsoft.WindowsAzure.ConfigurationManager és a windowsazure.Storage segítségével), és telepítse őket.
* Azt is megteheti keresse meg a ezekkel a szerelvényekkel-példány esetében az Azure SDK-t, és adja hozzá őket hivatkozásokat:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

A Program.cs fájl elején, adja hozzá a következő **használatával** irányelveket:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Szerkessze az app.config fájlt úgy, hogy a benne egy olyan kapcsolati karakterlánccal, amely a tárfiók a konfigurációs beállítások. Az app.config fájlt ehhez hasonlóan kell kinéznie:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Hozzon létre egy tároló közös hozzáférésű jogosultságkód URI
Először hozzáadunk egy új tároló közös hozzáférésű jogosultságkód létrehozása egy metódust. Ebben az esetben az aláírás nincs társítva egy tárolt hozzáférési szabályzatot, általa az URI a jelző annak lejárati időpontot és engedélyeket ad információkat.

Először adja hozzá a kódot a **Main()** módszer engedélyezéséhez a tárfiókhoz való hozzáférést, és hozzon létre egy új tárolót:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Ezután adjon hozzá egy metódus állít elő, a tároló közös hozzáférésű jogosultságkód, és adja vissza az aláírást URI:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Adja hozzá a következő sorokat alján a **Main()** metódus hívása előtt **Console.ReadLine()** meghívására **GetContainerSasUri()** írási és olvasási aláírásának URI-t, a a konzolablakban:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Fordítsa le és futtassa a kimenetben az új tároló közös hozzáférésű jogosultságkód URI. Az URI-t az alábbihoz hasonló lesz:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

A kód futtatása után a létrehozott tároló közös hozzáférésű jogosultságkód lesz érvényes a következő 24 órában. Az aláírás engedélyt ad egy ügyfél blobok listázása, a tárolóban, és új blobok írni a tárolót.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Hozzon létre egy BLOB közös hozzáférésű jogosultságkód URI
Következő lépésként hozzon létre egy új blobot a tárolóból, és a egy közös hozzáférésű jogosultságkód létrehozása, hasonló kódot írunk. A közös hozzáférésű jogosultságkód nincs társítva egy tárolt hozzáférési szabályzatot, így a kezdési idő, lejárati időpontot és engedély információkat tartalmaz az URI-ban.

Adjon hozzá egy új módszer, amely létrehoz egy új blob és valamilyen szöveget ír, akkor állít elő, a közös hozzáférésű jogosultságkód és aláírásának URI-t adja vissza:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Alsó részén a **Main()** metódust, hívja a következő sorokat **GetBlobSasUri()**, hívása előtt **Console.ReadLine()**, és a közös hozzáférésű jogosultságkód írása A konzolablakban URI:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Fordítsa le és futtassa a kimenetben az új blob közös hozzáférésű jogosultságkód URI. Az URI-t az alábbihoz hasonló lesz:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>A tárolóban tárolt hozzáférési szabályzat létrehozása
Most hozzunk létre egy tárolt hozzáférési szabályzatot a tárolót, amely meghatározza a függőségeket bármely közös hozzáférésű jogosultságkódok társított, a.

Az előző lépéseknél azt meg a kezdési idő (implicit és explicit módon), a lejárati időpontot és engedélyeket a közös hozzáférésű jogosultságkód URI magát. Az alábbi példák azt adja meg ezeket a tárolt hozzáférési házirendben, nem pedig a közös hozzáférésű jogosultságkód. Ez lehetővé teszi számunkra, hogy ezek a korlátozások nélkül a közös hozzáférésű jogosultságkód hosszkorlátját módosíthatja.

Lehetséges, hogy egy vagy több, a közös hozzáférésű jogosultságkód korlátait és a tárolt hozzáférési szabályzatot a fennmaradó. Azonban csak adhat a kezdési idő, lejárati időpontot és engedélyeket, vagy egy másik. Például nem határoz meg a közös hozzáférésű jogosultságkód engedélyeket és is adja meg azokat a tárolt hozzáférési szabályzatot.

Amikor egy tárolt hozzáférési szabályzatot hozzá egy tároló, a tároló meglévő engedélyeket, adja hozzá az új hozzáférési szabályzat, és majd beállítja annak a tároló engedélyeit.

Adjon hozzá egy új módszer, amely egy tárolót hoz létre egy új tárolt hozzáférési szabályzatot, és a szabályzat nevét adja vissza:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

Alsó részén a **Main()** metódus hívása előtt **Console.ReadLine()**, és adja hozzá a következő vonalak először törölje a meglévő hozzáférési házirendekben, majd hívja a  **CreateSharedAccessPolicy()** módszer:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Amikor törli a hozzáférési házirendek a tárolón, kell először a tároló meglévő engedélyeket, majd törölje a jelet az engedélyek, majd állítsa be újra az engedélyeket.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Közös hozzáférésű jogosultságkód URI, amely egy hozzáférési szabályzatot használ a tároló létrehozása
Ezután hozunk létre a tárolót, hogy létrehoztuk a korábban, de ezúttal azt társítsa az aláírást a tárolt hozzáférési szabályzatot az előző példában létrehoztunk egy másik közös hozzáférésű jogosultságkódot.

Adjon hozzá egy új metódust a tároló egy másik közös hozzáférésű jogosultságkód létrehozása:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Alsó részén a **Main()** metódus hívása előtt **Console.ReadLine()**, adja hozzá a következő sorokat hívja a **GetContainerSasUriWithPolicy** módszer:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Egy közös hozzáférésű Jogosultságkód URI, amely egy hozzáférési szabályzatot használja a BLOB létrehozása
Végül hozzáadunk egy másik blob létrehozásához, és a egy közös hozzáférésű jogosultságkód társított egy tárolt hozzáférési szabályzat létrehozása hasonló módon.

Adjon hozzá egy új metódust hozzon létre egy blobot, és a egy közös hozzáférésű jogosultságkód létrehozása:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Alsó részén a **Main()** metódus hívása előtt **Console.ReadLine()**, adja hozzá a következő sorokat hívja a **GetBlobSasUriWithPolicy** módszer:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

A **Main()** metódus hasonlóan kell kinéznie a teljes egészében. Futtassa a közös hozzáférésű jogosultságkód URI-k írni a konzolablakban, majd másolja és illessze be őket egy szöveges fájl, amely ebben az oktatóanyagban a második részében.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Amikor futtatja a GenerateSharedAccessSignatures konzolalkalmazást, látni fogja a következőhöz hasonló kimenetet. Ezek azok a közös hozzáférésű jogosultságkódok használata az oktatóanyag 2. rész.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>2. rész: Hozzon létre egy konzolalkalmazást a közös hozzáférésű jogosultságkódok tesztelése
A közös hozzáférésű jogosultságkódok, az előzőekben létrehozott teszteléséhez mintázatai alapján műveletek végrehajtásához, a tároló és a egy blob egy második konzolalkalmazást hozunk létre.

> [!NOTE]
> Ha több mint 24 órával teljesül, mivel elvégezte az oktatóanyag első részét, az Ön által létrehozott aláírásokat már nem érvényes. Ebben az esetben futtassa a kódot az oktatóanyag második felében létrehozni az új közös hozzáférésű jogosultságkódok használata az első Konzolalkalmazás.
>

A Visual Studióban hozzon létre egy új Windows-konzolalkalmazást, és adja neki **ConsumeSharedAccessSignatures**. Adja hozzá hivatkozásokat [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) és [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), ahogy korábban.

A Program.cs fájl elején, adja hozzá a következő **használatával** irányelveket:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Törzsében a **Main()** metódust, adja hozzá a következő karakterlánc-állandókat, azok az értékek módosítása a következőre a közös hozzáférésű jogosultságkódok, az oktatóanyag 1. rész létrehozott.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Adjon meg egy metódust tárolóművelet közös hozzáférésű jogosultságkód használatával próbálja
Ezután hozzáadunk egy metódust, amely bizonyos tárolóművelet, a tároló közös hozzáférésű jogosultságkód használatával teszteli. A közös hozzáférésű jogosultságkód küldhet vissza egy hivatkozást a tárolót, a tároló önálló aláírás alapján való hozzáférés hitelesítésére szolgál.

A program.cs fájlhoz adja hozzá a következő metódust:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Frissítés a **Main()** metódus meghívásához **UseContainerSAS()** mind a létrehozott a tároló közös hozzáférésű jogosultságkódok:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Adjon meg egy metódust próbálja meg a közös hozzáférésű jogosultságkódok használata blob műveletek
Végül hozzáadunk egy metódust, amely néhány a blob közös hozzáférésű jogosultságkódok használata blob művelet teszteli. Ebben az esetben a konstruktor használjuk **CloudBlockBlob(String)**, passzok, az a közös hozzáférésű jogosultságkóddal való visszatéréshez a blobra mutató hivatkozást. További hitelesítés nem szükséges; az önálló aláírás alapul.

A program.cs fájlhoz adja hozzá a következő metódust:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Frissítés a **Main()** metódus meghívásához **UseBlobSAS()** mind az Ön által létrehozott a BLOB közös hozzáférésű jogosultságkódok:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Futtassa a konzolalkalmazást, és vizsgálja meg a kimenet megtekintéséhez, hogy mely műveletek mely aláírások használata engedélyezett. A kimenetet a konzolablakban az alábbihoz hasonlóan fog kinézni:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>További lépések

* [A közös hozzáférésű Jogosultságkódot, 1. rész: A SAS-modell ismertetése](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Tárolók és blobok névtelen olvasási hozzáférésének kezelése](storage-manage-access-to-resources.md)
* [Közös hozzáférésű jogosultságkód (REST API) hozzáférés delegálása](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Tábla és üzenetsor SAS bemutatása](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
