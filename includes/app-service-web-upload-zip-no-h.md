---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710016"
---
Az a [az Azure portal](https://portal.azure.com), kattintson a **erőforráscsoportok** > **cloud-shell-storage -\<your_region >**  >   **\<tárfiók_neve >**.

![A Cloud Shell-storage-fiók található](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

Az a **áttekintése** a tárfiókot, válassza az oldal **fájlok**.

Válassza ki az automatikusan létrehozott fájlmegosztást, majd **feltöltése**. A fájlmegosztás csatlakoztatva van, a Cloud shellben `clouddrive`.

![Keresse meg a Feltöltés gombra](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Kattintson a file selector, és válassza ki a ZIP-fájlt, majd **feltöltése**. 

A Cloud shellben használja `ls` , ellenőrizze, hogy a feltöltött ZIP-fájlt az alapértelmezett `clouddrive` megosztani.

```azurecli-interactive
ls clouddrive
```
