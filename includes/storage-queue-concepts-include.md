---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 935a6201e24e97deaa0a1a65e499310f74a49e2d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059034"
---
## <a name="what-is-queue-storage"></a>Mi a várólista-tároló?

Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat. A várólista-tárolót gyakran használják várakozó feladatok aszinkron feldolgozására.

## <a name="queue-service-concepts"></a>Queue szolgáltatás fogalmak

Az Azure Queue szolgáltatás a következő összetevőket tartalmazza:

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **URL-formátum:** A várólisták a következő URL-formátummal érhetők el:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Storage-fiók:** Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../articles/storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).
* **Várólista** A várólista üzenetek készletét tartalmazza. Az összes üzenetnek üzenetsorban kell lennie. Vegye figyelembe, hogy az üzenetsor neve csak kisbetűket tartalmazhat. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).
* **Üzenetet** Legfeljebb 64 KB méretű, bármilyen formátumú üzenet. Egy üzenet legfeljebb 7 napig maradhat egy üzenetsorban.

