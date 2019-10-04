---
title: Azure VMware-megoldás CloudSimple-tűzfalakkal
description: További információ a CloudSimple privát felhőalapú tűzfalak tábláiról és a tűzfalszabályok használatáról.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877714"
---
# <a name="firewall-tables-overview"></a>A tűzfal táblái – áttekintés

A tűzfalszabályok a saját Felhőbeli erőforrásokra irányuló és onnan érkező hálózati forgalom szűrésére szolgáló szabályokat sorolja fel. A tűzfalakat egy VLAN-ra/alhálózatra is alkalmazhatja. A szabályok vezérlik a hálózati forgalmat a forrásoldali hálózat vagy az IP-cím, a célként megadott hálózat vagy az IP-cím között.

## <a name="firewall-rules"></a>Tűzfalszabályok

A következő táblázat a tűzfalszabály paramétereit ismerteti.

| Tulajdonság | Részletek |
| ---------| --------|
| **Name** | A tűzfalszabály egyedi azonosítására szolgáló név. |
| **prioritás** | 100 és 4096 közötti szám, amelynek a legmagasabb prioritása a 100. A szabályok feldolgozása prioritási sorrendben történik. Ha a forgalom egy szabály egyezését tapasztalja, a szabályok feldolgozása leáll. Ennek eredményeképpen az alacsonyabb prioritású szabályok, amelyek ugyanazokkal az attribútumokkal rendelkeznek, mint a magasabb prioritású szabályok, nem kerülnek feldolgozásra.  Ügyeljen az ütköző szabályok elkerülésére. |
| **Állapot követése** | A nyomon követés lehet állapot nélküli (privát felhő, Internet vagy VPN) vagy állapot-nyilvántartó (nyilvános IP).  |
| **Protocol (Protokoll)** | A lehetőségek közé tartoznak a következők: any, TCP vagy UDP. Ha az ICMP-t igényli, használja a bármelyiket. |
| **Direction (Irány)** | Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik. |
| **Művelet** | A szabályban definiált forgalom típusának engedélyezése vagy letiltása. |
| **Forrás** | IP-cím, osztály nélküli tartományok közötti útválasztási (CIDR) blokk (10.0.0.0/24, például) vagy bármely.  Egy tartomány, egy szolgáltatási címke vagy egy alkalmazás biztonsági csoport megadásával kevesebb biztonsági szabályt hozhat létre. |
| **Forrásport** | Az a port, amelyből a hálózati forgalom származik.  Megadhat egy különálló portot vagy portszámot, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. |
| **Cél** | IP-cím, osztály nélküli tartományok közötti útválasztási (CIDR) blokk (10.0.0.0/24, például) vagy bármely.  Egy tartomány, egy szolgáltatási címke vagy egy alkalmazás biztonsági csoport megadásával kevesebb biztonsági szabályt hozhat létre.  |
| **Célport** | Az a port, amelyhez a hálózati forgalom áramlik.  Megadhat egy különálló portot vagy portszámot, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia.|

### <a name="stateless"></a>Állapot nélküli

Az állapot nélküli szabály csak az egyes csomagokat vizsgálja, és a szabály alapján szűri azokat.  
A forgalom fordított irányú továbbításához további szabályokra lehet szükség.  Állapot nélküli szabályok használata a következő pontok közötti forgalomhoz:

* Privát felhők alhálózatai
* Helyszíni alhálózat és egy privát felhőalapú alhálózat
* Internetes forgalom a privát felhőkből

### <a name="stateful"></a>Nyilvántartó

 Egy állapot-nyilvántartó szabály ismeri az áthaladó kapcsolatokat. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva.  A nyilvános IP-címekre vonatkozó szabálytípus használatával szűrheti az internetről érkező forgalmat.

### <a name="default-rules"></a>Alapértelmezett szabályok

A következő alapértelmezett szabályok jönnek létre minden tűzfal-táblában.

|Priority|Name (Név)|Állapot követése|Direction|Forgalom típusa|Protocol|Source|Forrásport|Cél|Célport|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Nyilvántartó|Kimenő|Nyilvános IP-cím vagy internetes forgalom|Összes|Any|Any|Any|Any|Allow|
|65001|deny-all-from-internet|Nyilvántartó|Bejövő|Nyilvános IP-cím vagy internetes forgalom|Összes|Any|Any|Any|Any|Megtagadás|
|65002|allow-all-to-intranet|Állapot nélküli|Kimenő|Privát felhő belső vagy VPN-forgalma|Összes|Any|Any|Any|Any|Allow|
|65003|allow-all-from-intranet|Állapot nélküli|Bejövő|Privát felhő belső vagy VPN-forgalma|Összes|Any|Any|Any|Any|Allow|

## <a name="next-steps"></a>További lépések

* [Tűzfalszabályok és szabályok beállítása](firewall.md)
