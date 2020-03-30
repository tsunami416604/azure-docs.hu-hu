---
title: Regionális jelenlét az Azure Cosmos DB-vel
description: Ez a cikk ismerteti az Azure Cosmos DB és a különböző felhőalapú környezetek regionális jelenlétét ismerteti.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753225"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionális jelenlét az Azure Cosmos DB-vel

Az Azure Cosmos DB egy alapvető szolgáltatás az Azure-ban, és alapértelmezés szerint mindig elérhető minden régióban, ahol az Azure elérhető. Az Azure jelenleg világszerte [54 régióban](https://azure.microsoft.com/global-infrastructure/regions/) érhető el. 

[![Azok a régiók, ahol az Azure Cosmos DB elérhető](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

A Cosmos DB mind az öt különböző Azure-felhőkörnyezetben érhető el az ügyfelek számára:

* **Az Azure nyilvános** felhője, amely globálisan elérhető.

* **Az Azure China 21Vianet** a Microsoft és a 21Vianet, az ország egyik legnagyobb kínai internetszolgáltatója közötti egyedi partnerségen keresztül érhető el.

* **Az Azure Germany** adatkezelő imitátusként nyújt szolgáltatásokat, amely biztosítja, hogy az ügyféladatok Németországban maradjanak a Deutsche Telecom leányvállalata, a T-Systems International GmbH ellenőrzése alatt, amely német vagyonkezelőként működik.

* Az **Azure Government** az Egyesült Államok négy régiójában érhető el az Egyesült Államok kormányzati szervei és partnerei számára. 

* **Az Azure Government for Department of Defense (DoD)** az Egyesült Államok két régiójában érhető el az Egyesült Államok Védelmi Minisztériuma számára.

## <a name="regional-presence-with-global-distribution"></a>Regionális jelenlét globális elosztással

Az Azure Cosmos DB által elérhetővé tett összes API (beleértve az SQL, a MongoDB, a Cassandra, a Gremlin és a Table) alapértelmezés szerint minden Azure-régióban elérhető. Például a MongoDB és a Cassandra API-k az Azure Cosmos DB nem csak az összes globális Azure-régióban, hanem a szuverén felhők, például Kína, Németország, kormány és a Védelmi Minisztérium (DoD) régiókban is elérhetővé teheti.

Az Azure Cosmos DB egy [globálisan elosztott](distribute-data-globally.md) adatbázis-szolgáltatás. Tetszőleges számú Azure-régiót társíthat az Azure Cosmos-fiókhoz, és az adatok automatikusan és átláthatóan replikálhatók. Bármikor hozzáadhat vagy eltávolíthat egy régiót az Azure Cosmos-fiókba. A kulcsrakész globális terjesztési képességgel és a többfős replikációs protokollal az Azure Cosmos DB kevesebb mint 10 ms olvasási és írási késleltetést kínál a 99.percentilis, a 99.999 olvasási és írási rendelkezésre állás, valamint a kiépített kapacitás rugalmas méretezése az Azure Cosmos-fiókhoz társított összes régióban az olvasási és írási műveletek átviteli. Az Azure Cosmos DB öt jól definiált konzisztenciamodellt is kínál, és választhat, hogy egy adott konzisztenciamodellt alkalmaz az adatokra. Végül az Azure Cosmos DB az egyetlen olyan adatbázis-szolgáltatás az iparágban, amely átfogó [szolgáltatásiszint-szerződést (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) biztosít, amely magában foglalja a kiosztott átviteli, a 99 százalékos késést, a magas rendelkezésre állást és a konzisztenciát. A fenti képességek az összes Azure-felhőben elérhetők.

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB alapfogalmait az alábbi cikkekkel ismerheti meg:

* [Globális adatterjesztés](distribute-data-globally.md)
* [Azure Cosmos DB-fiók kezelése](manage-account.md)
* [Az Azure Cosmos-tárolók és -adatbázisok átviteli-átviteli átbocsátása](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
