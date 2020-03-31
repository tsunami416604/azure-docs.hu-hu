---
title: Magas rendelkezésre állás - Azure Database for PostgreSQL - Single Server
description: Ez a cikk az Azure Database for PostgreSQL – Single Server magas rendelkezésre állásáról nyújt tájékoztatást.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768571"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Magas rendelkezésre állási fogalmak az Azure Database for PostgreSQL-ben – Egykiszolgálós
Az Azure Database for PostgreSQL szolgáltatás garantáltan magas szintű rendelkezésre állást biztosít. A pénzügyileg támogatott szolgáltatásiszint-szerződés (SLA) 99,99%, általános anammár a rendelkezésre állás függvényében. A szolgáltatás használata gyakorlatilag nincs alkalmazás leállási idő.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre állású (HA) modell beépített feladatátvételi mechanizmusokon alapul, amikor egy csomópont szintű megszakítás történik. A csomópontszintű megszakítás hardverhiba vagy a szolgáltatás központi telepítésére adott válasz miatt fordulhat elő.

A PostgreSQL-adatbázis-kiszolgálóazure-adatbázis-kiszolgálón végrehajtott módosítások mindig egy tranzakció környezetében történnek. A módosításokat a rendszer szinkron módon rögzíti az Azure storage-ban, amikor a tranzakció véglegesítésre kerül. Ha csomópontszintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópontot, és adattárolást csatol az új csomóponthoz. Az aktív kapcsolatok megszakadnak, és a rendszer nem véglegesíti a bejövő tranzakciókat.

## <a name="application-retry-logic-is-essential"></a>Az alkalmazás újrapróbálkozási logikája elengedhetetlen
Fontos, hogy a PostgreSQL adatbázis-alkalmazások az eldobott kapcsolatok és sikertelen tranzakciók észlelésére és újrapróbálkozására épüljenek. Amikor az alkalmazás újrapróbálkozik, az alkalmazás kapcsolata transzparens módon átirányítja az újonnan létrehozott példány, amely átveszi a sikertelen példány.

Az Azure-on belül egy átjáró segítségével átirányítják a kapcsolatokat az új példányra. Megszakítás esetén a teljes feladatátvételi folyamat általában több tíz másodpercet vesz igénybe. Mivel az átirányítást az átjáró belsőleg kezeli, a külső kapcsolati karakterlánc ugyanaz marad az ügyfélalkalmazások esetében.

## <a name="scaling-up-or-down"></a>Fel- vagy leskálázás
A HA-modellhez hasonlóan, amikor egy Azure Database for PostgreSQL fel- vagy leskálázásra kerül, egy új kiszolgálópéldány jön létre a megadott mérettel. A meglévő adattároló leválik az eredeti példányról, és az új példányhoz kapcsolódik.

A méretezési művelet során megszakad az adatbázis-kapcsolatok. Az ügyfélalkalmazások levannak választva, és a nyitott nem véglegesített tranzakciók megszakadnak. Miután az ügyfélalkalmazás újrapróbálkozik a kapcsolattal, vagy új kapcsolatot létesít, az átjáró az újonnan méretezett példányhoz irányítja a kapcsolatot. 

## <a name="next-steps"></a>További lépések
- További információ [az átmeneti kapcsolódási hibák kezeléséről](concepts-connectivity.md)
- Az [adatok olvasási replikákkal való replikálása](howto-read-replicas-portal.md)
