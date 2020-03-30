---
title: Melyek az Azure Firewall Manager architektúra beállításai?
description: Hasonlítsa össze és kontraszt hub virtuális hálózat vagy biztonságos virtuális központ architektúrák az Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444575"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Melyek az Azure Firewall Manager architektúra beállításai?

Az Azure Firewall Manager két hálózati architektúratípus biztonságkezelését is biztosítja:

- **biztonságos virtuális elosztó**

   Az [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) a Microsoft által felügyelt erőforrás, amely lehetővé teszi a központi és küllős architektúrák egyszerű létrehozását. Ha egy ilyen hubhoz biztonsági és útválasztási házirendek vannak társítva, azt *[biztonságos virtuális elosztónak nevezzük.](secured-virtual-hub.md)* 
- **központi virtuális hálózat**

   Ez egy szabványos Azure virtuális hálózat, amelyet saját maga hoz létre és kezel. Ha ilyen hubhoz biztonsági házirendek vannak társítva, azt *központi virtuális hálózatnak nevezzük.* Jelenleg csak az Azure tűzfalszabályzat támogatott. Társklazon virtuális hálózatok, amelyek tartalmazzák a számítási feladatok kiszolgálók és szolgáltatások. A tűzfalakat olyan önálló virtuális hálózatokban is kezelheti, amelyek nem küllőkre néznek.

## <a name="comparison"></a>Összehasonlítás

Az alábbi táblázat összehasonlítja a két architektúra-beállítást, és segít eldönteni, hogy melyik felel meg a szervezet biztonsági követelményeinek:


|  |**Hub virtuális hálózata**|**Biztonságos virtuális elosztó**  |
|---------|---------|---------|
|**Mögöttes erőforrás**     |Virtuális hálózat|Virtuális WAN-központ|
|**Hub & küllő**     |Virtuális hálózati társviszony-létesítést használ|Automatikus használata a központi virtuális hálózati kapcsolat használatával|
|**On-prem kapcsolat**     |VPN-átjáró akár 10 Gb/s és 30 S2S-kapcsolatig; ExpressRoute|Több méretezhető VPN-átjáró 20 Gb/s és 1000 S2S-kapcsolaton keresztül; Expressz útvonal|
|**Automatizált elágazási kapcsolat az SDWAN használatával**      |Nem támogatott|Támogatott|
|**Hubok régiónként**     |Több virtuális hálózat régiónként|Egyetlen virtuális központ régiónként. Több hub is lehetséges több virtuális WAN-nel|
|**Azure Firewall – több nyilvános IP-cím**      |Ügyfél által megadott|Automatikus annekt. Ahhoz, hogy elérhető legyen a GA.|
|**Az Azure tűzfal rendelkezésre állási zónái**     |Támogatott|Nem érhető el előzetes verzióban. Ahhoz, hogy elérhető legyen a GA|
|**Speciális internetbiztonság külső biztonsági partnerekkel, mint szolgáltatási partnerek**     |Az ügyfél által létrehozott és felügyelt VPN-kapcsolat a választott partnerszolgáltatáshoz|Automatizált a Megbízható biztonsági partnerek folyamatán és partnerkezelési felületén keresztül|
|**Központosított útvonalkezelés a forgalom nak a hubra való irányításához**     |Ügyfél által felügyelt felhasználó által definiált útvonal|BGP használatával támogatott|
|**Web Application Firewall az Application Gatewayen** |Virtuális hálózat ban támogatott|Jelenleg támogatott küllős hálózat|
|**Hálózati virtuális berendezés**|Virtuális hálózat ban támogatott|Jelenleg támogatott küllős hálózat|

## <a name="next-steps"></a>További lépések

- Tekintse át [az Azure Firewall Manager előzetes telepítésének áttekintését – áttekintés](deployment-overview.md)
- További információ a [biztonságos virtuális központokról.](secured-virtual-hub.md)