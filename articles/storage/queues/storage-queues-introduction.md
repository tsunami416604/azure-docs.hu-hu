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
ms.openlocfilehash: 86bbff167a2653fd8d89b566b551c4c53dd3614e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124568"
---
# <a name="what-are-azure-queues"></a>Mik azok az Azure Queues?

Az Azure Queue Storage nagy mennyiségű üzenet tárolására szolgál. A HTTP-vagy HTTPS-t használó hitelesített hívásokon keresztül érheti el az üzeneteket a világ bármely pontján. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra.

## <a name="queue-service-concepts"></a>Queue szolgáltatás fogalmak

A Queue szolgáltatás az alábbi összetevőkből áll:

![Üzenetsor-fogalmak](./media/storage-queues-introduction/queue1.png)

* **URL-formátum:** A várólisták a következő URL-formátummal érhetők el:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Storage-fiók:** Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

* **Várólista** A várólista üzenetek készletét tartalmazza. A várólista nevének csak kisbetűsnek kell lennie. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).

* **Üzenetet** Legfeljebb 64 KB méretű, bármilyen formátumú üzenet. Az 2017-07-29-es verzió előtt az engedélyezett maximális élettartam hét nap. A 2017-07-29-es vagy újabb verzió esetén a maximális élettartam lehet bármilyen pozitív szám, vagy-1, amely azt jelzi, hogy az üzenet nem jár le. Ha a paraméter nincs megadva, az alapértelmezett élettartam hét nap.

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [A Queues használatának első lépései a .NET használatával](storage-dotnet-how-to-use-queues.md)
