---
title: Az Azure NetApp Files hálózattervezés irányelvei | Microsoft dokumentumok
description: Ez az irányelvek, amelyek segítségével hatékony hálózati architektúra az Azure NetApp Files használatával.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
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
ms.openlocfilehash: 12be766f36a0901079a5a26f20ea7dacc75268de
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667871"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Az Azure NetApp Files hálózattervezési irányelvei

A hálózati architektúra tervezése az alkalmazásinfrastruktúra tervezésének kulcsfontosságú eleme. Ez a cikk segít hatékony hálózati architektúrát tervezni a számítási feladatok számára, hogy kihasználhassa az Azure NetApp Files gazdag képességeit.

Az Azure NetApp-fájlok kötetei úgy vannak kialakítva, hogy egy speciális célú alhálózatban, az Azure virtuális hálózaton belül [delegált alhálózatban](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) legyenek. Ezért a kötetek közvetlenül a virtuális hálózatról, a társviszony-létesített virtuális hálózatok ugyanabban a régióban, vagy a helyszíni egy virtuális hálózati átjáró (ExpressRoute vagy VPN-átjáró) szükség szerint. Az alhálózat az Azure NetApp-fájlok számára van elrendelve, és nincs kapcsolat más Azure-szolgáltatásokkal vagy az internettel.

## <a name="considerations"></a>Megfontolandó szempontok  

Az Azure NetApp Files hálózat tervezésekor érdemes megvizsgálni a szempontokat.

### <a name="constraints"></a>Korlátozások

Az alábbi funkciók jelenleg nem támogatottak az Azure NetApp-fájlok esetében: 

* A delegált alhálózatra alkalmazott hálózati biztonsági csoportok (NSG-k)
* Felhasználó által definiált útvonalak (UDRs) címelőtaggal Azure NetApp-fájlok alhálózataként
* Azure-szabályzatok (például egyéni elnevezési szabályzatok) az Azure NetApp Files felületen
* Az Azure NetApp Files forgalom terheléselosztói
* Az Azure NetApp-fájlok nem támogatottak az Azure Virtual WAN szolgáltatással

Az Azure NetApp-fájlokra a következő hálózati korlátozások vonatkoznak:

* Az Azure NetApp-fájlokkal (beleértve a társviszonyba adott virtuális hálózatokat is) használatban lévő virtuális hálózatokban használt IP-k száma nem haladhatja meg az 1000-et. Azon dolgozunk, hogy növeljük ezt a korlátot, hogy megfeleljen az ügyfélméret követelményeinek. 
* Minden Egyes Azure virtuális hálózat (VNet) csak egy alhálózat delegálható az Azure NetApp-fájlok.


### <a name="supported-network-topologies"></a>Támogatott hálózati topológiák

Az alábbi táblázat az Azure NetApp-fájlok által támogatott hálózati topológiákat ismerteti.  Ismerteti a nem támogatott topológiák kerülő megoldásait is. 

|    Topológiák    |    Támogatott    |     Áthidaló megoldás    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    A kötethez való kapcsolódás helyi virtuális hálózatban    |    Igen    |         |
|    Kapcsolat a kötethez egy társviszony-létesített virtuális hálózatban (ugyanaz a régió)    |    Igen    |         |
|    Kapcsolat a kötethez egy társviszony-létesített virtuális hálózatban (több régióban vagy globális társviszony-létesítés)    |    Nem    |    None    |
|    Kapcsolat egy kötethez ExpressRoute-átjárón keresztül    |    Igen    |         |
|    A helyszíni kapcsolat egy küllős virtuális hálózatban lévő kötethez ExpressRoute-átjárón keresztül, valamint az átjáró-átvitellel rendelkező virtuális hálózat társviszony-létesítése    |    Igen    |        |
|    A helyszíni és a küllővirtuális hálózat vpn-átjárón keresztüli kötete közötti kapcsolat    |    Igen    |         |
|    A helyszíni kapcsolat egy küllővirtuális hálózat ban lévő kötethez VPN-átjárón keresztül, valamint virtuális hálózat-társviszony-létesítés átjáró-átvitellel    |    Igen    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuális hálózat az Azure NetApp-fájlok köteteihez

Ez a szakasz ismerteti a virtuális hálózat tervezését segítő fogalmakat.

### <a name="azure-virtual-networks"></a>Azure-beli virtuális hálózatok

Az Azure NetApp Files-kötet kiépítése előtt létre kell hoznia egy Azure virtuális hálózatot (VNet), vagy olyan hálózatot kell használnia, amely már létezik az előfizetésében. A virtuális hálózat határozza meg a kötet hálózati határát.  A virtuális hálózatok létrehozásáról az [Azure Virtuális hálózat dokumentációjában](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)olvashat bővebben.

### <a name="subnets"></a>Alhálózatok

Az alhálózatok a virtuális hálózatot külön címterekre bontják, amelyeket az Azure-erőforrások használnak.  Az Azure NetApp-fájlok kötetei egy [delegált alhálózatnak](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)nevezett speciális célú alhálózatban találhatók. 

Az alhálózati delegálás explicit engedélyeket ad az Azure NetApp Files szolgáltatásnak, hogy szolgáltatásspecifikus erőforrásokat hozzon létre az alhálózatban.  A szolgáltatás üzembe helyezésekor egyedi azonosítót használ. Ebben az esetben egy hálózati illesztő jön létre az Azure NetApp-fájlok hoz létre kapcsolatot.

Ha új virtuális hálózatot használ, létrehozhat egy alhálózatot, és delegálhatja az alhálózatot az Azure NetApp-fájlokba az [Alhálózat Azure NetApp-fájlokra való delegálása](azure-netapp-files-delegate-subnet.md)című útmutató utasításainak követésével. Delegálhat egy meglévő üres alhálózatot is, amely még nincs delegálva más szolgáltatásokhoz.

