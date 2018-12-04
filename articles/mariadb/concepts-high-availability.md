---
title: Magas rendelkezésre állású fogalmak MariaDB-hez készült Azure Database-ben
description: Ez a témakör a magas rendelkezésre állás MariaDB-hez készült Azure Database használata
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 043988066cae672225b850b57864bddb3155689c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833839"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Magas rendelkezésre állású fogalmak MariaDB-hez készült Azure Database-ben
Az Azure Database for MariaDB szolgáltatás garantált magas szintű rendelkezésre állást biztosít. A pénzügyi felelősséggel vállalt garantált szolgáltatási szinttel (SLA) 99,99 %-os általános elérhetővé tételtől. Gyakorlatilag alkalmazások egyike sem állásidő a szolgáltatás használata esetén.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre ÁLLÁS modell feladatátvételt a beépített mechanizmusok alapján egy csomópont-szintű megszakadása esetén. Egy csomópont-szintű megszakítás akkor fordulhat elő, hardverhiba miatt, vagy a szolgáltatások üzembe helyezéséhez.

Mindig egy Azure Database for MariaDB adatbázis-kiszolgáló végrehajtott módosítások történnek, egy tranzakció környezetében. A változásokat elszámolni szinkron módon történik az Azure storage-ban Ha a tranzakció véglegesítése. Ha egy csomópont-szintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópont, és adattárolás csatolja az új csomópont. Minden aktív kapcsolat a rendszer elveti és megszakít tranzakciók nem kerülnek.

## <a name="application-retry-logic-is-essential"></a>Alkalmazás újrapróbálkozási logikát elengedhetetlen.
Fontos, hogy a MariaDB adatbázis-alkalmazások észlelése, és ismételje meg a beépített kapcsolatok csökkent, és nem sikerült a tranzakció. Az alkalmazás újrapróbálkozik, ha az alkalmazás által létesített kapcsolatban a rendszer transzparens módon átirányítja az újonnan létrehozott példány, amely átveszi a hibás szolgáltatáspéldányt a.

Belsőleg az Azure-ban, az átjáró segítségével átirányítja a kapcsolatot az új példány. Alapján megszakadásának a feladatátvétel teljes folyamat általában több tíz másodpercet vesz igénybe. Az átirányítási belsőleg kezeli az átjárót, mert a külső kapcsolati karakterlánc ugyanaz marad, az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé és lefelé skálázás
A magas rendelkezésre ÁLLÁSÚ modell, Azure Database for MariaDB-méretezve, felfelé vagy lefelé hasonlóan egy új példány a megadott méretű jön létre. A meglévő adatok tárolási leválasztása az eredeti példányban, és az új példány csatlakozik.

A skálázási művelet során az adatbázis-kapcsolatok romlását történik. Az ügyfélalkalmazások le vannak választva, és nyissa meg a nem véglegesített tranzakciót megszakították. Az ügyfélalkalmazás újrapróbálja a kapcsolódást, vagy egy új kapcsolatot, miután az átjáró irányítja a kapcsolat az újonnan méretű példánnyal.

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [, Azure Database for MariaDB áttekintése](overview.md)
