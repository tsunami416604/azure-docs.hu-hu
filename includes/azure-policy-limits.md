---
title: fájl belefoglalása
description: fájl belefoglalása
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/25/2020
ms.author: dacoulte
ms.openlocfilehash: 19b43c857058b3af50db3bc1b42ec1ae558ee884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272649"
---
Az Azure-szabályzat minden objektumtípusához maximális szám van. A _Hatókör_ bejegyzés az előfizetést vagy a [felügyeleti csoportot](../articles/governance/management-groups/overview.md) jelöli.

| Ahol | Mi | Maximális darabszám |
|---|---|---|
| Hatókör | Szabályzatdefiníciók | 500 |
| Hatókör | Kezdeményezési definíciók | 100 |
| Bérlő | Kezdeményezési definíciók | 1,000 |
| Hatókör | Szakpolitikai vagy kezdeményezési feladatok | 100 |
| Szabályzatdefiníció | Paraméterek | 20 |
| Kezdeményezési definíció | Házirendek | 100 |
| Kezdeményezési definíció | Paraméterek | 100 |
| Szakpolitikai vagy kezdeményezési feladatok | Kizárások (notScope) | 400 |
| Házirendszabály | Beágyazott feltételes feltételek | 512 |
| Szervizelési feladat | Források | 1000 |
