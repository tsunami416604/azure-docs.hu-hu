---
title: "MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez a Node.js használatával | Microsoft Docs"
description: "Megtudhatja, hogyan csatlakoztathat egy meglévő Node.js MongoDB-alkalmazást az Azure Cosmos DB adatbázishoz."
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 0e5657e4d110af095c934431cb6e3bf8824f791d
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB: Meglévő Node.js MongoDB-webalkalmazás migrálása 

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum-, kulcs/érték és gráf típusú adatbázisokat, melyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket. 

Ez a bevezető ismerteti a Node.js-ben írt, meglévő [MongoDB](../documentdb/documentdb-protocol-mongodb.md)-alkalmazások használatát, valamint azok összekapcsolásának menetét a MongoDB-ügyfélkapcsolatokat támogató Azure Cosmos DB-adatbázissal. Más szóval a Node.js-alkalmazás csak azt tudja, hogy a MongoDB API-k használatával csatlakozott egy adatbázishoz. Az alkalmazás nem látja, hogy az adatokat az Azure Cosmos DB tárolja.

Az útmutató végére MEAN-alkalmazása (MongoDB, Express, AngularJS és Node.js) az [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) rendszert használva fog futni. 

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/create-mongodb-nodejs/meanjs-in-azure.png)

## <a name="prerequisites"></a>Előfeltételek 

A rövid útmutató elindítása előtt ellenőrizze, hogy [az Azure CLI telepítve van-e](https://docs.microsoft.com/cli/azure/install-azure-cli) a gépen. Szüksége lesz továbbá a [Node.js](https://nodejs.org/) és a [Git](http://www.git-scm.com/downloads) szoftverekre. Az útmutató során az `az`, `npm` és `git` parancsokat fogja futtatni.

Emellett ajánlott rendelkeznie a Node.js használatához szükséges ismeretekkel. A rövid útmutatónak nem célja általános segítséget nyújtani a Node.js-alkalmazások fejlesztéséhez.

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` paranccsal lépejen át egy munkakönyvtárba.  

Az alábbi parancsok futtatásával klónozza a mintatárházat. Ez a mintatárház az alapértelmezett [MEAN.js](http://meanjs.org/)-alkalmazást tartalmazza. 

```bash
git clone https://github.com/prashanthmadi/mean
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
cd mean
npm install
npm start
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Használja az Azure CLI 2.0-t egy terminálablakban a Node.js-alkalmazás Azure App Service-ben történő üzemeltetéséhez szükséges erőforrások létrehozásához.  Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat. 

```azurecli 
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Az Azure Cosmos DB modul hozzáadása

Azure Cosmos DB parancsok használatához adja hozzá a rendszerhez az Azure Cosmos DB modult. 

```azurecli
az component update --add cosmosdb
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például webappokat, adatbázisokat és tárfiókokat). 

A következő példában létrehozunk egy erőforráscsoportot a nyugat-európai régióban. Adjon egyedi nevet az erőforráscsoportnak.

```azurecli
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos DB-fiókot az [az cosmosdb create](/cli/azure/cosmosdb#create) paranccsal.

A következő parancsban a `<cosmosdb_name>` helyőrző helyett írja be Azure Cosmos DB-fiókjának egyedi nevét. A nevet a rendszer Azure Cosmos DB-végpontként (`https://<cosmosdb_name>.documents.azure.com/`) fogja használni, így annak egyedinek kell lennie az összes Azure-beli Azure Cosmos DB-fiók között. 

```azurecli
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

A `--kind MongoDB` paraméter lehetővé teszi a MongoDB-ügyfélkapcsolatok használatát.

Az Azure Cosmos DB-fiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb_name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb_name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb_name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb_name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>A Node.js-alkalmazás csatlakoztatása az adatbázishoz

Ebben a lépésben a MEAN.js-mintaalkalmazást a MongoDB-kapcsolati karakterlánc használatával egy korábban létrehozott Azure Cosmos DB-adatbázishoz fogjuk csatlakoztatni. 

## <a name="retrieve-the-key"></a>A kulcs lekérése

Az Azure Cosmos DB-adatbázishoz való csatlakozáshoz adatbáziskulcs szükséges. Az [az documentdb list-keys](/cli/azure/documentdb#list-keys) parancs használatával kérje le az elsődleges kulcsot.

```azurecli
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg a kimeneti adatokat. 