Ha a virtuális hálózat társviszonyban van egy másik virtuális hálózattal, nem bonthatja ki a virtuális hálózat címterületét. Ezért az új delegált alhálózatot a virtuális hálózat címterületén belül kell létrehozni. Ha ki kell terjesztenie a címterületet, a címterület kibontása előtt törölnie kell a virtuális társviszony-létesítést.

### <a name="udrs-and-nsgs"></a>UdRs és NSG-k

A felhasználó által definiált útvonalak (UDRs) és a hálózati biztonsági csoportok (NSG-k) nem támogatottak az Azure NetApp-fájlok delegált alhálózatain.

Megoldásként nsg-ket alkalmazhat más alhálózatokra, amelyek engedélyezik vagy megtagadják a forgalmat az Azure NetApp Files delegált alhálózatába és onnan.  

## <a name="azure-native-environments"></a>Az Azure natív környezetei

Az alábbi ábra egy Azure-natív környezetet mutat be:

![Azure-natív hálózati környezet](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Helyi virtuális hálózat

Az alapvető forgatókönyv az Azure NetApp Files-kötet létrehozása vagy az azure-beli NetApp-fájlok kötetének létrehozása vagy csatlakoztatása ugyanabban a virtuális hálózatban lévő virtuális gépről. A fenti ábrán a Virtuális hálózat 2 virtuális hálózatához az 1.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

Ha további virtuális hálózatok ugyanabban a régióban, amelyek hozzáférést egymás erőforrásaihoz való hozzáférésre van szüksége, a virtuális hálózatok virtuális [hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) használatával csatlakoztatható a biztonságos kapcsolat az Azure-infrastruktúrán keresztül. 

Vegye figyelembe a VNet 2 és a VNet 3 a fenti ábrán. Ha a Virtuálisgép 1-nek csatlakoznia kell a 2-es virtuális géphez vagy a 2-es kötethez, vagy ha a 2.virtuális géphez kell csatlakoznia az 1. 

Emellett fontolja meg egy forgatókönyvet, ahol a Virtuális hálózat 1 társviszonyban van a Virtuális hálózat 2 és a Virtuális hálózat 2 társviszonyt a VNet 3 ugyanabban a régióban. A Virtuális hálózat 1-ből származó erőforrások csatlakozhatnak a Virtuális hálózat 2-es hálózatának erőforrásaihoz, de nem tudnak csatlakozni a 3-as virtuális hálózat erőforrásaihoz, kivéve, ha a Virtuális hálózat 1 és a VNet 3 társviszonyban van. 

A fenti ábrán, bár a VM 3 tud csatlakozni az 1.  Ennek az az oka, hogy a küllővirtuális hálózatok nem társviszonyt, és _a tranzit-útválasztás nem támogatott a virtuális hálózat társviszony-létesítés._

## <a name="hybrid-environments"></a>Hibrid környezetek

Az alábbi ábra egy hibrid környezetet mutat be: 

![Hibrid hálózati környezet](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

A hibrid forgatókönyvben a helyszíni adatközpontokból származó alkalmazásoknak hozzá kell férnie az Azure-beli erőforrásokhoz.  Ez a helyzet, hogy ki kívánja-e terjeszteni az adatközpontot az Azure-ra, vagy az Azure natív szolgáltatásait vagy a vészhelyreállítást. Tekintse meg [a VPN-átjáró tervezési lehetőségeket,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) amelyek ből megtudhatja, hogyan csatlakoztathat több erőforrást a helyszínen az Azure-beli erőforrásokhoz egy helyek közötti VPN-en vagy egy ExpressRoute-on keresztül.

Egy hibrid hub-küllős topológia, az Azure-ban a központi virtuális hálózat a helyszíni hálózathoz való kapcsolódás központi pontjaként működik. A küllők virtuális hálózatok a hub, és a számítási feladatok elkülönítésére használhatók.

A konfigurációtól függően a helyszíni erőforrások at a hub és a küllők erőforrások csatlakoztatása.

A fent bemutatott topológia, a helyszíni hálózat csatlakozik egy központi virtuális hálózat az Azure-ban, és vannak 2 küllős virtuális hálózatok ugyanabban a régióban társviszonyban a központi virtuális hálózat.  Ebben a forgatókönyvben az Azure NetApp-fájlok kötetei számára támogatott csatlakozási lehetőségek a következők:

* A helyszíni erőforrások Virtuális gép 1 és Virtuálisgép 2 csatlakozhat az 1. 
* A helyszíni erőforrások virtuális gép 1 és virtuális gép 2 csatlakozhat a 2.
* A központi virtuális hálózat 3 virtuális gépe a küllővirtuális hálózat 2.
* VM 4 küllős vnet 1 és VM 5 küllős virtuális hálózat 2 csatlakozhat a kötet 1 a központi virtuális hálózat.
* A küllős VNet 1 vm 4-es gépe nem tud csatlakozni a 2.L. küllős virtuális hálózat 3. Emellett a vm 5 a küllős virtuális hálózat2 nem tud csatlakozni a 2. Ez azért van így, mert a küllővirtuális hálózatok nem társviszonyt, és _a tranzit-útválasztás nem támogatott a virtuális hálózat társviszony-létesítés._
* A fenti architektúrában, ha van egy átjáró a küllővirtuális hálózatban is, az ANF-kötethez való kapcsolódás a hub átjárón keresztül való csatlakozásból elvész. A küllővirtuális hálózatban előnyben részesíti az átjárót, így csak az átjárón keresztül csatlakozó gépek csatlakozhatnak az ANF-kötethez.

## <a name="next-steps"></a>További lépések

[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)
