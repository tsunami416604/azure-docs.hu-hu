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
ms.openlocfilehash: 6656c374ae0ff4aae7f99fd340e9e25e5cbc67d1
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854145"
---
Az új tárolóba az Adatkezelő használatával adhat hozzá adatait.

1. A **adatkezelő**bontsa ki a **feladatok** adatbázist, és bontsa ki az **elemek** tárolót. Válassza az **elemek**lehetőséget, majd kattintson az **új elem**lehetőségre.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Most adjon hozzá egy dokumentumot a tárolóhoz az alábbi struktúrával.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Miután hozzáadta a JSON-t a **dokumentumok** laphoz, válassza a **Mentés**lehetőséget.

    ![Másolja a JSON-adatfájlt, és válassza a Mentés Adatkezelő a Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Hozzon létre és mentsen még egy dokumentumot, amelyben egyedi értéket szúr be az `id` tulajdonság számára, és tetszés szerint módosítja a többi tulajdonságot. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.