```json
{
  "primaryMasterKey": "RUayjYjixJDWG5xTqIiXjC...",
  "primaryReadonlyMasterKey": "...",
  "secondaryMasterKey": "...",
  "secondaryReadonlyMasterKey": "..."
}
```

Másolja a `primaryMasterKey` értékét egy szövegszerkesztőbe. A következő lépés során szükség lesz erre az információra.

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>A kapcsolati karakterlánc konfigurálása a Node.js-alkalmazásban

A MEAN.js-tárházban nyissa meg a `config/env/local-development.js` fájlt.

Cserélje le a fájl tartalmát a következő kódra. Ügyeljen arra, hogy a két `<cosmosdb_name>` helyőrzőt se felejtse el lecserélni Azure Cosmos DB-fióknevére. A `<primary_master_key>` helyőrzőt az előző lépésben kimásolt kulccsal helyettesítse.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

> [!NOTE] 
> Az `ssl=true` beállítás azért fontos, mert az [Azure Cosmos DB-adatbázis SSL használatát igényli](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements). 
>
>

Mentse a módosításokat.

### <a name="run-the-application-again"></a>Futtassa ismét az alkalmazást.

Futtassa ismét az `npm start` parancsot. 

```bash
npm start
```

Ekkor egy konzolüzenet arról értesíti, hogy a fejlesztőkörnyezet fut. 

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre két tesztfelhasználót. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a MEAN.js-nek sikerül automatikusan bejelentkeznie a létrehozott felhasználói fiókba, akkor az Azure Cosmos DB-adatbázissal létesített kapcsolat megfelelően működik. 

![A MEAN.js sikeresen csatlakozik a MongoDB-hez](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Adatok megtekintése az Adatkezelőben

Az Azure Cosmos DB-adatbázisokban tárolt adatok az Azure Portalon megtekinthetők, lekérdezhetők, valamint azokon üzleti logika futtatható.

Az előző lépésben létrehozott felhasználói adatok megtekintéséhez, lekérdezéséhez, valamint az azokkal való munkához böngészőjében jelentkezzen be az [Azure Portalra](https://portal.azure.com).

A felső keresőmezőbe írja be az Azure Cosmos DB kifejezést. Amikor megnyílik a Cosmos DB-fiók panelje, válassza ki Cosmos DB-fiókját. A bal oldali navigációs panelen kattintson az Adatkezelőre. A Gyűjtemények panelen bontsa ki gyűjteményét. Ezt követően megtekintheti a gyűjteményhez tartozó dokumentumokat, lekérdezhet adatokat, valamint létrehozhat és futtathat tárolt eljárásokat, eseményindítókat és felhasználói függvényeket. 

![Adatkezelő az Azure Portalon](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>A Node.js-alkalmazás központi telepítése az Azure-ban

Ebben a lépésben a MongoDB-hez csatlakoztatott Node.js-alkalmazást telepítjük az Azure Cosmos DB-adatbázisba.

Talán észrevette, hogy a korábban módosított konfigurációs fájl a fejlesztési környezetre vonatkozik (`/config/env/local-development.js`). Miután az alkalmazást központilag telepíti az App Service-be, az alapértelmezés szerint az éles környezetben fog futni. Ezért az arra vonatkozó konfigurációs fájlban is el kell végezni a korábbi módosításokat.

A MEAN.js-tárházban nyissa meg a `config/env/production.js` fájlt.

A `db` objektumban cserélje le az `uri` értékét az alábbi példa szerint. A korábbiakhoz hasonlóan, most se felejtse el lecserélni a helyőrzőket.

```javascript
'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false',
```

A terminálban mentse a módosításait a Gitbe. Mindkét parancsot lemásolhatja és azokat egyszerre is futtathatja.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban bemutattuk az Azure Cosmos DB-fiókok létrehozásának menetét, valamint bemutattuk, hogyan hozható létre MongoDB-gyűjtemény az Adatkezelő segítségével. Így már áttelepítheti a MongoDB-adatait az Azure Cosmos DB-adatbázisba.  

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](../documentdb/documentdb-mongodb-migrate.md)

