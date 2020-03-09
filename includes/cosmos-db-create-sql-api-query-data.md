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
ms.openlocfilehash: 64c900e01496dad0d5f6a96d8d790ae0c2a8c95a
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927383"
---
Az Adatkezelő lévő lekérdezések segítségével lekérheti és szűrheti az adatait.

1. Az Adatkezelő **elemek** lapjának tetején tekintse át az alapértelmezett lekérdezési `SELECT * FROM c`. A lekérdezés lekérdezi és megjeleníti a gyűjteményben lévő összes dokumentumot azonosító sorrendben. 
   
   ![Az alapértelmezett lekérdezés az Adatkezelőben a `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. A lekérdezés módosításához válassza a **Szűrő szerkesztése**lehetőséget, cserélje le az alapértelmezett lekérdezést `ORDER BY c._ts DESC`re, majd válassza a **szűrő alkalmazása**lehetőséget.
   
   ![Az alapértelmezett lekérdezés módosítása az ORDER BY c._ts DESC hozzáadásával és a Szűrő alkalmazása lehetőségre való kattintással](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   A módosított lekérdezés a dokumentumokat csökkenő sorrendben jeleníti meg az időbélyegző alapján, így most már a második dokumentum jelenik meg. 
   
   ![A lekérdezés módosult a következő SORRENDre: c. _ts DESC, és kattintson a szűrő alkalmazása lehetőségre.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Ha már ismeri az SQL-szintaxist, megadhat bármilyen támogatott [SQL-lekérdezést](../articles/cosmos-db/sql-api-sql-query.md) a lekérdezési predikátum mezőben. A Adatkezelő használatával tárolt eljárásokat, UDF és a kiszolgálóoldali üzleti logikát kiváltó eseményindítókat is létrehozhat. 

A Adatkezelő egyszerű Azure Portal hozzáférést biztosít az API-kkal elérhető beépített programozott adathozzáférési funkciókhoz. A portálon az átviteli sebességet, a kulcsok és a kapcsolódási karakterláncok beszerzését, valamint a Azure Cosmos DB-fiók metrikáinak és SLA-ának áttekintését is használhatja. 

