---
title: Azure Cosmos DB biztonsági attribútumai
description: A Azure Cosmos DB értékelésére szolgáló biztonsági attribútumok ellenőrzőlistája
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: f1b578ff70e8a2795ba886e6d0c75eff521f77c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615535"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Azure Cosmos DB biztonsági attribútumai

Ez a cikk a Azure Cosmos DB beépített biztonsági attribútumokat dokumentálja.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban (például kiszolgálóoldali titkosítás, ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás és egyéb titkosítási funkciók) | Igen | Alapértelmezés szerint minden Cosmos-adatbázis és-biztonsági másolat titkosítva van; lásd [](database-encryption-at-rest.md): adattitkosítás Azure Cosmos DBban. Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás nem támogatott. |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az összes Azure Cosmos DB-adatforgalom titkosítva van az átvitel során. |
| Titkosítási kulcsok kezelését (CMK, BYOK stb.)| Nem |  |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | Csak a Tables API Premiumban. Nem minden API támogatja ezt a funkciót. Lásd [: a Azure Cosmos db bemutatása: Table API](table-introduction.md). |
| Titkosított API-hívások| Igen | A Azure Cosmos DB összes kapcsolata támogatja a HTTPS protokollt. A Azure Cosmos DB a TLS 1,2-kapcsolatokat is támogatja, de ez még nincs érvényesítve. Ha az ügyfelek kikapcsolják az alacsonyabb szintű TLS-t a végére, akkor a Cosmos DBhoz csatlakozhatnak.  |

## <a name="network-segmentation"></a>Hálózati szegmentálás

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Igen | A VNet szolgáltatás végpontja lehetővé teszi egy Azure Cosmos DB-fiók konfigurálását, hogy csak a virtuális hálózat (VNet) egy adott alhálózatáról engedélyezze a hozzáférést. A VNet-hozzáférés is kombinálható a tűzfalszabályok használatával.  Lásd: [hozzáférés Azure Cosmos db virtuális hálózatokból](VNet-service-endpoint.md). |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A tűzfal támogatásával beállíthatja az Azure Cosmos-fiókot úgy, hogy csak az IP-címek jóváhagyott készletéről, az IP-címek tartományáról és/vagy a felhőalapú szolgáltatásokból engedélyezze a hozzáférést. Lásd: [az IP-tűzfal konfigurálása Azure Cosmos DBban](how-to-configure-firewall.md).|
| Kényszerített bújtatás támogatása| Igen | Konfigurálható az ügyféloldali oldalon azon a VNet, ahol a virtuális gépek találhatók.   |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A rendszer a Azure Cosmos DB küldött összes kérelmet naplózza. Az Azure [monitoring](../azure-monitor/overview.md), az Azure-metrikák, az Azure audit naplózása támogatott.  Az adatsík-kérelmeknek, a lekérdezési futtatókörnyezet statisztikáinak, a lekérdezési szövegeknek és a MongoDB kérelmeknek megfelelő adatokat naplózhatja. Emellett riasztásokat is beállíthat. |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Authentication| Igen | Igen, az adatbázis-fiók szintjén; az adatsík szintjén Cosmos DB erőforrás-tokeneket és kulcs-hozzáférést használ. |
| Authorization| Igen | Támogatott az Azure Cosmos-fiókban főkulcsokkal (elsődleges és másodlagos) és erőforrás-tokenekkel. Olvasási/írási vagy olvasási hozzáférés csak a főkulcsokkal rendelkező adatokhoz. Az erőforrás-tokenek korlátozott ideig férnek hozzá az erőforrásokhoz, például a dokumentumokhoz és a tárolóhoz. |


## <a name="audit-trail"></a>Naplózási nyomvonal

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Azure-Tevékenységnaplók a fiók szintű műveletekhez, például tűzfalakhoz, virtuális hálózatok, kulcsok eléréséhez és IAM-hoz. |
| Adatsíkok naplózása és naplózása | Igen | Diagnosztika-figyelési naplózás a tárolók szintjén végrehajtott műveletekhez, például tároló létrehozása, átviteli sebesség, indexelési házirendek és szifilisz-műveletek a dokumentumokon. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Nem  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>A Cosmos DB további biztonsági attribútumai

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Több eredetű erőforrás-megosztás (CORS) | Igen | Lásd: a [több eredetű erőforrás-megosztás konfigurálása (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
