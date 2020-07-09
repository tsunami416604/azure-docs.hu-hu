---
title: IP-tűzfal Azure Cosmos-fiókokhoz
description: Megtudhatja, hogyan védheti meg Azure Cosmos DB-adatvédelmet az IP-hozzáférés-vezérlési házirendek használatával a tűzfal támogatásához.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "66241079"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-tűzfal az Azure Cosmos DB-ben

A fiókjában tárolt adatokat a Azure Cosmos DB támogatja egy olyan titkos alapú engedélyezési modellt, amely erős kivonatoló alapú üzenethitelesítő kód (HMAC) használ. Emellett a Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. Ez a modell hasonló a hagyományos adatbázisrendszer tűzfalszabályok esetében, és további biztonsági szintet biztosít a fiókja számára. A tűzfalakkal beállíthatja, hogy az Azure Cosmos-fiók csak jóváhagyott gépekből és/vagy felhőalapú szolgáltatásokból legyen elérhető. Az Azure Cosmos-adatbázisban tárolt adatoknak ezen jóváhagyott készletekből és szolgáltatásokból való elérése továbbra is megköveteli, hogy a hívó érvényes engedélyezési jogkivonatot jelentsen.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Az IP-hozzáférés-vezérlés áttekintése

Alapértelmezés szerint az Azure Cosmos-fiók elérhető az internetről, feltéve, hogy a kérést érvényes engedélyezési jogkivonat kíséri. Az IP-házirend alapú hozzáférés-vezérlés konfigurálásához a felhasználónak meg kell adnia az IP-címek vagy IP-címtartományok készletét a CIDR (osztály nélküli tartományok közötti útválasztás) képernyőn, hogy az ügyfél IP-címeinek engedélyezett listája szerepeljen egy adott Azure Cosmos-fiók eléréséhez. A konfiguráció alkalmazása után az ezen engedélyezett listán kívüli gépekről származó kérelmek 403 (tiltott) választ kapnak. Ha IP-tűzfalat használ, javasoljuk, hogy engedélyezze a Azure Portal számára a fiók elérését. Hozzáférés szükséges az adatkezelő használatának engedélyezéséhez, valamint a Azure Portalban megjelenített fiók metrikáinak lekéréséhez. Az adatkezelő használatakor a Azure Portal hozzáférésének engedélyezése mellett a tűzfal beállításait is frissítenie kell, hogy az aktuális IP-címet hozzáadja a tűzfalszabályok számára. Vegye figyelembe, hogy a tűzfal módosításai akár 15min is eltarthat. 

Az IP-alapú tűzfalat az alhálózat és a VNET hozzáférés-vezérlés használatával kombinálhatja. Ezek kombinálásával korlátozhatja a hozzáférést bármely olyan forráshoz, amely nyilvános IP-címmel és/vagy a VNET-n belül egy adott alhálózattal rendelkezik. Az alhálózati és a VNET hozzáférés-vezérlés használatáról további információt a [virtuális hálózatok Azure Cosmos db erőforrásainak elérését](vnet-service-endpoint.md)ismertető témakörben talál.

Összefoglalva, az engedélyezési jogkivonat mindig szükséges az Azure Cosmos-fiók eléréséhez. Ha nincs beállítva az IP-tűzfal és a VNET Access Control listája (ACL), az Azure Cosmos-fiók az engedélyezési jogkivonattal érhető el. Miután az IP-tűzfal vagy a VNET ACL-je vagy mindkettő be van állítva az Azure Cosmos-fiókon, csak a megadott forrásokból (és az engedélyezési jogkivonattal) származó kérelmek érvényes válaszokat kapnak. 

## <a name="next-steps"></a>További lépések

A következő docs használatával konfigurálhatja a fiókjához tartozó IP-tűzfalat vagy VNET szolgáltatási végpontot:

* [Az IP-tűzfal konfigurálása az Azure Cosmos-fiókhoz](how-to-configure-firewall.md)
* [Virtuális hálózatok Azure Cosmos DB erőforrásainak elérése](vnet-service-endpoint.md)
* [Virtuális hálózati szolgáltatás végpontjának konfigurálása az Azure Cosmos-fiókhoz](how-to-configure-vnet-service-endpoint.md)




