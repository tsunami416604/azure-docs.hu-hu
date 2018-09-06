---
title: Nyilvános olvasási hozzáférés tárolók és blobok az Azure Blob storage-ban engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan elérhetővé tárolók és blobok névtelen hozzáférés, és hogyan programozott módon érheti el őket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: bf7dabc1c3765d86e7a0f87acaa6f06a68d3d530
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782008"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Tárolók és blobok névtelen olvasási hozzáférésének kezelése
Engedélyezheti a névtelen, nyilvános olvasási hozzáférést a tárolóhoz és annak blobjaihoz, az Azure Blob storage-ban. Ezzel a módszerrel csak olvasási hozzáféréssel ezekhez az erőforrásokhoz a fiókkulcs megosztása nélkül, és anélkül, hogy egy közös hozzáférésű jogosultságkód (SAS) adhat meg.

Nyilvános olvasási hozzáférés az egyes blobok névtelen olvasási hozzáférés meg, ahová forgatókönyvek esetén ajánlott használni. A részletesebb szabályozás érdekében létrehozhat egy közös hozzáférésű jogosultságkód. Közös hozzáférésű jogosultságkódok lehetővé teszi különböző engedélyek használatával egy adott időtartamra korlátozott hozzáférést biztosíthat. További információ a megosztott hozzáférési aláírások című témakörben talál [a közös hozzáférésű jogosultságkódok (SAS) az Azure Storage-ban](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Tárolók és blobok névtelen felhasználók engedélyek megadása
Alapértelmezés szerint egy tárolót, és minden benne lévő blobok érhetik el csak a tárfiók tulajdonosa. Névtelen felhasználók számára, olvassa el a tárolóhoz és annak blobjaihoz biztosíthat, beállíthatja a tároló nyilvános hozzáférésének engedélyezése engedélyekkel. Névtelen felhasználók olvashatják a nyilvánosan elérhető tárolóban található blobok a kérelem hitelesítése nélkül.

Konfigurálhat egy tárolót a következő engedélyekkel:

* **Nincs nyilvános olvasási hozzáférés:** a tárolóhoz és annak blobjaihoz elérhetők, csak a tárfiók tulajdonosa. Ez az összes új tárolók alapértelmezett.
* **Nyilvános olvasási hozzáférés csak blobok:** a tárolóban található Blobok névtelen kérelem által is olvasható, de adatokat tároló nem érhető el. Ügyfelek névtelen nem lehet számba venni a a tárolóban lévő blobokat.
* **Teljes nyilvános olvasási hozzáférés:** minden tároló és blobnév adat névtelen kérelem által olvasható. Ügyfelek névtelen kérelem által a tárolóban található blobok enumerálása, de nem sikerült felsorolni a tárfiókban lévő tárolók.

A következő segítségével a tároló engedélyeinek beállítása:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programozott módon a storage ügyfélkódtáraival vagy a REST API segítségével szerint

### <a name="set-container-permissions-in-the-azure-portal"></a>Az Azure Portalon a tároló engedélyeinek beállítása
Tároló engedélyeinek beállítása a [az Azure portal](https://portal.azure.com), kövesse az alábbi lépéseket:

1. Nyissa meg a **tárfiók** panel a portálon. Annak a storage-fiók kiválasztásával **tárfiókok** a fő menü a portálon a panelen.
1. A **BLOB SERVICE** menü panelen válassza ki a **Blobok**.
1. A tároló sorban kattintson a jobb gombbal, vagy kattintson a három pontra, nyissa meg a tároló **helyi menü**.
1. Válassza ki **hozzáférési szabályzat** a helyi menüben.
1. Válasszon egy **típus eléréséhez** a legördülő menüből.

    ![Tároló metaadatai párbeszédpanel szerkesztése](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>.NET-tel tároló engedélyeinek beállítása
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
Ügyfél, amely hozzáfér a tárolók és blobok névtelen hitelesítő adatok nem igénylő konstruktorok használhatja. Az alábbi példák bemutatják a különböző módokon erőforrásokra kell hivatkoznia a Blob szolgáltatás névtelenül.

### <a name="create-an-anonymous-client-object"></a>Névtelen ügyfél objektum létrehozása
Létrehozhat egy új szolgáltatás ügyfélobjektummal névtelen hozzáférés a Blob-szolgáltatásvégpont azáltal, hogy a fiókhoz. Azonban azt is tudnia kell, amely érhető el a névtelen hozzáférés a fiókban lévő tároló nevét.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
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
Az alábbi táblázatban látható, hogy mely műveletek előfordulhat, hogy hívható névtelen felhasználók számára, ha ACL-t egy tároló nyilvános hozzáférésének engedélyezése.

| REST-művelet | Teljes körű nyilvános olvasási hozzáféréssel rendelkező engedély | Engedéllyel rendelkező nyilvános olvasási hozzáférés csak blobok esetén |
| --- | --- | --- |
| Tárolók listája |Csak a tulajdonosa |Csak a tulajdonosa |
| Tároló létrehozása |Csak a tulajdonosa |Csak a tulajdonosa |
| A tároló tulajdonságainak beolvasása |Összes |Csak a tulajdonosa |
| Tároló metaadatainak beolvasása |Összes |Csak a tulajdonosa |
| Állítsa be a tároló metaadatai |Csak a tulajdonosa |Csak a tulajdonosa |
| Tároló ACL lekérése |Csak a tulajdonosa |Csak a tulajdonosa |
| ACL tároló beállítása |Csak a tulajdonosa |Csak a tulajdonosa |
| Tároló törlése |Csak a tulajdonosa |Csak a tulajdonosa |
| Blobok listázása |Összes |Csak a tulajdonosa |
| Put Blob |Csak a tulajdonosa |Csak a tulajdonosa |
| A Blob lekérése |Összes |Összes |
| A Blob tulajdonságainak lekérése |Összes |Összes |
| A Blob tulajdonságainak megadása |Csak a tulajdonosa |Csak a tulajdonosa |
| Blob metaadatainak beolvasása |Összes |Összes |
| Állítsa be a Blob metaadatai |Csak a tulajdonosa |Csak a tulajdonosa |
| PUT letiltása |Csak a tulajdonosa |Csak a tulajdonosa |
| (Csak a véglegesített blokkolja) blokk-lista lekérése |Összes |Összes |
| (Csak a nem véglegesített blokkok vagy az összes blokkok) blokk-lista lekérése |Csak a tulajdonosa |Csak a tulajdonosa |
| PUT tiltólista |Csak a tulajdonosa |Csak a tulajdonosa |
| Delete Blob |Csak a tulajdonosa |Csak a tulajdonosa |
| Blob másolása |Csak a tulajdonosa |Csak a tulajdonosa |
| Snapshot Blob |Csak a tulajdonosa |Csak a tulajdonosa |
| Címbérleti Blobhoz |Csak a tulajdonosa |Csak a tulajdonosa |
| PUT lap |Csak a tulajdonosa |Csak a tulajdonosa |
| Laptartomány-beolvasási |Összes |Összes |
| Hozzáfűző Blob |Csak a tulajdonosa |Csak a tulajdonosa |

## <a name="next-steps"></a>További lépések

* [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Közös hozzáférésű Jogosultságkódok (SAS) használata](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Hozzáférés delegálása közös hozzáférésű jogosultságkód használatával](https://msdn.microsoft.com/library/azure/ee395415.aspx)
