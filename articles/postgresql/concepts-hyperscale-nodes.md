---
title: Csomópontok az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)
description: A kiszolgálócsoport csomópontok két típusa.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077275"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Csomópontok az Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió)

A nagy kapacitású (Citus) (előzetes verzió) típusú futtató lehetővé teszi, hogy a "megosztás nélküli" architektúra egy másik egyeztessen az Azure Database for PostgreSQL-kiszolgálók (a csomópontok). A csomópontok egy kiszolgálócsoport együttesen további adatok tárolásához, és használja, mint egy kiszolgálón több processzormag. Az architektúra emellett lehetővé teszi az adatbázis méretezése a kiszolgálócsoport a további fürtcsomópontok hozzáadásával.

## <a name="coordinator-and-workers"></a>Koordinátor és feldolgozók

Minden kiszolgálócsoport a koordinátor-csomópont és a feldolgozó rendelkezik. Alkalmazások küldése a lekérdezéseket az koordinátor-csomóponton, amely továbbítja azt a megfelelő Worker és összegzi az eredményeiket. Alkalmazások, amelyek nem képesek közvetlenül kapcsolódni a munkavállalók.

Az egyes lekérdezésekhez a koordinátor továbbítja azt egyetlen munkavégző csomópont vagy parallelizes, attól függően, hogy a szükséges adattárolás egyetlen csomópont, vagy több több között. A koordinátor úgy dönt, hogy mi a teendő metaadat-táblát consulting által. Ezek a táblák nyomon követheti a DNS-neveit és a feldolgozó csomópontok állapotát, és az adatok a terjesztési csomópontok között.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan tárolhatja a csomópontok [elosztott adatok](concepts-hyperscale-distributed-data.md)
