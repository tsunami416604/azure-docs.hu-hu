---
title: Bevezetés az Azure Queues használatába – Azure Storage
description: Az Azure Queues bemutatása
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/07/2019
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 67e4874fcca93633140b7630ceadd273d1646f86
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721173"
---
# <a name="what-are-azure-queues"></a>Mi az az Azure Queues?

Az Azure üzenetsor-tároló szolgáltatás nagy számú üzenet tárolására szolgál. A HTTP-vagy HTTPS-t használó hitelesített hívásokon keresztül érheti el az üzeneteket a világ bármely pontján. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját.

## <a name="common-uses"></a>Gyakori felhasználások

A Queue Storage gyakori használati módjai:

* Hátralékos munkák létrehozása aszinkron feldolgozáshoz
* Üzenetek átadása egy Azure webes szerepkörről egy Azure feldolgozói szerepkörnek

## <a name="queue-service-concepts"></a>Queue szolgáltatás fogalmak

A Queue szolgáltatás az alábbi összetevőkből áll:

![Üzenetsor-fogalmak](./media/storage-queues-introduction/queue1.png)

* **URL-formátum:** A várólisták a következő URL-formátummal érhetők el:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Storage-fiók:** Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

* **Várólista** A várólista üzenetek készletét tartalmazza. A **várólista nevének csak** kisbetűsnek kell lennie. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).

* **Üzenetet** Legfeljebb 64 KB méretű, bármilyen formátumú üzenet. Az 2017-07-29-es verzió előtt az engedélyezett maximális élettartam hét nap. A 2017-07-29-es vagy újabb verzió esetén a maximális élettartam lehet bármilyen pozitív szám, vagy-1, amely azt jelzi, hogy az üzenet nem jár le. Ha a paraméter nincs megadva, az alapértelmezett élettartam hét nap.

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [A Queues használatának első lépései a .NET használatával](storage-dotnet-how-to-use-queues.md)
