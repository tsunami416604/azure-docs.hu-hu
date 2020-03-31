---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179456"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Helyi fájlok átvitele a Cloud Shellbe
A `clouddrive` címtár szinkronizálja az Azure Portal storage panel. Ezzel a panelből helyi fájlokat vihet át a fájlmegosztásra vagy a fájlmegosztásból. A Cloud Shell-ből származó fájlok frissítése a fájltároló grafikus felhasználói felületén is megjelenik a panel frissítésekor.

### <a name="download-files"></a>Fájlok letöltése

![Helyi fájlok listája](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Az Azure Portalon nyissa meg a csatlakoztatott fájlmegosztást.
2. Jelölje ki a célfájlt.
3. Válassza a **Letöltés** gombot.

### <a name="upload-files"></a>Fájlok feltöltése

![Feltöltendő helyi fájlok](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Nyissa meg a csatlakoztatott fájlmegosztást.
2. Kattintson a **Feltöltés** gombra.
3. Jelölje ki a feltölteni kívánt fájlt vagy fájlokat.
4. Erősítse meg a feltöltést.

Most látnia kell a felhőshellben a `clouddrive` címtárban elérhető fájlokat.