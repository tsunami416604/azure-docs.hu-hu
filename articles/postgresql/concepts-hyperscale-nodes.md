---
title: Csomópontok – Nagy kapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: Ismerje meg a két típusú csomópontok, koordinátorok és a dolgozók, egy kiszolgálócsoportban az Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974002"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Csomópontok az Azure Database for PostgreSQL -for PostgreSQL – Hyperscale (Citus)

A Nagykapacitású (Citus) üzemeltetési típus lehetővé teszi, hogy az Azure Database for PostgreSQL-kiszolgálók (csomópontok) egy "megosztott semmit" architektúrában egyeztetjenek egymással. A kiszolgálócsoport csomópontjai együttesen több adatot tartanak, és több processzormagot használnak, mint amennyi egyetlen kiszolgálón lehetséges lenne. Az architektúra lehetővé teszi az adatbázis méretezését azáltal, hogy további csomópontokat ad hozzá a kiszolgálócsoporthoz.

## <a name="coordinator-and-workers"></a>Koordinátor és munkavállalók

Minden kiszolgálócsoportrendelkezik egy koordinátor-csomóponttal és több dolgozóval. Az alkalmazások elküldik a lekérdezéseiket a koordinátor csomópontnak, amely továbbítja azt az érintett dolgozóknak, és összegyűjti az eredményeket. Az alkalmazások nem tudnak közvetlenül csatlakozni a dolgozókhoz.

A koordinátor minden lekérdezéshez vagy egyetlen munkavégző csomóponthoz irányítja, vagy több csomóponton keresztül párhuzamosítja azt attól függően, hogy a szükséges adatok egyetlen vagy több csomóponton élnek-e. A koordinátor a metaadat-táblák kal eldönti, hogy mi a teendő. Ezek a táblák nyomon követik a feldolgozócsomópontok DNS-nevét és állapotát, valamint az adatok csomópontok közötti eloszlását.

## <a name="next-steps"></a>További lépések
- A csomópontok által [az elosztott adatok tárolása](concepts-hyperscale-distributed-data.md)
