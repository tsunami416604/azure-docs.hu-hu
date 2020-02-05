---
title: Azure VMware-megoldások (AVS) – tűzfalszabályok
description: Ismerkedjen meg az AVS Private Cloud Firewall-táblázatokkal és-tűzfalszabályok használatával.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 631ccb33e14586edce71752826f134c1e50819c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025044"
---
# <a name="firewall-tables-overview"></a>A tűzfal táblái – áttekintés

A tűzfalszabályok az AVS Private Cloud Resources szolgáltatásba irányuló és onnan érkező hálózati forgalom szűrésére szolgáló szabályokat listázza. A tűzfalakat egy VLAN-ra/alhálózatra is alkalmazhatja. A szabályok vezérlik a hálózati forgalmat a forrásoldali hálózat vagy az IP-cím, a célként megadott hálózat vagy az IP-cím között.

## <a name="firewall-rules"></a>Tűzfalszabályok

A következő táblázat a tűzfalszabály paramétereit ismerteti.

| Tulajdonság | Részletek |
| ---------| --------|
| **Name (Név)** | A tűzfalszabály egyedi azonosítására szolgáló név. |
| **Priority (Prioritás)** | 100 és 4096 közötti szám, amelynek a legmagasabb prioritása a 100. A szabályok feldolgozása prioritási sorrendben történik. Ha a forgalom egy szabály egyezését tapasztalja, a szabályok feldolgozása leáll. Ennek eredményeképpen az alacsonyabb prioritású szabályok, amelyek ugyanazokkal az attribútumokkal rendelkeznek, mint a magasabb prioritású szabályok, nem kerülnek feldolgozásra. Ügyeljen az ütköző szabályok elkerülésére. |
| **Állapot követése** | A nyomon követés lehet állapot nélküli (AVS Private Cloud, Internet vagy VPN) vagy állapot-nyilvántartó (nyilvános IP).  |
| **Protocol (Protokoll)** | A lehetőségek közé tartoznak a következők: any, TCP vagy UDP. Ha az ICMP-t igényli, használja a bármelyiket. |
| **Direction (Irány)** | Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik. |
| **Művelet** | A szabályban definiált forgalom típusának engedélyezése vagy letiltása. |
| **Forrás** | IP-cím, osztály nélküli Inter-domain Routing (CIDR) blokk (10.0.0.0/24, például) vagy bármely IP-cím.  Egy tartomány, egy szolgáltatási címke vagy egy alkalmazás biztonsági csoport megadásával kevesebb biztonsági szabályt hozhat létre. |
| **Forrásport** | Az a port, amelyből a hálózati forgalom származik.  Megadhat egy különálló portot vagy portszámot, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. |
| **Cél** | IP-cím, osztály nélküli Inter-domain Routing (CIDR) blokk (10.0.0.0/24, például) vagy bármely IP-cím.  Egy tartomány, egy szolgáltatási címke vagy egy alkalmazás biztonsági csoport megadásával kevesebb biztonsági szabályt hozhat létre.  |
| **Célport** | Az a port, amelyhez a hálózati forgalom áramlik.  Megadhat egy különálló portot vagy portszámot, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia.|

### <a name="stateless"></a>Állapot nélküli

Az állapot nélküli szabály csak az egyes csomagokat vizsgálja, és a szabály alapján szűri azokat. A forgalom fordított irányú továbbításához további szabályokra lehet szükség. Állapot nélküli szabályok használata a következő pontok közötti forgalomhoz:

* Az AVS privát felhők alhálózatai
* Helyszíni alhálózat és egy AVS Private Cloud subnet
* Internetes forgalom az AVS privát felhőkből

### <a name="stateful"></a>Nyilvántartó

 Egy állapot-nyilvántartó szabály ismeri az áthaladó kapcsolatokat. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva. A nyilvános IP-címekre vonatkozó szabálytípus használatával szűrheti az internetről érkező forgalmat.

### <a name="default-rules"></a>Alapértelmezett szabályok

A következő alapértelmezett szabályok jönnek létre minden tűzfal-táblában.

|Prioritás|Name (Név)|Állapot követése|Irány|Forgalom típusa|Protocol (Protokoll)|Forrás|Forrásport|Cél|Célport|Műveletek|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|a-all-to-Internet engedélyezése|Nyilvántartó|Kimenő|Nyilvános IP-cím vagy internetes forgalom|Mind|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|
|65001|internetről történő megtagadás|Nyilvántartó|Bejövő|Nyilvános IP-cím vagy internetes forgalom|Mind|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Megtagadás|
|65002|összes-az-intranetes engedélyezése|Állapot nélküli|Kimenő|AVS Private Cloud belső vagy VPN-forgalom|Mind|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|
|65003|intranetes engedélyezése|Állapot nélküli|Bejövő|AVS Private Cloud belső vagy VPN-forgalom|Mind|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|

## <a name="next-steps"></a>Következő lépések

* [Tűzfalszabályok és szabályok beállítása](firewall.md)
