---
title: Magas rendelkezésre állás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server magas rendelkezésre állásával kapcsolatos információkat tartalmaz.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 80229ff78c4570db583f1218d5d2f72da2dec388
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74768571"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Magas rendelkezésre állással kapcsolatos fogalmak Azure Database for PostgreSQL – egyetlen kiszolgálón
A Azure Database for PostgreSQL szolgáltatás garantált magas szintű rendelkezésre állást biztosít. A pénzügyileg támogatott szolgáltatói szerződés (SLA) az általánosan elérhető 99,99%-os. A szolgáltatás használata során gyakorlatilag nincs alkalmazás-leállási idő.

## <a name="high-availability"></a>Magas rendelkezésre állás
A magas rendelkezésre állású (HA) modell a beépített feladatátvételi mechanizmusokon alapul, ha csomópont szintű megszakítás történik. A csomópont-szintű megszakítás hardverhiba miatt vagy egy szolgáltatás központi telepítésére adott válaszként fordulhat elő.

Egy Azure Database for PostgreSQL adatbázis-kiszolgálón végzett módosítások mindig egy tranzakció kontextusában történnek. A módosításokat a rendszer szinkron módon rögzíti az Azure Storage-ban a tranzakció véglegesítése közben. Ha csomópont-szintű megszakítás történik, az adatbázis-kiszolgáló automatikusan létrehoz egy új csomópontot, és az új csomóponthoz csatolja az adattárat. Minden aktív kapcsolat el lesz dobva, és a fedélzeti tranzakciók nincsenek véglegesítve.

## <a name="application-retry-logic-is-essential"></a>Az alkalmazás újrapróbálkozási logikája elengedhetetlen
Fontos, hogy a PostgreSQL-adatbázis alkalmazásai felkészültek az eldobott kapcsolatok észlelésére és újrapróbálkozására, valamint a sikertelen tranzakciókat. Az alkalmazás újrapróbálkozásakor az alkalmazás kapcsolata transzparens módon át lesz irányítva az újonnan létrehozott példányra, amely átveszi a sikertelen példányra.

Az Azure-ban belsőleg az átjáró az új példánnyal létesített kapcsolatok átirányítására szolgál. Megszakítás esetén a teljes feladatátvételi folyamat általában több tízezer másodpercig tart. Mivel az átirányítás belsőleg van kezelve az átjárón, a külső kapcsolatok karakterlánca ugyanaz marad az ügyfélalkalmazások számára.

## <a name="scaling-up-or-down"></a>Felfelé vagy lefelé skálázás
A HA-modellhez hasonlóan, amikor egy Azure Database for PostgreSQL vertikálisan fel-vagy leskálázásra kerül, létrejön egy új kiszolgálópéldány a megadott mérettel. A meglévő adattárolás le van választva az eredeti példányból, és az új példányhoz van csatolva.

A skálázási művelet során az adatbázis-kapcsolatok megszakítása történik. Az ügyfélalkalmazások le vannak választva, és a nyitott nem véglegesített tranzakciók megszakadnak. Miután az ügyfélalkalmazás újrapróbálkozik a kapcsolódással, vagy új kapcsolódást végez, az átjáró irányítja a kapcsolódást az újonnan méretezett példányhoz. 

## <a name="next-steps"></a>További lépések
- Tudnivalók az [átmeneti kapcsolódási hibák kezelésére](concepts-connectivity.md)
- Ismerje meg, hogyan [replikálhatja adatait olvasási replikákkal](howto-read-replicas-portal.md)
