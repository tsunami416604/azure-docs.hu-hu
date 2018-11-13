---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572212"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Helyi fájlok átvitele a Cloud Shellben
A `clouddrive` directory szinkronizál az Azure portal tároló panelen. Használja ezt a panelt, vagy a fájlmegosztásból helyi fájlok átvitelét. A Cloud Shell található fájlokat frissítése tükrözi a file storage grafikus felhasználói felület a panel frissítésekor.

### <a name="download-files"></a>Fájlok letöltése

![Helyi fájlok listája](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Az Azure Portalon nyissa meg a csatlakoztatott fájlmegosztás.
2. Válassza ki a cél-fájlt.
3. Válassza ki a **letöltése** gombra.

### <a name="upload-files"></a>Fájlok feltöltése

![Helyi fájlok tölthető fel.](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Nyissa meg a csatlakoztatott fájlmegosztás.
2. Kattintson a **Feltöltés** gombra.
3. Válassza ki a fájlt vagy a feltölteni kívánt fájlokat.
4. Erősítse meg a feltöltést.

Meg kell jelennie a fájlokat, amelyek érhetők el a `clouddrive` könyvtárat a Cloud Shellben.