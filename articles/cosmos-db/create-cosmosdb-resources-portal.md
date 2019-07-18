---
title: Hozzon létre egy Azure Cosmos-fiókot,-tárolót és-elemeket a Azure Portal.
description: Hozzon létre egy Azure Cosmos-fiókot,-tárolót és-elemeket a Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 41670b74cdc6205ed7e02431dd10de8bea2d2858
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001201"
---
# <a name="quickstart-create-an-azure-cosmos-account-container-and-items-with-the-azure-portal"></a>Gyors útmutató: Hozzon létre egy Azure Cosmos-fiókot,-tárolót és-elemeket a Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. A Azure Cosmos DB segítségével gyorsan létrehozhat és lekérdezheti a kulcs/érték adatbázisait, a dokumentum-adatbázisokat és a Graph-adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Azure Cosmos DB középpontjában. 

Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy Azure Cosmos DB [SQL API](sql-api-introduction.md) -fiók létrehozásához, egy dokumentum-adatbázis és-gyűjtemény létrehozásához, valamint az adatgyűjtés hozzáadásához. 

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés vagy ingyenes Azure Cosmos DB próbaverziós fiók
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Adatbázis és gyűjtemény hozzáadása 

Adatbázis és gyűjtemény létrehozásához használhatja a Azure Portal Adatkezelő is. 

1.  Válassza ki a **adatkezelő** elemet a Azure Cosmos db fiók lapjának bal oldali navigációs sávjában, majd válassza az **új tároló**elemet. 
    
    Előfordulhat, hogy a **tároló hozzáadása** ablak megjelenítéséhez jobbra kell görgetni.
    
    ![Az Azure portal adatkezelő, gyűjtemény hozzáadása panel](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  A **tároló hozzáadása** panelen adja meg az új gyűjtemény beállításait.
    
    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Teendők|Adja meg a *ToDoList* nevet az új adatbázis neveként. Adatbázis neve 1 és 255 karakter között kell tartalmaznia, és nem tartalmazhat `/, \\, #, ?`, vagy záró szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Átviteli sebesség**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Az új gyűjteménynek adja az *Elemek* nevet. A gyűjtemény-azonosítók ugyanazokkal a karakterekkel rendelkeznek, mint az adatbázis neve.|
    |**Partíciós kulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként.|

    
    Ne adjon **egyedi kulcsokat** ehhez a példához. Az egyedi kulcsok lehetővé teszik adatintegritási réteg hozzáadását az adatbázishoz, ha a partíciós kulcs egy vagy több értékének egyediségét biztosítja. További információ: Azure Cosmos DBban található [egyedi kulcsok](unique-keys.md).
    
1.  Kattintson az **OK** gombra. A Adatkezelő megjeleníti az új adatbázist és a létrehozott tárolót.

## <a name="add-data-to-your-database"></a>Adatbevitel az adatbázisba

Adja hozzá az új adatbázis adatait az Adatkezelő használatával.

1. A **adatkezelő**bontsa ki a **ToDoList** -adatbázist, és bontsa ki az **elemek** tárolót. Ezután válassza az **elemek elemet**, majd válassza az **új elem elemet**. 
   
   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Adja hozzá a következő struktúrát a dokumentumhoz a **dokumentumok** ablaktábla jobb oldalán:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Kattintson a **Mentés** gombra.
   
   ![Másolja a JSON-adatfájlt, és válassza a Mentés Adatkezelő a Azure Portal](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Válassza újra az **új dokumentumot** , és hozzon létre és mentsen egy másik `id`, egyedi, valamint a kívánt tulajdonságokkal és értékekkel rendelkező dokumentumot. A dokumentumok bármilyen struktúrával rendelkezhetnek, mivel Azure Cosmos DB nem ró sémát az adataira.

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre adatbázist és tárolót a Adatkezelő használatával. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)