---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80151186"
---
## <a name="what-is-queue-storage"></a>Mi a várólista-tároló?

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat. A várólista-tárolót gyakran használják várakozó feladatok aszinkron feldolgozására.

## <a name="queue-service-concepts"></a>Queue szolgáltatás fogalmak

Az Azure Queue szolgáltatás a következő összetevőket tartalmazza:

![Azure Queue szolgáltatás-összetevők](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL-formátum:** A várólisták a következő URL-formátummal érhetők el: http:// `<storage account>` . Queue.Core.Windows.net/`<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Storage-fiók:** Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A Storage-fiókokkal kapcsolatos további információkért lásd: a [Storage-fiók áttekintése](../articles/storage/common/storage-account-overview.md).
* **Üzenetsor:** Az üzenetsorok üzenetek készleteit tartalmazzák. Az összes üzenetnek üzenetsorban kell lennie. Vegye figyelembe, hogy az üzenetsor neve csak kisbetűket tartalmazhat. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).
* **Üzenet:** Egy legfeljebb 64 KB méretű, tetszőleges méretű üzenet. Egy üzenet legfeljebb 7 napig maradhat egy üzenetsorban. A 2017-07-29-es vagy újabb verzió esetén a maximális élettartam lehet bármilyen pozitív szám, vagy-1, amely azt jelzi, hogy az üzenet nem jár le. Ha a paraméter nincs megadva, az alapértelmezett élettartam hét nap.

