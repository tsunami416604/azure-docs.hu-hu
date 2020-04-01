---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73491568"
---
1. Keresse meg a létrehozott virtuális WAN-t. A Virtual WAN (Virtuális WAN) lap **Kapcsolat szakaszában** válassza a **Hubs (Hubs)** lehetőséget.
2. A Hubs lapon válassza az **+Új központ** lehetőséget a **Virtuális központi központ létrehozása** lap megnyitásához.
3. A **Virtuális központ létrehozása** lap **alapjai** lapján töltse ki a következő mezőket:

   ![Alapvető beállítások](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Alapvető beállítások")

    **Projekt részletei**

   * Régió (korábbi nevén Hely)
   * Név
   * Hub privát címtér. A minimális címterület /24 hub létrehozásához, ami azt jelenti, hogy a /25 és /32 közötti tartomány hibát okoz a létrehozás során.
4. Válassza az **ExpressRoute lapot**.

5. Az **ExpressRoute** lapon töltse ki a következő mezőket:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * **ExpressRoute-átjáró** létrehozásához válassza az **Igen** lehetőséget.
   * Válassza ki az **Átjáró méretezési egységeinek** értékét a legördülő menüből.
6. Az ellenőrzéshez válassza a **Véleményezés + Létrehozás** lehetőséget.
7. A hub létrehozásához válassza a **Létrehozás** gombot. 30 perc elteltével **frissítse** a hub megtekintéséhez a **Hubs** lapon. Válassza **az Ugrás az erőforráshoz** lehetőséget az erőforrásra való navigáláshoz.