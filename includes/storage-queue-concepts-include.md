---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159780"
---
## <a name="what-is-queue-storage"></a>Mi a Queue Storage?
Az Azure Queue Storage szolgáltatás üzenetek nagy számban történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolattal, hitelesített hívásokon keresztül a világon bárhonnan elérhetők. Egyetlen üzenetsor akár 64 KB méretű is lehet, és a tárfiók maximális kapacitásán belül több millió üzenetet tartalmazhat.

A Queue Storage gyakori használati módjai:

* Hátralékos munkák létrehozása aszinkron feldolgozáshoz
* Üzenetek átadása egy Azure webes szerepkörről egy Azure feldolgozói szerepkörnek

## <a name="queue-service-concepts"></a>A Queue szolgáltatás alapfogalmai
A Queue szolgáltatás az alábbi összetevőkből áll:

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **URL-formátum:** Üzenetsorok a következő URL-formátummal:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Tárfiók:** Minden, az Azure Storage-hozzáférés tárfiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../articles/storage/common/storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).
* **Várólista:** Egy üzenetsor egy üzenetkészletet tartalmazza. Az összes üzenetnek üzenetsorban kell lennie. Vegye figyelembe, hogy az üzenetsor neve csak kisbetűket tartalmazhat. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).
* **üzenet:** Egy üzenet, tetszőleges méretű legfeljebb 64 KB-os. Egy üzenet legfeljebb 7 napig maradhat egy üzenetsorban.

