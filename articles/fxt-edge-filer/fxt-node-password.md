---
title: Hardver-Microsoft Azure FXT Edge Filer inicializálása
description: Kezdeti jelszó beállítása az Azure FXT Edge Filer-csomópontjain
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 080aa05af77b996bc0eb71287a3dfef25c24629a
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256010"
---
# <a name="tutorial-set-hardware-passwords"></a>Oktatóanyag: hardveres jelszavak beállítása

Amikor első alkalommal kapcsol be egy Azure FXT Edge Filer-csomópontot, be kell állítania egy rendszergazdai jelszót. A hardveres csomópontok nem rendelkeznek alapértelmezett jelszóval. 

A hálózati portok mindaddig le vannak tiltva, amíg a jelszót be nem állította, és a root felhasználó bejelentkezik.

Ezt a lépést a csomópont telepítése és kábelezése után végezze el, de a fürt létrehozásának megkísérlése előtt. 

Ez az oktatóanyag azt ismerteti, hogyan csatlakozhat a hardver-csomóponthoz, és hogyan állíthatja be a jelszót. 

Az oktatóanyag során a következőket fogja elsajátítani: 

> [!div class="checklist"]
> * Billentyűzet és figyelő összekapcsolása a csomóponttal és bekapcsolás
> * A iDRAC-port és a gyökérszintű felhasználó jelszavának beállítása ezen a csomóponton
> * Bejelentkezés root-ként 

Ismételje meg ezeket a lépéseket minden olyan csomópont esetében, amelyet a fürtben használni fog. 

Az oktatóanyag elvégzése körülbelül 15 percet vesz igénybe. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt végezze el a következő lépéseket: 

* [Telepítse](fxt-install.md) az összes Azure FXT Edge Filer-csomópontot egy berendezési állványba, és csatlakoztassa az áramellátási kábeleket és a hálózati hozzáférést a [korábbi oktatóanyagban](fxt-network-power.md)leírtak szerint. 
* Keressen egy USB-kapcsolattal rendelkező billentyűzetet és egy VGA-csatlakoztatott figyelőt, amelyet csatlakoztathat a hardveres csomópontokhoz. (A csomópont soros portja inaktív a jelszó beállítása előtt.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Billentyűzet és figyelő összekötése a csomóponttal

A monitor és a billentyűzet fizikailag csatlakoztatható az Azure FXT Edge Filer-csomóponthoz. 

* Kapcsolja össze a figyelőt a VGA-porttal.
* Kapcsolja össze a billentyűzetet az egyik USB-porttal. 

Ez a hivatkozási diagram a ház hátoldalán található portok megkeresésére használható. 

> [!NOTE]
> A soros port csak a jelszó beállítása után inaktív. 

![Az Azure FXT Edge Filer hátoldalának ábrája, amely soros, VGA és USB portokkal van ellátva](media/fxt-back-serial-vga-usb.png)

Ha több csomópontot szeretne ugyanahhoz a perifériához csatlakoztatni, a KVM-kapcsolót is használhatja. 

Kapcsolja be a csomópontot a főkapcsoló gomb megnyomásával. 

![Az Azure FXT Edge Filer-kör előtti Power gombjának ábrája a jobb felső sarokban látható.](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Kezdeti jelszavak beállítása 

Az Azure FXT Edge Filer-csomópontja a rendszerindításkor különböző üzeneteket fog kinyomtatni a figyelőbe. Néhány pillanat múlva a következőhöz hasonló kezdeti beállítási képernyő jelenik meg:

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

A beírt jelszó két dolgot használ: 

* Ez az Azure FXT Edge Filer-csomópontjának ideiglenes legfelső szintű jelszava. 

  Ez a jelszó akkor változik, amikor létrehoz egy fürtöt a csomópont használatával, vagy ha hozzáadja ezt a csomópontot a fürthöz. A fürt összes csomópontja számára a Fürtfelügyelő jelszava (a ``admin`` felhasználóhoz társítva) is a gyökér jelszava.

* Ez a iDRAC/IPMI hardveres felügyeleti port hosszú távú jelszava.

  Ügyeljen arra, hogy jegyezze fel a jelszót arra az esetre, ha később be kell jelentkeznie a IPMI a hardveres probléma elhárításához.

Adja meg és erősítse meg a jelszót: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

A jelszó megadása után a rendszer folytatja a rendszerindítást. Ha ez befejeződik, ``login:`` promptot ad meg. 

## <a name="sign-in-as-root"></a>Bejelentkezés root-ként

Jelentkezzen be ``root`` néven az imént beállított jelszóval. 

```
login: root
Password:**********
```

Miután bejelentkezett a gyökérként, a hálózati portok aktívak, és az IP-címekhez felveszik a kapcsolatot a DHCP-kiszolgálóval. 

## <a name="next-steps"></a>Következő lépések

A csomópont készen áll a fürt egy részének beosztására. Ezzel létrehozhatja az Azure FXT Edge Filer-fürtöt, vagy [hozzáadhatja egy meglévő fürthöz](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Fürt létrehozása](fxt-cluster-create.md)
