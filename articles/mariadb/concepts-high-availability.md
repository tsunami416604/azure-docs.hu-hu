---
title: Magas rendelkezésre állás - Azure-adatbázis a MariaDB-hez
description: Ez a témakör az Azure Database for MariaDB használata esetén rendelkezésre álló magas rendelkezésre állásról nyújt tájékoztatást.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a87646f6195a06cf0a5382cb248efa5516c953f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531991"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Magas rendelkezésre állási fogalmak a MariaDB Azure Database-ben
Az Azure Database for MariaDB szolgáltatás garantáltan magas szintű rendelkezésre állást biztosít. A pénzügyileg támogatott szolgáltatásiszint-szerződés (SLA) 99,99%, általános anammár a rendelkezésre állás függvényében. A szolgáltatás használata gyakorlatilag nincs alkalmazás leállási idő.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre állású (HA) modell a beépített feladatátvételi mechanizmusokon alapul, amikor csomópontszintű megszakítás történik. A csomópontszintű megszakítás hardverhiba vagy a szolgáltatás központi telepítésére adott válasz miatt fordulhat elő.

A MariaDB adatbázis-kiszolgálóazure-adatbázis-kiszolgálón végrehajtott módosítások mindig egy tranzakció környezetében történnek. A módosításokat a rendszer szinkron módon rögzíti az Azure storage-ban, amikor a tranzakció véglegesítésre kerül. Ha csomópontszintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópontot, és adattárolást csatol az új csomóponthoz. Az aktív kapcsolatok megszakadnak, és a rendszer nem véglegesíti a bejövő tranzakciókat.

## <a name="application-retry-logic-is-essential"></a>Az alkalmazás újrapróbálkozási logikája elengedhetetlen
Fontos, hogy a MariaDB adatbázis-alkalmazások az eldobott kapcsolatok és sikertelen tranzakciók észlelésére és újrapróbálkozására készültek. Amikor az alkalmazás újrapróbálkozik, az alkalmazás kapcsolata transzparens módon átirányítja az újonnan létrehozott példány, amely átveszi a sikertelen példány.

Az Azure-on belül egy átjáró segítségével átirányítják a kapcsolatokat az új példányra. Megszakítás esetén a teljes feladatátvételi folyamat általában több tíz másodpercet vesz igénybe. Mivel az átirányítást az átjáró belsőleg kezeli, a külső kapcsolati karakterlánc ugyanaz marad az ügyfélalkalmazások esetében.

## <a name="scaling-up-or-down"></a>Fel- vagy leskálázás
A HA-modellhez hasonlóan, amikor egy Azure Database for MariaDB fel- vagy leskálázásra kerül, egy új kiszolgálópéldány jön létre a megadott mérettel. A meglévő adattároló leválik az eredeti példányról, és az új példányhoz kapcsolódik.

A méretezési művelet során megszakad az adatbázis-kapcsolatok. Az ügyfélalkalmazások levannak választva, és a nyitott nem véglegesített tranzakciók megszakadnak. Miután az ügyfélalkalmazás újrapróbálkozik a kapcsolattal, vagy új kapcsolatot létesít, az átjáró az újonnan méretezett példányhoz irányítja a kapcsolatot.

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését az [Azure Database for MariaDB áttekintése című témakörben találja.](overview.md)
