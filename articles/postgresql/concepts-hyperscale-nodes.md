---
title: Csomópontok – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Ismerkedjen meg a csomópontok, a koordinátorok és a feldolgozók két típusával a Azure Database for PostgreSQL egy kiszolgálói csoportjában.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974002"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Csomópontok Azure Database for PostgreSQL – nagy kapacitású (Citus)

A nagy kapacitású (Citus) üzemeltetési típusa lehetővé teszi, hogy Azure Database for PostgreSQL-kiszolgálók (más néven csomópontok) összehangolják egymással a "shared nothing" architektúrában. A kiszolgálócsoport csomópontjai közösen több adattal rendelkeznek, és több CPU-magot használnak, mint amennyit egyetlen kiszolgálón lehet használni. Az architektúra azt is lehetővé teszi, hogy az adatbázis méretezhető legyen, ha további csomópontokat ad hozzá a kiszolgálói csoporthoz.

## <a name="coordinator-and-workers"></a>Koordinátor és feldolgozók

Minden kiszolgálócsoport rendelkezik egy koordinátori csomóponttal és több feldolgozóval. Az alkalmazások a koordinátori csomópontba küldik a lekérdezéseket, amelyek továbbítják az érintett munkavállalóknak, és felhalmozzák eredményeiket. Az alkalmazások nem tudnak közvetlenül csatlakozni a feldolgozókhoz.

Az egyes lekérdezésekhez a koordinátor vagy egyetlen munkavégző csomópontra irányítja, vagy parallelizes azt attól függően, hogy a szükséges adat egyetlen vagy több csomóponton van-e. A koordinátor eldönti, hogy mi a teendő a metaadatokkal folytatott konzultációk során. Ezek a táblák nyomon követik a munkavégző csomópontok DNS-neveit és állapotát, valamint az adateloszlást a csomópontok között.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan tárolják a csomópontok [az elosztott információkat](concepts-hyperscale-distributed-data.md)
