---
title: "Azure Cosmos DB: Alkalmazás létrehozása Pythonnal és DocumentDB API-val | Microsoft Docs"
description: "Egy Python-kódmintát mutat be, amellyel csatlakozhat az Azure Cosmos DB DocumentDB API-hoz és lekérdezheti azt"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/13/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 08d467ea27484e7d1d07d6c21b2e04b6525fbcd8
ms.contentlocale: hu-hu
ms.lasthandoff: 06/01/2017


---
<a id="azure-cosmos-db-build-a-documentdb-api-app-with-python-and-the-azure-portal" class="xliff"></a>

# Azure Cosmos DB: DocumentDB API-alkalmazás létrehozása Pythonnal és az Azure Portallal

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

A rövid útmutató bemutatja, hogyan hozhat létre az Azure Portal segítségével Azure Cosmos DB-fiókot, dokumentum-adatbázist, és gyűjteményt. Ezután létrehozhatja és futtathatja a [DocumentDB Python API-ra](documentdb-sdk-python.md) épülő konzolalkalmazást.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

* Mielőtt futtathatná ezt a mintát, rendelkeznie kell a következő előfeltételekkel:
    * [Visual Studio 2015](http://www.visualstudio.com/) vagy újabb.
    * Python Tools for Visual Studio, amely beszerezhető a [GitHubról](http://microsoft.github.io/PTVS/). Ez az oktatóanyag a Python Tools VS 2015-ös verziót használja.
    * A [python.org](https://www.python.org/downloads/release/python-2712/) webhelyen elérhető Python 2.7-es verzió

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-a-database-account" class="xliff"></a>

## Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="add-a-collection" class="xliff"></a>

## Gyűjtemény hozzáadása

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="clone-the-sample-application" class="xliff"></a>

## A mintaalkalmazás klónozása

Most pedig klónozunk egy DocumentDB API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, és futtatni is fogjuk. Ilyen egyszerű az adatokkal programozott módon dolgozni. 

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.  

2. Futtassa a következő parancsot a minta tárház klónozásához. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
<a id="review-the-code" class="xliff"></a>

## A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a DocumentDBGetStarted.py fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB-erőforrásokat. 


* A DocumentClient inicializálva van.

    ```python
    # Initialize the Python DocumentDB client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* A rendszer létrehozza az új adatbázist.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DOCUMENTDB_DATABASE'] })
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
    collection = client.CreateCollection(db['_self'], { 'id': config['DOCUMENTDB_COLLECTION'] }, options)
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

<a id="update-your-connection-string" class="xliff"></a>

## A kapcsolati karakterlánc frissítése

Lépjen vissza az Azure Portalra a kapcsolati karakterlánc adataiért, majd másolja be azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **kulcsok** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépésben a képernyő jobb oldalán lévő másolási gombokkal másolhatja az URI-t és az elsődleges kulcsot a `DocumentDBGetStarted.py` fájlba.

    ![Hozzáférési kulcs megtekintése és másolása az Azure Portal kulcsok paneljén](./media/create-documentdb-dotnet/keys.png)

2. Nyissa meg a `DocumentDBGetStarted.py` fájlt. 

3. A másolási gomb használatával másolja ki az URI érteket a Portalról, és azt adja meg a végpont kulcs értékeként a `DocumentDBGetStarted.py`-ben. 

    `config.ENDPOINT : "https://FILLME.documents.azure.com"`

4. Ezután másolja ki az ELSŐDLEGES KULCS értékét a Portalról, és adja meg a `config.MASTERKEY` értékeként a `DocumentDBGetStarted.py`-ben. Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

    `config.MASTERKEY : "FILLME"`
    
<a id="run-the-app" class="xliff"></a>

## Az alkalmazás futtatása
1. A Visual Studióban kattintson jobb gombbal a projekt lehetőségre a **Megoldáskezelőben**, válassza ki a jelenlegi Python-környezetet, majd kattintson a jobb gombbal.

2. Válassza ki a Python-csomag telepítése lehetőséget, majd írja be a következőt: **pydocumentdb**

3. Az alkalmazás futtatásához nyomja le az F5 billentyűt. Az alkalmazás megjelenik a böngészőben. 

Lépjen vissza az Adatkezelőbe, ahol lekérdezheti, módosíthatja, és dolgozhat az új adattal. 

<a id="review-slas-in-the-azure-portal" class="xliff"></a>

## Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

<a id="clean-up-resources" class="xliff"></a>

## Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

<a id="next-steps" class="xliff"></a>

## Következő lépések

Ebben a rövid útmutatóban bemutattuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, hogyan lehet az Adatkezelő segítségével gyűjteményt készíteni, és hogyan lehet futtatni az alkalmazást. Most további adatokat importálhat a Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be a DocumentDB API-hoz](import-data.md)



