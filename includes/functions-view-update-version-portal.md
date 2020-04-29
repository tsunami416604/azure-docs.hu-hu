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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179768"
---
A következő eljárással tekintheti meg és frissítheti a Function app által jelenleg használt futtatókörnyezet verzióját.

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg a Function alkalmazást.

1. A **konfigurált szolgáltatások**területen válassza a **Function app Settings**elemet.

    ![Function app-beállítások kiválasztása](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. A **Function app Settings** lapon keresse meg a **futtatókörnyezet verzióját**. Jegyezze fel a futtatókörnyezet adott verzióját és a kért főverziót. Az alábbi példában a verzió a következőre van beállítva `~2`:.

   ![Function app-beállítások kiválasztása](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Ha a Function alkalmazást az 1. x verzióra szeretné rögzíteni, válassza a **~ 1** elemet a **futtatókörnyezet verziója**alatt. Ez a kapcsoló le van tiltva, ha az alkalmazásban vannak függvények.

1. Ha megváltoztatja a futtatókörnyezet verzióját, térjen vissza az **Áttekintés** lapra, és válassza az **Újraindítás** lehetőséget az alkalmazás újraindításához.  A Function app az 1. x futtatókörnyezetben futtatja az újraindítást, és a függvények létrehozásakor a rendszer az 1. x verziójú sablonokat használja.