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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927383"
---
Az adatkezelőlekérdezései segítségével bekeresheti és szűrheti az adatokat.

1. Az Adatkezelő **Elemek** lapjának tetején tekintse át `SELECT * FROM c`az alapértelmezett lekérdezést. Ez a lekérdezés a gyűjteményben lévő összes dokumentumot azonosító sorrendben olvassa be és jeleníti meg. 
   
   ![Az alapértelmezett lekérdezés az Adatkezelőben a `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. A lekérdezés módosításához válassza a **Szűrő szerkesztése**lehetőséget, cserélje le az alapértelmezett lekérdezést a lehetőségre, `ORDER BY c._ts DESC`majd válassza a Szűrő **alkalmazása**lehetőséget.
   
   ![Az alapértelmezett lekérdezés módosítása az ORDER BY c._ts DESC hozzáadásával és a Szűrő alkalmazása lehetőségre való kattintással](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   A módosított lekérdezés a dokumentumokat az időbélyegzőjük alapján csökkenő sorrendben jeleníti meg, így most a második dokumentum jelenik meg először. 
   
   ![A lekérdezés módosítása rendelésre c._ts A DESC-re, majd a Szűrő alkalmazása parancsra kattintva](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Ha ismeri az SQL szintaxist, a lekérdezés predikátuma mezőbe bármilyen támogatott [SQL-lekérdezést](../articles/cosmos-db/sql-api-sql-query.md) megadhat. A Data Explorer segítségével tárolt eljárásokat, UDF-eket és eseményindítókat is létrehozhat a kiszolgálóoldali üzleti logikához. 

A Data Explorer egyszerű Azure Portal hozzáférést biztosít az API-kban elérhető összes beépített automatizált adatelérési funkcióhoz. A portál használatával is skálázhatja az átviteli, kulcsok és kapcsolati karakterláncok leválasztása, és tekintse át a metrikák és Az SLA-k az Azure Cosmos DB-fiók. 

