---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179575"
---
Ez a szakasz ismerteti az Azure Storage-szolgáltatás korlátait, valamint az Azure Files, az Azure-blokkblobok és az Azure-lapblobok szükséges elnevezési konvencióit, a Data Box Gateway/Data Box Edge szolgáltatásra vonatkozóan. Tekintse át figyelmesen a tárolási korlátokat, és kövesse az összes ajánlást.

Az Azure storage-szolgáltatás korlátaival és a megosztások, tárolók és fájlok elnevezésével kapcsolatos gyakorlati tanácsokról a következő információkat talál:

- [Tárolók elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blobok és lapblobok blokkolása konvenciók](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak olyan fájlok vagy könyvtárak, amelyek meghaladják az Azure Storage szolgáltatás korlátait, vagy nem felelnek meg az Azure Files/Blob elnevezési konvencióknak, majd ezek a fájlok vagy könyvtárak nem kerülnek be az Azure Storage-ba a Data Box Gateway/Data Box Edge szolgáltatáson keresztül.