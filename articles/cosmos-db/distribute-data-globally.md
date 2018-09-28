---
title: Globális adatterjesztés az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg a globális georeplikáció, több főkiszolgálós, feladatátvétel és data recovery global Database-adatbázisok az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás használatával.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 227c243d82665dc533e3bfa6a1fe3e9bb775a262
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408895"
---
# <a name="global-data-distribution-with-azure-cosmos-db"></a>Az Azure Cosmos DB globális terjesztési

Az Azure széles körben használt – Ez egy globális lefedettséggel rendelkezik több mint 50 földrajzi régióban, és folyamatosan bővülnek. A globális jelenlét és több főkiszolgálós támogatással elkészítheti, telepítheti és globálisan elosztott alkalmazások egyszerű kezelés lehetővé teszi a fejlesztők számára kínál az Azure egyedi képességeit egyik célja.

Az [Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása az alapvető fontosságú alkalmazásokhoz. Azure Cosmos DB biztosítja a kulcsrakész globális disztribúciót, [átviteli sebesség és tárterület rugalmas méretezését](../cosmos-db/partition-data.md) világszerte, egyjegyű olvasási és írási ezredmásodperces késés 99 százalékon [jól definiált modellek](consistency-levels.md), és garantált magas rendelkezésre állású, mindezt [iparágvezető átfogó SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Az Azure Cosmos DB [automatikusan indexeli az összes adatot](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy sémákkal vagy indexkezeléssel foglalkozik.

## <a name="global-distribution-with-multi-master"></a>Több főkiszolgálós globális terjesztés

Felhőalapú szolgáltatásként az Azure Cosmos DB gondosan kategóriában a több-bérlős, a globális terjesztés és a több főkiszolgálós a dokumentum, kulcs-érték, gráf és oszlopcsalád-adatmodelleket.

![Az Azure Cosmos DB-tárolók particionált, és az elosztott három régióban](./media/distribute-data-globally/global-apps.png)

**Egyetlen Azure Cosmos DB-tároló particionált, és több Azure-régiók között elosztva**

Ahogy megtudtuk rendelkezik Azure Cosmos DB létrehozása közben, globális terjesztés hozzáadása nem másodrangú ügyként kezelni. Nem lehet "menetes az" egy "egyszeri" adatbázis helyrendszer interaktív irányítópultunkat. Egy globálisan elosztott adatbázis által kínált funkciók kiterjednek az olyan hagyományos földrajzi vész-helyreállítási (Geo-DR) "egyhelyes" adatbázisok által kínált által nyújtott szolgáltatások mellett. Egyetlen hely adatbázisok Geo-DR-funkciót kínál a egy szigorú alkészletéből állnak a globálisan elosztott adatbázisok.

Az Azure Cosmos DB kulcsrakész globális disztribúciót a fejlesztők nem rendelkezik a saját replikációs szerkezetkialakító hozhat létre, az adatbázis naplója keresztül Lambda minta alkalmazó, vagy "dupla írások" végrehajtása több régióban. Végzünk *nem* ezek a módszerek ajánljuk, mivel nem lehetséges, az ilyen megközelítés majd eredményes SLA-kat biztosít.

## <a id="Next Steps"></a>Következő lépések

* [Hogyan teszi lehetővé az Azure Cosmos DB a kulcsrakész globális terjesztés](distribute-data-globally-turnkey.md)

* [Az Azure Cosmos DB globális terjesztésének előnyei](distribute-data-globally-benefits.md)

* [Azure Cosmos DB globális adatbázis-replikáció konfigurálása](tutorial-global-distribution-sql-api.md)

* [Azure Cosmos DB-fiókok több főkiszolgálós engedélyezése](enable-multi-master.md)

* [Az Azure Cosmos DB automatikus és kézi feladatátvételi működése](regional-failover.md)

* [Az Azure Cosmos DB ütközésfeloldás ismertetése](multi-master-conflict-resolution.md)

* [több főkiszolgálós használata a nyílt forráskódú NoSQL-adatbázisok](multi-master-oss-nosql.md)
