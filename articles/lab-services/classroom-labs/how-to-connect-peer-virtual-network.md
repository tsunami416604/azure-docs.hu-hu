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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 8d8f2c747a4bc0ab2119c92e61188e3c57f2b212
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118362"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>A labor hálózatának összekötése egy társ virtuális hálózattal Azure Lab Services

Ez a cikk a labor-hálózat más hálózattal való társításával kapcsolatos információkat tartalmaz.

## <a name="overview"></a>Áttekintés

A virtuális hálózatok közötti kapcsolat lehetővé teszi az Azure-beli virtuális hálózatok zökkenőmentes összekapcsolását. A társviszony kialakítását követően a virtuális hálózatok a csatlakozás szempontjából egyetlen hálózatnak látszanak. A virtuális gépek közötti forgalmat a rendszer a Microsoft gerinc-infrastruktúrán keresztül irányítja át, hasonlóan a forgalomhoz, mint az azonos virtuális hálózatban lévő virtuális gépek között, a magánhálózati IP-címeken keresztül. További információ: [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md).

Előfordulhat, hogy a tesztkörnyezet hálózatát egy társ virtuális hálózattal kell összekötnie bizonyos helyzetekben, többek között az alábbiakat:

- A laborban található virtuális gépek olyan szoftverekkel rendelkeznek, amelyek a licencek megvásárlásához kapcsolódnak a helyszíni licenckiszolgálóra.
- A laborban található virtuális gépeknek hozzá kell férniük az adatkészletekhez (vagy bármely más fájlhoz) az egyetemi hálózati megosztásokon.

Bizonyos helyszíni hálózatok az Azure-Virtual Networkhoz kapcsolódnak [ExpressRoute](../../expressroute/expressroute-introduction.md) vagy [Virtual Network átjárón](../../vpn-gateway/vpn-gateway-about-vpngateways.md)keresztül. Ezeket a szolgáltatásokat Azure Lab Serviceson kívül kell beállítani. Ha szeretne többet megtudni arról, hogyan csatlakoztathat helyszíni hálózatot az Azure-hoz az ExpressRoute használatával, tekintse meg az [ExpressRoute áttekintése](../../expressroute/expressroute-introduction.md)című témakört. Virtual Network átjárót használó helyszíni kapcsolat esetén az átjárónak, a megadott virtuális hálózatnak és a labor-fióknak ugyanabban a régióban kell lennie.

> [!NOTE]
> Ha olyan Azure-Virtual Network hoz létre, amely egy labor-fiókkal lesz társítva, fontos tisztában lennie azzal, hogy a virtuális hálózat régiója milyen hatással van az osztályterem Labs létrehozására.  További információkért tekintse meg a rendszergazdai útmutató szakaszát a [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)webhelyen.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurálás a labor-fiók létrehozásakor

Az új [labor-fiók létrehozása](tutorial-setup-lab-account.md)során kiválaszthat egy meglévő virtuális hálózatot, amely a **speciális** lapon, a **társ virtuális hálózat** legördülő listában látható.  A lista csak a labor-fiókkal megegyező régióban lévő virtuális hálózatokat jeleníti meg. A kiválasztott virtuális hálózat csatlakoztatva van a labor-fiókban létrehozott laborokhoz.  A jelen módosítás után létrehozott Labs-beli virtuális gépek hozzáférhetnek a virtuális hálózatban lévő erőforrásokhoz.

![VNet kiválasztása társként](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Címtartomány

Emellett lehetőség van arra is, hogy **címtartományt** biztosítson a Labs virtuális gépei számára.  A **címtartomány** tulajdonság csak akkor érvényes, ha a **társ virtuális hálózat** engedélyezve van a laborban.  Ha a címtartomány meg van adni, a rendszer a labor fiókban található laborok alatt lévő összes virtuális gépet létrehozza az adott címtartományból. A címtartomány CIDR (például 10.20.0.0/20) kell legyen, és ne legyen átfedésben a meglévő címtartományok.  Címtartomány megadásakor fontos meggondolni a létrehozandó *laborok* számát, és biztosítania kell a címtartomány megadását. A labor-szolgáltatások legfeljebb 512 virtuális gépet feltételeznek laborban.  Egy "/23" nevű IP-címtartomány például csak egy labort hozhat létre.  A (z) "/21" tartománya négy labor létrehozását teszi lehetővé.

Ha nincs megadva a **címtartomány** , a labor Services az Azure által megadott alapértelmezett címtartományt fogja használni, amikor létrehozza a virtuális hálózatot a virtuális hálózattal.  A tartomány gyakran hasonló, mint 10. x. 0.0/16.  Ez az IP-címtartomány átfedését eredményezheti, ezért ügyeljen arra, hogy adjon meg egy címtartományt a tesztkörnyezet beállításai között, vagy ellenőrizze, hogy a virtuális hálózat milyen címtartományt használ.

## <a name="configure-after-the-lab-is-created"></a>Konfigurálás a tesztkörnyezet létrehozása után

Ugyanezt a tulajdonságot engedélyezheti a **labor-fiók** lap **Labs-konfiguráció** lapján, ha nem állított be egy társ hálózatot a labor-fiók létrehozásakor. Az erre a beállításra végzett módosítás csak a módosítás után létrehozott laborokra vonatkozik. Ahogy a képen is látható, a labor fiókban engedélyezheti vagy letilthatja a Labs **társ virtuális hálózatát** .

![VNet-társítás engedélyezése vagy letiltása a tesztkörnyezet létrehozása után](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Ha kijelöl egy virtuális hálózatot a **társ virtuális hálózat** mezőhöz, akkor a **tesztkörnyezet-létrehozó engedélyezése a laborban hely** beállítás le van tiltva. Ennek az az oka, hogy a labor-fiókban lévő laboroknak ugyanabban a régióban kell lenniük, mint a társ virtuális hálózatban lévő erőforrásokhoz való kapcsolódáshoz szükséges labor-fióknak.

> [!IMPORTANT]
> A társ virtuális hálózat beállítás csak a módosítás után létrehozott laborokra vonatkozik, nem a meglévő Labs-re.

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)
