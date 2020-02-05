---
title: Kapcsolódás Linux rendszerű virtuális géphez az Azure Bastion használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat Linux rendszerű virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7fe1c2f74ca2a7b0fa4aefad934c45edd6f85a73
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990441"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Kapcsolódás az SSH-val egy Linux rendszerű virtuális géphez az Azure Bastion használatával

Ebből a cikkből megtudhatja, hogyan teheti meg biztonságosan és zökkenőmentesen az SSH-t egy Azure-beli virtuális hálózatban lévő linuxos virtuális gépeken. A virtuális gépekhez közvetlenül az Azure Portalon csatlakozhat. Az Azure Bastion használatával a virtuális gépeken nincs szükség ügyfélre, ügynökre és további szoftverekre sem. További információ az Azure Bastion-ről: [Áttekintés](bastion-overview.md).

Az Azure Bastion használatával Linux rendszerű virtuális gépekhez csatlakozhat SSH-val. A hitelesítéshez felhasználónevet/jelszót és SSH-kulcsokat is használhat. Az SSH-kulcsokkal csatlakozhat a virtuális géphez az alábbiak használatával:

* Egy manuálisan megadott titkos kulcs
* A titkos kulcs adatait tartalmazó fájl

Az SSH titkos kulcsának olyan formátumúnak kell lennie, amely a `"-----BEGIN RSA PRIVATE KEY-----"` és a `"-----END RSA PRIVATE KEY-----"`végződik.

## <a name="before-you-begin"></a>Előzetes teendők

Győződjön meg arról, hogy beállította az Azure-beli megerősített gazdagépet ahhoz a virtuális hálózathoz, amelyben a virtuális gép található. További információ: [Azure Bastion-gazdagép létrehozása](bastion-create-host-portal.md). A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a használatával csatlakozhat a virtuális hálózat bármely virtuális GÉPHEZ. 

Amikor a Bastion használatával csatlakozik, feltételezi, hogy az RDP-t használja egy Windows rendszerű virtuális géphez való csatlakozáshoz, és SSH-t a linuxos virtuális gépekhez való csatlakozáshoz. További információ a Windows rendszerű virtuális gépekhez való csatlakozásról: [Csatlakozás virtuális géphez – Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Szükséges szerepkörök

A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a virtuális gép magánhálózati IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure-beli megerősített erőforráson

### <a name="ports"></a>Portok

Ha SSH-n keresztül szeretne csatlakozni a Linux rendszerű virtuális géphez, a következő portokat kell megnyitnia a virtuális gépen:

* Bejövő port: SSH (22)


## <a name="username"></a>Kapcsolat: Felhasználónév és jelszó használata

1.   Nyissa meg az [Azure portált](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás**elemre. SSH-kapcsolatok használata esetén a virtuális gépnek linuxos virtuális gépnek kell lennie.
1. Miután rákattintott a kapcsolat lehetőségre, egy oldalsó sáv jelenik meg, amely három lapot – RDP, SSH és Bastion – tartalmaz. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![VM-kapcsolat](./media/bastion-connect-vm-ssh/bastion.png)
1. Adja meg az SSH felhasználónevét és jelszavát a virtuális géphez.
1. A kulcs beírása után kattintson a **kapcsolat** gombra.

## <a name="privatekey"></a>Kapcsolat: manuálisan adjon meg egy titkos kulcsot

1. Nyissa meg az [Azure portált](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás**elemre. SSH-kapcsolatok használata esetén a virtuális gépnek linuxos virtuális gépnek kell lennie.
1. Miután rákattintott a kapcsolat lehetőségre, egy oldalsó sáv jelenik meg, amely három lapot – RDP, SSH és Bastion – tartalmaz. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![VM-kapcsolat](./media/bastion-connect-vm-ssh/bastion.png)
1. Adja meg a felhasználónevet, és válassza az **SSH titkos kulcs**lehetőséget.
1. Adja meg a titkos kulcsot a szövegmezőben található **SSH titkos kulcsban** (vagy illessze be közvetlenül a vágólapra).
1. A kulcs beírása után kattintson a **kapcsolat** gombra.

## <a name="ssh"></a>Összekapcsolás: titkos kulcsfájl használata

1. Nyissa meg az [Azure portált](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás**elemre. SSH-kapcsolatok használata esetén a virtuális gépnek linuxos virtuális gépnek kell lennie.

   ![VM-kapcsolat](./media/bastion-connect-vm-ssh/connect.png)
1. Miután rákattintott a kapcsolat lehetőségre, egy oldalsó sáv jelenik meg, amely három lapot – RDP, SSH és Bastion – tartalmaz. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, olvassa el a következő témakört: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![VM-kapcsolat](./media/bastion-connect-vm-ssh/bastion.png)
1. Adja meg a felhasználónevet, és válassza az **SSH titkos kulcs lehetőséget a helyi fájlból**.
1. Kattintson a **Tallózás** gombra (a mappa ikon a helyi fájlban).
1. Tallózással keresse meg a fájlt, majd kattintson a **Megnyitás**gombra.
1. Kattintson a **Kapcsolódás** gombra a virtuális géphez való kapcsolódáshoz. Ha a kapcsolódás gombra kattint, az SSH-t a virtuális géphez közvetlenül a Azure Portal fogja megnyitni. Ez a kapcsolat a HTML5-en keresztül a 443-es portot használja a virtuális gép magánhálózati IP-címén keresztül.

## <a name="next-steps"></a>Következő lépések

A [megerősített GYIK](bastion-faq.md) áttekintése
