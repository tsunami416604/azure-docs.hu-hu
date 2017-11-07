---
title: "Build a Node.js és a MongoDB webalkalmazás az Azure-ban |} Microsoft Docs"
description: "Útmutató az Azure, a MongoDB kapcsolati karakterlánccal Cosmos-adatbázis adatbázis-kapcsolat használata Node.js-alkalmazásokat."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 9fc11352a031ac1c1abcc6c6bd173bd9b0e8a222
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure"></a>Az Azure-ban Node.js és a MongoDB webalkalmazás létrehozása

Az Azure Web Apps jól skálázható, önálló javítási a webhelyszolgáltató biztosít. Ez az oktatóanyag bemutatja, hogyan Node.js-webalkalmazás létrehozása az Azure-ban, és csatlakoztassa a MongoDB-adatbázist. Amikor elkészült, konfigurálnia kell egy átlagos alkalmazás (MongoDB, Express, AngularJS és Node.js) fut a [Azure App Service](app-service-web-overview.md). Az egyszerűség kedvéért a mintaalkalmazást használ a [MEAN.js webes keretrendszer](http://meanjs.org/).

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Ismertetett témák:

> [!div class="checklist"]
> * MongoDB-adatbázis létrehozása az Azure-ban
> * A Node.js alkalmazás csatlakoztatása a mongodb-Protokolltámogatással
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Node.js-t és az NPM-et](https://nodejs.org/)
1. [Telepítse a Gulp.js](http://gulpjs.com/) (által megkövetelt [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Telepítheti és futtathatja a MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-mongodb"></a>Teszt helyi mongodb-Protokolltámogatással

Nyissa meg a terminálablakot, és `cd` való a `bin` a MongoDB telepítési könyvtárába. Ebben az oktatóanyagban a parancsok futtatásához használhatja a terminálablakot.

Futtatás `mongo` a terminálban a helyi MongoDB-kiszolgálóhoz való csatlakozáshoz.

```bash
mongo
```

Ha a kapcsolódás sikeres, majd a MongoDB adatbázis már fut. Ha nem, győződjön meg arról, hogy elindult-e a helyi MongoDB-adatbázist a következő lépéseket követve [MongoDB Community Edition telepítése](https://docs.mongodb.com/manual/administration/install-community/). Gyakran, MongoDB telepítve van, de továbbra is szeretné el `mongod`. 

Ha elkészült a MongoDB adatbázis tesztelése, írja be a `Ctrl+C` a terminálon. 

## <a name="create-local-nodejs-app"></a>Helyi Node.js-alkalmazás létrehozása

Ebben a lépésben beállíthatja a helyi Node.js-projektet.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A Terminálszolgáltatások ablakban `cd` egy működő könyvtárba.  

Futtassa a következő parancsot a minta tárház klónozásához. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Ez a minta-tárház másolatát tartalmazza a [MEAN.js tárház](https://github.com/meanjs/mean). Az App Service futtatásához módosítása (további információkért lásd: a MEAN.js tárház [információs fájl](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Az alkalmazás futtatása

A következő parancsokat a szükséges csomagok telepítése, és indítsa el az alkalmazást.

```bash
cd meanjs
npm install
npm start
```

Az alkalmazás teljesen betöltődik, lásd a következő üzenet hasonló:

```console
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. Kattintson a **regisztráció** a felső menüben és tesztfelhasználó létrehozása. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a felhasználó létrehozásának és a bejelentkezés sikeres, majd az alkalmazás ír adatokat a helyi MongoDB-adatbázist.

![A MEAN.js sikeresen csatlakozik a MongoDB-hez](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Válassza ki **Admin > kezelése cikkek** egyes cikkek hozzáadni.

Node.js bármikor leállításához nyomja le az `Ctrl+C` a terminálon. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Éles MongoDB létrehozása

Ebben a lépésben az Azure-ban MongoDB adatbázis létrehozása. Az alkalmazás telepítésekor az Azure-ba, használja a felhő adatbázis.

Ez az oktatóanyag használja a MongoDB, [Azure Cosmos DB](/azure/documentdb/). Cosmos DB támogatja a MongoDB-ügyfélkapcsolatokat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>A Cosmos DB-fiók létrehozása

A felhő rendszerhéj a Cosmos DB-fiók létrehozása a [az cosmosdb létrehozása](/cli/azure/cosmosdb#create) parancsot.

Az alábbi parancs egyedi Cosmos DB nevet helyettesítse a  *\<cosmosdb_name >* helyőrző. Ez a név a Cosmos DB végpont részeként használatos `https://<cosmosdb_name>.documents.azure.com/`, így a nevének egyedinek kell lennie az Azure-ban összes Cosmos DB fiók. A név csak kisbetűket, számokat és a kötőjel (-) karaktert kell tartalmaznia, és 3 – 50 karakter közé kell esnie.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

A *--kind MongoDB* paraméter lehetővé teszi, hogy a MongoDB-ügyfélkapcsolatokat.

Ha a Cosmos DB-fiók létrehozása az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló:

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

## <a name="connect-app-to-production-mongodb"></a>Alkalmazás csatlakoztatása a termelési mongodb-Protokolltámogatással

Ebben a lépésben csatlakoztatja az imént létrehozott, a MongoDB-kapcsolati karakterlánc használatával Cosmos DB adatbázishoz MEAN.js mintaalkalmazást. 

### <a name="retrieve-the-database-key"></a>Az adatbázis-kulcs beolvasása

A Cosmos DB adatbázishoz való kapcsolódáshoz szükséges az adatbázis-kulcs. A felhő rendszerhéj használata a [az cosmosdb lista-kulcsok](/cli/azure/cosmosdb#list-keys) parancsot az elsődleges kulcs lekéréséhez.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló:

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

A helyi MEAN.js tárházban lévő a _config/env/_ mappa, hozzon létre egy fájlt _helyi-production.js_. Alapértelmezés szerint _.gitignore_ ezt a fájlt a tárház kívül van konfigurálva. 

A következő kódot bemásolhatja azt. Ügyeljen arra, hogy cserélje le a két  *\<cosmosdb_name >* helyőrzőt a Cosmos DB adatbázis neve, és cserélje le a  *\<primary_master_key >* helyőrző a kulccsal, az előző lépésben a vágólapra másolt.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A `ssl=true` beállítást azért szükség, mert [Cosmos DB SSL megkövetelése](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Mentse a módosításokat.

### <a name="test-the-application-in-production-mode"></a>Az alkalmazást tesztelheti éles módban 

A következő parancsot minify és az éles környezet parancsfájlok csomagot. Ez a folyamat által az éles környezetben szükséges fájlok állít elő.

```bash
gulp prod
```

A következő parancsot a kapcsolati karakterlánc, amelyet _config/env/local-production.js_.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production`Beállítja a környezeti változó, amely közli a Node.js az éles környezetben történő futását.  `node server.js`Elindítja a Node.js-kiszolgálót `server.js` az adattár gyökérkönyvtárában található. Ez az, hogy be van töltve a Node.js-alkalmazás az Azure-ban. 

Ha az alkalmazás be van töltve, ellenőrizze, hogy fut-e az éles környezetben:

```console
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Egy böngészőben nyissa meg a `http://localhost:8443` oldalt. Kattintson a **regisztráció** a felső menüben és tesztfelhasználó létrehozása. Ha sikeres felhasználó létrehozása, és jelentkezzen be, majd az alkalmazás az adatok Cosmos DB adatbázis írásakor az Azure-ban. 

A terminálban leállításához írja be a Node.js `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Alkalmazás telepítése az Azure-bA

Ebben a lépésben az Azure App Service a Node.js MongoDB-kompatibilis alkalmazást telepít központilag.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Egy környezeti változó beállítása

Alapértelmezés szerint a MEAN.js projekt tartja _config/env/local-production.js_ kívül a Git-tárházba. Ezért a Azure webalkalmazás app beállításai segítségével határozza meg a MongoDB-kapcsolati karakterlánc.

Alkalmazásbeállítások beállításához használja a [az webapp config appsettings frissítése](/cli/azure/webapp/config/appsettings#update) a felhő rendszerhéj parancsot. 

A következő példa egy `MONGODB_URI` Alkalmazásbeállítás az Azure web app alkalmazásban. Cserélje le a  *\<alkalmazás_neve >*,  *\<cosmosdb_name >*, és  *\<primary_master_key >* helyőrzők.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

A Node.js-kódnak érheti el az alkalmazás setting `process.env.MONGODB_URI`, ahogy bármely környezeti változó elérésére. 

Nyissa meg a helyi MEAN.js tárház _config/env/production.js_ (nem _config/env/local-production.js_), amely éles környezet adott konfigurációval rendelkezik. Az alapértelmezett MEAN.js alkalmazás már használatára van konfigurálva a `MONGODB_URI` létrehozott környezeti változó.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

Azt tapasztalhatja, hogy fut-e a telepítési folyamat [Gulp](http://gulpjs.com/) után `npm install`. App Service nem futtatható Gulp vagy Grunt feladatok központi telepítése során így ez a minta-tárház két további fájlok a gyökérkönyvtárban engedélyezéséhez: 

- _.Deployment_ – Ez a fájl közli az App Service futtatásához `bash deploy.sh` , az egyéni telepítési parancsfájl.
- _Deploy.SH_ – az egyéni telepítési parancsfájl. Tekintse át a fájlt, ha látni fogja, hogy fusson `gulp prod` után `npm install` és `bower install`. 

Ez a módszer segítségével bármely lépése hozzáadni a Git-alapú telepítéshez. Újraindítja az Azure-webalkalmazásban bármikor, ha az App Service automatizálási feladatok nem futtassa újra.

### <a name="browse-to-the-azure-web-app"></a>Keresse meg az Azure-webalkalmazásban 

Tallózással keresse meg a telepített webalkalmazás webböngészővel. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Kattintson a **regisztráció** a felső menüben, és hozzon létre egy üres felhasználó. 

Ha sikeres, és az alkalmazás automatikusan bejelentkezik a létrehozott felhasználót, majd a MEAN.js alkalmazás az Azure-ban rendelkezik (Cosmos DB) MongoDB adatbázis-kapcsolata. 

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Válassza ki **Admin > kezelése cikkek** egyes cikkek hozzáadni. 

**Gratulálunk!** Adatalapú Node.js-alkalmazás fut az Azure App Service-ben.

## <a name="update-data-model-and-redeploy"></a>Frissítés adatmodell, és helyezze üzembe újra

Ebben a lépésben módosítsa a `article` adatok modell, és közzéteheti a módosítást az Azure-bA.

### <a name="update-the-data-model"></a>Az adatmodell frissítése

Nyissa meg _modules/articles/server/models/article.server.model.js_.

A `ArticleSchema`, vegye fel a `String` nevű típus `comment`. Amikor elkészült, a séma kódot kell néznek ki:

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

### <a name="update-the-articles-code"></a>Frissítse a cikkek kódot

Frissíti a többi a `articles` használandó kódját `comment`.

Módosítania kell öt fájl van: a kiszolgáló tartományvezérlő, és a négy ügyfél nézeteket. 

Nyissa meg _modules/articles/server/controllers/articles.server.controller.js_.

Az a `update` működik, vegye fel a hozzárendelés `article.comment`. A következő kód bemutatja a befejezett `update` függvény:

```javascript
exports.update = function (req, res) {
  var article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Nyissa meg _modules/articles/client/views/view-article.client.view.html_.

A záró felett `</section>` címkét, adja hozzá a következő sort a megjelenítendő `comment` együtt a cikk többi:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Nyissa meg _modules/articles/client/views/list-articles.client.view.html_.

A záró felett `</a>` címkét, adja hozzá a következő sort a megjelenítendő `comment` együtt a cikk többi:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Nyissa meg _modules/articles/client/views/admin/list-articles.client.view.html_.

Belül a `<div class="list-group">` elem és a záró felett `</a>` címkét, adja hozzá a következő sort a megjelenítendő `comment` a cikk többi együtt:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Nyissa meg _modules/articles/client/views/admin/form-article.client.view.html_.

Keresés a `<div class="form-group">` elem, amely tartalmazza a Küldés gomb, mely dolgozunk:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Ez a tag fölött hozzáadása egy másik `<div class="form-group">` elem, amelynek segítségével a szerkesztése a `comment` mező. Az új elem kell kinéznie:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>A módosításokat a helyi tesztelése

Mentse az összes módosítást.

A helyi terminálablakot, a teszteléshez éles módban a módosításokat.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Navigáljon a `http://localhost:8443` a böngészőben, és győződjön meg arról, hogy be van jelentkezve.

Válassza ki **Admin > cikkek kezelése**, majd adja hozzá a cikk kiválasztásával a  **+**  gombra.

Megjelenik az új `Comment` szövegmező most.

![A hozzáadott megjegyzés mező cikkekhez](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

A terminálban leállításához írja be a Node.js `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Változások közzétételére Azure

A helyi terminálablakot a Git a változtatások véglegesítése a határidő, majd küldje le a kód módosításokat az Azure-bA.

```bash
git commit -am "added article comment"
git push azure master
```

Egyszer a `git push` befejezése, navigáljon az Azure web app és az új funkciók kipróbálásához.

![Az Azure-bA közzétett modell és adatbázis módosítása](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Ha korábban hozzáadott olyan cikkek, továbbra is láthatja azokat. A Cosmos DB meglévő adatait nem elvész. A Adatséma is, a frissítések és a meglévő adatok sértetlenek.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók adatfolyam 

A Node.js-alkalmazás futtatása az Azure App Service-ben, közben kaphat az adatcsatornán a terminálon a konzol naplói. Ily módon kaphat segítséget nyújtanak az alkalmazáshibák debug diagnosztikai ugyanazokat az üzeneteket.

Napló streaming indításához használja a [az webapp napló végéről](/cli/azure/webapp/log#tail) a felhő rendszerhéj parancsot.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Miután napló streaming elindult, frissítse az Azure-webalkalmazásban néhány webes forgalom eléréséhez a böngészőben. Ekkor megjelenik a terminálon az adatcsatornán konzolnaplófájlokban.

Írja be a bármikor streaming leállítási napló `Ctrl+C`. 

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazásban kezelése

Lépjen a [Azure-portálon](https://portal.azure.com) létrehozott webalkalmazás megjelenítéséhez.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Alapértelmezés szerint a portál megjeleníti a webalkalmazás **áttekintése** lap. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A lap bal oldalán a lapok megnyithatja a különböző konfigurációs lapok megjelenítése.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Következő lépések

Hogy mit tudott:

> [!div class="checklist"]
> * MongoDB-adatbázis létrehozása az Azure-ban
> * A Node.js alkalmazás csatlakoztatása a mongodb-Protokolltámogatással
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Stream naplók az Azure-ból a Terminálszolgáltatások számára
> * Felügyelheti az alkalmazást az Azure-portálon

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"] 
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
