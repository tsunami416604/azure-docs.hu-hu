---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 43ede7f3340f190f9eb37ca90fb74f2215db9062
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227402"
---
## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > virtuális hálózat** lehetőséget, vagy keressen rá a **virtuális hálózatra** a keresőmezőbe.

2. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetést     | Azure-előfizetés kiválasztása                                  |
    | Erőforráscsoport   | Válassza az **új létrehozása**elemet, írja be **\<erőforrás-csoport neve >** , majd kattintson az OK gombra. |
    | **Példány részletei** |                                                                 |
    | Name (Név)             | Adja meg **\<Virtual-Network-name >**                                    |
    | Régió           | **\<régió kiválasztása – név >** |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címterület | Adja meg **\<IPv4-címtartomány >** |

5. Az **alhálózat neve**alatt válassza ki az **alapértelmezett**szót.

6. Az **alhálózat szerkesztése**területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | Adja meg **\<alhálózat neve >** |
    | Alhálózati címtartomány | Adja meg **\<alhálózat-címtartomány >**

7. Kattintson a **Mentés** gombra.

8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Kattintson a **Létrehozás** gombra.