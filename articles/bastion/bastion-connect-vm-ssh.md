---
title: Csatlakozás Linuxos virtuális géphez az Azure Bastion használatával
description: Ebben a cikkben megtudhatja, hogyan csatlakozhat a Linux virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596827"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Csatlakozás SSH használatával egy Linux-alapú virtuális géphez az Azure Bastion használatával

Ez a cikk bemutatja, hogyan biztonságosan és zökkenőmentesen SSH a Linux virtuális virtuális hálózat ban A Linux virtuális virtuális gépek. Közvetlenül az Azure Portalról csatlakozhat egy virtuális géphez. Az Azure Bastion használatakor a virtuális gépek nem igényelnek ügyfelet, ügynököt vagy további szoftvert. Az Azure Bastionról további információt az [Áttekintés című témakörben talál.](bastion-overview.md)

Az Azure Bastion segítségével csatlakozhat egy Linux virtuális gép hez az SSH használatával. A hitelesítéshez felhasználónév/jelszó és SSH-kulcsok is használhatók. Az SSH-kulcsokkal a virtuális géphez a következők valamelyikével csatlakozhat:

* Személyes kulcs, amelyet manuálisan adott meg
* A személyes kulcs adatait tartalmazó fájl

Az SSH személyes kulcsának a programmal `"-----BEGIN RSA PRIVATE KEY-----"` kezdődő `"-----END RSA PRIVATE KEY-----"`és végződő formátumban kell lennie.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy beállított egy Azure-bastion gazdagépet a virtuális hálózathoz, amelyben a virtuális gép található. További információ: [Create an Azure Bastion host](bastion-create-host-portal.md). Miután a megerősített szolgáltatás kivan építve és üzembe helyezve a virtuális hálózatban, használhatja a virtuális hálózat bármely virtuális géphez való csatlakozásához. 

Amikor a Bastion használatával csatlakozik, feltételezi, hogy rdp-t használ a Windows virtuális géphez való csatlakozáshoz, és az SSH-t a Linux-virtuális gépekhez való csatlakozáshoz. A Windows virtuális gépekhez való csatlakozásról a [Csatlakozás virtuális géphez – Windows](bastion-connect-vm-rdp.md)című témakörben talál további információt.

### <a name="required-roles"></a>Szükséges szerepkörök

A kapcsolat létrejöttéhez a következő szerepkörökszükségesek:

* Olvasói szerepkör a virtuális gépen
* Olvasói szerepkör a virtuális gép privát IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure Bastion erőforráson

### <a name="ports"></a>Portok

A Linux virtuális géphez SSH-n keresztül történő csatlakozáshoz a következő portokkal kell rendelkeznie a virtuális gépen:

* Bejövő port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Csatlakozás: Felhasználónév és jelszó használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Csatlakozás** gombra, és válassza a **Bástya** lehetőséget a legördülő menüből.

   ![Kapcsolódás](./media/bastion-connect-vm-ssh/connect.png)
1. Miután a Bastion gombra kattintott, egy oldalsáv jelenik meg, amely három lapot – RDP, SSH és Bástya . Ha a Bastion ki van építve a virtuális hálózathoz, a Bástya lap alapértelmezés szerint aktív. Ha nem létesített hidatot a virtuális hálózathoz, olvassa el [a Bastion konfigurálása című témakört.](bastion-create-host-portal.md)

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Adja meg az SSH felhasználónevét és jelszavát a virtuális géphez.
1. A kulcs megadása után kattintson a **Csatlakozás** gombra.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Csatlakozás: Személyes kulcs manuális megadása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Csatlakozás** gombra, és válassza a **Bástya** lehetőséget a legördülő menüből.

   ![Kapcsolódás](./media/bastion-connect-vm-ssh/connect.png)
1. Miután a Bastion gombra kattintott, egy oldalsáv jelenik meg, amely három lapot – RDP, SSH és Bástya . Ha a Bastion ki van építve a virtuális hálózathoz, a Bástya lap alapértelmezés szerint aktív. Ha nem létesített hidatot a virtuális hálózathoz, olvassa el [a Bastion konfigurálása című témakört.](bastion-create-host-portal.md)

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Írja be a felhasználónevet, és válassza **az SSH személyes kulcsot**.
1. Írja be a személyes kulcsot az **SSH személyes kulcs** szövegterületre (vagy illessze be közvetlenül).
1. A kulcs megadása után kattintson a **Csatlakozás** gombra.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Csatlakozás: Személyes kulcsfájl használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Csatlakozás** gombra, és válassza a **Bástya** lehetőséget a legördülő menüből.

   ![Kapcsolódás](./media/bastion-connect-vm-ssh/connect.png)
1. Miután a Bastion gombra kattintott, egy oldalsáv jelenik meg, amely három lapot – RDP, SSH és Bástya . Ha a Bastion ki van építve a virtuális hálózathoz, a Bástya lap alapértelmezés szerint aktív. Ha nem létesített hidatot a virtuális hálózathoz, olvassa el [a Bastion konfigurálása című témakört.](bastion-create-host-portal.md)

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Írja be a felhasználónevet, és válassza az **SSH személyes kulcsot a helyi fájlból**.
1. Kattintson a **Tallózás** gombra (a mappa ikonjára a helyi fájlban).
1. Tallózással keresse meg a fájlt, majd kattintson a **Megnyitás gombra.**
1. Kattintson a **Csatlakozás** gombra a virtuális géphez való csatlakozáshoz. Miután a Csatlakozás elemre kattint, az SSH a virtuális géphez közvetlenül megnyílik az Azure Portalon. Ez a kapcsolat html5-ös felett van, a Bastion szolgáltatás 443-as portját használva a virtuális gép privát IP-címén keresztül.

## <a name="next-steps"></a>További lépések

Olvassa el a [Bástya GYIK-et](bastion-faq.md)
