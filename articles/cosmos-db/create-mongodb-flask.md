---
title: Python Flask webalkalmazás létrehozása az Azure Cosmos DB MongoDB-hoz való API-jával
description: Bemutatja a Python Flask-kód minta segítségével csatlakozhat, és lekérdezésaz Azure Cosmos DB API-mongoDB használatával.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 43f4cf7e4008aa01a26c48a8e99f7465eeeb234b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77061743"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Rövid útmutató: Python-alkalmazás létrehozása az Azure Cosmos DB MongoDB-hoz való API-jával

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Ebben a rövid útmutatóban egy Azure Cosmos DB Mongo DB API-fiókhoz vagy az Azure Cosmos DB-emulátort használ a GitHubról klónozott Python Flask To-Do webalkalmazás futtatásához. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül. Vagy használhatja az [Azure Cosmos DB-emulátort.](local-emulator.md) 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio-kód](https://code.visualstudio.com/Download) a [Python kiterjesztéssel.](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python)

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most klónozzunk egy Flask-MongoDB alkalmazást a GitHubról, állítsuk be a kapcsolati karakterláncot, és futtassuk. Ilyen egyszerű az adatokkal programozott módon dolgozni.

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

Az alábbi kódrészletek mind a *app.py* fájlból származnak, és a helyi Azure Cosmos DB-emulátor kapcsolati karakterláncát használja. A jelszót az alább látható módon kell felosztani a máshogy nem elemezhető perjelek befogadásához.

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

3. Ezután állítsa be a Flask `set FLASK_APP=app.py` `$env:FLASK_APP = app.py` alkalmazás környezeti változóját `export FLASK_APP=app.py` a , PowerShell-szerkesztők höz, vagy ha Mac et használ. 

4. Futtassa `flask run` az alkalmazást, és keresse meg *a\/http: /127.0.0.1:5000/*.

5. Adjon hozzá és távolítson el feladatokat, és figyelje meg, ahogyan változnak a gyűjteményben.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

Ha egy élő Azure Cosmos DB-fiókkal szeretné tesztelni a kódot, az Azure Portalon létrehozhat egy fiókot.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

A kód az élő Azure Cosmos DB-fiókkal szemben, a kapcsolati karakterlánc adatait. Ezután másolja azokat az alkalmazásba.

1. Az Azure Cosmos DB-fiókjában az Azure Portalon a bal oldali navigációs sávon válassza a **Kapcsolati karakterlánc**lehetőséget, majd válassza **az Olvasási és írási kulcsok lehetőséget.** A képernyő jobb oldalán található másolási gombokkal másolhatja a felhasználónevet, a kapcsolati karakterláncot és a jelszót. 

2. Nyissa meg a gyökérkönyvtárban lévő *app.py* fájlt.

3. A másolási gomb használatával másolja ki a **username** érteket a Portalból, és azt adja meg a **name** értékeként az *app.py* fájlban.

4. Ezután másolja a **kapcsolati karakterlánc** értékét a portálról, és tegye a **mongoclient** értékére a *app.py* fájlban.

5. Végül másolja ki a **jelszó** értékét a Portalból, és azt adja meg a **password** értékeként az *app.py* fájlban.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. Ugyanúgy futtathatja, mint előtte.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Az alkalmazás üzembe helyezéséhez létrehozhat egy új webalkalmazást az Azure-ban, és engedélyezheti a folyamatos üzembe helyezést a GitHub-tárcsa elírásával. Az [oktatóanyagból](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) folyamatos üzembe helyezést állíthat be a GitHubon az Azure-ban.

Amikor az Azure-ba végez üzembe helyezést, el kell távolítania az alkalmazáskulcsokat, és ellenőriznie kell, hogy az alábbi szakasz ne megjegyzésként szerepeljen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Ezután hozzá kell adnia a MONGOURL, MONGO_PASSWORD és MONGO_USERNAME elemeket az alkalmazásbeállításokhoz. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) követve további információkat tudhat meg az Azure-webalkalmazások alkalmazásbeállításairól.

Ha nem szeretne villát létrehozni ebből a tárhétből, az alábbi **Üzembe helyezés az Azure-ba** gombot is választhatja. Ezután az Azure-ba kell lépnie, és be kell állítania az alkalmazásbeállításokat az Azure Cosmos DB-fiók adataival.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Ha azt tervezi, hogy tárolja a kódot a GitHubon vagy más forrásvezérlési beállításokban, kérjük, távolítsa el a kapcsolati karakterláncokat a kódból. Ehelyett a webalkalmazás alkalmazásbeállításaival állíthatók be.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-t a Mongo DB API-fiókhoz, és az Azure Cosmos DB-emulátor használatával futtathat egy GitHubról klónozott Python Flask To-Do webalkalmazást. Így már további adatokat importálhat az Azure Cosmos DB-fiókba. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
