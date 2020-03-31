---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080904"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Maximális határérték** |
| --- | --- | --- |
| Azure Batch-fiókok régiónként és előfizetésenként | 1-3 |50 |
| Dedikált magok Batch-fiókonként | 90-900 | Kapcsolatfelvétel a támogatási szolgáltatással |
| Alacsony prioritású magok kötegelt fiókonként | 10-100 | Kapcsolatfelvétel a támogatási szolgáltatással |
| **[Aktív](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** feladatok és feladatütemezések kötegelt fiókonként **(a befejezett** feladatoknak nincs korlátozva) | 100-300 | 1000<sup>1</sup> |
| Készletek száma Batch-fiókonként | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Az alapértelmezett korlátok a Batch-fiók létrehozásához használt előfizetés típusától függően változnak. A megjelenített magok a Batch-fiókok batch szolgáltatás módban. [Tekintse meg a kvótákat a Batch-fiókban.](../articles/batch/batch-quota-limit.md#view-batch-quotas)

<sup>1 1</sup> Ha ennél a korláton túl szeretne növekedést kérni, forduljon az Azure-támogatáshoz.
