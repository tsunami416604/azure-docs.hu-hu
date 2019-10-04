---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203124"
---
## <a name="test"></a>A függvény ellenőrzése az Azure-ban

A cURL használatával ellenőrizze az üzembe helyezett függvényt. Az előző lépésben átmásolt URL-cím (például a Function Key) használatával fűzze hozzá a lekérdezési `&name=<yourname>` karakterláncot az URL-címhez.

![a cURL használatával hívja meg a függvényt az Azure-ban.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

A vágólapra másolt URL-címet, beleértve a funkcióbillentyű is, a webböngésző címsorába is beillesztheti. A kérés végrehajtása előtt fűzze hozzá `&name=<yourname>` a lekérdezési karakterláncot az URL-címhez.

![A függvény meghívásához egy webböngészőt használjon.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
