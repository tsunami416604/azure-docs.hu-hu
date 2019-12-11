---
title: Magas rendelkezésre állás – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Magas rendelkezésre állás és vész-helyreállítási fogalmak
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975533"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Magas rendelkezésre állás Azure Database for PostgreSQL – nagy kapacitású (Citus)

A magas rendelkezésre állás (HA) úgy segít elkerülni az adatbázisok állásidejét, hogy készenléti replikákat tart fenn egy kiszolgálócsoport összes csomópontjáról. Ha egy csomópont leáll, a rugalmas skálázás a leállt csomópontról a tartalékra irányítja át a bejövő kapcsolatokat. A feladatátvétel néhány percen belül megtörténik, az előléptetett csomópontok pedig mindig friss adatokkal rendelkeznek a PostgreSQL szinkron streamelési replikációjának köszönhetően.

Ha a koordinátor csomóponton szeretné kihasználni a szolgáltatást, az adatbázis-alkalmazásoknak meg kell ismerniük és újra kell próbálnia a kapcsolatokat és a sikertelen tranzakciókat. Az újonnan előléptetett koordinátor ugyanazzal a kapcsolatok sztringtel lesz elérhető.

A helyreállítás három szakaszra bontható: észlelés, feladatátvétel és teljes helyreállítás.  A nagy kapacitású rendszeres állapot-ellenőrzéseket futtat minden csomóponton, és négy sikertelen ellenőrzés után meghatározza, hogy a csomópont le van-e kapcsolva. A nagy kapacitású ezután elősegíti a készenléti állapotot az elsődleges csomópont állapota (feladatátvétel) számára, és új készenléti állapotot biztosít.
A folyamatos átviteli replikáció megkezdődik, így az új csomópont naprakész állapotba kerül.  Ha minden adat replikálódik, a csomópont elérte a teljes helyreállítást.

### <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [engedélyezheti a magas rendelkezésre állást](howto-hyperscale-high-availability.md) egy nagy kapacitású-kiszolgáló csoportban.
