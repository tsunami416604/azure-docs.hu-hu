---
title: Python (Django) webalkalmazás a PostgreSQL használatával Linux rendszeren – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan futtathat adatvezérelt Python-(Django-) webalkalmazásokat az Azure-ban egy PostgreSQL-adatbázishoz való kapcsolódással.
services: app-service\web
documentationcenter: python
author: cephalin
manager: gwallace
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b5a21bd144558459ac60ae2da405f9ea57ca2fd1
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020188"
---
# <a name="build-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Python-(Django-) Webalkalmazás létrehozása a PostgreSQL-sel Azure App Service

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan skálázható, önjavító webes üzemeltetési szolgáltatást nyújt. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy adatvezérelt Python-(Django-) webalkalmazást a PostgreSQL használatával adatbázis-háttérként. Ha elkészült, Django webalkalmazása Azure App Service Linuxon fut.

![Python Django-webalkalmazás App Service Linuxon](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-webalkalmazás összekötése a PostgreSQL-vel
> * A Python-webalkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók megtekintése
> * A Python-webalkalmazás kezelése a Azure Portal

> [!NOTE]
> Azure Database for PostgreSQL létrehozása előtt ellenőrizze, hogy az [adott régióban elérhető-e a számítási generáció](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

A cikk lépései macOS rendszerre vonatkoznak. Linux és Windows rendszeren a legtöbb esetben ugyanezek az utasítások érvényesek, az oktatóanyag azonban nem tér ki az eltérésekkel kapcsolatos részletekre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
2. [Telepítse a Pythont](https://www.python.org/downloads/)
3. [A PostgreSQL telepítése és futtatása](https://www.postgresql.org/download/)

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

Hozzon létre egy *pollsdb* nevű adatbázist, és állítson be egy külön adatbázis-felhasználót, amelynek neve Password *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Helyi Python-alkalmazás létrehozása

Ebben a lépésben beállítja a helyi Python Django-projektet.

### <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Nyissa meg a terminálablakot, és a `CD` paranccsal hozzon létre egy munkakönyvtárat.

Az alábbi parancsok futtatásával klónozza a mintatárházat.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Ez a példa egy [Django](https://www.djangoproject.com/) alkalmazást tartalmaz. Ez ugyanaz az adatvezérelt alkalmazás, amelyet a [Django dokumentációjának első lépések oktatóanyaga](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)alapján érhet el. Ez az oktatóanyag nem tanít Django, de bemutatja, hogyan helyezhet üzembe és futtathat egy Django-webalkalmazást (vagy egy másik adatvezérelt Python-alkalmazást) a Azure App Service.

### <a name="configure-environment"></a>A környezet konfigurálása

Hozzon létre egy Python virtuális környezetet, és használjon parancsfájlt az adatbázis-kapcsolódási beállítások megadásához.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

A *env.sh* és a *env. ps1* definiált környezeti változók a _azuresite/Settings. a. a._ az adatbázis-beállítások definiálásához használatosak.

### <a name="run-app-locally"></a>Az alkalmazás futtatása helyileg

Telepítse a szükséges csomagokat, [futtassa a Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) -áttelepítést, és [hozzon létre egy rendszergazdai felhasználót](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

A rendszergazda felhasználó létrehozása után futtassa a Django-kiszolgálót.

```bash
python manage.py runserver
```

Ha a Django-webalkalmazás teljesen be van töltve, az alábbihoz hasonló üzenet jelenik meg:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

`http://localhost:8000` Ugrás a böngészőben. Ekkor megjelenik az üzenet `No polls are available.`. 

Nyissa `http://localhost:8000/admin` meg a t, és jelentkezzen be az utolsó lépésben létrehozott rendszergazdai felhasználó használatával. Válassza a **Hozzáadás** lehetőséget a **kérdések** mellett, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

![Helyileg futó Python Django-alkalmazás](./media/tutorial-python-postgresql-app/django-admin-local.png)

`http://localhost:8000` Lépjen újra, és tekintse meg a megjelenő lekérdezési kérdést.

A Django-minta alkalmazás az adatbázisban tárolja a felhasználói adatbázisokat. Ha sikeres a lekérdezési kérdés hozzáadásakor, az alkalmazás az adatait a helyi PostgreSQL-adatbázisba írja.

Ha bármikor le szeretné állítani a Django-kiszolgálót, írja be a CTRL + C billentyűkombinációt a terminálon.

## <a name="create-a-production-postgresql-database"></a>Éles PostgreSQL-adatbázis létrehozása

Ebben a lépésben egy PostgreSQL-adatbázist hozunk létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL-kiszolgálót a Cloud Shellben az [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) paranccsal.

Az alábbi példában a következő parancsban cserélje le  *\<a PostgreSQL-Name >t* egyedi kiszolgálónévre, és cserélje le  *\<a admin-username >* és  *\<a rendszergazdai jelszó >* a kívánt felhasználói hitelesítő adatokkal. A felhasználói hitelesítő adatokra az adatbázis rendszergazdai fiókjához van szükség. A kiszolgálónév a postgreSQL-végpont (`https://<postgresql-name>.postgres.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

Az Azure Database for PostgreSQL-kiszolgáló létrehozását követően az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> Ne \<feledje, hogy az admin \<-username > és a admin-password > később. Szüksége lesz rájuk a Postgres-kiszolgálóra és annak adatbázisaiba való bejelentkezéshez.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Tűzfalszabályok létrehozása a PostgreSQL-kiszolgálóhoz

A Cloud Shellben futtassa a következő Azure CLI-parancsokat, hogy engedélyezze az adatbázishoz való hozzáférést az Azure-erőforrásokról.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Ez a beállítás lehetővé teszi a hálózati kapcsolatok létesítését az Azure hálózatán belül található minden IP-címről. Éles környezetben próbálja meg a lehető legszigorúbb tűzfalszabályokat konfigurálni úgy, hogy [kizárólag az alkalmazása által használt kimenő IP-címeket használja](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

A Cloud Shell futtassa újra a parancsot, hogy engedélyezze a hozzáférést a helyi számítógépről úgy, hogy lecseréli  *\<az-IP-cím >* a [helyi IPv4 IP](https://www.whatsmyip.org/)-címére.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python-alkalmazás csatlakoztatása éles adatbázishoz

Ebben a lépésben a Django-webalkalmazást a létrehozott Azure Database for PostgreSQL-kiszolgálóhoz kapcsolja.

### <a name="create-empty-database-and-user-access"></a>Üres adatbázis és felhasználói hozzáférés létrehozása

A Cloud Shell az alábbi parancs futtatásával kapcsolódjon az adatbázishoz. Ha a rendszer a rendszergazdai jelszó megadására kéri, használja az [Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz](#create-an-azure-database-for-postgresql-server) részben megadott jelszót.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Csakúgy, mint a helyi Postgres-kiszolgáló esetében, az adatbázist és a felhasználót itt is az Azure Postgres-kiszolgálón hozza létre.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

> [!NOTE]
> Ajánlott eljárás, hogy bizonyos alkalmazásokhoz korlátozott hozzáféréssel rendelkező adatbázis-felhasználókat hozzon létre a rendszergazdai felhasználó használata helyett. Ebben a példában a `manager` felhasználó _kizárólag_ az `pollsdb` adatbázishoz rendelkezik teljes körű hozzáféréssel.

### <a name="test-app-connectivity-to-production-database"></a>Az alkalmazás és az éles adatbázis közötti kapcsolat ellenőrzése

A helyi terminál ablakban módosítsa a *env.sh* vagy *env. ps1*futtatásával korábban konfigurált adatbázis-környezeti változókat:

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
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

`http://localhost:8000` Ugrás újra. Ekkor újra meg kell jelennie `No polls are available.` az üzenetnek. 

Nyissa `http://localhost:8000/admin` meg a t, és jelentkezzen be a létrehozott rendszergazda felhasználóval, és hozzon létre egy lekérdezési kérdést, mint korábban.

![Helyileg futó Python Django-alkalmazás](./media/tutorial-python-postgresql-app/django-admin-local.png)

`http://localhost:8000` Lépjen újra, és tekintse meg a megjelenő lekérdezési kérdést. Az alkalmazás most már az Azure-adatbázisba írja az adatait.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a Postgreshez csatlakoztatott Python-alkalmazást az Azure App Service-ben.

### <a name="configure-repository"></a>Az adattár konfigurálása

A Django ellenőrzi a `HTTP_HOST` bejövő kérelmek fejlécét. Ahhoz, hogy a Django-webalkalmazás működjön a App Serviceban, hozzá kell adnia az alkalmazás teljes tartománynevét az engedélyezett gazdagépekhez. Nyissa meg a _azuresite/Settings._ , és keresse meg a `ALLOWED_HOSTS` beállítást. Módosítsa a sort a következőre:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Ezután a Django nem támogatja [a statikus fájlok éles](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)környezetben való kiszolgálását, ezért ezt manuálisan kell engedélyeznie. Ebben az oktatóanyagban a [WhiteNoise](https://whitenoise.evans.io/en/stable/)-t használja. A WhiteNoise csomag már szerepel a _követelmények. txt fájlban_. Csak konfigurálnia kell a Django a használatára. 

A _azuresite/Settings. a._ a `MIDDLEWARE` -ben keresse meg a `whitenoise.middleware.WhiteNoiseMiddleware` beállítást, és adja hozzá a middleware- `django.middleware.security.SecurityMiddleware` t a listához közvetlenül a middleware alá. A `MIDDLEWARE` beállításnak így kell kinéznie:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Adja hozzá a következő sorokat a _azuresite/Settings. a. a._

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

A WhiteNoise konfigurálásával kapcsolatos további információkért tekintse meg a [WhiteNoise dokumentációját](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Az adatbázis-beállítások szakasz már a környezeti változók használatával kapcsolatos ajánlott biztonsági gyakorlatot követi. Az üzembe helyezéssel kapcsolatos javaslatokért tekintse meg a [Django dokumentációját: telepítési ellenőrzőlista](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Véglegesítse a módosításokat a tárházban.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Üzembe helyezési felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az oktatóanyag korábbi részében meghatároztunk környezeti változókat a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ lehet beállítani az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs Cloud Shellben való használatával.

Az alábbi példa az adatbázis kapcsolati adatait alkalmazásbeállításokként adja meg. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

További információ arról, hogyan érhetők el ezek az Alkalmazásbeállítások a kódban: hozzáférés a [környezeti változókhoz](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

A App Service központi telepítési kiszolgáló a _követelmények. txt fájlt_ látja a tárház gyökerében, és automatikusan `git push`futtatja a Python csomagkezelő szolgáltatását.

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Tallózással keresse meg az üzembe helyezett alkalmazást. Az elindítása hosszabb időt vesz igénybe, mivel a tárolót le kell tölteni és el kell indítani az alkalmazás első igénylésekor. Ha a lapon túllépi az időkorlátot vagy egy hibaüzenet jelenik meg, várjon néhány percet, és frissítse az oldalt.

```bash
http://<app-name>.azurewebsites.net
```

Ekkor meg kell jelennie a korábban létrehozott lekérdezési kérdésnek. 

A app Service egy Django-projektet észlel a tárházban úgy, hogy megkeresi az egyes alkönyvtárakban található `manage.py startproject` WSGI.py, amelyet alapértelmezés szerint hoz létre. Amikor megkeresi a fájlt, betölti a Django-webalkalmazást. További információ a App Service Python-alkalmazások betöltéséről: a [beépített Python-rendszerkép konfigurálása](how-to-configure-python.md).

Nyissa `<app-name>.azurewebsites.net` meg a t, és jelentkezzen be ugyanazzal a rendszergazda felhasználóval, amelyet Ön hozott létre. Ha szeretné, próbálkozzon még több lekérdezési kérdés létrehozásával.

![Helyileg futó Python Django-alkalmazás](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Gratulálunk!** Python-(Django-) webalkalmazást futtat Azure App Service Linux rendszeren.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése a Azure Portalban

A létrehozott alkalmazás megjelenítéséhez nyissa meg a [Azure Portal](https://portal.azure.com) .

A bal oldali menüben válassza a **app Services**lehetőséget, majd válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-python-postgresql-app/app-resource.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** lapját jeleníti meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-webalkalmazás összekötése a PostgreSQL-vel
> * A Python-webalkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók megtekintése
> * A Python-webalkalmazás kezelése a Azure Portal

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)