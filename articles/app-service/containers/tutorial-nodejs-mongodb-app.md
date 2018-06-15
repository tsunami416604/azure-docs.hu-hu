---
title: Node.js- és MongoDB-webalkalmazás létrehozása Linuxon futó Azure App Service-ben | Microsoft Docs
description: Megismerheti, hogyan tehet szert egy olyan, a Linuxon futó Azure App Service-ben működő Node.js-alkalmazásra, amely MongoDB-kapcsolati karakterlánccal rendelkező Cosmos DB-hez csatlakozik.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ee7f37f83d6b3503df1af61509f6f85ca19bc13e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29932745"
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure-app-service-on-linux"></a>Node.js- és MongoDB-webalkalmazás létrehozása Linuxon futó Azure App Service-ben

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Linuxon futó App Service-ben. A _Windowson_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [Node.js- és MongoDB-webalkalmazás létrehozása az Azure-ban](../app-service-web-tutorial-nodejs-mongodb-app.md).
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy Node.js-webalkalmazást, hogyan csatlakoztathatja egy helyi MongoDB-adatbázishoz, majd hogyan helyezheti üzembe az Azure-ban, egy CosmosDB-adatbázishoz csatlakoztatva a MongoDB API segítségével. Az oktatóanyag eredménye egy, a Linux App Service-ben futó MEAN-alkalmazás (MongoDB, Express, AngularJS, and Node.js) lesz. Az egyszerűség kedvéért a mintaalkalmazás a [MEAN.js webes keretrendszert](http://meanjs.org/) használja.

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * CosmosDB-adatbázis létrehozása a MongoDB API segítségével az Azure-ban
> * Node.js-alkalmazás csatlakoztatása a MongoDB-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Node.js 6.0-s vagy újabb verzióját és az NPM-et](https://nodejs.org/).
1. [Telepítse a Gulp.js-t](http://gulpjs.com/) (a [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started) követelménye)
1. [Telepítse és futtassa a MongoDB Community Editiont](https://docs.mongodb.com/manual/administration/install-community/).

## <a name="test-local-mongodb"></a>Helyi MongoDB tesztelése

Nyissa meg a terminálablakot, és a `cd` paranccsal lépjen a `bin` MongoDB telepítés könyvtárába. Ezzel a terminálablakkal futtathatja az oktatóanyagban szereplő összes parancsot.

Futtassa a `mongo` parancsot a terminálban a helyi MongoDB-kiszolgálójához való csatlakozáshoz.

```bash
mongo
```

Ha a kapcsolat létrejött, akkor a MongoDB-adatbázis már fut. Ha nem, a [MongoDB Community Edition telepítését](https://docs.mongodb.com/manual/administration/install-community/) ismertető cikk lépései szerint indítsa el a helyi MongoDB-adatbázist. Gyakran előfordul, hogy a MongoDB telepítése már megtörtént, azonban még el kell indítani a `mongod` parancs futtatásával.

Ha végzett a MongoDB-adatbázis tesztelésével, írja be a `Ctrl+C` parancsot a terminálon.

## <a name="create-local-nodejs-app"></a>Helyi Node.js-alkalmazás létrehozása

Ebben a lépésben a helyi Node.js-projektet állítja be.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa a következő parancsot a minta tárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Ez a mintaadattár a [MEAN.js-adattár](https://github.com/meanjs/mean) másolatát tartalmazza. Módosítva lett az App Service-ben való futtatáshoz (további információért tekintse meg a MEAN.js-adattár [információs fájlját](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alábbi parancsokat a szükséges csomagok telepítéséhez és az alkalmazás elindításához.

```bash
cd meanjs
npm install
npm start
```

Hagyja figyelmen kívül a config.domain figyelmeztetést. Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

```txt
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre egy tesztfelhasználót. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a felhasználó létrehozása és a bejelentkezés sikeres volt, akkor az alkalmazás a helyi MongoDB-adatbázisba írja az adatokat.

![A MEAN.js sikeresen csatlakozik a MongoDB-hez](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Válassza az **Admin > Manage Articles** (Felügyelet > Cikkek kezelése) elemet néhány cikk hozzáadásához.

A Node.js leállításához nyomja le a `Ctrl+C` billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Éles MongoDB létrehozása

Ebben a lépésben egy MongoDB-adatbázist hoz létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

A MongoDB esetében ez az oktatóanyag az [Azure Cosmos DB-t](/azure/documentdb/) használja. A Cosmos DB támogatja a MongoDB-ügyfélkapcsolatokat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása

A Cloud Shellben hozzon létre egy Cosmos DB-fiókot az [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create) paranccsal.

Az alábbi parancsban a *\<cosmosdb_name>* helyőrző helyére írjon be egy egyedi Cosmos DB-nevet. Ezt a nevet a rendszer a Cosmos DB-végpont (`https://<cosmosdb_name>.documents.azure.com/`) részeként fogja használni, így a névnek egyedinek kell lennie az Azure-ban található összes Cosmos DB-fiókban. A név csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat, és 3–50 karakter hosszúságú lehet.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

A *--kind MongoDB* paraméter lehetővé teszi a MongoDB-ügyfélkapcsolatok használatát.

A Cosmos DB-fiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies":
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Alkalmazás csatlakoztatása az éles MongoDB-hez

Ebben a lépésben a MEAN.js-mintaalkalmazást a MongoDB-kapcsolati karakterlánc használatával a korábban létrehozott Cosmos DB-adatbázishoz fogjuk csatlakoztatni.

### <a name="retrieve-the-database-key"></a>Az adatbáziskulcs lekérése

A Cosmos DB-adatbázishoz való csatlakozáshoz adatbáziskulcs szükséges. A Cloud Shellben használja az [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_list_keys) parancsot az elsődleges kulcs lekéréséhez.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg az adatokat:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Másolja a `primaryMasterKey` értékét. A következő lépés során szükség lesz erre az információra.

<a name="devconfig"></a>

### <a name="configure-the-connection-string-in-your-nodejs-application"></a>A kapcsolati karakterlánc konfigurálása a Node.js-alkalmazásban

A helyi MEAN.js-adattár _config/env/_ mappájában hozzon létre egy _local-production.js_ nevű fájlt. A _.gitignore_ a fájlnak az adattáron kívül való tartására lett konfigurálva.

Másolja az alábbi kódot a fájlba. Ügyeljen arra, hogy cserélje le a két *\<cosmosdb_name>* helyőrzőt a Cosmos DB-adatbázis nevére, illetve cserélje le a *\<primary_master_key>* helyőrzőt az előző lépésben másolt kulcsra.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A `ssl=true` beállításra azért van szükség, mert a [Cosmos DB SSL használatát igényli](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Mentse a módosításokat.

### <a name="test-the-application-in-production-mode"></a>Az alkalmazás tesztelése éles módban

Egy helyi terminálablakban futtassa a következő parancsot a szkriptek lekicsinyítésére és csomagolására az éles környezet számára. Ez a folyamat az éles környezet számára szükséges fájlokat hozza létre.

```bash
gulp prod
```

Egy helyi terminálablakban futtassa az alábbi parancsot a _config/env/local-production.js_ fájlban konfigurált kapcsolati karakterlánc használatára. Hagyja figyelmen kívül a tanúsítványhibát és a config.domain figyelmeztetést.

```bash
NODE_ENV=production node server.js
```

A `NODE_ENV=production` beállítja a környezeti változót, amely az éles környezetben történő futásra utasítja a Node.js-t.  A `node server.js` elindítja a Node.js-kiszolgálót az adattár gyökérkönyvtárában található `server.js` fájllal. Így történik a Node.js-alkalmazás betöltése az Azure-ban.

Az alkalmazás betöltése után ellenőrizze, hogy fut-e az éles környezetben:

```
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Egy böngészőben nyissa meg a `http://localhost:8443` oldalt. A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre egy tesztfelhasználót. Ha a felhasználó létrehozása és a bejelentkezés sikeres volt, akkor az alkalmazás a Cosmos DB-adatbázisba írja az adatokat az Azure-ban.

A Node.js leállításához nyomja le a `Ctrl+C` billentyűkombinációt a terminálon.

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben a MongoDB-hez csatlakoztatott Node.js-alkalmazást fogja üzembe helyezni az Azure App Service-ben.

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-linux-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Környezeti változó konfigurálása

Alapértelmezés szerint a MEAN.js-projekt a Git-adattáron kívül tartja a _config/env/local-production.js_ fájlt. Az Azure-webalkalmazás esetén ezért alkalmazásbeállításokat kell használnia a MongoDB kapcsolati karakterláncának meghatározásához.

Az alkalmazásbeállítások megadásához használja az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) parancsot a Cloud Shellben.

A következő példa egy `MONGODB_URI` alkalmazásbeállítást konfigurál az Azure-webalkalmazásban. Cserélje le az *\<app_name>*, a *\<cosmosdb_name>* és a *\<primary_master_key>* helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

A Node.js-kódban a `process.env.MONGODB_URI` használatával érheti el ezt az alkalmazásbeállítást, mint ahogy bármelyik környezeti változót is. 

A helyi MEAN.js-adattárban nyissa meg a _config/env/production.js_ fájlt (ne a _config/env/local-production.js_ fájlt), amely az éles környezetre vonatkozó konfigurációval rendelkezik. Az alapértelmezett MEAN.js-alkalmazás már a létrehozott `MONGODB_URI` környezeti változó használatára lett konfigurálva.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

Azt tapasztalhatja, hogy az üzembehelyezési folyamat az `npm install` után kezdi meg a [Gulp](http://gulpjs.com/) futtatását. Az App Service nem futtatja a Gulp vagy a Grunt feladatait az üzembe helyezés során, ezért ez a mintaadattár két további fájllal rendelkezik a gyökérkönyvtárában ennek lehetővé tételéhez:

- _.deployment_ – Ez a fájl utasítja az App Service-t, hogy a `bash deploy.sh` fájlt futtassa egyéni üzembehelyezési szkriptként.
- _deploy.sh_ – Az egyéni üzembehelyezési szkript. Ha áttekinti a fájlt, láthatja, hogy az `npm install` és a `bower install` után futtatja a `gulp prod` parancsot.

Ezzel a módszerrel adhat hozzá további lépéseket a Git-alapú üzemelő példányhoz. Ha újraindítja az Azure-webalkalmazást, az App Service nem futtatja újra ezeket az automatizálási feladatokat.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Keresse meg az üzembe helyezett webalkalmazást a webböngésző használatával.

```bash
http://<app_name>.azurewebsites.net
```

A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre egy tesztfelhasználót.

Ha sikerrel jár, és az alkalmazás automatikusan bejelentkezik a létrehozott felhasználói fiókba, akkor a MEAN.js-alkalmazás az Azure-ban kapcsolódik a Cosmos DB-adatbázis MongoDB API felületéhez.

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Válassza az **Admin > Manage Articles** (Felügyelet > Cikkek kezelése) elemet néhány cikk hozzáadásához.

**Gratulálunk!** Egy adatvezérelt Node.js-alkalmazást futtat a Linuxon futó Azure App Service-ben.

## <a name="update-data-model-and-redeploy"></a>Az adatmodell frissítése és ismételt üzembe helyezése

Ebben a lépésben módosítja az `article` adatmodellt, és közzéteszi a módosítást az Azure-ban.

### <a name="update-the-data-model"></a>Az adatmodell frissítése

A helyi MEAN.js-adattárban nyissa meg a _modules/articles/server/models/article.server.model.js_ fájlt.

Az `ArticleSchema` sémában adjon hozzá egy `comment` nevű `String` típust. Amikor elkészült, a séma kódjának így kell kinéznie:

```javascript
var ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>A cikkek kódjának frissítése

Frissítse az `articles` kód többi részét a `comment` használatára.

Öt fájlt kell módosítania: a kiszolgálóvezérlőt és a négy ügyfélnézetet. 

Nyissa meg a _modules/articles/server/controllers/articles.server.controller.js_ fájlt.

A `update` függvényben vegyen fel egy hozzárendelést a `article.comment` számára. Az alábbi kódban a befejezett `update` függvény látható:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Nyissa meg a _modules/articles/client/views/view-article.client.view.html_ fájlt.

Közvetlenül a záró `</section>` címke felett adja hozzá a következő sort a `comment` megjelenítéséhez a cikk további adatai mellett.

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Nyissa meg a _modules/articles/client/views/list-articles.client.view.html_ fájlt.

Közvetlenül a záró `</a>` címke felett adja hozzá a következő sort a `comment` megjelenítéséhez a cikk további adatai mellett.

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Nyissa meg a _modules/articles/client/views/admin/list-articles.client.view.html_ fájlt.

A `<div class="list-group">` elemben és közvetlenül a záró `</a>` címke felett adja hozzá a következő sort a `comment` megjelenítéséhez a cikk további adatai mellett:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Nyissa meg a _modules/articles/client/views/admin/form-article.client.view.html_ fájlt.

Keresse meg az elküldés gombot tartalmazó `<div class="form-group">` elemet, amelynek a következőképpen kell kinéznie:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Közvetlenül a címke felett adjon hozzá egy másik `<div class="form-group">` elemet, amely lehetővé teszi a `comment` mező szerkesztését a felhasználók számára. Az új elemnek így kell kinéznie:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Módosítások helyi tesztelése

Mentse az összes módosítást.

A helyi terminálablakban tesztelje újra a módosításokat éles módban.

```bash
gulp prod
NODE_ENV=production node server.js
```

Nyissa meg a `http://localhost:8443` címet egy böngészőben, és ellenőrizze, hogy bejelentkezett-e.

Válassza az **Admin > Manage Articles** (Felügyelet > Cikkek kezelése) elemet, majd vegyen fel egy cikket a **+** gombra kattintva.

Megjelenik az új `Comment` szövegmező.

![Cikkekhez hozzáadott megjegyzésmező](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

A Node.js leállításához nyomja le a `Ctrl+C` billentyűkombinációt a terminálon.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "added article comment"
git push azure master
```

Miután a `git push` befejeződött, lépjen az Azure webalkalmazáshoz, és próbálja ki az új funkciót.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Ha korábban hozzáadott cikkeket, továbbra is láthatja őket. A Cosmos DB-ben meglévő adatok nem vesztek el. Emellett az adatséma frissítései is érintetlenül hagyják a meglévő adatokat.

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazás kezelése

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Alapértelmezés szerint a portálon a webalkalmazás **Áttekintés** oldala jelenik meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * CosmosDB-adatbázis létrehozása a MongoDB API segítségével az Azure-ban
> * Node.js-alkalmazás csatlakoztatása a MongoDB-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)
