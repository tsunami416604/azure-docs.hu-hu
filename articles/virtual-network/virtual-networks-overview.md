---
title: "Azure-beli virtuális hálózat |} Microsoft Docs"
description: "További tudnivalók az Azure Virtual Network fogalmakat és szolgáltatásokat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network"></a>Azure Virtual Network

Az Azure Virtual Network szolgáltatás lehetővé teszi, hogy biztonságosan kapcsolódjanak a Azure-erőforrások egymástól a virtuális hálózatokon (Vnetek). A virtuális hálózat a felhőben saját hálózati ábrázolása. Egy Vnetet az Azure-előfizetéshez dedikált felhő logikai elkülönítése. A helyszíni hálózat csatlakozhat Vnetek is. Az alábbi képen néhány olyan funkciója, az Azure Virtual Network szolgáltatás:

![Hálózati diagram](./media/virtual-networks-overview/virtual-network-overview.png)

A következő Azure Virtual Network képességeivel kapcsolatos további tudnivalókért kattintson a funkció:
- **[Elkülönítési:](#isolation)**  Vnetek el különítve egymástól. Létrehozhat külön Vnetek fejlesztési, tesztelési és éles, amelyek ugyanazon a CIDR címblokkokat használják. Ezzel szemben, amelyek különböző CIDR címblokkokat és összekapcsolhatja az hálózatok több Vnetek is létrehozhat. Egy VNet érdekében több alhálózatra is szegmentálhatja. Azure belső névfeloldást biztosít a virtuális gépek és Felhőszolgáltatások szerepkörpéldányokat egy Vnetet csatlakozik. Konfigurálhatja egy VNet és a saját DNS-kiszolgálók használata az Azure belső névfeloldást használata helyett.
- **[Internetkapcsolat:](#internet)**  csatlakozik egy VNet minden Azure virtuális gépek (VM) és a felhőalapú szolgáltatások szerepkörpéldányokat alapértelmezés szerint rendelkezik Internet,-hozzáférés. Bejövő hozzáférés adható konkrét erőforrásokhoz, engedélyezheti igény szerint is.
- **[Az Azure erőforrás-kapcsolat:](#within-vnet)**  Azure-erőforrások, például a Felhőszolgáltatások és virtuális gépek ugyanazt a virtuális hálózatot lehet csatlakoztatni. Az erőforrások egymást magánhálózati IP-címek is csatlakozhat, ha külön alhálózatokon vannak. Azure biztosít alapértelmezett között alhálózatok, a Vnetek és a helyszíni hálózatokhoz, így nem kell konfigurálnia és felügyelnie az útvonalakat.
- **[VNet-kapcsolatot:](#connect-vnets)**  Vnetek csatlakozhatnak egymáshoz, csatlakoznak bármely virtuális hálózat bármely más virtuális hálózaton erőforrás kommunikálni erőforrások engedélyezése.
- **[A helyi kapcsolat:](#connect-on-premises)**  Vnetek lehet csatlakoztatni a helyszíni hálózatokban magánhálózati kapcsolatok a hálózati és az Azure között, vagy egy pont-pont VPN-kapcsolaton keresztül az interneten keresztül.
- **[Forgalomszűrést végez:](#filtering)**  virtuális gép és a felhőalapú szolgáltatások szerepkör példányok hálózati forgalom bejövő és kimenő alapján szűrhetők forrás IP-cím és port, cél IP-cím és port és protokoll.
- **[Útválasztás:](#routing)**  opcionálisan felülbírálhatja Azure alapértelmezett útválasztási saját útvonalak konfigurálása, vagy használja a BGP-útvonalakat hálózati átjárón keresztül.

## <a name = "isolation"></a>A hálózati elkülönítés és Szegmentálás

Minden Azure belül több Vnetek Megvalósíthat [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és az Azure [régió](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region). Minden egyes virtuális hálózat el különítve a más Vnetekről. Minden egyes vnet a következő műveletek végezhetők el:
- Adjon meg egy egyéni privát IP-címtér nyilvános és titkos (az RFC 1918) címeket használnak. Azure rendel erőforrások a VNet egy magánhálózati IP-cím keresztül kapcsolódik a címterület lehet kijelölni.
- A VNet szegmentálni be egy vagy több alhálózatból, és foglaljon le a VNet-címterek minden alhálózat egy része.
- Azure által biztosított névfeloldás használata, vagy adjon meg egy Vnetet csatlakoztatott a saját DNS-kiszolgáló számára. Névfeloldás a Vnetek kapcsolatos további tudnivalókért olvassa el a [névfeloldását virtuális gépek és Felhőszolgáltatások](virtual-networks-name-resolution-for-vms-and-role-instances.md) cikk.

## <a name = "internet"></a>Az internetkapcsolat működését
Egy Vnetet kapcsolódó összes erőforrásokhoz alapértelmezés szerint kimenő képes kapcsolódni az internetre rendelkeznek. A magánhálózati IP-cím erőforrás hálózati forráscím lefordított (SNAT) által az Azure-infrastruktúra egy nyilvános IP-cím. Kimenő internetkapcsolattal kapcsolatos további tudnivalókért olvassa el a [ismertetése az Azure-ban kimenő kapcsolatok](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address) cikk. Egyéni Útválasztás és a forgalom szűrés alkalmazásával módosíthatja az alapértelmezett működését.

Való kommunikációra bejövő Azure-erőforrások az internetről, vagy az internethez nélkül SNAT kimenő kommunikációra, egy erőforrást egy nyilvános IP-címet kell hozzárendelve. Nyilvános IP-címek kapcsolatos további tudnivalókért olvassa el a [nyilvános IP-címek](virtual-network-public-ip-address.md) cikk.

## <a name="within-vnet"></a>Csatlakozás Azure-erőforrások
Több Azure-erőforrások csatlakozhat a virtuális hálózat, például a virtuális gépek (VM), a Cloud Services, az App Service Environment-környezetek és a virtuálisgép-méretezési készlet. Virtuális gépek csatlakozni egy alhálózatot a hálózati adaptert (NIC) keresztül egy Vneten belül. Hálózati adapter kapcsolatos további tudnivalókért olvassa el a [hálózati illesztőt](virtual-network-network-interface.md) cikk.

## <a name="connect-vnets"></a>Virtuális hálózatok csatlakoztatása

Kapcsolódás Vnetek egymással, vagy VNet kommunikálhassanak egymással Vnetek csatlakoztatott engedélyezése. Valamelyike vagy mindegyike a következő beállítások segítségével Vnetek csatlakozni egymáshoz:
- **Társviszony-létesítés:** lehetővé teszi az erőforrások egymással kommunikálni az Azure ugyanazon a helyen belül különböző Azure Vnetekhez csatlakoztatni. A sávszélesség és a késleltetés között a Vnetek megegyezik, mintha az erőforrások csatlakoztatva ugyanazt a virtuális hálózatot. Társviszony-létesítés kapcsolatos további tudnivalókért olvassa el a [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md) cikk.
- **VNet – VNet-kapcsolatot:** lehetővé teszi, hogy az ugyanazon vagy másik Azure helyek belül különböző Azure VNet kapcsolódó erőforrásokhoz. Ellentétben a társviszony-létesítést, sávszélesség oka Vnetek közötti forgalmat kell áramlása az Azure VPN Gateway. VNet – VNet-kapcsolatot Vneteket összekötő kapcsolatos további tudnivalókért olvassa el a [VNet – VNet-kapcsolatot konfiguráló](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk.

## <a name="connect-on-premises"></a>Egy a helyszíni hálózat

A helyszíni hálózat csatlakozhatnak egy virtuális hálózat használatával tetszőleges kombinációját az alábbiak közül:
- **Pont-pont virtuális magánhálózati (VPN):** a egyetlen számítógép csatlakozik a hálózathoz és a virtuális hálózat között. A kapcsolat típusa nem nagyszerű, ha Ön most csak az első lépések az Azure-ral, vagy a fejlesztők számára, mert azt a meglévő hálózati kevéssé vagy egyáltalán ne módosítását igényli. A kapcsolat az SSTP protokoll segítségével, titkosított kommunikációt biztosít a számítógép és a virtuális hálózat között az interneten keresztül. A pont-pont típusú VPN várakozási is előre nem látható, mivel a forgalom halad át az interneten.
- **Telephelyek közötti VPN:** a VPN-eszköz és az Azure VPN-átjáró között. A kapcsolat típusa lehetővé teszi, hogy az összes helyszíni erőforrás is engedélyezni szeretné a virtuális hálózat eléréséhez. A kapcsolat az IPSec/IKE VPN, amely titkosított kommunikációt biztosít az interneten, a helyszíni eszközök és az Azure VPN gateway között. A pont-pont kapcsolat a késési is előre nem látható, mivel a forgalom halad át az interneten.
- **Az Azure ExpressRoute:** hoznak létre egy ExpressRoute-partner keresztül a hálózat és az Azure-ban. Ezt a kapcsolatot a sajátja. Forgalom nem bejárják az interneten. Az ExpressRoute-kapcsolat a késési is előre jelezhető, mivel a forgalom nem haladnak át az interneten.

Az előző kapcsolódási beállítás kapcsolatos további tudnivalókért olvassa el a [kapcsolat topológiai diagramot](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams) cikk.

## <a name="filtering"></a>Hálózati forgalom szűrésére
Szűrheti a hálózati forgalom valamelyike vagy mindegyike a következő beállítások segítségével alhálózatok között:
- **Hálózati biztonsági csoportok (NSG):** minden NSG tartalmazhat több bejövő és kimenő biztonsági szabály lehetővé teszi, hogy a forrás és cél IP-cím, port és protokoll forgalmának szűrésére. Akkor is alkalmazzon NSG-t egyes hálózati adapterek virtuális gépen. Az NSG-t is alkalmazhat az alhálózat egy hálózati Adaptert, vagy más Azure-erőforrás, csatlakozik-e. Ha többet szeretne megtudni az NSG-k, olvassa el a [hálózati biztonsági csoportok](virtual-networks-nsg.md) cikk.
- **Virtuális készülékek (NVA) hálózati:** az NVA egy hálózati funkciót, például egy tűzfal végző szoftvert futtató virtuális gép. A rendelkezésre álló NVAs listájának megtekintése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). NVAs is elérhetők, hogy WAN-optimalizálást és az egyéb hálózati forgalom funkciókat biztosít. NVAs általában használt felhasználói vagy a BGP-útvonalakat. NVA Vnetek közötti forgalom szűrésére is használhatja.

## <a name="routing"></a>Hálózati forgalom

Az útvonaltáblák, amelyek lehetővé teszik az erőforrások csatlakoznak bármely kommunikálhatnak egymással, alapértelmezés szerint a virtuális hálózat egyetlen alhálózatának sem az Azure létrehoz. Megvalósíthat valamelyike vagy mindegyike felülbírálhatja az alapértelmezett útvonalak Azure-hoz létre a következő beállításokat:
- **Felhasználó által definiált útvonalak:** hozhat létre egyéni útvonaltáblák útvonalak adott vezérlőn, ahol továbbítódik a az egyes alhálózatokon. A felhasználó által megadott útválasztással kapcsolatos további információkért olvassa el a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md) ismertető cikket.
- **BGP-útvonalakat:** Ha a virtuális hálózat csatlakozni a helyszíni hálózat az Azure VPN-átjáró vagy ExpressRoute kapcsolattal, akkor kerülhet BGP-útvonalakat a Vnetek.

## <a name="pricing"></a>Díjszabás

Használata díjmentes virtuális hálózatok, alhálózatok, útvonaltábláit vagy hálózati biztonsági csoportok. Kimenő internetes sávszélesség, nyilvános IP-címek, virtuális hálózati társviszony-létesítést, VPN-átjárók, és ExpressRoute minden rendelkezik meg saját struktúrákat árképzési. Nézet a [virtuális hálózati](https://azure.microsoft.com/pricing/details/virtual-network), [VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway), és [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) árképzési lapok további információt.

## <a name="faq"></a>GYIK

Virtuális hálózattal kapcsolatos gyakran ismételt kérdések ellenőrzéséhez tekintse meg a [virtuális hálózati gyakran ismételt kérdések](virtual-networks-faq.md) cikk.


## <a name="next-steps"></a>Következő lépések

- Az első virtuális hálózat létrehozása, és csatlakozik néhány virtuális géppel, Ehhez hajtsa végre a lépéseket a [az első virtuális hálózat létrehozása](virtual-network-get-started-vnet-subnet.md) cikk.
- Hozzon létre egy Vnetet egy pont – hely kapcsolatot; Ehhez hajtsa végre a lépéseket a [egy pont – hely kapcsolat beállítása](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk.
- Ismerje meg, azzal kapcsolatban, a másik kulccsal [hálózati képességek](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.
