---
title: Csatlakozás egy Linux virtuális gép Azure megerősített |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan kapcsolódhat a Linux rendszerű virtuális gép Azure megerősített.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 572043598c71a400e154c5c2e9e6c2f1e9b4ab49
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191797"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Csatlakozás Azure megerősített (előzetes verzió) használata Linux rendszerű virtuális géphez SSH-val

Ez a cikk bemutatja, hogyan, biztonságos és zökkenőmentes SSH a Linux rendszerű virtuális gépek az Azure-beli virtuális hálózathoz. A virtuális gépekhez közvetlenül az Azure Portalon csatlakozhat. Az Azure Bastion használatával a virtuális gépeken nincs szükség ügyfélre, ügynökre és további szoftverekre sem. Azure megerősített kapcsolatos további információkért lásd: a [áttekintése](bastion-overview.md).

Azure megerősített használatával egy Linux rendszerű virtuális gép SSH-val csatlakozhat. A hitelesítéshez felhasználónév/jelszó és SSH-kulcsokat is használható. Csatlakozhat a virtuális gép SSH-kulcsokkal való használatával:

* Manuálisan adja meg a titkos kulcs
* Egy fájl, amely tartalmazza a titkos kulcs adataival

A titkos SSH-kulcs, amely a következővel kezdődik: formátumban kell lennie `"-----BEGIN RSA PRIVATE KEY-----"` és végződik `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy rendelkezik-e beállítva, amelyben a virtuális gép található a virtuális hálózat Azure bástyagazdagép. További információkért lásd: [hozzon létre egy Azure bástyagazdagép](bastion-create-host-portal.md). A megerősített szolgáltatás kiépítése, és a virtuális hálózaton üzembe helyezett, miután a virtuális hálózatban lévő bármely virtuális Géphez való csatlakozáshoz használhatja. Ebben az előzetes verzióban való csatlakozáshoz, megerősített használatakor azt feltételezi, hogy egy Windows virtuális gép és az SSH a Linux rendszerű virtuális géphez való kapcsolódásra való kapcsolódáshoz használ az RDP.

Annak érdekében, hogy a kapcsolat létrehozása a következő szerepkörök szükségesek:

* A virtuális gépen olvasói szerepköre
* A hálózati adapteren a virtuális gép magánhálózati IP-Címmel rendelkező olvasói szerepköre
* Az Azure megerősített erőforráson olvasói szerepköre

## <a name="username"></a>Csatlakozás: Felhasználónév és jelszó használatával


1. Az a [az Azure portal](https://aka.ms/BastionHost) a megerősített előzetes verzió, lépjen a virtuális gép, amelyhez csatlakozni kíván, majd kattintson **Connect**. A virtuális Gépet kell Linux rendszerű virtuális gép, ha az SSH-kapcsolatot használ.
1. Connect gombra kattint, az oldalsó sáv megjelenik, amely három lap található – RDP, SSH és a megerősített. Megerősített lett üzembe helyezve, a virtuális hálózathoz, ha a megerősített lap alapértelmezés szerint aktív. Ha a virtuális hálózat a megerősített töltötte, tekintse meg [konfigurálása megerősített](bastion-create-host-portal.md). Ha nem látja, akkor **megerősített** felsorolt, nem nyitott a betekintő portálon. Nyissa meg a portál használatával [ezt a hivatkozást](https://aka.ms/BastionHost).

      ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-ssh/bastion.png)

1. Adja meg a felhasználónevet és jelszót az SSH-hoz a virtuális géphez.
1. Kattintson a **Connect** gomb a kulcs megadása után.

## <a name="privatekey"></a>Csatlakozás: Manuálisan adja meg a titkos kulcsot

1. Az a [az Azure portal](https://aka.ms/BastionHost) a megerősített előzetes verzió, lépjen a virtuális gép, amelyhez csatlakozni kíván, majd kattintson **Connect**. A virtuális Gépet kell Linux rendszerű virtuális gép, ha az SSH-kapcsolatot használ.
1. Connect gombra kattint, az oldalsó sáv megjelenik, amely három lap található – RDP, SSH és a megerősített. Megerősített lett üzembe helyezve, a virtuális hálózathoz, ha a megerősített lap alapértelmezés szerint aktív. Ha a virtuális hálózat a megerősített töltötte, tekintse meg [konfigurálása megerősített](bastion-create-host-portal.md). Ha nem látja, akkor **megerősített** felsorolt, nem nyitott a betekintő portálon. Nyissa meg a portál használatával [ezt a hivatkozást](https://aka.ms/BastionHost).

      ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-ssh/bastion.png)

1. Adja meg a felhasználónevet, és válassza ki **SSH titkos kulcs**.
1. Adja meg a titkos kulcsot a text területre **SSH titkos kulcs** (vagy beillesztheti közvetlenül).
1. Kattintson a **Connect** gomb a kulcs megadása után.

## <a name="ssh"></a>Csatlakozás: A titkos kulcsfájl használata

1. Az a [az Azure portal](https://aka.ms/BastionHost) a megerősített előzetes verzió, lépjen a virtuális gép, amelyhez csatlakozni kíván, majd kattintson **Connect**. A virtuális Gépet kell Linux rendszerű virtuális gép, ha az SSH-kapcsolatot használ.

    ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-ssh/connect.png)

1. Connect gombra kattint, az oldalsó sáv megjelenik, amely három lap található – RDP, SSH és a megerősített. Megerősített lett üzembe helyezve, a virtuális hálózathoz, ha a megerősített lap alapértelmezés szerint aktív. Ha a virtuális hálózat a megerősített töltötte, tekintse meg [konfigurálása megerősített](bastion-create-host-portal.md). Ha nem látja, akkor **megerősített** felsorolt, nem nyitott a betekintő portálon. Nyissa meg a portál használatával [ezt a hivatkozást](https://aka.ms/BastionHost).

    ![Virtuális gép csatlakoztatása](./media/bastion-connect-vm-ssh/bastion.png)

1. Adja meg a felhasználónevet, és válassza ki **SSH titkos kulcs helyi fájlból**.
1. Kattintson a **Tallózás** gombra (mappa ikon a helyi fájl).
1. Keresse meg a fájlt, majd kattintson a **nyílt**.
1. Kattintson a **Connect** a virtuális Géphez való csatlakozáshoz. Kattintva Connect, az ssh-KAPCSOLATOT a virtuális gép közvetlenül megnyílik az Azure Portalon. Ez a kapcsolat nem keresztül HTML5 használatával a megerősített szolgáltatás a 443-as porton keresztül a magánhálózati IP-címét a virtuális gép.

## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített – gyakori kérdések](bastion-faq.md)