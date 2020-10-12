---
title: fájlbefoglalás
description: fájlbefoglalás
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83343354"
---
A következő eljárással tekintheti meg és frissítheti a Function app által jelenleg használt futtatókörnyezet verzióját.

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg a Function alkalmazást.

1. A **Beállítások**területen válassza a **konfiguráció**elemet. A **Function Runtime beállításai** lapon keresse meg a **futtatókörnyezet verzióját**. Jegyezze fel a futtatókörnyezet adott verzióját. Az alábbi példában a verzió a következőre van beállítva: `~3` .

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Tekintse meg a futtatókörnyezet verzióját." border="true":::

1. Ha a Function alkalmazást az 1. x verzióra szeretné rögzíteni, válassza a **~ 1** elemet a **futtatókörnyezet verziója**alatt. Ez a kapcsoló le van tiltva, ha az alkalmazásban vannak függvények.

1. Ha megváltoztatja a futtatókörnyezet verzióját, térjen vissza az **Áttekintés** lapra, és válassza az **Újraindítás** lehetőséget az alkalmazás újraindításához.  A Function app az 1. x futtatókörnyezetben futtatja az újraindítást, és a függvények létrehozásakor a rendszer az 1. x verziójú sablonokat használja.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Tekintse meg a futtatókörnyezet verzióját." border="true":::
