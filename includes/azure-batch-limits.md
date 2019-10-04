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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080904"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Azure Batch fiók/régió/előfizetés | 1-3 |50 |
| Dedikált magok száma batch-fiókkal | 90-900 | Forduljon a támogatási szolgálathoz. |
| Alacsony prioritású magok száma batch-fiókban | 10-100 | Forduljon a támogatási szolgálathoz. |
| **[Aktív](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** feladatok és feladatok ütemezett száma batch-fiókban (a**befejezett** feladatok nem rendelkeznek korláttal) | 100-300 | 1 000<sup>1</sup> |
| Készletek Batch-fiókonként | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Az alapértelmezett korlátok a Batch-fiók létrehozásához használt előfizetés típusától függően változnak. A kimutatott magok kvótái a Batch szolgáltatás üzemmódjában található batch-fiókokra vonatkoznak. [Megtekintheti a Batch-fiókban lévő kvótákat](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> A korlátot meghaladó növekedés igényléséhez forduljon az Azure ügyfélszolgálatához.
