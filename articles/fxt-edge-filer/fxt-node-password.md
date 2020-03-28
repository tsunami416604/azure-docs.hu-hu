---
title: 'Oktatóanyag: Hardver inicializálása – Azure FXT Edge Filer'
description: Kezdeti jelszó beállítása az Azure FXT Edge Filer csomópontjain
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550891"
---
# <a name="tutorial-set-hardware-passwords"></a>Oktatóanyag: Hardverjelszavak beállítása

Az első alkalommal, amikor bekapcsolja az Azure FXT Edge Filer csomópontot, be kell állítania egy gyökérjelszót. A hardvercsomópontok nem alapértelmezett jelszóval vannak elszállítva. 

A hálózati portok le vannak tiltva, amíg a jelszó be nem áll, és a gyökérfelhasználó be nem jelentkezik.

Ezt a lépést a csomópont telepítése és kábelezése után, de a fürt létrehozása előtt tegye meg. 

Ez az oktatóanyag bemutatja, hogyan csatlakozhat a hardvercsomóponthoz, és hogyan állíthatja be a jelszót. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Billentyűzet és monitor csatlakoztatása a csomóponthoz, és bekapcsolása
> * Az iDRAC-port és a gyökérfelhasználó jelszavainak beállítása ezen a csomóponton
> * Bejelentkezés gyökérként 

Ismételje meg ezeket a lépéseket a fürtben használni kívánt csomópontokesetében. 

Ez az oktatóanyag körülbelül 15 percet vesz igénybe. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt hajtsa végre az alábbi lépéseket: 

* [Telepítse](fxt-install.md) az egyes Azure FXT Edge Filer-csomópontokat egy berendezésállványra, és csatlakoztassa a tápkábeleket és a hálózati hozzáférést a [korábbi oktatóanyagban](fxt-network-power.md)leírtak szerint. 
* Keressen egy USB-csatlakozású billentyűzetet és egy VGA-csatlakozású monitort, amelyet a hardvercsomópontokhoz csatlakoztathat. (A jelszó beállítása előtt a csomópont soros portja inaktív.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Billentyűzet és monitor csatlakoztatása a csomóponthoz

Fizikailag csatlakoztasson egy monitort és egy billentyűzetet az Azure FXT Edge Filer csomóponthoz. 

* Csatlakoztassa a monitort a VGA-porthoz.
* Csatlakoztassa a billentyűzetet az egyik USB-porthoz. 

Ezzel a referenciaábrával megkeresheti a ház hátoldalán található portokat. 

> [!NOTE]
> A soros port inaktív, amíg a jelszó be nem áll. 

![az Azure FXT Edge Filer hátoldalán futó, VGA és USB portokkal ellátott diagram](media/fxt-back-serial-vga-usb.png)

KVM-kapcsolót akkor használhat, ha egynél több csomópontot szeretne ugyanazokhoz a perifériákhoz csatlakoztatni. 

Kapcsolja be a csomópontot az előlapon található bekapcsológomb megnyomásával. 

![Az Azure FXT Edge Filer elülső részének diagramja - a kerek bekapcsológomb a jobb felső sarok közelében van címkézve](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Kezdeti jelszavak beállítása 

Az Azure FXT Edge Filer csomópont különböző üzeneteket nyomtat a figyelőbe indítás közben. Néhány pillanat múlva, ez mutat egy kezdő beállít képernyő mint ez:

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

A megadott jelszó két dologban használható: 

* Ez az Azure FXT Edge Filer csomópont ideiglenes gyökérjelszava. 

  Ez a jelszó megváltozik, amikor fürtöt hoz létre ezzel a csomópontkal, vagy amikor hozzáadja ezt a csomópontot a fürthöz. A fürtkezelési jelszó (a ``admin``felhasználóhoz társítva) egyben a fürt összes csomópontjának gyökérszava is.

* Ez az iDRAC/IPMI hardverkezelő port hosszú távú jelszava.

  Győződjön meg róla, hogy emlékszik a jelszóra, ha később be kell jelentkeznie az IPMI-vel egy hardverprobléma elhárításához.

Adja meg és erősítse meg a jelszót: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

A jelszó megadása után a rendszer folytatja a rendszer indítását. Amikor befejezi, ez ad egy ``login:`` gyors. 

## <a name="sign-in-as-root"></a>Bejelentkezés gyökérként

Jelentkezzen be ``root`` az imént beállított jelszóval együtt. 

```
login: root
Password:**********
```

Miután bejelentkezett gyökérként, a hálózati portok aktívak, és kapcsolatba lépnek a DHCP-kiszolgálóval az IP-címekért. 

## <a name="next-steps"></a>További lépések

A csomópont készen áll arra, hogy egy fürt része legyen. Használhatja az Azure FXT Edge Filer fürt létrehozásához, vagy [hozzáadhatja egy meglévő fürthöz.](fxt-add-nodes.md) 

> [!div class="nextstepaction"]
> [Fürt létrehozása](fxt-cluster-create.md)
