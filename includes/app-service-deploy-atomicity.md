---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d9b0a7f08efcf9ea5892467b638e95d0404f35f
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752044"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazással az üzembe helyezés során?

Az összes hivatalosan támogatott üzembe helyezési módszer módosítja az alkalmazás `/home/site/wwwroot` mappájában található fájlokat. Ezek a fájlok az alkalmazás futtatására szolgálnak. Ezért a központi telepítés zárolt fájlok miatt sikertelen lehet. Az alkalmazás az üzembe helyezés során kiszámíthatatlanul is működhet, mert nem minden fájl frissült egyszerre. Ez nem kívánatos az ügyfél által használt alkalmazásokhoz. Ezeket a problémákat többféleképpen is elkerülheti:

- Állítsa le az alkalmazást, vagy engedélyezze az alkalmazás offline üzemmódját az üzembe helyezés során. További információ: [az üzembe helyezés során zárolt fájlok kezelése](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Üzembe helyezés [átmeneti tárolóhelyre](../articles/app-service/deploy-staging-slots.md) , amelyen engedélyezve van az [automatikus felcserélés](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) . 
- Folyamatos üzembe helyezés helyett használja [a Futtatás a csomagból](https://github.com/Azure/app-service-announcements/issues/84) lehetőséget.
