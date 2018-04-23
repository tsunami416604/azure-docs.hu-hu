---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
Az Adatkezelővel így már lekérdezések használatával lekérheti és szűrheti adatait.

1. Alapértelmezés szerint a lekérdezés `SELECT * FROM c` értékre van állítva. Ez az alapértelmezett lekérdezés lekéri és megjeleníti a gyűjteményben szereplő összes dokumentumot. 

    ![Az alapértelmezett lekérdezés az Adatkezelőben a `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. Maradjon a **Dokumentumok** lapon és módosítsa a lekérdezést a **Szűrő szerkesztése** gombra kattintva, a lekérdezési predikátumhoz adja hozzá az `ORDER BY c._ts DESC` elemet, végül pedig kattintson a **Szűrő alkalmazása** elemre.

    ![Az alapértelmezett lekérdezés módosítása az ORDER BY c._ts DESC hozzáadásával és a Szűrő alkalmazása lehetőségre való kattintással](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

A módosított lekérdezés az időbélyegük alapján csökkenő sorrendben listázza a dokumentumokat, így most már a második dokumentum van a lista elején. Ha ismeri az SQL szintaxisát, bármelyik támogatott [SQL-lekérdezést](../articles/cosmos-db/sql-api-sql-query.md) megadhatja ebben a mezőben. 

Ezzel befejeződött az Adatkezelőben végzett munkánk. Mielőtt áttérünk a kódokra, ne felejtse el, hogy az Adatkezelővel létrehozhat tárolt eljárásokat, felhasználói függvényeket és a kiszolgálóoldali üzleti logikákat végrehajtó eseményindítókat is, valamint szabályozhatja az átviteli sebességet is. Az Adatkezelő hozzáférhetővé teszi az API-k összes beépített, programozható adatelérési funkcióját, és az Azure Portalon tárolt adataihoz is egyszerű hozzáférést biztosít.