---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78262371"
---
### <a name="set-the-storage-account-connection"></a>A Storage-fiók kapcsolatainak beállítása

Nyissa meg a local. Settings. JSON fájlt, és `AzureWebJobsStorage`másolja ki a értékét, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Állítsa a `AZURE_STORAGE_CONNECTION_STRING` környezeti változót a kapcsolódási karakterláncra a bash parancs használatával:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Ha a kapcsolati karakterláncot a `AZURE_STORAGE_CONNECTION_STRING` környezeti változóban állítja be, akkor anélkül férhet hozzá a Storage-fiókhoz, hogy minden alkalommal hitelesítést kellene biztosítania.