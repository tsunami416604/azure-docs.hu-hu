---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 9ffb02fce41e8805dfccf1dfd6e982cf107039ec
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127548"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az Azure Batch-fiókok régiónként és előfizetésenként | 1-3 |50 |
| Batch-fiókonkénti dedikált magok | 10-100 | N/A<sup>1</sup> |
| Alacsony prioritású magok száma Batch-fiókonként | 10-100 | N/A<sup>2</sup> |
| Aktív feladatok és feladatütemezések<sup>3</sup> Batch-fiókonként | 100-300 | 1,000<sup>4</sup> |
| Készletek Batch-fiókonként | 20-100 | 500<sup>4</sup> |

> [!NOTE]
> Alapértelmezett korlátai szerint hozhat létre Batch-fiók előfizetés típusától függően eltérőek lehetnek. Batch-fiókok a Batch szolgáltatás módban látható magkvóták vonatkoznak. [A kvóták megtekintése a Batch-fiókban](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup>dedikált magok Batch-fiókonkénti száma növelhető, de a maximális száma nincs megadva. A növelési lehetőségek tekintetében, forduljon az Azure ügyfélszolgálatához.

<sup>2</sup>alacsony prioritású magok Batch-fiókonkénti száma növelhető, de a maximális száma nincs megadva. A növelési lehetőségek tekintetében, forduljon az Azure ügyfélszolgálatához.

<sup>3</sup>befejezett feladatok és feladatütemezések nincsenek korlátozva.

<sup>4</sup>meghaladja ezt a korlátot növelni, forduljon az Azure ügyfélszolgálatához.
