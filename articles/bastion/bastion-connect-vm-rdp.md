---
title: Kapcsolódás Windows rendszerű virtuális géphez az Azure Bastion használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Windows rendszerű Azure-beli virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744323"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával

Az Azure Bastion használatával biztonságosan és zökkenőmentesen csatlakozhat a virtuális gépekhez az SSL-en keresztül közvetlenül a Azure Portal. Az Azure Bastion használata esetén a virtuális gépekhez nem szükséges ügyfél, ügynök vagy további szoftver. Ez a cikk bemutatja, hogyan csatlakozhat a Windows rendszerű virtuális gépekhez. További információ a Linux rendszerű virtuális gépekhez való csatlakozásról: [Csatlakozás virtuális géphez az Azure Bastion-Linux használatával](bastion-connect-vm-ssh.md).

Az Azure Bastion biztonságos kapcsolatot biztosít a virtuális hálózatban lévő összes olyan virtuális géphez, amelyben kiépítve van. Az Azure Bastion használatával megvédheti a virtuális gépeket az RDP/SSH-portok a külvilág felé való kitéve, miközben továbbra is biztonságos hozzáférést biztosít az RDP/SSH használatával. További információ: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy beállította az Azure-beli megerősített gazdagépet ahhoz a virtuális hálózathoz, amelyben a virtuális gép található. A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a használatával csatlakozhat a virtuális hálózatban található bármelyik virtuális GÉPHEZ. Az Azure Bastion-gazdagépek beállításával kapcsolatban lásd: [Azure Bastion-gazdagép létrehozása](bastion-create-host-portal.md).

### <a name="required-roles"></a>Szükséges szerepkörök

A kapcsolatok létrehozásához a következő szerepkörök szükségesek:

* Olvasó szerepkör a virtuális gépen
* Olvasó szerepkör a virtuális gép magánhálózati IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure-beli megerősített erőforráson

### <a name="ports"></a>Portok

A Windows rendszerű virtuális géphez való csatlakozáshoz a következő portokat kell megnyitnia a Windows rendszerű virtuális gépen:

* Bejövő portok: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Kapcsolódás

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Kapcsolódás** elemre, és válassza a **kilépési pont lehetőséget** .

   ![VM-kapcsolat](./media/bastion-connect-vm-rdp/connect.png)
1. Miután rákattintott a Bastion elemre, egy oldalsó sáv jelenik meg, amely három lapot – RDP, SSH és Bastion – tartalmaz. Ha a megerősített szolgáltatás a virtuális hálózathoz lett kiépítve, a megerősített lap alapértelmezés szerint aktív. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, akkor a következő hivatkozásra kattintva konfigurálhatja a Bastion-t. A konfigurációs utasításokért lásd: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![megerősített lap](./media/bastion-connect-vm-rdp/bastion.png)
1. A megerősített lapon adja meg a virtuális gép felhasználónevét és jelszavát, majd kattintson a **kapcsolat**gombra. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   ![RDP-kapcsolat](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>További lépések

A [megerősített GYIK](bastion-faq.md) áttekintése
