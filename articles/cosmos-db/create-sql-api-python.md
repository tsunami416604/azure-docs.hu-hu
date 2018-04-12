---
title: 'Azure Cosmos DB: Alkalmazás létrehozása a Python és az SQL API használatával | Microsoft Docs'
description: Egy Python-kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB SQL API-hoz, és lekérdezést lehet végezni vele
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: 5b9206a7bbd0fe5afcb2c65f2270fc67dffee4e3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-build-a-sql-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB: SQL API-alkalmazás létrehozása a Python és az Azure Portal használatával

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB [SQL API](sql-api-introduction.md)-fiókot, dokumentum-adatbázist és gyűjteményt. Ezután megtudhatja, hogyan hozhat létre és futtathat egy, az [SQL Python API](sql-api-sdk-python.md)-val létrehozott konzolalkalmazást.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Továbbá:
    * Ha nincs telepítve a Visual Studio 2017, letöltheti és használhatja az **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)t. Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
    * Python Tools for Visual Studio, amely beszerezhető a [GitHubról](http://microsoft.github.io/PTVS/). Ez az oktatóanyag a Python Tools VS 2015-ös verziót használja.
    * A [python.org](https://www.python.org/downloads/release/python-2712/) webhelyen elérhető Python 2.7-es verzió

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy SQL API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, és futtatjuk az alkalmazást. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a minta tárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a DocumentDBGetStarted.py fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB-erőforrásokat. 


* A DocumentClient inicializálva van.

    ```python
    # Initialize the Python client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* A rendszer létrehozza az új adatbázist.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['SQL_DATABASE'] })
    ```

* A rendszer létrehozza az új gyűjteményt.

    ```python
    # Create collection options
    options = {
        'offerEnableRUPerMinuteThroughput': True,
        'offerVersion': "V2",
        'offerThroughput': 400
    }

    # Create a collection
    collection = client.CreateCollection(db['_self'], { 'id': config['SQL_COLLECTION'] }, options)
    ```

* A rendszer létrehoz néhány dokumentumot.

    ```python
    # Create some documents
    document1 = client.CreateDocument(collection['_self'],
        { 
            'id': 'server1',
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': 'some' 
        })
    ```

* A rendszer végrehajt egy lekérdezést az SQL használatával

    ```python
    # Query them in SQL
    query = { 'query': 'SELECT * FROM server s' }    
            
    options = {} 
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryDocuments(collection['_self'], query, options)
    results = list(result_iterable);

    print(results)
    ```

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **kulcsok** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot a `DocumentDBGetStarted.py` fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-sql-api-dotnet/keys.png)

2. Nyissa meg a `DocumentDBGetStarted.py` fájlt. 

3. A másolási gomb használatával másolja ki az URI érteket a Portalról, és azt adja meg a végpont kulcs értékeként a `DocumentDBGetStarted.py`-ben. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Ezután másolja ki az ELSŐDLEGES KULCS értékét a Portalról, és adja meg a `config.MASTERKEY` értékeként a `DocumentDBGetStarted.py`-ben. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `'MASTERKEY': 'FILLME',`
    
## <a name="run-the-app"></a>Az alkalmazás futtatása
1. A Visual Studióban kattintson jobb gombbal a projekt lehetőségre a **Megoldáskezelőben**, válassza ki a jelenlegi Python-környezetet, majd kattintson a jobb gombbal.

2. Válassza ki a Python-csomag telepítése lehetőséget, majd írja be a következőt: **pydocumentdb**

3. Az alkalmazás futtatásához nyomja le az F5 billentyűt. Az alkalmazás megjelenik a böngészőben. 

Lépjen vissza az Adatkezelőbe, ahol lekérdezheti és módosíthatja az új adatokat, valamint dolgozhat azokkal. 

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gyűjteményt készíteni, és hogyan lehet futtatni az alkalmazást. Így már további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be az SQL API-hoz](import-data.md)


