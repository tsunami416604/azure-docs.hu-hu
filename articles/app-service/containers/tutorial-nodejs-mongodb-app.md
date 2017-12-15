---
title: "A Node.js és a MongoDB webalkalmazás az Azure App Service létrehozása Linux rendszeren |} Microsoft Docs"
description: "Útmutató az Azure App Service Linux, a MongoDB kapcsolati karakterlánccal Cosmos-adatbázis adatbázis-kapcsolat használata Node.js-alkalmazásokat."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: syntaxc4
editor: 
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/10/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: c2087af14ad456c679479334c9391055f6b2e45e
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="build-a-nodejs-and-mongodb-web-app-in-azure-app-service-on-linux"></a>A Node.js és a MongoDB webalkalmazás az Azure App Service létrehozása Linux-kiszolgálón

> [!NOTE]
> Ez a cikk egy alkalmazást az App Service Linux központilag telepíti. Az App Service üzembe _Windows_, lásd: [összeállítása a Node.js és a MongoDB webalkalmazás az Azure-ban](../app-service-web-tutorial-nodejs-mongodb-app.md).
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan Node.js-webalkalmazás létrehozása, helyileg csatlakoztassa a MongoDB-adatbázist, majd telepítse az Azure a MongoDB API-jával CosmosDB adatbázishoz kapcsolódó. Amikor elkészült, konfigurálnia kell egy átlagos alkalmazás (MongoDB, Express, AngularJS és Node.js) az App Service-ben futó Linux. Az egyszerűség kedvéért a mintaalkalmazást használ a [MEAN.js webes keretrendszer](http://meanjs.org/).

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Amiről tanulni hogyan:

> [!div class="checklist"]
> * A MongoDB API használata az Azure-ban CosmosDB adatbázis létrehozása
> * A Node.js alkalmazás csatlakoztatása a mongodb-Protokolltámogatással
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Node.js v6.0 vagy újabb és NPM](https://nodejs.org/)
1. [Telepítse a Gulp.js](http://gulpjs.com/) (által megkövetelt [MEAN.js](http://meanjs.org/docs/0.5.x/#getting-started))
1. [Telepítheti és futtathatja a MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

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

A config.domain figyelmeztetést figyelmen kívül. Az alkalmazás teljesen betöltődik, lásd a következő üzenet hasonló:

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

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. Kattintson a **regisztráció** a felső menüben és tesztfelhasználó létrehozása. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a felhasználó létrehozásának és a bejelentkezés sikeres, majd az alkalmazás ír adatokat a helyi MongoDB-adatbázist.

![A MEAN.js sikeresen csatlakozik a MongoDB-hez](./media/tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Válassza ki **Admin > kezelése cikkek** egyes cikkek hozzáadni.

Node.js bármikor leállításához nyomja le az `Ctrl+C` a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Éles MongoDB létrehozása

Ebben a lépésben az Azure-ban MongoDB adatbázis létrehozása. Az alkalmazás telepítésekor az Azure-ba, használja a felhő adatbázis.

Ez az oktatóanyag használja a MongoDB, [Azure Cosmos DB](/azure/documentdb/). Cosmos DB támogatja a MongoDB-ügyfélkapcsolatokat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-cosmos-db-account"></a>A Cosmos DB-fiók létrehozása

A felhő rendszerhéj a Cosmos DB-fiók létrehozása a [az cosmosdb létrehozása](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_create) parancsot.

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

A Cosmos DB adatbázishoz való kapcsolódáshoz szükséges az adatbázis-kulcs. A felhő rendszerhéj használata a [az cosmosdb lista-kulcsok](/cli/azure/cosmosdb?view=azure-cli-latest#az_cosmosdb_list_keys) parancsot az elsődleges kulcs lekéréséhez.

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

A helyi MEAN.js tárházban lévő a _config/env/_ mappa, hozzon létre egy fájlt _helyi-production.js_. _.gitignore_ ezt a fájlt a tárház kívül van konfigurálva.

A következő kódot bemásolhatja azt. Ügyeljen arra, hogy cserélje le a két  *\<cosmosdb_name >* helyőrzőt a Cosmos DB adatbázis neve, és cserélje le a  *\<primary_master_key >* helyőrző a kulccsal, az előző lépésben a vágólapra másolt.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A `ssl=true` beállítást azért szükség, mert [Cosmos DB SSL megkövetelése](../../cosmos-db/connect-mongodb-account.md#connection-string-requirements).

Mentse a módosításokat.

### <a name="test-the-application-in-production-mode"></a>Az alkalmazást tesztelheti éles módban

Egy helyi terminálablakot futtassa a következő parancsot minify és csomagot az éles környezet parancsfájlok. Ez a folyamat által az éles környezetben szükséges fájlok állít elő.

```bash
gulp prod
```

Egy helyi terminálablakot, futtassa a következő parancsot a kapcsolati karakterlánc, amelyet _config/env/local-production.js_. A Tanúsítványhiba és a config.domain figyelmeztetést figyelmen kívül.

```bash
NODE_ENV=production node server.js
```

`NODE_ENV=production`Beállítja a környezeti változó, amely közli a Node.js az éles környezetben történő futását.  `node server.js`Elindítja a Node.js-kiszolgálót `server.js` az adattár gyökérkönyvtárában található. Ez az, hogy be van töltve a Node.js-alkalmazás az Azure-ban.

Ha az alkalmazás be van töltve, ellenőrizze, hogy fut-e az éles környezetben:

```
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

### <a name="configure-local-git-deployment"></a>A Git helyi üzemelő példányának konfigurálása

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-linux-based-web-app"></a>Linux-alapú webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Egy környezeti változó beállítása

Alapértelmezés szerint a MEAN.js projekt tartja _config/env/local-production.js_ kívül a Git-tárházba. Ezért a Azure webalkalmazás app beállításai segítségével határozza meg a MongoDB-kapcsolati karakterlánc.

Alkalmazásbeállítások beállításához használja a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) a felhő rendszerhéj parancsot.

A következő példa egy `MONGODB_URI` Alkalmazásbeállítás az Azure web app alkalmazásban. Cserélje le a  *\<alkalmazás_neve >*,  *\<cosmosdb_name >*, és  *\<primary_master_key >* helyőrzők.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

A Node.js-kódnak érheti el az alkalmazás setting `process.env.MONGODB_URI`, ahogy bármely környezeti változó elérésére. 

Nyissa meg a helyi MEAN.js tárház _config/env/production.js_ (nem _config/env/local-production.js_), amely éles környezet adott konfigurációval rendelkezik. Vegye figyelembe, hogy az alapértelmezett MEAN.js alkalmazás már használatára konfigurált a `MONGODB_URI` létrehozott környezeti változó.

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

Ha sikeres, és az alkalmazás automatikusan bejelentkezik a létrehozott felhasználót, majd az Azure-ban a MEAN.js alkalmazás rendelkezik MongoDB API Cosmos-adatbázis adatbázis-kapcsolata.

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Válassza ki **Admin > kezelése cikkek** egyes cikkek hozzáadni.

**Gratulálunk!** Adatalapú Node.js-alkalmazás az Azure App Service Linux futtatja.

## <a name="update-data-model-and-redeploy"></a>Frissítés adatmodell, és helyezze üzembe újra

Ebben a lépésben módosítsa a `article` adatok modell, és közzéteheti a módosítást az Azure-bA.

### <a name="update-the-data-model"></a>Az adatmodell frissítése

Nyissa meg a helyi MEAN.js tárház _modules/articles/server/models/article.server.model.js_.

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
gulp prod
NODE_ENV=production node server.js
```

> [!NOTE]
> Ne feledje, hogy a _config/env/production.js_ vissza lett állítva, és a `MONGODB_URI` környezeti változó csak akkor állítható be, az Azure web app alkalmazásban, és nem a helyi számítógépen. A konfigurációs fájl tekinti meg, ha látnia, hogy az üzemi configuration értéke alapértelmezés szerint a helyi MongoDB-adatbázist használja. Ezzel biztosíthatja, hogy nem touch termelési adatok helyben a kódmódosításokat tesztelésekor.

Navigáljon a `http://localhost:8443` a böngészőben, és győződjön meg arról, hogy be van jelentkezve.

Válassza ki **Admin > cikkek kezelése**, majd adja hozzá a cikk kiválasztásával a  **+**  gombra.

Megjelenik az új `Comment` szövegmező most.

![A hozzáadott megjegyzés mező cikkekhez](./media/tutorial-nodejs-mongodb-app/added-comment-field.png)

A terminálban leállításához írja be a Node.js `Ctrl+C`.

### <a name="publish-changes-to-azure"></a>Változások közzétételére Azure

A Git a változtatások véglegesítése a határidő, majd küldje le a kód módosításokat az Azure-bA.

```bash
git commit -am "added article comment"
git push azure master
```

Egyszer a `git push` befejezése, navigáljon az Azure web app és az új funkciók kipróbálásához.

![Az Azure-bA közzétett modell és adatbázis módosítása](media/tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Ha korábban hozzáadott olyan cikkek, továbbra is láthatja azokat. A Cosmos DB meglévő adatait nem elvész. A Adatséma is, a frissítések és a meglévő adatok sértetlenek.

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazásban kezelése

Lépjen a [Azure-portálon](https://portal.azure.com) létrehozott webalkalmazás megjelenítéséhez.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-nodejs-mongodb-app/access-portal.png)

Alapértelmezés szerint a portál megjeleníti a webalkalmazás **áttekintése** lap. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A lap bal oldalán a lapok megnyithatja a különböző konfigurációs lapok megjelenítése.

![Az App Service lap az Azure Portalon](./media/tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

Hogy mit tudott:

> [!div class="checklist"]
> * A MongoDB API használata az Azure-ban CosmosDB adatbázis létrehozása
> * A Node.js alkalmazás csatlakoztatása a mongodb-Protokolltámogatással
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Stream naplók az Azure-ból a Terminálszolgáltatások számára
> * Felügyelheti az alkalmazást az Azure-portálon

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)
