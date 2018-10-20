---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c98a2146a019817152be9fae76638dbaa4d9de3d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49458857"
---
| **Erőforrás** | **Alapértelmezett korlát** | **Felső korlát** |
| --- | --- | --- |
| Az előfizető számára régiónként rendelkezésre álló Batch-fiókok | 1 - 3 |50 |
| Batch-fiókonkénti dedikált magok | 10 - 100 | N/A<sup>1</sup> |
| Alacsony prioritású magok száma Batch-fiókonként | 10 - 100 | N/A<sup>2</sup> |
| Aktív feladatok és feladatütemezések<sup>3</sup> Batch-fiókonként | 100 – 300 | 1000<sup>4</sup> |
| Készletek száma Batch-fiókonként | 20 - 100 | 500-as<sup>4</sup> |

> [!NOTE]
> Alapértelmezett korlátai szerint hozhat létre Batch-fiók előfizetés típusától függően eltérőek lehetnek. Batch-fiókok a Batch szolgáltatás módban látható magkvóták vonatkoznak. [A kvóták megtekintése a Batch-fiókban](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> dedikált magok Batch-fiókonkénti száma növelhető, de a maximális száma nincs megadva. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

<sup>2</sup> alacsony prioritású magok Batch-fiókonkénti száma növelhető, de a maximális száma nincs megadva. Lépjen kapcsolatba a növelési lehetőségek tekintetében az Azure ügyfélszolgálatától.

<sup>3</sup> befejezett feladatok és feladatütemezések nincsenek korlátozva.

<sup>4</sup> Azure forduljon az ügyfélszolgálathoz, ha meghaladja ezt a korlátot növelni szeretné.
