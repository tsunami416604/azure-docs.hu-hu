---
title: Python-lombik Webalkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val
description: Egy Python-beli mintakód-mintát tartalmaz, amellyel csatlakozhat a Azure Cosmos DB API-MongoDB való kapcsolódáshoz és lekérdezésekhez.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 53d1010f501e59b99c3ba6bac675124cdc5f77a0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659215"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Gyors útmutató: Python-alkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DBt használ a Mongo DB API-fiókhoz vagy a Azure Cosmos DB emulátorhoz, amellyel a GitHubról klónozott Python-lombikot futtathat webalkalmazásként. A Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, tábla, kulcs-érték és gráf adatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Vagy [próbálja ki Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) ingyen Azure-előfizetés nélkül. Vagy használhatja a [Azure Cosmos db emulatort](local-emulator.md)is. 
- [Python 3.6 +](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) a [Python bővítménnyel](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most hozzon létre egy MongoDB alkalmazást a GitHubról, állítsa be a kapcsolatok karakterláncát, és futtassa. Ilyen egyszerű az adatokkal programozott módon dolgozni.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Futtassa az alábbi parancsot a python modulok telepítéséhez.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Nyissa meg a mappát a Visual Studio Code-ban.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben áttérhet [A webalkalmazás futtatása](#run-the-web-app) című szakaszra. 

A következő kódrészletek mind a *app.py* fájlból származnak, és a helyi Azure Cosmos db emulátorhoz tartozó kapcsolatok karakterláncot használják. A jelszót az alább látható módon kell felosztani a máshogy nem elemezhető perjelek befogadásához.

* Inicializálja a MongoDB-ügyfelet, kérje le az adatbázist, és hitelesítsen.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Ha még nem létezik, kérje le a gyűjteményt, vagy hozza létre azt.

    ```python
    todos = db.todo #Select the collection
    ```

* Az alkalmazás létrehozása

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Győződjön meg arról, hogy az Azure Cosmos DB Emulator fut.

2. Nyisson meg egy terminálablakot és a `cd` paranccsal lépjen arra a könyvtárra, amelyben az alkalmazás mentve van.

3. Ezután állítsa be a környezeti változót a `set FLASK_APP=app.py` , PowerShell- `$env:FLASK_APP = app.py` szerkesztőkhöz, vagy Mac-et használó lombik alkalmazáshoz `export FLASK_APP=app.py` . 

4. Futtassa az alkalmazást a következővel, `flask run` és keresse meg a *http: \/ /127.0.0.1:5000/*.

5. Adjon hozzá és távolítson el feladatokat, és figyelje meg, ahogyan változnak a gyűjteményben.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Ha a kódot egy élő Azure Cosmos DB-fiókkal szeretné tesztelni, a fiók létrehozásához nyissa meg a Azure Portal.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Ha tesztelni szeretné a kódot az élő Azure Cosmos DB fiókkal, szerezze be a kapcsolódási karakterlánc adatait. Ezután másolja azokat az alkalmazásba.

1. A Azure Portal Azure Cosmos DB-fiókjában a bal oldali navigációs sávon válassza a **kapcsolatok karakterlánc**lehetőséget, majd válassza az **írási/olvasási kulcsok**elemet. A Felhasználónév, a kapcsolódási karakterlánc és a jelszó másolásához a képernyő jobb oldalán található másolási gombokat fogja használni. 

2. Nyissa meg a gyökérkönyvtárban lévő *app.py* fájlt.

3. A másolási gomb használatával másolja ki a **username** érteket a Portalból, és azt adja meg a **name** értékeként az *app.py* fájlban.

4. Ezután másolja a **kapcsolatok karakterlánc** értékét a portálról, és adja meg a **MongoClient** értékét a *app.py* -fájlban.

5. Végül másolja ki a **jelszó** értékét a Portalból, és azt adja meg a **password** értékeként az *app.py* fájlban.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. Ugyanúgy futtathatja, mint előtte.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Az alkalmazás üzembe helyezéséhez létrehozhat egy új webalkalmazást az Azure-ban, és engedélyezheti a folyamatos üzembe helyezést a GitHub-tárház villájának használatával. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) követve folyamatos üzembe helyezést állíthat be a GitHub használatával az Azure-ban.

Amikor az Azure-ba végez üzembe helyezést, el kell távolítania az alkalmazáskulcsokat, és ellenőriznie kell, hogy az alábbi szakasz ne megjegyzésként szerepeljen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Ezután hozzá kell adnia a MONGOURL, MONGO_PASSWORD és MONGO_USERNAME elemeket az alkalmazásbeállításokhoz. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) követve további információkat tudhat meg az Azure-webalkalmazások alkalmazásbeállításairól.

Ha nem szeretne elágazást létrehozni ebből a tárházból, válassza az alábbi **üzembe helyezés az Azure-** ban gombot. Ezután be kell lépnie az Azure-ba, és be kell állítania az Alkalmazásbeállítások beállításait a Azure Cosmos DB fiókjának adataival.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Ha a kódot a GitHubon vagy más verziókövetés-beállításokban szeretné tárolni, ne felejtse el törölni a kapcsolódási karakterláncokat a kódból. Ehelyett a webalkalmazás alkalmazásbeállításaival állíthatók be.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre Azure Cosmos DB a Mongo DB API-fiókhoz, és hogyan futtathat egy Python-lombikot Azure Cosmos DB a GitHubról klónozott webalkalmazáshoz. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
