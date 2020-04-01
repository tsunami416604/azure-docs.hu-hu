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
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488968"
---
1. Keresse meg a létrehozott virtuális WAN-t. A Virtual WAN (Virtuális WAN) lap **Kapcsolat szakaszában** válassza a **Hubs (Hubs)** lehetőséget.
2. A Hubs lapon válassza az **+Új központ** lehetőséget a **Virtuális központi központ létrehozása** lap megnyitásához.

    ![Alapvető beállítások](./media/virtual-wan-tutorial-hub-include/basics.png "Alapvető beállítások")
3. A **Virtuális központ létrehozása** lap **alapjai** lapján töltse ki a következő mezőket:

    **Projekt részletei**

   * Régió (korábbi nevén Hely)
   * Név
   * Hub privát címtér. A minimális címterület /24 hub létrehozásához, ami azt jelenti, hogy a /25 és /32 közötti tartomány hibát okoz a létrehozás során.
4. Válassza a **Tovább: Helyek közötti hely**lehetőséget.

    ![Helyek közötti kapcsolat](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Helyek közötti kapcsolat")

5. A **Helyek közötti** lapon töltse ki a következő mezőket:

   * Válassza az **Igen** lehetőséget a helyek közötti VPN létrehozásához.
   * Az AS-szám mező jelenleg nem szerkeszthető a virtuális központban.
   * Válassza ki az **Átjáró méretezési egységeinek** értékét a legördülő menüből. A méretezési egység lehetővé teszi a virtuális hubban létrehozott VPN-átjáró összesített átviteli kapcsolatának kiválasztását a helyek csatlakoztatásához. Ha 1 méretezési egység = 500 Mbps értéket választ, az azt jelenti, hogy két redundanciapéldány jön létre, amelyek maximális átviteli sebessége 500 Mbps. Ha például öt ága van, amelyek mindegyike 10 Mbps-t végez az ágon, akkor összesen 50 Mbps-ra lesz szüksége a fejvégén. Az Azure VPN-átjáró összesített kapacitásának megtervezése a hub ágak számának támogatásához szükséges kapacitás felmérése után kell elvégezni.
6. Az ellenőrzéshez válassza a **Véleményezés + Létrehozás** lehetőséget.
7. A hub létrehozásához válassza a **Létrehozás** gombot. 30 perc elteltével **frissítse** a hub megtekintéséhez a **Hubs** lapon. Válassza **az Ugrás az erőforráshoz** lehetőséget az erőforrásra való navigáláshoz.