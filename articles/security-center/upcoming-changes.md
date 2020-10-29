---
title: Fontos változások a Azure Security Center
description: Előfordulhat, hogy a Azure Security Center közelgő módosításaival tisztában kell lennie a szolgáltatással, és amelyre szükség lehet a tervezéshez
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: 549a95b0b2ffc2b2d2bf5670a961e0454683e33a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026717"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Az Azure biztonsági teljesítménymutatóval kapcsolatos javaslatok (előzetes verzió)

| Szempont | Részletek |
|---------|---------|
|Bejelentés dátuma | 26. október 2020  |
|A módosítás dátuma  |  November 2020 |
|Hatás     | Lehetséges, hogy további javaslatokat kell megvizsgálnia.<br>Nincs azonnali hatása a biztonságos pontszámra – az előzetes verzióra vonatkozó javaslatok nem érintik a biztonságos pontszámot.<br>Nincs azonnali hatással az erőforrások állapotára – az előzetes verzióra vonatkozó javaslatok nem teszik lehetővé a "nem megfelelő" erőforrás megjelenítését.|
|  |  |

Az Azure biztonsági teljesítményteszt a Microsoft által létrehozott, Azure-specifikus irányelvek a biztonsági és megfelelőségi szabályzatok közös megfelelőségi keretrendszereken alapuló bevált eljárásaihoz. [További tudnivalók az Azure-biztonsági teljesítménytesztről](../security/benchmarks/introduction.md).

A következő 18 új javaslat jelenik meg Security Center a teljesítményteszt lefedettségének növeléséhez.

Az előzetes verzióra vonatkozó javaslatok nem jelenítik meg az erőforrás állapotát, és nem tartoznak bele a biztonságos pontszám számításaiba. Ha lehetséges, javítsa őket, hogy ha az előzetes verzió időtartama lejár, a pontszáma is hozzájárul. További információ a javaslatokról a [Azure Security Centerban található javaslatok szervizelése](security-center-remediate-recommendations.md)című témakörben található.

- Azure Backup engedélyezni kell a virtuális gépeket
- Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon
- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL
- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for PostgreSQL
- A Java-t az API-alkalmazás legújabb verziójára kell frissíteni
- A Java-t a Function app legújabb verziójára kell frissíteni
- A Java-t a webalkalmazás legújabb verziójára kell frissíteni
- A PHP-t az API-alkalmazás legújabb verziójára kell frissíteni
- A PHP-t a webalkalmazás legújabb verziójára kell frissíteni
- A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon
- A magánhálózati végpontot engedélyezni kell a MySQL-kiszolgálókon
- A privát végpontot engedélyezni kell a PostgreSQL-kiszolgálókhoz
- A Pythont az API-alkalmazás legújabb verziójára kell frissíteni
- A Pythont a Function alkalmazás legújabb verziójára kell frissíteni
- A Pythont a webalkalmazás legújabb verziójára kell frissíteni
- A webalkalmazásoknak SSL-tanúsítványt kell igényelnie minden bejövő kérelemhez

Kapcsolódó hivatkozások:

- [További információ az Azure biztonsági teljesítménytesztről](../security/benchmarks/introduction.md)
- [További információ az Azure API apps szolgáltatásról](../app-service/app-service-web-tutorial-rest-api.md)
- [További információ az Azure Function apps szolgáltatásról](../azure-functions/functions-overview.md)
- [További információ az Azure Web Apps szolgáltatásról](../app-service/overview.md)
- [További információ a Azure Database for MariaDB](../mariadb/overview.md)
- [További információ a Azure Database for MySQL](../mysql/overview.md)
- [További információ a Azure Database for PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.