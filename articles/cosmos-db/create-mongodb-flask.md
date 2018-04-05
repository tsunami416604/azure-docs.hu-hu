---
title: 'Azure Cosmos DB: Flask webalkalmazás létrehozása Pythonnal és az Azure Cosmos DB MongoDB API-val | Microsoft Docs'
description: Egy Python Flask kódmintát mutat be, amellyel csatlakozni lehet az Azure Cosmos DB MongoDB API-hoz, és lekérdezést lehet végezni vele
services: cosmos-db
documentationcenter: ''
author: hshapiro
manager: scicoria
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: ea6920c179bfee22c0aa545ccc3d0386940205e4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB: Flask alkalmazás létrehozása a MongoDB API-val

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

Ez a rövid útmutató a következő [Flask példát](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) használja, és bemutatja, hogyan építhet egyszerű teendőlista Flask alkalmazást a MongoDB helyett az [Azure Cosmos DB Emulatorral](/local-emulator.md) és az Azure Cosmos DB [MongoDB API](mongodb-introduction.md)-val.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure Cosmos DB Emulator](/local-emulator.md) letöltése. Az Emulator jelenleg csak a Windows rendszerben támogatott. A minta bemutatja, hogyan használhatja a mintát az Azure-tól kapott termékkulccsal, ami bármilyen platformon elvégezhető.

- Ha még nincs telepítve a Visual Studio Code, gyorsan telepítheti a [VS Code-ot](https://code.visualstudio.com/Download) a platformján (Windows, Mac, Linux).

- Az egyik népszerű Python bővítmény telepítésével adja hozzá a Python nyelv támogatását.
    1. Válassza ki a bővítményt.
    2. Telepítse a bővítményt a `Ctrl+Shift+P` parancskatalógusban az `ext install` parancs beírásával.

    A dokumentumban szereplő példák Don Jayamanne népszerű és minden funkcióval ellátott [Python bővítményét](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) használja.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Flask-MongoDB API-alkalmazást a GitHubról, beállítjuk a kapcsolati karakterláncot, majd futtatni fogjuk az alkalmazást. Ilyen egyszerű az adatokkal programozott módon dolgozni.

1. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépjen egy munkakönyvtárba.
2. Futtassa a következő parancsot a minta tárház klónozásához.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Futtassa az alábbi parancsot a python modulok telepítéséhez.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Nyissa meg a mappát a Visual Studio Code-ban.

## <a name="review-the-code"></a>A kód áttekintése

Tekintsük át, hogy mi történik az alkalmazásban. Nyissa meg a gyökérkönyvtárban lévő **app.py** fájlt: az itt található kódsorok hozzák létre az Azure Cosmos DB-kapcsolatot. A következő kód a helyi Azure Cosmos DB Emulator kapcsolati karakterláncát használja. A jelszót az alább látható módon kell felosztani a máshogy nem elemezhető perjelek befogadásához.

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

## <a name="update-your-connection-string"></a>A kapcsolati karakterlánc frissítése

Ha tesztelni szeretné a kódot egy élő Azure Cosmos DB-fiókban, lépjen az Azure Portalra egy fiók létrehozásához, és kérje le a kapcsolati karakterlánc adatait. Ezután másolja azokat az alkalmazásba.

1. Az [Azure Portalon](http://portal.azure.com/) az Azure Cosmos DB-fiókban a bal oldalsávon kattintson a **Kapcsolati karakterlánc** elemre, majd kattintson az **írási/olvasási kulcsok** lehetőségre. A következő lépésben használja a képernyő jobb oldalán lévő másolási gombokat a felhasználónév, a jelszó és a gazdagép másolásához a Dal.cs fájlba.

2. Nyissa meg a gyökérkönyvtárban lévő **app.py** fájlt.

3. A másolási gomb használatával másolja ki a **username** érteket a Portalból, és azt adja meg a **name** értékeként az **app.py** fájlban.

4. Ezután másolja ki a **kapcsolati karakterlánc** értékét a Portalból, és azt adja meg a MongoClient értékeként az **app.py** fájlban.

5. Végül másolja ki a **jelszó** értékét a Portalból, és azt adja meg a **password** értékeként az **app.py** fájlban.

Az alkalmazás frissítve lett minden olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. Ugyanúgy futtathatja, mint előtte.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Az alkalmazás üzembe helyezéséhez hozzon létre egy új webalkalmazást az Azure-ban, és engedélyezze a Github-adattár elágazásával végzett folyamatos üzembe helyezést. Kövesse ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment) a GitHubbal való folyamatos üzembe helyezés beállításához az Azure-ban.

Amikor az Azure-ba végez üzembe helyezést, el kell távolítania az alkalmazáskulcsokat, és ellenőriznie kell, hogy az alábbi szakasz ne megjegyzésként szerepeljen:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Ezután hozzá kell adnia a MONGOURL, MONGO_PASSWORD és MONGO_USERNAME elemeket az alkalmazásbeállításokhoz. Ezt az [oktatóanyagot](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) követve további információkat tudhat meg az Azure-webalkalmazások alkalmazásbeállításairól.

Ha nem szeretne létrehozni elágazást ehhez az adattárhoz, az alul található Üzembe helyezés az Azure-ban gombra is kattinthat. Ezután nyissa meg az Azure-t, és állítsa be az alkalmazásbeállításokat a Cosmos DB-fiókadatokkal.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> Ha a Githubon vagy más forrásvezérlő helyen szeretné tárolni a kódot, távolítsa el a kapcsolati karakterláncokat a kódból. Ehelyett a webalkalmazás alkalmazásbeállításaival állíthatók be.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az alkalmazást már nem használja, akkor a következő lépésekkel a mintaalkalmazás által létrehozott összes erőforrást törölheti az Azure Portalon:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtudtuk, hogyan lehet Azure Cosmos DB-fiókot létrehozni, és hogyan lehet Flask alkalmazást futtatni a MongoDB API-val. Most további adatokat importálhat a Cosmos DB-fiókjába.

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be a MongoDB API-hoz](mongodb-migrate.md)
