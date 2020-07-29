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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74975533"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Magas rendelkezésre állás Azure Database for PostgreSQL – nagy kapacitású (Citus)

A magas rendelkezésre állás (HA) elkerüli az adatbázis leállását azáltal, hogy a csoport minden csomópontjának készenléti replikáit megtartja. Ha egy csomópont leáll, a nagy kapacitású bekapcsolja a bejövő kapcsolatokat a meghibásodott csomópontról a készenléti állapotba. A feladatátvétel néhány percen belül megtörténik, és a támogatott csomópontok mindig friss adattal rendelkeznek a PostgreSQL szinkron adatfolyam-továbbítási replikálásával.

Ha a koordinátor csomóponton szeretné kihasználni a szolgáltatást, az adatbázis-alkalmazásoknak meg kell ismerniük és újra kell próbálnia a kapcsolatokat és a sikertelen tranzakciókat. Az újonnan előléptetett koordinátor ugyanazzal a kapcsolatok sztringtel lesz elérhető.

A helyreállítás három szakaszra bontható: észlelés, feladatátvétel és teljes helyreállítás.  A nagy kapacitású rendszeres állapot-ellenőrzéseket futtat minden csomóponton, és négy sikertelen ellenőrzés után meghatározza, hogy a csomópont le van-e kapcsolva. A nagy kapacitású ezután elősegíti a készenléti állapotot az elsődleges csomópont állapota (feladatátvétel) számára, és új készenléti állapotot biztosít.
A folyamatos átviteli replikáció megkezdődik, így az új csomópont naprakész állapotba kerül.  Ha minden adat replikálódik, a csomópont elérte a teljes helyreállítást.

### <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [engedélyezheti a magas rendelkezésre állást](howto-hyperscale-high-availability.md) egy nagy kapacitású-kiszolgáló csoportban.
