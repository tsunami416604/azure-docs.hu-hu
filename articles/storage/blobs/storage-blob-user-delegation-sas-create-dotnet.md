---
title: Felhasználói delegálási SAS létrehozása egy tárolóhoz vagy blobhoz a .NET használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre Azure Active Directory hitelesítő adatokkal rendelkező felhasználói delegálási SAS-t az Azure Storage-hoz készült .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371836"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz .NET-tel

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory (Azure AD) hitelesítő adatai egy felhasználói delegálási SAS létrehozásához egy tárolóhoz vagy blobhoz a .NET-hez készült Azure Storage ügyféloldali kódtár használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-szerepkörök kiosztása az adathoz való hozzáféréshez

Amikor egy Azure AD rendszerbiztonsági tag megpróbál hozzáférni a blob-adatforráshoz, a rendszerbiztonsági tag engedélyekkel kell rendelkeznie az erőforráshoz. Azt jelzi, hogy a rendszerbiztonsági tag felügyelt identitás-e az Azure-ban vagy egy olyan Azure AD-felhasználói fiók, amely kódot futtat a fejlesztési környezetben, a rendszerbiztonsági tag számára olyan RBAC-szerepkört kell hozzárendelni, amely hozzáférést biztosít az Azure Storage-beli blob- Az engedélyek **RBAC-n** keresztüli hozzárendelésével kapcsolatos információkért tekintse meg az [Azure-blobok és-várólisták hozzáférésének engedélyezése a Azure Active Directory használatával](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)című témakör című szakaszát.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Ha többet szeretne megtudni arról, hogyan lehet hitelesíteni az Azure Identity ügyféloldali függvénytárát az Azure Storage-ból, tekintse meg a **hitelesítés az Azure Identity Library** használatával című szakaszt a [blobokhoz és várólistákhoz való hozzáférés engedélyezése a Azure Active Directory és felügyelt identitások Azure-erőforrásokhoz](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)című részben.

## <a name="add-using-directives"></a>Hozzáadás irányelvekkel

Adja hozzá a `using` következő irányelveket a kódhoz az Azure Identity és az Azure Storage ügyféloldali kódtárainak használatához.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Hitelesített jogkivonat hitelesítő adatainak beolvasása

Ha meg szeretné kapni a jogkivonat hitelesítő adatait, amelyet a kód használhat az Azure Storage-ba irányuló kérések engedélyezéséhez, hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát.

A következő kódrészlet bemutatja, hogyan kérheti le a hitelesített jogkivonat hitelesítő adatait, és hogyan hozhat létre szolgáltatási ügyfelet a blob Storage-hoz:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Felhasználói delegálási kulcs beszerzése

Minden SAS egy kulccsal van aláírva. Felhasználói delegálási SAS létrehozásához először egy felhasználói delegálási kulcsot kell igényelnie, amelyet a rendszer az SAS aláírására használ. A felhasználói delegálási kulcs hasonlít a szolgáltatás SAS-vagy fiók-SAS-aláírásához használt fiók kulcsához, azzal a különbséggel, hogy az Azure AD-beli hitelesítő adataira támaszkodik. Amikor egy ügyfél egy OAuth 2,0-token használatával kér felhasználói delegálási kulcsot, az Azure Storage a felhasználó nevében visszaadja a felhasználói delegálási kulcsot.

A felhasználó delegálási kulcsának megadásával a kulcs használatával tetszőleges számú felhasználói delegálási közös hozzáférési aláírás hozható létre. A felhasználói delegálási kulcs független a beszerzéséhez használt OAuth 2,0 tokentől, így a tokent nem kell megújítani, amíg a kulcs még érvényes. Azt is megadhatja, hogy a kulcs legfeljebb 7 napig érvényes legyen.

A következő módszerek egyikével kérheti le a felhasználói delegálási kulcsot:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

A következő kódrészlet lekéri a felhasználói delegálási kulcsot, és kiírja a tulajdonságait:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS-token létrehozása

A következő kódrészlet az új [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) létrehozását mutatja be, és megadja a felhasználói delegálási sas paramétereit. A kódrészlet ezután meghívja a [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) az SAS-jogkivonat karakterláncának beolvasásához. Végül a kód létrehozza a teljes URI-t, beleértve az erőforrás-címeket és az SAS-tokent.

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Példa: felhasználói delegálási SAS beszerzése

A következő példa a rendszerbiztonsági tag hitelesítéséhez és a felhasználói delegálás SAS létrehozásához használható teljes kódot mutatja be:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Példa: blob beolvasása felhasználói delegálási SAS-vel

A következő példa egy szimulált ügyfélalkalmazás által az előző példában létrehozott felhasználói delegálási SAS tesztelését teszteli. Ha az SAS érvényes, az ügyfélalkalmazás el tudja olvasni a blob tartalmát. Ha az SAS érvénytelen, például ha lejárt, az Azure Storage a 403-as hibakódot (tiltott) adja vissza.

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md)
- [Felhasználói delegálási kulcs műveletének beolvasása](/rest/api/storageservices/get-user-delegation-key)
- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
