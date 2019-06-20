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
ms.openlocfilehash: 127d67cc3b5dcd0ddd585470821eb1baa08c2388
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179665"
---
Az Adatkezelő segítségével adatokat adhat hozzá az új gyűjteményhez.

1. Az új adatbázis az Adatkezelőben a Gyűjtemények ablaktáblán jelenik meg. Bontsa ki a **Feladatok** adatbázist, majd az **Elemek** gyűjteményt, végül kattintson a **Dokumentumok**, majd pedig az **Új dokumentumok** lehetőségre. 

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Adjon hozzá egy dokumentumot a gyűjteményhez az alábbi struktúrával.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. A JSON hozzáadása után a **Dokumentumok** lapon kattintson a **Mentés** gombra.

    ![Másolja át a json-adatokat, és kattintson a Mentés gombra az Adatkezelőben az Azure-portálon](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Hozzon létre és mentsen még egy dokumentumot, amelyben egyedi értéket szúr be az `id` tulajdonság számára, és tetszés szerint módosítja a többi tulajdonságot. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.