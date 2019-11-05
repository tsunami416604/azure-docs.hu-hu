---
title: Csomópontok Azure Database for PostgreSQL – nagy kapacitású (Citus)
description: Ismerkedjen meg a csomópontok, a koordinátorok és a feldolgozók két típusával a Azure Database for PostgreSQL egy kiszolgálói csoportjában.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 5348c8bbe432cd024a8da93ca866ae4458f956e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468088"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Csomópontok Azure Database for PostgreSQL – nagy kapacitású (Citus)

A nagy kapacitású (Citus) üzemeltetési típusa lehetővé teszi, hogy Azure Database for PostgreSQL-kiszolgálók (más néven csomópontok) összehangolják egymással a "shared nothing" architektúrában. A kiszolgálócsoport csomópontjai közösen több adattal rendelkeznek, és több CPU-magot használnak, mint amennyit egyetlen kiszolgálón lehet használni. Az architektúra azt is lehetővé teszi, hogy az adatbázis méretezhető legyen, ha további csomópontokat ad hozzá a kiszolgálói csoporthoz.

## <a name="coordinator-and-workers"></a>Koordinátor és feldolgozók

Minden kiszolgálócsoport rendelkezik egy koordinátori csomóponttal és több feldolgozóval. Az alkalmazások a koordinátori csomópontba küldik a lekérdezéseket, amelyek továbbítják az érintett munkavállalóknak, és felhalmozzák eredményeiket. Az alkalmazások nem tudnak közvetlenül csatlakozni a feldolgozókhoz.

Az egyes lekérdezésekhez a koordinátor vagy egyetlen munkavégző csomópontra irányítja, vagy parallelizes azt attól függően, hogy a szükséges adat egyetlen vagy több csomóponton van-e. A koordinátor eldönti, hogy mi a teendő a metaadatokkal folytatott konzultációk során. Ezek a táblák nyomon követik a munkavégző csomópontok DNS-neveit és állapotát, valamint az adateloszlást a csomópontok között.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan tárolják a csomópontok [az elosztott információkat](concepts-hyperscale-distributed-data.md)
