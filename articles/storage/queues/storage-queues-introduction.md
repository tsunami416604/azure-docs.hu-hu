---
title: Bevezetés az Azure Queues használatába – Azure Storage
description: Az Azure Queues bemutatása
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 4a2bea77578282d68d86bc1a8cea765aa2cbd555
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80060852"
---
# <a name="what-are-azure-queues"></a>Mik az Azure-üzenetsorok?

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. A HTTP-vagy HTTPS-t használó hitelesített hívásokon keresztül érheti el az üzeneteket a világ bármely pontján. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra.

## <a name="queue-service-concepts"></a>Queue szolgáltatás fogalmak

A Queue szolgáltatás az alábbi összetevőkből áll:

![A Storage-fiók, a várólisták és az üzenetek közötti kapcsolatot bemutató ábra](./media/storage-queues-introduction/queue1.png)

* **URL-formátum:** Az üzenetsorok a következő URL-formátummal érhetők el:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    Az ábra egyik üzenetsora a következő URL-címmel érhető el:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Storage-fiók:** Az Azure Storage-hoz való összes hozzáférés egy Storage-fiókon keresztül történik. A Storage-fiók kapacitásával kapcsolatos további információkért lásd [a szabványos Storage-fiókok skálázhatósági és teljesítménybeli céljait](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)ismertető témakört.

* **Üzenetsor:** Az üzenetsorok üzenetek készleteit tartalmazzák. A **várólista nevének csak kisbetűsnek kell** lennie. Az üzenetsorok elnevezésével kapcsolatos információkat lásd: [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Üzenetsorok és metaadatok elnevezése).

* **Üzenet:** Egy legfeljebb 64 KB méretű, tetszőleges méretű üzenet. Az 2017-07-29-es verzió előtt az engedélyezett maximális élettartam hét nap. A 2017-07-29-es vagy újabb verzió esetén a maximális élettartam lehet bármilyen pozitív szám, vagy-1, amely azt jelzi, hogy az üzenet nem jár le. Ha a paraméter nincs megadva, az alapértelmezett élettartam hét nap.

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [A Queues használatának első lépései a .NET használatával](storage-dotnet-how-to-use-queues.md)
