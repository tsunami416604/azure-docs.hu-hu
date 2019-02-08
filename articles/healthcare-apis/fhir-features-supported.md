---
title: Az Azure-ban – Azure API-FHIR támogatott FHIR-funkciók
description: Ez a cikk ismerteti a FHIR specifikáció mely funkcióit FHIR az Azure API-ban megvalósított
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 1752ec8b2f846b51ef8222c54a00d5a5a0cdd05a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875195"
---
# <a name="features"></a>Szolgáltatások

Az Azure API-val FHIR teljes körűen felügyelt üzembe helyezése az Azure-hoz a Microsoft FHIR Server biztosít. A kiszolgáló nem megvalósítását a [FHIR](https://hl7.org/fhir) standard. Ez a dokumentum felsorolja a FHIR Server fő funkciói.

## <a name="fhir-version"></a>FHIR-verzió

jelenlegi verziója: `3.0.1`

## <a name="rest-api"></a>REST API

| API                            | Támogatott | Megjegyzés |
|--------------------------------|-----------|---------|
| olvasás                           | Igen       |         |
| vread                          | Igen       |         |
| update                         | Igen       |         |
| Az optimista frissítése | Igen       |         |
| frissítés (feltételes)           | Nem        |         |
| Javítás                          | Nem        |         |
| delete                         | Igen       |         |
| Törlés (feltételes)           | Nem        |         |
| létrehozás                         | Igen       | Mindkét POST és PUT támogatása |
| (feltételes) létrehozása           | Nem        |         |
| keresés                         | Részleges   | Lásd alább |
| Képességek                   | Igen       |         |
| kötegelt                          | Nem        |         |
| Tranzakció                    | Nem        |         |
| Előzmények                        | Igen       |         |
| Stránkování                         | Részleges   | `self` és `next` támogatottak |
| közvetítők                 | Nem        |         |

## <a name="search"></a>Keresés

Az összes keresési paramétert típusok támogatottak. Paraméterek és a láncolási fordított láncolt *nem* támogatott.

| Keresés a paraméter típusa | Támogatott | Megjegyzés |
|-----------------------|-----------|---------|
| Szám                | Igen       |         |
| Date/DateTime         | Igen       |         |
| String                | Igen       |         |
| Jogkivonat                 | Igen       |         |
| Leírások             | Igen       |         |
| Összetett             | Igen       |         |
| Mennyiség              | Igen       | A probléma [#103](https://github.com/Microsoft/fhir-server/issues/103) |
| URI                   | Igen       |         |


| Dostupnosti             | Támogatott | Megjegyzés |
|-----------------------|-----------|---------|
|`:missing`             | Igen       |         |
|`:exact`               | Igen       |         |
|`:contains`            | Igen       |         |
|`:text`                | Igen       |         |
|`:in` (token)          | Nem        |         |
|`:below` (token)       | Nem        |         |
|`:above` (token)       | Nem        |         |
|`:not-in` (token)      | Nem        |         |
|`:[type]` (hivatkozás)  | Nem        |         |
|`:below` (uri)         | Igen       |         |
|`:above` (uri)         | Nem        | A probléma [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Gyakori keresési paraméter | Támogatott | Megjegyzés |
|-------------------------| ----------|---------|
| `_id`                   | Igen       |         |
| `_lastUpdated`          | Igen       |         |
| `_tag`                  | Igen       |         |
| `_profile`              | Igen       |         |
| `_security`             | Igen       |         |
| `_text`                 | Nem        |         |
| `_content`              | Nem        |         |
| `_list`                 | Nem        |         |
| `_has`                  | Nem        |         |
| `_type`                 | Igen       |         |
| `_query`                | Nem        |         |

| Keresés a műveletekben       | Támogatott | Megjegyzés |
|-------------------------|-----------|---------|
| `_filter`               | Nem        |         |
| `_sort`                 | Nem        |         |
| `_score`                | Nem        |         |
| `_count`                | Igen       |         |
| `_summary`              | Részleges   | `_summary=count` támogatott |
| `_include`              | Nem        |         |
| `_revinclude`           | Nem        |         |
| `_contained`            | Nem        |         |
| `_elements`             | Nem        |         |

## <a name="persistence"></a>Adatmegőrzés

A Microsoft FHIR kiszolgálón, a moduláris adatmegőrzés modul (lásd: [ `Microsoft.Health.Fhir.Core.Features.Persistence` ](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Jelenleg a FHIR kiszolgáló nyílt forráskód tartalmazza a implementaci Pro [Azure Cosmos DB](../cosmos-db/index-overview.md).

A cosmos DB a egy (az SQL API-t, a MongoDB API-t, stb.) a globálisan elosztott többmodelles adatbázisa. Támogatja a különböző [konzisztenciaszintek](../cosmos-db/consistency-levels.md). Az alapértelmezett központi telepítési sablont konfigurál az FHIR-kiszolgáló `Strong` konzisztencia, de a konzisztencia-szabályzat (általában Könnyített) módosítható egy kérelem a kérelem alapján használatával a `x-ms-consistency-level` kérés fejlécéhez.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A FHIR-kiszolgáló használ [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) hozzáférés-vezérlést. Pontosabban a szerepkör alapú hozzáférés-vezérlés (RBAC) van érvényben, ha a `FhirServer:Security:Enabled` konfigurációs paraméter értéke `true`, és az összes kérelem (kivéve a `/metadata`) a FHIR kiszolgálónak rendelkeznie kell `Authorization` fejléc beállítása `Bearer <TOKEN>`. A jogkivonat tartalmaznia kell egy vagy több szerepkört, ahogyan az a `roles` jogcím. Egy kérelem lesz engedélyezve, ha a jogkivonat tartalmaz egy szerepkör, amely lehetővé teszi, hogy a megadott művelet a megadott erőforrás.

Jelenleg az adott szerepkörben engedélyezett műveletek alkalmaznak *globálisan* API.

## <a name="next-steps"></a>További lépések

Ebben a cikkben elolvasta az Azure API-ban támogatott FHIR-funkciókkal kapcsolatos FHIR számára. Ezután helyezze üzembe az Azure-ban FHIR API.
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló telepítése](fhir-oss-powershell-quickstart.md)