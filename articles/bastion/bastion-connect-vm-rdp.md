---
title: Kapcsolódás Windows rendszerű virtuális géphez az Azure Bastion használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat egy Windows rendszerű Azure-beli virtuális géphez az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978132"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Kapcsolódás Windowsos virtuális géphez az Azure Bastion használatával

Az Azure Bastion használatával biztonságosan és zökkenőmentesen csatlakozhat a virtuális gépekhez az SSL-en keresztül közvetlenül a Azure Portal. Az Azure Bastion használata esetén a virtuális gépekhez nem szükséges ügyfél, ügynök vagy további szoftver. Ez a cikk bemutatja, hogyan csatlakozhat a Windows rendszerű virtuális gépekhez. További információ a Linux rendszerű virtuális gépekhez való csatlakozásról: [Csatlakozás virtuális géphez az Azure Bastion-Linux használatával](bastion-connect-vm-ssh.md).

Az Azure Bastion biztonságos kapcsolatot biztosít a virtuális hálózatban lévő összes olyan virtuális géphez, amelyben kiépítve van. Az Azure Bastion használatával megvédheti a virtuális gépeket az RDP/SSH-portok a külvilág felé való kitéve, miközben továbbra is biztonságos hozzáférést biztosít az RDP/SSH használatával. További információ: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előkészületek

### <a name="install-the-bastion-host"></a>A megerősített gazdagép telepítése

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

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
