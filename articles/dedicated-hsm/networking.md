---
title: Hálózati megfontolások – Azure dedikált HSM-be |} A Microsoft Docs
description: Hálózati Azure dedikált HSM központi telepítésekre vonatkozó szempontok áttekintése
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: 46a4cc5e48a9fe239a3c62bd72cf6a99e9bf76d8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092883"
---
# <a name="azure-dedicated-hsm-networking"></a>Az Azure dedikált HSM-hálózatok

Az Azure, a dedikált HSM rendkívül biztonságos hálózati környezet szükséges. Ez igaz legyen az az Azure felhőalapú vissza az ügyfél az IT-környezet (helyszíni), az elosztott alkalmazások vagy a magas rendelkezésre állási forgatókönyvek esetén. Azure-hálózatok biztosítják ezt és vonhat négy különböző területen.

- A virtuális hálózaton (VNet) belüli hardveres biztonsági modulokhoz létrehozása az Azure-ban
- Helyszíni kapcsolódás felhőalapú erőforrásokhoz a konfigurációs és a HSM-eszközök kezelése
- Létrehozása és csatlakozás a virtuális hálózatok közötti csatlakozó alkalmazás-erőforrások és a hardveres biztonsági modulokhoz
- Helyek közötti kommunikációra és magas rendelkezésre állású forgatókönyvek megvalósítását teszik lehetővé a régiók közötti virtuális hálózatok összekapcsolása

## <a name="virtual-network-for-your-dedicated-hsms"></a>A dedikált HSM-EK a virtuális hálózat

Dedikált HSM-EK integrálva a virtuális hálózat és az ügyfelek saját privát hálózat az Azure-ban helyezi el. Ez lehetővé teszi hozzáférési az eszközökön a virtuális gépek vagy a számítási erőforrások a virtuális hálózatban.  
A virtuális hálózat és a képességeket biztosít az Azure-szolgáltatások integrálásával kapcsolatos további információkért lásd: [virtuális hálózatok az Azure-szolgáltatások](../virtual-network/virtual-network-for-azure-services.md) dokumentációját.

### <a name="virtual-networks"></a>Virtuális hálózatok

