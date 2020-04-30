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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81737569"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Maximális korlát** |
| --- | --- | --- |
| Azure Batch fiók/régió/előfizetés | 1-3 |50 |
| Dedikált magok száma batch-fiókkal | 90-900 | Kapcsolatfelvétel a támogatási szolgáltatással |
| Alacsony prioritású magok száma batch-fiókban | 10-100 | Kapcsolatfelvétel a támogatási szolgáltatással |
| **[Aktív](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** feladatok és feladatok ütemezett száma batch-fiókban (a**befejezett** feladatok nem rendelkeznek korláttal) | 100-300 | 1 000<sup>1</sup> |
| Készletek száma Batch-fiókonként | 20-100 | 500<sup>1</sup> |

<sup>1</sup> A korlátot meghaladó növekedés igényléséhez forduljon az Azure ügyfélszolgálatához.

> [!NOTE]
> Az alapértelmezett korlátok a Batch-fiók létrehozásához használt előfizetés típusától függően változnak. A kimutatott magok kvótái a Batch szolgáltatás üzemmódjában található batch-fiókokra vonatkoznak. [Megtekintheti a Batch-fiókban lévő kvótákat](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> A globális egészségügyi világjárvány során a kapacitás jobb kezelése érdekében az egyes régiókban és bizonyos előfizetésekben lévő új batch-fiókokra vonatkozó alapértelmezett alapkvóták a fenti értékektől, bizonyos esetekben pedig nulla maggal csökkentek. Új batch-fiók létrehozásakor [ellenőrizze az alapvető kvótát](../articles/batch/batch-quota-limit.md#view-batch-quotas) , és szükség esetén adja meg az [alapvető kvóta növelését](../articles/batch/batch-quota-limit.md#increase-a-quota). 
