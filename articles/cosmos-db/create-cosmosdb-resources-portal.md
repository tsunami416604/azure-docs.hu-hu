---
title: Rövid útmutató – Azure Cosmos DB-erőforrások létrehozása az Azure Portalról
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Cosmos-adatbázist, -tárolót és -elemeket az Azure Portal használatával.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: bc7e77cc498958b2f8f0c5b2d5ab2d59db97a235
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240407"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure Cosmos-fiókot, adatbázist, tárolót és elemeket az Azure Portalról

> [!div class="op_single_selector"]
> * [Azure-portál](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB segítségével gyorsan létrehozhat és lekérdezhet kulcs-érték adatbázisokat, dokumentum-adatbázisokat és gráf-adatbázisokat, amelyek mindegyike kihasználja az Azure Cosmos DB magjában található globális terjesztési és horizontális méretezési képességek előnyeit. 

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Portalon egy Azure Cosmos DB [SQL API-fiók](sql-api-introduction.md) létrehozásához, hozzon létre egy dokumentum-adatbázist és -tárolót, és adjon hozzá adatokat a tárolóhoz. 

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés vagy ingyenes Azure Cosmos DB próbafiók
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adatbázis és tároló hozzáadása 

Az Azure Portalon az Adatkezelő segítségével létrehozhat egy adatbázist és egy tárolót. 

1.  Válassza a **Data Explorer elemet** az Azure Cosmos DB-fióklapján a bal oldali navigációs sávon, majd válassza az Új **tároló**lehetőséget. 
    
    Előfordulhat, hogy jobbra kell görgetnie a **Tároló hozzáadása** ablak megtekintéséhez.
    
    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  A **Tároló hozzáadása** ablaktáblán adja meg az új tároló beállításait.
    
    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis azonosítója**|Teendők|Írja be a *ToDoList* nevet az új adatbázis neveként. Az adatbázisneveknek 1 és 255 karakter `/, \\, #, ?`között kell lennie, és nem tartalmazhatnak , illetve záró szóközt. Ellenőrizze a **létesítési adatbázis átviteli beállítás,** lehetővé teszi, hogy megosszák az adatbázis számára kiosztott átviteli átmenő az adatbázis összes tárolója között. Ez az opció is segít a költségmegtakarítást. |
    |**Teljesítmény**|400|Hagyja az átviteli értéket 400 kérelemegység/másodperc (RU/s) értéken. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Írja be *az elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* kapcsolót használja partíciókulcsként.|

    
    Ehhez a példához ne adjon hozzá **egyedi kulcsokat.** Az egyedi kulcsok lehetővé teszik, hogy egy adatintegritási réteget adjon az adatbázishoz azáltal, hogy biztosítja egy vagy több érték egyediségét partíciókulcsonként. További információ: [Unique keys in Azure Cosmos DB.](unique-keys.md)
    
1.  Válassza **az OK gombot.** Az Adatkezelő megjeleníti az új adatbázist és a létrehozott tárolót.

## <a name="add-data-to-your-database"></a>Adatok hozzáadása az adatbázishoz

Adatok hozzáadása az új adatbázishoz az Adatkezelő segítségével.

1. Az **Adatkezelőben**bontsa ki a **ToDoList** adatbázist, és bontsa ki az **Elemek** tárolót. Ezután válassza **az Elemek**lehetőséget, majd az **Új elem**lehetőséget. 
   
   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Adja hozzá a következő struktúrát a **dokumentumokhoz** a Dokumentumok ablaktábla jobb oldalán:

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
   
   ![Másolás jsonadatokközött, és az Azure Portalon válassza a Mentés adatkezelőben lehetőséget](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Ismét válassza **az Új dokumentum lehetőséget,** majd `id`hozzon létre és mentsen egy másik dokumentumot egyedi , valamint bármely más tulajdonsággal és értékkel. A dokumentumok bármilyen struktúrával rendelkezhetnek, mivel az Azure Cosmos DB nem ír elő sémát az adatokra.

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Ha csak az adatbázist szeretné törölni, és a jövőben az Azure Cosmos-fiókot szeretné használni, a következő lépésekkel törölheti az adatbázist:

* Az Azure Cosmos-fiókhoz.
* Nyissa meg **az Adatkezelőt**, kattintson a jobb gombbal a törölni kívánt adatbázisra, és válassza **az Adatbázis törlése parancsot.**
* A törlési művelet megerősítéséhez adja meg az adatbázis-azonosító/adatbázis nevét. 

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot, hozzon létre egy adatbázist és egy tárolót az Adatkezelő használatával. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
