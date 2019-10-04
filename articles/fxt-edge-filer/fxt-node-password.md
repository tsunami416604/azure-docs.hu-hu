---
title: Hardver – Microsoft Azure FXT Edge Filer inicializálása
description: Azure FXT Edge Filer csomópontokon egy kezdeti jelszó megadása
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450295"
---
# <a name="tutorial-set-hardware-passwords"></a>Oktatóanyag: Hardver jelszavak beállítása

Először egy Azure FXT Edge Filer csomópont power, akkor be kell egy rendszergazdai jelszót. A hardver-csomópontok nem szállítják alapértelmezett jelszóval. 

Hálózati portok le vannak tiltva amíg után a jelszót van beállítva, és a gyökér szintű felhasználó bejelentkezik.

Ehhez a lépéshez, miután telepítette és a csomópont kábelek, de a fürt létrehozása előtt. 

Ez az oktatóanyag ismerteti a hardver csomópont csatlakozik, és állítsa be a jelszót. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Csatlakoztassa a billentyűzetet és a figyelő a csomópontra, és kapcsolja be
> * Állítsa be a jelszavakat a iDRAC port és a gyökérszintű felhasználó ezen a csomóponton
> * Jelentkezzen be gyökérszintű felhasználóként 

Ismételje meg ezeket a lépéseket a fürtben használni kívánt minden egyes csomópont esetében. 

Ebben az oktatóanyagban körülbelül 15 percet vesz igénybe. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt végezze el az alábbi lépéseket: 

* [Telepítés](fxt-install.md) minden-berendezések Azure FXT Edge Filer csomópontja állványra, és a tápkábelek csatlakoztatása és a hálózati hozzáférés leírtak szerint a [az oktatóanyag korábbi](fxt-network-power.md). 
* Keresse meg a billentyűzet USB-csatlakozóval csatlakoztatott és a hardveres csomópontok csatlakoztathat VGA kapcsolódó figyelő. (A csomópont soros port az inaktív, mielőtt beállítaná a jelszó.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>A csomóponthoz csatlakozás billentyűzetre és monitorra

Az Azure FXT Edge Filer csomópont fizikailag csatlakozni monitorral és billentyűzettel. 

* Csatlakozzon a figyelő a VGA-port.
* A billentyűzet USB-porttal kapcsolódnak. 

Ez a referencia-diagram használatával keresse meg a portokat a váz-jének. 

> [!NOTE]
> A soros port nem aktív, amíg, a jelszó megadása után. 

![ábrája Azure FXT Edge Filer oldalán a soros, VGA, és az USB-porttal címkével](media/fxt-back-serial-vga-usb.png)

Egy KVM-kapcsolót is használhatja, ha egynél több csomópont az azonos perifériák csatlakozni kíván. 

Energiagazdálkodási az előtér főkapcsoló lenyomásával a csomóponton. 

![főkapcsoló round Azure FXT Edge Filer – az első diagram feliratú tetejénél található megfelelő](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Kezdeti jelszó beállítása 

Az Azure FXT Edge Filer csomópont nyomtatási különböző üzeneteket a figyelő elindítása közben. Néhány pillanat múlva, egy kezdeti beállítás képernyőt jeleníti meg:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

A megadott jelszó két dolgokat szolgál: 

* Ez a csomópont Azure FXT Edge Filer ideiglenes rendszergazdai jelszavához. 

  Ez a jelszó változik, ez a csomópont használatával a fürt létrehozásakor, vagy ha ez a csomópont hozzáadása a fürthöz. A fürt felügyeleti jelszó (a felhasználóhoz társított ``admin``) egyben legfelső szintű jelszavát a fürt összes csomópontján.

* A hosszú távú jelszót a iDRAC/IPMI hardver felügyeleti port.

  Győződjön meg arról, jegyezze meg a jelszót kell bejelentkeznie IPMI később hardveres hiba elhárítása céljából.

Írja be és erősítse meg a jelszót: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Miután megadta a jelszót, a rendszer folytatja a rendszerindítást. Amikor befejeződik, ráadásul egy ``login:`` parancssort. 

## <a name="sign-in-as-root"></a>Jelentkezzen be gyökérszintű felhasználóként

Jelentkezzen be, ``root`` az imént beállított jelszóval. 

```
login: root
Password:**********
```

Gyökér szintű bejelentkezés után a hálózati portok aktívak, és felveszi a kapcsolatot a DHCP-kiszolgáló IP-címekhez. 

## <a name="next-steps"></a>További lépések

A csomópont készen áll a lehet egy fürt része. Az Azure FXT Edge Filer fürt létrehozására használhatja, vagy beállíthatja a [adja hozzá egy meglévő fürthöz](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Fürt létrehozása](fxt-cluster-create.md)
