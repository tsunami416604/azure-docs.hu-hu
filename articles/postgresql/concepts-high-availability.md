---
title: Az Azure Database for PostgreSQL - kiszolgáló egyetlen magas rendelkezésre állású fogalmak
description: Ez a cikk nyújt információkat a magas rendelkezésre állás, Azure Database for PostgreSQL - kiszolgáló egyetlen használata.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f54c83099957b4d8795c4049be52d70e8a0e2a61
ms.sourcegitcommit: f4469b7bb1f380bf9dddaf14763b24b1b508d57c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "65073436"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Az Azure Database for PostgreSQL - kiszolgáló egyetlen magas rendelkezésre állású fogalmak
Az Azure Database for PostgreSQL szolgáltatás garantált magas szintű rendelkezésre állást biztosít. A pénzügyi felelősséggel vállalt garantált szolgáltatási szinttel (SLA) 99,99 %-os általános elérhetővé tételtől. Gyakorlatilag alkalmazások egyike sem állásidő a szolgáltatás használata esetén.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre ÁLLÁS modell beépített feladatátvételi mechanizmusok alapján egy csomópont-szintű megszakadása esetén. Egy csomópont-szintű megszakítás akkor fordulhat elő, hardverhiba miatt, vagy a szolgáltatások üzembe helyezéséhez.

Mindig egy Azure Database for PostgreSQL-adatbáziskiszolgáló végrehajtott módosítások történnek, egy tranzakció környezetében. A változásokat elszámolni szinkron módon történik az Azure storage-ban Ha a tranzakció véglegesítése. Ha egy csomópont-szintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópont, és adattárolás csatolja az új csomópont. Minden aktív kapcsolat a rendszer elveti és megszakít tranzakciók nem kerülnek.

## <a name="application-retry-logic-is-essential"></a>Alkalmazás újrapróbálkozási logikát elengedhetetlen.
Fontos, hogy a PostgreSQL adatbázis-alkalmazások észlelése, és ismételje meg a beépített kapcsolatok csökkent, és nem sikerült a tranzakció. Az alkalmazás újrapróbálkozik, ha az alkalmazás által létesített kapcsolatban a rendszer transzparens módon átirányítja az újonnan létrehozott példány, amely átveszi a hibás szolgáltatáspéldányt a.

Belsőleg az Azure-ban, az átjáró segítségével átirányítja a kapcsolatot az új példány. Alapján megszakadásának a feladatátvétel teljes folyamat általában több tíz másodpercet vesz igénybe. Az átirányítási belsőleg kezeli az átjárót, mert a külső kapcsolati karakterlánc ugyanaz marad, az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé és lefelé skálázás
A magas rendelkezésre ÁLLÁSÚ modell, amikor egy Azure Database for PostgreSQL méretezése felfelé vagy lefelé hasonlóan egy új példány a megadott méretű jön létre. A meglévő adatok tárolási leválasztása az eredeti példányban, és az új példány csatlakozik.

A skálázási művelet során az adatbázis-kapcsolatok romlását történik. Az ügyfélalkalmazások le vannak választva, és nyissa meg a nem véglegesített tranzakciót megszakították. Az ügyfélalkalmazás újrapróbálja a kapcsolódást, vagy egy új kapcsolatot, miután az átjáró irányítja a kapcsolat az újonnan méretű példánnyal. 

## <a name="next-steps"></a>További lépések
- Ismerje meg [átmeneti kapcsolati hibák kezelése](concepts-connectivity.md)
- Ismerje meg, hogyan [olvasható replikákat az adatok replikálása](howto-read-replicas-portal.md)
