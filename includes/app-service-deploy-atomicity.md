---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75945139"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazással az üzembe helyezés során?

Az összes hivatalosan támogatott üzembe helyezési módszer módosítja az `/home/site/wwwroot` alkalmazás mappájában található fájlokat. Ezek a fájlok az alkalmazás futtatására szolgálnak. Ezért a központi telepítés zárolt fájlok miatt sikertelen lehet. Az alkalmazás az üzembe helyezés során kiszámíthatatlanul is működhet, mert nem minden fájl frissült egyszerre. Ez nem kívánatos az ügyfél által használt alkalmazásokhoz. Ezeket a problémákat többféleképpen is elkerülheti:

- Az [alkalmazást közvetlenül a kicsomagolás nélkül futtathatja a zip-csomagból](../articles/app-service/deploy-run-package.md) .
- Állítsa le az alkalmazást, vagy engedélyezze az alkalmazás offline üzemmódját az üzembe helyezés során. További információ: [az üzembe helyezés során zárolt fájlok kezelése](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Üzembe helyezés [átmeneti tárolóhelyre](../articles/app-service/deploy-staging-slots.md) , amelyen engedélyezve van az [automatikus felcserélés](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) . 
