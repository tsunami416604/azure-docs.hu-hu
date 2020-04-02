---
title: Csatlakozás társhálózathoz az Azure Lab Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a tesztkörnyezet hálózatát egy másik hálózattal társként. Például csatlakoztassa a helyszíni iskolai/egyetemi hálózatát a Lab azure-beli virtuális hálózatával.
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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 224526efc2152e0b788c5cbc7f3bd60bb3363c1a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545712"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>A labor hálózatának csatlakoztatása egyenrangú virtuális hálózattal az Azure Lab Servicesben 
Ez a cikk a laborok hálózatának egy másik hálózattal való társviszony-létesítésével kapcsolatos információkat tartalmaz. 

## <a name="overview"></a>Áttekintés
A virtuális hálózati társviszony-létesítés lehetővé teszi az Azure virtuális hálózatainak zökkenőmentes összekapcsolását. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A társviszony-létesített virtuális hálózatok virtuális gépei közötti forgalom a Microsoft gerinchálózati infrastruktúráján keresztül történik, hasonlóan a forgalomhoz ugyanebben a virtuális hálózatban lévő virtuális gépek között, csak magánjellegű IP-címeken keresztül. További információt a [Virtuális hálózati társviszony-létesítés](../../virtual-network/virtual-network-peering-overview.md)című témakörben talál.

Előfordulhat, hogy a labor hálózatát egy társvirtuális hálózattal kell csatlakoztatnia bizonyos esetekben, beleértve a következőket is:

- A laborban lévő virtuális gépek olyan szoftverrel rendelkeznek, amely a licencbeszerzéshez helyszíni licenckiszolgálókhoz csatlakozik
- A laborban lévő virtuális gépeknek hozzá kell férniük az egyetemi hálózati megosztásokon lévő adatkészletekhez (vagy bármely más fájlhoz). 

Bizonyos helyszíni hálózatok az [ExpressRoute](../../expressroute/expressroute-introduction.md) vagy a Virtual Network Gateway segítségével kapcsolódnak az Azure virtuális [hálózathoz.](../../vpn-gateway/vpn-gateway-about-vpngateways.md) Ezeket a szolgáltatásokat az Azure Lab Servicesen kívül kell beállítani. Ha többet szeretne tudni arról, hogy miként csatlakozhat helyszíni hálózathoz az ExpressRoute használatával az Azure-hoz, olvassa el az [ExpressRoute áttekintése című témakört.](../../expressroute/expressroute-introduction.md) A virtuális hálózati átjárót használó helyszíni kapcsolat esetén az átjárónak, a megadott virtuális hálózatnak és a tesztkörnyezet-fióknak ugyanabban a régióban kell lennie.

> [!NOTE]
> Amikor egy tesztkörnyezet-fiókkal társviszonyba kerül egy Azure virtuális hálózat létrehozásakor fontos megérteni, hogy a virtuális hálózat régiója hogyan befolyásolja az osztálytermi laborok létrehozását.  További információt a rendszergazdai útmutató [régiók\helyek](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)című szakasza tartalmaz.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a tesztkörnyezet-fiók létrehozásakor
Az új tesztkörnyezet-fiók létrehozása során kiválaszthatja a meglévő virtuális hálózatot, amely a Speciális lap **Társ virtuális hálózat** legördülő listájában jelenik **meg.** A kijelölt virtuális hálózat csatlakozik (társviszonyban) a laborfiók alatt létrehozott laborok. A módosítás után létrehozott laborokban található összes virtuális gép hozzáférhet a társviszonyt létesített virtuális hálózat erőforrásaihoz. 

Van is egy rendelkezés, amely a hálózati gépek **címtartományát** a laborok számára. Ha a címtartomány meg van adva, a laborok ban a laborfiók ban lévő összes virtuális gép az adott címtartományban jön létre. A címtartománynak CIDR jelöléssel kell lennie (pl. 10.20.0.0/20), és nem lehet átfedésben a meglévő címtartományokkal. Címtartomány megadásakor fontos átgondolni a laborokban létrehozandó virtuális gépek számát, és megadni egy címtartományt ennek befogadására. Egy adott tartományban, a laborok száma tud befogadni jelenik meg.

![Válassza a társhoz jelölő virtuális hálózatot](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> A tesztkörnyezet-fiók létrehozásának részletes, részletes, részletes útmutatóját a [Laborfiók beállítása című témakörben](tutorial-setup-lab-account.md) találja.


## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után
Ugyanez a tulajdonság engedélyezhető a **Labor konfigurációs** lap a **Labor fiók** lap, ha nem állított be társhálózatot a laborfiók létrehozásakor. A beállítás módosítása csak a módosítás után létrehozott laborokra vonatkozik. Amint látható a képen, engedélyezheti vagy letilthatja **a társ virtuális hálózat** a laborok a laborfiókban. 

![Virtuális kapcsolatlétesítés engedélyezése vagy letiltása a tesztkörnyezet létrehozása után](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Ha virtuális hálózatot választ a **Társ virtuális hálózati** mezőhöz, a Labor **készítője kiválaszthatja** a labor helyét beállítás le van tiltva. Ez azért van, mert a laborfiókban lévő laborok ugyanabban a régióban kell lennie, mint a laborfiók számára, hogy csatlakozzanak a társ virtuális hálózat erőforrásaival. 

> [!IMPORTANT]
> Ez a beállítás csak a módosítás után létrehozott laborokra vonatkozik, a meglévő laborokra nem. 


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [Megosztott képtár csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása tesztkörnyezet tulajdonosaként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet tűzfalbeállításainak megtekintése](how-to-configure-firewall-settings.md)
- [Tesztkörnyezet egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)
