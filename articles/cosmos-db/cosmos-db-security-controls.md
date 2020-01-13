---
title: A Azure Cosmos DB biztonsági vezérlői
description: Tekintse át a biztonsági vezérlők, például a hálózat, a figyelés, az identitás és az adatvédelem ellenőrző listáját, hogy kiértékelje Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: 1ac4d12f58977497642cdb0706ab7e85e9a4db64
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913082"
---
# <a name="security-controls-for-azure-cosmos-db"></a>A Azure Cosmos DB biztonsági vezérlői

Ez a cikk a Azure Cosmos DB beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen |  |
| VNet-befecskendezés támogatása| Igen | A VNet szolgáltatás végpontja lehetővé teszi egy Azure Cosmos DB-fiók konfigurálását, hogy csak a virtuális hálózat (VNet) egy adott alhálózatáról engedélyezze a hozzáférést. A VNet-hozzáférés is kombinálható a tűzfalszabályok használatával. További információ: [hozzáférés Azure Cosmos db virtuális hálózatokból](VNet-service-endpoint.md). |
| Hálózati elkülönítés és tűzfal-támogatás| Igen | A tűzfal támogatásával beállíthatja az Azure Cosmos-fiókot úgy, hogy csak az IP-címek jóváhagyott készletéről, az IP-címek tartományáról és/vagy a felhőalapú szolgáltatásokból engedélyezze a hozzáférést. További információ: [az IP-tűzfal konfigurálása Azure Cosmos DBban](how-to-configure-firewall.md).|
| Kényszerített bújtatás támogatása| Igen | Konfigurálható az ügyféloldali oldalon azon a VNet, ahol a virtuális gépek találhatók.   |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A rendszer a Azure Cosmos DB küldött összes kérelmet naplózza. Az Azure [monitoring](../azure-monitor/overview.md), az Azure-metrikák, az Azure audit naplózása támogatott.  Az adatsík-kérelmeknek, a lekérdezési futtatókörnyezet statisztikáinak, a lekérdezési szövegeknek és a MongoDB kérelmeknek megfelelő adatokat naplózhatja. Emellett riasztásokat is beállíthat. |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | Azure-Tevékenységnaplók a fiók szintű műveletekhez, például tűzfalakhoz, virtuális hálózatok, kulcsok eléréséhez és IAM-hoz. |
| Adatsíkok naplózása és naplózása | Igen | Diagnosztika-figyelési naplózás a tárolók szintjén végrehajtott műveletekhez, például tároló létrehozása, átviteli sebesség, indexelési házirendek és szifilisz-műveletek a dokumentumokon. |

## <a name="identity"></a>Identitáskezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Igen, az adatbázis-fiók szintjén; az adatsík szintjén Cosmos DB erőforrás-tokeneket és kulcs-hozzáférést használ. |
| Engedélyezés| Igen | Támogatott az Azure Cosmos-fiókban főkulcsokkal (elsődleges és másodlagos) és erőforrás-tokenekkel. Olvasási/írási vagy olvasási hozzáférés csak a főkulcsokkal rendelkező adatokhoz. Az erőforrás-tokenek korlátozott ideig férnek hozzá az erőforrásokhoz, például a dokumentumokhoz és a tárolóhoz. |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | Alapértelmezés szerint az összes Azure Cosmos-adatbázis és-biztonsági másolat titkosítva van; Lásd: [adattitkosítás Azure Cosmos DBban](database-encryption-at-rest.md). |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Lásd: [az ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos db-fiókhoz](how-to-setup-cmk.md)  |
| Oszlop szintű titkosítás (Azure Data Services)| Igen | Csak a Tables API Premiumban. Nem minden API támogatja ezt a funkciót. Lásd [: a Azure Cosmos db bemutatása: table API](table-introduction.md). |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az összes Azure Cosmos DB-adatforgalom titkosítva van az átvitel során. |
| Titkosított API-hívások| Igen | A Azure Cosmos DB összes kapcsolata támogatja a HTTPS protokollt. A Azure Cosmos DB a TLS 1,2-et is támogatja.<br>Egy minimális TLS-verzió kiszolgálóoldali kikényszeríthető. Ehhez vegye fel a kapcsolatot [cosmosdbpm@microsoft.com](maito:cosmosdbpm@microsoft.com]). |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Nem  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>További biztonsági vezérlők Cosmos DB

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Több eredetű erőforrás-megosztás (CORS) | Igen | Lásd: a [több eredetű erőforrás-megosztás konfigurálása (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).