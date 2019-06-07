---
title: Az Azure-üzenetsorok – Bevezetés az Azure Storage
description: Az Azure-üzenetsorok bemutatása
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 06/07/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: bc3045e3d3b6977555818fcdb3dcaf3246ebd200
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754812"
---
# <a name="what-are-azure-queues"></a>Mi az az Azure Queues?

Az Azure Queue storage szolgáltatás üzenetek nagy számban tárolásához. Üzenetek bárhonnan elérheti HTTP vagy HTTPS PROTOKOLLT használó hitelesített hívásokon keresztül a világon. Egy üzenetsor akár 64 KB méretű lehet. Egy üzenetsor több millió üzenetet a storage-fiók összesített kapacitásán belül is tartalmazhat.

## <a name="common-uses"></a>Gyakori használati mód

A Queue Storage gyakori használati módjai:

* Hátralékos munkák létrehozása aszinkron feldolgozáshoz
* Üzenetek átadása egy Azure webes szerepkörről egy Azure feldolgozói szerepkörnek

## <a name="queue-service-concepts"></a>A Queue szolgáltatás alapfogalmai

A Queue szolgáltatás az alábbi összetevőkből áll:

![Üzenetsor-kapcsolatos fogalmak](./media/storage-queues-introduction/queue1.png)

* **URL-formátum:** Üzenetsorok a következő URL-formátummal:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Tárfiók:** Minden, az Azure Storage-hozzáférés tárfiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

* **Várólista:** Egy üzenetsor egy üzenetkészletet tartalmazza. Az üzenetsor neve **kell** kisbetűkből állhat. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).

* **üzenet:** Egy üzenet, tetszőleges méretű legfeljebb 64 KB-os. A következőnél régebbi verziók 2017-07-29 a megengedett időt a működés közbeni hét nap. 2017-07-29 verzió vagy újabb, illetve a maximális idő – élő bármilyen pozitív szám, vagy lehet-1 értéket, amely azt jelzi, hogy az üzenet nem lejár. Ha ez a paraméter nincs megadva, az alapértelmezett time-to-live hét nap.

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Ismerkedés az Üzenetsorokkal .NET használatával](storage-dotnet-how-to-use-queues.md)
