---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170884"
---
## <a name="test"></a>A függvény tesztelése az Azure-ban

A cURL használatával tesztelheti az üzembe helyezett függvényt. Az előző lépésben átmásolt URL-cím (például a Function Key) használatával fűzze hozzá a lekérdezési `&name=<yourname>` karakterláncot az URL-címhez.

![a cURL használatával hívja meg a függvényt az Azure-ban.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

A vágólapra másolt URL-címet, beleértve a funkcióbillentyű, a webböngészője címét is beillesztheti. A kérés végrehajtása előtt fűzze hozzá `&name=<yourname>` a lekérdezési karakterláncot az URL-címhez.

![A függvény meghívásához egy webböngészőt használjon.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
