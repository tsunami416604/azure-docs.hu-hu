---
title: BLOB verziószámozásának engedélyezése és kezelése (előzetes verzió)
titleSuffix: Azure Storage
description: Megtudhatja, hogyan engedélyezheti a Blobok verziószámozását a Azure Portal vagy egy Azure Resource Manager sablon használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 0e24bcb54fd26d4a3d983681b3348ef736b277cf
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884344"
---
# <a name="enable-and-manage-blob-versioning"></a>BLOB-verziószámozás engedélyezése és kezelése

A Storage-fiókhoz bármikor engedélyezheti vagy letilthatja a blob verziószámozását (előzetes verzió) a Azure Portal vagy egy Azure Resource Manager sablon használatával.

## <a name="enable-blob-versioning"></a>BLOB verziószámozásának engedélyezése

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A blob verziószámozásának engedélyezése a Azure Portalban:

1. Navigáljon a Storage-fiókjához a portálon.
1. Az **blob Service**területen válassza **az adatvédelem**elemet.
1. A **verziószámozás** szakaszban válassza az **engedélyezve**lehetőséget.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="A blob verziószámozásának engedélyezését bemutató képernyőkép Azure Portal":::

# <a name="template"></a>[Sablon](#tab/template)

A Blobok verziószámozásának engedélyezéséhez sablon használatával hozzon létre egy sablont a **IsVersioningEnabled** tulajdonsággal az **igaz**értékre. A következő lépések azt ismertetik, hogyan lehet sablont létrehozni a Azure Portalban.

1. A Azure Portal válassza az **erőforrás létrehozása**lehetőséget.
1. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **template Deployment**lehetőséget, válassza a **Létrehozás**lehetőséget, majd **a szerkesztőben válassza a saját sablon**létrehozása lehetőséget.
1. A sablon szerkesztőjében illessze be a következő JSON-t. Cserélje le `<accountName>` a helyőrzőt a Storage-fiók nevére.
1. Mentse a sablont.
1. Adja meg a fiókhoz tartozó erőforráscsoportot, majd válassza a **vásárlás** gombot a sablon telepítéséhez és a blob verziószámozásának engedélyezéséhez.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Az erőforrások Azure Portal-sablonokkal való üzembe helyezésével kapcsolatos további információkért lásd: [erőforrások központi telepítése Azure Portalokkal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>BLOB módosítása új verzió elindításához

A következő mintakód bemutatja, hogyan indíthat el egy új verziót az Azure Storage ügyféloldali kódtár for .NET 12-es verziójának használatával. A példa futtatása előtt győződjön meg arról, hogy engedélyezte a verziószámozást a Storage-fiókhoz.

A példa létrehoz egy blokk blobot, majd frissíti a blob metaadatait. A blob metaadatainak frissítése elindítja egy új verzió létrehozását. A példa lekéri a kezdeti verziót és a jelenlegi verziót, és azt mutatja, hogy csak az aktuális verzió tartalmazza a metaadatokat.

```csharp
public static async Task UpdateVersionedBlobMetadata(string containerName, string blobName)
{
    // Create a new service client from the connection string.
    BlobServiceClient blobServiceClient = new BlobServiceClient(ConnectionString);

    // Create a new container client.
    BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);

    try
    {
        // Create the container.
        await containerClient.CreateIfNotExistsAsync();

        // Upload a block blob.
        BlockBlobClient blockBlobClient = containerClient.GetBlockBlobClient(blobName);

        string blobContents = string.Format("Block blob created at {0}.", DateTime.Now);
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        string initalVersionId;
        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            Response<BlobContentInfo> uploadResponse = await blockBlobClient.UploadAsync(stream, null, default);

            // Get the version ID for the current version.
            initalVersionId = uploadResponse.Value.VersionId;
        }

        // Update the blob's metadata to trigger the creation of a new version.
        Dictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "key", "value" },
            { "key1", "value1" }
        };

        Response<BlobInfo> metadataResponse = await blockBlobClient.SetMetadataAsync(metadata);

        // Get the version ID for the new current version.
        string newVersionId = metadataResponse.Value.VersionId;

        // Request metadata on the previous version.
        BlockBlobClient initalVersionBlob = blockBlobClient.WithVersion(initalVersionId);
        Response<BlobProperties> propertiesResponse = await initalVersionBlob.GetPropertiesAsync();
        PrintMetadata(propertiesResponse);

        // Request metadata on the current version.
        BlockBlobClient newVersionBlob = blockBlobClient.WithVersion(newVersionId);
        Response<BlobProperties> newPropertiesResponse = await newVersionBlob.GetPropertiesAsync();
        PrintMetadata(newPropertiesResponse);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}

static void PrintMetadata(Response<BlobProperties> propertiesResponse)
{
    if (propertiesResponse.Value.Metadata.Count > 0)
    {
        Console.WriteLine("Metadata values for version {0}:", propertiesResponse.Value.VersionId);
        foreach (var item in propertiesResponse.Value.Metadata)
        {
            Console.WriteLine("Key:{0}  Value:{1}", item.Key, item.Value);
        }
    }
    else
    {
        Console.WriteLine("Version {0} has no metadata.", propertiesResponse.Value.VersionId);
    }
}
```

## <a name="next-steps"></a>További lépések

- [BLOB verziószámozása (előzetes verzió)](versioning-overview.md)
- [Az Azure Storage-blobok helyreállítható törlése](soft-delete-overview.md)
