---
title: "Magas rendelkezésre állású kapcsolatos fogalmak, MySQL az Azure-adatbázis"
description: "Ez a témakör a magas rendelkezésre állás MySQL az Azure-adatbázis használata esetén"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 58f10f0a0938da5887d44959f65b9016dfa88f2c
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Magas rendelkezésre állású kapcsolatos fogalmak, MySQL az Azure-adatbázis
Az Azure-adatbázishoz a MySQL-szolgáltatás biztosít garantált magas szintű elérhetőség. A pénzügyi biztonsági szolgáltatásiszint-szerződéssel (SLA) alapján általánosan rendelkezésre álló 99,99 %. Nincs állásidő alkalmazás gyakorlatilag a szolgáltatás használatakor.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre ÁLLÁS modell beépített feladatátvételi mechanizmusok alapul, a csomópont-szintű megszakadása esetén. A csomópont-szintű megszakítás akkor fordulhat elő, hardverhiba miatt vagy egy szolgáltatás telepítését.

Mindig MySQL adatbázis-kiszolgáló egy Azure-adatbázison végrehajtott változások történnek, egy tranzakció környezetében. Módosítások rögzíti szinkron módon történik az Azure storage a tranzakció során. A csomópont-szintű megszakadása esetén az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópont, és adattárolás csatolja az új csomópont. Minden aktív kapcsolatok megszakadnak, és aktív tranzakciók nem kerülnek.

## <a name="application-retry-logic-is-essential"></a>Alkalmazás újrapróbálkozási logika elengedhetetlen.
Fontos, hogy MySQL adatbázis-alkalmazások beépített észlelésére, és próbálkozzon újra kapcsolatok eldobott tranzakciók nem sikerült. Az alkalmazás újbóli, az alkalmazás kapcsolat transzparens módon átirányítja az újonnan létrehozott példányát veszi át a sikertelen példányhoz.

Belsőleg, az Azure-ban egy átjáró használatával átirányítja a kapcsolatot az új példány. Megzavarná, akkor a teljes feladatátvételi általában, amelynek során több tíz, másodpercben. Az átirányítási belsőleg kezeli az átjáró, mert a külső kapcsolati karakterlánc változatlan marad, az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé vagy lefelé skálázás
A magas rendelkezésre ÁLLÁSÚ modell, amikor egy MySQL az Azure-adatbázis méretezése felfelé vagy lefelé hasonló, egy új példány a megadott méretű jön létre. A meglévő adatok tárolási az eredeti példány le, és az új példány csatolva.

A méretezési művelet megtörténik az adatbázis-kapcsolatok megszakadását. Az ügyfélalkalmazások számára le van választva, és nyissa meg a nem véglegesített tranzakciók pedig leállítottak. Az ügyfélalkalmazás újrapróbálja a kapcsolódást, vagy egy új kapcsolatot, ha az átjáró irányítja a kapcsolat az újonnan méretű példányához. 

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [Azure adatbázis MySQL – áttekintés](overview.md)
