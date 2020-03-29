---
title: A .NET használatával felhasználói delegálási SAS-t hozhat létre egy tárolóhoz vagy blobhoz
titleSuffix: Azure Storage
description: Ismerje meg, hogyan hozhat létre felhasználói delegálássas SAS-t az Azure Active Directory hitelesítő adataival a .NET ügyfélkódtár használatával az Azure Storage-hoz.
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
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Felhasználói delegálássas létrehozása a .NET-tel rendelkező tárolóhoz vagy blobhoz

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan azure Active Directory (Azure AD) hitelesítő adatok létrehozása a felhasználói delegálás SAS egy tároló vagy blob az Azure Storage-ügyfélkódtár .NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>RBAC-szerepkörök hozzárendelése az adatokhoz való hozzáféréshez

Ha egy Azure AD rendszerbiztonsági tag próbál hozzáférni blob adatok, hogy a rendszerbiztonsági tag nak rendelkeznie kell az erőforrás hoz. Függetlenül attól, hogy a rendszerbiztonsági tag felügyelt identitás az Azure-ban, vagy egy Azure AD felhasználói fiók futó kód a fejlesztési környezetben, a rendszerbiztonsági tag kell rendelni egy RBAC szerepkör, amely hozzáférést biztosít a blob adatok az Azure Storage-ban. Az Engedélyek RBAC-on keresztüli hozzárendeléséről az **RBAC-szerepkörök hozzárendelése** a hozzáférési jogokhoz című szakaszban az [Azure-blobokhoz és várólistákhoz való hozzáférés engedélyezése az Azure Active Directory használatával](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)című szakaszban talál.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Ha többet szeretne megtudni arról, hogyan hitelesítheti magát az Azure Identity ügyfélkódtáraz Azure Storage-ból, tekintse meg a **Hitelesítés az Azure Identity könyvtár az Azure** Active Directory és az Azure Resources felügyelt [identitások engedélyezése című](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)szakaszban.

## <a name="add-using-directives"></a>Hozzáadás irányelvekkel

Adja hozzá `using` a következő direktívákat a kódhoz az Azure Identity és az Azure Storage ügyfélkódtárak használatához.

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

## <a name="get-an-authenticated-token-credential"></a>Hitelesített jogkivonat-hitelesítő adatok beszereznie

Egy token hitelesítő adatok bekérése, amely a kód segítségével engedélyezheti az Azure Storage-kérelmeket, hozzon létre egy példányt a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály.

A következő kódrészlet bemutatja, hogyan szerezheti be a hitelesített jogkivonat-hitelesítő adatokat, és hogyan hozhat létre vele egy szolgáltatásügyfelet a Blob storage számára:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>A felhasználói delegálási kulcs beszereznie

Minden SAS egy kulccsal van aláírva. Felhasználói delegálássas sas létrehozásához először meg kell kérnie egy felhasználói delegálási kulcsot, amelyet ezután a SAS aláírására használnak. A felhasználói delegálási kulcs hasonló a szolgáltatás SAS vagy egy fiók SAS aláírásához használt fiókkulcs, azzal a különbséggel, hogy az Azure AD hitelesítő adatait. Amikor egy ügyfél egy OAuth 2.0-s jogkivonathasználatával felhasználói delegálási kulcsot kér, az Azure Storage a felhasználó nevében visszaadja a felhasználó delegálási kulcsát.

Miután rendelkezik a felhasználói delegálási kulccsal, ezzel a kulccsal tetszőleges számú megosztott hozzáférésű felhasználói aláírást hozhat létre a kulcs élettartama alatt. A felhasználói delegálási kulcs független a beszerzéséhez használt OAuth 2.0 jogkivonattól, ezért a jogkivonatot nem kell megújítani mindaddig, amíg a kulcs még érvényes. Megadhatja, hogy a kulcs legfeljebb 7 napig érvényes.You can specify that the key is valid for a period of up 7 days.

A felhasználói delegálási kulcs igényléséhez használja az alábbi módszerek egyikét:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

A következő kódrészlet leveszi a felhasználó delegálási kulcsát, és kiírja annak tulajdonságait:

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

## <a name="create-the-sas-token"></a>A SAS-jogkivonat létrehozása

A következő kódrészlet azt mutatja, hozzon létre egy új [BlobSasBuilder,](/dotnet/api/azure.storage.sas.blobsasbuilder) és adja meg a paramétereket a felhasználói delegálás SAS. A kódrészlet ezután meghívja a [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) a SAS token karakterláncának lekéréséhez. Végül a kód létrehozza a teljes URI-t, beleértve az erőforrás-címet és a SAS-jogkivonatot.

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

## <a name="example-get-a-user-delegation-sas"></a>Példa: Felhasználói delegálássas beszereznie

A következő példamódszer a rendszerbiztonsági tag hitelesítésére és a felhasználói delegálási SAS létrehozására szolgáló teljes kódot mutatja be:

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Példa: Blob olvasása felhasználói delegálású SAS-sel

A következő példa az előző példában létrehozott felhasználói delegálási SAS-t teszteli egy szimulált ügyfélalkalmazásból. Ha a SAS érvényes, az ügyfélalkalmazás képes olvasni a blob tartalmát. Ha a SAS érvénytelen, például ha lejárt, az Azure Storage 403-as (Tiltott) hibakódot ad vissza.

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

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md)
- [Felhasználói delegálási kulcs művelet beszereznie](/rest/api/storageservices/get-user-delegation-key)
- [Felhasználói delegálássas (REST API) létrehozása](/rest/api/storageservices/create-user-delegation-sas)
