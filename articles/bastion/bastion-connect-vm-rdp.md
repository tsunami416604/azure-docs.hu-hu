---
title: Kapcsolódás Azure megerősített használata Windows VM-hez |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure virtuális gépként fut a Windows Azure megerősített használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478409"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Windows virtuális gépek Azure megerősített (előzetes verzió) segítségével csatlakozhat

Ez a cikk bemutatja, hogyan biztonságosan és akadálytalanul RDP-vel a Windows virtuális gépek az Azure-beli virtuális hálózatot szeretné Azure megerősített használatával. A virtuális gépekhez közvetlenül az Azure Portalon csatlakozhat. Az Azure Bastion használatával a virtuális gépeken nincs szükség ügyfélre, ügynökre és további szoftverekre sem. Azure megerősített kapcsolatos további információkért lásd: a [áttekintése](bastion-overview.md).

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik-e beállítva, amelyben a virtuális gép található a virtuális hálózat Azure bástyagazdagép. További információkért lásd: [hozzon létre egy Azure bástyagazdagép](bastion-create-host-portal.md). A megerősített szolgáltatás kiépítése, és a virtuális hálózaton üzembe helyezett, miután a virtuális hálózatban lévő bármely virtuális Géphez való csatlakozáshoz használhatja. Ebben az előzetes verzióban megerősített azt feltételezi, hogy egy Windows virtuális gép és az SSH a Linux rendszerű virtuális géphez való kapcsolódásra való kapcsolódáshoz használ az RDP. Egy Linux rendszerű virtuális Géppel való kapcsolat kapcsolatos információkért lásd: [csatlakozhat a virtuális gépek – Linux](bastion-connect-vm-ssh.md).

Annak érdekében, hogy a kapcsolat létrehozása a következő szerepkörök szükségesek:

* A virtuális gépen olvasói szerepköre
* A hálózati adapteren a virtuális gép magánhálózati IP-Címmel rendelkező olvasói szerepköre
* Az Azure megerősített erőforráson olvasói szerepköre

## <a name="rdp"></a>Csatlakozás RDP-vel

1. Használat [ezt a hivatkozást](https://aka.ms/BastionHost) Azure megerősített a betekintő portál lap megnyitásához. Keresse meg a virtuális gép, amelyhez csatlakozni kíván, majd kattintson a **Connect**. A virtuális gép lehet egy Windows virtuális gép RDP-kapcsolatok használatakor.

    ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-rdp/connect.png)

1. Connect gombra kattint, az oldalsó sáv megjelenik, amely három lap található – RDP, SSH és a megerősített. Megerősített lett üzembe helyezve, a virtuális hálózathoz, ha a megerősített lap alapértelmezés szerint aktív. Megerősített nem hozza létre a virtuális hálózathoz, ha a hivatkozásra kattintva konfigurálja a megerősített kattinthat. Konfigurációs útmutatásért lásd: [konfigurálása megerősített](bastion-create-host-portal.md). Ha nem látja, akkor **megerősített** felsorolt, nem nyitott a betekintő portálon. Nyissa meg a portálon ez [előzetes hivatkozás](https://aka.ms/BastionHost).

    ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-rdp/bastion.png)

1. A megerősített lapon a felhasználónév és a virtuális gép jelszavát, majd kattintson **Connect**. A távoli asztali kapcsolaton keresztül megerősített a virtuális gép közvetlenül az Azure Portalon a 443-as portot és a megerősített szolgáltatások használatával (HTML5) protokollon keresztül nyílik meg.

    ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített – gyakori kérdések](bastion-faq.md)
