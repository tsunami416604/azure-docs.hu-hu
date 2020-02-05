---
title: Kapcsolódás Windows rendszerű virtuális géphez az Azure Bastion használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Windows rendszerű Azure-beli virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990487"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával

Ebből a cikkből megtudhatja, hogyan lehet biztonságosan és zökkenőmentesen RDP-t használni az Azure-beli virtuális hálózatban található Windows rendszerű virtuális gépekhez az Azure Bastion használatával A virtuális gépekhez közvetlenül az Azure Portalon csatlakozhat. Az Azure Bastion használatával a virtuális gépeken nincs szükség ügyfélre, ügynökre és további szoftverekre sem. További információ az Azure Bastion-ről: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előzetes teendők

Győződjön meg arról, hogy beállította az Azure-beli megerősített gazdagépet ahhoz a virtuális hálózathoz, amelyben a virtuális gép található. További információ: [Azure Bastion-gazdagép létrehozása](bastion-create-host-portal.md). A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a használatával csatlakozhat a virtuális hálózat bármely virtuális GÉPHEZ.

A Bastion feltételezi, hogy RDP-t használ egy Windows rendszerű virtuális géphez való kapcsolódáshoz, és az SSH-t a linuxos virtuális gépekhez való kapcsolódáshoz. További információ a Linux rendszerű virtuális gépekhez való kapcsolódásról: [Kapcsolódás virtuális géphez – Linux](bastion-connect-vm-ssh.md).

### <a name="required-roles"></a>Szükséges szerepkörök
A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a virtuális gép magánhálózati IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure-beli megerősített erőforráson

### <a name="ports"></a>Portok

Ha a Windows rendszerű virtuális géphez RDP-n keresztül szeretne csatlakozni, a következő portokat kell megnyitnia a Windows rendszerű virtuális gépen:

* Bejövő portok: RDP (3389)

## <a name="rdp"></a>Kapcsolat RDP használatával

1. Nyissa meg az [Azure portált](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás**elemre. RDP-kapcsolat használata esetén a virtuális gépnek Windowsos virtuális gépnek kell lennie.

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/connect.png)
1. Miután rákattintott a kapcsolat lehetőségre, egy oldalsó sáv jelenik meg, amely három lapot – RDP, SSH és Bastion – tartalmaz. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, akkor a következő hivatkozásra kattintva konfigurálhatja a Bastion-t. A konfigurációs utasításokért lásd: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/bastion.png)
1. A megerősített lapon adja meg a virtuális gép felhasználónevét és jelszavát, majd kattintson a **kapcsolat**elemre. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Következő lépések

A [megerősített GYIK](bastion-faq.md) áttekintése
