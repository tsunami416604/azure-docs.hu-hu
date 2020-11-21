---
title: Támogatott FHIR-funkciók az Azure-ban – Azure API a FHIR-hez
description: Ez a cikk ismerteti a FHIR-specifikáció azon funkcióit, amelyek a FHIR készült Azure API-ban lettek implementálva
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 71097f13fffbbe5cb57a69c98fb0ab272e16af5c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026301"
---
# <a name="features"></a>Funkciók

A FHIR készült Azure API teljes körűen felügyelt üzembe helyezést biztosít az Azure-hoz készült Microsoft FHIR-kiszolgáló számára. A kiszolgáló a [FHIR](https://hl7.org/fhir) standard implementációja. Ez a dokumentum a FHIR-kiszolgáló fő funkcióit sorolja fel.

## <a name="fhir-version"></a>FHIR verziója

A legújabb verzió támogatott: `4.0.1`

A korábbi verziók jelenleg is támogatottak: `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| olvasás                           | Igen       | Igen       | Yes       |                                                     |
| vread                          | Igen       | Igen       | Yes       |                                                     |
| update                         | Igen       | Igen       | Yes       |                                                     |
| frissítés optimista zárolással | Igen       | Igen       | Yes       |                                                     |
| frissítés (feltételes)           | Igen       | Igen       | Yes       |                                                     |
| javítás                          | Nem        | Nem        | Nem        |                                                     |
| delete                         | Igen       | Igen       | Yes       |                                                     |
| Törlés (feltételes)           | Nem        | Nem        | Nem        |                                                     |
| előzmények                        | Igen       | Igen       | Yes       |                                                     |
| létrehozás                         | Igen       | Igen       | Yes       | Mind a POST, mind a PUT támogatása                               |
| létrehozás (feltételes)           | Igen       | Igen       | Yes       | Probléma [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| keresés                         | Részleges   | Részleges   | Részleges   | Lásd lent                                           |
| láncolt keresés                 | Nem        | Igen       | Nem        |                                           |
| fordított láncolt keresés         | Nem        | Nem        | Nem        |                                            |
| képességek                   | Igen       | Igen       | Yes       |                                                     |
| kötegelt                          | Igen       | Igen       | Yes       |                                                     |
| tranzakció                    | Nem        | Igen       | Nem        |                                                     |
| lapozófájl                         | Részleges   | Részleges   | Részleges   | `self` és `next` támogatottak                     |
| közvetítők                 | Nem        | Nem        | Nem        |                                                     |

## <a name="search"></a>Keresés

Az összes keresési paraméter típusa támogatott. 

| Keresési paraméter típusa | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
| Szám                | Igen       | Igen       | Yes       |         |
| Dátum/dátum/idő         | Igen       | Igen       | Igen       |         |
| Sztring                | Igen       | Igen       | Yes       |         |
| Jogkivonat                 | Igen       | Igen       | Yes       |         |
| Referencia             | Igen       | Igen       | Yes       |         |
| Kompozit             | Igen       | Igen       | Yes       |         |
| Mennyiség              | Igen       | Igen       | Yes       |         |
| URI                   | Igen       | Igen       | Yes       |         |
| Speciális               | Nem        | Nem        | Nem        |         |


| Módosítók             | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Igen       | Igen       | Igen       |         |
|`:exact`               | Igen       | Igen       | Igen       |         |
|`:contains`            | Igen       | Igen       | Igen       |         |
|`:text`                | Igen       | Igen       | Yes       |         |
|`:in` jogkivonat          | Nem        | Nem        | Nem        |         |
|`:below` jogkivonat       | Nem        | Nem        | Nem        |         |
|`:above` jogkivonat       | Nem        | Nem        | Nem        |         |
|`:not-in` jogkivonat      | Nem        | Nem        | Nem        |         |
|`:[type]` referencia  | Nem        | Nem        | Nem        |         |
|`:below` URI         | Igen       | Igen       | Igen       |         |
|`:not`                 | Nem        | Nem        | Nem        |         |
|`:above` URI         | Nem        | Nem        | Nem        | Probléma [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Gyakori keresési paraméter | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Igen       | Igen       | Igen       |         |
| `_lastUpdated`          | Igen       | Igen       | Igen       |         |
| `_tag`                  | Igen       | Igen       | Igen       |         |
| `_profile`              | Igen       | Igen       | Igen       |         |
| `_security`             | Igen       | Igen       | Igen       |         |
| `_text`                 | Nem        | Nem        | Nem        |         |
| `_content`              | Nem        | Nem        | Nem        |         |
| `_list`                 | Igen       | Igen       | Igen       |         |
| `_has`                  | Nem        | Nem        | Nem        |         |
| `_type`                 | Igen       | Igen       | Igen       |         |
| `_query`                | Nem        | Nem        | Nem        |         |
| `_filter`               | Nem        | Nem        | Nem        |         |

| Keresési eredmények paraméterei | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Részleges        | Részleges   | Részleges        |   `_sort=_lastUpdated` támogatott       |
| `_count`                | Igen       | Igen       | Yes       | `_count` legfeljebb 100 karakter hosszú lehet. Ha 100-nél magasabbra van állítva, akkor a rendszer csak 100 értéket ad vissza, és a kötegben figyelmeztetést ad vissza. |
| `_include`              | Igen       | Igen       | Yes       |A tartalmazott elemek 100-re korlátozódnak. A (z) Cosmos DB nem tartalmazza a (z) és az OSS-t a következőn: iteráció támogatása.|
| `_revinclude`           | Igen       | Igen       | Yes       | A tartalmazott elemek 100-re korlátozódnak. A (z) Cosmos DB nem tartalmazza a (z) és az OSS-t a következőn: iteráció támogatása.|
| `_summary`              | Részleges   | Részleges   | Részleges   | `_summary=count` támogatott |
| `_total`                | Részleges   | Részleges   | Részleges   | _total = nem és _total = pontos      |
| `_elements`             | Igen       | Igen       | Igen       |         |
| `_contained`            | Nem        | Nem        | Nem        |         |
| `containedType`         | Nem        | Nem        | Nem        |         |
| `_score`                | Nem        | Nem        | Nem        |         |

## <a name="extended-operations"></a>Kiterjesztett műveletek

A REST API-t kiterjesztő összes támogatott művelet.

| Keresési paraméter típusa | Támogatott – Péter | Támogatott-OSS (SQL) | Támogatott-OSS (Cosmos DB) | Megjegyzés |
|------------------------|-----------|-----------|-----------|---------|
| $export (teljes rendszeren) | Igen       | Igen       | Yes       |         |
| Beteg/$export        | Igen       | Igen       | Yes       |         |
| Csoport/$export          | Igen       | Igen       | Yes       |         |

## <a name="persistence"></a>Kitartás

A Microsoft FHIR-kiszolgáló rendelkezik egy csatlakoztatható adatmegőrzési modullal (lásd: [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Jelenleg a FHIR-kiszolgáló nyílt forráskódú programkódja [Azure Cosmos db](../cosmos-db/index-overview.md) és [SQL Database](https://azure.microsoft.com/services/sql-database/)megvalósítását is tartalmazza.

A Cosmos DB egy globálisan elosztott, többmodelles (SQL API-, MongoDB API-stb.-) adatbázis. Különböző konzisztencia- [szinteket](../cosmos-db/consistency-levels.md)támogat. Az alapértelmezett központi telepítési sablon konzisztens FHIR-kiszolgálót konfigurál `Strong` , de a konzisztencia-házirend a kérelem fejlécének használatával a kérelem alapján módosítható (általában nyugodt) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A FHIR-kiszolgáló [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) használ a hozzáférés-vezérléshez. Pontosabban, Role-Based Access Control (RBAC) kényszerítve van, ha a `FhirServer:Security:Enabled` konfigurációs paraméter értéke `true` , és a FHIR-kiszolgálónak küldött összes kérelemnek (kivéve `/metadata` ) a `Authorization` kérelem fejlécének kell lennie `Bearer <TOKEN>` . A tokennek tartalmaznia kell egy vagy több, a jogcímben definiált szerepkört `roles` . A rendszer akkor fogadja a kérést, ha a jogkivonat olyan szerepkört tartalmaz, amely engedélyezi a megadott műveletet a megadott erőforráson.

Jelenleg az adott szerepkörre vonatkozó engedélyezett műveletek *globálisan* lesznek alkalmazva az API-ra.

## <a name="service-limits"></a>Szolgáltatási korlátozások

* [**Kérelmek egységei (RUS)**](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) – a FHIR-hez készült Azure API-portálon akár 10 000 RUs is konfigurálható. Legalább 400 RUs vagy 10 RUs/GB szükséges, attól függően, hogy melyik a nagyobb. Ha 10 000 RUs-nál többre van szüksége, a megnövelt támogatási jegybe helyezhető. Az elérhető maximális érték 1 000 000.

* **Egyidejű kapcsolatok** és **példányok** – a dafault öt párhuzamos kapcsolatot biztosít a fürt két példányán (összesen 10 egyidejű kérelem esetén). Ha úgy gondolja, hogy több egyidejű kérésre van szüksége, nyisson meg egy támogatási jegyet az igényeinek megfelelő részletekkel.

* **Köteg mérete** – minden köteg 500 elemre van korlátozva.

* **Adatméret** – az adatmennyiségnek és a dokumentumoknak egyenként 2 MB-nál kisebbnek kell lenniük.

## <a name="performance-expectations"></a>Teljesítményre vonatkozó elvárások

A rendszer teljesítménye az RUs, az egyidejű kapcsolatok és a végrehajtott műveletek (Put, post stb.) számától függ. Az alábbiakban a konfigurált RUs alapján várhatóan megjelenő általános tartományok láthatók. Általánosságban elmondható, hogy a teljesítmény lineárisan növekszik az RUs növekedésével:

| RUs száma | Erőforrások/mp |
|----------|---------------|
| 400      | 5-10          |
| 1,000    | 100-150       |
| 10,000   | 225-400       |
| 100.000  | 2500 – 4000   |

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a FHIR készült Azure API támogatott FHIR szolgáltatásairól olvashat. Ezután telepítse az Azure API-t a FHIR-hez.
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-portal-quickstart.md)
