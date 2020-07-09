---
title: Magas rendelkezésre állás – Azure Database for MySQL
description: Ez a témakör a magas rendelkezésre állásról nyújt tájékoztatást a Azure Database for MySQL használatakor
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 50bf1502589ea0932fd45367c039e6e37135d761
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086058"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql"></a>Magas rendelkezésre állással kapcsolatos fogalmak a Azure Database for MySQL
A Azure Database for MySQL szolgáltatás garantált magas szintű rendelkezésre állást biztosít. A pénzügyileg támogatott szolgáltatói szerződés (SLA) az általánosan elérhető 99,99%-os. A szolgáltatás használata során gyakorlatilag nincs alkalmazás-leállási idő.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre állású (HA) modell a beépített feladatátvételi mechanizmusokon alapul, ha csomópont szintű megszakítás történik. A csomópont-szintű megszakítás hardverhiba miatt vagy egy szolgáltatás központi telepítésére adott válaszként fordulhat elő.

Egy Azure Database for MySQL adatbázis-kiszolgálón végzett módosítások mindig egy tranzakció kontextusában történnek. A módosításokat a rendszer szinkron módon rögzíti az Azure Storage-ban a tranzakció véglegesítése közben. Ha csomópont-szintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópontot, és az új csomóponthoz csatolja az adattárat. Minden aktív kapcsolat el lesz dobva, és a fedélzeti tranzakciók nincsenek véglegesítve.

## <a name="application-retry-logic-is-essential"></a>Az alkalmazás újrapróbálkozási logikája elengedhetetlen
Fontos, hogy a MySQL-adatbázis alkalmazásai felkészültek az eldobott kapcsolatok észlelésére és újrapróbálkozására, valamint a sikertelen tranzakciókat. Az alkalmazás újrapróbálkozásakor az alkalmazás kapcsolata transzparens módon át lesz irányítva az újonnan létrehozott példányra, amely átveszi a sikertelen példányra.

Az Azure-ban belsőleg az átjáró az új példánnyal létesített kapcsolatok átirányítására szolgál. Megszakítás esetén a teljes feladatátvételi folyamat általában több tízezer másodpercig tart. Mivel az átirányítás belsőleg van kezelve az átjárón, a külső kapcsolatok karakterlánca ugyanaz marad az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé vagy lefelé skálázás
A HA-modellhez hasonlóan, amikor egy Azure Database for MySQL vertikálisan fel-vagy leskálázásra kerül, létrejön egy új kiszolgálópéldány a megadott mérettel. A meglévő adattárolás le van választva az eredeti példányból, és az új példányhoz van csatolva.

A skálázási művelet során az adatbázis-kapcsolatok megszakítása történik. Az ügyfélalkalmazások le vannak választva, és a nyitott nem véglegesített tranzakciók megszakadnak. Miután az ügyfélalkalmazás újrapróbálkozik a kapcsolódással, vagy új kapcsolódást végez, az átjáró irányítja a kapcsolódást az újonnan méretezett példányhoz.

## <a name="next-steps"></a>További lépések
- Tudnivalók az [átmeneti kapcsolódási hibák kezelésére](concepts-connectivity.md)
- Ismerje meg, hogyan [replikálhatja adatait olvasási replikákkal](howto-read-replicas-portal.md)
