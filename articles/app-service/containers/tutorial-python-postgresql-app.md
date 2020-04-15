---
title: 'Oktatóanyag: Linux Python alkalmazás a Postgres-szel'
description: Megtudhatja, hogyan szerezhet be egy Linux Python-alkalmazást az Azure App Service szolgáltatásban, amely kapcsolatban áll egy PostgreSQL-adatbázissal az Azure-ban. Az oktatóanyag egy Django mintaalkalmazás használatával szemlélteti.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 47beb964f87fb7a68a4c12b0e35f17038cdf16f8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380697"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Python (Django) webalkalmazás üzembe helyezése a PostgreSQL alkalmazással az Azure App Service-ben

Ez az oktatóanyag bemutatja, hogyan telepíthet egy adatközpontú Python (Django) webalkalmazást az [Azure App Service szolgáltatásba,](app-service-linux-intro.md) és hogyan csatlakoztathatja egy Azure-adatbázispostgreSQL-adatbázishoz. Az App Service rendkívül skálázható, önjavító webhosting szolgáltatást nyújt. 

![Python Django webalkalmazás telepítése az Azure App Service szolgáltatásba](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure-adatbázis létrehozása postgreSQL-adatbázishoz
> * Kód üzembe helyezése az Azure App Service szolgáltatásban, és csatlakozás a Postgres-hez
> * A kód frissítése és újratelepítés
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése az Azure Portalon

A cikk lépéseit macOS, Linux vagy Windows rendszeren követheti.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Telepítse [az Azure CLI-t](/cli/azure/install-azure-cli).
- Telepítse [a Git](https://git-scm.com/)- alkalmazást.
- Telepítse [a Python 3-at](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Terminálablakban futtassa a következő parancsokat a mintaalkalmazás-tárház klónozásához, és váltson az új munkakönyvtárra:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

A djangoapp mintatár tartalmazza az adatközpontú [Django](https://www.djangoproject.com/) szavazási alkalmazást, amelyet az [első Django alkalmazás](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) djangoi dokumentációban való írása követve kap.

## <a name="prepare-app-for-app-service"></a>Alkalmazás előkészítése az App Service szolgáltatásra

Mint sok Python webes keretrendszerek, Django [igényel bizonyos módosításokat, mielőtt azok futtathatók az éles kiszolgálón,](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)és ez nem különbözik az App Service. Módosítania kell, és hozzá kell adnia néhány beállítást az alapértelmezett *azuresite/settings.py* fájlban, hogy az alkalmazás az App Service-be való üzembe helyezése után is működjön. 

Vessen egy pillantást *az azuresite/production.py,* amely leteszi a szükséges konfigurációt az App Service. Ez hozzá a kényelem, de még nem használja az alkalmazás. Röviden a következőket teszi:

- Örökölje az összes beállítást az *azuresite/settings.py*webhelyről.
- Adja hozzá az App Service alkalmazás teljesen minősített tartománynevét az engedélyezett állomásokhoz. 
- A [WhiteNoise](https://whitenoise.evans.io/en/stable/) használatával engedélyezheti a statikus fájlok kiszolgálását éles környezetben, mert a Django alapértelmezés szerint nem szolgál statikus fájlokat éles környezetben. A WhiteNoise csomag már szerepel *a requirements.txt*.
- Adja hozzá a PostgreSQL adatbázis konfigurációját. Alapértelmezés szerint a Django az Sqlite3-ot használja adatbázisként, de nem alkalmas éles alkalmazásokhoz. A [psycopg2-bináris](https://pypi.org/project/psycopg2-binary/) csomag már szerepel a *requirements.txt*fájlban.

Hajtsa végre a következő módosításokat az alkalmazáson úgy, hogy *az azuresite/production.py-t* használja az App Service-ben.

1. A *manage.py*módosítsa a következő sort:

    ```python
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    A következő kódra:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Az App Service-alkalmazás `DJANGO_ENV` konfigurálásakor később be kell állítania a környezeti változót.

1. Az *azuresite/wsgi.py*alkalmazásban ugyanazt a módosítást kell elkészíteni, mint fent.

    Az App Service-ben *manage.py* használatával futtathatja az adatbázis-áttelepítéseket, és az App Service *az azuresite/wsgi.py* használatával futtatja a Django-alkalmazást éles környezetben. Ez a módosítás mindkét fájlban biztosítja, hogy a termelési beállítások mindkét esetben használatosak legyenek.

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Az Azure CLI-t már telepítenie kell. [Az Azure CLI](/cli/azure/what-is-azure-cli) lehetővé teszi, hogy a parancssori terminálról származó Azure-erőforrásokkal dolgozzon. 

Az Azure-ba való [`az login`](/cli/azure/reference-index#az-login) bejelentkezéshez futtassa a következő parancsot:

```azurecli
az login
```

Kövesse a terminál on utasításokat, hogy jelentkezzen be az Azure-fiókba. Ha elkészült, az előfizetések a következőképpen jelennek meg:

```
[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "00000000-0000-0000-0000-000000000000",
    "id": "00000000-0000-0000-0000-000000000000",
    "isDefault": false,
    "managedByTenants": [],
    "name": "<subscription-name>",
    "state": "Enabled",
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "user": {
      "name": "<azure-account-name>",
      "type": "user"
    }
  },
  ...
]
```

## <a name="create-postgres-database-in-azure"></a>Postgres-adatbázis létrehozása az Azure-ban

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Ebben a szakaszban hozzon létre egy Azure-adatbázist a PostgreSQL-kiszolgálóhoz és -adatbázishoz. A kezdéshez `db-up` telepítse a bővítményt a következő paranccsal:

```azurecli
az extension add --name db-up
```

Hozza létre a Postgres [`az postgres up`](/cli/azure/ext/db-up/postgres?view=azure-cli-latest#ext-db-up-az-postgres-up) adatbázist az Azure-ban a paranccsal, ahogy az a következő példában látható. Cserélje le * \<a postgresql-név>* *egyedi* névre (a kiszolgálóvégpont *https://\<postgresql-név>.postgres.database.azure.com*). A * \<rendszergazdai felhasználónév>* és * \<a rendszergazdai jelszó>* esetén adja meg az adatbázis-rendszergazdai fiók hitelesítő adatait.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Ez a parancs eltarthat egy ideig, mert a következőket teszi:

- Létrehoz egy `myResourceGroup`, ha nem létező erőforráscsoportot. A(z) `--resource-group` nem kötelező.
- Postgres-kiszolgálót hoz létre a rendszergazda felhasználóval.
- Létrehoz `pollsdb` egy adatbázist.
- Hozzáférést biztosít a helyi IP-címről.
- Lehetővé teszi a hozzáférést az Azure-szolgáltatásokból.
- Hozzon létre egy `pollsdb` olyan felhasználót, aki hozzáfér az adatbázishoz.

Az összes lépést külön-külön `az postgres` is `psql`megteheti `az postgres up` más parancsokkal és a , de mindegyiket egy lépésben végzi el.

Amikor a parancs befejeződik, keresse meg az adatbázis-felhasználót létrehozó parancsfájlt a felhasználónévvel `root` és jelszóval, `Pollsdb1`amelyet később az adatbázishoz való csatlakozáshoz fog használni:

```
Successfully Connected to PostgreSQL.
Ran Database Query: `CREATE USER root WITH ENCRYPTED PASSWORD 'Pollsdb1'`
Ran Database Query: `GRANT ALL PRIVILEGES ON DATABASE pollsdb TO root`
```

<!-- not all locations support az postgres up -->
> [!TIP]
> A Postgres-kiszolgáló helyének megadásához `--location <location-name>`adja `<location_name>` meg az argumentumot, ahol az Azure egyik [régiója.](https://azure.microsoft.com/global-infrastructure/regions/) A parancs segítségével beszerezheti az [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) előfizetéshez elérhető régiókat.

## <a name="deploy-the-app-service-app"></a>Az App Service-alkalmazás telepítése

Ebben a szakaszban hozza létre az App Service-alkalmazást. Ezt az alkalmazást csatlakoztatja a létrehozott Postgres adatbázishoz, és telepíti a kódot.

### <a name="create-the-app-service-app"></a>Az App Service-alkalmazás létrehozása

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Ez a parancs eltarthat egy ideig, mert a következőket teszi:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Automatikusan létrehoz egy erőforráscsoportot.
- Létrehozza az App Service-terv *myAppServicePlan* alapszintű (B1) rétegben, ha nem létezik. `--plan`és `--sku` nem kötelező.
- Létrehozza az App Service-alkalmazást, ha nem létezik.
- Engedélyezi az alapértelmezett naplózást az alkalmazáshoz, ha még nincs engedélyezve.
- Feltölti a tárházat a ZIP-telepítés használatával, engedélyezve van a buildautomatizálás.

A központi telepítés befejezése után megjelenik egy JSON-kimenet, mint például a következő:

```json
{
  "URL": "http://<app-name>.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "<app-resource-group>",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
```

Másolja az * \<alkalmazás-erőforráscsoport>* értékét. Szüksége van rá, hogy később konfigurálja az alkalmazást. 

> [!TIP]
> Ugyanezt a parancsot később is használhatja a módosítások üzembe helyezéséhez, és azonnal engedélyezheti a diagnosztikai naplókat a következőkkel:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

A mintakód már telepítve van, de az alkalmazás még nem csatlakozik a Postgres-adatbázishoz az Azure-ban. Ezt fogod csinálni legközelebb.

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Ha helyileg futtatja az alkalmazást, beállíthatja a környezeti változókat a terminálmunkamenetben. Az Azure App Service-ben *ezt az alkalmazásbeállításokkal,* az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs használatával.

Futtassa a következő parancsot az adatbázis-kapcsolat részleteinek alkalmazásbeállításokként való megadásához. Cserélje le * \<az alkalmazásnevet>*, * \<az alkalmazás-erőforráscsoport>* és * \<a postgresql-name>* a saját értékeire. Ne feledje, `root` hogy `Pollsdb1` a felhasználói `az postgres up`hitelesítő adatokat a .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Arról, hogy a kód hogyan fér hozzá ezekhez az alkalmazásbeállításokhoz, olvassa el [az Access környezeti változóit.](how-to-configure-python.md#access-environment-variables)

### <a name="run-database-migrations"></a>Adatbázis-áttelepítések futtatása

Az Adatbázis-áttelepítések app service-ben való futtatásához nyisson meg egy SSH-munkamenetet a böngészőben úgy, hogy *https://\<alkalmazásnévre>.scm.azurewebsites.net/webssh/host:*

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Az SSH munkamenetben futtassa a következő parancsokat:

```bash
cd site/wwwroot

# Activate virtual environment
python3 -m venv venv
source venv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Tallózás az Azure alkalmazásban

Tallózással keresse meg a telepített alkalmazást AZ *URL-címmel\//\<http: app-name>.azurewebsites.net* böngészőben. Megjelenik a **Nincs szavazás.** 

Tallózással keresse meg a *\//\<http: app-name>.azurewebsites.net/admin* és jelentkezzen be az utolsó lépésben létrehozott rendszergazdai felhasználóhasználatával. Válassza a **Hozzáadás** lehetőséget a **Kérdések**csoportban, és hozzon létre néhány választási lehetőséget.

Tallózással keresse meg a telepített alkalmazást AZ *URL-címmel\//\<http: app-name>.azurewebsites.net/admin*, és hozzon létre néhány szavazási kérdést. A kérdéseket a *\//\<http: app-name>.azurewebsites.net/ címen láthatja.* 

![Python Django alkalmazás futtatása az Azure-beli App Services szolgáltatásban](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Tallózással keresse meg a telepített alkalmazást AZ *URL-címmel\//\<http: app-name>.azurewebsites.net* újra a szavazási kérdés megtekintéséhez és a kérdés megválaszolásához.

Az App Service észleli a Django-projektet *wsgi.py* a tárházban, ha `manage.py startproject` minden alkönyvtárban wsgi.py fájlt keres, amely alapértelmezés szerint létrehoz. Amikor az App Service megtalálja a fájlt, betölti a Django webalkalmazást. Arról, hogy az App Service hogyan tölti be a Python-alkalmazásokat, olvassa [el a Beépített Python-lemezkép konfigurálása című témakört.](how-to-configure-python.md)

**Gratulálok!** Python (Django) webalkalmazást futtat az Azure App Service Linux szolgáltatásában.

## <a name="develop-app-locally-and-redeploy"></a>Alkalmazás helyi fejlesztése és újratelepítés

Ebben a szakaszban a helyi környezetben fejleszti az alkalmazást, és újratelepíti a kódot az App Service-be.

### <a name="set-up-locally-and-run"></a>Helyi beállítás és futtatás

A helyi fejlesztési környezet beállításához és a mintaalkalmazás első futtatásához futtassa a következő parancsokat:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Amikor a Django webalkalmazás teljesen be van töltve, a következőhez hasonló üzenetet ad vissza:

```
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

Ugrás *a\/http: /localhost:8000* újra, hogy a szavazás kérdésére, és válaszoljon a kérdésre. A helyi Django mintaalkalmazás írja és tárolja a felhasználói adatokat egy helyi Sqlite3 adatbázisba, így nem kell aggódnia az éles adatbázis elcseszése miatt. Ha azt szeretné, hogy a fejlesztői környezet megfeleljen az Azure-környezetnek, fontolja meg egy Postgres-adatbázis helyi használatával.

A Django kiszolgáló leállításához írja be a Ctrl+C billentyűkombinációt.

### <a name="update-the-app"></a>Az alkalmazás frissítése

Csak hogy lássa, hogyan működik az alkalmazásfrissítések készítése, készítsen egy kis módosítást a alkalmazásban. `polls/models.py` Keresse meg a sort:

```python
choice_text = models.CharField(max_length=200)
```

És változtassa meg a következőre:

```python
choice_text = models.CharField(max_length=100)
```

Az adatmodell módosításával létre kell hoznia egy új Django-áttelepítést. Tegye meg a következő paranccsal:

```
python manage.py makemigrations
```

A módosításokat helyileg tesztelheti az áttelepítések futtatásával, a fejlesztői kiszolgáló futtatásával és a *\/http: /localhost:8000/admin*címen való navigálással:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Kód újratelepítése az Azure-ba

A módosítások újratelepítéséhez futtassa a következő parancsot a tárház gyökéréből:

```azurecli
az webapp up --name <app-name>
```

Az App Service észleli, hogy az alkalmazás létezik, és csak telepíti a kódot.

### <a name="rerun-migrations-in-azure"></a>Áttelepítések újrafuttatása az Azure-ban

Mivel módosította az adatmodellt, újra kell futtatnia az adatbázis-áttelepítéseket az App Service-ben. Nyisson meg egy SSH-munkamenetet a böngészőben, és navigáljon *https://\<alkalmazásnévre>.scm.azurewebsites.net/webssh/host*. Futtassa az alábbi parancsot:

```
cd site/wwwroot

# Activate the virtual environment
source venv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Alkalmazás áttekintése éles környezetben

Tallózással keresse meg *a\//\<http: app-name>.azurewebsites.net* és tekintse meg a változásokfutó élőben éles környezetben. 

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

A tárolón belülről létrehozott konzolnaplók at érheti el.

> [!TIP]
> `az webapp up`bekapcsolja az alapértelmezett naplózást. A teljesítmény miatt ez a naplózás egy idő után kikapcsol, `az webapp up` de minden alkalommal, amikor újra fut, újra bekapcsol. A kézi bekapcsoláshoz futtassa a következő parancsot:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Futtassa a következő Azure CLI parancsot a naplófolyam megtekintéséhez:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> A naplófájlokat a böngészőből `https://<app-name>.scm.azurewebsites.net/api/logs/docker`is megtekintheti a(.

Ha bármikor le szeretné állítani `Ctrl` + `C`a naplóstreamelést, írja be a következőt:

## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése az Azure Portalon

Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a létrehozott alkalmazást.

![Keresse meg a Python Django alkalmazást az Azure Portalon](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portál megjeleníti az alkalmazás **áttekintése** lapot. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt olyan alapvető felügyeleti feladatokat is végrehajthat, mint a tallózás, a leállítás, az újraindítás és a törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django alkalmazás kezelése az Azure Portal Áttekintés lapján](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a jövőben nem várható, hogy ezekre az erőforrásokra szüksége lesz, törölje az erőforráscsoportokat a következő parancsok futtatásával:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket tanulta meg:

> [!div class="checklist"]
> * Azure-adatbázis létrehozása postgreSQL-adatbázishoz
> * Kód üzembe helyezése az Azure App Service szolgáltatásban, és csatlakozás a Postgres-hez
> * A kód frissítése és újratelepítés
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése az Azure Portalon

A következő oktatóanyagból megtudhatja, hogyan képezheti le az egyéni DNS-nevet az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni DNS-név hozzárendelése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy nézd meg más források:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)
