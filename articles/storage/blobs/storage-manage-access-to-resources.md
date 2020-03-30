---
title: Tárolók és blobok nyilvános olvasási hozzáférésének kezelése
titleSuffix: Azure Storage
description: Ismerje meg, hogyan teheti elérhetővé a tárolókat és a blobokat a névtelen hozzáféréshez, és hogyan érheti el őket programozott módon.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255482"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Tárolók és blobok névtelen olvasási hozzáférésének kezelése

Az Azure Blob-tárolókban egy tárolóhoz és annak blobjaihoz névtelen, nyilvános olvasási hozzáférés engedélyezhető. Ezzel csak olvasási hozzáférést adhat ezekhez az erőforrásokhoz enélkül, hogy megosztaná a fiók kulcsát, vagy közös hozzáférésű jogosultságkódra volna szükség.

A nyilvános olvasási hozzáférés olyan esetekben a legjobb, ha bizonyos blobok mindig elérhetők a névtelen olvasási hozzáféréshez. A részletesebb vezérléshez létrehozhat egy közös hozzáférési aláírást. A megosztott hozzáférésű aláírások lehetővé teszik, hogy korlátozott hozzáférést biztosítson különböző engedélyek használatával egy adott időszakra. A megosztott hozzáférésű aláírások létrehozásáról további információt a [Megosztott hozzáférésű aláírások (SAS) használata az Azure Storage-ban című témakörben talál.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Névtelen felhasználók engedélyek engedélyezése tárolókhoz és blobokhoz

Alapértelmezés szerint a tárolóhoz és az abban elhelyezkedő blobokhoz csak olyan felhasználó férhet hozzá, akinek megadta a megfelelő engedélyeket. Ha névtelen felhasználóknak szeretne olvasási hozzáférést adni egy tárolóhoz és annak blobjaihoz, beállíthatja a tároló nyilvános hozzáférési szintjét. Ha nyilvános hozzáférést ad egy tárolóhoz, akkor a névtelen felhasználók a kérés hitelesítése nélkül olvashatják a nyilvánosan elérhető tárolón belüli blobokat.

A tárolókat a következő engedélyekkel konfigurálhatja:

- **Nincs nyilvános olvasási hozzáférés:** A tároló és a blobok csak a tárfiók tulajdonosa érhető el. Ez az alapértelmezett az összes új tárolók.
- **Nyilvános olvasási hozzáférés csak blobok esetén:** Blobok a tárolón belül névtelen kéréssel olvasható, de a tároló adatok nem érhetők el. A névtelen ügyfelek nem soraszthatják fel a tárolón belüli blobokat.
- **Nyilvános olvasási hozzáférés a tárolóhoz és a blobokhoz:** Az összes tároló- és blobadat névtelen kéréssel olvasható. Az ügyfelek névtelen kéréssel számba vehetik a tárolón belüli blobokat, de a tárfiókon belüli tárolókat nem.

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Tároló nyilvános hozzáférési szintjének beállítása az Azure Portalon

Az [Azure Portalon](https://portal.azure.com)frissítheti a nyilvános hozzáférési szintet egy vagy több tárolóhoz:

1. Keresse meg a tárfiók áttekintését az Azure Portalon.
1. A **menüpanel Blob szolgáltatás csoportjában** válassza a **Blobok**lehetőséget.
1. Válassza ki azokat a tárolókat, amelyekhez be szeretné állítani a nyilvános hozzáférési szintet.
1. A **hozzáférési szint módosítása** gombbal megjelenítheti a nyilvános hozzáférési beállításokat.
1. Válassza ki a kívánt nyilvános hozzáférési szintet a **Nyilvános hozzáférési szint** legördülő menüből, és kattintson az OK gombra a módosítás nak a kijelölt tárolókra való alkalmazásához.

A következő képernyőkép bemutatja, hogyan módosíthatja a nyilvános hozzáférési szintet a kiválasztott tárolókhoz.

![Képernyőkép a nyilvános hozzáférési szint beállításáról a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Az egyes blobok nyilvános hozzáférési szintjét nem módosíthatja. A nyilvános hozzáférési szint csak a tároló szintjén van beállítva.

### <a name="set-container-public-access-level-with-net"></a>Tároló nyilvános hozzáférési szintjének beállítása a .NET segítségével

Ha engedélyeket szeretne beállítani egy tárolóhoz az Azure Storage-ügyfélkódtár használatával .

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

Ezután állítsa be a **PublicAccess** tulajdonságot a **GetPermissions** metódus által visszaadott [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) objektumon.

Végül hívja meg az alábbi módszerek egyikét a tároló engedélyeinek frissítéséhez:

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

A következő példa beállítja a tároló engedélyeit a teljes nyilvános olvasási hozzáférésre. Ha csak a blobok nyilvános olvasási hozzáférési engedélyeit szeretné beállítani, állítsa a **PublicAccess** tulajdonságot **BlobContainerPublicAccessType.Blob .Blob .t.** A névtelen felhasználók összes engedélyének eltávolításához állítsa a tulajdonságot **BlobContainerPublicAccessType.Off .**

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Tárolók és blobok elérése névtelenül

A tárolókhoz és blobokhoz névtelenül hozzáférő ügyfél olyan konstruktorokat használhat, amelyek nem igényelnek hitelesítő adatokat. Az alábbi példák bemutatják néhány különböző módon hivatkozzon tárolók és blobok névtelenül.

### <a name="create-an-anonymous-client-object"></a>Névtelen ügyfélobjektum létrehozása

Létrehozhat egy új szolgáltatás ügyfélobjektumot a névtelen hozzáféréshez a Blob-tároló végpont biztosításával a fiókhoz. Azonban azt is tudnia kell, hogy a fiókban lévő tároló neve elérhető a névtelen hozzáféréshez.

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

### <a name="reference-a-container-anonymously"></a>Tároló ra hivatkozzon névtelenül

Ha rendelkezik egy névtelenül elérhető tároló URL-címére, használhatja közvetlenül a tárolóra való hivatkozáshoz.

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

### <a name="reference-a-blob-anonymously"></a>Hivatkozás egy blobra névtelenül

Ha rendelkezik egy névtelen hozzáféréshez elérhető blob URL-címével, a blobra közvetlenül az adott URL-cím használatával hivatkozhat:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>További lépések

- [Hozzáférés engedélyezése az Azure Storage-hoz](../common/storage-auth.md)
- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md)
- [A Blob szolgáltatás REST API-ja](/rest/api/storageservices/blob-service-rest-api)
