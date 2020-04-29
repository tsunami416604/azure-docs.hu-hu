---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 036e99a193d539bdcb204b5b062de261c30dffc5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66244669"
---
Ez a szakasz az Azure Storage szolgáltatásra vonatkozó korlátozásokat, valamint a Azure Files, az Azure Block blobok és az Azure Page Blobok szükséges elnevezési konvencióit írja le a Data Box szolgáltatásnak megfelelően. Alaposan tekintse át a tárolási korlátokat, és kövesse az összes javaslatot.

Az Azure Storage szolgáltatás korlátaival és a megosztások, tárolók és fájlok elnevezésére vonatkozó ajánlott eljárásokkal kapcsolatos legfrissebb információkért keresse fel a következőt:

- [Tárolók elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blobok és lapok blob-konvencióinak letiltása](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak olyan fájlok vagy könyvtárak, amelyek túllépik az Azure Storage szolgáltatás korlátozásait, vagy nem felelnek meg a Azure Files/blob elnevezési konvencióknak, akkor ezeket a fájlokat vagy címtárakat a rendszer nem tölti be az Azure Storage-ba a Data Box szolgáltatás segítségével.
