---
title: A blob (Object) tároló bemutatása
titleSuffix: Azure Storage
description: Azure Blob Storage nagy mennyiségű strukturálatlan objektumadat, például szöveg vagy bináris adat tárolására szolgál. Az Azure Blob Storage nagymértékben skálázható és magas rendelkezésre állású. Az ügyfelek a PowerShellből vagy az Azure CLI-ből érhetik el a Blob Storage tárolóban lévő adatobjektumokat, programozhatóan az Azure Storage ügyfélkódtárakon keresztül vagy REST használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/18/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 084b364f4a1414febb7b6f11c9cf70958ee823c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176669"
---
# <a name="introduction-to-azure-blob-storage"></a>Az Azure Blob Storage bemutatása

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>BLOB Storage-erőforrások

A blob Storage háromféle típusú erőforrást kínál:

- A **Storage-fiók**
- Egy **tároló a Storage** -fiókban
- Egy tárolóban lévő **blob**

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Storage-fiók, a tárolók és a Blobok közötti kapcsolatot bemutató ábra](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Tárfiókok

A Storage-fiók egyedi névteret biztosít az Azure-ban az adataihoz. Az Azure Storage-ban tárolt összes objektumhoz tartozik egy olyan címe, amely tartalmazza az egyedi fióknevet. A fióknév és az Azure Storage blob-végpont kombinációja a Storage-fiókban lévő objektumok alapcímeit képezi.

Ha például a Storage-fiók neve *mystorageaccount*, akkor a blob Storage alapértelmezett végpontja a következő:

```
http://mystorageaccount.blob.core.windows.net
```

A Storage-fiók létrehozásával kapcsolatban tekintse meg [a Storage-fiók létrehozása](../common/storage-account-create.md)című témakört. További információ a Storage-fiókokról: az [Azure Storage-fiók áttekintése](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containers

Egy tároló blobokat szervez, hasonlóan a fájlrendszer egy könyvtárához. Egy tárfiók korlátlan számú tárolót tartalmazhat, egy tároló pedig korlátlan számú blob tárolására használható.

> [!NOTE]
> A tárolók nevei csak kisbetűket tartalmazhatnak. További információ a tárolók elnevezéséről: [tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Blobok

Az Azure Storage három típusú blobot támogat:

- A **Blobok** a szöveg és a bináris adat tárolására, körülbelül 4,7 TB-ra. A blokkblobok önállóan felügyelhető adatblokkokból állnak.
- A **hozzáfűzési Blobok** olyan blokkokból állnak, mint a blokkos Blobok, de a hozzáfűzési műveletekre vannak optimalizálva. A hozzáfűző blobok ideálisak például a virtuális gépek adatainak naplózásához és hasonló forgatókönyvekhez.
- Az **oldal Blobok** legfeljebb 8 TB méretű véletlenszerű hozzáférési fájlokat tárolhatnak. Az oldal Blobok tárolják a virtuális merevlemezeket (VHD-fájlokat), és az Azure-beli virtuális gépek lemezként szolgálnak. További információ a lapok Blobokkal kapcsolatban: [Az Azure-oldal Blobok áttekintése](storage-blob-pageblob-overview.md)

A Blobok különböző típusaival kapcsolatos további információkért lásd: a [Blobok, a Blobok hozzáfűzése és az oldal Blobok ismertetése](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Adatok áthelyezése a Blob Storage-ba

Számos megoldás létezik a meglévő és a blob Storage-tárolóba való áttelepítéshez:

- A **AzCopy** egy könnyen használható parancssori eszköz a Windows és a Linux rendszerhez, amely a blob Storage-ba, a tárolók között vagy a Storage-fiókokba másolt adatok másolására szolgál. További információ a AzCopy: [adatok átvitele a AzCopy v10-vel (előzetes verzió)](../common/storage-use-azcopy-v10.md).
- Az **Azure Storage adatátviteli könyvtára** egy .net-függvénytár az Azure Storage-szolgáltatások közötti adatáthelyezéshez. A AzCopy segédprogram az adatátviteli függvénytárral együtt épül fel. További információt az adatátviteli [függvénytár dokumentációjában](/dotnet/api/microsoft.azure.storage.datamovement) talál.
- **Azure Data Factory** támogatja az adatok másolását a blob Storage-ba és a-ból a fiók kulcsa, a közös hozzáférési aláírás, az egyszerű szolgáltatásnév vagy a felügyelt identitások használatával az Azure-erőforrásokhoz. További információ: [adatok másolása az Azure Blob Storage-ba vagy onnan az Azure Data Factory használatával](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- A **Blobfuse** egy virtuális fájlrendszer-illesztőprogram az Azure Blob Storage szolgáltatáshoz. A blobfuse használatával elérheti a meglévő blokk blob-adatait a Storage-fiókban a Linux fájlrendszerén keresztül. További információ: a [blob Storage csatlakoztatása fájlrendszerként a blobfuse](storage-how-to-mount-container-linux.md)használatával.
- **Azure Data Box** szolgáltatás elérhető a helyszíni adatok blob Storage-ba történő átviteléhez, ha a nagyméretű adathalmazok vagy hálózati korlátozások miatt az adatok feltöltése nem reális. Az adatmérettől függően [Azure Data Box Disk](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md)vagy [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) eszközt kérhet a Microsofttól. Ezután átmásolhatja az adatait az eszközökre, és visszaküldheti azokat a Microsoftnak a blob Storage-ba való feltöltéshez.
- Az **Azure import/export szolgáltatás** lehetővé teszi nagy mennyiségű adatok importálását és exportálását a Storage-fiókba az Ön által megadott merevlemez-meghajtók használatával. További információ: [a Microsoft Azure import/export szolgáltatás használata adatok blob Storage-tárolóba történő átviteléhez](../common/storage-import-export-service.md).

## <a name="next-steps"></a>További lépések

- [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [A blob Storage méretezhetőségi és teljesítménybeli céljai](scalability-targets.md)
