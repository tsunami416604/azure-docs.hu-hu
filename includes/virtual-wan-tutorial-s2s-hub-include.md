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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488968"
---
1. Keresse meg a létrehozott virtuális WAN-t. A virtuális WAN lapon a **kapcsolat** szakaszban válassza a **hubok**lehetőséget.
2. A hubok lapon válassza az **+ új hub** lehetőséget a **virtuális központ létrehozása** lap megnyitásához.

    ![Alapvető beállítások](./media/virtual-wan-tutorial-hub-include/basics.png "Alapvető beállítások")
3. A **virtuális központ létrehozása** lap **alapjai** lapon végezze el a következő mezőket:

    **Projekt részletei**

   * Régió (korábbi nevén hely)
   * Name (Név)
   * Hub magánhálózati címtartomány. A minimális címtartomány/24 a hub létrehozásához, ami azt jelenti, hogy a/25-től/32-ig terjedő minden tartomány hibát eredményez a létrehozás során.
4. Válassza **a Next (tovább) lehetőséget: helyek közötti**kapcsolat.

    ![Helyek közötti](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Helyek közötti kapcsolat")

5. A **helyek közötti** lapon hajtsa végre a következő mezőket:

   * A helyek közötti VPN létrehozásához válassza az **Igen** lehetőséget.
   * A AS Number mező jelenleg nem szerkeszthető a virtuális központban.
   * Válassza ki az **átjáró méretezési egységének** értékét a legördülő listából. A skálázási egység lehetővé teszi, hogy kiválassza a virtuális központban létrehozott VPN-átjáró összesített átviteli sebességét, hogy a helyekhez kapcsolódjon. Ha 1 skálázási egység = 500 Mbps-t választ, azt jelenti, hogy a redundancia két példánya jön létre, amelyek mindegyike 500 Mbps maximális átviteli sebességgel rendelkezik. Ha például öt ág van, amelyek mindegyike 10 Mbps-t használ a fiókirodában, akkor a fő végponton 50 Mbps összesített értékre van szükség. Az Azure VPN Gateway összesített kapacitásának megtervezését a hub-ágak számának támogatásához szükséges kapacitás kiértékelése után kell elvégezni.
6. Válassza a **felülvizsgálat + létrehozás** elemet az ellenőrzéshez.
7. Válassza a **Létrehozás** lehetőséget a központ létrehozásához. 30 perc elteltével a **frissítés** gombra kattintva megtekintheti a hubot a **hubok** oldalon. Az erőforráshoz való **ugráshoz válassza az erőforrás keresése** lehetőséget.