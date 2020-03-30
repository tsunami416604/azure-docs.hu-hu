---
title: Bevezetés az Azure-várólistákba – Azure Storage
description: Bevezetés az Azure-várólistákba
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80060852"
---
# <a name="what-are-azure-queues"></a>Mik az Azure-üzenetsorok?

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Az üzeneteket a világ bármely pontjáról elérheti http vagy HTTPS protokollon keresztül hitelesített hívásokon keresztül. A várólista-üzenet mérete legfeljebb 64 KB lehet. Egy várólista több millió üzenetet tartalmazhat, a tárfiók teljes kapacitáskorlátjáig. A várólistákat gyakran használják az aszinkron feldolgozáshoz használt munka hátralékának létrehozására.

## <a name="queue-service-concepts"></a>Várólista-szolgáltatás fogalmai

A Queue szolgáltatás az alábbi összetevőkből áll:

![Tárfiók, várólisták és üzenetek közötti kapcsolatot bemutató diagram](./media/storage-queues-introduction/queue1.png)

* **URL-formátum:** Az üzenetsorok a következő URL-formátummal érhetők el:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Tárfiók:** Az Azure Storage-hoz való hozzáférés egy tárfiókon keresztül történik. A tárfiókok kapacitásáról a [méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)című témakörben talál.

* **Üzenetsor:** Az üzenetsorok üzenetek készleteit tartalmazzák. A várólista nevének kisbetűsnek **kell** lennie. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).

* **Üzenet:** Egy legfeljebb 64 KB méretű, tetszőleges méretű üzenet. A 2017-07-29-es verzió előtt a maximálisan engedélyezett időtartam hét nap. A 2017-07-29-es vagy újabb verzió esetén a maximális ideig használható lehet bármilyen pozitív szám, vagy -1, amely jelzi, hogy az üzenet nem jár le. Ha ez a paraméter nincs megadva, az alapértelmezett idő-hátra hét nap.

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [A várólisták használatának első lépései a .NET használatával](storage-dotnet-how-to-use-queues.md)
