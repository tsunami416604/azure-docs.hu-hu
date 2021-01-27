---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: d12a5042d0c2d79989d82e86e7265d030912f5ee
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900973"
---
Ez a szakasz az Azure Storage szolgáltatásra vonatkozó korlátozásokat, valamint a Azure Files, az Azure Block blobok és az Azure Page Blobok szükséges elnevezési konvencióit írja le az Azure Stack Edge szolgáltatásnak megfelelően. Alaposan tekintse át a tárolási korlátokat, és kövesse az összes javaslatot.

Az Azure Storage szolgáltatás korlátaival és a megosztások, tárolók és fájlok elnevezésére vonatkozó ajánlott eljárásokkal kapcsolatos legfrissebb információkért keresse fel a következőt:

- [Tárolók elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blobok és lapok blob-konvencióinak letiltása](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak olyan fájlok vagy címtárak, amelyek túllépik az Azure Storage szolgáltatás korlátozásait, vagy nem felelnek meg a Azure Files/blob elnevezési konvencióknak, akkor ezeket a fájlokat vagy könyvtárakat a Azure Stack Edge szolgáltatáson keresztül nem kell bevenni az Azure Storage-ba.