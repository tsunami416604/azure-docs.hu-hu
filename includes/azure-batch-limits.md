---
title: fájlbefoglalás
description: fájlbefoglalás
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 8d23da1c3a45ed12193dfd4c1fc2ede453fc6ac7
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323882"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Maximális korlát** |
| --- | --- | --- |
| Azure Batch fiók/régió/előfizetés | 1-3 |50 |
| Dedikált magok száma batch-fiókkal | 90-900 | Kapcsolatfelvétel a támogatási szolgáltatással |
| Alacsony prioritású magok száma batch-fiókban | 10-100 | Kapcsolatfelvétel a támogatási szolgáltatással |
| **[Aktív](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** feladatok és feladatok ütemezett száma batch-fiókban (a**befejezett** feladatok nem rendelkeznek korláttal) | 100-300 | 1 000<sup>1</sup> |
| Készletek száma Batch-fiókonként | 20-100 | 500<sup>1</sup> |

<sup>1</sup> A korlátot meghaladó növekedés igényléséhez forduljon az Azure ügyfélszolgálatához.

> [!IMPORTANT]
> Módosítjuk a dedikált kvóta kérését és kezelését.  A teljes dedikált vCPU a jelenleg érvényesített érték, de hamarosan dedikált kvótát fogunk kikényszeríteni a virtuálisgép-sorozatok esetében. Az alacsony prioritású kvóta a teljes korlát alapján továbbra is érvényben marad; a VM-sorozat nem kényszeríti ki.

> [!NOTE]
> Az alapértelmezett korlátok a Batch-fiók létrehozásához használt előfizetés típusától függően változnak. A kimutatott magok kvótái a Batch szolgáltatás üzemmódjában található batch-fiókokra vonatkoznak. [Megtekintheti a Batch-fiókban lévő kvótákat](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> A globális egészségügyi világjárvány során a kapacitás jobb kezelése érdekében az egyes régiókban és bizonyos előfizetésekben lévő új batch-fiókokra vonatkozó alapértelmezett alapkvóták a fenti értékektől, bizonyos esetekben pedig nulla maggal csökkentek. Új batch-fiók létrehozásakor [ellenőrizze az alapvető kvótát](../articles/batch/batch-quota-limit.md#view-batch-quotas) , és szükség esetén adja meg az [alapvető kvóta növelését](../articles/batch/batch-quota-limit.md#increase-a-quota). 
