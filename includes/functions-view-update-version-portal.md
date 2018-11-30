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
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500039"
---
Az alábbi eljárás segítségével megtekintheti, és a függvényalkalmazás által jelenleg használt futtatókörnyezet verziójának frissítése.

1. Az a [az Azure portal](https://portal.azure.com), tallózással keresse meg a függvényalkalmazást.

1. A **konfigurált szolgáltatások**, válassza a **Alkalmazásbeállítások függvény**.

    ![Válassza ki a függvényalkalmazás beállításai](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Az a **Alkalmazásbeállítások függvény** lapra, keresse meg a **verze modulu Runtime**. Vegye figyelembe az adott futtatókörnyezet-verzió és a kért főverziója. Az alábbi példában a verzió értéke `~2`.

   ![Válassza ki a függvényalkalmazás beállításai](./media/functions-view-update-version-portal/function-app-view-version.png)

1. A függvényalkalmazást, hogy a verzió 1.x futásidejű rögzít, válassza a **~ 1** alatt **verze modulu Runtime**. Ez a kapcsoló le van tiltva, ha az alkalmazás funkciói.

1. Ha módosítja a futtatókörnyezet-verzió, lépjen vissza a **áttekintése** lapot, majd **indítsa újra a** , indítsa újra az alkalmazást.  A függvényalkalmazás újraindul, 1.x verzió modulban fut, és a verzió 1.x sablonok függvények létrehozásakor használt.