---
title: Azure VMware-megoldás a CloudSimple által – tűzfaltáblák
description: További információ a CloudSimple felhőalapú tűzfaltábláiról és tűzfalszabályairól.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025044"
---
# <a name="firewall-tables-overview"></a>Tűzfaltáblák – áttekintés

A tűzfaltábla felsorolja azokat a szabályokat, amelyek szűrhetik a hálózati forgalmat a magánfelhő-erőforrásokba és a magánfelhő-erőforrásokból. Tűzfaltáblákat alkalmazhat vlan/alhálózatra. A szabályok szabályozzák a hálózati forgalmat a forráshálózat vagy IP-cím és a célhálózat vagy IP-cím között.

## <a name="firewall-rules"></a>Tűzfalszabályok

Az alábbi táblázat a tűzfalszabály paramétereit ismerteti.

| Tulajdonság | Részletek |
| ---------| --------|
| **Név** | Olyan név, amely egyedileg azonosítja a tűzfalszabályt és annak célját. |
| **Prioritás** | Egy 100 és 4096 közötti szám, a 100 a legfontosabb. A szabályok feldolgozása prioritási sorrendben történik. Amikor a forgalom szabályegyezésbe ütközik, a szabályfeldolgozás leáll. Ennek eredményeképpen az alacsonyabb prioritású szabályok, amelyek ugyanazokkal az attribútumokkal rendelkeznek, mint a magasabb prioritású szabályok nem dolgoznak fel.  Ügyeljen arra, hogy elkerüljék az egymásnak ellentmondó szabályokat. |
| **Állapotkövetés** | A követés lehet állapotnélküli (privát felhő, internet vagy VPN) vagy állapotalapú (nyilvános IP).  |
| **Protocol (Protokoll)** | A beállítások közé tartozik a Bármely, A TCP vagy az UDP. Ha ICMP-re van szüksége, használja az Any(Any) (Bármelyik) ( Bármilyen) |
| **Irányba** | Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik. |
| **Művelet** | Engedélyezés vagy megtagadás a szabályban definiált forgalom típusára vonatkozóan. |
| **Forrás** | IP-cím, osztály nélküli tartományok közötti útválasztási (CIDR) blokk (például 10.0.0.0/24) vagy Bármely.  Tartomány, szolgáltatáscímke vagy alkalmazásbiztonsági csoport megadása lehetővé teszi kevesebb biztonsági szabály létrehozását. |
| **Forrásport** | Az a port, ahonnan a hálózati forgalom származik.  Megadhat egy adott portot vagy porttartományt, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. |
| **Cél** | IP-cím, osztály nélküli tartományok közötti útválasztási (CIDR) blokk (például 10.0.0.0/24) vagy Bármely.  Tartomány, szolgáltatáscímke vagy alkalmazásbiztonsági csoport megadása lehetővé teszi kevesebb biztonsági szabály létrehozását.  |
| **Célport** | Az a port, amelyre a hálózati forgalom áramlik.  Megadhat egy adott portot vagy porttartományt, például 443 vagy 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia.|

### <a name="stateless"></a>Állapot nélküli

Az állapotnélküli szabály csak az egyes csomagokat vizsgálja, és szűri őket a szabály alapján.  
A fordított irányú forgalomhoz további szabályokra lehet szükség.  Állapotnélküli szabályok használata a következő pontok közötti forgalomhoz:

* Privát felhők alhálózatai
* Helyszíni alhálózat és magánfelhő-alhálózat
* Internetes forgalom a privát felhők

### <a name="stateful"></a>Állapotalapú alkalmazások és szolgáltatások

 Az állapotalapú szabály tisztában van az ezen áthaladó kapcsolatokkal. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva.  Ezzel a szabálytípussal nyilvános IP-címek az internetről érkező forgalom szűréséhez használható.

### <a name="default-rules"></a>Alapértelmezett szabályok

A következő alapértelmezett szabályok jönnek létre minden tűzfaltáblában.

|Prioritás|Név|Állapotkövetés|Irány|Forgalom típusa|Protocol (Protokoll)|Forrás|Forrásport|Cél|Célport|Műveletek|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|lehetővé teszi-all-to-internet|Állapotalapú alkalmazások és szolgáltatások|Kimenő|Nyilvános IP- vagy internetes forgalom|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|
|65001|deny-all-from-internet|Állapotalapú alkalmazások és szolgáltatások|Bejövő|Nyilvános IP- vagy internetes forgalom|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Megtagadás|
|65002|az összes ről intranetre engedélyezés|Állapot nélküli|Kimenő|Privát felhő belső vagy VPN-forgalma|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|
|65003|az intranetről való engedélyezés|Állapot nélküli|Bejövő|Privát felhő belső vagy VPN-forgalma|Összes|Bármelyik|Bármelyik|Bármelyik|Bármelyik|Engedélyezés|

## <a name="next-steps"></a>További lépések

* [Tűzfaltáblák és -szabályok beállítása](firewall.md)
