---
title: Mi az IP-168.63.129.16? | Microsoft Docs
description: Ismerje meg az IP-168.63.129.16, valamint azt, hogy miként működik az erőforrásaival.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: e061d503254ba7aa7735a97a060fc63f96b3fb61
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196672"
---
# <a name="what-is-ip-address-1686312916"></a>Mi az IP-168.63.129.16?

Az IP-168.63.129.16 egy virtuális nyilvános IP-cím, amely az Azure platform erőforrásainak kommunikációs csatornájának megkönnyítésére szolgál. Az ügyfelek meghatározhatnak bármilyen címtartományt az Azure-beli magánhálózati virtuális hálózathoz. Ezért az Azure platform-erőforrásokat egyedi nyilvános IP-címként kell megjeleníteni. Ez a virtuális nyilvános IP-cím a következő dolgokat teszi lehetővé:

- Lehetővé teszi, hogy a virtuálisgép-ügynök kommunikáljon az Azure platformmal, és jelezze, hogy "kész" állapotban van.
- Engedélyezi a kommunikációt a DNS virtuális kiszolgálóval, hogy a szűrt névfeloldást biztosítson az olyan erőforrásokra (például virtuális gépre), amelyek nem rendelkeznek egyéni DNS-kiszolgálóval. Ez a szűrés gondoskodik arról, hogy az ügyfelek csak az erőforrásaik állomásnevét tudják feloldani.
- A virtuális gépek állapotának meghatározásához engedélyezi az [Azure Load Balancer állapot-](../load-balancer/load-balancer-custom-probe-overview.md) mintavételét.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be a DHCP szolgáltatásból az Azure-ban.
- A vendég ügynök szívverési üzeneteinek engedélyezése a Pásti szerepkörhöz.

## <a name="scope-of-ip-address-1686312916"></a>IP-168.63.129.16 hatóköre

A nyilvános IP-168.63.129.16 minden régióban és minden nemzeti felhőben használatos. Ez a speciális nyilvános IP-cím a Microsoft tulajdonában van, és nem fog változni. Ezt az alapértelmezett hálózati biztonsági csoport szabálya engedélyezi. Azt javasoljuk, hogy engedélyezze ezt az IP-címet bármely helyi tűzfal-házirendben a bejövő és kimenő irányban is. A speciális IP-cím és az erőforrások közötti kommunikáció biztonságos, mert csak a belső Azure-platform tud üzenetet kiszolgálni ebből az IP-címről. Ha ez a címe le van tiltva, a váratlan viselkedés különböző helyzetekben fordulhat elő.
Legalább a következő portokat kell megnyitni a WireServer: 80, 443 és 32526 közötti kommunikáció engedélyezéséhez.

[Azure Load Balancer állapot](../load-balancer/load-balancer-custom-probe-overview.md) -mintavétel ebből az IP-címről származik. Ha letiltja ezt az IP-címet, a mintavétel sikertelen lesz.

A nem virtuális hálózati forgatókönyvek (klasszikus) esetében az állapot-mintavétel egy magánhálózati IP-címről származik, és a rendszer nem használja a 168.63.129.16.

## <a name="next-steps"></a>Következő lépések

- [Biztonsági csoportok](security-overview.md)
- [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](manage-network-security-group.md)
