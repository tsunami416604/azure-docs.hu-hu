---
title: "Magas rendelkezésre állású fogalmak PostgreSQL Azure-adatbázisban"
description: "Ez a cikk a magas rendelkezésre állás információkat tartalmazza, PostgreSQL az Azure-adatbázis használata esetén."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 203a142a21153935e172508e62b813dca95468cb
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Magas rendelkezésre állású fogalmak PostgreSQL Azure-adatbázisban
Az Azure-adatbázishoz PostgreSQL-szolgáltatáshoz biztosít garantált magas szintű rendelkezésre állási. A pénzügyi biztonsági szolgáltatásiszint-szerződéssel (SLA) alapján általánosan rendelkezésre álló 99,99 %. Nincs állásidő alkalmazás gyakorlatilag a szolgáltatás használatakor.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre ÁLLÁS modell beépített feladatátvételi mechanizmusok alapul, a csomópont-szintű megszakadása esetén. A csomópont-szintű megszakítás akkor fordulhat elő, hardverhiba miatt vagy egy szolgáltatás telepítését.

Mindig egy PostgreSQL-kiszolgáló Azure-adatbázison végrehajtott változások történnek, egy tranzakció környezetében. Módosítások rögzíti szinkron módon történik az Azure storage a tranzakció során. A csomópont-szintű megszakadása esetén az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópont, és adattárolás csatolja az új csomópont. Minden aktív kapcsolatok megszakadnak, és aktív tranzakciók nem kerülnek.

## <a name="application-retry-logic-is-essential"></a>Alkalmazás újrapróbálkozási logika elengedhetetlen.
Fontos, hogy PostgreSQL adatbázis-alkalmazások beépített észleléséhez, majd próbálja megismételni kapcsolatok eldobott tranzakciók nem sikerült. Az alkalmazás újbóli, az alkalmazás kapcsolat transzparens módon átirányítja az újonnan létrehozott példányát veszi át a sikertelen példányhoz.

Belsőleg, az Azure-ban egy átjáró használatával átirányítja a kapcsolatot az új példány. Megzavarná, akkor a teljes feladatátvételi általában, amelynek során több tíz, másodpercben. Az átirányítási belsőleg kezeli az átjáró, mert a külső kapcsolati karakterlánc változatlan marad, az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé vagy lefelé skálázás
A magas rendelkezésre ÁLLÁSÚ modell, amikor az Azure adatbázisban PostgreSQL méretezett felfelé vagy lefelé hasonló, egy új példány a megadott méretű jön létre. A meglévő adatok tárolási az eredeti példány le, és az új példány csatolva.

A méretezési művelet megtörténik az adatbázis-kapcsolatok megszakadását. Az ügyfélalkalmazások számára le van választva, és nyissa meg a nem véglegesített tranzakciók pedig leállítottak. Az ügyfélalkalmazás újrapróbálja a kapcsolódást, vagy egy új kapcsolatot, ha az átjáró irányítja a kapcsolat az újonnan méretű példányához. 

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [Azure adatbázis PostgreSQL – áttekintés](overview.md)
