---
title: "Azure-beli virtuális hálózat |} Microsoft Docs"
description: "További tudnivalók az Azure Virtual Network fogalmakat és szolgáltatásokat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/1/2018
ms.author: jdial
ms.openlocfilehash: fadc1994cd930df36387a5bfb302c00d66f74fad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="what-is-azure-virtual-network"></a>Mi az Azure Virtual Network?

Azure-beli virtuális hálózat lehetővé teszi, hogy az Azure-erőforrások kommunikál egymással és az interneten. A virtuális hálózati elkülöníti az erőforrások erőforrásokból mások Azure felhőben. Virtuális hálózatok más virtuális hálózatok, vagy a helyszíni hálózathoz is elérheti. 

Azure-beli virtuális hálózat a következő széleskörű lehetőségeket biztosítja:
- **[Elkülönítési:](#isolation)**  virtuális hálózatok el különítve egymástól. Külön hozhat létre virtuális hálózatok fejlesztési, tesztelési és éles, amelyek az ugyanazon CIDR (például 10.0.0.0/0) cím blokkokat. Ezzel szemben több virtuális hálózat, amely különböző a CIDR címblokkokat használja, és összekapcsolhatja a hálózatok is létrehozhat. Több virtuális hálózat is szegmentálhatja. Azure belső névfeloldást biztosít a virtuális hálózat üzembe helyezett erőforrás. Szükség esetén egy virtuális hálózatot az Azure belső névfeloldást használata helyett a saját DNS-kiszolgálók használatára konfigurálhatja.
- **[Internetes kommunikáció:](#internet)**  erőforrások, például a virtuális gépek része virtuális hálózatnak, rendelkezik internetkapcsolattal, alapértelmezés szerint. Bejövő hozzáférés adható konkrét erőforrásokhoz, engedélyezheti igény szerint is.
- **[Azure-erőforrás kommunikációs:](#within-vnet)**  Azure-erőforrások része virtuális hálózatnak is kommunikálhatnak egymással magánhálózati IP-címek használata akkor is, ha az erőforrások külön alhálózatokon vannak telepítve. Azure biztosít alapértelmezett között alhálózatokat, virtuális hálózatokhoz csatlakozó és a helyszíni hálózatokban, így nem kell konfigurálnia és felügyelnie az útvonalakat. Igény szerint testre szabhatja Azure útválasztási.
- **[Virtuális hálózati kapcsolat:](#connect-vnets)**  virtuális hálózatok csatlakozhatnak egymáshoz, engedélyezése bármely más virtuális hálózatán lévő erőforrásokat kommunikálni bármely virtuális hálózatán lévő erőforrásokat.
- **[A helyi kapcsolat:](#connect-on-premises)**  egy virtuális hálózatot egy a helyszíni hálózati erőforrások egymás közötti kommunikáció engedélyezésével lehet csatlakoztatni.
- **[Forgalomszűrést végez:](#filtering)**  szűrheti a hálózati forgalom forrás IP-cím és port, cél IP-cím és port és protokoll által a virtuális hálózatán lévő erőforrásokat.
- **[Útválasztás:](#routing)**  opcionálisan úgy konfigurálja a saját útvonalak útválasztási Azure alapértelmezett érték felülírható, vagy BGP propagálására irányítja a hálózati átjárón keresztül.

## <a name = "isolation"></a>A hálózati elkülönítés és Szegmentálás

Minden Azure belül több virtuális hálózat is létrehozható [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) és az Azure [régió](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region). Az egyes virtuális hálózatok egymástól elkülönítve működnek. Minden virtuális hálózathoz a következő műveletek végezhetők el:
- Adjon meg egy egyéni privát IP-címtér nyilvános és titkos (az RFC 1918) címeket használnak. Azure virtuális hálózatban egy magánhálózati IP-cím erőforrás a hozzárendelt címtér rendeli hozzá.
- A virtuális hálózati szegmenseket, egy vagy több alhálózatból be, és foglaljon le a virtuális hálózati címterüket minden alhálózat egy része.
- Azure által biztosított névfeloldás használata, vagy adja meg a saját DNS-kiszolgáló, a virtuális hálózatán lévő erőforrásokat általi használatra. Névfeloldás a virtuális hálózatok kapcsolatos további információkért lásd: [névfeloldását virtuális hálózatokon lévő erőforrások](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Internetes kommunikáció
A virtuális hálózat összes erőforrás képes kommunikálni a kimenő internetkapcsolat. Alapértelmezés szerint a magánhálózati IP-cím erőforrás az adatforrás hálózati cím lefordított (SNAT) egy nyilvános IP-cím szerint az Azure-infrastruktúra. Kimenő internetkapcsolattal kapcsolatos további információkért lásd: [ismertetése az Azure-ban kimenő kapcsolatok](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ha szeretné megakadályozni a kimenő internetkapcsolat, valósíthatja meg egyéni útvonalak vagy forgalomszűrést végez.

Való kommunikációra bejövő Azure-erőforrások az internetről, vagy az internethez nélkül SNAT kimenő kommunikációra, egy erőforrást egy nyilvános IP-címet kell hozzárendelve. Nyilvános IP-címekre vonatkozó további tudnivalókért lásd: [nyilvános IP-címek](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Azure-erőforrások közötti biztonságos kommunikációhoz

A virtuális hálózaton belüli virtuális gépek telepítése. Virtuális gépek kommunikálni a hálózati adaptert egy virtuális hálózatot egyéb erőforrások. Hálózati illesztők kapcsolatos további információkért lásd: [hálózati illesztőt](virtual-network-network-interface.md).

Azure-erőforrások különböző típusú virtuális hálózathoz, például az Azure App Service Environment-környezetek és Azure virtuálisgép-méretezési csoportok is telepíthet. Azure-erőforrások telepítése virtuális hálózatba teljes listáját lásd: [virtuális hálózati szolgáltatás integráció az Azure szolgáltatások](virtual-network-for-azure-services.md).

Bizonyos erőforrások nem állítható rendszerbe a virtuális hálózathoz, de engedélyezi, hogy csak virtuális hálózaton belüli erőforrások kommunikáció korlátozása. Az erőforrásokhoz való hozzáférés korlátozása kapcsolatos további információkért lásd: [virtuális hálózati Szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Virtuális hálózatok csatlakoztatása

Kapcsolódás virtuális hálózatok egymással, vagy használja a virtuális hálózati társviszony-létesítés egymással kommunikálni virtuális hálózatán lévő erőforrásokat engedélyezése. A sávszélesség és a különböző virtuális hálózatokon lévő erőforrások közötti kommunikációs késés: ugyanaz, mintha az erőforrásokat ugyanabban a virtuális hálózatban. A társviszony-létesítés kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md).

## <a name="connect-on-premises"></a>Egy a helyszíni hálózat

A helyszíni hálózat csatlakozhat a virtuális hálózat tetszőleges kombinációját az alábbiak közül:
- **Pont-pont virtuális magánhálózati (VPN):** a hálózat meglévő virtuális hálózat és a egyetlen számítógép között. Minden számítógépen, hogy a virtuális hálózati kapcsolatok létrehozása próbál egymástól függetlenül kell konfigurálnia a kapcsolatot. A kapcsolat típusa nem nagyszerű, ha Ön most csak az első lépések az Azure-ral, vagy a fejlesztők számára, mert azt a meglévő hálózati kevéssé vagy egyáltalán ne módosítását igényli. A kapcsolat az SSTP protokoll segítségével, titkosított kommunikációt biztosít a számítógép és egy virtuális hálózat között az interneten keresztül. A pont-pont típusú VPN várakozási is előre nem látható, mivel a forgalom halad át az interneten.
- **Telephelyek közötti VPN:** a VPN-eszköz és az Azure VPN Gateway része virtuális hálózatnak között. A kapcsolat típusa lehetővé teszi, hogy bármely is hozzáférhetnek a virtuális hálózathoz engedélyezni szeretné a helyi erőforrás. A kapcsolat az IPSec/IKE VPN, amely titkosított kommunikációt biztosít az interneten, a helyszíni eszközök és az Azure VPN gateway között. A pont-pont kapcsolat a késési is előre nem látható, mivel a forgalom halad át az interneten.
- **Az Azure ExpressRoute:** hoznak létre egy ExpressRoute-partner keresztül a hálózat és az Azure-ban. Ezt a kapcsolatot a sajátja. Forgalom nem bejárják az interneten. Az ExpressRoute-kapcsolat a késési is előre jelezhető, mivel a forgalom nem haladnak át az interneten.

Az előző kapcsolódási beállítás kapcsolatos további információkért lásd: [kapcsolat topológiai diagramot](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Hálózati forgalom szűrésére
Szűrheti a hálózati forgalom valamelyike vagy mindegyike a következő beállítások segítségével alhálózatok között:
- **Hálózati biztonsági csoportok:** hálózati biztonsági csoport több bejövő és kimenő biztonsági szabály lehetővé teszi, hogy a forrás és cél IP-cím, port és protokoll forgalmának szűrésére tartalmazhat. Hálózati biztonsági csoport egy virtuális gép mindegyik hálózati interfész alkalmazhatja. Az alhálózatnak a hálózati illesztő hálózati biztonsági csoport is alkalmazhat, vagy más Azure-erőforrás. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](security-overview.md#network-security-groups).
- **Virtuális készülékekre:** A hálózati virtuális készülék valójában egy hálózati funkciót, például egy tűzfal végző szoftvert futtató virtuális gép. Virtuális készülékek rendelkezésre álló hálózati listájának megtekintése a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Virtuális hálózati berendezések megtalálhatók érhető el, hogy WAN-optimalizálást és az egyéb hálózati forgalom funkciókat biztosít. Virtuális hálózati berendezéseket jellemzően használják a felhasználó által definiált vagy a BGP-útvonalakat. A hálózati virtuális készülék segítségével virtuális hálózatok közötti forgalom szűrésére.

## <a name="routing"></a>Hálózati forgalom

Az útvonaltáblák, amelyek lehetővé teszik a csatlakoztatott kommunikál egymással és az interneten, a virtuális hálózat egyetlen alhálózatának alapértelmezés szerint az Azure létrehoz. Megvalósíthat valamelyike vagy mindegyike felülbírálhatja az alapértelmezett útvonalak Azure-hoz létre a következő beállításokat:
- **Útvonaltáblát:** hozhat létre egyéni útvonaltáblák útvonalak adott vezérlőn, ahol továbbítódik a az egyes alhálózatokon. Egyéni útválasztás kapcsolatos további információkért lásd: [egyéni útválasztás](virtual-networks-udr-overview.md#user-defined).
- **BGP-útvonalakat:** Ha a helyszíni hálózat az Azure VPN-átjáró vagy ExpressRoute keresztül csatlakozni a virtuális hálózaton, akkor kerülhet BGP-útvonalakat a virtuális hálózatok.

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik Azure Virtual Network áttekintése. Útmutató: virtuális hálózat létrehozása, és néhány Azure virtuális gép telepítésével bele használatára bizonyos Azure virtuális hálózat képességeket.

> [!div class="nextstepaction"]
> [Virtuális hálózat létrehozása](quick-create-portal.md)
