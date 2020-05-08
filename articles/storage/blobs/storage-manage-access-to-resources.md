---
title: A tárolók és Blobok nyilvános olvasási hozzáférésének kezelése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan tehet elérhetővé tárolókat és blobokat névtelen hozzáféréshez, és hogyan érheti el azokat programozott módon.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7c524cb30b73c95329650924123b2ebc26a5d8a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856007"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Tárolók és blobok névtelen olvasási hozzáférésének kezelése

Az Azure Blob-tárolókban egy tárolóhoz és annak blobjaihoz névtelen, nyilvános olvasási hozzáférés engedélyezhető. Ezzel csak olvasási hozzáférést adhat ezekhez az erőforrásokhoz enélkül, hogy megosztaná a fiók kulcsát, vagy közös hozzáférésű jogosultságkódra volna szükség.

A nyilvános olvasási hozzáférés a legjobb olyan helyzetekben, amikor azt szeretné, hogy bizonyos Blobok mindig elérhetők legyenek a névtelen olvasási hozzáféréshez. A részletesebb szabályozás érdekében létrehozhat egy közös hozzáférési aláírást. A közös hozzáférésű aláírások lehetővé teszik a korlátozott hozzáférés biztosítását különböző engedélyekkel egy adott időszakban. A megosztott hozzáférési aláírások létrehozásával kapcsolatos további információkért lásd: [közös hozzáférésű aláírások (SAS) használata az Azure Storage-ban](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Névtelen felhasználók engedélyeinek megadása tárolók és Blobok számára

Alapértelmezés szerint a tárolóhoz és az abban elhelyezkedő blobokhoz csak olyan felhasználó férhet hozzá, akinek megadta a megfelelő engedélyeket. Ha névtelen felhasználóknak szeretne olvasási hozzáférést adni egy tárolóhoz és annak blobjaihoz, beállíthatja a tároló nyilvános hozzáférési szintjét. Ha nyilvános hozzáférést ad egy tárolóhoz, akkor a névtelen felhasználók a kérés hitelesítése nélkül olvashatják a nyilvánosan elérhető tárolón belüli blobokat.

A tárolót a következő engedélyekkel konfigurálhatja:

- **Nincs nyilvános olvasási hozzáférés:** A tároló és a hozzá tartozó Blobok csak a Storage-fiók tulajdonosa számára érhetők el. Ez az alapértelmezett érték minden új tárolónál.
- **Nyilvános olvasási hozzáférés csak Blobok esetén:** A tárolóban lévő Blobok névtelen kéréssel is olvashatók, de a tárolók nem érhetők el. A névtelen ügyfelek nem tudják enumerálni a tárolóban lévő blobokat.
- **Nyilvános olvasási hozzáférés a tárolóhoz és a blobokhoz:** A tárolók és a Blobok összes adatolvasása névtelen kérelem alapján végezhető el. Az ügyfelek a tárolóban lévő blobokat névtelen kérelem alapján enumerálják, de a tárolóban lévő tárolók nem sorolhatók fel.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>A tároló nyilvános hozzáférési szintjének beállítása a Azure Portalban

A [Azure Portal](https://portal.azure.com)egy vagy több tároló nyilvános hozzáférési szintjének frissítését végezheti el:

1. A Azure Portalban navigáljon a Storage-fiók áttekintéséhez.
1. A menü panel **blob Service** területén válassza a **Blobok**elemet.
1. Válassza ki azokat a tárolókat, amelyekhez a nyilvános hozzáférési szintet be szeretné állítani.
1. A **hozzáférési szint módosítása** gomb használatával jelenítse meg a nyilvános hozzáférési beállításokat.
1. Válassza ki a **nyilvános hozzáférési szint** legördülő menüből a kívánt nyilvános hozzáférési szintet, majd az OK gombra kattintva alkalmazza a módosítást a kijelölt tárolók elemre.

Az alábbi képernyőképen megtudhatja, hogyan módosíthatja a kijelölt tárolók nyilvános hozzáférési szintjét.

![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Az egyes Blobok nyilvános hozzáférési szintje nem módosítható. A nyilvános hozzáférési szint csak a tároló szintjén állítható be.

### <a name="set-container-public-access-level-with-net"></a>Tároló nyilvános hozzáférési szintjének beállítása a .NET-tel

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

A tároló engedélyeinek megadásához hívja meg a [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) metódust. 

A következő példa a tároló engedélyeit állítja be a teljes nyilvános olvasási hozzáféréshez. Ha csak a Blobok nyilvános olvasási hozzáférésére vonatkozó engedélyeket kíván beállítani, adja át az **PublicAccessType. blob** mezőt a [BlobContainerClient. SetAccessPolicy](https://docs.microsoft.com/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy?view=azure-dotnet) metódusnak. A névtelen felhasználókra vonatkozó összes engedély eltávolításához használja az **BlobContainerPublicAccessType. None** mezőt.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_SetPublicContainerPermissions":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

Ha a .NET-hez készült Azure Storage ügyféloldali kódtára használatával szeretné beállítani egy tároló engedélyeit, először kérje le a tároló meglévő engedélyeit az alábbi módszerek egyikének meghívásával:

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Ezután állítsa be a **PublicAccess** tulajdonságot a **GetPermissions** metódus által visszaadott [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) objektumra.

Végül hívja meg az alábbi módszerek egyikét a tároló engedélyeinek frissítéséhez:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

A következő példa a tároló engedélyeit állítja be a teljes nyilvános olvasási hozzáféréshez. Ha csak a Blobok nyilvános olvasási hozzáférését szeretné beállítani, állítsa a **PublicAccess** tulajdonságot a **BlobContainerPublicAccessType. blob**értékre. A névtelen felhasználókra vonatkozó összes engedély eltávolításához állítsa a tulajdonságot **BlobContainerPublicAccessType. off**értékre.

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

---

## <a name="access-containers-and-blobs-anonymously"></a>Tárolók és Blobok névtelen elérésének elérése

A tárolókat és blobokat elérő ügyfelek névtelenül használhatják a hitelesítő adatokat nem igénylő konstruktorokat. Az alábbi példák a tárolók és Blobok névtelen módon történő hivatkozásának néhány különböző módját mutatják be.

### <a name="create-an-anonymous-client-object"></a>Névtelen ügyfél-objektum létrehozása

A névtelen hozzáféréshez létrehozhat egy új szolgáltatás-ügyféloldali objektumot, ha megadja a fiókhoz tartozó blob Storage-végpontot. Azonban ismernie kell egy olyan tároló nevét is a fiókban, amely elérhető a névtelen hozzáféréshez.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
``` 

---

### <a name="reference-a-container-anonymously"></a>Tároló hivatkozása névtelenül

Ha egy névtelenül elérhető tároló URL-címe van, akkor használhatja a tároló közvetlen hivatkozására.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
``` 

---

### <a name="reference-a-blob-anonymously"></a>BLOB hivatkozása névtelenül

Ha rendelkezik egy névtelen hozzáféréshez elérhető blob URL-címével, akkor közvetlenül a következő URL-cím használatával hivatkozhat a blobra:

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
``` 

---

## <a name="next-steps"></a>További lépések

- [Hozzáférés engedélyezése az Azure Storage-hoz](../common/storage-auth.md)
- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md)
- [A Blob szolgáltatás REST API-ja](/rest/api/storageservices/blob-service-rest-api)
