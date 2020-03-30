---
title: Mi az IP-cím 168.63.129.16? | Microsoft Docs
description: Információ a 168.63.129.16 IP-címről és annak működéséről az erőforrásokkal.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114361"
---
# <a name="what-is-ip-address-1686312916"></a>Mi az IP-cím 168.63.129.16?

A 168.63.129.16 IP-cím egy virtuális nyilvános IP-cím, amely az Azure platform erőforrásaival való kommunikációs csatorna megkönnyítésére szolgál. Az ügyfelek tetszőleges címterületet definiálhatnak a privát virtuális hálózatukhoz az Azure-ban. Ezért az Azure platform erőforrásait egyedi nyilvános IP-címként kell bemutatni. Ez a virtuális nyilvános IP-cím a következő dolgokat könnyíti meg:

- Lehetővé teszi, hogy a virtuálisgép-ügynök kommunikáljon az Azure platformgal annak jelzésére, hogy "Kész" állapotban van.
- Lehetővé teszi a virtuális DNS-kiszolgálóval való kommunikációt, hogy szűrt névfeloldást biztosítson az egyéni DNS-kiszolgálóval nem rendelkező erőforrásoknak (például virtuális gépnek). Ez a szűrés biztosítja, hogy az ügyfelek csak az erőforrások állomásneveit tudják feloldani.
- Lehetővé teszi, hogy az [Azure terheléselosztó állapotának](../load-balancer/load-balancer-custom-probe-overview.md) meghatározásához az Azure terheléselosztó állapotának meghatározásához.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be az Azure-beli DHCP-szolgáltatásból.
- Engedélyezi a Vendégügynök szívverési üzeneteit a PaaS szerepkörhöz.

## <a name="scope-of-ip-address-1686312916"></a>Az IP-cím hatálya 168.63.129.16

A 168.63.129.16 nyilvános IP-címet minden régióban és nemzeti felhőben használják. Ez a különleges nyilvános IP-cím a Microsoft tulajdonában van, és nem fog változni. Azt javasoljuk, hogy engedélyezze ezt az IP-címet bármely helyi (a virtuális gép) tűzfal házirendekben (kimenő irányban). A speciális IP-cím és az erőforrások közötti kommunikáció biztonságos, mert csak a belső Azure platform képes beszerezni egy üzenetet erről az IP-címről. Ha ez a cím le van tiltva, nem várt viselkedés léphet fel a különböző esetekben. A 168.63.129.16 [a gazdacsomópont virtuális IP-címe,](../virtual-network/security-overview.md#azure-platform-considerations) és mint ilyen, nem vonatkozik rá a felhasználó által meghatározott útvonalak.

- A virtuálisgép-ügynök kimenő kommunikációt igényel a 80, 443, 32526 portokon a WireServer (168.63.129.16) portokon keresztül. Ezeket meg kell nyitni a helyi tűzfalon a virtuális gép. A 168.63.129.16-os portokon a kommunikációra nem vonatkoznak a konfigurált hálózati biztonsági csoportok.
- A 168.63.129.16 képes DNS-szolgáltatásokat nyújtani a virtuális gépnek. Ha ez nem kívánatos, ez a forgalom blokkolható a helyi tűzfalon a virtuális gép. Alapértelmezés szerint a DNS-kommunikáció nem vonatkozik a konfigurált hálózati biztonsági csoportok, kivéve, ha kifejezetten célzott kihasználva az [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags) szolgáltatás címke.
- Ha a virtuális gép egy terheléselosztó háttérkészlet része, [az állapotminta-kommunikáció](../load-balancer/load-balancer-custom-probe-overview.md) t a 168.63.129.16-ból kell származnia. Az alapértelmezett hálózati biztonsági csoport konfigurációja rendelkezik egy szabállyal, amely lehetővé teszi ezt a kommunikációt. Ez a szabály az [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags) szolgáltatáscímkét használja. Ha szükséges, ez a forgalom blokkolható a hálózati biztonsági csoport konfigurálásával, azonban ez sikertelen mintavételeket eredményez.

Egy nem virtuális hálózati forgatókönyv (Klasszikus) az állapotminta származik egy privát IP-cím és 168.63.129.16 nem használják.

## <a name="next-steps"></a>További lépések

- [Biztonsági csoportok](security-overview.md)
- [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](manage-network-security-group.md)
