---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179768"
---
Az alábbi eljárással megtekintheti és frissítheti a függvényalkalmazás által jelenleg használt futásidejű verziót.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a függvényalkalmazást.

1. A **Konfigurált szolgáltatások csoportban**válassza **a Függvényalkalmazás beállításai lehetőséget.**

    ![A függvényalkalmazás beállításainak kiválasztása](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. A **Függvényalkalmazás beállításai** lapon keresse meg a **Futásidejű verziót.** Vegye figyelembe az adott futásidejű verziót és a kért főverziót. Az alábbi példában a verzió `~2`beállítása .

   ![A függvényalkalmazás beállításainak kiválasztása](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Ha a függvényalkalmazást az 1.x-es verzióra szeretné rögzíteni, válassza a **~1** lehetőséget a **Futásidejű verzió**csoportban. Ez a kapcsoló le van tiltva, ha funkciók vannak az alkalmazásban.

1. Ha módosítja a futásidejű verziót, lépjen vissza az **Áttekintés** lapra, és az **Újraindítás gombra** kerülve indítsa újra az alkalmazást.  A függvényalkalmazás újraindul az 1.x-es verziójú futtatón, és a függvények létrehozásakor az 1.x-es verziójú sablonokat használja a program.