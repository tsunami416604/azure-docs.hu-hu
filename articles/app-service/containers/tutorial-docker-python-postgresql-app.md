---
title: Python- és PostgreSQL-webalkalmazás létrehozása az Azure App Service-ben | Microsoft Docs
description: Ismerje meg, hogyan futtathat egy PostgreSQL-adatbáziskapcsolattal rendelkező, adatvezérelt Python-alkalmazást az Azure-ban.
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 20b549914daf71c0d23235b5c20ebb6f14367471
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172034"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Docker Python- és PostgreSQL-webalkalmazás létrehozása az Azure-ban

A Web App for Containers egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy adatvezérelt Python-webalkalmazást PostgreSQL-háttéradatbázis segítségével. Az oktatóanyag eredménye egy, a [Linux App Service-ben](app-service-linux-intro.md) lévő Docker-tárolóban futó Python Flask-alkalmazás lesz.

![Docker Python Flask-alkalmazás a Linux App Service-ben](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Az alkalmazás kezelése az Azure Portalon

A cikk lépései macOS rendszerre vonatkoznak. Linux és Windows rendszeren a legtöbb esetben ugyanezek az utasítások érvényesek, az oktatóanyag azonban nem tér ki az eltérésekkel kapcsolatos részletekre.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Pythont](https://www.python.org/downloads/)
1. [A PostgreSQL telepítése és futtatása](https://www.postgresql.org/download/)
1. [A Docker Community Edition telepítése](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>A helyi PostgreSQL-telepítés tesztelése és egy adatbázis létrehozása

A helyi PostgreSQL-kiszolgálóhoz való csatlakozáshoz futtassa egy helyi terminálablakban a `psql` parancsot.

```bash
sudo -u postgres psql
```

Ha a kapcsolat létrejött, a PostgreSQL-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadás](https://www.postgresql.org/download/) szakaszban ismertetett lépéseket követve indítsa el a helyi PostgreSQL-adatbázist.

Hozzon létre egy adatbázist *eventregistration* néven, majd hozzon létre egy adatbázis-felhasználót *manager* néven és a *supersecretpass* jelszóval.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot. 

<a name="step2"></a>

## <a name="create-local-python-app"></a>Helyi Python-alkalmazás létrehozása

Ebben a lépésben beállítjuk a helyi Python Flask-projektet.

### <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Nyissa meg a terminálablakot, és a `CD` paranccsal hozzon létre egy munkakönyvtárat.

Az alábbi parancsok futtatásával klónozza a mintaadattárat, és lépjen a *0.1-initialapp* kiadásra.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

A mintaadattár tartalmaz egy [Flask](http://flask.pocoo.org/)-alkalmazást. 

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

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

Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson a **Register!** (Regisztrálás) gombra, és hozzon létre egy tesztfelhasználót.

![Helyileg futó Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/local-app.png)

A Flask-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. A felhasználó sikeres regisztrálása esetén az alkalmazás a helyi PostgreSQL-adatbázisba írja az adatokat.

Ha bármikor le szeretné állítani a Flask-kiszolgálót, nyomja le a Ctrl+C billentyűparancsot a terminálban. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Éles PostgreSQL-adatbázis létrehozása

Ebben a lépésben egy PostgreSQL-adatbázist hozunk létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL-kiszolgálót a Cloud Shellben az [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) paranccsal.

Az alábbi példaparancsban a *\<postgresql_name>* helyett írjon be egy egyedi kiszolgálónevet, az *\<admin_username>* és az *\<admin_password>* helyett pedig a kívánt felhasználói hitelesítő adatokat. A kiszolgálónév a postgreSQL-végpont (`https://<postgresql_name>.postgres.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban. A felhasználói hitelesítő adatok az adatbázis-rendszergazdai felhasználói fiókhoz szükségesek. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

Az Azure Database for PostgreSQL-kiszolgáló létrehozását követően az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "<admin_username>",
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

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>Tűzfalszabály létrehozása a PostgreSQL-kiszolgálóhoz

A Cloud Shellben futtassa a következő Azure CLI-parancsot, hogy engedélyezze az adatbázishoz való hozzáférést minden IP-címről. Ha a kezdő IP-cím és a záró IP-cím is `0.0.0.0` értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

Ahhoz, hogy helyi számítógépről érhesse el az adatbázist, futtassa újra a parancsot a Cloud Shellben, és a *\<you_ip_address>* helyére írja be a [helyi IPv4 IP-címet](https://whatismyipaddress.com/). 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python-alkalmazás csatlakoztatása éles adatbázishoz

Ebben a lépésben a Flask-mintaalkalmazást a létrehozott Azure Database for PostgreSQL-kiszolgálóhoz csatlakoztatjuk.

### <a name="create-empty-database-and-user-access"></a>Üres adatbázis és felhasználói hozzáférés létrehozása

A Cloud Shellben csatlakozzon az adatbázishoz a `psql` parancs futtatásával. Ha a rendszer a rendszergazdai jelszó megadására kéri, használja az [Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz](#create-an-azure-database-for-postgresql-server) részben megadott jelszót.

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Hozza létre az adatbázist és a felhasználót a PostgreSQL parancssori felületén.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

### <a name="test-app-connectivity-to-production-database"></a>Az alkalmazás és az éles adatbázis közötti kapcsolat ellenőrzése

A helyi terminálablakba visszalépve futtassa a következő parancsokat a Flask-adatbázis migrálása és a Flask-kiszolgáló futtatásához.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Egy böngészőben nyissa meg a http://localhost:5000 oldalt. Kattintson a **Register!** (Regisztrálás) gombra, és hozzon létre egy tesztregisztrációt. Most az Azure-ban lévő adatbázisba írunk adatokat.

![Helyileg futó Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>Alkalmazás feltöltése tárolóregisztrációs adatbázisba

Ebben a lépésben egy Docker-rendszerképet hoz létre, majd feltölti az Azure Container Registrybe. Olyan népszerű tárolóregisztrációs adatbázisokat is használhat, mint a Docker Hub.

### <a name="build-the-docker-image-and-test-it"></a>A Docker-rendszerkép összeállítása és tesztelése

A helyi terminálablakban állítsa össze a Docker-rendszerképet.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

A Docker megjelenít egy megerősítő üzenetet, ha sikeresen létrehozta a rendszerképet.

```bash
Successfully built 7548f983a36b
```

Az adattár gyökerében adjon hozzá egy környezetiváltozó-fájlt _db.env_ néven, és vegye fel bele az alábbi adatbázis-környezeti változókat. Az alkalmazás az éles Azure Database for PostgreSQL-adatbázishoz csatlakozik.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Futtassa a rendszerképet helyileg egy Docker-tárolóban. Az alábbi parancs megadja a környezetiváltozó-fájlt, és az alapértelmezett 5000-es Flask-portot leképezi a helyi 5000-es portra.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

A kimenet a korábbihoz hasonló. A kezdeti adatbázis-migrálást azonban már nem szükséges végrehajtani, ezért azt kihagyjuk.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Az adatbázis már tartalmazza a korábban létrehozott regisztrációt.

![Helyileg futó, Docker-tárolóalapú Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/local-docker.png)

Most, hogy ellenőrizte, a tároló helyileg működik-e, törölje a _db.env_ fájlt. Az Azure App Service-ben az alkalmazásbeállítások segítségével fogjuk meghatározni a környezeti változókat.  

### <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

A Cloud Shellben hozzon létre egy tárolóregisztrációs adatbázist az Azure Container Registryben a következő parancs használatával. A *\<registry_name* helyére írjon be egy egyedi nevet a tárolóregisztrációs adatbázis számára.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>A tárolóregisztrációs adatbázis hitelesítő adatainak lekérése

A Cloud Shellben futtassa a következő parancsokat a tárolóregisztrációs adatbázis hitelesítő adatainak lekéréséhez. Ezek a rendszerképek leküldéséhez és lekéréséhez szükségesek.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

A kimenetben két jelszó jelenik meg. Jegyezze fel a felhasználónevet (alapértelmezés szerint ez a tárolóregisztrációs adatbázis neve) és az első jelszót.

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

### <a name="upload-docker-image-to-registry"></a>A Docker-rendszerkép feltöltése a tárolóregisztrációs adatbázisba

A helyi terminálablakból jelentkezzen be az új tárolóregisztrációs adatbázisba a `docker` segítségével. Amikor a rendszer kéri, adja meg a lekért jelszót.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Küldje le a Docker-rendszerképet a tárolóregisztrációs adatbázisba.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>Webalkalmazás létrehozása feltöltött rendszerképpel

Ebben a lépésben létrehoz egy alkalmazást az Azure App Service-ben, majd úgy konfigurálja, hogy az a feltöltött Docker-rendszerképet használja az Azure Container Registryben.

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal hozzon létre egy webalkalmazást a *myAppServicePlan* App Service-csomagban.

Az alábbi parancsban cserélje le az *\<app_name>* helyőrzőt egy egyedi alkalmazásnévre. Ez a név a webalkalmazás alapértelmezett URL-címének részét képezi majd, így egyedi alkalmazásnévnek kell lennie a teljes Azure App Service-ben.

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
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

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az oktatóanyag korábbi részében meghatároztunk környezeti változókat a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ adhatja meg az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) paranccsal.

Az alábbi példa az adatbázis kapcsolati adatait alkalmazásbeállításokként adja meg. Emellett a *WEBSITES_PORT* változó értékeként az 5000-es tárolóport van megadva, ami lehetővé teszi a tároló számára HTTP-forgalom fogadását a 80-as porton.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>Egyéni tároló üzembe helyezésének konfigurálása

Bár már megadta a tárolórendszerkép nevét, meg kell adnia a tárolóregisztrációs adatbázis egyéni URL-címét és a felhasználói hitelesítő adatokat is. A Cloud Shellben futtassa az [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) parancsot.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Indítsa újra az alkalmazást a Cloud Shellben. Az újraindítás biztosítja, hogy minden beállítás alkalmazva lesz, és a rendszer a legújabb tárolót kéri le a tárolójegyzékből.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése 

Keresse meg az üzembe helyezett webalkalmazást. 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> A webalkalmazás elindítása hosszabb időt vesz igénybe, mivel a tárolót le kell tölteni és el kell indítani az alkalmazás első igénylésekor. Ha először hosszú várakozási idő után hibát észlel, frissítse a lapot.

Láthatja az előzőleg regisztrált vendégeket, akik az előző lépésben lettek mentve az éles Azure-adatbázisban.

![Helyileg futó, Docker-tárolóalapú Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Gratulálunk!** Egy Python-alkalmazást futtat a Web App for Containersben.

## <a name="update-data-model-and-redeploy"></a>Az adatmodell frissítése és ismételt üzembe helyezése

Ebben a lépésben adott számú résztvevőt ad hozzá az egyes eseményregisztrációkhoz a `Guest` modell frissítésével.

A helyi terminálablakban vegye ki a *0.2-migration* kiadást a következő git-paranccsal:

```bash
git checkout tags/0.2-migration
```

Ebben a kiadásban már végre lettek hajtva a szükséges módosítások a modellen, a nézeteken és a vezérlőkön. Emellett tartalmaz egy, az *alembic* (`flask db migrate`) használatával létrehozott adatbázismigrálást is. Az alábbi git-paranccsal áttekintheti a módosításokat:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Módosítások helyi tesztelése

A helyi terminálablakban az alábbi parancsok futtatásával helyben tesztelheti a módosításokat a Flask-kiszolgáló futtatásával.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

A módosítások megtekintéséhez a böngészőben keresse fel a http://localhost:5000 címet. Hozzon létre egy tesztregisztrációt.

![Helyileg futó, Docker-tárolóalapú Python Flask-alkalmazás](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A helyi terminálablakban állítsa össze az új Docker-rendszerképet, és küldje le a tárolóregisztrációs adatbázisba.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

A Cloud Shellben indítsa újra az alkalmazást, hogy a rendszer biztosan a legújabb tárolót kérje le a tárolóregisztrációs adatbázisból.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Nyissa meg az Azure-webalkalmazást, és próbálja ki ismét az új funkciót. Hozzon létre egy másik eseményregisztrációt.

```bash 
http://<app_name>.azurewebsites.net 
```

![Docker Python Flask-alkalmazás az Azure App Service-ben](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazás kezelése

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Alapértelmezés szerint a portálon a webalkalmazás **Áttekintés** oldala jelenik meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>További lépések

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)
