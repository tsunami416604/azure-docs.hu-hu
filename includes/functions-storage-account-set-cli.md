---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329563"
---
### <a name="set-the-storage-account-connection"></a>A Storage-fiók kapcsolatainak beállítása

Nyissa meg a local. Settings. JSON fájlt, és másolja a `AzureWebJobsStorage` értéket, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Állítsa be a `AZURE_STORAGE_CONNECTION_STRING` környezeti változót a kapcsolódási sztringre a következő bash-parancs használatával:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Ha a kapcsolati karakterláncot a `AZURE_STORAGE_CONNECTION_STRING` környezeti változóban állítja be, akkor anélkül férhet hozzá a Storage-fiókhoz, hogy minden alkalommal hitelesítést kellene biztosítania.