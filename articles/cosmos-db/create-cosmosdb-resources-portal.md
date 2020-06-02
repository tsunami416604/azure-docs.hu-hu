---
title: Rövid útmutató – Azure Cosmos DB erőforrások létrehozása a Azure Portal
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Cosmos-adatbázist,-tárolót és-elemeket a Azure Portal használatával.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: 0f7e210c0818bd317f8e69ebed28301fd9b06dc0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263873"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Gyors útmutató: Azure Cosmos-fiók,-adatbázis,-tároló és-elemek létrehozása a Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A Azure Cosmos DB segítségével gyorsan létrehozhat és lekérdezheti a kulcs/érték adatbázisait, a dokumentum-adatbázisokat és a Graph-adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Azure Cosmos DB középpontjában. 

Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy Azure Cosmos DB [SQL API](sql-api-introduction.md) -fiók létrehozásához, egy dokumentum-adatbázis és-tároló létrehozásához, valamint az adattárolóhoz való hozzáadásához. 

## <a name="prerequisites"></a>Előfeltételek

Azure-előfizetés vagy ingyenes Azure Cosmos DB próbaverziós fiók
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Azure Cosmos DB-fiók létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com/) . Keresse meg és válassza az **Azure Cosmos DB** lehetőséget.

   ![Az Azure Portal Adatbázisok panelje](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Válassza a **Hozzáadás** lehetőséget.
1. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos-fiók alapszintű beállításait. 

    |Beállítás|Érték|Leírás |
    |---|---|---|
    |Előfizetés|Előfizetés neve|Válassza ki az Azure Cosmos-fiókhoz használni kívánt Azure-előfizetést. |
    |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget, majd adjon meg egy egyedi nevet az új erőforráscsoport számára. |
    |Account Name|Egyedi név|Adjon meg egy nevet az Azure Cosmos-fiók azonosításához. Mivel a rendszer hozzáfűzi a *Documents.Azure.com* az URI létrehozásához megadott névvel, adjon meg egy egyedi nevet.<br><br>A név csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Hosszának 3-31 karakter közöttinek kell lennie.|
    |API|A létrehozandó fiók típusa|Válassza a **Core (SQL)** lehetőséget egy dokumentum-adatbázis és-lekérdezés létrehozásához az SQL-szintaxis használatával. <br><br>A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: a Core (SQL) és a MongoDB, a Gremlin, az Azure Table és a Cassandra típusú dokumentumokat. Jelenleg külön fiókot kell létrehoznia minden egyes API-hoz. <br><br>[További információ az SQL API-ról](introduction.md).|
    |Ingyenes szintű árengedmény alkalmazása|Alkalmazás vagy Mellőzés|A Azure Cosmos DB ingyenes szinten az első 400 RU/s és 5 GB tárhely ingyenesen elérhető egy fiókban. További információ az [ingyenes szintjéről](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Hely|A felhasználókhoz legközelebb eső régió|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adataihoz.|
    |Fiók típusa|Éles üzem vagy nem éles üzem|Válassza a **gyártás** lehetőséget, ha a fiókot éles számítási feladatokhoz kívánja használni. Válassza a **nem üzemi** lehetőséget, ha a fiókot nem éles környezetben, például fejlesztési, tesztelési, QA vagy előkészítési célokra kívánja használni. Ez egy Azure-beli erőforrás-címkézési beállítás, amely a portál felületét, de nincs hatással az alapul szolgáló Azure Cosmos DB fiókra. Ezt az értéket bármikor megváltoztathatja.|
    |Georedundancia|Engedélyezés vagy Letiltás|Engedélyezheti vagy letilthatja a globális terjesztést a fiókjában, ha a régiót párosítja egy pár régióval. Később további régiókat is hozzáadhat a fiókhoz.|
    |Többrégiós írók|Engedélyezés vagy Letiltás|A többrégiós írások funkció lehetővé teszi, hogy kihasználja az adatbázisok és tárolók kiépített átviteli sebességét az egész világon.|
    |Rendelkezésre állási zónák|Engedélyezés vagy Letiltás|Availability Zones segít tovább javítani az alkalmazás rendelkezésre állását és rugalmasságát.|


> [!NOTE]
> Az Azure-előfizetések esetében akár egy ingyenes szintű Azure Cosmos DB fiókot is beállíthat, és a fiók létrehozásakor be kell jelentkeznie. Ha nem látja az ingyenes csomagra vonatkozó kedvezményt, ez azt jelenti, hogy az előfizetés egy másik fiókja már engedélyezve van az ingyenes szinten.
   
   ![Az Azure Cosmos DB új fiók lapja](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és a **címkék** szakaszt.

1. Tekintse át a Fiókbeállítások beállítást, majd kattintson a **Létrehozás**gombra. A fiók létrehozása néhány percet vesz igénybe. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. 

    ![Az Azure Portal Értesítések panelje](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a Azure Cosmos db fiók lapot. 

    ![A Azure Cosmos DB fiók lapja](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adatbázis és tároló hozzáadása 

Adatbázis és tároló létrehozásához használhatja a Azure Portal Adatkezelő is. 

1.  Válassza ki a **adatkezelő** elemet a Azure Cosmos db fiók lapjának bal oldali navigációs sávjában, majd válassza az **új tároló**elemet. 
    
    Előfordulhat, hogy a **tároló hozzáadása** ablak megjelenítéséhez jobbra kell görgetni.
    
    ![Az Azure Portal Adatkezelője a Tároló hozzáadása panellel](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  A **tároló hozzáadása** panelen adja meg az új tároló beállításait.
    
    |Beállítás|Ajánlott érték|Leírás
    |---|---|---|
    |**Adatbázis-azonosító**|Teendők|Adja meg a *ToDoList* nevet az új adatbázis neveként. Az adatbázis nevének 1 és 255 karakter közöttinek kell lennie, és nem tartalmazhat `/, \\, #, ?` szóközt. Tekintse meg az **adatbázis átviteli sebességének** kiosztása lehetőséget, amellyel megoszthatja az adatbázison belül kiosztott átviteli sebességet az adatbázis összes tárolóján. Ez a lehetőség a költségmegtakarítást is segíti. |
    |**Teljesítmény**|400|Az átviteli sebesség 400 adategység/másodperc (RU/s) esetén. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében.| 
    |**Tároló azonosítója**|Elemek|Adja meg az *elemeket* az új tároló neveként. A tárolóazonosítók nevére ugyanazok a karakterkorlátozások vonatkoznak, mint az adatbázisnevekre.|
    |**Partíciókulcs**| /kategória| A cikkben ismertetett minta a */category* használja a partíciós kulcsként.|

    
    Ne adjon **egyedi kulcsokat** ehhez a példához. Az egyedi kulcsok lehetővé teszik adatintegritási réteg hozzáadását az adatbázishoz, ha a partíciós kulcs egy vagy több értékének egyediségét biztosítja. További információ: [Azure Cosmos DBban található egyedi kulcsok](unique-keys.md).
    
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
   
1. Válassza újra az **új dokumentumot** , és hozzon létre és mentsen egy másik `id` , egyedi, valamint a kívánt tulajdonságokkal és értékekkel rendelkező dokumentumot. A dokumentumok bármilyen struktúrával rendelkezhetnek, mivel Azure Cosmos DB nem ró sémát az adataira.

## <a name="query-your-data"></a>Adatok lekérdezése

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Ha csak az adatbázist szeretné törölni, és a jövőben az Azure Cosmos-fiókot kívánja használni, akkor az alábbi lépésekkel törölheti az adatbázist:

* Megkapta az Azure Cosmos-fiókját.
* Nyissa meg **adatkezelő**, kattintson a jobb gombbal a törölni kívánt adatbázisra, majd válassza az **adatbázis törlése**lehetőséget.
* Adja meg az adatbázis-azonosító/adatbázis nevét a törlési művelet megerősítéséhez. 

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB fiókot, hogyan hozhat létre adatbázist és tárolót a Adatkezelő használatával. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
