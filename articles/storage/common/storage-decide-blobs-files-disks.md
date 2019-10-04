---
title: Az Azure-Blobok, a Azure Files-vagy az Azure-lemezek használatának eldöntése
description: Ismerje meg az Azure-ban tárolt adattárolási és-hozzáférés különböző módszereit, amelyek segítségével eldöntheti, hogy melyik technológiát kívánja használni.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671047"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Az Azure-Blobok, a Azure Files-vagy az Azure-lemezek használatának eldöntése

Microsoft Azure az Azure Storage számos funkcióját biztosítja az adatok felhőben való tárolásához és eléréséhez. Ez a cikk a Azure Files, a blobokat és a lemezeket ismerteti, és úgy lett kialakítva, hogy segítse a szolgáltatások közötti választást.

## <a name="scenarios"></a>Forgatókönyvek

A következő táblázat összehasonlítja a fájlokat, a blobokat és a lemezeket, és az egyes alkalmazásokhoz megfelelő példákat jelenít meg.

| Funkció | Leírás | A következő esetekben használja |
|--------------|-------------|-------------|
| **Az Azure Files** | Egy SMB-felületet, egy ügyféloldali kódtárat és egy [Rest-felületet](/rest/api/storageservices/file-service-rest-api) biztosít, amely lehetővé teszi a hozzáférést bárhonnan a tárolt fájlokhoz. | Egy olyan alkalmazást szeretne átemelni és áttérni a felhőbe, amely már a natív fájlrendszer API-kat használja az Azure-ban futó más alkalmazások közötti adatmegosztáshoz.<br/><br/>Olyan fejlesztési és hibakeresési eszközöket szeretne tárolni, amelyeknek számos virtuális gépről kell elérniük. |
| **Azure Blobs** | Az ügyféloldali kódtárakat és egy [Rest-felületet](/rest/api/storageservices/blob-service-rest-api) biztosít, amely lehetővé teszi a strukturálatlan adatok tárolását és elérését a blokkos Blobok nagy méretekben.<br/><br/>A [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) is támogatja a vállalati Big Data elemzési megoldásokhoz. | Azt szeretné, hogy az alkalmazás támogassa a folyamatos átvitelt és a véletlenszerű hozzáférési forgatókönyveket.<br/><br/>Bárhonnan elérhetővé szeretné tenni az alkalmazásadatok elérését.<br/><br/>Az Azure-ban szeretne létrehozni egy vállalati adattavat, és big data elemzést hajt végre. |
| **Azure-lemezek** | Az ügyféloldali kódtárakat és egy [Rest-felületet](/rest/api/compute/manageddisks/disks/disks-rest-api) biztosít, amely lehetővé teszi az adatok tartós tárolását és elérését egy csatlakoztatott virtuális merevlemezről. | A natív fájlrendszer API-kat használó alkalmazásokat szeretné felemelni és átírni az állandó lemezekre történő adatolvasásra és az adatírásra.<br/><br/>Olyan adatok tárolására van szükség, amelyek nem szükségesek azon a virtuális gépen kívülről való hozzáféréshez, amelyhez a lemez csatlakoztatva van. |


## <a name="next-steps"></a>További lépések

Az adattárolásra és-hozzáférésre vonatkozó döntések meghozatalakor figyelembe kell vennie az érintett költségeket is. További információ: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).
  
Egyes SMB-funkciók nem alkalmazhatók a felhőre. További információ: [Az Azure file Service által nem támogatott szolgáltatások](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Az Azure-Blobokkal kapcsolatos további információkért tekintse meg a cikk, [Mi az Azure Blob Storage?](../blobs/storage-blobs-overview.md)című cikket.

További információ a Disk Storageről: [Bevezetés a Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)szolgáltatásba.

További információ a Azure Filesről: a [Azure Files üzembe helyezésének megtervezése](../files/storage-files-planning.md)című cikk.