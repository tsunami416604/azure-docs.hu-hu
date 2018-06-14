---
title: Létrehozhat és használhat egy közös hozzáférésű jogosultságkód (SAS) az Azure Blob storage szolgáltatással |} Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan használható közös hozzáférésű jogosultságkód létrehozása a Blob-tároló, és hogyan kell felhasználni azokat az ügyfél alkalmazásokban.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873211"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Közös hozzáférésű Jogosultságkód, 2. rész: Létrehozása és SAS-kód használata a Blob-tároló

[1. rész](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) felfedezte az oktatóanyag a közös hozzáférésű jogosultságkód (SAS), és azok használatának ajánlott eljárásai alapján. 2. rész bemutatja, hogyan hozhat létre, és a Blob storage közös hozzáférésű jogosultságkód majd használni. A példák C# nyelven íródtak, és használja az Azure Storage ügyféloldali kódtára a .NET-hez. Az oktatóanyagban szereplő példák:

* A közös hozzáférésű jogosultságkód egy tároló létrehozása
* A blob megosztott hozzáférési aláírást létrehozni
* Egy tároló-erőforrások aláírások kezeléséhez tárolt hozzáférési házirend létrehozása
* A közös hozzáférésű jogosultságkód egy ügyfél-alkalmazás tesztelése

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ebben az oktatóanyagban létrehozhatunk két konzol alkalmazások, amelyek bemutatják, létrehozása és a tárolók és blobok közös hozzáférésű jogosultságkód használata:

**1 alkalmazás**: A felügyeleti alkalmazás. A tároló és a blob egy közös hozzáférésű jogosultságkódot állít elő. A tárfiók elérési kulcsának forráskód tartalmazza.

