---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 9971b16da42cdf1de0464857291c74a947535735
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59286997"
---
Segítségével lekérdezések az Adatkezelőben lekérheti és szűrheti az adatokat.

1. Felső részén a **dokumentumok** az Adatkezelőben lapján, az alapértelmezett lekérdezéssel `SELECT * FROM c`. Ez a lekérdezés lekéri és összes dokumentum a gyűjtemény azonosítója sorrendben jeleníti meg. 
   
   ![Az alapértelmezett lekérdezés az Adatkezelőben a `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. A lekérdezés módosításához válassza **szűrő szerkesztése**, cserélje le az alapértelmezett lekérdezéssel rendelkező `ORDER BY c._ts DESC`, majd válassza ki **szűrő alkalmazása**.
   
   ![Az alapértelmezett lekérdezés módosítása az ORDER BY c._ts DESC hozzáadásával és a Szűrő alkalmazása lehetőségre való kattintással](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   A módosított lekérdezés megjeleníti a dokumentumok az időbélyegzőt, így most már a második dokumentum alapján csökkenő sorrendben az első helyen szerepel. 
   
   ![Az ORDER BY c._ts DESC lekérdezés és szűrés gombra kattintva módosítani](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Ha ismeri az SQL szintaxisát, megadhatja bármelyik támogatott [SQL-lekérdezések](../articles/cosmos-db/sql-api-sql-query.md) a lekérdezési predikátumhoz a. Az adatkezelő segítségével hozzon létre a tárolt eljárásokat, felhasználói függvények és eseményindítók a kiszolgálóoldali üzleti logikát. 

Az adatkezelő könnyen elérhetők az Azure portál összes beépített, programozható adatok hozzáférési funkcióit az API-k. A portál skálázhatja átviteli sebességet, a kulcsok és a kapcsolati karakterláncok beolvasása és a metrikák és SLA-k áttekintése az Azure Cosmos DB-fiók is használhatja. 

