---
title: A Blob Storage bemutatása – Objektumtárolás az Azure-ban
description: Azure Blob Storage nagy mennyiségű strukturálatlan objektumadat, például szöveg vagy bináris adat tárolására szolgál. Az Azure Blob Storage nagymértékben skálázható és magas rendelkezésre állású. Az ügyfelek a PowerShellből vagy az Azure CLI-ből érhetik el a Blob Storage tárolóban lévő adatobjektumokat, programozhatóan az Azure Storage ügyfélkódtárakon keresztül vagy REST használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 05/24/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e216503cac2db55115bd4c1b5bf0e2f6e50355fc
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190857"
---
# <a name="introduction-to-azure-blob-storage"></a>Az Azure Blob Storage bemutatása

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>BLOB storage-erőforrások

A BLOB storage három típusú erőforrásokat nyújt:

- A **tárfiók**. 
- A **tároló** a storage-fiókban
- A **blob** -tárolóban 

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![a Naplótárolásifiók-Blob és tároló-erőforrások közötti kapcsolat](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Tárfiókok

Storage-fiók az adatok az Azure-ban egy egyedi névteret biztosít. Az Azure Storage-ban minden egyes objektum, amely tartalmazza az egyedi fióknevet címmel rendelkezik. A fiók nevét és az Azure Storage-blob végpont kombinációja a tárfiókban lévő objektumok základní adresa képezi.

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
* **Lapblobok** store véletlenszerű elérésű fájlok tárolhatók legfeljebb 8 TB méretű. A lapblobok virtuális merevlemez (VHD) fájlok tárolására és állnak az Azure-beli virtuális gépek lemezeinek. A lapblobok kapcsolatos további információkért lásd: [áttekintése az Azure-lapblobokkal](storage-blob-pageblob-overview.md)

Blobok különböző típusaival kapcsolatos további információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Adatok áthelyezése a Blob storage

Számos megoldás létezik, a meglévő adatok áttelepítése a Blob storage:

- **Az AzCopy** van egy könnyen használható parancssori eszköz a Windows és Linux rendszerekre, amely adatokat másol és Blob storage, az egyes tárolók között, vagy tárfiókok között. Az AzCopy kapcsolatos további információkért lásd: [adatátvitel az AzCopy v10 (előzetes verzió)](../common/storage-use-azcopy-v10.md). 
- A **Azure Storage Adatáthelyezés könyvtár** egy .NET-kódtár adatok áthelyezése az Azure Storage szolgáltatás között. Az AzCopy segédprogram épül, és az adatok áthelyezését. További információkért lásd: a [referenciadokumentációt](/dotnet/api/microsoft.azure.storage.datamovement) a Adatáthelyezés szalagtár. 
- **Az Azure Data Factory** támogatja az adatok másolása Blob storage szolgáltatásba vagy onnan a fiókkulcs, közös hozzáférésű jogosultságkódok, a szolgáltatás egyszerű, vagy a felügyelt identitások a Azure-erőforrások hitelesítéséről. További információkért lásd: [másolhat az Azure Blob storage-ból az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** van a virtuális fájlrendszer illesztőprogramja az Azure Blob storage. Blobfuse használhatja a meglévő blokkblobok adataival eléréséhez a Linux fájlrendszeren keresztül a Storage-fiókban. További információkért lásd: [Blob-tároló csatlakoztatása fájlrendszerként való blobfuse hogyan](storage-how-to-mount-container-linux.md).
- **Az Azure Data Box** szolgáltatás a helyszíni adatok átviteléhez a Blob Storage nagy méretű adatkészletek és a hálózati korlátok, hogy a kvótán túli adatfeltöltés szempontjából a hálózaton keresztül sokfélesége érhető el. Az adatok méretétől függően kérhet [Azure Data Box-lemezek](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md), vagy [Azure Data Box nehéz](../../databox/data-box-heavy-overview.md) eszközökhöz a Microsoft. Ezután az adatok másolása az eszközök és szállíthat őket a Microsoftnak kell feltölteni a Blob storage-bA.
- A **Azure Import/Export szolgáltatás** lehetővé teszi a importálása és exportálása a nagy mennyiségű adatot, és az Ön tárfiókjából, Ön által megadott merevlemezek használatával. További információkért lásd: [a Microsoft Azure Import/Export szolgáltatás használata az adatok átviteléhez a Blob storage](../common/storage-import-export-service.md).

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Az Azure Storage méretezhetőségi és teljesítménycéljai](../common/storage-scalability-targets.md)
