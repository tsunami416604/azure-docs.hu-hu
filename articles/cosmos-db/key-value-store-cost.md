---
title: "Azure Cosmos-adatbázis egy kulcs-érték tárolóként – költség áttekintése |} Microsoft Docs"
description: "További tudnivalók az alacsony költségű Azure Cosmos DB használatával egy kulcs-érték tárolóként."
keywords: "kulcs-érték tároló"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mimig
ms.openlocfilehash: 33eef1b51a5ee00b0fa67096030ed9ce92cf768e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos-adatbázis egy kulcs-érték tárolóként – költség áttekintése

Azure Cosmos-adatbázis egy olyan globálisan elosztott, több modellre adatbázis szolgáltatás magas rendelkezésre állású, nagy méretű alkalmazások könnyen készítéséhez. Alapértelmezés szerint Azure Cosmos DB automatikusan elvégzi az ingests, hatékony összes adatot. Ez lehetővé teszi, hogy gyors és egységes [SQL](documentdb-sql-query.md) (és [JavaScript](programming.md)) az adatok bármilyen lekérdezések. 

Ez a cikk ismerteti a Azure Cosmos DB költségét egyszerű írásra, és olvasási műveleteket, ha egy kulcs-érték tárolóként szolgál. Az írási műveletek közé tartozik a beszúrások, cserél, törléseket és dokumentumok upserts. Azure Cosmos DB ajánlatok garantált mellett, amelyek biztosítják a magas rendelkezésre állás 99,99 %, < 10 ms késleltetés olvasása és < 15 ms késleltetés (indexelt) számára, a 99th PERCENTILIS, írja. 

## <a name="why-we-use-request-units-rus"></a>Miért kérelem egységek (RUs) használjuk.

Azure Cosmos DB teljesítmény mennyisége alapján kiosztott [kérelem egységek](request-units.md) (RU) a partíció. A kiépítés második részletességű és értékesítik RUs/mp és RUs/perc ([való nem keverendő össze a óránkénti számlázási](https://azure.microsoft.com/pricing/details/cosmos-db/)). A pénznem, amely leegyszerűsíti az alkalmazáshoz szükséges átviteli kiépítési RUs kell tekinteni. Az ügyfelek nem olvasható megkülönböztetése gondol írási és olvasási kapacitásegység kell. RUs egyetlen pénznem modelljét hoz létre a hatékonyság a kiosztott kapacitást olvasási és írási műveletek közötti megosztásához. Ez a kiosztott kapacitást modell lehetővé teszi, hogy a szolgáltatás segítségével garantált alacsony késéssel és magas rendelkezésre állású következetes és kiszámítható teljesítményt biztosít. Végezetül modell átviteli RU használjuk, de minden kiosztott RU rendelkezik is egy meghatározott mennyiségű erőforrást (memória, mag). RU/mp nincs csak iops-érték.

Globálisan elosztott adatbázis rendszerként Cosmos DB a csak az Azure-szolgáltatás, amely egy SLA-t a késés, az átviteli sebesség és a magas rendelkezésre állású mellett konzisztencia. Az átviteli sebesség kiépítése a Cosmos-adatbázis adatbázis-fiókjához társított régió alkalmazható. Olvasási műveleteknél a Cosmos DB kínál több, jól meghatározott [konzisztenciaszintek](consistency-levels.md) is választhat. 

A következő táblázatban RUs száma hajtsa végre az olvasási és írási tranzakciót 1KB és 100KBs dokumentum mérete alapján szükséges.

|Elem mérete|1 olvasása|1 írási|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási költsége

Ha 1000 RU/mp, 3,6 m RU/óra és a rendszer a díjak költsége $0.08 az óra (a az amerikai és európai). 1KB méretű dokumentum, ez azt jelenti, hogy használatba vehetné 3,6 m olvasási vagy 0,72 m ír (3.6mRU / 5) a létesített átviteli sebesség használatával. Normalizálva millió olvasási és írási, a költség lenne $0,022 /m olvasási ($0.08 / 3,6) és a $0.111/ m írja a ($0.08 / 0,72). Egy millió válik minimális, az alábbi táblázatban látható módon.

|Elem mérete|1m olvasása|1m írási|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


A legtöbb alapvető blob vagy objektum tárolók szolgáltatások költség $0,40 / millió olvasási tranzakció és $5 millió írási tranzakciónként. Ha optimálisan használja, Cosmos DB 98 %-kal (1KB tranzakciókhoz) ezek más megoldásokat olcsóbbak lehet.

## <a name="next-steps"></a>Következő lépések

Kövessen bennünket az új cikkek az Azure Cosmos DB az erőforrás-kiépítés optimalizálásához. Addig nyugodtan használja a [RU Számológép](https://www.documentdb.com/capacityplanner).

