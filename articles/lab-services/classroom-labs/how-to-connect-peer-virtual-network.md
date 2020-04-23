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
ms.openlocfilehash: 6ed0d743f9b9cdc136b8f52f4d9d02583fc63eb9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870182"
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
> Amikor egy tesztkörnyezet-fiókkal társviszonyba kerül egy Azure virtuális hálózat létrehozásakor fontos megérteni, hogy a virtuális hálózat régiója hogyan befolyásolja az osztálytermi laborok létrehozását.  További információt a rendszergazdai útmutató [régiók\helyek című szakaszában talál.](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a tesztkörnyezet-fiók létrehozásakor

Az új [tesztkörnyezet-fiók létrehozása során](tutorial-setup-lab-account.md)kiválaszthatja a meglévő virtuális hálózatot, amely a Speciális lap Társ virtuális **hálózat** legördülő listájában jelenik **meg.**  A lista csak a tesztkörnyezet-fiókkal azonos régióban lévő virtuális hálózatokat jeleníti meg. A kijelölt virtuális hálózat csatlakozik (társviszonyban) a laborfiók alatt létrehozott laborok.  A módosítás után létrehozott laborokban található összes virtuális gép hozzáférhet a társviszonyt létesített virtuális hálózat erőforrásaihoz.

![Válassza a társhoz jelölő virtuális hálózatot](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Címtartomány

Lehetőség van arra is, hogy **címtartományt** biztosítson a virtuális gépekhez a laborok számára.  A **Címtartomány** tulajdonság csak akkor **érvényes,** ha a társ virtuális hálózat engedélyezve van a tesztkörnyezetben.  Ha a címtartomány meg van adva, a laborok ban a laborfiók ban lévő összes virtuális gép az adott címtartományban jön létre. A címtartománynak CIDR jelöléssel kell lennie (pl. 10.20.0.0/20), és nem lehet átfedésben a meglévő címtartományokkal.  Címtartomány megadásakor fontos átgondolni a létrehozandó *laborok* számát, és megadni egy címtartományt ennek megfelelően. A Lab Services laboronként legfeljebb 512 virtuális gépet feltételez.  Például egy ip-tartomány "/23" hozhat létre csak egy labor.  A "/21" tartomány lehetővé teszi négy laboratórium létrehozását.

Ha a **címtartomány** nincs megadva, a Lab Services az Azure által megadott alapértelmezett címtartományt fogja használni a virtuális hálózattal társviszonyba kerülő virtuális hálózat létrehozásakor.  A tartomány gyakran olyasmi, mint 10.x.0.0/16.  Ez az ip-tartomány átfedéséhez vezethet, ezért győződjön meg arról, hogy adja meg és a címtartománya a labor beállításait, vagy ellenőrizze a címkén a virtuális hálózat társviszonyban lévő.

## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után

Ugyanez a tulajdonság engedélyezhető a **Labor konfigurációs** lap a **Labor fiók** lap, ha nem állított be társhálózatot a laborfiók létrehozásakor. A beállítás módosítása csak a módosítás után létrehozott laborokra vonatkozik. Amint látható a képen, engedélyezheti vagy letilthatja **a társ virtuális hálózat** a laborok a laborfiókban.

![Virtuális kapcsolatlétesítés engedélyezése vagy letiltása a tesztkörnyezet létrehozása után](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Ha virtuális hálózatot választ a **Társ virtuális hálózati** mezőhöz, a Labor **készítője kiválaszthatja** a labor helyét beállítás le van tiltva. Ennek az az oka, hogy a laborfiókban lévő laboroknak ugyanabban a régióban kell lenniük, mint a laborfióknak, hogy a társ virtuális hálózat erőforrásaival kapcsolódhassanak.

> [!IMPORTANT]
> A társviszony-létesített virtuális hálózati beállítás csak a módosítás után létrehozott laborokra vonatkozik, a meglévő laborokra nem.

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [Megosztott képtár csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása tesztkörnyezet tulajdonosaként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet tűzfalbeállításainak megtekintése](how-to-configure-firewall-settings.md)
- [Tesztkörnyezet egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)
