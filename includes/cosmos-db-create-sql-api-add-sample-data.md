---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70020174"
---
Most már hozzáadhat adatokat az új tárolóhoz az Adatkezelővel.

1. Az **Adatkezelőben**bontsa ki a **Feladatok** adatbázist, bontsa ki az **Elemek tárolót.** Jelölje be **az Elemek**lehetőséget, majd válassza az **Új elem**lehetőséget.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Most adjon hozzá egy dokumentumot a tárolóhoz a következő szerkezettel.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Miután felvette a jsont a **Dokumentumok** lapra, válassza a **Mentés gombot.**

    ![Másolás jsonadatokközött, és az Azure Portalon válassza a Mentés adatkezelőben lehetőséget](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Hozzon létre és mentsen még egy dokumentumot, amelyben egyedi értéket szúr be az `id` tulajdonság számára, és tetszés szerint módosítja a többi tulajdonságot. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.