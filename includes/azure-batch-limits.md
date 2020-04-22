---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737569"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Maximális határérték** |
| --- | --- | --- |
| Azure Batch-fiókok régiónként és előfizetésenként | 1-3 |50 |
| Dedikált magok Batch-fiókonként | 90-900 | Kapcsolatfelvétel a támogatási szolgáltatással |
| Alacsony prioritású magok kötegelt fiókonként | 10-100 | Kapcsolatfelvétel a támogatási szolgáltatással |
| **[Aktív](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** feladatok és feladatütemezések kötegelt fiókonként **(a befejezett** feladatoknak nincs korlátozva) | 100-300 | 1000<sup>1</sup> |
| Készletek száma Batch-fiókonként | 20-100 | 500<sup>1</sup> |

<sup>1 1</sup> Ha ennél a korláton túl szeretne növekedést kérni, forduljon az Azure-támogatáshoz.

> [!NOTE]
> Az alapértelmezett korlátok a Batch-fiók létrehozásához használt előfizetés típusától függően változnak. A megjelenített magok a Batch-fiókok batch szolgáltatás módban. [Tekintse meg a kvótákat a Batch-fiókban.](../articles/batch/batch-quota-limit.md#view-batch-quotas)

> [!IMPORTANT]
> Annak érdekében, hogy a globális egészségügyi világjárvány során jobban kezelhesd a kapacitást, egyes régiókban az új Batch-fiókok alapértelmezett alapkvótái, valamint bizonyos típusú előfizetések esetében a fenti értéktartományról nullára csökkentek. Amikor új Batch-fiókot hoz létre, [ellenőrizze az alapkvótát,](../articles/batch/batch-quota-limit.md#view-batch-quotas) és szükség esetén [kérje az alapkvóta növelését.](../articles/batch/batch-quota-limit.md#increase-a-quota) 
