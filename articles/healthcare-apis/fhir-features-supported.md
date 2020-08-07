---
title: Támogatott FHIR-funkciók az Azure-ban – Azure API a FHIR-hez
description: Ez a cikk ismerteti a FHIR-specifikáció azon funkcióit, amelyek a FHIR készült Azure API-ban lettek implementálva
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 1cb3af32f1ad16218c82f91c3f28d4f4ab47e677
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843504"
---
# <a name="features"></a>Szolgáltatások

A FHIR készült Azure API teljes körűen felügyelt üzembe helyezést biztosít az Azure-hoz készült Microsoft FHIR-kiszolgáló számára. A kiszolgáló a [FHIR](https://hl7.org/fhir) standard implementációja. Ez a dokumentum a FHIR-kiszolgáló fő funkcióit sorolja fel.

## <a name="fhir-version"></a>FHIR verziója

A legújabb verzió támogatott:`4.0.1`

A korábbi verziók jelenleg is támogatottak:`3.0.2`

## <a name="rest-api"></a>REST API

| API                            | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| olvasás                           | Igen       | Igen       | Igen       |                                                     |
| vread                          | Igen       | Igen       | Igen       |                                                     |
| update                         | Igen       | Igen       | Igen       |                                                     |
| frissítés optimista zárolással | Igen       | Igen       | Igen       |                                                     |
| frissítés (feltételes)           | Igen       | Igen       | Igen       |                                                     |
| javítás                          | Nem        | Nem        | Nem        |                                                     |
| törlés                         | Igen       | Igen       | Igen       |                                                     |
| Törlés (feltételes)           | Nem        | Nem        | Nem        |                                                     |
| létrehozás                         | Igen       | Igen       | Igen       | Mind a POST, mind a PUT támogatása                               |
| létrehozás (feltételes)           | Igen       | Igen       | Igen       |                                                     |
| keresés                         | Részleges   | Részleges   | Részleges   | Lásd lent                                           |
| láncolt keresés                 | Nem        | Igen       | Nem        |                                           |
| fordított láncolt keresés         | Nem        | Nem        | Nem        |                                            |
| képességek                   | Igen       | Igen       | Igen       |                                                     |
| kötegelt                          | Igen       | Igen       | Igen       |                                                     |
| tranzakció                    | Nem        | Igen       | Nem        |                                                     |
| előzmények                        | Igen       | Igen       | Igen       |                                                     |
| lapozófájl                         | Részleges   | Részleges   | Részleges   | `self`és `next` támogatottak                     |
| közvetítők                 | Nem        | Nem        | Nem        |                                                     |

## <a name="search"></a>Keresés

Az összes keresési paraméter típusa támogatott. 

| Keresési paraméter típusa | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
| Szám                | Igen       | Igen       | Igen       |         |
| Dátum/dátum/idő         | Igen       | Igen       | Igen       |         |
| Sztring                | Igen       | Igen       | Igen       |         |
| Jogkivonat                 | Igen       | Igen       | Igen       |         |
| Referencia             | Igen       | Igen       | Igen       |         |
| Kompozit             | Igen       | Igen       | Igen       |         |
| Mennyiség              | Igen       | Igen       | Igen       |         |
| URI                   | Igen       | Igen       | Igen       |         |
| Speciális               | Nem        | Nem        | Nem        |         |


| Módosítók             | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Igen       | Igen       | Igen       |         |
|`:exact`               | Igen       | Igen       | Igen       |         |
|`:contains`            | Igen       | Igen       | Igen       |         |
|`:text`                | Igen       | Igen       | Igen       |         |
|`:in`jogkivonat          | Nem        | Nem        | Nem        |         |
|`:below`jogkivonat       | Nem        | Nem        | Nem        |         |
|`:above`jogkivonat       | Nem        | Nem        | Nem        |         |
|`:not-in`jogkivonat      | Nem        | Nem        | Nem        |         |
|`:[type]`referencia  | Nem        | Nem        | Nem        |         |
|`:below`URI         | Igen       | Igen       | Igen       |         |
|`:not`                 | Nem        | Nem        | Nem        |         |
|`:above`URI         | Nem        | Nem        | Nem        | Probléma [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Gyakori keresési paraméter | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Igen       | Igen       | Igen       |         |
| `_lastUpdated`          | Igen       | Igen       | Igen       |         |
| `_tag`                  | Igen       | Igen       | Igen       |         |
| `_profile`              | Igen       | Igen       | Igen       |         |
| `_security`             | Igen       | Igen       | Igen       |         |
| `_text`                 | Nem        | Nem        | Nem        |         |
| `_content`              | Nem        | Nem        | Nem        |         |
| `_list`                 | Nem        | Igen       | Igen       |         |
| `_has`                  | Nem        | Nem        | Nem        |         |
| `_type`                 | Igen       | Igen       | Igen       |         |
| `_query`                | Nem        | Nem        | Nem        |         |

| Keresési műveletek       | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Nem        | Nem        | Nem        |         |
| `_sort`                 | Nem        | Nem        | Nem        |         |
| `_score`                | Nem        | Nem        | Nem        |         |
| `_count`                | Igen       | Igen       | Igen       |         |
| `_summary`              | Részleges   | Részleges   | Részleges   | `_summary=count`támogatott |
| `_include`              | Nem        | Igen       | Nem        |         |
| `_revinclude`           | Nem        | Nem        | Nem        |         |
| `_contained`            | Nem        | Nem        | Nem        |         |
| `_elements`             | Nem        | Nem        | Nem        |         |

## <a name="persistence"></a>Kitartás

A Microsoft FHIR-kiszolgáló rendelkezik egy csatlakoztatható adatmegőrzési modullal (lásd: [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Jelenleg a FHIR-kiszolgáló nyílt forráskódú programkódja [Azure Cosmos db](../cosmos-db/index-overview.md) és [SQL Database](https://azure.microsoft.com/services/sql-database/)megvalósítását is tartalmazza.

A Cosmos DB egy globálisan elosztott, többmodelles (SQL API-, MongoDB API-stb.-) adatbázis. Különböző konzisztencia- [szinteket](../cosmos-db/consistency-levels.md)támogat. Az alapértelmezett központi telepítési sablon konzisztens FHIR-kiszolgálót konfigurál `Strong` , de a konzisztencia-házirend a kérelem fejlécének használatával a kérelem alapján módosítható (általában nyugodt) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A FHIR-kiszolgáló [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) használ a hozzáférés-vezérléshez. A szerepköralapú Access Control (RBAC) kényszerítve van, ha a `FhirServer:Security:Enabled` konfigurációs paraméter értéke `true` , és a FHIR-kiszolgálónak küldött összes kérelemnek (kivéve `/metadata` ) a `Authorization` kérelem fejlécét kell beállítani `Bearer <TOKEN>` . A tokennek tartalmaznia kell egy vagy több, a jogcímben definiált szerepkört `roles` . A rendszer akkor fogadja a kérést, ha a jogkivonat olyan szerepkört tartalmaz, amely engedélyezi a megadott műveletet a megadott erőforráson.

Jelenleg az adott szerepkörre vonatkozó engedélyezett műveletek *globálisan* lesznek alkalmazva az API-ra.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a FHIR készült Azure API támogatott FHIR szolgáltatásairól olvashat. Ezután telepítse az Azure API-t a FHIR-hez.
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)
