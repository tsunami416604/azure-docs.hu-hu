---
title: Csomópontok – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerkedjen meg a csomópontok, a koordinátorok és a feldolgozók két típusával a Azure Database for PostgreSQL egy kiszolgálói csoportjában.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382797"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Csomópontok Azure Database for PostgreSQL – nagy kapacitású (Citus)

A nagy kapacitású (Citus) üzemeltetési típusa lehetővé teszi, hogy Azure Database for PostgreSQL-kiszolgálók (más néven csomópontok) összehangolják egymással a "shared nothing" architektúrában. A kiszolgálócsoport csomópontjai közösen több adattal rendelkeznek, és több CPU-magot használnak, mint amennyit egyetlen kiszolgálón lehet használni. Az architektúra azt is lehetővé teszi, hogy az adatbázis méretezhető legyen, ha további csomópontokat ad hozzá a kiszolgálói csoporthoz.

## <a name="coordinator-and-workers"></a>Koordinátor és feldolgozók

Minden kiszolgálócsoport rendelkezik egy koordinátori csomóponttal és több feldolgozóval. Az alkalmazások a koordinátori csomópontba küldik a lekérdezéseket, amelyek továbbítják az érintett munkavállalóknak, és felhalmozzák eredményeiket. Az alkalmazások nem tudnak közvetlenül csatlakozni a feldolgozókhoz.

A nagy kapacitású (Citus) lehetővé teszi, hogy az adatbázis rendszergazdája a különböző munkavégző csomópontokon lévő különböző sorok tárolására szolgáló táblákat *terjesszen* . Az elosztott táblák a teljesítmény nagy kapacitású kulcsa. A táblázatok nem terjeszthetők ki teljes mértékben a koordinátor csomóponton, és nem tudják kihasználni a több gépen történő párhuzamosságot.

Az elosztott táblákon lévő összes lekérdezés esetében a koordinátor vagy egyetlen munkavégző csomópontra irányítja, vagy parallelizes azt attól függően, hogy a szükséges adat egyetlen vagy több csomóponton van-e. A koordinátor eldönti, hogy mi a teendő a metaadatokkal folytatott konzultációk során. Ezek a táblák nyomon követik a munkavégző csomópontok DNS-neveit és állapotát, valamint az adateloszlást a csomópontok között.

## <a name="next-steps"></a>Következő lépések
- További információ az [elosztott táblákról](concepts-hyperscale-distributed-data.md)
