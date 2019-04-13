---
title: IP-tűzfal az Azure Cosmos-fiókok
description: Ismerje meg, hogyan teheti biztonságossá az Azure Cosmos DB-adatai tűzfaltámogatás IP hozzáférés-vezérlési házirendeket használatával.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: c5c103fcb7f80533f6f34cb51fd661364d77d6ef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549012"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Az Azure Cosmos DB IP-tűzfal

A fiókban tárolt adatok védelmében, az Azure Cosmos DB támogatja, amelyek már használja egy erős kivonat-alapú üzenethitelesítési kód (HMAC) titkos alapú engedélyezési modell. Továbbá a Azure Cosmos DB támogatja IP-alapú hozzáférés-vezérlést a tűzfaltámogatás bejövő. Ez a modell a tűzfalszabályok a hagyományos adatbázisok rendszer hasonló, és egy további szintű fiókjába biztonságot nyújt. Tűzfal konfigurálhatja az Azure Cosmos fiók elérésére csak egy jóváhagyott gépekre és/vagy felhőszolgáltatásokra. Az ezen jóváhagyott készletek gépek és szolgáltatások az Azure Cosmos-adatbázisban tárolt adatokhoz való hozzáférés továbbra is szükséges a hívót annak érvényes engedélyezési jogkivonat található.

## <a id="ip-access-control-overview"></a>IP hozzáférés-vezérlés áttekintése

Alapértelmezés szerint az Azure Cosmos-fiók internetről érhető el, mindaddig, amíg a kérelem érvényes engedélyezési jogkivonat kíséri. IP csoportházirend-alapú hozzáférés-vezérlés konfigurálása, a felhasználónak meg kell adni a készlet IP-cím vagy IP-címtartományokat (Classless Inter-Domain Routing) CIDR formátumban része, mint az engedélyezett listára ügyfél IP-címek egy adott Azure Cosmos-fiók eléréséhez. Ez a konfiguráció alkalmazása után minden olyan engedélyezési listán szereplő gépekről származó kérelmek kapnak a 403-as (tiltott) választ. IP-tűzfal használata esetén ajánlott ahhoz, hogy az Azure portál hozzáférjen a fiókjához. Hozzáférés megadása kötelező való használatának engedélyezése az adatkezelőt, valamint lekérni a metrikák a fiók, amely az Azure Portalon a helyrendszerszerepkörökre. Az adatkezelő segítségével, mellett az Azure portál hozzáférjen a fiókjához, így ha is frissíteni szeretné a tűzfal beállításait a tűzfalszabályok hozzáadása az aktuális IP-címhez. Vegye figyelembe, hogy a tűzfal eltarthat propagálása akár 15 perc. 

IP-alapú tűzfal-alhálózattal és virtuális hálózati hozzáférés-vezérlés kombinálhatók. Kombinálja őket, korlátozhatja a hozzáférést bármilyen forrásból, amely a nyilvános IP-címet és/vagy virtuális hálózaton belüli adott alhálózatokról. Ismerje meg az alhálózat és a VNET-alapú hozzáférés-vezérlés használatával kapcsolatos információkért tekintse meg a [hozzáférés az Azure Cosmos DB-erőforrások virtuális hálózatokról](vnet-service-endpoint.md).

Összefoglalva, engedélyezési jogkivonat mindig egy Azure Cosmos-fiókja eléréséhez szükséges. Ha IP-tűzfal és a virtuális hálózati hozzáférés-vezérlési lista (ACL) nem állította be, az Azure Cosmos-fiók érhetők el az engedélyezési jogkivonatot. Az IP-tűzfal vagy a virtuális hálózati hozzáférés-vezérlési listák vagy mindkettő beállítását követően az Azure Cosmos-fiókon, csak a megadott forrásokból származó (és engedélyezési jogkivonat) kérései érvényes választ kaphat. 

## <a name="next-steps"></a>További lépések

Ezután konfigurálhatja IP-tűzfal vagy a szolgáltatásvégpont-fiókja a következő docs:

* [Az Azure Cosmos-fiókhoz tartozó IP-tűzfal konfigurálása](how-to-configure-firewall.md)
* [Azure Cosmos DB-erőforrások eléréséhez a virtuális hálózatok](vnet-service-endpoint.md)
* [Az Azure Cosmos-fiókhoz tartozó virtuális hálózati szolgáltatásvégpont beállítása](how-to-configure-vnet-service-endpoint.md)




