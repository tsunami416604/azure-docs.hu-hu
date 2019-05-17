---
title: Útmutató az Azure Files-NetApp hálózati tervezése |} A Microsoft Docs
description: Ismerteti iránymutatásokat tartalmaz, amely egy hatékony hálózati architektúra tervezése az Azure Files-NetApp használatával segítségével.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 2afd5f0a574fd15c4327b141901d2651dbe2b9e5
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524235"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Az Azure NetApp Files hálózattervezési irányelvei

Bármely alkalmazás infrastruktúra tervezésekor fontos elemei a hálózati architektúra tervezése. Ez a cikk segít kialakítani a egy érvényes hálózati architektúra a számítási feladatok számára, hogy az Azure Files-NetApp sokoldalú képességeit.

Az Azure NetApp fájlok kötetek úgy tervezték, hogy egy speciális célú nevű alhálózat szereplő [alhálózati delegált](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) Azure virtuális hálózaton belül. Ezért elérheti a kötetek közvetlenül a vnetről, a társviszonyban lévő virtuális hálózatok ugyanabban a régióban vagy a helyszínről keresztül a virtuális hálózati átjáró (ExpressRoute vagy VPN-átjáró) szükség szerint. Az alhálózat az Azure Files-NetApp van kijelölve, és nincs más Azure-szolgáltatások és az internet kapcsolat.

## <a name="considerations"></a>Megfontolandó szempontok  

NetApp fájlokat az Azure-hálózat tervezése során meg kell ismernie néhány megfontolandó szempont.

### <a name="constraints"></a>Megkötések

Az alábbi funkciók jelenleg nem támogatott a NetApp Azure-fájlok: 

* Hálózati biztonsági csoportok (NSG) alhálózaton
* Felhasználó által megadott útvonalak (udr-EK) a következő ugrás pedig Azure NetApp fájlok alhálózat
* Az Azure Files-NetApp illesztőn Azure szabályzatok (például egyéni elnevezési szabályzat)
* Az Azure Files-NetApp forgalom terheléselosztók

A következő hálózati korlátozások vonatkoznak az Azure Files-NetApp:

* A szám az IP-címek a virtuális hálózaton (tartalmazza a virtuális társhálózatba tartozó virtuális hálózatok) egy kötetet a virtuális hálózat csatlakoztatása legfeljebb 1000.
* Az egyes Azure virtuális hálózatok (VNet) csak egy alhálózatot az Azure Files-NetApp delegálható.


### <a name="supported-network-topologies"></a>A támogatott hálózati topológiák

A következő táblázat ismerteti a NetApp fájlokat az Azure által támogatott hálózati topológiák.  A nem támogatott topológiák megoldásai is ismerteti. 

|    Topológiák    |    támogatott    |     Áthidaló megoldás    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    A helyi virtuális hálózat kötetre kapcsolat    |    Igen    |         |
|    Kapcsolat a kötethez társított virtuális hálózaton (ugyanabban a régióban)    |    Igen    |         |
|    Kötethez társított virtuális hálózaton (régió vagy globális társviszony-létesítés) közötti kapcsolat    |    Nem    |    Egyik sem    |
|    Kapcsolat egy kötetre keresztül ExpressRoute-átjáró    |    Igen    |         |
|    Kapcsolat a helyszíni és a kötet a küllő virtuális hálózat ExpressRoute-átjáró és a virtuális hálózatok közötti társviszony az átjárói átvitel    |    Nem    |    Hozzon létre egy meghatalmazott alhálózatot az agyi virtuális hálózat (az Azure VNet-átjáró)    |
|    Kapcsolat a helyszíni és a egy küllő virtuális hálózatok közötti VPN-átjárón keresztül a kötet    |    Igen    |         |
|    Kapcsolat a helyszíni és a kötet a küllő virtuális hálózatok közötti VPN-átjáró és a virtuális hálózatok közötti társviszony az átjárói átvitel    |    Igen    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuális hálózatot az Azure Files-NetApp kötetek

