---
title: 'Oktatóanyag: Linux Python alkalmazás postgre-vel'
description: Megtudhatja, hogyan szerezhet be egy Linux Python-alkalmazást az Azure App Service szolgáltatásban, amely kapcsolatban áll egy PostgreSQL-adatbázissal az Azure-ban. A Django az oktatóanyagban használatos.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523934"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Python (Django) webalkalmazás futtatása a PostgreSQL szolgáltatással az Azure App Service-ben

Az [Azure App Service](app-service-linux-intro.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan kapcsolhat össze egy adatközpontú Python Django webalkalmazást egy Azure Database for PostgreSQL-adatbázishoz, és hogyan telepítheti és futtathatja az alkalmazást az Azure App Service-en.

![Python Django webalkalmazás az Azure App Service-ben](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure-adatbázis létrehozása postgreSQL-adatbázishoz és webalkalmazás csatlakoztatása
> * A webalkalmazás üzembe helyezése az Azure App Service szolgáltatásban
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése az Azure Portalon

A cikk lépéseit macOS, Linux vagy Windows rendszeren követheti. A lépések hasonlóak a legtöbb esetben, bár a különbségek nem részletesebben az oktatóanyagban. Az alábbi példák `bash` többsége terminálablakot használ Linuxon. 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Telepítse [a Git](https://git-scm.com/)- alkalmazást.
- Telepítse [a Python 3-at](https://www.python.org/downloads/).
- Telepítse és futtassa [a PostgreSQL-t.](https://www.postgresql.org/download/)

## <a name="test-postgresql-installation-and-create-a-database"></a>Tesztelje a PostgreSQL telepítését és hozzon létre egy adatbázist

Először is, csatlakozzon a helyi PostgreSQL szerverhez, és hozzon létre egy adatbázist: 

A helyi terminálablakban `psql` `postgres` futtassa a helyi PostgreSQL-kiszolgálóhoz való csatlakozáshoz beépített felhasználóként való csatlakozáshoz.

```bash
sudo su - postgres
psql
```
vagy
```PowerShell
psql -U postgres
```

Ha a kapcsolat létrejött, a PostgreSQL-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadással](https://www.postgresql.org/download/) foglalkozó szakaszban ismertetett, az operációs rendszerére vonatkozó utasításokat követve indítsa el a helyi PostgreSQL-adatbázist.

Hozzon létre egy új adatbázisnevű *pollsdb*, és hozzon létre egy adatbázis felhasználó neve *manager* jelszóval *szupertitkos pass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>A helyi Python-alkalmazás létrehozása és futtatása

Ezután állítsa be és futtassa a minta Python Django webalkalmazást.

A [djangoapp](https://github.com/Azure-Samples/djangoapp) mintatár tartalmazza az adatközpontú [Django](https://www.djangoproject.com/) szavazási alkalmazást, amelyet az [első Django alkalmazás](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) djangoi dokumentációban való írása követve kap.

### <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Terminálablakban futtassa a következő parancsokat a mintaalkalmazás-tárház klónozásához, és váltson az új munkakönyvtárra:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>A Python virtuális környezet konfigurálása

Hozzon létre és aktiváljon egy Python virtuális környezetet az alkalmazás futtatásához.

```bash
python3 -m venv venv
source venv/bin/activate
```
vagy
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

A `venv` környezetben futtassa *a env.sh* vagy *env.ps1* parancsot az *azuresite/settings.py* által az adatbázis-kapcsolat beállításaihoz használt környezeti változók beállításához.

```bash
source ./env.sh
```
vagy
```PowerShell
.\env.ps1
```

Telepítse a szükséges csomagokat *a requirements.txt*fájlból, futtassa a [Django-áttelepítéseket](https://docs.djangoproject.com/en/2.1/topics/migrations/), és [hozzon létre egy rendszergazdai felhasználót:](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>A webalkalmazás futtatása

A rendszergazdai felhasználó létrehozása után futtassa a Django-kiszolgálót.

```bash
python manage.py runserver
```

Amikor a Django webalkalmazás teljesen be van töltve, a következőhez hasonló üzenetet ad vissza:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Ugrás a *http:\//localhost:8000* böngészőre. Megjelenik a **Nincs szavazás.** 

Nyissa meg a *\/http: /localhost:8000/admin* lehetőséget, és jelentkezzen be az utolsó lépésben létrehozott rendszergazdai felhasználóval. Válassza a **Hozzáadás** lehetőséget a **Kérdések**csoportban, és hozzon létre néhány választási lehetőséget.

![Python Django alkalmazás futtatása az App Servicesben helyileg](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ugrás *a\/http: /localhost:8000* újra, hogy a szavazás kérdésére, és válaszoljon a kérdésre. A helyi Django mintaalkalmazás felhasználói adatokat ír és tárol a helyi PostgreSQL-adatbázisba.

A Django kiszolgáló leállításához írja be a Ctrl+C billentyűkombinációt a terminálba.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ebben a cikkben a fennmaradó lépések az Azure CLI-parancsok at az Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Létrehozása és csatlakozás a PostgreSQL Azure-adatbázisához

Ebben a szakaszban hozzon létre egy Azure Database for PostgreSQL-kiszolgáló és -adatbázis, és csatlakoztassa a webalkalmazást hozzá. Amikor telepíti a webalkalmazást az Azure App Service-ben, az alkalmazás ezt a felhőalapú adatbázist használja. 

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Létrehozhat egy új erőforráscsoportot az Azure Database for PostgreSQL-kiszolgálóhoz, vagy használhat egy meglévő erőforráscsoportot. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL szervert az [az postgres kiszolgáló létrehozása](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) paranccsal a Cloud Shellben.

> [!NOTE]
> Mielőtt létrehozna egy Azure Database for PostgreSQL-kiszolgálót, ellenőrizze, hogy melyik [számítási generáció](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) érhető el az Ön régiójában. Ha a régió nem támogatja a Gen4 hardvert, módosítsa a *--sku-name parancsot* a következő parancssorban egy olyan értékre, amely et a régióban támogat, például a Gen5.If your region doesn't support Gen4 hardware, change --sku-name in the following command line to a value that's supported in your region, suchas Gen5. 

A következő parancsban cserélje le * \<a postgresql-name>* egyedi kiszolgálónévre. A kiszolgálónév a *PostgreSQL-végpont\<része, https:// postgresql-name>.postgres.database.azure.com,* ezért a névnek egyedinek kell lennie az Azure összes kiszolgálóján. 

Cserélje le * \<az erőforráscsoport nevét>* és * \<a régió>* a használni kívánt erőforráscsoport nevére és régiójára. A * \<rendszergazdai felhasználónév>* és * \<a rendszergazdai jelszó>* esetén hozza létre az adatbázis-rendszergazdai fiók felhasználói hitelesítő adatait. Ne * \<* feledje, hogy a>és * \<a rendszergazdai jelszó>* később a PostgreSQL kiszolgálóra és adatbázisokba való bejelentkezéshez használható.

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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Tűzfalszabályok létrehozása az Azure Database for PostgreSQL kiszolgálóhoz

Futtassa az [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) parancsot, hogy az Azure-erőforrásokból hozzáférést biztosítson az adatbázishoz. Cserélje le a * \<postgresql-név>* és * \<erőforráscsoport-nevét>* helyőrzők az értékeket.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Az előző beállítás lehetővé teszi a hálózati kapcsolatok az Azure-hálózaton belüli összes IP-címről. Éles használatra próbálja meg beállítani a lehető legszigorúbb tűzfalszabályokat, [ha csak az alkalmazás által használt kimenő IP-címeket engedélyezi.](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)

Futtassa újra a `firewall-rule create` parancsot a helyi számítógépről való hozzáférés engedélyezéséhez. Cserélje * \<le az ip-cím>* [a helyi IPv4 IP-címre.](https://www.whatsmyip.org/) Cserélje le a * \<postgresql-név>* és * \<erőforráscsoport-név>* helyőrzők a saját értékeket.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Az Azure-adatbázis létrehozása és csatlakozás a PostgreSQL-adatbázishoz

Csatlakozzon az Azure Database for PostgreSQL-kiszolgálóhoz a következő parancs futtatásával. Használja a saját * \<postgresql-név>* és * \<admin-username>*, és jelentkezzen be a jelszót létrehozott.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Csakúgy, mint a helyi PostgreSQL-kiszolgálón, hozzon létre egy adatbázist és egy felhasználót az Azure Database for PostgreSQL kiszolgálón:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Ajánlott eljárás, ha a rendszergazdai felhasználó használata helyett korlátozott engedélyekkel rendelkező adatbázis-felhasználókat hoz létre bizonyos alkalmazásokhoz. A `manager` felhasználó teljes jogosultsággal `pollsdb` *rendelkezik,* hogy csak az adatbázis.

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Az alkalmazáskapcsolat tesztelése az Azure PostgreSQL-adatbázissal

A helyi *env.sh* vagy *env.ps1* fájlja a felhőpostgreSQL-adatbázisra mutat, és a * \<postgresql-név>* lecseréli az Azure Database for PostgreSQL kiszolgálónevére.

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

A `venv` helyi terminálablakban lévő környezetben futtassa a szerkesztett *env.sh* vagy *az env.ps1 .* 
```bash
source ./env.sh
```
vagy
```PowerShell
.\env.ps1
```

Futtassa a Django-áttelepítést az Azure-adatbázisba, és hozzon létre egy rendszergazdai felhasználót.

```bash
python manage.py migrate
python manage.py createsuperuser
```

A rendszergazdai felhasználó létrehozása után futtassa a Django kiszolgálót.

```bash
python manage.py runserver
```

A böngészőben nyissa meg *a http:\//localhost:8000*, és meg kell jelennie az üzenet **Nincs szavazás oka** újra. 

Ugrás a *\/http: /localhost:8000/admin*, jelentkezzen be a rendszergazda felhasználó által létrehozott, és hozzon létre egy szavazáskérdés, mint korábban.

![Python Django alkalmazás futtatása az App Servicesben helyileg](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Ugrás *a\/http: /localhost:8000* újra, és látni a szavazás kérdés jelenik meg. Az alkalmazás most adatokat ír az Azure Database for PostgreSQL adatbázisba.

A Django kiszolgáló leállításához írja be a Ctrl+C billentyűkombinációt a terminálba.

## <a name="deploy-the-web-app-to-azure-app-service"></a>A webalkalmazás üzembe helyezése az Azure App Service szolgáltatásban

Ebben a lépésben telepíti az Azure Database for PostgreSQL adatbázis-csatlakoztatott Python alkalmazást az Azure App Service-be.

### <a name="configure-repository"></a>Az adattár konfigurálása

Mivel ez az oktatóanyag egy Django-mintát használ, módosítania kell, és hozzá kell adnia néhány beállítást a *djangoapp/azuresite/settings.py* fájlban az Azure App Service-rel való munkához. 

1. A Django `HTTP_HOST` ellenőrzi a fejlécet a bejövő kérelmekben. Ahhoz, hogy a Django webalkalmazás működjön az App Service-ben, hozzá kell adnia az alkalmazás teljesen minősített tartománynevét az engedélyezett állomásokhoz. 
   
   Az *azuresite/settings.py* szerkesztéséhez `ALLOWED_HOSTS` módosítsa a sort az alábbiak szerint:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. A Django nem támogatja [a statikus fájlok éles környezetben való kiszolgálását.](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/) Ebben az oktatóanyagban a [WhiteNoise](https://whitenoise.evans.io/en/stable/) segítségével engedélyezheti a fájlok kiszolgálását. A WhiteNoise csomag már telepítve volt *a requirements.txt*. 
   
   Konfigurálása Django használni WhiteNoise, az *azuresite/settings.py*, keresse meg a `MIDDLEWARE` beállítást, és adja hozzá `whitenoise.middleware.WhiteNoiseMiddleware` a listához, közvetlenül a `django.middleware.security.SecurityMiddleware` sor után. A `MIDDLEWARE` beállításnak így kell kinéznie:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Az *azuresite/settings.py*végén adja hozzá a következő sorokat:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   A WhiteNoise konfigurálásáról a [WhiteNoise dokumentációjában](https://whitenoise.evans.io/en/stable/)olvashat bővebben.

> [!IMPORTANT]
> Az adatbázis-beállítások szakasz már követi a környezeti változók használatának ajánlott biztonsági gyakorlatát. A teljes telepítési javaslatokat a [Django dokumentációja: telepítési ellenőrzőlista tartalmazza.](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)

Véglegesítse a módosításokat a *djangoapp-tárház* elágazásában:

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

Az Azure App Service-ben a környezeti változókat *alkalmazásbeállításokként*állíthatja be, az [az webapp-appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs használatával.

Az Azure Cloud Shell, futtassa a következő parancsot az adatbázis-kapcsolat részleteinek megadása alkalmazásbeállításokként. Cserélje le * \<az alkalmazásnevet>*, * \<erőforráscsoport-név>* és * \<a postgresql-név>* a saját értékeire.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Arról, hogy a kód hogyan fér hozzá ezekhez az alkalmazásbeállításokhoz, olvassa el [az Access környezeti változóit.](how-to-configure-python.md#access-environment-variables)

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

Az App Service központi telepítési kiszolgálója látja *a requirements.txt* `git push`fájlt a tárház gyökérében, és a .

### <a name="browse-to-the-azure-app"></a>Tallózás az Azure alkalmazásban

Tallózással keresse meg a telepített alkalmazást az URL-címmel *\//\<http: app-name>.azurewebsites.net*. Az indítás némi időt vesz igénybe, mert a tárolót le kell tölteni, és futtatni kell, amikor az alkalmazás először kéri. Ha a lapon túllépi az időkorlátot vagy egy hibaüzenet jelenik meg, várjon néhány percet, és frissítse az oldalt.

A korábban létrehozott szavazási kérdéseknek meg kell jelennie. 

Az App Service észleli a Django-projektet *wsgi.py* a tárházban, ha `manage.py startproject` minden alkönyvtárban wsgi.py fájlt keres, amely alapértelmezés szerint létrehoz. Amikor az App Service megtalálja a fájlt, betölti a Django webalkalmazást. Arról, hogy az App Service hogyan tölti be a Python-alkalmazásokat, olvassa [el a Beépített Python-lemezkép konfigurálása című témakört.](how-to-configure-python.md)

Nyissa meg *a\//\<http: app-name>.azurewebsites.net/admin* webhelyet, és jelentkezzen be a létrehozott rendszergazdai felhasználóval. Ha szeretné, hozzon létre még néhány szavazási kérdést.

![Python Django alkalmazás futtatása az Azure-beli App Services szolgáltatásban](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Gratulálok!** Python (Django) webalkalmazást futtat az Azure App Service Linux szolgáltatásában.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése az Azure Portalon

Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a létrehozott alkalmazást.

![Keresse meg a Python Django alkalmazást az Azure Portalon](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portál megjeleníti az alkalmazás **áttekintése** lapot. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt olyan alapvető felügyeleti feladatokat is végrehajthat, mint a tallózás, a leállítás, az újraindítás és a törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django alkalmazás kezelése az Azure Portal Áttekintés lapján](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

A következő oktatóanyagból megtudhatja, hogyan képezheti le az egyéni DNS-nevet az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név hozzárendelése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy nézd meg más források:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)
