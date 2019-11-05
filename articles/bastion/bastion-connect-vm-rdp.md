---
title: Kapcsolódás Windows rendszerű virtuális géphez az Azure Bastion használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Windows rendszerű Azure-beli virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498356"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával

Ebből a cikkből megtudhatja, hogyan lehet biztonságosan és zökkenőmentesen RDP-t használni az Azure-beli virtuális hálózatban található Windows rendszerű virtuális gépekhez az Azure Bastion használatával A virtuális gépekhez közvetlenül az Azure Portalon csatlakozhat. Az Azure Bastion használatával a virtuális gépeken nincs szükség ügyfélre, ügynökre és további szoftverekre sem. További információ az Azure Bastion-ről: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előzetes teendők

Győződjön meg arról, hogy beállította az Azure-beli megerősített gazdagépet ahhoz a virtuális hálózathoz, amelyben a virtuális gép található. További információ: [Azure Bastion-gazdagép létrehozása](bastion-create-host-portal.md). A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a használatával csatlakozhat a virtuális hálózat bármely virtuális GÉPHEZ. A Bastion feltételezi, hogy RDP-t használ egy Windows rendszerű virtuális géphez való kapcsolódáshoz, és az SSH-t a linuxos virtuális gépekhez való kapcsolódáshoz. További információ a Linux rendszerű virtuális gépekhez való kapcsolódásról: [Kapcsolódás virtuális géphez – Linux](bastion-connect-vm-ssh.md).

A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a virtuális gép magánhálózati IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure-beli megerősített erőforráson

## <a name="rdp"></a>Kapcsolat RDP használatával

1. Nyissa meg az [Azure portált](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás**elemre. RDP-kapcsolat használata esetén a virtuális gépnek Windowsos virtuális gépnek kell lennie.

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/connect.png)
1. Miután rákattintott a kapcsolat lehetőségre, egy oldalsó sáv jelenik meg, amely három lapot – RDP, SSH és Bastion – tartalmaz. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, akkor a következő hivatkozásra kattintva konfigurálhatja a Bastion-t. A konfigurációs utasításokért lásd: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/bastion.png)
1. A megerősített lapon adja meg a virtuális gép felhasználónevét és jelszavát, majd kattintson a **kapcsolat**elemre. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>További lépések

A [megerősített GYIK](bastion-faq.md) áttekintése