---
title: "Az Azure Container Instances-régiók és -erőforrások rendelkezésre állása"
description: "Felderítheti, hogy mely Azure-régiók támogatják a tárolópéldányok üzembe helyezését, valamint ezen példányok processzor- és memóriakorlátait."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Az Azure Container Instances régióinak rendelkezésre állása

Az előzetes verzióban az Azure Container Instances a következő régiókban érhető el a megadott processzor- és memóriakorlátokkal.

| Hely | Operációs rendszer | CPU | Memória (GB) |
| -------- | -- | :---: | :-----------: |
| Nyugat-Európa, USA nyugati régiója, USA keleti régiója | Linux | 4 | 14 |
| Nyugat-Európa, USA nyugati régiója, USA keleti régiója | Windows | 4 | 14 |

## <a name="resource-availability"></a>Erőforrás rendelkezésre állása

Az ezen erőforráskorlátokon belül létrehozott tárolópéldányok az üzembe helyezés régiójában állnak rendelkezésre. Amikor egy régió nagy terhelés alatt áll, hibát észlelhet a példányok üzembe helyezésekor.

Az ilyen üzembe helyezési hibák csillapítása érdekében próbálja meg alacsonyabb processzor- és memóriabeállításokkal üzembe helyezni a példányokat, vagy próbálja meg később az üzembe helyezést.

## <a name="next-steps"></a>További lépések

A tárolópéldány üzembe helyezésének hibaelhárításáról további információt [az Azure Container Instances üzembe helyezési problémáinak elhárítását](container-instances-troubleshooting.md) ismertető cikkben talál.