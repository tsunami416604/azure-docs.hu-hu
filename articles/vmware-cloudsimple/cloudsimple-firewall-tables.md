---
title: Táblák – tűzfal CloudSimple – Azure VMware-megoldások
description: Ismerje meg CloudSimple magánfelhő-alapú tűzfal táblák és -tűzfalszabályok.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577345"
---
# <a name="firewall-tables-overview"></a>Tűzfal táblák áttekintése

Egy tűzfal táblázat és magánfelhőbeli erőforrásokat a hálózati forgalmat szűrő szabályokkal. Alhálózat vagy VLAN alkalmazhatja őket. A szabályok majd szabályozhatja a Forráshálózat vagy IP-címet, és a egy célhálózat vagy IP-cím közötti hálózati forgalmat.

## <a name="firewall-rules"></a>Tűzfalszabályok

A következő táblázat ismerteti a paramétereket a tűzfalszabály.

| Tulajdonság | Részletek |
| ---------| --------|
| **Name (Név)** | Egy név, amely egyedileg azonosítja a tűzfalszabályt, és a célra. |
| **prioritás** | Egy 100 és 4096 folyamatban van a legmagasabb prioritású 100 közötti szám. Szabályok feldolgozása prioritási sorrendben történik. Ha az adatforgalom több szabály egyezést, a szabály feldolgozása leáll. Ennek eredményeképpen kisebb prioritásokat lehet, magasabb prioritású szabályok azonos attribútumokkal rendelkező meglévő szabályok nem feldolgozása.  Körültekintően ütköző szabályok elkerülése érdekében. |
| **Állapot nyomon követése** | Követési lehet állapot nélküli (Magánfelhő, Internet vagy VPN) vagy állapotalapú (nyilvános IP-cím).  |
| **Protocol (Protokoll)** | Lehetőségek a következők bármely, TCP vagy UDP. Ha arra van szüksége, használatára. |
| **Direction (Irány)** | Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik. |
| **Művelet** | Engedélyezése vagy letiltása a forgalom a szabályban definiált típus. |
| **Forrás** | IP-címet, classless inter-domain routing (CIDR) letiltása (például 10.0.0.0/24), vagy bármely.  Adjon meg egy tartományt, egy szolgáltatáscímke vagy alkalmazásbiztonsági csoportok lehetővé teszi, hogy kevesebb biztonsági szabályt kell majd létrehoznia. |
| **Forrásport** | A port a hálózati forgalom származik.  Megadhat egy egyéni portot vagy porttartományt, például a 443-as vagy a 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. |
| **cél** | IP-címet, classless inter-domain routing (CIDR) letiltása (például 10.0.0.0/24), vagy bármely.  Adjon meg egy tartományt, egy szolgáltatáscímke vagy alkalmazásbiztonsági csoportok lehetővé teszi, hogy kevesebb biztonsági szabályt kell majd létrehoznia.  |
| **Célport** | A port, amelyhez a hálózati forgalmat.  Megadhat egy egyéni portot vagy porttartományt, például a 443-as vagy a 8000-8080. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia.|

### <a name="stateless"></a>Állapot nélküli

Állapot nélküli szabály csak az egyes csomagok megvizsgálja, és azokat a szabály alapján szűri.  
További szabályokat a fordított irányú adatforgalmat szükséges lehet.  A következő pontok közötti forgalom állapotmentes szabályok használata:

* A privát felhők alhálózatok
* Helyszíni alhálózat és a egy Magánfelhő-alhálózat
* Internetes forgalmat a Magánfelhők létrehozása

### <a name="stateful"></a>Állapotalapú

 Állapot-nyilvántartó szabály, amely engedi át a kapcsolatok tisztában. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva.  Az internetről érkező forgalom szűrésére nyilvános IP-címeket használja ezt a szabálytípust.

### <a name="default-rules"></a>Alapértelmezett szabályok

Következő alapértelmezett szabályokat minden tűzfal táblán jönnek létre.

|Prioritás|Name (Név)|Állapot nyomon követése|Direction|Forgalomtípus|Protocol|source|Forrásport|Cél|Célport|Műveletek|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Állapotalapú|Kimenő|Nyilvános IP- vagy internetes forgalom|Összes|Bármely|Bármely|Bármely|Bármely|Engedélyezés|
|65001|deny-all-from-internet|Állapotalapú|Bejövő|Nyilvános IP- vagy internetes forgalom|Összes|Bármely|Bármely|Bármely|Bármely|Megtagadás|
|65002|allow-all-to-intranet|Állapot nélküli|Kimenő|A Magánfelhő belső vagy a VPN-forgalom|Összes|Bármely|Bármely|Bármely|Bármely|Engedélyezés|
|65003|allow-all-from-intranet|Állapot nélküli|Bejövő|A Magánfelhő belső vagy a VPN-forgalom|Összes|Bármely|Bármely|Bármely|Bármely|Engedélyezés|

## <a name="next-steps"></a>További lépések

* [Tűzfal-táblák és a szabályok beállítása](https://docs.azure.cloudsimple.com/firewall/)