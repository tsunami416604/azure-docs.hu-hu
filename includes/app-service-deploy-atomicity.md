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
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75945139"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Mi történik az alkalmazásommal a telepítés során?

A hivatalosan támogatott telepítési módszerek mindegyike `/home/site/wwwroot` módosítja az alkalmazás mappájában lévő fájlokat. Ezeket a fájlokat az alkalmazás futtatásához használjuk. Ezért a központi telepítés zárolt fájlok miatt sikertelen lehet. Az alkalmazás is viselkedhet kiszámíthatatlanul a központi telepítés során, mert nem minden fájl frissítése egy időben. Ez nem kívánatos egy ügyfél felé néző alkalmazás esetében. Néhány féleképpen lehet elkerülni ezeket a problémákat:

- [Futtassa az alkalmazást közvetlenül a ZIP-csomagból](../articles/app-service/deploy-run-package.md) kicsomagolás nélkül.
- Állítsa le az alkalmazást, vagy engedélyezze az alkalmazás offline üzemmódját a telepítés során. További információ: [A zárolt fájlok kezelése a telepítés során.](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)
- Üzembe helyezés egy [átmeneti tárolóhelyre,](../articles/app-service/deploy-staging-slots.md) ahol [az automatikus csere](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) engedélyezve van. 
