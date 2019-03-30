---
title: Csatlakozás az Azure Lab Services társ hálózathoz |} A Microsoft Docs
description: Ismerje meg, hogy a tesztlabor-hálózat csatlakoztatása egy társ, egy másik hálózattal. Például csatlakoztassa a helyszíni iskola/Egyetem hálózati tesztkörnyezet virtuális hálózattal, az Azure-ban.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653072"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>A tesztkörnyezet hálózati csatlakozás és a társ virtuális hálózat az Azure Lab Services 
Ez a cikk ismerteti, a tesztkörnyezetek hálózat és egy másik hálózat közötti társviszony. 

## <a name="overview"></a>Áttekintés
Virtuális hálózatok közötti társviszony lehetővé teszi, hogy zökkenőmentesen csatlakoztathatja az Azure virtuális hálózatok. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat továbbítja a rendszer a Microsoft gerincinfrastruktúráján keresztül, lényegében ugyanúgy van a virtuális hálózaton keresztül a magánhálózati IP-címek csak a virtuális gépek között. További információkért lásd: [virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md).

Szükség lehet a tesztkörnyezet hálózati csatlakozás és a társ virtuális hálózat bizonyos esetekben a következő azokat is beleértve:

- A lab-ben a virtuális gépek rendelkezik, amely kapcsolódik a helyszíni licenckiszolgálókat licenc beszerzésére szoftver
- A lab-ben a virtuális gépek university a hálózati megosztásokon kell az adatkészletek hozzáférést (vagy bármely más fájlok). 

Bizonyos a helyszíni hálózat csatlakozik vagy Azure virtuális hálózaton keresztül [ExpressRoute](../../expressroute/expressroute-introduction.md) vagy [virtuális hálózati átjáró](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Ezek a szolgáltatások az Azure Lab Services kívül kell állítani. Egy helyszíni hálózat csatlakoztatása az Azure ExpressRoute használatával kapcsolatos további információkért lásd: [az ExpressRoute áttekintése]) (.. /expressroute/expressroute-Introduction.MD). Egy virtuális hálózati átjárót, az átjárót használó helyszíni kapcsolatok virtuális hálózati megadott, és a tesztlabor összes kell ugyanabban a régióban.


## <a name="configure-at-the-time-of-lab-account-creation"></a>A labor létrehozása idején konfigurálása
Új labor-fiók létrehozása során választhat egy meglévő virtuális hálózatot, amely megmutatja, a **társ virtuális hálózatnak** legördülő listából. A kiválasztott virtuális hálózat connected(peered) a Labs szolgáltatásban létrehozott tesztkörnyezet fiók alatt. A virtuális gépek laborokban, amelyek akkor jönnek létre a elvégzése után a változás az erőforrásokhoz való hozzáférés kellene a virtuális társhálózatban működő. 

![Válassza ki a virtuális hálózat társviszonyba állítása](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Részletes, lépésenkénti útmutatót tesztkörnyezetfiók létrehozásához, lásd: [tesztkörnyezetfiók beállítása](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>A labor létrehozása után konfigurálása
Ugyanahhoz a tulajdonsághoz engedélyezhető a **Labs konfigurációs** lapján a **labor fiók** lapon, ha nem adott meg egyenrangú hálózat létrehozása a lab-fiók létrehozása idején. Ezzel a beállítással végrehajtott módosítása csak a váltás után létrehozott labs vonatkozik.

![Engedélyezheti vagy tilthatja le a virtuális hálózatok közötti társviszonyt a labor létrehozása után](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Ahogy az képen látható, engedélyezése vagy letiltása **társ virtuális hálózatnak** a labs labor-fiókban. 

> [!IMPORTANT]
> A beállítás ezen módosítása csak a Labs szolgáltatásban létrehozott után a változás történik, a meglévő labs, nem vonatkozik. 



## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)

