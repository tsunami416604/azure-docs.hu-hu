---
title: Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz .NET-tel (előzetes verzió) – Azure Storage
description: Megtudhatja, hogyan hozhat létre felhasználói delegálási SAS-t az Azure Storage Azure Active Directory hitelesítő adataival a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: bed95c070649785a701f9d08a98faf29c8ee1413
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990688"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz .NET-tel (előzetes verzió)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory (Azure AD) hitelesítő adatai egy felhasználói delegálási SAS létrehozásához egy tárolóhoz vagy blobhoz a [.net-hez készült Azure Storage ügyféloldali kódtár](https://www.nuget.org/packages/Azure.Storage.Blobs)használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Az előzetes verziójú csomagok telepítése

A jelen cikkben szereplő példák az Azure Storage ügyféloldali kódtár legújabb előzetes verzióját használják a blob Storage-hoz. Az előnézeti csomag telepítéséhez futtassa a következő parancsot a NuGet Package Manager konzolról:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

A cikkben szereplő példák a [.net-hez készült Azure Identity Client Library](https://www.nuget.org/packages/Azure.Identity/) legújabb előzetes verzióját is használják az Azure ad-beli hitelesítő adatokkal való hitelesítéshez. Az Azure Identity Client Library egy rendszerbiztonsági tag hitelesítésére szolgál. A hitelesített rendszerbiztonsági tag ezután létrehozhatja a felhasználói delegálási SAS-t. Az Azure Identity ügyféloldali függvénytárával kapcsolatos további információkért lásd: az [Azure Identity ügyféloldali kódtára a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ha az Azure AD-beli hitelesítő adatokkal szeretne hitelesítést végezni az Azure Identity ügyféloldali kódtár használatával, a kód helyétől függően adjon meg egy egyszerű szolgáltatásnevet vagy egy felügyelt identitást a rendszerbiztonsági tagként. Ha a kód fejlesztési környezetben fut, tesztelési célokra használjon egyszerű szolgáltatásnevet. Ha a kód az Azure-ban fut, használjon felügyelt identitást. Ez a cikk azt feltételezi, hogy a fejlesztési környezetből származó kódot futtat, és bemutatja, hogyan lehet egyszerű szolgáltatásnév használatával létrehozni a felhasználói delegálási SAS-t.

Ha egy egyszerű szolgáltatásnevet szeretne létrehozni az Azure CLI-vel, és hozzárendel egy RBAC-szerepkört, hívja meg az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancsot. Adjon meg egy Azure Storage-adathozzáférési szerepkört az új egyszerű szolgáltatáshoz való hozzárendeléshez. A szerepkörnek tartalmaznia kell a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. Az Azure Storage beépített szerepköreivel kapcsolatos további információkért lásd: [beépített szerepkörök az Azure](../../role-based-access-control/built-in-roles.md)-erőforrásokhoz.

Továbbá adja meg a szerepkör-hozzárendelés hatókörét. Az egyszerű szolgáltatásnév létrehozza a felhasználói delegálási kulcsot, amely a Storage-fiók szintjén végrehajtott művelet, így a szerepkör-hozzárendelés hatókörét a Storage-fiók, az erőforráscsoport vagy az előfizetés szintjén kell végrehajtani. A felhasználói delegálási SAS létrehozásához szükséges engedélyekkel kapcsolatos további információkért tekintse meg a [felhasználói delegálási sas (REST API) létrehozása](/rest/api/storageservices/create-a-user-delegation-sas)a RBAC a **RBAC** című szakaszát.

Ha nem rendelkezik megfelelő engedélyekkel ahhoz, hogy szerepkört rendeljen a szolgáltatáshoz, előfordulhat, hogy meg kell kérnie a fiók tulajdonosát vagy a rendszergazdát, hogy elvégezze a szerepkör-hozzárendelést.

Az alábbi példa az Azure CLI-t használja egy új egyszerű szolgáltatásnév létrehozásához, és hozzárendeli a **Storage blob** adatolvasói szerepkört a fiók hatóköréhez

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

A `az ad sp create-for-rbac` parancs JSON formátumban adja vissza a szolgáltatásnév tulajdonságainak listáját. Másolja ezeket az értékeket, hogy felhasználhassa a szükséges környezeti változókat a következő lépésben.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> A RBAC szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

## <a name="set-environment-variables"></a>Környezeti változók beállítása

Az Azure Identity ügyféloldali függvénytár három környezeti változóból olvassa be az értékeket az egyszerű szolgáltatásnév hitelesítéséhez. A következő táblázat az egyes környezeti változókhoz beállított értéket ismerteti.

|Környezeti változó|Value
|-|-
|`AZURE_CLIENT_ID`|Az egyszerű szolgáltatáshoz tartozó alkalmazás azonosítója
|`AZURE_TENANT_ID`|Az egyszerű szolgáltatás Azure AD-bérlői azonosítója
|`AZURE_CLIENT_SECRET`|Az egyszerű szolgáltatásnév számára létrehozott jelszó

> [!IMPORTANT]
> A környezeti változók beállítása után zárjuk be és nyissa meg újra a konzolablak ablakát. Ha a Visual studiót vagy más fejlesztési környezetet használ, előfordulhat, hogy újra kell indítania a fejlesztési környezetet ahhoz, hogy regisztrálni lehessen az új környezeti változókat.

## <a name="add-using-directives"></a>Hozzáadás irányelvekkel

Adja hozzá a `using` következő irányelveket a kódhoz az Azure Identity és az Azure Storage ügyféloldali kódtárainak előzetes verziójának használatához.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>Az egyszerű szolgáltatás hitelesítése

Az egyszerű szolgáltatás hitelesítéséhez hozzon létre egy példányt a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztályból. A `DefaultAzureCredential` konstruktor beolvassa a korábban létrehozott környezeti változókat.

A következő kódrészlet bemutatja, hogyan kérheti le a hitelesített hitelesítő adatokat, és hogyan hozhat létre szolgáltatási ügyfelet a blob Storage-hoz

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS-token létrehozása

A következő kódrészlet az új [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) létrehozását mutatja be, és megadja a felhasználói delegálási sas paramétereit. A kódrészlet ezután meghívja a [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) az SAS-jogkivonat karakterláncának beolvasásához. Végül a kód létrehozza a teljes URI-t, beleértve az erőforrás-címeket és az SAS-tokent.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Példa: Felhasználói delegálási SAS beszerzése

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
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Példa: BLOB beolvasása felhasználói delegálási SAS-vel

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
    catch (StorageRequestFailedException e)
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

- [Felhasználói delegálási kulcs műveletének beolvasása](/rest/api/storageservices/get-user-delegation-key)
- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-a-user-delegation-sas)
