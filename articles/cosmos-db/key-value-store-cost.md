---
title: Az Azure Cosmos DB egy kulcs-érték tárolóként – költségek áttekintése
description: További információ az alacsony költségű, mint egy kulcsérték-tárolóba az Azure Cosmos DB használatával.
keywords: kulcs-érték tároló
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 7331db23757ab4eaae054c7fe640952fe22a7917
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840868"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Az Azure Cosmos DB egy kulcs-érték tárolóként – költségek áttekintése

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás magas rendelkezésre állású, nagy méretű alkalmazások létrehozását, egyszerűen a. Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli az összes adatot, feltölti, hatékony. Ez lehetővé teszi a gyors és következetes [SQL](how-to-sql-query.md) (és [JavaScript](programming.md)) bármilyen típusú adatot-lekérdezéseket. 

Ez a cikk ismerteti az Azure Cosmos DB költsége egyszerű írási és olvasási műveletek, ha használatban van egy kulcs/érték tároló. Az írási műveletnek számítanak többek között Beszúrások, cserél, törlése és a dokumentumok upserts. Mellett kínál 99,99 %-os rendelkezésre állási SLA minden egyrégiós és minden többrégiós fiókok Könnyített konzisztenciáját, és 99,999 %-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz, Azure Cosmos DB-ajánlatok garantált < 10 ms késést olvassa be és < 15 ms késést (indexelt), az írási műveleteknél a 99. percentilis. 

## <a name="why-we-use-request-units-rus"></a>Miért kérelemegység (RU) használjuk.

Az Azure Cosmos DB-teljesítmény mennyisége alapján üzembe helyezett [kérelemegység](request-units.md) (RU) a partíció. A kiépítés egy második granularitási és megvásárlása esetén a kérelemegység/s ([, nem tévesztendő össze az óradíjas](https://azure.microsoft.com/pricing/details/cosmos-db/)). Fenntartott egységek kell tekinteni, amely leegyszerűsíti az átviteli sebességgel az alkalmazás üzembe helyezése egy pénznemet. Ügyfeleinknek nem kell gondolja át, hogy olvasási megkülönböztetése és a kapacitásegységek írása. Az egyetlen pénznem modell kérelemegységet hoz létre, növelhető a hatékonyság a kiépített lemezkapacitás olvasási és írási közötti megosztásához. A kiosztott kapacitást modell lehetővé teszi, hogy a szolgáltatás kiszámítható és következetes teljesítményű, garantáltan alacsony késéssel és magas rendelkezésre állást biztosít. Végül modell átviteli RU használjuk, de minden kiosztott RU is rendelkezik egy meghatározott mennyiségű erőforrás (memória, Core). RU/s nem csak az iops-t.

Cosmos DB egy globálisan elosztott adatbázisrendszerként lesz a csak az Azure szolgáltatást biztosító SLA-t a késés, átviteli sebesség és a konzisztencia magas rendelkezésre állás mellett. Az üzembe helyezi az átviteli sebesség a Cosmos DB-adatbázisfiókhoz társított régiók mindegyikében alkalmazza. Az olvasási, Cosmos DB több jól definiált kínál [konzisztenciaszintek](consistency-levels.md) a közül választhat. 

Az alábbi táblázat a fenntartott egységek számát hajtsa végre az olvasási és írási tranzakciók 1 KB-os és 100 Tudásbázis dokumentum mérete alapján szükséges.

|Elem mérete|1 olvasása|1 írási|
|-------------|------|-------|
|1 KB|1 RU|5 Kérelemegységet|
|100 KB|10 RU|50 kérelemegység|

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási költség

Ha üzembe helyezi az 1000 RU/s, 3.6-os m Kérelemegység/óra és fogja a összegek költségét 0,08 óra (az Egyesült Államok és Európa). Egy 1 KB-os mérete a dokumentumhoz, ez azt jelenti, hogy felhasználhatja 3.6-m olvasási 0,72-m ír (3.6-os m Kérelemegység / 5) használatával a kiosztott átviteli sebesség. 1 millió olvasási és írási normalizálva, a díj a következő lesz $0,022 /m olvasási (0,08 / 3.6-os) és 0.111 $/ millió ír (0,08 / 0,72). A költség / millió válik minimális, az alábbi táblázatban látható módon.

|Elem mérete|1-m olvasása|1-m írási|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


A legtöbb a alapszintű blobot vagy objektum tárolók szolgáltatások ingyenesen 0,40 $ / millió olvasási tranzakciós és 5 $ / millió írási tranzakció. Ha használta az optimális, Cosmos DB lehet akár 98 %-kal olcsóbb, mint a más megoldások (az 1 KB-os tranzakció).

## <a name="next-steps"></a>További lépések

Kövessen bennünket az új cikkek az Azure Cosmos DB erőforrás-kiépítés optimalizálásához. Addig nyugodtan használhatja az [RU Számológép](https://www.documentdb.com/capacityplanner).

