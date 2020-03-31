---
title: Az Azure Blobok, az Azure Files vagy az Azure Disks használatának eldöntése
description: Ismerje meg az adatok azure-beli tárolásának és elérésének különböző módjait, amelyek segítségével eldöntheti, hogy melyik technológiát használja.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671047"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Az Azure Blobok, az Azure Files vagy az Azure Disks használatának eldöntése

A Microsoft Azure számos szolgáltatást kínál az Azure Storage-ban az adatok felhőben való tárolásához és eléréséhez. Ez a cikk az Azure-fájlokat, a Blobokat és a Lemezeket ismerteti, és célja, hogy segítsen választani ezek közül a funkciók közül.

## <a name="scenarios"></a>Forgatókönyvek

Az alábbi táblázat összehasonlítja a fájlokat, a blobokat és a lemezeket, és bemutatja az egyes eseteknek megfelelő példaforgatókönyveket.

| Szolgáltatás | Leírás | A következő esetekben használja |
|--------------|-------------|-------------|
| **Azure Files** | SMB-összeköttetést, ügyféltárakat és [REST-felületet](/rest/api/storageservices/file-service-rest-api) biztosít, amely bárhonnan hozzáférést biztosít a tárolt fájlokhoz. | Azt szeretné, hogy "lift and shift" egy alkalmazást a felhőbe, amely már használja a natív fájlrendszer API-k at adatok megosztására közte és más azure-ban futó alkalmazások között.<br/><br/>Olyan fejlesztői és hibakeresési eszközöket szeretne tárolni, amelyeket számos virtuális gépről kell elérni. |
| **Azure Blobs** | Ügyfélkódtárakat és [REST-felületet](/rest/api/storageservices/blob-service-rest-api) biztosít, amely lehetővé teszi a strukturálatlan adatok tömeges tárolását és elérését blokkblobokban.<br/><br/>Támogatja az [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) vállalati big data-elemzési megoldásokat is. | Azt szeretné, hogy az alkalmazás támogatja a streamelési és véletlenszerű hozzáférésű forgatókönyvek.<br/><br/>Bárhonnan hozzá szeretne férni az alkalmazásadatokhoz.<br/><br/>Nagyvállalati adattót szeretne építeni az Azure-on, és big data-elemzéseket szeretne végezni. |
| **Azure Disks** | Ügyféltárakat és [REST-felületet](/rest/api/compute/manageddisks/disks/disks-rest-api) biztosít, amely lehetővé teszi az adatok állandó tárolását és elérését egy csatlakoztatott virtuális merevlemezről. | Fel szeretné emelni és át helyezni azokat az alkalmazásokat, amelyek natív fájlrendszer API-kat használnak az adatok állandó lemezekre történő olvasásához és írásához.<br/><br/>Olyan adatokat szeretne tárolni, amelyekhez nem szükséges hozzáférni azon a virtuális gépen kívülről, amelyhez a lemez csatlakozik. |


## <a name="next-steps"></a>További lépések

Amikor az adatok tárolásával és elérésével kapcsolatos döntéseket hoz, figyelembe kell vennie a felmerülő költségeket is. További információ: [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).
  
Egyes SMB-funkciók nem alkalmazhatók a felhőre. További információ: [Az Azure File szolgáltatás által nem támogatott szolgáltatások.](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)
 
Az Azure Blobokról további információt a Mi az Azure Blob storage című cikkünkben [talál.](../blobs/storage-blobs-overview.md)

A Lemeztárolásról további információt a Felügyelt lemezek – bevezetés című témakörben [talál.](../../virtual-machines/windows/managed-disks-overview.md)

Az Azure Files szolgáltatásról további információt az [Azure-fájlok telepítésének megtervezése](../files/storage-files-planning.md)című cikkünkben talál.