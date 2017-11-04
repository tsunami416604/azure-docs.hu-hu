---
title: "A tárolók és blobok az Azure Blob storage nyilvános olvasási hozzáférés engedélyezése |} Microsoft Docs"
description: "Útmutató: elérhetővé tárolók és blobok névtelen hozzáféréshez, és hogyan programokon keresztül férhet hozzá."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: f52079c72be298daaa45074e516f911022780392
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Tárolók és blobok névtelen olvasási hozzáférésének kezelése
Engedélyezheti a névtelen, nyilvános olvasási hozzáférés egy tároló és a benne található blobokat az Azure Blob Storage tárolóban. Ezzel a módszerrel csak olvasható hozzáférést biztosíthat ezeken az erőforrásokon a fiókkulcs megosztása nélkül, és anélkül, hogy a közös hozzáférésű jogosultságkód (SAS).

Nyilvános olvasási hozzáférés esetén ajánlott forgatókönyvek, ahol azt szeretné, hogy egyes blobok névtelen olvasási hozzáférés mindig elérhetőek. Részletesebb vezérlést létrehozhat egy közös hozzáférésű jogosultságkódot. Közös hozzáférésű jogosultságkód lehetővé teszik a különböző engedélyeket használ egy adott időszakra vonatkozóan korlátozott hozzáférést biztosítanak. Megosztott létrehozásával kapcsolatos további információért aláírások hozzáférni, lásd: [használata közös hozzáférésű jogosultságkód (SAS) az Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>A tárolók és blobok névtelen felhasználók engedélyeket
Alapértelmezés szerint a tároló és szereplő bármely BLOB érhetik csak a tárfiók tulajdonosa által. Lehetőséget nyújt a névtelen felhasználóknak olvasási engedéllyel a tároló és a benne található blobokat, beállíthatja a tároló engedélyeit, hogy a nyilvános hozzáférést. Névtelen felhasználók olvashatják a nyilvánosan elérhető tárolóban található blobok a kérelem hitelesítése nélkül.

Egy tároló is konfigurálhatja a következő engedélyekkel:

* **Nincs nyilvános olvasási hozzáférés:** a tároló és a benne található blobokat elérhetők, csak a fiók tulajdonosa. Ez az alapértelmezés az összes új tárolókat.
* **Nyilvános olvasási hozzáférés a blobok csak:** a tárolóban található Blobok névtelen kérelem által olvasható, de adatai nem érhető el. Névtelen ügyfeleket nem tudja felsorolni a BLOB a tárolóban.
* **Teljes nyilvános olvasási hozzáférés:** minden tároló és a blob adatainak névtelen kérelem által olvasható. Ügyfelek is számba venni a tárolóban található blobok névtelen kérelem, de nem tudja felsorolni a tárolók a tárfiókon belül.

Tároló engedélyek beállítása a következő használható:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programozott módon egyikével a storage ügyfélkódtáraival vagy a REST API

### <a name="set-container-permissions-in-the-azure-portal"></a>Tároló engedélyeinek beállítása az Azure-portálon
Tároló engedélyek beállítása a [Azure-portálon](https://portal.azure.com), kövesse az alábbi lépéseket:

1. Nyissa meg a **tárfiók** a portálon. A tárfiók található kiválasztásával **tárfiókok** a portál főmenü paneljén.
1. A **BLOB szolgáltatás** menü paneljén válassza **tárolók**.
1. Kattintson a jobb gombbal a tároló sorban, vagy válassza ki a tároló megnyitása a három pont **helyi menü**.
1. Válassza ki **házirendhez** a helyi menü.
1. Válasszon egy **hozzáférési típus** a legördülő menüből.

    ![Szerkessze a Csomagtároló metaadatai párbeszédpanel](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>A .NET tároló engedélyeinek beállítása
A C# és a Storage ügyféloldali kódtára a .NET használatával tároló engedélyeinek beállítása, először a tároló meglévő engedélyek lekérdezése meghívásával a **GetPermissions** metódust. Utána állítsa be a **PublicAccess** tulajdonságát a **BlobContainerPermissions** által visszaadott objektum a **GetPermissions** metódust. Végezetül hívja a **SetPermissions** a frissített engedélyekkel rendelkező metódust.

A következő példa a teljes nyilvános olvasási hozzáférés a tároló engedélyeket állít be. Engedélyek beállítása a blobok csak nyilvános olvasási hozzáférés, állítsa be a **PublicAccess** tulajdonságot **BlobContainerPublicAccessType.Blob**. Távolítsa el az összes engedélyt a névtelen felhasználók esetében, állítsa be a tulajdonságot **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Tárolók és blobok névtelen eléréséhez
Ügyfél tárolók és blobok névtelen hozzáféréshez használhatja a konstruktorok, amelyekhez nem szükséges hitelesítő adatokat. A következő példák azt szemléltetik erőforrásokra kell hivatkoznia Blob szolgáltatás névtelenül fejlesztésében többféle formában.

### <a name="create-an-anonymous-client-object"></a>Névtelen ügyfél objektum létrehozása
Létrehozhat egy új ügyfél szolgáltatásobjektum névtelen hozzáférés a Blob-szolgáltatásvégpont megadásával a fiók. Azonban is ismernie kell az adott fiók által elérhető névtelen hozzáférés a tároló nevét.

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

### <a name="reference-a-container-anonymously"></a>Egy tároló névtelenül hivatkozik
Ha olyan tároló, amely névtelenül elérhető URL-CÍMÉT, használhatja a tároló közvetlenül hivatkozni.

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

### <a name="reference-a-blob-anonymously"></a>Egy blob névtelenül hivatkozik
Ha elérhető a névtelen hozzáféréshez blob URL-CÍMÉT, melyeket referenciaként használhat, a blob, közvetlenül az adott URL-cím segítségével:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>A névtelen felhasználók számára elérhető szolgáltatások
Az alábbi táblázat bemutatja, hogy milyen műveletek is hívható névtelen felhasználók számára, ha egy tároló hozzáférés-vezérlési lista úgy van beállítva, hogy a nyilvános hozzáférést.

| REST-művelet | Teljes körű nyilvános olvasási joggal rendelkező engedély | A blobok csak nyilvános olvasási joggal rendelkező engedély |
| --- | --- | --- |
| Tárolók listája |Csak a tulajdonos |Csak a tulajdonos |
| Tároló létrehozása |Csak a tulajdonos |Csak a tulajdonos |
| A tároló tulajdonságainak beolvasása |Összes |Csak a tulajdonos |
| Tároló metaadatot beszerezni |Összes |Csak a tulajdonos |
| Állítsa be a Csomagtároló metaadatai |Csak a tulajdonos |Csak a tulajdonos |
| Tároló hozzáférés-vezérlési lista beolvasása |Csak a tulajdonos |Csak a tulajdonos |
| Tároló hozzáférés-vezérlési lista beállítása |Csak a tulajdonos |Csak a tulajdonos |
| Törli a tárolót |Csak a tulajdonos |Csak a tulajdonos |
| Lista Blobok |Összes |Csak a tulajdonos |
| Helyezze a Blob |Csak a tulajdonos |Csak a tulajdonos |
| A Blob beolvasása |Összes |Összes |
| A Blob tulajdonságainak beolvasása |Összes |Összes |
| A Blob tulajdonságainak beállítása |Csak a tulajdonos |Csak a tulajdonos |
| A Blob metaadatot beszerezni |Összes |Összes |
| Állítsa be a Blob metaadatai |Csak a tulajdonos |Csak a tulajdonos |
| PUT letiltása |Csak a tulajdonos |Csak a tulajdonos |
| (Csak a véglegesített blokkolja) tiltólista beolvasása |Összes |Összes |
| (Csak a nem véglegesített blokkok vagy blokkok) tiltólista beolvasása |Csak a tulajdonos |Csak a tulajdonos |
| PUT tiltólista |Csak a tulajdonos |Csak a tulajdonos |
| A Blob törlése |Csak a tulajdonos |Csak a tulajdonos |
| A Blob másolása |Csak a tulajdonos |Csak a tulajdonos |
| Pillanatkép Blob |Csak a tulajdonos |Csak a tulajdonos |
| Címbérlet Blob |Csak a tulajdonos |Csak a tulajdonos |
| Helyezze a lap |Csak a tulajdonos |Csak a tulajdonos |
| Get tartományokat |Összes |Összes |
| Hozzáfűző Blob |Csak a tulajdonos |Csak a tulajdonos |

## <a name="next-steps"></a>Következő lépések

* [Az Azure Storage szolgáltatásainak hitelesítése](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Közös hozzáférésű Jogosultságkód (SAS) használatával](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Hozzáférés delegálása közös hozzáférésű jogosultságkód használatával](https://msdn.microsoft.com/library/azure/ee395415.aspx)
