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
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77114361"
---
# <a name="what-is-ip-address-1686312916"></a>Mi az IP-168.63.129.16?

Az IP-168.63.129.16 egy virtuális nyilvános IP-cím, amely az Azure platform erőforrásainak kommunikációs csatornájának megkönnyítésére szolgál. Az ügyfelek meghatározhatnak bármilyen címtartományt az Azure-beli magánhálózati virtuális hálózathoz. Ezért az Azure platform-erőforrásokat egyedi nyilvános IP-címként kell megjeleníteni. Ez a virtuális nyilvános IP-cím a következő dolgokat teszi lehetővé:

- Lehetővé teszi, hogy a virtuálisgép-ügynök kommunikáljon az Azure platformmal, és jelezze, hogy "kész" állapotban van.
- Engedélyezi a kommunikációt a DNS virtuális kiszolgálóval, hogy a szűrt névfeloldást biztosítson az olyan erőforrásokra (például virtuális gépre), amelyek nem rendelkeznek egyéni DNS-kiszolgálóval. Ez a szűrés gondoskodik arról, hogy az ügyfelek csak az erőforrásaik állomásnevét tudják feloldani.
- A virtuális gépek állapotának meghatározásához engedélyezi az [Azure Load Balancer állapot-](../load-balancer/load-balancer-custom-probe-overview.md) mintavételét.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be a DHCP szolgáltatásból az Azure-ban.
- A vendég ügynök szívverési üzeneteinek engedélyezése a Pásti szerepkörhöz.

## <a name="scope-of-ip-address-1686312916"></a>IP-168.63.129.16 hatóköre

A nyilvános IP-168.63.129.16 minden régióban és minden nemzeti felhőben használatos. Ez a speciális nyilvános IP-cím a Microsoft tulajdonában van, és nem fog változni. Azt javasoljuk, hogy engedélyezze ezt az IP-címet bármely helyi (virtuális gép) tűzfal-házirendben (kimenő irány). A speciális IP-cím és az erőforrások közötti kommunikáció biztonságos, mert csak a belső Azure-platform tud üzenetet kiszolgálni ebből az IP-címről. Ha ez a címe le van tiltva, a váratlan viselkedés különböző helyzetekben fordulhat elő. a 168.63.129.16 a [gazdagép csomópontjának virtuális IP-címe](../virtual-network/security-overview.md#azure-platform-considerations) , ezért a felhasználó által megadott útvonalak nem érvényesek.

- A virtuálisgép-ügynök a 80, 443, 32526 és a WireServer (168.63.129.16) portok felé irányuló kimenő kommunikációt követel meg. Ezeket a virtuális gép helyi tűzfalán kell megnyitni. Ezeknek a portoknak a 168.63.129.16-mel való kommunikációja nem vonatkozik a konfigurált hálózati biztonsági csoportokra.
- a 168.63.129.16 DNS-szolgáltatásokat biztosíthat a virtuális géphez. Ha ez nem megfelelő, akkor ez a forgalom blokkolható a virtuális gép helyi tűzfalán. Alapértelmezés szerint a DNS-kommunikáció nem vonatkozik a konfigurált hálózati biztonsági csoportokra, kivéve, ha kifejezetten a [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) szolgáltatás címkéjét kihasználják.
- Ha a virtuális gép egy terheléselosztó-készlet része, az [állapot](../load-balancer/load-balancer-custom-probe-overview.md) -mintavételi kommunikációnak engedélyezni kell, hogy a 168.63.129.16-ből származzon. Az alapértelmezett hálózati biztonsági csoport konfigurációjában olyan szabály van, amely lehetővé teszi a kommunikációt. Ez a szabály kihasználja a [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) szolgáltatás címkéjét. Ha azt szeretné, hogy ez a forgalom blokkolható legyen a hálózati biztonsági csoport konfigurálásával, ez azonban a sikertelen mintavételt eredményezi.

A nem virtuális hálózati forgatókönyvek (klasszikus) esetében az állapot-mintavétel egy magánhálózati IP-címről származik, és a rendszer nem használja a 168.63.129.16.

## <a name="next-steps"></a>További lépések

- [Biztonsági csoportok](security-overview.md)
- [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](manage-network-security-group.md)
