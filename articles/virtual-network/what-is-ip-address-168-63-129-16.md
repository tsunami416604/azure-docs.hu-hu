---
title: Mi az, hogy a 168.63.129.16 IP-cím? | Microsoft Docs
description: Ismerje meg a 168.63.129.16 IP-címet, és hogyan működik az erőforrásokhoz.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296038"
---
# <a name="what-is-ip-address-1686312916"></a>Mi az, hogy a 168.63.129.16 IP-cím?

168.63.129.16 IP-cím virtuális nyilvános IP-címet, amely a kommunikációs csatornát, az Azure platform erőforrásainak megkönnyítésére szolgál. Ügyfelek definiálhat bármilyen címteret a saját privát virtuális hálózatot az Azure-ban. Ezért az Azure-platform-erőforrások egyedi, nyilvános IP-címet kell bemutatni. A nyilvános virtuális IP-cím lehetővé teszi az alábbiakat:

- Lehetővé teszi, hogy a Virtuálisgép-ügynök kommunikálni az Azure platformon, hogy jelezze, hogy a "Kész" állapotban van.
- Lehetővé teszi a kommunikációt a DNS-kiszolgáló az erőforrások (például a virtuális gép), amelyek nem rendelkeznek egy egyéni DNS-kiszolgáló szűrt névfeloldást biztosítanak. Ez a szűrés gondoskodik arról, hogy a felhasználók is csak az erőforrások állomásnevét feloldani.
- Lehetővé teszi egy elosztott terhelésű készlet virtuális gépek állapota határozza meg, hogy a terheléselosztó állapot-mintavételei.
- Lehetővé teszi a Vendégügynök szívverés a PaaS-szerepkör.

## <a name="scope-of-ip-address-1686312916"></a>Hatókör a 168.63.129.16 IP-cím

Virtuális nyilvános IP-cím 168.63.129.16 minden régióban, és minden országos felhők használatban van. Ez speciális nyilvános IP-cím nem változik. Ez az alapértelmezett hálózati biztonsági csoportszabály által engedélyezett. Azt javasoljuk, hogy lehetővé tegye az IP-címet a bármilyen helyi tűzfal-házirendek. Ez speciális IP-cím és az erőforrások közötti kommunikáció biztonságos-e, mert csak a belső Azure platformon is forrás IP-cím üzenetét. Ha ez a cím le van tiltva, nem várt viselkedést számos forgatókönyv esetében fordul elő.

Ezenkívül várható, hogy a végponthoz konfigurált virtuális nyilvános IP-címről érkező 168.63.129.16 haladjon a forgalom egy [terheléselosztó állapotmintát](../load-balancer/load-balancer-custom-probe-overview.md). A nem virtuális hálózat a forgatókönyvben az állapotminta forrása egy magánhálózati IP-címet. 

## <a name="next-steps"></a>További lépések

- [Biztonsági csoportok](security-overview.md)
- [Hálózati biztonsági csoport létrehozása, módosítása vagy törlése](manage-network-security-group.md)
