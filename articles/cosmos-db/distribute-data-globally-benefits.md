---
title: Azure Cosmos DB globális terjesztésének főbb előnyei
description: Tudnivalók a georeplikáció, több főkiszolgálós és használati esetekben, ahol hasznos által kínált Azure Cosmos DB több főkiszolgálós, key előnyeit.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 11d70a648bfc1882753688e5352835b04cee6b9f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968345"
---
# <a name="distribute-data-globally-with-azure-cosmos-db"></a>Globális adatterjesztés az Azure Cosmos DB-hez

Ez a cikk ismerteti az adatok elosztása globálisan és a egy valós idejű felhasználói helyzetek, ahol van szükség a globális adatok terjesztési fő előnye.

## <a name="key-benefits"></a>Főbb előnyök

A következő előnyökkel jár, az Azure Cosmos DB globális terjesztési képességek fiókokhoz érhető el:

* **Egy számjegyű késés** – Azure Cosmos DB biztosít < 10 ms olvasási és írási késés 99 százalékon által garantált [pénzügyileg támogatott SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **5 – 9 rendelkezésre állási** – Azure Cosmos DB kínál 99,999 %-os írási és olvasási rendelkezésre állás érdekében által garantált [pénzügyileg támogatott SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/).

* **Rugalmas ütközésfeloldás** – az ügyfelek több főkiszolgálós képességeivel, az Azure Cosmos DB három módot biztosít a globális adatok integritásának megőrzése, és a konzisztencia biztosításához ütközések kezelése.

* **Hangolható konzisztencia** – Azure Cosmos DB támogatja a különböző 5 [konzisztenciaszintek](consistency-levels.md) globális elosztás támogatja a többszörös elsajátítására képességgel rendelkező fiókok esetében az összes, de konzisztenciát szem előtt.

* **Implicit hibatűrés** – több régióra replikált adatok, alkalmazások továbbra is élvezhetik magas hibatűrés regionális üzemkimaradások utáni helyreállításon ellen.

## <a name="use-cases"></a>Használati esetek

Íme a forgatókönyveket, amelyek kihasználhatják a csak egy kis példa a globálisan telepítési képességeket Azure Cosmos DB-hez.

* **Közösségimédia-alkalmazások** – közösségi hálózati alkalmazásaikat megkövetelése alacsony késésű, magas rendelkezésre állású, és nagyszerű felhasználói élményt nyújt a felhasználók méretezhetőség a világon.

* **IoT** – földrajzilag elosztott edge-telepítések gyakran kell nyomon követnie az idősorozat-adatok több helyről. Minden egyes eszköz is lehet a következő helyen a legközelebb eső régióban. Eszközök áthelyezni különböző helyek, ha azokat az eszközöket is rehomed írni a legközelebbi elérhető régióban.

* **E-kereskedelmi** -E-kereskedelmi rendkívül alacsony késésű, valamint a magas rendelkezésre állást igényel. Az adatok olvasása és írása a földrajzi elosztás elérhető adatok legközelebbi a felhasználók számára, az alkalmazások válaszkészségét növelése. Az olvasásokat és az írásokat több régióban rendelkezésre állási nagyobb rendelkezésre állást biztosít.

* **Csalás/rendellenesség-észlelést** – gyakran olyan alkalmazásokat, amelyek figyelése, vagy felhasználói fiók tevékenysége globálisan terjesztett és kell nyomon követésére egyszerre való frissítése, hogy a metrikák beágyazott kockázati pontszámokat több eseményt.

* **Szoftverhasználat-mérő** - számbavételi és használat szabályozása (például az API-hívás, tranzakció/másodperc, perc használt) az egyszerűség kedvéért használja az Azure Cosmos DB több főkiszolgálós globálisan implementálható. Beépített ütközésfeloldás biztosítja mindkét számát és a valós idejű rendelet pontosságát.

## <a name="next-steps"></a>További lépések  

Ebben a cikkben megismerkedett a főbb előnyeit és alkalmazási helyzetei az Azure Cosmos DB a globális adatok telepítési képességeket. Ezután keresse meg az alábbi forrásanyagokat:

* [Hogyan teszi lehetővé az Azure Cosmos DB a kulcsrakész globális terjesztés](distribute-data-globally-turnkey.md)

* [Azure Cosmos DB globális adatbázis-replikáció konfigurálása](tutorial-global-distribution-sql-api.md)

* [Azure Cosmos DB-fiókok több főkiszolgálós engedélyezése](enable-multi-master.md)

* [Az Azure Cosmos DB automatikus és kézi feladatátvételi működése](regional-failover.md)

* [Az Azure Cosmos DB ütközésfeloldás ismertetése](multi-master-conflict-resolution.md)

* [több főkiszolgálós használata a nyílt forráskódú NoSQL-adatbázisok](multi-master-oss-nosql.md)
