---
title: 'Oktatóanyag: Linux Python-alkalmazás és Postgre'
description: Megtudhatja, hogyan szerezhet be egy Azure App Serviceon működő Linux Python-alkalmazást, amely egy Azure-beli PostgreSQL-adatbázishoz csatlakozik. Ebben az oktatóanyagban a Django van használatban.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523934"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Python-(Django-) webalkalmazás futtatása a PostgreSQL-sel Azure App Service

Az [Azure App Service](app-service-linux-intro.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy adatvezérelt Python Django-webalkalmazást egy Azure Database for PostgreSQL-adatbázishoz, és hogyan telepítheti és futtathatja az alkalmazást Azure App Serviceon.

![Python Django-webalkalmazás a Azure App Serviceban](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hozzon létre egy Azure Database for PostgreSQL adatbázist, és kapcsolódjon hozzá az alkalmazáshoz
> * A webalkalmazás üzembe helyezése Azure App Service
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése a Azure Portalban

A cikkben ismertetett lépéseket macOS, Linux vagy Windows rendszeren is követheti. A lépések a legtöbb esetben hasonlóak, bár ebben az oktatóanyagban nem részletezik a különbségeket. Az alábbi példák többsége egy `bash` terminál-ablakot használ Linuxon. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elindítása előtt:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Telepítse a [Git](https://git-scm.com/) szoftvert.
- Telepítse a [Python 3](https://www.python.org/downloads/)programot.
- A [PostgreSQL](https://www.postgresql.org/download/)telepítése és futtatása.

## <a name="test-postgresql-installation-and-create-a-database"></a>A PostgreSQL-telepítés tesztelése és adatbázis létrehozása

Először kapcsolódjon a helyi PostgreSQL-kiszolgálóhoz, és hozzon létre egy adatbázist: 

Egy helyi terminál ablakban futtassa `psql` a helyi PostgreSQL-kiszolgálóhoz való kapcsolódáshoz a beépített `postgres` felhasználóként.

```bash
sudo su - postgres
psql
```
vagy
```PowerShell
psql -U postgres
```

Ha a kapcsolat létrejött, a PostgreSQL-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadással](https://www.postgresql.org/download/) foglalkozó szakaszban ismertetett, az operációs rendszerére vonatkozó utasításokat követve indítsa el a helyi PostgreSQL-adatbázist.

Hozzon létre egy *pollsdb*nevű új adatbázist, és állítson be egy *Manager* nevű adatbázis-felhasználót a Password *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>A helyi Python-alkalmazás létrehozása és futtatása

Ezután állítsa be és futtassa a Python Django-webalkalmazást.

A [djangoapp](https://github.com/Azure-Samples/djangoapp) -minta adattár tartalmazza az adatvezérelt [Django](https://www.djangoproject.com/) -lekérdezési alkalmazást, amelyet az [első Django-alkalmazásnak](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) a Django dokumentációjában való megírásával talál.

### <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Egy terminál ablakban futtassa a következő parancsokat a minta alkalmazás-tárház klónozásához, és váltson az új munkakönyvtárra:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>A Python virtuális környezet konfigurálása

Hozzon létre és aktiváljan egy Python virtuális környezetet az alkalmazás futtatásához.

```bash
python3 -m venv venv
source venv/bin/activate
```
vagy
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

A `venv` környezetben futtassa a *env.sh* vagy az *env. ps1* parancsot a *azuresite/Settings. a. a.*

```bash
source ./env.sh
```
vagy
```PowerShell
.\env.ps1
```

Telepítse a szükséges csomagokat a *követelmények. txt*fájlból, futtassa a [Django-áttelepítést](https://docs.djangoproject.com/en/2.1/topics/migrations/), és [hozzon létre egy rendszergazda felhasználót](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>A webalkalmazás futtatása

A rendszergazda felhasználó létrehozása után futtassa a Django-kiszolgálót.

```bash
python manage.py runserver
```

Ha a Django-webalkalmazás teljesen be van töltve, az a következő üzenethez hasonló módon tér vissza:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Nyissa meg a *http:\//localhost: 8000* -et egy böngészőben. Ekkor az üzenet **nem érhető**el. 

Lépjen a *http:\//localhost: 8000/admin* lehetőségre, és jelentkezzen be az utolsó lépésben létrehozott rendszergazdai felhasználó használatával. Válassza a **Hozzáadás** a **kérdések**mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

![Python Django-alkalmazás futtatása helyileg App Services](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Lépjen a *http:\//localhost: 8000* -re a lekérdezési kérdés megtekintéséhez és a kérdés megválaszolásához. A helyi Django-minta alkalmazás a helyi PostgreSQL-adatbázisba írja és tárolja a felhasználói adatot.

A Django-kiszolgáló leállításához írja be a CTRL + C billentyűkombinációt a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A cikk hátralévő lépéseinek többsége az Azure CLI-parancsokat használja a Azure Cloud Shellban. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Azure Database for PostgreSQL létrehozása és kapcsolódás

Ebben a szakaszban egy Azure Database for PostgreSQL-kiszolgálót és egy adatbázist hoz létre, és a webalkalmazást hozzá kell kötnie. Ha Azure App Servicere telepíti a webalkalmazást, az alkalmazás ezt a felhőalapú adatbázist használja. 

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Létrehozhat egy új erőforráscsoportot a Azure Database for PostgreSQL-kiszolgálóhoz, vagy használhat meglévő erőforráscsoportot is. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL-kiszolgálót az az [postgres Server Create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) paranccsal a Cloud shell.

> [!NOTE]
> Azure Database for PostgreSQL-kiszolgáló létrehozása előtt győződjön meg arról, hogy melyik [számítási generáció](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) érhető el a régióban. Ha a régió nem támogatja a Gen4 hardvert, módosítsa a *-SKU-Name* parancsot a következő parancssorban a régiójában támogatott értékre, például Gen5. 

A következő parancsban cserélje le *\<PostgreSQL-name >* egyedi kiszolgálónévre. A kiszolgálónév része a PostgreSQL *-végpont https://\<PostgreSQL-name >. postgres. database. Azure. com*, így a névnek egyedinek kell lennie az Azure összes kiszolgálóján. 

Cserélje le *\<resourcegroup >* és *\<régiót >* a használni kívánt erőforráscsoport nevére és régiójára. *\<admin-username >* és *\<admin-password >* hozzon létre felhasználói hitelesítő adatokat az adatbázis-rendszergazdai fiókhoz. Ne feledje, hogy a *\<admin-username >* és *\<admin-password >* , hogy később jelentkezzen be a PostgreSQL-kiszolgálóra és-adatbázisokra.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Az Azure Database for PostgreSQL-kiszolgáló létrehozásakor az Azure CLI a következő példához hasonló JSON-kódot ad vissza:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Tűzfalszabályok létrehozása a Azure Database for PostgreSQL-kiszolgálóhoz

Futtassa az az [postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) parancsot az adatbázis Azure-erőforrásokból való elérésének engedélyezéséhez. Cserélje le a *\<PostgreSQL-name >* és a *\<resourcegroup nevet >* helyőrzőket az értékekre.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Az előző beállítás lehetővé teszi, hogy a hálózati kapcsolatok az Azure-hálózaton belül minden IP-címről elérhetők legyenek. Éles használatra a lehető legszigorúbb tűzfalszabályok konfigurálását kell végrehajtani, ha [csak az alkalmazás által használt kimenő IP-címeket engedélyezi](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Futtassa újra a `firewall-rule create` parancsot, hogy engedélyezze a hozzáférést a helyi számítógépről. Cserélje le *\<saját IP-címét >* a [helyi IPv4 IP-címére](https://www.whatsmyip.org/). Cserélje le a *\<PostgreSQL-name >* és a *\<resourcegroup-Name >* helyőrzőket a saját értékeire.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>A Azure Database for PostgreSQL-adatbázis létrehozása és kapcsolódás

A következő parancs futtatásával csatlakozhat a Azure Database for PostgreSQL-kiszolgálóhoz. Használja a saját *\<PostgreSQL-name >* és *\<admin-username >* , és jelentkezzen be a létrehozott jelszóval.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Akárcsak a helyi PostgreSQL-kiszolgálón, hozzon létre egy adatbázist és egy felhasználót a Azure Database for PostgreSQL-kiszolgálón:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Az ajánlott eljárás az, hogy a rendszergazda felhasználó helyett korlátozott engedélyekkel rendelkező adatbázis-felhasználókat hozzon létre bizonyos alkalmazásokhoz. A `manager` felhasználó *csak* a `pollsdb` adatbázishoz rendelkezik teljes jogosultsággal.

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Az alkalmazás kapcsolatának tesztelése az Azure PostgreSQL-adatbázissal

Szerkessze a helyi *env.sh* vagy *env. ps1* fájlt úgy, hogy az a Felhőbeli PostgreSQL-adatbázisra mutasson, ehhez cserélje *\<PostgreSQL-Name >t* a Azure Database for PostgreSQL-kiszolgáló nevére.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
vagy
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

A helyi terminál ablakának `venv` környezetében futtassa a szerkesztett *env.sh* vagy *env. ps1*parancsot. 
```bash
source ./env.sh
```
vagy
```PowerShell
.\env.ps1
```

Futtasson Django-áttelepítést az Azure Database-be, és hozzon létre egy rendszergazdai felhasználót.

```bash
python manage.py migrate
python manage.py createsuperuser
```

A rendszergazda felhasználó létrehozása után futtassa a Django-kiszolgálót.

```bash
python manage.py runserver
```

A böngészőben nyissa meg a *http:\//localhost: 8000*-et, és az üzenetben ne legyenek **elérhetők a lekérdezések** . 

Nyissa meg a *http:\//localhost: 8000/admin*, jelentkezzen be a létrehozott rendszergazda felhasználóval, és hozzon létre egy lekérdezési kérdést, mint korábban.

![Python Django-alkalmazás futtatása helyileg App Services](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Lépjen a *http:\//localhost: 8000* elemre, és tekintse meg a megjelenő lekérdezési kérdést. Az alkalmazás most már beírja az adatait a Azure Database for PostgreSQL adatbázisba.

A Django-kiszolgáló leállításához írja be a CTRL + C billentyűkombinációt a terminálon.

## <a name="deploy-the-web-app-to-azure-app-service"></a>A webalkalmazás üzembe helyezése Azure App Service

Ebben a lépésben üzembe helyezi a Azure Database for PostgreSQL adatbázishoz csatlakoztatott Python-alkalmazást a Azure App Service.

### <a name="configure-repository"></a>Az adattár konfigurálása

Mivel ez az oktatóanyag egy Django mintát használ, módosítania kell, és hozzá kell adnia néhány beállítást a *djangoapp/azuresite/Settings. file.* a fájlban, hogy működjön a Azure app Service. 

1. A Django érvényesíti a bejövő kérelmek `HTTP_HOST` fejlécét. Ahhoz, hogy a Django-webalkalmazás működjön a App Serviceban, hozzá kell adnia az alkalmazás teljes tartománynevét az engedélyezett gazdagépekhez. 
   
   Szerkessze a *azuresite/Settings.* a (z) `ALLOWED_HOSTS` sort a következőképpen:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. A Django nem támogatja [a statikus fájlok üzemi környezetben való kiszolgálását](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). Ebben az oktatóanyagban a [WhiteNoise](https://whitenoise.evans.io/en/stable/) használatával engedélyezheti a fájlok kiszolgálását. A WhiteNoise csomag már telepítve van a *követelmények. txt*fájllal. 
   
   Ha `django.middleware.security.SecurityMiddleware` a Django-t a WhiteNoise használatára szeretné konfigurálni, akkor a *azuresite/Settings. a. a.......* .... pontban keresse meg a `MIDDLEWARE` beállítást, és vegyen fel `whitenoise.middleware.WhiteNoiseMiddleware` A `MIDDLEWARE`-beállításnak a következőhöz hasonlóan kell kinéznie:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Adja hozzá a következő sorokat a *azuresite/Settings.* a (z)
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   A WhiteNoise konfigurálásával kapcsolatos további információkért tekintse meg a [WhiteNoise dokumentációját](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Az adatbázis-beállítások szakasz már a környezeti változók használatával kapcsolatos ajánlott biztonsági gyakorlatot követi. Az üzembe helyezéssel kapcsolatos javaslatokért tekintse meg a [Django dokumentációját: telepítési ellenőrzőlista](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Véglegesítse a módosításokat a *djangoapp* adattárának villájában:

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az oktatóanyag korábbi részében meghatároztunk környezeti változókat a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Azure App Service a környezeti változókat az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) paranccsal állíthatja be *alkalmazásbeállításokként*.

A Azure Cloud Shell a következő parancs futtatásával adja meg az adatbázis-kapcsolat részleteit az Alkalmazásbeállítások beállításnál. Cserélje le az *\<app-name >* , a *\<resourcegroup-Name >* és a *\<PostgreSQL-Name >t* a saját értékeire.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

További információ arról, hogy a kód hogyan fér hozzá ezekhez az alkalmazásokhoz, lásd: [hozzáférés környezeti változókhoz](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

A App Service központi telepítési kiszolgáló a *követelmények. txt fájlt* látja az adattár gyökerében, és `git push`után automatikusan futtatja a Python-csomagok felügyeletét.

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Tallózással keresse meg az üzembe helyezett alkalmazást URL-címmel *http:\//\<app-name >. azurewebsites. net*. Némi időt vesz igénybe, mivel a tárolót le kell tölteni és futtatni kell, amikor az alkalmazást első alkalommal kérik le. Ha a lapon túllépi az időkorlátot vagy egy hibaüzenet jelenik meg, várjon néhány percet, és frissítse az oldalt.

Ekkor meg kell jelennie a korábban létrehozott lekérdezési kérdéseknek. 

App Service észleli a Django-projektet a tárházban, ha egy *WSGI.py* -fájlt keres minden alkönyvtárban, amelyet a `manage.py startproject` alapértelmezés szerint hoz létre. Amikor App Service megkeresi a fájlt, betölti a Django-webalkalmazást. További információ a App Service Python-alkalmazások betöltéséről: a [beépített Python-rendszerkép konfigurálása](how-to-configure-python.md).

Nyissa meg a *http:\//\<app-name >. azurewebsites. net/admin nevet* , és jelentkezzen be a létrehozott rendszergazda felhasználó használatával. Ha szeretné, hozzon létre néhány lekérdezési kérdést.

![Python Django-alkalmazás futtatása App Services az Azure-ban](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Gratulálunk!** Python-(Django-) webalkalmazást futtat Azure App Service Linux rendszeren.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése a Azure Portalban

A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a létrehozott alkalmazást.

![Navigáljon a Python Django alkalmazáshoz a Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** lapját jeleníti meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt olyan alapszintű felügyeleti feladatokat is elvégezhet, mint a Tallózás, Leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django-alkalmazás kezelése a Azure Portal áttekintés lapján](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)
