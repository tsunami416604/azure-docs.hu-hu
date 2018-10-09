---
title: Python- és PostgreSQL-webalkalmazás létrehozása az Azure App Service-ben | Microsoft Docs
description: Ismerje meg, hogyan futtathat egy PostgreSQL-adatbáziskapcsolattal rendelkező, adatvezérelt Python-alkalmazást az Azure-ban.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435794"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Docker Python- és PostgreSQL-webalkalmazás létrehozása az Azure-ban

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan skálázható, önjavító webes üzemeltetési szolgáltatást nyújt. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy adatvezérelt Python-webalkalmazást PostgreSQL-háttéradatbázis segítségével. Az oktatóanyag eredménye egy, a Linux App Service-ben lévő Docker-tárolóban futó Python Flask-alkalmazás lesz.

![Docker Python Flask-alkalmazás a Linux App Service-ben](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók megtekintése
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Az alkalmazás kezelése az Azure Portalon

A cikk lépései macOS rendszerre vonatkoznak. Linux és Windows rendszeren a legtöbb esetben ugyanezek az utasítások érvényesek, az oktatóanyag azonban nem tér ki az eltérésekkel kapcsolatos részletekre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Pythont](https://www.python.org/downloads/)
1. [A PostgreSQL telepítése és futtatása](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>A helyi PostgreSQL-telepítés tesztelése és egy adatbázis létrehozása

A helyi PostgreSQL-kiszolgálóhoz való csatlakozáshoz futtassa egy helyi terminálablakban a `psql` parancsot.

```bash
sudo -u postgres psql postgres
```

Ha egy, az `unknown user: postgres` üzenethez hasonló hibaüzenetet kap, előfordulhat, hogy a PostgreSQL telepítése a bejelentkezett felhasználónévvel lett konfigurálva. Próbálkozzon inkább a következő paranccsal.

```bash
psql postgres
```

Ha a kapcsolat létrejött, a PostgreSQL-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadással](https://www.postgresql.org/download/) foglalkozó szakaszban ismertetett, az operációs rendszerére vonatkozó utasításokat követve indítsa el a helyi PostgreSQL-adatbázist.

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

Az alábbi parancsok futtatásával klónozza a mintatárházat.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

A mintaadattár tartalmaz egy [Flask](http://flask.pocoo.org/)-alkalmazást.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Helyileg futó Python Flask-alkalmazás](./media/tutorial-python-postgresql-app/local-app.png)

A Flask-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. A felhasználó sikeres regisztrálása esetén az alkalmazás a helyi PostgreSQL-adatbázisba írja az adatokat.

Ha bármikor le szeretné állítani a Flask-kiszolgálót, nyomja le a Ctrl+C billentyűparancsot a terminálban.

## <a name="create-a-production-postgresql-database"></a>Éles PostgreSQL-adatbázis létrehozása

Ebben a lépésben egy PostgreSQL-adatbázist hozunk létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL-kiszolgálót a Cloud Shellben az [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) paranccsal.

Az alábbi példaparancsban a *\<postgresql_name>* helyett írjon be egy egyedi kiszolgálónevet, az *\<admin_username>* és az *\<admin_password>* helyett pedig a kívánt felhasználói hitelesítő adatokat. A felhasználói hitelesítő adatokra az adatbázis rendszergazdai fiókjához van szükség. A kiszolgálónév a postgreSQL-végpont (`https://<postgresql_name>.postgres.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Jegyezze fel az \<admin_username > és \<admin_password > adatokat későbbi használat céljából. Szüksége lesz rájuk a Postgres-kiszolgálóra és annak adatbázisaiba való bejelentkezéshez.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Tűzfalszabályok létrehozása a PostgreSQL-kiszolgálóhoz

A Cloud Shellben futtassa a következő Azure CLI-parancsokat, hogy engedélyezze az adatbázishoz való hozzáférést az Azure-erőforrásokról.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Ez a beállítás lehetővé teszi a hálózati kapcsolatok létesítését az Azure hálózatán belül található minden IP-címről. Éles környezetben próbálja meg a lehető legszigorúbb tűzfalszabályokat konfigurálni úgy, hogy [kizárólag az alkalmazása által használt kimenő IP-címeket használja](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Ahhoz, hogy engedélyezze a helyi számítógépről történő hozzáférést, futtassa újra a parancsot a Cloud Shellben, és a *\<your_ip_address>* helyére írja be a [helyi IPv4 IP-címet](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python-alkalmazás csatlakoztatása éles adatbázishoz

Ebben a lépésben a Flask-mintaalkalmazást a létrehozott Azure Database for PostgreSQL-kiszolgálóhoz csatlakoztatjuk.

### <a name="create-empty-database-and-user-access"></a>Üres adatbázis és felhasználói hozzáférés létrehozása

A helyi terminálablakban csatlakozzon az adatbázishoz az alábbi parancs futtatásával. Ha a rendszer a rendszergazdai jelszó megadására kéri, használja az [Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz](#create-an-azure-database-for-postgresql-server) részben megadott jelszót.

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Csakúgy, mint a helyi Postgres-kiszolgáló esetében, az adatbázist és a felhasználót itt is az Azure Postgres-kiszolgálón hozza létre.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

> [!NOTE]
> Ajánlott eljárás, hogy bizonyos alkalmazásokhoz korlátozott hozzáféréssel rendelkező adatbázis-felhasználókat hozzon létre a rendszergazdai felhasználó használata helyett. Ebben a példában a `manager` felhasználó _kizárólag_ az `eventregistration` adatbázishoz rendelkezik teljes körű hozzáféréssel.

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

![Helyileg futó Python Flask-alkalmazás](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a Postgreshez csatlakoztatott Python-alkalmazást az Azure App Service-ben.

### <a name="configure-repository"></a>Az adattár konfigurálása

A Git üzembe helyező összetevője az App Service-ben elindítja a `pip` automatizálást, ha van egy _application.py_ elem az adattárgyökérben. Ebben az oktatóanyagban engedje, hogy az üzembe helyező összetevő futtassa Ön helyett az automatizálást. A helyi terminálablakban keresse meg az adattárgyökeret, hozzon létre egy tesztként funkcionáló _application.py_ elemet, majd véglegesítse a módosításokat.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az oktatóanyag korábbi részében meghatároztunk környezeti változókat a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ lehet beállítani az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs Cloud Shellben való használatával.

Az alábbi példa az adatbázis kapcsolati adatait alkalmazásbeállításokként adja meg. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
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

### <a name="configure-entry-point"></a>Belépési pont konfigurálása

Alapértelmezés szerint a beépített rendszerkép egy _wsgi.py_ vagy egy _application.py_ elemet keres a gyökérkönyvtárban belépési pontként, de az Ön belépési pontja az _app/app.py_. Az _application.py_, amelyet korábban hozzáadott, üres, és nincsen semmi szerepe.

A Cloud Shellben futtassa az [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) parancsot egy egyéni indítási szkript beállításához.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

A `--startup-file` paraméter egy egyéni parancs vagy az egyéni parancsot tartalmazó fájlhoz vezető elérési út értékét veszi fel. Az egyéni parancsot a következő formátumban kell megadni:

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

Az egyéni parancsban szükség van a `--chdir` elemre, ha a belépési pontja nem szerepel a gyökérkönyvtárban, és a `<subdirectory>` az alkönyvtár. A _.py_ fájl neve `<module>`, és az Ön webalkalmazását a `<variable>` változó képviseli a modulban.

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Keresse meg az üzembe helyezett webalkalmazást. Az elindítása hosszabb időt vesz igénybe, mivel a tárolót le kell tölteni és el kell indítani az alkalmazás első igénylésekor. Ha a lapon túllépi az időkorlátot vagy egy hibaüzenet jelenik meg, várjon néhány percet, és frissítse az oldalt.

```bash
http://<app_name>.azurewebsites.net
```

Láthatja az előzőleg regisztrált vendégeket, akik az előző lépésben lettek mentve az éles Azure-adatbázisban.

![Azure-ban futó Python Flask-alkalmazás](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Gratulálunk!** Egy Python-alkalmazást futtat a Linux rendszerhez készült App Service-ben.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

Mivel a Python-alkalmazás egy tárolóban fut, a Linux rendszeren futó App Service lehetővé teszi a tárolóból létrehozott konzolnaplókhoz való hozzáférést. A naplófájlok kikereséséhez látogasson el a következő URL-címre:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Két JSON-objektumot kell látnia, amelyek mindegyike rendelkezik egy `href` tulajdonsággal. Az egyik `href` a Docker-konzolnaplókra mutat (a vége `_docker.log`), a másik `href` pedig a Python-tárolóból létrehozott konzolnaplókhoz. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Másolja át a használni által kívánt `href` értékét egy böngészőablakba a naplók megkereséséhez. A naplók küldése nem streameléssel történik, így a művelet eredményére valamennyit várni kell. Az új naplók megtekintéséhez frissítse a böngészőlapot.

## <a name="update-data-model-and-redeploy"></a>Az adatmodell frissítése és ismételt üzembe helyezése

Ebben a lépésben hozzáadja a résztvevők számát ad hozzá az egyes eseményregisztrációkhoz a `Guest` modell frissítésével, majd újra üzembe helyezi a frissítést az Azure-ban.

A helyi terminálablakban vegye ki a fájlokat a `modelChange` ágból a következő git-paranccsal:

```bash
git checkout origin/modelChange -- .
```

Ez a kivételi művelet végrehajtja a szükséges módosításokat a modellen, a nézeteken és a vezérlőkön. Emellett tartalmaz egy, az *alembic* (`flask db migrate`) használatával létrehozott adatbázismigrálást is. Az alábbi git-paranccsal áttekintheti a módosításokat:

```bash
git diff master origin/modelChange
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

![Helyileg futó, Docker-tárolóalapú Python Flask-alkalmazás](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A helyi terminálablakban mentse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Nyissa meg az Azure-webalkalmazást, és próbálja ki ismét az új funkciót. Mindenképpen frissítse az oldalt.

```bash
http://<app_name>.azurewebsites.net
```

![Docker Python Flask-alkalmazás az Azure App Service-ben](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>A webalkalmazás kezelése az Azure Portalon

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-python-postgresql-app/app-resource.png)

Alapértelmezés szerint a portálon a webalkalmazás **Áttekintés** oldala jelenik meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók megtekintése
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Beépített Python-rendszerkép konfigurálása](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)

