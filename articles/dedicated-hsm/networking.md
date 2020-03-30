---
title: Hálózati szempontok - Azure dedikált HSM | Microsoft dokumentumok
description: Az Azure dedikált HSM-telepítéseivel kapcsolatos hálózati szempontok áttekintése
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881283"
---
# <a name="azure-dedicated-hsm-networking"></a>Az Azure dedikált HSM-hálózata

Az Azure dedikált HSM rendkívül biztonságos hálózati környezetet igényel. Ez igaz, függetlenül attól, hogy az Azure-felhővissza az ügyfél informatikai környezet (helyszíni), elosztott alkalmazások használatával vagy a magas rendelkezésre állású forgatókönyvek. Az Azure Networking biztosítja ezt, és négy különböző területeket kell kezelni.

- HSM-eszközök létrehozása a virtuális hálózaton (VNet) belül az Azure-ban
- A helyszíni csatlakoztatás felhőalapú erőforrásokhoz a HSM-eszközök konfigurálásához és kezeléséhez
- Virtuális hálózatok létrehozása és csatlakoztatása az összekapcsolt alkalmazás-erőforrásokhoz és HSM-eszközökhöz
- Virtuális hálózatok összekapcsolása régiók között a kommunikáció közötti kommunikáció hoz, valamint a magas rendelkezésre állású forgatókönyvek lehetővé tétele

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuális hálózat a dedikált HSM-ekhez

A dedikált HSM-ek integrálva vannak egy virtuális hálózatba, és az ügyfelek saját magánhálózatába kerülnek az Azure-ban. Ez lehetővé teszi a virtuális gépekről vagy a virtuális hálózat számítási erőforrásaiból származó eszközök elérését.  
Az Azure-szolgáltatások virtuális hálózatba való integrálásáról és az általa nyújtott képességekről további információt az [Azure-szolgáltatások virtuális hálózatdokumentációjában](../virtual-network/virtual-network-for-azure-services.md) talál.

### <a name="virtual-networks"></a>Virtuális hálózatok

Egy dedikált HSM-eszköz kiépítése előtt az ügyfeleknek először létre kell hozniuk egy virtuális hálózatot az Azure-ban, vagy olyan hálózatot kell használniuk, amely már létezik az ügyfelek előfizetésében. A virtuális hálózat határozza meg a dedikált HSM-eszköz biztonsági peremét. A virtuális hálózatok létrehozásával kapcsolatos további információkért lásd a [virtuális hálózati dokumentációt.](../virtual-network/virtual-networks-overview.md)

### <a name="subnets"></a>Alhálózatok

Az alhálózatok a virtuális hálózatot külön címterekre bontják, amelyeket az Azure-erőforrások hoznak létre. A dedikált HSM-ek a virtuális hálózat alhálózatába vannak telepítve. Minden dedikált HSM-eszköz, amely az ügyfél alhálózatában van telepítve, egy privát IP-címet kap erről az alhálózatról. Az alhálózatot, amelyben a HSM-eszközt telepítették, explicit módon delegálni kell a szolgáltatásnak: Microsoft.HardwareSecurityModules/dedicatedHSMs. Ez bizonyos engedélyeket ad a HSM-szolgáltatásnak az alhálózatba való telepítéshez. A dedikált HSM-ek delegálása bizonyos házirendkorlátozásokat ír elő az alhálózaton. A delegált alhálózatokon jelenleg nem támogatottak a hálózati biztonsági csoportok (NSG-k) és a felhasználó által definiált útvonalak (UDRs). Ennek eredményeképpen, ha egy alhálózat delegálása dedikált HSM-ek, csak hsm-erőforrások üzembe helyezésére használható. Bármely más ügyfélerőforrás üzembe helyezése az alhálózatba sikertelen lesz.


### <a name="expressroute-gateway"></a>ExpressRoute-átjáró

Az aktuális architektúra követelménye az ügyfelek alhálózatában lévő ER-átjáró konfigurációja, ahol egy HSM-eszközt kell elhelyezni a HSM-eszköz Azure-ba való integrálásának lehetővé tétele érdekében. Ez az ER-átjáró nem használható a helyszíni helyek csatlakoztatására az azure-beli HSM-eszközökhöz.

## <a name="connecting-your-on-premises-it-to-azure"></a>A helyszíni informatikai környezet és az Azure csatlakoztatása

