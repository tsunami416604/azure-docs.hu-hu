---
title: Bevezetés a Blob (objektum) tárolásba – Azure Storage
description: Azure Blob Storage nagy mennyiségű strukturálatlan objektumadat, például szöveg vagy bináris adat tárolására szolgál. Az Azure Blob Storage nagymértékben skálázható és magas rendelkezésre állású. Az ügyfelek a PowerShellből vagy az Azure CLI-ből érhetik el a Blob Storage tárolóban lévő adatobjektumokat, programozhatóan az Azure Storage ügyfélkódtárakon keresztül vagy REST használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/18/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ff1d34462680ddd6be7f1a47d9a27594bcce4ff6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061491"
---
# <a name="introduction-to-azure-blob-storage"></a>Az Azure Blob Storage bemutatása

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Blob-tárolási erőforrások

A Blob storage háromféle erőforrást kínál:

- A **tárfiók**
- Tároló **container** a tárfiókban
- **Blob egy** tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Tárfiók, tárolók és blobok közötti kapcsolatot bemutató diagram](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Tárfiókok

A tárfiók egyedi névteret biztosít az Azure-ban az adatokszámára. Az Azure Storage-ban tárolt minden objektum rendelkezik egy címmel, amely tartalmazza az Ön egyedi fióknevét. A fiók név és az Azure Storage blob végpont kombinációja képezi a tárfiókban lévő objektumok alapcímét.

Ha például a tárfiók neve *mystorageaccount,* akkor a Blob storage alapértelmezett végpontja a következő:

```
http://mystorageaccount.blob.core.windows.net 
```

Tárfiók létrehozásához [lásd: Tárfiók létrehozása.](../common/storage-account-create.md) A tárfiókokról az [Azure storage-fiók áttekintése című témakörben olvashat bővebben.](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="containers"></a>Containers

A tároló a fájlrendszerben lévő könyvtárhoz hasonlóan blobok készletét rendezi. Egy tárfiók korlátlan számú tárolót tartalmazhat, egy tároló pedig korlátlan számú blob tárolására használható. 

  > [!NOTE]
  > A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók elnevezéséről a [Tárolók, blobok és metaadatok elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)című témakörben talál további információt.

### <a name="blobs"></a>Blobok
 
Az Azure Storage háromféle blobot támogat:

- **A blokkblobok** szöveget és bináris adatokat tárolnak, akár 4,7 TB-ig. A blokkblobok önállóan felügyelhető adatblokkokból állnak.
- **A hozzáfűző blobok** blokkokból, például blokkblobokból állnak, de hozzáfűző műveletekre vannak optimalizálva. A hozzáfűző blobok ideálisak például a virtuális gépek adatainak naplózásához és hasonló forgatókönyvekhez.
- **A lapblobok** legfeljebb 8 TB méretű véletlen hozzáférésű fájlokat tárolnak. A lapblobok virtuális merevlemez-fájlokat (VHD) tárolnak, és az Azure virtuális gépek lemezeiként szolgálnak. A lapblobokról az [Azure-lapblobok áttekintése című](storage-blob-pageblob-overview.md) témakörben olvashat bővebben.

A különböző típusú blobokról a [Blokkblobok, a Hozzáfűző blobok és a Lapblobok ismertetése](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)című témakörben talál további információt.

## <a name="move-data-to-blob-storage"></a>Adatok áthelyezése a Blob Storage-ba

Számos megoldás létezik a meglévő adatok Blob storage-ba történő áttelepítésére:

- **Az AzCopy** egy könnyen használható windowsos és linuxos parancssori eszköz, amely adatokat másol a Blob storage-ba, a tárolók között vagy a tárfiókok között. Az AzCopy programról további információt [az Adatok átvitele az AzCopy v10(Előzetes verzió) alkalmazásban című témakörben talál.](../common/storage-use-azcopy-v10.md) 
- Az **Azure Storage Data Movement könyvtár** egy .NET-kódtár az Azure Storage-szolgáltatások közötti átmozgatáshoz. Az AzCopy segédprogram az Adatmozgatás könyvtárral együtt épül fel. További információt az Adatmozgatási könyvtár [referenciadokumentációjában](/dotnet/api/microsoft.azure.storage.datamovement) talál. 
- **Az Azure Data Factory** támogatja az adatok másolását a Blob storage-ba és onnan a fiókkulcs, a megosztott hozzáférés-aláírás, a szolgáltatás névvel ellátott vagy felügyelt identitások az Azure-erőforrások használatával. További információ: [Adatok másolása azure blob tárhelyre vagy azure Data Factory használatával.](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 
- **A Blobfuse** az Azure Blob storage virtuális fájlrendszer-illesztőprogramja. A blobfuse segítségével elérheti a meglévő blokkblob-adatokat a storage-fiókjában a Linux fájlrendszeren keresztül. További információ: [Blob storage csatlakoztatása fájlrendszerként blobfuse.For](storage-how-to-mount-container-linux.md)more information, to How to mount Blob storage as a file system with blobfuse .
- **Az Azure Data Box** szolgáltatás akkor érhető el a Blob storage-ba való helyszíni adatok átviteléhez, ha a nagy adatkészletek vagy hálózati korlátozások irreálissá teszik az adatok hálózaton keresztüli feltöltését. Az adatok méretétől függően azure [data box lemezt,](../../databox/data-box-disk-overview.md) [Azure Data Boxot](../../databox/data-box-overview.md)vagy [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) eszközöket kérhet a Microsofttól. Ezután átmásolhatja az adatokat ezekre az eszközökre, és visszaküldheti őket a Microsoftnak a Blob storage-ba való feltöltéshez.
- Az **Azure importálási/exportálási szolgáltatás** lehetővé teszi nagy mennyiségű adat importálását vagy exportálását a tárfiókba az Ön által megadott merevlemezek használatával. További információt [a Microsoft Azure importálási/exportálási szolgáltatásának használata a Blob storage-ba történő átviteléhez című témakörben talál.](../common/storage-import-export-service.md)

## <a name="next-steps"></a>További lépések

- [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Méretezhetőségi és teljesítménycélok a Blob storage-hoz](scalability-targets.md)
