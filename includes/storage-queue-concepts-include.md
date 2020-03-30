---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80151186"
---
## <a name="what-is-queue-storage"></a>Mi az a várólista-tároló?

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat. A várólista-tárolást gyakran használják az aszinkron feldolgozáshoz szükséges munka hátralékának létrehozására.

## <a name="queue-service-concepts"></a>Várólista-szolgáltatás fogalmai

Az Azure Queue szolgáltatás a következő összetevőket tartalmazza:

![Az Azure Queue szolgáltatás összetevői](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-formátum:** A várólisták a következő URL-formátumban címezhetők: http://`<storage account>`.queue.core.windows.net/`<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Tárfiók:** Az Azure Storage-hoz való hozzáférés egy tárfiókon keresztül történik. A tárfiókokról a [Tárfiók áttekintéscímű témakörben olvashat bővebben.](../articles/storage/common/storage-account-overview.md)
* **Üzenetsor:** Az üzenetsorok üzenetek készleteit tartalmazzák. Az összes üzenetnek üzenetsorban kell lennie. Vegye figyelembe, hogy az üzenetsor neve csak kisbetűket tartalmazhat. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).
* **Üzenet:** Egy legfeljebb 64 KB méretű, tetszőleges méretű üzenet. Egy üzenet legfeljebb 7 napig maradhat egy üzenetsorban. A 2017-07-29-es vagy újabb verzió esetén a maximális ideig használható lehet bármilyen pozitív szám, vagy -1, amely jelzi, hogy az üzenet nem jár le. Ha ez a paraméter nincs megadva, az alapértelmezett idő-hátra hét nap.

