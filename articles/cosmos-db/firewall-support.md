---
title: IP-tűzfal Az Azure Cosmos-fiókokhoz
description: Megtudhatja, hogyan védje meg az Azure Cosmos DB-adatokat az IP-hozzáférés-vezérlési szabályzatok használatával a tűzfal támogatásához.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241079"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-tűzfal az Azure Cosmos DB-ben

A fiókjában tárolt adatok biztonságossá tétele érdekében az Azure Cosmos DB egy titkos alapú engedélyezési modellt támogat, amely erős kivonatalapú üzenethitelesítési kódot (HMAC) használ. Emellett az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlésa a bejövő tűzfal támogatása. Ez a modell hasonló a hagyományos adatbázis-rendszer tűzfalszabályaihoz, és további biztonsági szintet biztosít a fiókjának. A tűzfalak konfigurálhatja az Azure Cosmos-fiók csak egy jóváhagyott gépkészletről és/vagy felhőszolgáltatásokból érhető el. Az Azure Cosmos-adatbázisban tárolt adatokhoz való hozzáférés a jóváhagyott gép- és szolgáltatáskészletektovábbra is megköveteli, hogy a hívó érvényes engedélyezési jogkivonatot mutasson be.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>IP-hozzáférés-vezérlés – áttekintés

Alapértelmezés szerint az Azure Cosmos-fiók elérhető az internetről, mindaddig, amíg a kérelemhez egy érvényes engedélyezési jogkivonat tartozik. Az IP-házirend-alapú hozzáférés-vezérlés konfigurálásához a felhasználónak meg kell adnia a CIDR (Osztály nélküli tartományok közötti útválasztás) képernyőn található IP-címek vagy IP-címtartományok készletét, hogy az ügyfél IP-címek engedélyezett listájaként szerepelhessen egy adott Azure Cosmos-fiók eléréséhez. A konfiguráció alkalmazása után az engedélyezett listán kívüli gépekről származó kérelmek 403 -as (Tiltott) választ kapnak. IP-tűzfal használata esetén ajánlott engedélyezni, hogy az Azure Portal hozzáférjen a fiókjához. Hozzáférés szükséges az adatkezelő használatához, valamint az Azure Portalon megjelenő fiók metrikáinak lekéréséhez. Adatkezelő használatakor, amellett, hogy az Azure Portal hozzáférhet a fiókjához, a tűzfal beállításait is frissítenie kell, hogy hozzáadja az aktuális IP-címét a tűzfalszabályokhoz. Vegye figyelembe, hogy a tűzfal módosításai propagálása akár 15 min is eltarthat. 

Az IP-alapú tűzfal at alhálózati és virtuális hálózati hozzáférés-vezérléssel kombinálhatja. Kombinálásával, korlátozhatja a hozzáférést bármely forrás, amely egy nyilvános IP-cím és/vagy egy adott alhálózat a virtuális hálózaton belül. Ha többet szeretne tudni az alhálózat és a virtuális hálózat alapú hozzáférés-vezérlés használatáról, olvassa el az [Access Azure Cosmos DB-erőforrások virtuális hálózatokból történő használatát.](vnet-service-endpoint.md)

Összefoglalva, engedélyezési jogkivonat mindig szükség van egy Azure Cosmos-fiók eléréséhez. Ha az IP-tűzfal és a vnet-hozzáférés-vezérlési lista (ACLs) nincs beállítva, az Azure Cosmos-fiók elérhető az engedélyezési jogkivonattal. Miután az IP-tűzfal vagy a VNET ACLs vagy mindkettő be van állítva az Azure Cosmos-fiókban, csak a megadott forrásokból (és az engedélyezési jogkivonattal) származó kérelmek kapnak érvényes válaszokat. 

## <a name="next-steps"></a>További lépések

Ezután az IP-tűzfalat vagy a VNET-szolgáltatás végpontját a következő dokumentumok használatával konfigurálhatja a fiókjához:

* [Az IP-tűzfal beállítása az Azure Cosmos-fiókhoz](how-to-configure-firewall.md)
* [Az Azure Cosmos DB erőforrásainak elérése virtuális hálózatokból](vnet-service-endpoint.md)
* [A virtuális hálózati szolgáltatás végpontjának konfigurálása az Azure Cosmos-fiókhoz](how-to-configure-vnet-service-endpoint.md)




