---
title: Nyilvános olvasási hozzáférés engedélyezése tárolók és Blobok számára az Azure Blob Storage-ban | Microsoft Docs
description: Megtudhatja, hogyan tehet elérhetővé tárolókat és blobokat névtelen hozzáféréshez, és hogyan érheti el azokat programozott módon.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 6293fc84969c4e246c05da4482f76142263db230
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985545"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Tárolók és blobok névtelen olvasási hozzáférésének kezelése

Engedélyezheti a névtelen, nyilvános olvasási hozzáférést egy tárolóhoz és a blobokhoz az Azure Blob Storage-ban. Így csak olvasási hozzáférést biztosíthat ezekhez az erőforrásokhoz anélkül, hogy meg kellene osztania a fiók kulcsát, és nem igényel közös hozzáférési aláírást (SAS).

A nyilvános olvasási hozzáférés a legjobb olyan helyzetekben, amikor azt szeretné, hogy bizonyos Blobok mindig elérhetők legyenek a névtelen olvasási hozzáféréshez. A részletesebb szabályozás érdekében létrehozhat egy közös hozzáférési aláírást. A közös hozzáférésű aláírások lehetővé teszik a korlátozott hozzáférés biztosítását különböző engedélyekkel egy adott időszakban. A megosztott hozzáférési aláírások létrehozásával kapcsolatos további információkért lásd: [közös hozzáférésű aláírások (SAS) használata az Azure Storage-ban](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Névtelen felhasználók engedélyeinek megadása tárolók és Blobok számára

Alapértelmezés szerint a tárolók és a benne lévő Blobok csak olyan felhasználók férhetnek hozzá, akik megfelelő engedélyekkel rendelkeznek. A tároló nyilvános hozzáférési szintjének megadásával engedélyezheti a névtelen felhasználók számára, hogy olvasási hozzáférést biztosítson egy tárolóhoz és a blobokhoz. Ha nyilvános hozzáférést biztosít egy tárolóhoz, a névtelen felhasználók a kérelem engedélyezése nélkül is elolvashatják a blobokat egy nyilvánosan elérhető tárolón belül.

A tárolót a következő engedélyekkel konfigurálhatja:

* **Nincs nyilvános olvasási hozzáférés:** A tároló és a hozzá tartozó Blobok csak a Storage-fiók tulajdonosa számára érhetők el. Ez az alapértelmezett érték minden új tárolónál.
* **Nyilvános olvasási hozzáférés csak Blobok esetén:** A tárolóban lévő Blobok névtelen kéréssel is olvashatók, de a tárolók nem érhetők el. A névtelen ügyfelek nem tudják enumerálni a tárolóban lévő blobokat.
* **Nyilvános olvasási hozzáférés a tárolóhoz és a blobokhoz:** A tárolók és a Blobok összes adatolvasása névtelen kérelem alapján végezhető el. Az ügyfelek a tárolóban lévő blobokat névtelen kérelem alapján enumerálják, de a tárolóban lévő tárolók nem sorolhatók fel.

A tároló engedélyeinek beállításához a következőket használhatja:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programozott módon, a Storage ügyféloldali kódtárak vagy a REST API használatával

### <a name="set-container-public-access-level-in-the-azure-portal"></a>A tároló nyilvános hozzáférési szintjének beállítása a Azure Portalban

A [Azure Portal](https://portal.azure.com)egy vagy több tároló nyilvános hozzáférési szintjének frissítését végezheti el:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A menü panel **blob Service** területén válassza a **Blobok**elemet.
1. Válassza ki azokat a tárolókat, amelyekhez a nyilvános hozzáférési szintet be szeretné állítani.
1. A **hozzáférési szint módosítása** gomb használatával jelenítse meg a nyilvános hozzáférési beállításokat.
1. Válassza ki a **nyilvános hozzáférési szint** legördülő menüből a kívánt nyilvános hozzáférési szintet, majd az OK gombra kattintva alkalmazza a módosítást a kijelölt tárolók elemre.

Az alábbi képernyőképen megtudhatja, hogyan módosíthatja a kijelölt tárolók nyilvános hozzáférési szintjét.

![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Az egyes Blobok nyilvános hozzáférési szintje nem módosítható. A nyilvános hozzáférési szint csak a tároló szintjén állítható be.

### <a name="set-container-public-access-level-with-net"></a>Tároló nyilvános hozzáférési szintjének beállítása a .NET-tel

Ha a és a .NET-hez C# készült Storage ügyféloldali kódtára segítségével szeretne engedélyeket beállítani egy tárolóhoz, először kérje le a tároló meglévő engedélyeit a **GetPermissions** metódus meghívásával. Ezután állítsa be a **GetPermissions** metódus által visszaadott **BlobContainerPermissions** objektum **PublicAccess** tulajdonságát. Végül hívja meg a **SetPermissions** metódust a frissített engedélyekkel.

A következő példa a tároló engedélyeit állítja be a teljes nyilvános olvasási hozzáféréshez. Ha csak a Blobok nyilvános olvasási hozzáférését szeretné beállítani, állítsa a **PublicAccess** tulajdonságot a **BlobContainerPublicAccessType. blob**értékre. A névtelen felhasználókra vonatkozó összes engedély eltávolításához állítsa a tulajdonságot **BlobContainerPublicAccessType. off**értékre.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Tárolók és Blobok névtelen elérésének elérése

A tárolókat és blobokat elérő ügyfelek névtelenül használhatják a hitelesítő adatokat nem igénylő konstruktorokat. Az alábbi példák a tárolók és Blobok névtelen módon történő hivatkozásának néhány különböző módját mutatják be.

### <a name="create-an-anonymous-client-object"></a>Névtelen ügyfél-objektum létrehozása

A névtelen hozzáféréshez létrehozhat egy új szolgáltatás-ügyféloldali objektumot, ha megadja a fiókhoz tartozó blob Storage-végpontot. Azonban ismernie kell egy olyan tároló nevét is a fiókban, amely elérhető a névtelen hozzáféréshez.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Tároló hivatkozása névtelenül

Ha egy névtelenül elérhető tároló URL-címe van, akkor használhatja a tároló közvetlen hivatkozására.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>BLOB hivatkozása névtelenül

Ha rendelkezik egy névtelen hozzáféréshez elérhető blob URL-címével, akkor közvetlenül a következő URL-cím használatával hivatkozhat a blobra:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Névtelen felhasználók számára elérhető funkciók

A következő táblázat azt mutatja be, hogy mely műveleteket lehet névtelenül meghívni, ha egy tároló nyilvános hozzáférésre van konfigurálva.

| REST-művelet | Nyilvános olvasási hozzáférés a tárolóhoz | Nyilvános olvasási hozzáférés csak a blobokhoz |
| --- | --- | --- |
| Tárolók listázása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tároló létrehozása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tároló tulajdonságainak beolvasása | Névtelen kérelmek engedélyezve | Csak az erre vonatkozó kérelmek |
| Tároló metaadatainak beolvasása | Névtelen kérelmek engedélyezve | Csak az erre vonatkozó kérelmek |
| Tároló metaadatainak beállítása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tároló ACL lekérése | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tároló ACL beállítása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tároló törlése | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Blobok listázása | Névtelen kérelmek engedélyezve | Csak az erre vonatkozó kérelmek |
| Put Blob | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| BLOB beolvasása | Névtelen kérelmek engedélyezve | Névtelen kérelmek engedélyezve |
| BLOB tulajdonságainak beolvasása | Névtelen kérelmek engedélyezve | Névtelen kérelmek engedélyezve |
| BLOB tulajdonságainak beállítása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Blob metaadatainak beolvasása | Névtelen kérelmek engedélyezve | Névtelen kérelmek engedélyezve |
| BLOB metaadatainak beállítása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Put blokk | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tiltási lista lekérése (csak véglegesített blokkok esetén) | Névtelen kérelmek engedélyezve | Névtelen kérelmek engedélyezve |
| Tiltási lista lekérése (csak a nem véglegesített blokkok vagy az összes blokk) | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Tiltási lista | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Delete Blob | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Blob másolása | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Snapshot Blob | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Címbérleti blob | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Oldal elhelyezése | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |
| Oldalak tartományának beolvasása | Névtelen kérelmek engedélyezve | Névtelen kérelmek engedélyezve |
| Blob hozzáfűzése | Csak az erre vonatkozó kérelmek | Csak az erre vonatkozó kérelmek |

## <a name="next-steps"></a>További lépések

* [Az Azure Storage szolgáltatásainak engedélyezése](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Közös hozzáférésű aláírások (SAS) használata](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)