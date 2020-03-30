---
title: Csatlakozás Windows virtuális géphez az Azure Bastion használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Azure-beli virtuális géphez Az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597344"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Csatlakozás Windows-alapú virtuális géphez az Azure Bastion használatával

Az Azure Bastion használatával biztonságosan és zökkenőmentesen csatlakozhat a virtuális gépekhez SSL-en keresztül közvetlenül az Azure Portalon. Az Azure Bastion használatakor a virtuális gépek nem igényelnek ügyfelet, ügynököt vagy további szoftvert. Ez a cikk bemutatja, hogyan csatlakozhat a Windows virtuális gépekhez. A Linux virtuális géphez való csatlakozásról a [Csatlakozás virtuális géphez az Azure Bastion - Linux használatával című](bastion-connect-vm-ssh.md)témakörben talál.

Az Azure Bastion biztonságos kapcsolatot biztosít a virtuális hálózat összes virtuális gépéhez, amelyben ki van építve. Az Azure Bastion használatával megvédi a virtuális gépeket az RDP/SSH portok külvilág számára való kiteszik, miközben továbbra is biztonságos hozzáférést biztosít az RDP/SSH használatával. További információ: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy beállított egy Azure-bastion gazdagépet a virtuális hálózathoz, amelyben a virtuális gép található. Miután a megerősített szolgáltatás kivan építve és üzembe helyezve a virtuális hálózatban, használhatja a virtuális hálózat bármely virtuális géphez való csatlakozásához. Az Azure Bastion gazdagép beállításához [lásd: Hozzon létre egy Azure-bastion host.](bastion-create-host-portal.md)

### <a name="required-roles"></a>Szükséges szerepkörök

A kapcsolat létrejöttéhez a következő szerepkörök szükségesek:

* Olvasói szerepkör a virtuális gépen
* Olvasói szerepkör a virtuális gép privát IP-címével rendelkező hálózati adapteren
* Olvasói szerepkör az Azure Bastion erőforráson

### <a name="ports"></a>Portok

A Windows virtuális géphez való csatlakozáshoz a következő portok vannak megnyitva a Windows virtuális gépen:

* Bejövő portok: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Kapcsolódás

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Keresse meg azt a virtuális gépet, amelyhez csatlakozni szeretne, majd kattintson a **Csatlakozás** gombra, és válassza a **Bástya** lehetőséget a legördülő menüből.

   ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-rdp/connect.png)
1. Miután a Bastion gombra kattintott, egy oldalsáv jelenik meg, amely három lapot – RDP, SSH és Bástya . Ha a Bastion ki van építve a virtuális hálózathoz, a Bástya lap alapértelmezés szerint aktív. Ha nem létesített hidasztiszt a virtuális hálózathoz, a hivatkozásra kattintva konfigurálhatja a Bastion t. A konfigurációs utasításokat a [Bastion konfigurálása](bastion-create-host-portal.md)című témakörben találja.

   ![bástya lap](./media/bastion-connect-vm-rdp/bastion.png)
1. A Bástya lapon adja meg a virtuális gép felhasználónevét és jelszavát, majd kattintson a **Csatlakozás gombra.** Az RDP-kapcsolat a virtuális gép hez keresztül Bastion nyílik meg közvetlenül az Azure Portalon (HTML5 felett) a 443-as port és a Bástya szolgáltatás használatával.

   ![RDP-csatlakozás](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>További lépések

Olvassa el a [Bástya GYIK-et](bastion-faq.md)
