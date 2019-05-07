---
title: Nyilvános olvasási hozzáférés tárolók és blobok az Azure Blob storage-ban engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan elérhetővé tárolók és blobok névtelen hozzáférés, és hogyan programozott módon érheti el őket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148357"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Tárolók és blobok névtelen olvasási hozzáférésének kezelése

Engedélyezheti a névtelen, nyilvános olvasási hozzáférést a tárolóhoz és annak blobjaihoz, az Azure Blob storage-ban. Ezzel a módszerrel csak olvasási hozzáféréssel ezekhez az erőforrásokhoz a fiókkulcs megosztása nélkül, és anélkül, hogy egy közös hozzáférésű jogosultságkód (SAS) adhat meg.

Nyilvános olvasási hozzáférés az egyes blobok névtelen olvasási hozzáférés meg, ahová forgatókönyvek esetén ajánlott használni. A részletesebb szabályozás érdekében létrehozhat egy közös hozzáférésű jogosultságkód. Közös hozzáférésű jogosultságkódok lehetővé teszi különböző engedélyek használatával egy adott időtartamra korlátozott hozzáférést biztosíthat. További információ a megosztott hozzáférési aláírások című témakörben talál [a közös hozzáférésű jogosultságkódok (SAS) az Azure Storage-ban](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Tárolók és blobok névtelen felhasználók engedélyek megadása

Alapértelmezés szerint egy tárolót, és minden benne lévő blobok előfordulhat, hogy kizárólag számára legyen elérhető, amely rendelkezik engedélyekkel rendelkező felhasználó. Névtelen felhasználók számára olvasási hozzáférést a tárolóhoz és annak blobjaihoz megadását beállíthatja a tároló nyilvános hozzáférés szintje. Tárolóba való nyilvános hozzáférést engedélyez, majd a névtelen felhasználók elolvashatja egy nyilvánosan elérhető tárolóban található blobok anélkül a kérelmet.

Konfigurálhat egy tárolót a következő engedélyekkel:

* **Nincs nyilvános olvasási hozzáférés:** A tárolóhoz és annak blobjaihoz csak a tárfiók tulajdonosa férhet. Ez az összes új tárolók alapértelmezett.
* **Nyilvános olvasási hozzáférés csak blobok esetén:** A tárolóban található blobok névtelen kérelem által is olvasható, de adatokat tároló nem érhető el. Ügyfelek névtelen nem lehet számba venni a a tárolóban lévő blobokat.
* **Nyilvános olvasási hozzáférést a tárolóhoz és annak blobjaihoz:** Az összes tárolót és Blobadatok névtelen kérelem által olvasható. Ügyfelek névtelen kérelem által a tárolóban található blobok enumerálása, de nem sikerült felsorolni a tárfiókban lévő tárolók.

A következő segítségével a tároló engedélyeinek beállítása:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programozott módon a storage ügyfélkódtáraival vagy a REST API segítségével szerint

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Állítsa be a tároló nyilvános hozzáférés szintje az Azure Portalon

Az a [az Azure portal](https://portal.azure.com), egy vagy több tároló nyilvános hozzáférési szintjét is frissítheti:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. A **Blob service** menü panelen válassza ki a **Blobok**.
1. Válassza ki a tárolókat, amelynek meg szeretné beállítani a nyilvános hozzáférés szintje.
1. Használja a **hozzáférési szint módosítása** gombra kattintva jelenítse meg a nyilvános hozzáférés beállításait.
1. Válassza ki a kívánt nyilvános hozzáférési szintjét a **nyilvános hozzáférés szintje** legördülő menüben kattintson az OK gombra a módosítás alkalmazásához a kiválasztott tárolókat.

Az alábbi képernyőfelvételen a kiválasztott tárolókat nyilvános hozzáférési szintjének módosítása.

![Képernyőfelvétel: hogyan állíthatja be a nyilvános hozzáférés szintje a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Egy adott BLOB nyilvános hozzáférés szintje nem módosítható. Nyilvános hozzáférés szintje csak a tároló szintjén.

### <a name="set-container-public-access-level-with-net"></a>Állítsa be a tároló nyilvános hozzáférés szintje a .NET használatával

.NET-hez készült C# és a Storage ügyféloldali kódtár használatával egy tároló engedélyeinek beállítása, meghívásával először kérjen le a meglévő engedélyeket a tárolót a **GetPermissions** metódust. Majd állítsa be a **PublicAccess** tulajdonsága a **BlobContainerPermissions** által visszaadott objektum a **GetPermissions** metódust. Végezetül hívja meg a **SetPermissions** metódus az engedélyekkel rendelkező.

Az alábbi példa a teljes nyilvános olvasási hozzáférés a tároló engedélyeket állít be. Engedélyek beállítása a nyilvános olvasási hozzáférés csak blobokhoz, állítsa be a **PublicAccess** tulajdonságot **BlobContainerPublicAccessType.Blob**. Névtelen felhasználók számára az összes engedélyének törléséhez a tulajdonsága **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Tárolók és blobok névtelen hozzáférés

Ügyfél, amely hozzáfér a tárolók és blobok névtelen hitelesítő adatok nem igénylő konstruktorok használhatja. Az alábbi példák bemutatják a különböző módokon tárolók és blobok névtelenül hivatkozni.

### <a name="create-an-anonymous-client-object"></a>Névtelen ügyfél objektum létrehozása

Létrehozhat egy új szolgáltatás ügyfélobjektummal névtelen hozzáférés a Blobtároló végpontjától azáltal, hogy a fiókhoz. Azonban azt is tudnia kell, amely érhető el a névtelen hozzáférés a fiókban lévő tároló nevét.

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

### <a name="reference-a-container-anonymously"></a>Egy tároló hivatkozhat névtelenül

Ha egy tároló, amely névtelenül elérhető URL-címe van, használhatja közvetlenül hivatkozni a tárolót.

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

### <a name="reference-a-blob-anonymously"></a>Blobok névtelenül hivatkozik

Ha az URL-cím, egy blobhoz elérhető névtelen hozzáférés, a blob közvetlenül az adott URL-cím használatával is lehet hivatkozni:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Névtelen felhasználók számára elérhető szolgáltatások

Az alábbi táblázatban látható, hogy mely műveletek eltérő elnevezése lehet névtelenül tároló konfigurálásakor nyilvánosan elérhető.

| REST-művelet | Nyilvános olvasási hozzáférés a tároló | Nyilvános olvasási hozzáférés csak a blobokhoz |
| --- | --- | --- |
| Tárolók listája | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Tároló létrehozása | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| A tároló tulajdonságainak beolvasása | Névtelen kérések engedélyezett | Csak a hitelesített kérelmek |
| Tároló metaadatainak beolvasása | Névtelen kérések engedélyezett | Csak a hitelesített kérelmek |
| Set Container Metadata | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Tároló ACL lekérése | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Set Container ACL | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Tároló törlése | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Blobok listázása | Névtelen kérések engedélyezett | Csak a hitelesített kérelmek |
| Put Blob | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| A Blob lekérése | Névtelen kérések engedélyezett | Névtelen kérések engedélyezett |
| A Blob tulajdonságainak lekérése | Névtelen kérések engedélyezett | Névtelen kérések engedélyezett |
| A Blob tulajdonságainak megadása | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Blob metaadatainak beolvasása | Névtelen kérések engedélyezett | Névtelen kérések engedélyezett |
| Állítsa be a Blob metaadatai | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| PUT letiltása | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| (Csak a véglegesített blokkolja) blokk-lista lekérése | Névtelen kérések engedélyezett | Névtelen kérések engedélyezett |
| (Csak a nem véglegesített blokkok vagy az összes blokkok) blokk-lista lekérése | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| PUT tiltólista | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Delete Blob | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Blob másolása | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Snapshot Blob | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Címbérleti Blobhoz | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| PUT lap | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |
| Laptartomány-beolvasási | Névtelen kérések engedélyezett | Névtelen kérések engedélyezett |
| Blob hozzáfűzése | Csak a hitelesített kérelmek | Csak a hitelesített kérelmek |

## <a name="next-steps"></a>További lépések

* [Az Azure Storage szolgáltatásainak engedélyezése](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Közös hozzáférésű Jogosultságkódok (SAS) használata](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)