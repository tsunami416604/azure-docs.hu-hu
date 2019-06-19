---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178901"
---
## <a name="test"></a>A függvény tesztelése az Azure-ban

Tesztelje az üzembe helyezett függvényt a cURL használatával. Az előző lépésben másolt URL-cím használatával fűzze hozzá a lekérdezési karakterlánc `&name=<yourname>` az URL-címhez, akkor az alábbi példában látható módon:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![a cURL használatával meghívja a függvényt az Azure-ban.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

A másolt URL-címét a webböngésző is beillesztheti. Újra, fűzze hozzá a lekérdezési karakterlánc `&name=<yourname>` az URL-címet, a kérelem végrehajtása előtt.

![A függvény hívása egy webböngésző használatával.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