Felhőalapú erőforrások létrehozásakor ez egy tipikus követelmény a helyszíni informatikai erőforrásokhoz való privát kapcsolathoz. Dedikált HSM esetében ez elsősorban a HSM-ügyfélszoftver számára lesz a HSM-eszközök konfigurálása, valamint olyan tevékenységek esetében, mint a biztonsági mentések és a naplók lehívása a HSM-ekből elemzéscéljából. A legfontosabb döntési pont itt a kapcsolat jellege, mivel vannak lehetőségek.  A legrugalmasabb megoldás a helyek közötti VPN, mivel valószínűleg több helyszíni erőforrás lesz, amelyek biztonságos kommunikációt igényelnek az Azure-felhőben az erőforrásokkal (beleértve a HSM-eket is). Ehhez az ügyfélszervezetnek rendelkeznie kell VPN-eszközzel a kapcsolat megkönnyítése érdekében. A pont-hely VPN-kapcsolat akkor használható, ha csak egyetlen végpont van a helyszínen, például egyetlen felügyeleti munkaállomás.
A kapcsolódási lehetőségekről a [VPN-átjáró tervezési beállításai](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)című témakörben talál további információt.

> [!NOTE]
> Jelenleg az ExpressRoute nem lehetséges a helyszíni erőforrásokhoz való csatlakozáshoz. Azt is meg kell jegyezni, hogy a fent leírt ExpressRoute-átjáró nem a helyszíni infrastruktúrához való csatlakozáshoz használható.

### <a name="point-to-site-vpn"></a>Pont–hely VPN

A pont-hely virtuális magánhálózat a legegyszerűbb formája a biztonságos kapcsolat egyetlen végpont helyszíni. Ez akkor lehet fontos, ha csak egyetlen felügyeleti munkaállomást kíván rendelkezni az Azure-alapú dedikált HSM-ekhez.

### <a name="site-to-site-vpn"></a>Helyek közötti VPN

A helyek közötti virtuális magánhálózat biztonságos kommunikációt tesz lehetővé az Azure-alapú dedikált HSM-ek és a helyszíni informatikai környezet között. Ennek egyik oka, hogy rendelkezik egy biztonsági mentési lehetőség a HSM helyszíni és szüksége van egy kapcsolat a kettő között a biztonsági mentés futtatásához.

## <a name="connecting-virtual-networks"></a>Virtuális hálózatok csatlakoztatása

A dedikált HSM tipikus telepítési architektúrája egyetlen virtuális hálózattal és a megfelelő alhálózattal kezdődik, amelyben a HSM-eszközök létrejönnek és kivannak építve. Ugyanebben a régióban lehet, hogy további virtuális hálózatok és alhálózatok az alkalmazás-összetevők, amelyek a dedikált HSM. A kommunikáció engedélyezéséhez ezeken a hálózatokon keresztül virtuális hálózati társviszony-létesítést használunk.

### <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Ha egy régióban több virtuális hálózat van, amelyeknek el kell érniük egymás erőforrásait, a virtuális hálózati társviszony-létesítés segítségével biztonságos kommunikációs csatornákat hozhat létre közöttük.  A virtuális hálózati társviszony-létesítés nem csak biztonságos kommunikációt biztosít, hanem alacsony késleltetést és nagy sávszélességű kapcsolatokat biztosít az Azure-beli erőforrások között.

![hálózati társviszony-létesítés](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Csatlakozás az Azure-régiók között

A HSM-eszközök szoftverkönyvtárakon keresztül képesek átirányítani a forgalmat egy másik HSM-re. A forgalom átirányítása akkor hasznos, ha az eszközök meghibásodnak, vagy az eszközhöz való hozzáférés megszakad. A regionális szintű meghibásodási forgatókönyvek csökkenthetők a HSM-ek más régiókban történő telepítésével és a virtuális hálózatok közötti kommunikáció lehetővé tévő lehetővé teszi a régiók közötti kommunikációt.

### <a name="cross-region-ha-using-vpn-gateway"></a>Régióközi HA VPN-átjáró használatával

Globálisan elosztott alkalmazások vagy magas rendelkezésre állású regionális feladatátvételi forgatókönyvek esetén a régiók közötti virtuális hálózatok összekapcsolásához szükséges. Az Azure dedikált HSM-mel a magas rendelkezésre állás egy VPN-átjáró használatával érhető el, amely biztonságos alagutat biztosít a két virtuális hálózat között. A VPN-átjárót használó vnet-vnet-kapcsolatokról a [Mi a VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V) című cikkben olvashat bővebben?

> [!NOTE]
> A globális virtuális kapcsolatlétesítés nem érhető el a régiók közötti kapcsolatforgatókönyvek dedikált HSMs ebben az időben, és a VPN-átjáró kell használni helyette. 

![globális-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>További lépések

- [Gyakori kérdések](faq.md)
- [Támogatási lehetőségek](supportability.md)
- [Magas rendelkezésre állás](high-availability.md)
- [Fizikai biztonság](physical-security.md)
- [Megfigyelő](monitoring.md)
- [Telepítési architektúra](deployment-architecture.md)