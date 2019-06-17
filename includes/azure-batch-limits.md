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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080904"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az Azure Batch-fiókok régiónként és előfizetésenként | 1-3 |50 |
| Batch-fiókonkénti dedikált magok | 90-900 | Forduljon a támogatási szolgálathoz. |
| Alacsony prioritású magok száma Batch-fiókonként | 10-100 | Forduljon a támogatási szolgálathoz. |
| **[Aktív](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)**  feladatok és feladatütemezések Batch-fiókonként (**befejeződött** feladatok rendelkezik nincs korlát) | 100-300 | 1,000<sup>1</sup> |
| Készletek száma Batch-fiókonként | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Alapértelmezett korlátai szerint hozhat létre Batch-fiók előfizetés típusától függően eltérőek lehetnek. Batch-fiókok a Batch szolgáltatás módban látható magkvóták vonatkoznak. [A kvóták megtekintése a Batch-fiókban](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup>meghaladja ezt a korlátot növelni, forduljon az Azure ügyfélszolgálatához.
