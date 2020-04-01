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
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244989"
---
## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban virtuális hálózatot és alhálózatot hozhat létre.

1. A képernyő bal felső részén válassza az Erőforrás létrehozása **> Hálózati > virtuális hálózat** elemre, vagy keressen virtuális **hálózatot** a keresőmezőben.

2. A **Virtuális hálózat létrehozása**csoportban írja be vagy jelölje ki ezt az információt az **Alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | Válassza **az Új létrehozása**lehetőséget, írja be ** \<az erőforráscsoport-név>**, majd válassza az OK gombot, vagy válasszon ki egy meglévő ** \<erőforráscsoport-név>** paraméterek alapján. |
    | **Példány részletei** |                                                                 |
    | Név             | Adja meg ** \<a virtuális hálózat nevét>**                                    |
    | Régió           | ** \<Régiónév->kiválasztása** |

3. Válassza az **IP-címek** lapot, vagy kattintson a Lap alján található **Következő: IP-címek** gomb.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtér | Adja meg ** \<az IPv4-címtér>** |

5. Az **Alhálózat neve csoportban**jelölje ki az **alapértelmezett**szót.

6. Az **Alhálózat szerkesztése**mezőbe írja be ezt az információt:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | Adja meg ** \<az alhálózat nevét>** |
    | Alhálózati címtartomány | Adja meg ** \<az alhálózati címtartomány>**

7. Kattintson a **Mentés** gombra.

8. Válassza a **Véleményezés + létrehozás** lapot, vagy kattintson a **Véleményezés + létrehozás** gombra.

9. Kattintson a **Létrehozás** gombra.