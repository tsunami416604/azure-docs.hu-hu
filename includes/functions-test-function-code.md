---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279313"
---
## <a name="test"></a>A függvény ellenőrzése az Azure-ban

A központilag telepített függvények ellenőrzéséhez használhat webböngészőt is.  Másolja az URL-címet, beleértve a funkcióbillentyű, a böngésző címsorába. A kérés végrehajtása előtt fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez.

![A függvény meghívásához egy webböngészőt használjon.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

Azt is megteheti, hogy a cURL használatával ellenőrzi az üzembe helyezett függvényt. Az előző lépésben átmásolt URL-cím (például a Function Key) használatával fűzze hozzá a lekérdezési karakterláncot `&name=<yourname>` az URL-címhez.

![a cURL használatával hívja meg a függvényt az Azure-ban.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