Ez a szakasz ismerteti, amelyek segítenek a virtuális hálózat megtervezése.

### <a name="azure-virtual-networks"></a>Azure-alapú virtuális hálózatok

Mielőtt üzembe helyezés az Azure Files-NetApp kötet esetében meg kell hozzon létre egy Azure virtuális hálózaton (VNet), vagy használjon egy meglévőt az előfizetésében. A virtuális hálózat határozza meg, a hálózathatár a kötet.  Virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: a [Azure Virtual Network dokumentációja](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Alhálózatok

Alhálózatok a virtuális hálózat oszthatja be különálló címterekkel, amelyekhez használhatók lesznek az újjáépítés az őket az Azure-erőforrások.  Az Azure NetApp fájlok kötetek találhatók az olyan speciális célú nevű alhálózat [alhálózati delegált](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Alhálózat delegálás révén a kifejezett engedélyek az Azure NetApp Files szolgáltatásba szolgáltatásspecifikus-erőforrások létrehozása az alhálózat.  Egy egyedi azonosítóvá a szolgáltatás telepítéséhez használja. Ebben az esetben egy hálózati adapter az Azure Files-NetApp kapcsolat jön létre.

Ha egy új virtuális hálózatot használ, hozzon létre egy alhálózatot, és delegálja az alhálózaton található utasításokat követve az Azure Files-NetApp [delegálása az Azure Files-NetApp alhálózat](azure-netapp-files-delegate-subnet.md). Delegálhatja egy meglévő üres alhálózatot, amely már nem delegált más szolgáltatásokhoz.

Ha a virtuális hálózatok közötti társviszonyban van egy másik virtuális hálózattal, a virtuális hálózat címtere nem terjeszthető ki. Éppen ezért az új delegált alhálózat kell létrehozni a virtuális hálózati címtéren belül. Ha szeretné bővíteni a címtér, törölnie kell a virtuális hálózatok közötti társviszony-létesítés a címtér bővítése előtt.

### <a name="udrs-and-nsgs"></a>Udr-EK és NSG-kkel

Hálózati biztonsági csoportok (NSG) egy következő ugrási Azure NetApp Files, meghatalmazott alhálózatok nem használható. Hasonlóképpen felhasználó által megadott útvonalak (udr-EK) még nem támogatottak. 

Áthidaló megoldásként alkalmazhat az más alhálózatokra, amelyek akár vagy megtagadhatja a forgalmat az Azure NetApp Files, meghatalmazott alhálózat NSG-k.  

## <a name="azure-native-environments"></a>Natív Azure-környezetek

A következő ábra szemlélteti egy natív Azure-környezetben:

![Az Azure natív hálózati környezet](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Helyi virtuális hálózat

Alapszintű forgatókönyv létrehozása vagy csatlakozás az Azure Files-NetApp köteten az azonos virtuális hálózaton található virtuális gép (VM) a. Virtuális hálózat 2. a fenti ábrán 1. kötet jön létre egy meghatalmazott alhálózatot, és az alapértelmezett alhálózat csatlakoztathatók a virtuális gép 1.

### <a name="vnet-peering"></a>VNet-társviszony

Ha további virtuális hálózatok ugyanabban a régióban, amely a többi összes erőforrásokhoz való hozzáférésre van szükségük, a virtuális hálózatok hitelesítéssel lehet csatlakozni [virtuális hálózatok közötti társviszony](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) biztonságos kapcsolat az Azure infrastruktúráján keresztül. 

A fenti ábrán fontolja meg a virtuális hálózat 2. és 3 virtuális hálózathoz. Ha szeretne csatlakozni a virtuális gép 2 és a kötet 2 virtuális gép 1 vagy 2. virtuális gép csatlakoznia kell a virtuális gép 1 vagy az 1. kötet, majd, engedélyeznie kell a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti 2 és 3 virtuális hálózat között. 

Továbbá vegye figyelembe, hogy ez a forgatókönyv, ahol az 1 virtuális hálózat társviszonyban áll-e virtuális hálózat 2. és 2 virtuális hálózat társviszonyban áll-e az azonos régióban található virtuális hálózatok közötti 3. A virtuális hálózatok közötti 1 erőforrásait a 2. virtuális hálózati erőforrások csatlakozni, de nem tud kapcsolódni a virtuális hálózatok közötti 3, erőforrás, ha a virtuális hálózat 1. és 3 virtuális hálózatok társviszonyban állnak. 

A fenti ábrán, bár a virtuális gép 3 csatlakozhat az 1. kötet, 4 virtuális gép nem tud kapcsolódni kötet 2.  A hiba oka, hogy a küllő virtuális hálózatok nem társviszonyban állnak, és _tranzit útválasztás nem támogatott virtuális hálózatok közötti társviszony-létesítésen keresztül_.

## <a name="hybrid-environments"></a>Hibrid környezetek

A következő ábra szemlélteti a hibrid környezetben: 

![Hibrid hálózati környezetben](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

A hibrid forgatókönyvek esetében az alkalmazások a helyszíni adatközpontjaiban kell az Azure-erőforrásokhoz való hozzáférés.  Ez a helyzet az Adatközpont bővítéséhez az Azure-ba, vagy a natív Azure-szolgáltatások használatára szeretné-e, vagy vész-helyreállítási. Lásd: [tervezésénél VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) több erőforrást a helyszíni erőforrásokhoz az Azure site-to-site VPN-en vagy egy ExpressRoute-ban történő használatáról.

A hibrid küllős topológiában az agyi virtuális hálózat az Azure-ban a helyszíni hálózathoz való csatlakozási lehetőségek központi helye funkcionál. A küllő virtuális hálózatok társviszonyban állnak az agyhoz, és azok számítási feladatok elkülönítésére használhatók.

Attól függően, a konfiguráció. A helyszíni erőforrások csatlakozhat a küllős topológia az erőforrásokhoz.

A topológia a fent a helyszíni hálózathoz csatlakoztatva van egy agyi virtuális hálózat az Azure-ban, és nincsenek 2 küllő virtuális hálózatok társviszonyban az agyi virtuális hálózat.  Ebben a forgatókönyvben az Azure Files-NetApp kötetek esetében támogatott kapcsolati lehetőségek a következők:

* A helyszíni erőforrások a virtuális gép 1. és 2. virtuális gép 1. kötet az agyban protokollon keresztül is kapcsolódhatnak site-to-site VPN-en vagy az ExpressRoute. 
* Virtuális gép 1. és 2. virtuális gép a helyszíni erőforrásokhoz csatlakozhat a kötet 2 vagy 3 kötet.
* Az agyi virtuális gép 3 virtuális hálózat kapcsolódhat kötet küllő virtuális hálózatok közötti 1 a 2. és 3 kötet a küllő virtuális hálózat 2.
* 4 virtuális gép küllő virtuális hálózatok közötti 1 és 5. küllő virtuális hálózat 2 virtuális gép csatlakozhat 1. kötet az agyi virtuális hálózat.

4 virtuális gép küllő virtuális hálózatok közötti 1 kötet 3 a küllő virtuális hálózat 2 nem lehet csatlakozni. Ezenkívül küllő virtuális gép 5 VNet2 nem lehet csatlakozni kötet 2 a küllő virtuális hálózat 1. Ez így, mert nem a küllő virtuális hálózatok társviszonyba és _tranzit útválasztás nem támogatott virtuális hálózatok közötti társviszony-létesítésen keresztül_.

## <a name="next-steps"></a>További lépések

[Az Azure Files-NetApp alhálózat delegálása](azure-netapp-files-delegate-subnet.md)