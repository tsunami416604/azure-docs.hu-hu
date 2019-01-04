---
title: A Flask-webalkalmazás létrehozása Azure Cosmos DB API a mongodb-hez és a Python SDK használatával
description: Egy használatával csatlakoznak és kérnek az Azure Cosmos DB API használatával a mongodb-hez készült Python Flask kódmintát mutat be.
services: cosmos-db
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 78fa871deb36b9f3596632976ce5a17b2f4d71fa
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792249"
---
# <a name="build-a-flask-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Készíthet olyan Flask-alkalmazás használatával az Azure Cosmos DB API a mongodb-hez

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyors létrehozása és lekérdezése, a dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike globális elosztási és horizontális skálázhatósági képességeket Cosmos DB középpontjában.

Ez a rövid útmutató használja a következő [Flask példát](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) és bemutatja, hogyan hozhat létre egy egyszerű teendőlista Flask alkalmazást a [Azure Cosmos DB Emulatort](local-emulator.md) és az Azure Cosmos DB API a mongodb-hez.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure Cosmos DB Emulator](local-emulator.md) letöltése. Az Emulator jelenleg csak a Windows rendszerben támogatott. A minta bemutatja, hogyan használhatja a mintát az Azure-tól kapott termékkulccsal, ami bármilyen platformon elvégezhető.

- Ha még nincs telepítve a Visual Studio Code, gyorsan telepítheti a [VS Code-ot](https://code.visualstudio.com/Download) a platformján (Windows, Mac, Linux).

- Az egyik népszerű Python bővítmény telepítésével adja hozzá a Python nyelv támogatását.
    1. Válassza ki a bővítményt.
    2. Telepítse a bővítményt a `Ctrl+Shift+P` parancskatalógusban az `ext install` parancs beírásával.

    A dokumentumban szereplő példák Don Jayamanne népszerű és minden funkcióval ellátott [Python bővítményét](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) használja.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Flask-MongoDB alkalmazást a Githubról, beállítjuk a kapcsolati karakterláncot, és futtassa azt. Ilyen egyszerű az adatokkal programozott módon dolgozni.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű új mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a minta tárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

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

Az alábbi kódrészletek mind az app.py fájlból származnak, és a helyi Azure Cosmos DB Emulator kapcsolati sztringjét használják. A jelszót az alább látható módon kell felosztani a máshogy nem elemezhető perjelek befogadásához.

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

3. Ezután mentse a Flask alkalmazás környezeti változóját a `set FLASK_APP=app.py` vagy az `export FLASK_APP=app.py` paranccsal, ha Macet használ.

4. Futtassa az alkalmazást a `flask run` paranccsal és lépjen a [http://127.0.0.1:5000/](http://127.0.0.1:5000/) címre.

5. Adjon hozzá és távolítson el feladatokat, és figyelje meg, ahogyan változnak a gyűjteményben.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Ha szeretné tesztelni a kódot egy élő Cosmos-fiók, nyissa meg egy fiók létrehozásához, és a kapcsolati sztringek adatainak lekérése az Azure Portalon. Ezután másolja azokat az alkalmazásba.

1. Az a [az Azure portal](https://portal.azure.com/), a Cosmos-fiókban a bal oldalsávon kattintson **kapcsolati karakterlánc**, és kattintson a **írható és olvasható kulcsok**. A következő lépésben használja a képernyő jobb oldalán lévő másolási gombokat a felhasználónév, a jelszó és a gazdagép másolásához a Dal.cs fájlba.

2. Nyissa meg a gyökérkönyvtárban lévő **app.py** fájlt.

3. A másolási gomb használatával másolja ki a **username** érteket a Portalból, és azt adja meg a **name** értékeként az **app.py** fájlban.

4. Ezután másolja ki a **kapcsolati sztring** értékét a Portalból, és azt adja meg a MongoClient értékeként az **app.py** fájlban.

5. Végül másolja ki a **jelszó** értékét a Portalból, és azt adja meg a **password** értékeként az **app.py** fájlban.

Az alkalmazás frissítése megtörtént, és a Cosmos DB közötti kommunikációhoz szükséges összes információval. Ugyanúgy futtathatja, mint előtte.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Az alkalmazás üzembe helyezéséhez, hozzon létre egy új webalkalmazást az Azure-ban, és a GitHub-adattár elágazásával végzett folyamatos üzembe helyezés engedélyezése. Kövesse ezt [oktatóanyag](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) folyamatos üzembe helyezés a Githubbal, az Azure-ban való beállításához.

Amikor az Azure-ba végez üzembe helyezést, el kell távolítania az alkalmazáskulcsokat, és ellenőriznie kell, hogy az alábbi szakasz ne megjegyzésként szerepeljen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Ezután hozzá kell adnia a MONGOURL, MONGO_PASSWORD és MONGO_USERNAME elemeket az alkalmazásbeállításokhoz. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) követve további információkat tudhat meg az Azure-webalkalmazások alkalmazásbeállításairól.

Ha nem szeretne létrehozni elágazást ehhez az adattárhoz, az alul található Üzembe helyezés az Azure-ban gombra is kattinthat. Ezután nyissa meg az Azure-t, és állítsa be az alkalmazásbeállításokat a Cosmos DB-fiókadatokkal.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Ha szeretné tárolni a kódot a Githubon vagy más Forrásvezérlő,. mindenképpen távolítsa el a kapcsolati karakterláncokat a kódból. Ehelyett a webalkalmazás alkalmazásbeállításaival állíthatók be.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk hogyan hozzon létre egy Cosmos-fiókot, és a Flask alkalmazást futtatni. További adatok már importálhat a Cosmos database. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
