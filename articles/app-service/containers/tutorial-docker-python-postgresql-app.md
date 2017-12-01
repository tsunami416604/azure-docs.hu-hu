---
title: "Build egy Docker Python és PostgreSQL webalkalmazást az Azure-ban |} Microsoft Docs"
description: "Útmutató a Docker Python alkalmazást az Azure PostgreSQL adatbázis-kapcsolat használata."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2017
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 161d9fda75caa7836e012e6e1ff79df576281137
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Az Azure-ban Docker Python és PostgreSQL webalkalmazás létrehozása

Webes alkalmazás a tárolók egy jól skálázható, önálló javítási webhelyszolgáltató biztosít. Ez az oktatóanyag bemutatja, hogyan alapvető Docker Python-webalkalmazás létrehozása az Azure-ban. Ez az alkalmazás kapcsolódni egy PostgreSQL-adatbázisban. Amikor elkészült, a Python Flask-alkalmazás egy Docker-tároló belül futó van [App Service Linux](app-service-linux-intro.md).

![Docker Python Flask-alkalmazás az App Service Linux rendszeren](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

A macOS hajtsa végre az alábbi lépéseket. Linux és Windows utasításokat legtöbbször azonos, de az eltéréseket nem részletes ebben az oktatóanyagban.
 
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Pythont](https://www.python.org/downloads/)
1. [Telepítheti és futtathatja PostgreSQL](https://www.postgresql.org/download/)
1. [Telepítse a Docker Community Edition](https://www.docker.com/community-edition)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Adatbázis létrehozása és helyi PostgreSQL-telepítés sikerességének ellenőrzése

Nyissa meg a terminálablakot, és futtassa `psql postgres` a helyi PostgreSQL-kiszolgálóhoz való csatlakozáshoz.

```bash
psql postgres
```

Ha a kapcsolódás sikeres, a rendszer fut a PostgreSQL-adatbázisból. Ha nem, győződjön meg arról, hogy a helyi PostgresQL-adatbázisban van-e indítva a következő lépéseket követve [letölti - PostgreSQL Core terjesztési](https://www.postgresql.org/download/).

Adatbázis létrehozása *eventregistration* és nevű külön adatbázis-felhasználó beállítása *manager* jelszóval *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Típus *\q* való kilépéshez a PostgreSQL-ügyfél. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Helyi Python Flask-alkalmazás létrehozása

Ebben a lépésben beállíthatja a helyi Python Flask-projektet.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyissa meg a terminálablakot, és `CD` egy működő könyvtárba.

A minta-tárház klónozása, és folytassa a következő parancsokat a *0,1-initialapp* kiadási.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Ez a minta-tárház tartalmaz egy [Flask](http://flask.pocoo.org/) alkalmazás. 

### <a name="run-the-application"></a>Az alkalmazás futtatása

> [!NOTE] 
> Egy későbbi lépésben épület egy Docker-tároló, hogy az éles adatbázist használ, egyszerűsíti a folyamatot.

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Az alkalmazás teljesen betöltődik, lásd a következő üzenet hasonló:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Egy böngészőben nyissa meg a `http://127.0.0.1:5000` oldalt. Kattintson a **regisztrálni!** és tesztfelhasználó létrehozása.

![Helyileg futó Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/local-app.png)

A Flask mintaalkalmazás felhasználói adatot tárol az adatbázisban. Ha a felhasználó regisztrálása sikeres, az alkalmazás a helyi PostgreSQL-adatbázisból adatokat ír.

A Flask kiszolgáló bármikor leállításához írja be a Terminálszolgáltatások Ctrl + C. 

## <a name="create-a-production-postgresql-database"></a>Hozzon létre egy PostgreSQL-adatbázisban

Ebben a lépésben létrehoz egy PostgreSQL-adatbázisban az Azure-ban. Az alkalmazás telepítésekor az Azure-ba, használja a felhő adatbázis.

### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Most kívánja használni az Azure CLI 2.0 létrehozása a Python-alkalmazás a tárolók a webalkalmazás üzemeltetéséhez szükséges erőforrásokat.  Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#az_login) paranccsal, és kövesse a képernyőn látható utasításokat.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [erőforráscsoportot](../../azure-resource-manager/resource-group-overview.md) az [az group create](/cli/azure/group#az_group_create) paranccsal.

[!INCLUDE [Resource group intro](../../../includes/resource-group.md)]

A következő példa egy erőforráscsoportot az USA nyugati régiója régióban hoz létre:

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Használja a [az App Service lista-helyek](/cli/azure/appservice#az_appservice_list_locations) lista elérhető helyről az Azure parancssori felület parancsot.

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL-kiszolgáló ezzel a [az postgres kiszolgáló létrehozni](/cli/azure/postgres/server#az_postgres_server_create) parancsot.

Az alábbi parancs helyettesítse be egy egyedi kiszolgálónevet számára a  *\<postgresql_name >* helyőrző és egy felhasználói nevet a  *\<admin_username >* helyőrző. A kiszolgáló nevét használja a PostgreSQL-végpontot részeként (`https://<postgresql_name>.postgres.database.azure.com`), így a nevének egyedinek kell lennie az Azure-ban minden kiszolgálóra. A felhasználónév megadása a kezdeti adatbázis rendszergazdai felhasználói fiókhoz. Válassza ki a felhasználó jelszavát kéri.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>
```

PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis létrehozásakor az Azure parancssori felület kapcsolatos adatokat jeleníti meg az alábbi példához hasonló:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Hozzon létre egy tűzfalszabályt PostgreSQL-kiszolgáló Azure-adatbázis

A következő parancsot az Azure parancssori felület összes IP-címet az adatbázis hozzáférést.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Az Azure parancssori felület megerősíti, hogy a kimenet az alábbihoz hasonló tűzfalszabály létrehozása:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Kapcsolódás az adatbázishoz a Python Flask-alkalmazás

Ebben a lépésben a Python Flask mintaalkalmazást PostgreSQL-kiszolgáló létrehozta az Azure-adatbázishoz való csatlakozáshoz.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Hozzon létre egy üres adatbázist, és állítson be egy új adatbázis-alkalmazás felhasználói

Hozzon létre egy adatbázis-felhasználó csak egy önálló adatbázis elérésére. Ezek a hitelesítő adatok segítségével elkerülése érdekében adjon az alkalmazás teljes hozzáférést a kiszolgáló.

Kapcsolódni az adatbázishoz (felszólítja a rendszergazdai jelszavát).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Az adatbázis és a felhasználó létrehozása a PostgreSQL parancssori.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Típus *\q* való kilépéshez a PostgreSQL-ügyfél.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Az alkalmazás helyileg a Azure PostgreSQL-adatbázison tesztelése

Visszalépés most a *app* mappa a klónozott Github tárház, futtathatja a Python Flask-alkalmazás az adatbázis környezeti változók frissítésével.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Az alkalmazás teljesen betöltődik, lásd a következő üzenet hasonló:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Nyissa meg a böngészőben http://127.0.0.1:5000. Kattintson a **regisztrálni!** és egy tesztelési regisztráció létrehozása. Adatokat ír az adatbázisba az Azure-ban most.

![Helyileg futó Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Futtatni az alkalmazást egy Docker-tároló

A Docker tároló lemezképet létre.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker visszaigazolja sikeresen létrejött a tároló.

```bash
Successfully built 7548f983a36b
```

Adatbázis környezeti változók hozzá egy környezeti változó fájl *db.env*. Az alkalmazás PostgreSQL éles adatbázis az Azure-adatbázishoz csatlakozik.

```text
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Futtassa az alkalmazást a belül a Docker-tároló. A következő parancsot a környezeti változó fájlt adja meg, és helyi port 5000 van leképezve az alapértelmezett Flask 5000-es port.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

A kimeneti mi korábban látott hasonlít. A kezdeti adatbázis az áttelepítés azonban már nem hajtható végre, és ezért a rendszer kihagyja.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Az adatbázis már tartalmaz a korábban létrehozott regisztrációját.

![Docker tároló-alapú Python Flask helyileg futó alkalmazásba](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Töltse fel a Docker-tároló egy tároló beállításjegyzék

Ebben a lépésben a Docker-tároló egy tároló beállításjegyzék feltöltése. Használata Azure tároló beállításjegyzék, de más például Docker Hub népszerű ők is használhatja.

### <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Cserélje le az alábbi parancs segítségével hozza létre a tároló beállításkulcs  *\<registry_name >* az Ön által választott egyedi Azure tároló beállításjegyzék néven.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Kimenet

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>A beállításjegyzék hitelesítő adatokat kérdez le, és húzza a Docker képek beolvasása

Beállításjegyzék hitelesítő adatok megjelenítéséhez engedélyezéséhez először rendszergazdai módot.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Megjelenik a két jelszó. Jegyezze meg a felhasználónevet és az első jelszót.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Töltse fel a Docker-tároló Azure tároló beállításjegyzék

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Az Azure-bA a Docker Python Flask-alkalmazás központi telepítése

Ebben a lépésben alkalmazást telepít központilag a Docker tároló-alapú Python Flask Azure App Service szolgáltatásban.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) paranccsal.

[!INCLUDE [app-service-plan](../../../includes/app-service-plan-linux.md)]

Az alábbi példa létrehoz egy Linux-alapú App Service-csomag nevű *myAppServicePlan* használatával a S1 árképzési szintjüket:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Az App Service-csomag létrehozásakor az Azure parancssori felület kapcsolatos adatokat jeleníti meg az alábbi példához hasonló:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A webalkalmazás létrehozása a *myAppServicePlan* az App Service-csomag a [az webalkalmazás létrehozása](/cli/azure/webapp#az_webapp_create) parancsot.

A webes alkalmazás lehetővé teszi az üzemeltető adható meg a kód telepítésére, és biztosítja, hogy a telepített alkalmazás megtekintése egy URL-CÍMÉT. A webalkalmazás létrehozásához használja.

A következő parancsban cserélje le a  *\<alkalmazás_neve >* helyőrzőt egy egyedi alkalmazásnevet. Ez a név része a web app alkalmazásban alapértelmezett URL-CÍMÉT, a nevének egyedinek kell lennie az Azure App Service-ben minden alkalmazások között.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Konfigurálja az adatbázis környezeti változók

Az oktatóanyag korábbi részében definiált környezeti változók a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Az App Service-ben, a környezeti változók beállítása _Alkalmazásbeállítások_ használatával a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) parancsot.

A következő példa az adatbázis-kapcsolat adatai Alkalmazásbeállítások adja meg. Is használja a *PORT* PORT 5000-leképezés változót a Docker-tároló, a 80-as PORT HTTP-forgalom fogadására.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Docker-tároló telepítés konfigurálása

App Service automatikusan letölteni és futtatni egy Docker-tároló.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Amikor a Docker-tároló frissítésére, vagy módosítsa a beállításokat, indítsa újra az alkalmazást. Újraindítása biztosítja, hogy minden beállítások érvényesek, és a legújabb tároló van lekért a beállításjegyzékből.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Keresse meg az Azure-webalkalmazásban 

Tallózással keresse meg a telepített webalkalmazás webböngészővel. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> A webalkalmazás betölteni, mert a tároló rendelkezik letöltődnek és a tároló konfigurációjának módosítása után elindult hosszabb időt vesz igénybe.

Az éles Azure-adatbázishoz az előző lépésben mentett korábban regisztrált vendégek láthatja.

![Docker tároló-alapú Python Flask helyileg futó alkalmazásba](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Gratulálunk!** Futtatja egy Docker tároló-alapú Python Flask-alkalmazás az Azure App Service-ben.

## <a name="update-data-model-and-redeploy"></a>Frissítés adatmodell, és helyezze üzembe újra

Ebben a lépésben hozzá a résztvevők száma minden eseményregisztráció vendég modell frissítésével.

Tekintse meg a *0,2-áttelepítési* kiadás az alábbi git-paranccsal:

```bash
git checkout tags/0.2-migration
```

Ebben a kiadásban a nézeteket, a tartományvezérlőket és a modell már elvégezték a szükséges módosításokat. Is keresztül létrehozott adatbázis áttelepítés *alembic* (`flask db migrate`). A következő git-parancs használatával végrehajtott valamennyi módosítást látható:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>A módosításokat a helyi tesztelése

A következő parancsokat a módosítások ellenőrzéséhez helyileg a flask kiszolgáló futtatásával.

Mac / Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Nyissa meg a böngészőben, hogy a változások http://127.0.0.1:5000. Hozzon létre egy teszt regisztrációs.

![Docker tároló-alapú Python Flask helyileg futó alkalmazásba](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Változások közzétételére Azure

Az új docker-lemezkép, hogy a tároló beállításjegyzék, és indítsa újra az alkalmazást.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Keresse meg az Azure-webalkalmazásban, és újra próbálja ki az új funkciókat. Hozzon létre egy másik eseményregisztráció.

```bash 
http://<app_name>.azurewebsites.net 
```

![Docker Python Flask-alkalmazás az Azure App Service-ben](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazásban kezelése

Lépjen a [Azure-portálon](https://portal.azure.com) létrehozott webalkalmazás megjelenítéséhez.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Alapértelmezés szerint a portál megjeleníti a webalkalmazás **áttekintése** lap. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. A lap bal oldalán a lapok megnyithatja a különböző konfigurációs lapok megjelenítése.

![Az App Service lap az Azure Portalon](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)