A dedikált HSM-eszköz kiépítése, mielőtt ügyfelek először egy virtuális hálózat létrehozása az Azure-ban, vagy használjon egy meglévőt az ügyfél-előfizetés. A virtuális hálózat határozza meg a biztonsági határt, a dedikált HSM-eszközhöz. Virtuális hálózatok létrehozásával kapcsolatos további információkért lásd: [virtual network dokumentációja](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Alhálózatok

Alhálózatok, külön címterek használható a virtuális hálózat szegmentáljon az Azure-erőforrások bennük. Dedikált HSM-EK a rendszer üzembe helyezi a virtuális hálózat egyik alhálózata. Minden egyes dedikált HSM eszközt, amely az ügyfél alhálózaton van üzembe helyezve a magánhálózati IP-cím érkezik ezt az alhálózatot. Az alhálózatot, amelyben a HSM-eszközre van üzembe helyezve a szolgáltatás, explicit módon delegálható kell: Microsoft.HardwareSecurityModules/dedicatedHSMs. Ez bizonyos engedélyt a HSM szolgáltatást üzembe helyezését az alhálózathoz. Dedikált HSM delegálás bizonyos házirend-korlátozások az alhálózaton ír elő. Hálózati biztonsági csoportok (NSG-k) és a felhasználó által megadott útvonalakkal (Udr) jelenleg nem támogatottak a delegált alhálózatokon. Ennek eredményeképpen egy alhálózat delegált dedikált HSM-EK, miután csak használat a HSM-erőforrások üzembe helyezése. Az alhálózatban egyéb ügyfél-erőforrások üzembe helyezés sikertelen lesz.


### <a name="expressroute-gateway"></a>ExpressRoute-átjáró

Az aktuális architektúra követelményt a konfiguráció-ER-átjáró az ügyfelek alhálózat, ahol egy HSM-eszközre kell elhelyezni a HSM eszközt az Azure-bA integrációjának engedélyezése. Az ER-átjáró nem használhatók fel a helyszínek az Azure-ban, az ügyfelek hardveres biztonsági modulokhoz való kapcsolódáshoz.

## <a name="connecting-your-on-premises-it-to-azure"></a>Csatlakozás a helyi IT az Azure-bA

Felhőalapú erőforrások létrehozásakor, egy tipikus egy privát kapcsolaton keresztül érik el a helyszíni informatikai erőforrásra lenne szükség. Dedikált HSM esetén ez túlnyomórészt lesz a HSM-ügyfélszoftver a HSM-eszközök konfigurálása és tevékenységek, például a biztonsági másolatokat és naplókat jogosultak-e a HSM-EK elemzés céljából. A legfontosabb itt, hogy a kapcsolat természetét, lehetőség van.  A legrugalmasabb lehetőség, a Site-to-Site VPN, a rendszer valószínűleg több helyszíni erőforrásokhoz, amelyek biztonságos kommunikációt igényelnek az erőforrások (beleértve a HSM-EK) az Azure-felhőben. Ezt a beállítást egy ügyfél a szervezet rendelkezik egy VPN-eszköz kapcsolat megkönnyítése érdekében. Pont – hely VPN-kapcsolat használható, ha például egyetlen felügyeleti munkaállomások helyszíni csak egyetlen végpontja van.
A csatlakozási lehetőségek további információkért lásd: [tervezésénél VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

>[!NOTE]
Jelenleg az ExpressRoute lehetőség nem a helyszíni erőforrásokhoz való csatlakozáshoz. Megjegyzendő, hogy az ExpressRoute-átjárót használja a fent leírtak szerint, nem a helyszíni infrastruktúra-kapcsolatok is.

### <a name="point-to-site-vpn"></a>Pont – hely VPN

A pont – hely virtuális magánhálózati legegyszerűbb formája a biztonságos kapcsolatot létesíthet egy végpontot a helyszínen. Erre akkor lehet megfelelő, ha azt tervezi, hogy csak egyetlen felügyeleti munkaállomás rendelkezik Azure-alapú dedikált HSM-EK.

### <a name="site-to-site-vpn"></a>Site-to-Site VPN

Egy helyek közötti virtuális magánhálózat lehetővé teszi, hogy az Azure-alapú dedikált HSM-ekről és a helyszíni közötti biztonságos kommunikációhoz informatikai. Ennek okát a hardveres biztonsági MODULT a helyszíni és a biztonsági mentés futtatásához a kettő közötti kapcsolat létrehozása egy biztonsági mentési létesítmény tapasztalja.

## <a name="connecting-virtual-networks"></a>Virtuális hálózatok összekapcsolása

Egy tipikus telepítési dedikált HSM-architektúrát egy egyetlen virtuális hálózatot és a megfelelő alhálózatot, amelyben a hardveres biztonsági modulokhoz létrehozása és üzembe helyezett indul. Belül ugyanabban a régióban is lehetnek további virtuális hálózatok és alhálózatok alkalmazás-összetevők, amely biztosítja, a dedikált HSM használata. Ezek a hálózatok közötti kommunikáció engedélyezése, hogy használjuk, virtuális hálózatok közötti Társviszony.

### <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Ha több virtuális hálózat ugyanabban a régióban, amely a többi összes erőforrásokhoz kell hozzáférniük, virtuális hálózatok közötti Társviszony segítségével közöttük biztonságos kommunikációs csatornákat hozhat létre.  Virtuális hálózatok közötti társviszony nem csak a biztonságos kommunikációt biztosít, de egy kis késleltetésű, nagy sávszélességű kapcsolatok között az Azure-erőforrások is biztosítja.

![Hálózatok közötti társviszony](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Csatlakozás Azure-régiók között

A hardveres biztonsági modulokhoz is képes, keresztül szoftverek tárak, a forgalom átirányítása egy másik HSM-be. Forgalom átirányítása akkor hasznos, ha eszközök nem tudnak, vagy egy eszköz való hozzáférés elvész. Regionális szintű hibaesetet enyhíthetők üzembe helyezése a HSM-EK más régiókban, és régiók közötti virtuális hálózatok közötti kommunikáció engedélyezése.

### <a name="cross-region-ha-using-vpn-gateway"></a>Magas rendelkezésre ÁLLÁSÚ régiók közötti VPN-átjáró használatával

Globálisan elosztott alkalmazások vagy a magas rendelkezésre állású regionális feladatátvételi funkciók esetén szükség van a virtuális hálózatok összekapcsolása a régiók között elosztva. Az Azure dedikált HSM érhető el magas rendelkezésre állást biztosít egy biztonságos alagúton, a virtuális hálózatok közötti VPN-átjáró használatával. VPN-átjáró használatával további információk a virtuális hálózatok közötti kapcsolatok című cikkben: [Mi az a VPN-átjáró?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

>[!NOTE]
Globális virtuális társhálózatok létesítése nem áll rendelkezésre a régiók közötti kapcsolat a időben és a VPN-átjáró dedikált HSM-EK az forgatókönyvek helyette kell használni. 

![globális virtuális](media/networking/global-vnet.png)

## <a name="next-steps"></a>További lépések

- [Gyakori kérdések](faq.md)
- [Támogatási lehetőségek](supportability.md)
- [Magas rendelkezésre állás](high-availability.md)
- [Fizikai biztonság](physical-security.md)
- [Monitorozás](monitoring.md)
- [Üzembe helyezési architektúrája](deployment-architecture.md)