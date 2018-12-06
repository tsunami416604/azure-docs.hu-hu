---
title: A Blob Storage bemutatása – Objektumtárolás az Azure-ban
description: Azure Blob Storage nagy mennyiségű strukturálatlan objektumadat, például szöveg vagy bináris adat tárolására szolgál. Az Azure Blob Storage nagymértékben skálázható és magas rendelkezésre állású. Az ügyfelek a PowerShellből vagy az Azure CLI-ből érhetik el a Blob Storage tárolóban lévő adatobjektumokat, programozhatóan az Azure Storage ügyfélkódtárakon keresztül vagy REST használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 11/19/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 7628260efff34b52ca7d4bd4c35cce279d5474b3
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965410"
---
# <a name="introduction-to-azure-blob-storage"></a>Az Azure Blob Storage bemutatása

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>BLOB storage-erőforrások

A BLOB storage három típusú erőforrásokat nyújt:

- A **tárfiók**. 
- A **tároló** a storage-fiókban
- A **blob** -tárolóban 

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Blob storage-architektúra ábrája](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Tárfiókok

Storage-fiók az adatok az Azure-ban egy egyedi névteret biztosít. Az Azure Storage-ban minden egyes objektum, amely tartalmazza az egyedi fióknevet címmel rendelkezik. A fiók nevét és az Azure Storage-szolgáltatásvégpont kombinációja képezi a tárfiók végpontjait.

Például, ha a tárfiók neve *mystorageaccount*, akkor az alapértelmezett végpont a Blob Storage:

```
http://mystorageaccount.blob.core.windows.net 
```

Storage-fiók létrehozásához lásd: [hozzon létre egy tárfiókot](../common/storage-quickstart-create-account.md). Storage-fiókokkal kapcsolatos további tudnivalókért lásd: [az Azure storage-fiók áttekintése](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containers

Egy tároló rendszerezi a blobok hasonló ahhoz a könyvtárhoz, a fájlrendszer egy készletét. Egy tárfiók korlátlan számú tárolót tartalmazhat, egy tároló pedig korlátlan számú blob tárolására használható. 

  > [!NOTE]
  > A tárolók nevei csak kisbetűket tartalmazhatnak. Elnevezési tárolókkal kapcsolatos további információkért lásd: [elnevezése és a hivatkozó tárolók, Blobok és metaadatok](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobok
 
Az Azure Storage háromféle blobot támogatja:

* **Blokkblobok** legfeljebb nagyjából 4,7 TB szöveges és bináris adatok tárolására. A blokkblobok önállóan felügyelhető adatblokkokból állnak.
* **Hozzáfűző blobok** blokkokból épülnek fel, mint a blokkblobok használatát támogatják, de vannak optimalizálva műveletek hozzáfűzésére. A hozzáfűző blobok ideálisak például a virtuális gépek adatainak naplózásához és hasonló forgatókönyvekhez.
* **Lapblobok** store véletlenszerű elérésű fájlok tárolhatók legfeljebb 8 TB méretű. A lapblobok a virtuális merevlemez (VHD) fájlok állnak az Azure-beli virtuális gépek lemezeinek tárolóban. Fore lapblobok kapcsolatos további információkért lásd: (.. / articles/storage/blobs/storage-blob-pageblob-overview.md)

Blobok különböző típusaival kapcsolatos további információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Adatok áthelyezése a Blob storage

Számos megoldás létezik, a meglévő adatok áttelepítése a Blob storage:

- **Az AzCopy** van egy könnyen használható parancssori eszköz a Windows és Linux rendszerekre, amely adatokat másol és Blob storage, az egyes tárolók között, vagy tárfiókok között. Az AzCopy kapcsolatos további információkért lásd: [adatátvitel az AzCopy v10 (előzetes verzió)](../common/storage-use-azcopy-v10.md). 
- A **Azure Storage Adatáthelyezés könyvtár** egy .NET-kódtár adatok áthelyezése az Azure Storage szolgáltatás között. Az AzCopy segédprogram épül, és az adatok áthelyezését. További információkért lásd: a [referenciadokumentációt](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement) a Adatáthelyezés szalagtár. 
- **Az Azure Data Factory** támogatja az adatok másolása Blob storage szolgáltatásba vagy onnan a fiókkulcs, közös hozzáférésű jogosultságkódok, a szolgáltatás egyszerű, vagy a felügyelt identitások a Azure-erőforrások hitelesítéséről. További információkért lásd: [másolhat az Azure Blob storage-ból az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** van a virtuális fájlrendszer illesztőprogramja az Azure Blob storage. Blobfuse használhatja a meglévő blokkblobok adataival eléréséhez a Linux fájlrendszeren keresztül a Storage-fiókban. További információkért lásd: [Blob-tároló csatlakoztatása fájlrendszerként való blobfuse hogyan](storage-how-to-mount-container-linux.md).
- **Az Azure Data Box-lemezek** átviteléhez a helyszíni adatok Blob storage nagy méretű adatkészletek és a hálózati korlátok, hogy a kvótán túli adatfeltöltés szempontjából a hálózaton keresztül sokfélesége szolgáltatás. Az [Azure Data Box Disk](../../databox/data-box-disk-overview.md) használatával SSD-meghajtókat kérhet a Microsofttól. Ezután az adatokat a meghajtókra másolhatja, azokat pedig visszaküldheti, hogy a Microsoft feltöltse az adatokat a Blob Storage-ba.
- A **Azure Import/Export szolgáltatás** lehetővé teszi a nagy mennyiségű adat exportálása merevlemez-meghajtókat, amelyek adnia és vissza a adatait, majd mobilplatform, hogy a Microsoft a tárfiókból. További információkért lásd: [a Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob storage](../common/storage-import-export-service.md).

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Az Azure Storage méretezhetőségi és teljesítménycéljai](../common/storage-scalability-targets.md)
