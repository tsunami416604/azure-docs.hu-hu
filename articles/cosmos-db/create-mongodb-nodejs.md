---
title: 'Rövid útmutató: Node.js MongoDB alkalmazás csatlakoztatása az Azure Cosmos DB-hez'
description: Ez a rövid útmutató bemutatja, hogyan csatlakoztathat egy node.js nyelven írt meglévő MongoDB-alkalmazást az Azure Cosmos DB-hez.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: de4a9324cd1cfaccec6dcca6a8dfc057d37275c0
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619241"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Rövid útmutató: Meglévő MongoDB Node.js webalkalmazás áttelepítése az Azure Cosmos DB-be 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Ebben a rövid útmutatóban hozzon létre és kezeljen egy Azure Cosmos DB Mongo DB API-fiókot az Azure Cloud Shell használatával, valamint a GitHubról klónozott MEAN (MongoDB, Express, Angular és Node.js) alkalmazással. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek
- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül. Az [Azure Cosmos DB emulátort](https://aka.ms/cosmosdb-emulator) is `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`használhatja a kapcsolati karakterlánccal.
- [Node.js](https://nodejs.org/), és a Node.js.
- [Git](https://git-scm.com/downloads).
- Ha nem szeretné használni az Azure Cloud Shellt, [az Azure CLI 2.0+](/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Az alábbi parancsok futtatásával klónozza a mintatárházat. Ez a mintatárház az alapértelmezett [MEAN.js](https://meanjs.org/)-alkalmazást tartalmazza.

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Ez a Node.js fájlban írt MongoDB alkalmazás csatlakozik az Azure Cosmos DB adatbázisához, amely támogatja a MongoDB-ügyfelet. Más szóval az alkalmazás számára, hogy az adatok egy Azure Cosmos DB-adatbázisban tárolt.

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
cd mean
npm install
npm start
```
Az alkalmazás sikertelenül megkísérel csatlakozni egy MongoDB-forráshoz. Lépjen ki az alkalmazásból, amikor a kimenet a következőt adja vissza: „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell, olvassa el az [Azure CLI telepítése] című témakört. 

Ha telepített Azure CLI-t használ, jelentkezzen be az Azure-előfizetésbe az [az bejelentkezési](/cli/azure/reference-index#az-login) paranccsal, és kövesse a képernyőn megjelenő utasításokat. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Az Azure Cosmos DB modul hozzáadása

Telepített Azure-os parancssori felület használata esetén az `az` parancs futtatásával ellenőrizze, hogy a `cosmosdb` összetevő telepítve van-e. Ha a `cosmosdb` szerepel az alapparancsok listáján, lépjen tovább a következő parancsra. Az Azure Cloud Shell használata esetén kihagyhatja ezt a lépést.

Ha a `cosmosdb` nincs az alapparancsok listáján, telepítse újra az [Azure CLI-t](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/management/overview.md) az [az csoport létrehozásával.](/cli/azure/group#az-group-create) Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat). 

A következő példában létrehozunk egy erőforráscsoportot a nyugat-európai régióban. Adjon egyedi nevet az erőforráscsoportnak.

Ha Az Azure Cloud Shell, válassza **a Try It**lehetőséget, kövesse a képernyőn megjelenő utasításokat a bejelentkezéshez, majd másolja a parancsot a parancssorba.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Cosmos-fiókot az [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) paranccsal.

A következő parancsban helyettesítse saját egyedi Cosmos-fiókjának nevét, ahol a `<cosmosdb-name>` helyőrző látható. Ez az egyedi név a Cosmos DB-végpont`https://<cosmosdb-name>.documents.azure.com/`( részeként lesz használva), így a névnek egyedinek kell lennie az Azure összes Cosmos-fiókjában. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

A `--kind MongoDB` paraméter lehetővé teszi a MongoDB-ügyfélkapcsolatok használatát.

Az Azure Cosmos DB-fiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. 

> [!NOTE]
> Ez a példa az alapértelmezett JSON formátumot használja az Azure CLI kimeneti formátumaként. Más kimeneti formátum használatához lásd: [Az Azure CLI-parancsok kimeneti formátumai](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>A Node.js-alkalmazás csatlakoztatása az adatbázishoz

Ebben a lépésben csatlakoztathatja a MEAN.js mintaalkalmazást az imént létrehozott Azure Cosmos DB adatbázis-fiókhoz. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>A kapcsolati sztring konfigurálása a Node.js-alkalmazásban

A MEAN.js-tárházban nyissa meg a `config/env/local-development.js` fájlt.

Cserélje le a fájl tartalmát a következő kódra. Ügyeljen arra is, `<cosmosdb-name>` hogy cserélje le a két helyőrzők a Cosmos-fiók nevét.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>A kulcs lekérése

A Cosmos-adatbázishoz való csatlakozáshoz szüksége van az adatbáziskulcsra. Használja az [az cosmosdb kulcsok lista](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) parancs az elsődleges kulcs lekéréséhez.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg a kimeneti adatokat. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Másolja a `primaryMasterKey` értékét. Illessze be a `<primary_master_key>` fölé a `local-development.js` fájlban.

Mentse a módosításokat.

### <a name="run-the-application-again"></a>Futtassa ismét az alkalmazást.

Futtassa ismét az `npm start` parancsot. 

```bash
npm start
```

Ekkor egy konzolüzenet arról értesíti, hogy a fejlesztőkörnyezet fut. 

Ugrás `http://localhost:3000` a böngészőben. Válassza a **Feliratkozás** lehetőséget a felső menüben, és próbáljon meg két hamis felhasználót létrehozni. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a MEAN.js-nek sikerül automatikusan bejelentkeznie a létrehozott felhasználói fiókba, akkor az Azure Cosmos DB-adatbázissal létesített kapcsolat megfelelően működik. 

![A MEAN.js sikeresen csatlakozik a MongoDB-hez](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

A Cosmos-adatbázisban tárolt adatok az Azure Portalon tekinthetők meg és kérdezhatók le.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be az Azure Cosmos DB című **jelölőnégyzetet.** Amikor a Cosmos-fiókpanel megnyílik, válassza ki a Cosmos-fiók. A bal oldali navigációs sávon válassza az **Adatkezelő**lehetőséget. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket. 

![Adatkezelő az Azure Portalon](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>A Node.js-alkalmazás központi telepítése az Azure-ban

Ebben a lépésben üzembe helyezheti a Node.js alkalmazást a Cosmos DB-re.

Talán észrevette, hogy a korábban módosított konfigurációs fájl a fejlesztési környezetre vonatkozik (`/config/env/local-development.js`). Miután az alkalmazást központilag telepíti az App Service-be, az alapértelmezés szerint az éles környezetben fog futni. Ezért az arra vonatkozó konfigurációs fájlban is el kell végezni a korábbi módosításokat.

A MEAN.js-tárházban nyissa meg a `config/env/production.js` fájlt.

A `db` objektumban cserélje le az `uri` értékét az alábbi példa szerint. A korábbiakhoz hasonlóan, most se felejtse el lecserélni a helyőrzőket.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> A `ssl=true` lehetőség a Cosmos DB követelményei miatt fontos. További információt a [Kapcsolati karakterlánc követelményei](connect-mongodb-account.md#connection-string-requirements)című témakörben talál.
>
>

A terminálban mentse a módosításait a Gitbe. Mindkét parancsot lemásolhatja és azokat egyszerre is futtathatja.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB MongoDB API-fiókot az Azure Cloud Shell használatával, és hozzon létre és futtasson egy MEAN.js alkalmazást a felhasználók fiókhoz való hozzáadásához. Így már további adatokat importálhat az Azure Cosmos DB-fiókba.

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
