---
title: Python (a Django) és a Linux - az Azure App Service PostgreSQL |} A Microsoft Docs
description: Ismerje meg, hogyan futtathat egy PostgreSQL-adatbáziskapcsolattal rendelkező, adatvezérelt Python-alkalmazást az Azure-ban. Django szerepel az oktatóanyagot.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: f82cccb66c0aae93afe19259393f094d0627c801
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546421"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Az Azure App Service a Python és PostgreSQL alkalmazás készítése

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan skálázható, önjavító webes üzemeltetési szolgáltatást nyújt. Ez az oktatóanyag bemutatja, hogyan hozhat létre adatvezérelt Python-alkalmazás, adatbázis háttérrendszerként PostgreSQL használatával. Ha elkészült, rendelkezni fog olyan Django-alkalmazást a linuxon futó App Service-ben.

![Python Django-alkalmazást a linuxon futó App Service-ben](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók megtekintése
> * Az alkalmazás kezelése az Azure Portalon

> [!NOTE]
> Mielőtt hoz létre egy Azure Database for postgresql-hez, ellenőrizze [számítási generáció érhető el az Ön régiójában](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

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

Hozzon létre egy adatbázist nevű *pollsdb* és a egy adatbázis-felhasználót beállítása *manager* felhasználónévvel és jelszóval *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Helyi Python-alkalmazás létrehozása

Ebben a lépésben beállíthatja a helyi Python Django-projektet.

### <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Nyissa meg a terminálablakot, és a `CD` paranccsal hozzon létre egy munkakönyvtárat.

Az alábbi parancsok futtatásával klónozza a mintatárházat.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Ez a mintaadattár tartalmaz egy [Django](https://www.djangoproject.com/) alkalmazás. A következő számíthat ugyanazon az adatvezérelt alkalmazás legyen a [a Django dokumentációja a kezdeti lépéseket ismertető oktatóanyag](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Ez az oktatóanyag nem tartalmazza a Django, de bemutatja, hogyan helyezhet üzembe és futtathat egy Django-alkalmazás (vagy az adatvezérelt Python-alkalmazás egy másik) App Service-ben.

### <a name="configure-environment"></a>A környezet konfigurálása

Hozzon létre egy Python virtuális környezetet, és a egy parancsfájllal állítsa be az adatbázis kapcsolati beállításokat.

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

A meghatározott környezeti változókat *env.sh* és *env.ps1* használt _azuresite/settings.py_ adatbázis beállítások meghatározásához.

### <a name="run-app-locally"></a>Az alkalmazás futtatása helyileg

A szükséges csomagok telepítéséhez [futtassa a Django-migrálási](https://docs.djangoproject.com/en/2.1/topics/migrations/) és [hozzon létre egy rendszergazdai felhasználó](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

A rendszergazda felhasználó létrehozása után futtassa a Django-kiszolgáló.

```bash
python manage.py runserver
```

Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Egy böngészőben nyissa meg a `http://localhost:8000` oldalt. Az üzenetnek kell megjelennie `No polls are available.`. 

Navigáljon a `http://localhost:8000/admin` és jelentkezzen be a rendszergazdai felhasználó, az előző lépésben létrehozott. Kattintson a **Hozzáadás** melletti **kérdések** , és hozzon létre egy lekérdezési kérdést néhány.

![Helyileg futó Python Django-alkalmazás](./media/tutorial-python-postgresql-app/django-admin-local.png)

Navigáljon a `http://localhost:8000` újra, és tekintse meg a lekérdezési kérdés jelenik meg.

A Django-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha Ön egy lekérdezési kérdés hozzáadása sikeres, az alkalmazás írja az adatokat a helyi PostgreSQL-adatbázishoz.

Bármikor a Django-kiszolgáló leállításához írja be a Ctrl + C billentyűkombinációt a terminálon.

## <a name="create-a-production-postgresql-database"></a>Éles PostgreSQL-adatbázis létrehozása

Ebben a lépésben egy PostgreSQL-adatbázist hozunk létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy PostgreSQL-kiszolgálót a Cloud Shellben az [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) paranccsal.

Cserélje le a következő példaparancs  *\<postgresql-name >* egy egyedi kiszolgálónevet, és cserélje le a  *\<admin-username >* és  *\<rendszergazdai jelszó->* a kívánt felhasználói hitelesítő adatokkal. A felhasználói hitelesítő adatokra az adatbázis rendszergazdai fiókjához van szükség. A kiszolgálónév a postgreSQL-végpont (`https://<postgresql-name>.postgres.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban.

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
> Ne feledje \<admin-username > és \<rendszergazdai jelszó-> későbbi használatra. Szüksége lesz rájuk a Postgres-kiszolgálóra és annak adatbázisaiba való bejelentkezéshez.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Tűzfalszabályok létrehozása a PostgreSQL-kiszolgálóhoz

A Cloud Shellben futtassa a következő Azure CLI-parancsokat, hogy engedélyezze az adatbázishoz való hozzáférést az Azure-erőforrásokról.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Ez a beállítás lehetővé teszi a hálózati kapcsolatok létesítését az Azure hálózatán belül található minden IP-címről. Éles környezetben próbálja meg a lehető legszigorúbb tűzfalszabályokat konfigurálni úgy, hogy [kizárólag az alkalmazása által használt kimenő IP-címeket használja](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

A Cloud shellben futtassa a parancsot újból engedélyezi a hozzáférést a helyi számítógépről történő lecserélésével  *\<az ip-cím >* a [a helyi IPv4 IP-cím](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Python-alkalmazás csatlakoztatása éles adatbázishoz

Ebben a lépésben csatlakoztatja a Django-mintaalkalmazást, az Azure Database for PostgreSQL-kiszolgálóhoz létrehozott.

### <a name="create-empty-database-and-user-access"></a>Üres adatbázis és felhasználói hozzáférés létrehozása

A Cloud shellben csatlakozzon az adatbázishoz az alábbi parancs futtatásával. Ha a rendszer a rendszergazdai jelszó megadására kéri, használja az [Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz](#create-an-azure-database-for-postgresql-server) részben megadott jelszót.

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

A helyi terminálablakban, módosítsa az adatbázis-környezeti változók (amelyet korábban beállított futtatásával *env.sh* vagy *env.ps1*):

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

Django-áttelepítés futtatása az Azure-adatbázishoz, és hozzon létre egy rendszergazdai felhasználót.

```bash
python manage.py migrate
python manage.py createsuperuser
```

A rendszergazda felhasználó létrehozása után futtassa a Django-kiszolgáló.

```bash
python manage.py runserver
```

Navigáljon a `http://localhost:8000` az újra. Az üzenetnek kell megjelennie `No polls are available.` újra. 

Navigáljon a `http://localhost:8000/admin` , és jelentkezzen be a rendszergazdai felhasználó hozott létre, és hozzon létre egy lekérdezési kérdést például előtt.

![Helyileg futó Python Django-alkalmazás](./media/tutorial-python-postgresql-app/django-admin-local.png)

Navigáljon a `http://localhost:8000` újra, és tekintse meg a lekérdezési kérdés jelenik meg. Az alkalmazás most már írja az adatokat az adatbázisba, az Azure-ban.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a Postgreshez csatlakoztatott Python-alkalmazást az Azure App Service-ben.

### <a name="configure-repository"></a>Az adattár konfigurálása

Django érvényesíti a `HTTP_HOST` fejléc a bejövő kérelmeket. Az App Service-ben működnek a Django-alkalmazások esetén szüksége a teljesen minősített tartománynevet az alkalmazás hozzáadása az engedélyezett gazdagépek. Nyissa meg _azuresite/settings.py_ , és keresse meg a `ALLOWED_HOSTS` beállítás. Módosítsa a sort:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Ezután nem támogatja a Django [éles környezetben statikus fájlok kiszolgálása](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), így ez manuálisan engedélyeznie kell. A jelen oktatóanyag esetében használja [WhiteNoise](https://whitenoise.evans.io/en/stable/). A WhiteNoise csomag már szerepel a _requirements.txt_. Egyszerűen Django a használatára konfigurálja. 

A _azuresite/settings.py_, keresse meg a `MIDDLEWARE` beállítást, és adja hozzá a `whitenoise.middleware.WhiteNoiseMiddleware` közbenső szoftvert a listában csak az alábbiakban a `django.middleware.security.SecurityMiddleware` közbenső szoftver. A `MIDDLEWARE` beállítást kell kinéznie:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

Végén _azuresite/settings.py_, adja hozzá a következő sorokat.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

WhiteNoise konfigurálásával kapcsolatos további információkért lásd: a [WhiteNoise dokumentáció](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Az adatbázis-beállítások szakaszban már követi a biztonsági szempontból ajánlott a környezeti változók használatával. A teljes üzembe helyezés javaslatokért lásd: [a Django dokumentációja: telepítési ellenőrzőlista](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Véglegesítse a módosításokat a tárházba.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az oktatóanyag korábbi részében meghatároztunk környezeti változókat a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ lehet beállítani az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs Cloud Shellben való használatával.

Az alábbi példa az adatbázis kapcsolati adatait alkalmazásbeállításokként adja meg. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Hogyan érhetők el ezeket az alkalmazásbeállításokat a kódban kapcsolatos információkért lásd: [hozzáférés a környezeti változókhoz](how-to-configure-python.md#access-environment-variables).

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

Az App Service-ben rendszerbe állítási kiszolgáló látja _requirements.txt_ az adattár gyökérkönyvtárában és után automatikusan futtatja a Python-csomagkezelés `git push`.

### <a name="browse-to-the-azure-app"></a>Az Azure alkalmazás megkeresése tallózással

Az üzembe helyezett alkalmazás megkeresése tallózással. Az elindítása hosszabb időt vesz igénybe, mivel a tárolót le kell tölteni és el kell indítani az alkalmazás első igénylésekor. Ha a lapon túllépi az időkorlátot vagy egy hibaüzenet jelenik meg, várjon néhány percet, és frissítse az oldalt.

```bash
http://<app-name>.azurewebsites.net
```

A korábban létrehozott lekérdezési kérdést kell megjelennie. 

App Service-ben a tárház projekt Django észleli az olyan _wsgi.py_ egyes alkönyvtárában található cikkre hivatkozik, amely által létrehozott `manage.py startproject` alapértelmezés szerint. Ha a fájlt talál, betölti a Django-alkalmazás. Hogyan betölti az App Service a Python-alkalmazások további információkért lásd: [konfigurálása beépített Python-rendszerkép](how-to-configure-python.md).

Navigáljon a `<app-name>.azurewebsites.net` , és jelentkezzen be a ugyanaz a rendszergazda felhasználó hozott létre. Ha szeretné próbálja meg néhány további lekérdezési kérdések létrehozása.

![Helyileg futó Python Django-alkalmazás](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Gratulálunk!** Egy Python-alkalmazást futtat a Linux rendszerhez készült App Service-ben.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Az Azure Portalon az alkalmazás kezelése

Nyissa meg a [az Azure portal](https://portal.azure.com) szeretné megtekinteni a létrehozott alkalmazást.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-python-postgresql-app/app-resource.png)

Alapértelmezés szerint a portál megjeleníti az alkalmazás **áttekintése** lapot. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók megtekintése
> * Az alkalmazás kezelése az Azure Portalon

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név leképezése az alkalmazás](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg az egyéb erőforrások:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)