**Alkalmazás 2**: az ügyfélalkalmazást. Hozzáférések tároló és a blob erőforrások a közös hozzáférésű jogosultságkód létre az első alkalmazás használatával. Csak a közös hozzáférésű jogosultságkód hozzáférést tároló és a blob-erőforrások – használja az hajtja végre *nem* tartalmazza a tárfiók elérési kulcsának.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>1. lépés: Hozzon létre egy konzolalkalmazást közös hozzáférésű jogosultságkód létrehozása
Először győződjön meg róla, hogy rendelkezik-e az Azure Storage ügyféloldali kódtára a .NET telepítése. Telepítheti a [NuGet-csomag](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet-csomag") tartalmazó az ügyféloldali kódtár legújabb szerelvényeket. Ez az az ajánlott módszer annak biztosítására, hogy rendelkezik-e a legújabb javításokat. Az ügyféloldali kódtár legújabb verziójának részeként is letöltheti a [Azure SDK for .NET](https://azure.microsoft.com/downloads/).

A Visual Studio, hozzon létre egy új Windows-konzolalkalmazást, és adjon neki nevet **GenerateSharedAccessSignatures**. Hivatkozásokat adni [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) és [windowsazure.Storage kifejezésre](https://www.nuget.org/packages/WindowsAzure.Storage/) a következő módszerek egyikével:

* Használja a [NuGet-Csomagkezelő](https://docs.nuget.org/consume/installing-nuget) a Visual Studióban. Válassza ki **projekt** > **NuGet-csomagok kezelése**, keresse rá az interneten csomagonként (Microsoft.WindowsAzure.ConfigurationManager és windowsazure.Storage kifejezésre), és a telepítést.
* Keresse meg a szerelvényeket az Azure SDK telepítése, és adja hozzá őket mutató hivatkozásokat:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

A Program.cs fájl felső részén adja hozzá a következő **használatával** irányelveket:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Szerkessze az app.config fájlt, hogy a konfigurációs beállítások mutat, a tárfiók kapcsolati karakterláncot tartalmaz. Az app.config fájlt ehhez hasonlóan kell kinéznie:

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

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>A közös hozzáférésű jogosultságkód URI egy tároló létrehozása
Először igazolnia adja hozzá a közös hozzáférésű jogosultságkód egy új tároló létrehozásához egy metódust. Ebben az esetben az aláírás nincs társítva egy tárolt hozzáférési házirend, az általa URI-n az adatokat, feltüntetve a lejárat időpontjának és az engedélyeket ad.

Először adja hozzá a kódot a **Main()** módszer, amellyel hitelesíti a hozzáférést a tárfiókhoz, és hozzon létre egy új tároló:

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

Ezután adja hozzá a állít elő, a közös hozzáférésű jogosultságkódot, a tároló és az aláírás URI metódus:

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

Adja hozzá a következő sorokat alján a **Main()** metódus hívása előtt **Console.ReadLine()** meghívására **GetContainerSasUri()** és az aláírás URI írni a konzolablakban:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Fordítsa le és futtassa a közös hozzáférésű jogosultságkódot URI az új tároló kimeneti. Az URI azonosító a következőhöz hasonló lesz:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

A kód futtatása után a közös hozzáférésű jogosultságkódot, a tároló létrehozott lesz érvényes a következő 24 órában. Az aláírás engedélyt ad egy ügyfél lista BLOB a tárolóban, és a tároló új blobok írni.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>A közös hozzáférésű jogosultságkód URI egy BLOB készítése
A következő azt írhatja hasonló kódot hozzon létre egy új blob a tárolóban, és egy közös hozzáférésű jogosultságkódot létrehozni hozzá. A közös hozzáférésű jogosultságkódot nincs társítva tárolt hozzáférési házirenddel, így a kezdési ideje, a lejárati idő és a engedély információkat tartalmaz az URI azonosító.

Adja hozzá egy új hoz létre egy új blob és szöveget abba, majd állít elő, a közös hozzáférésű jogosultságkód és metódus aláírása URI:

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

Alján a **Main()** módszer, adja hozzá a következő sorokat hívására **GetBlobSasUri()**, hívása előtt **Console.ReadLine()**, és a közös hozzáférésű jogosultságkódot URI írni a konzolablakban:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Fordítsa le és futtassa az új BLOB URI közös hozzáférésű jogosultságkódot kimeneti. Az URI azonosító a következőhöz hasonló lesz:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>A tárolóban tárolt hozzáférési házirend létrehozása
Most hozzon létre egy tárolt házirend a tárolóra, és határozza meg, amely hozzá van rendelve megosztott hozzáférési aláírásokkal korlátait.

Az előző példákban azt meg a kezdési idő (implicit vagy explicit módon), a lejárati időpont és az engedélyek a közös hozzáférésű jogosultságkódot maga URI. Az alábbi példák azt adja meg ezeket a tárolt házirend, nem a közös hozzáférésű jogosultságkóddal. Így lehetővé teszi, hogy ezek a megkötések nélkül a közös hozzáférésű jogosultságkódot hosszkorlátját módosíthatja.

Lehetséges, hogy egy vagy több, a közös hozzáférésű jogosultságkódot korlátozásaival, valamint a tárolt házirend a többi. Azonban csak megadhatja a kezdő időpont, a lejárat időpontjának és az engedélyek egy helyen vagy a másik. Például nem engedélyeket ad meg a közös hozzáférésű jogosultságkódot és is adja meg azokat a tárolt házirend.

Amikor egy tárolt házirend hozzáadása a tárolóhoz, a tároló meglévő engedélyeket szerezni, adja hozzá az új házirend, és adja meg a tároló engedélyeit.

Adja hozzá egy új módszer, amely létrehoz egy új tárolt házirend tárolóba, és a házirend nevét adja vissza:

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

Alján a **Main()** metódus hívása előtt **Console.ReadLine()** adja hozzá a következő sorokat első törölje a meglévő hozzáférési házirendeket, és, majd hívja a **CreateSharedAccessPolicy()** módszert:

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

Ha törli a hozzáférési házirendek tárolóba, kell először lekérdezni a tároló meglévő engedélyeket, majd törölje a jelet az engedélyeket, majd állítsa be újra a engedélyeket.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>A közös hozzáférésű jogosultságkód által használt a hozzáférési házirendek a tárolón URI generálása
A következő létrehozhatunk egy másik közös hozzáférésű jogosultságkódot ahhoz a tárolóhoz, amelybe a korábban, de ezúttal a tárolt házirend az előző példában létrehozott azt társítsa az aláírás létrehozott.

Adja hozzá a tároló egy másik közös hozzáférésű jogosultságkódot létrehozni egy új módszer:

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

Alján a **Main()** metódus hívása előtt **Console.ReadLine()**, adja hozzá a következő sorokat hívni a **GetContainerSasUriWithPolicy** módszert:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>A közös hozzáférésű Jogosultságkód által használt a hozzáférési házirendek blobot URI generálása
Végül azt adja hozzá a hasonló módszert, hozzon létre egy másik blob és tárolt hozzáférési házirenddel társított megosztott hozzáférési aláírást létrehozni.

Hozzon létre egy blobot, és egy közös hozzáférésű jogosultságkódot létrehozni egy új módszer hozzáadása:

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

Alján a **Main()** metódus hívása előtt **Console.ReadLine()**, adja hozzá a következő sorokat hívni a **GetBlobSasUriWithPolicy** módszert:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

A **Main()** metódus most példához hasonló egészében. Futtassa a közös hozzáférésű jogosultságkódot URI-azonosítók írni a konzolablakban Ezután másolja és illessze be egy szövegfájlt, ez az oktatóanyag második része legyen.

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

Amikor futtatja a GenerateSharedAccessSignatures konzolalkalmazást, látni fogja, a következőhöz hasonló kimenetet. Ezek azok a megosztott hozzáférési aláírásokkal használhatja az oktatóanyag az 2.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>2. lépés: Hozzon létre egy konzolalkalmazást a közös hozzáférésű jogosultságkód tesztelése
A közös hozzáférésű jogosultságkód létrehozása a fenti példákban teszteléséhez jelenleg hozzon létre egy második konzolalkalmazást, amely a aláírásait használja a műveletek végrehajtásához, a tároló és a blob.

> [!NOTE]
> Ha több mint 24 órája eltelt óta elvégezte az oktatóanyag első részét, az Ön által létrehozott aláírásokat már nem érvényesek. Ebben az esetben az első Konzolalkalmazás friss közös hozzáférésű jogosultságkód használatra létrehozni az oktatóanyag második része a kódot kell futtatni.
>

A Visual Studio, hozzon létre egy új Windows-konzolalkalmazást, és adjon neki nevet **ConsumeSharedAccessSignatures**. Hivatkozásokat adni [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) és [windowsazure.Storage kifejezésre](https://www.nuget.org/packages/WindowsAzure.Storage/), mint korábban.

A Program.cs fájl felső részén adja hozzá a következő **használatával** irányelveket:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Törzsében a **Main()** módszer, adja hozzá a következő karakterlánckonstansokat, az értékek módosítása a közös hozzáférésű jogosultságkód az oktatóanyag 1 részében létrehozott.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Próbálja meg a tároló műveletek használatával a közös hozzáférésű jogosultságkód egy olyan metódus hozzáadása
Ezután azt adja hozzá a módszere, amely megvizsgálja az egyes tároló műveletek használatával a közös hozzáférésű jogosultságkód ahhoz a tárolóhoz. A közös hozzáférésű jogosultságkódot ad vissza egy hivatkozást a tárolóhoz, a tároló önmagában aláírás alapján való hozzáférés hitelesítéséhez.

Adja hozzá a következő metódust a program.cs fájlt:

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

Frissítés a **Main()** metódus hívására **UseContainerSAS()** mindkét a közös hozzáférésű jogosultságkód létrehozta a tárolón:

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

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Egy blob műveletek használatával a közös hozzáférésű jogosultságkód kipróbálásához metódus hozzáadása
Végül azt adja hozzá a módszere, amely a blob néhány blob-műveletekbe egy közös hozzáférésű jogosultságkódot teszteli. Ebben az esetben a konstruktor használjuk **CloudBlockBlob(String)**, tompított a közös hozzáférésű jogosultságkódot, a blobra mutató hivatkozást ad eredményül. Nincs más hitelesítés szükség; az aláírás önmagában alapul.

Adja hozzá a következő metódust a program.cs fájlt:

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

Frissítés a **Main()** metódus hívására **UseBlobSAS()** mindkét a közös hozzáférésű jogosultságkód blobot létrehozott:

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

Futtassa a konzolalkalmazást, és megfigyelheti, hogy mely műveleteket úgy is, mely aláírások kimeneti. A konzolablakban a kimenet az alábbihoz hasonló fog kinézni:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Következő lépések

* [Megosztott hozzáférési aláírásokkal, 1. lépés: Az SAS-modell ismertetése](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Tárolók és blobok névtelen olvasási hozzáférés kezelése](storage-manage-access-to-resources.md)
* [A közös hozzáférésű jogosultságkód (REST API-t) hozzáférés delegálása](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Tábla- és várólista SAS bemutatása](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
