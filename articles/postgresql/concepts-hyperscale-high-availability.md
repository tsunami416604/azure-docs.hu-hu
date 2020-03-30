---
title: Magas rendelkezésre állás – Nagykapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: Magas rendelkezésre állás és vészhelyreállítási koncepciók
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975533"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Magas rendelkezésre állás a PostgreSQL-alapú Azure Database ben – Nagy kapacitás (Citus)

A magas rendelkezésre állás (HA) elkerüli az adatbázis állásidejét azáltal, hogy a kiszolgálócsoport minden csomópontjának készenléti replikáit fenntartja. Ha egy csomópont leáll, a nagykapacitású kapcsolók a bejövő kapcsolatokat a sikertelen csomópontról a készenléti állapotba kapcsolják. Feladatátvétel történik néhány percen belül, és az előléptetett csomópontok mindig friss adatokat PostgreSQL szinkron streamelési replikáción keresztül.

A ha a koordinátor csomóponton a HA előnyeinek kihasználásához az adatbázis-alkalmazásoknak észlelniük kell és újra meg kell próbálniuk az eldobott kapcsolatokat és a sikertelen tranzakciókat. Az újonnan előléptetett koordinátor ugyanazzal a kapcsolati karakterlánccal lesz elérhető.

A helyreállítás három szakaszra bontható: észlelés, feladatátvétel és teljes helyreállítás.  A nagy kapacitású rendszeres állapot-ellenőrzéseket végez minden csomóponton, és négy sikertelen ellenőrzés után megállapítja, hogy egy csomópont nem működik. A nagy kapacitású ezután elősegíti a készenléti elsődleges csomópont állapotát (feladatátvétel), és új készenléti állapotot biztosít.
Megkezdődik a streamelési replikáció, így az új csomópont naprakész.  Az összes adat replikálása után a csomópont elérte a teljes helyreállítást.

### <a name="next-steps"></a>További lépések

- Megtudhatja, hogy miként engedélyezheti a [magas rendelkezésre állást](howto-hyperscale-high-availability.md) egy nagy kapacitású kiszolgálócsoportban.
