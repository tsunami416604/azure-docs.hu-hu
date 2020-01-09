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
ms.openlocfilehash: 8e58d0bdaaa5e4fb4564a68b46de7887ec28336d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445492"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Gyors útmutató: Python-alkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérdezheti a dokumentum-, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Cosmos DB középpontjában.

Ez a rövid útmutató az alábbi [példában](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) látható, és bemutatja, hogyan hozhat létre egy egyszerű teendőt a [Azure Cosmos db emulátorral](local-emulator.md) és a Azure Cosmos db API-MongoDB.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure Cosmos DB Emulator](local-emulator.md) letöltése. Az Emulator jelenleg csak a Windows rendszerben támogatott. A minta bemutatja, hogyan használhatja a mintát az Azure-tól kapott termékkulccsal, ami bármilyen platformon elvégezhető.

- Ha még nincs telepítve a Visual Studio Code, gyorsan telepítheti a [VS Code-ot](https://code.visualstudio.com/Download) a platformján (Windows, Mac, Linux).

- Az egyik népszerű Python bővítmény telepítésével adja hozzá a Python nyelv támogatását.
  1. Válassza ki a bővítményt.
  2. Telepítse a bővítményt a `Ctrl+Shift+P` parancskatalógusban az `ext install` parancs beírásával.

     A dokumentumban szereplő példák Don Jayamanne népszerű és minden funkcióval ellátott [Python bővítményét](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) használja.

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

3. Ezután állítsa be a környezeti változót a lombik alkalmazáshoz `set FLASK_APP=app.py`, `$env:FLASK_APP = app.py` a PowerShell-szerkesztőkhöz, vagy ha Mac-et használ, `export FLASK_APP=app.py`. 

4. Futtassa az alkalmazást a `flask run` paranccsal és lépjen a [http://127.0.0.1:5000/](http://127.0.0.1:5000/) címre.

5. Adjon hozzá és távolítson el feladatokat, és figyelje meg, ahogyan változnak a gyűjteményben.

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Ha a kódot egy élő Cosmos-fiókkal szeretné tesztelni, lépjen a Azure Portal egy fiók létrehozásához és a kapcsolódási karakterlánc adatainak lekérdezéséhez. Ezután másolja azokat az alkalmazásba.

1. A [Azure Portal](https://portal.azure.com/)a Cosmos-fiókban a bal oldali navigációs sávon kattintson a **kapcsolatok karakterlánca**elemre, majd kattintson az **írási/olvasási kulcsok**elemre. A következő lépésben használja a képernyő jobb oldalán lévő másolási gombokat a felhasználónév, a jelszó és a gazdagép másolásához a Dal.cs fájlba.

2. Nyissa meg a gyökérkönyvtárban lévő **app.py** fájlt.

3. A másolási gomb használatával másolja ki a **username** érteket a Portalból, és azt adja meg a **name** értékeként az **app.py** fájlban.

4. Ezután másolja ki a **kapcsolati sztring** értékét a Portalból, és azt adja meg a MongoClient értékeként az **app.py** fájlban.

5. Végül másolja ki a **jelszó** értékét a Portalból, és azt adja meg a **password** értékeként az **app.py** fájlban.

Ezzel frissítette az alkalmazást az Cosmos DBsal való kommunikációhoz szükséges összes információval. Ugyanúgy futtathatja, mint előtte.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Az alkalmazás üzembe helyezéséhez létrehozhat egy új webalkalmazást az Azure-ban, és engedélyezheti a folyamatos üzembe helyezést a GitHub-tárház villájának használatával. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment) követve folyamatos üzembe helyezést állíthat be a GitHub használatával az Azure-ban.

Amikor az Azure-ba végez üzembe helyezést, el kell távolítania az alkalmazáskulcsokat, és ellenőriznie kell, hogy az alábbi szakasz ne megjegyzésként szerepeljen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Ezután hozzá kell adnia a MONGOURL, MONGO_PASSWORD és MONGO_USERNAME elemeket az alkalmazásbeállításokhoz. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) követve további információkat tudhat meg az Azure-webalkalmazások alkalmazásbeállításairól.

Ha nem szeretne létrehozni elágazást ehhez az adattárhoz, az alul található Üzembe helyezés az Azure-ban gombra is kattinthat. Ezután nyissa meg az Azure-t, és állítsa be az alkalmazásbeállításokat a Cosmos DB-fiókadatokkal.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Ha a kódot a GitHubon vagy más verziókövetés-beállításokban szeretné tárolni, ne felejtse el törölni a kapcsolódási karakterláncokat a kódból. Ehelyett a webalkalmazás alkalmazásbeállításaival állíthatók be.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Cosmos-fiókot, és hogyan futtathat egy lombik-alkalmazást. Mostantól további adatait is importálhatja a Cosmos-adatbázisba. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
