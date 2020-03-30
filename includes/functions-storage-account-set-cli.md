---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78262371"
---
### <a name="set-the-storage-account-connection"></a>A Tárfiók-kapcsolat beállítása

Nyissa meg a local.settings.json `AzureWebJobsStorage`fájlt, és másolja a , amely a Storage-fiók kapcsolati karakterlánca. Állítsa `AZURE_STORAGE_CONNECTION_STRING` be a környezeti változót a kapcsolati karakterláncra ezzel a Bash paranccsal:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

Ha beállítja a `AZURE_STORAGE_CONNECTION_STRING` kapcsolati karakterláncot a környezeti változóban, a storage-fiókhoz anélkül férhet hozzá, hogy minden alkalommal hitelesítést kellene biztosítania.