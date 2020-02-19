---
title: Kapcsolódás a Azure Lab Services társ hálózatához | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a labor-hálózat egy másik hálózathoz társként. Például összekapcsolhatja a helyszíni iskolai vagy egyetemi hálózatot a labor virtuális hálózatával az Azure-ban.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443190"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>A labor hálózatának összekötése egy társ virtuális hálózattal Azure Lab Services 
Ez a cikk a labor-hálózat más hálózattal való társításával kapcsolatos információkat tartalmaz. 

## <a name="overview"></a>Áttekintés
A virtuális hálózatok közötti kapcsolat lehetővé teszi az Azure-beli virtuális hálózatok zökkenőmentes összekapcsolását. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális gépek közötti forgalmat a rendszer a Microsoft gerinc-infrastruktúrán keresztül irányítja át, hasonlóan a forgalomhoz, mint az azonos virtuális hálózatban lévő virtuális gépek között, a magánhálózati IP-címeken keresztül. További információ: [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md).

Előfordulhat, hogy a tesztkörnyezet hálózatát egy társ virtuális hálózattal kell összekötnie bizonyos helyzetekben, többek között az alábbiakat:

- A laborban található virtuális gépek olyan szoftverrel rendelkeznek, amely a licencek megvásárlásához csatlakozik a helyszíni licenckiszolgálók számára
- A laborban található virtuális gépeknek hozzá kell férniük az adatkészletekhez (vagy bármely más fájlhoz) az egyetemi hálózati megosztásokon. 

Bizonyos helyszíni hálózatok az Azure-Virtual Networkhoz kapcsolódnak [ExpressRoute](../../expressroute/expressroute-introduction.md) vagy [Virtual Network átjárón](../../vpn-gateway/vpn-gateway-about-vpngateways.md)keresztül. Ezeket a szolgáltatásokat Azure Lab Serviceson kívül kell beállítani. Ha szeretne többet megtudni arról, hogyan csatlakoztathat helyszíni hálózatot az Azure-hoz az ExpressRoute használatával, tekintse meg az [ExpressRoute áttekintése](../../expressroute/expressroute-introduction.md)című témakört. Virtual Network átjárót használó helyszíni kapcsolat esetén az átjárónak, a megadott virtuális hálózatnak és a labor-fióknak ugyanabban a régióban kell lennie.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a labor-fiók létrehozásakor
Az új Labor-fiók létrehozása során kiválaszthat egy meglévő virtuális hálózatot, amely a **speciális** lapon, a **társ virtuális hálózat** legördülő listában látható. A kiválasztott virtuális hálózat csatlakoztatva van a labor-fiókban létrehozott laborokhoz. Az összes olyan virtuális gép, amely a módosítás létrehozása után jön létre, hozzáférhet a virtuális hálózatban lévő erőforrásokhoz. 

![VNet kiválasztása társként](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> A labor-fiókok létrehozásával kapcsolatos részletes útmutatásért lásd: [labor-fiók beállítása](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után
Ugyanezt a tulajdonságot engedélyezheti a **labor-fiók** lap **Labs-konfiguráció** lapján, ha nem állított be egy társ hálózatot a labor-fiók létrehozásakor. Az erre a beállításra végzett módosítás csak a módosítás után létrehozott laborokra vonatkozik. Ahogy a képen is látható, a labor fiókban engedélyezheti vagy letilthatja a Labs **társ virtuális hálózatát** . 

![VNet-társítás engedélyezése vagy letiltása a tesztkörnyezet létrehozása után](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Ha kijelöl egy virtuális hálózatot a **társ virtuális hálózat** mezőhöz, akkor a **tesztkörnyezet-létrehozó engedélyezése a laborban hely** beállítás le van tiltva. Ez azért van, mert a labor-fiókban lévő laboroknak ugyanabban a régióban kell lenniük, mint a társ virtuális hálózatban lévő erőforrásokhoz való kapcsolódáshoz. 

> [!IMPORTANT]
> Ez a beállítás csak a módosítás után létrehozott laborokra vonatkozik, nem a meglévő laborokra. 


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Tesztkörnyezet helyének engedélyezése a labor Creator számára](allow-lab-creator-pick-lab-location.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)