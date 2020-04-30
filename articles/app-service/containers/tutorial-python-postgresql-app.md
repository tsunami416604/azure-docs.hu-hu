---
title: 'Oktatóanyag: a Python (Django) üzembe helyezése a postgres-mel'
description: Megtudhatja, hogyan hozhat létre egy PostgreSQL-adatbázissal rendelkező Python-alkalmazást, és hogyan telepítheti azt Azure App Service Linux rendszeren. Az oktatóanyag egy Django minta alkalmazást használ a bemutatóhoz.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085756"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Python-(Django-) webalkalmazás üzembe helyezése a PostgreSQL-sel Azure App Service

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy adatvezérelt Python-(Django-) webalkalmazást, hogy [Azure app Service](app-service-linux-intro.md) és egy Azure Database for PostgreSQL-adatbázishoz kapcsolódjon. A App Service egy jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt.

![Python Django-webalkalmazás üzembe helyezése Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Database for PostgreSQL-adatbázis létrehozása
> * Kód üzembe helyezése Azure App Service és kapcsolódás a postgres-hez
> * A kód frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése a Azure Portalban

A cikkben ismertetett lépéseket macOS, Linux vagy Windows rendszeren is követheti.

## <a name="install-dependencies"></a>Függőségek telepítése

Az oktatóanyag elindítása előtt:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Telepítse az [Azure CLI](/cli/azure/install-azure-cli)-t.
- Telepítse a [git](https://git-scm.com/)-t.
- Telepítse a [Python 3](https://www.python.org/downloads/)programot.

## <a name="clone-the-sample-app"></a>A mintaalkalmazás klónozása

Egy terminál ablakban futtassa a következő parancsokat a minta alkalmazás-tárház klónozásához, és váltson a tárház gyökerére:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

A djangoapp-minta adattár tartalmazza az adatvezérelt [Django](https://www.djangoproject.com/) -lekérdezési alkalmazást, amelyet az [első Django-alkalmazásnak](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) a Django dokumentációjában való megírásával talál. Itt az Ön kényelmét szolgálja.

## <a name="prepare-app-for-app-service"></a>Alkalmazás előkészítése App Service

A számos Python webes keretrendszerhez hasonlóan a Django is [szükség van bizonyos változásokra, mielőtt az üzemi kiszolgálón fussanak](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/), és nem különböznek a app Service. Módosítania kell a beállításokat, és hozzá kell adnia néhány beállítást az alapértelmezett *azuresite/Settings. file.* a fájlhoz, hogy az alkalmazás a app Service való üzembe helyezése után is működjön. 

Tekintse meg a *azuresite/Production.* a (z), amely a app Service szükséges konfigurációját használja. Röviden, a következő műveleteket végzi el:

- Az összes beállítás öröklése a *azuresite/Settings.* másol.
- Adja hozzá a App Service alkalmazás teljes tartománynevét az engedélyezett gazdagépekhez. 
- A [WhiteNoise](https://whitenoise.evans.io/en/stable/) használatával engedélyezheti a statikus fájlok éles környezetben való kiszolgálását, mivel a Django alapértelmezés szerint nem szolgálja ki a statikus fájlokat az éles környezetben. A WhiteNoise csomag már szerepel a *követelmények. txt fájlban*.
- Adja hozzá a PostgreSQL-adatbázis konfigurációját. Alapértelmezés szerint a Django a Sqlite3-t használja adatbázisként, de az éles alkalmazások esetében nem megfelelő. A [psycopg2-Binary](https://pypi.org/project/psycopg2-binary/) csomag már szerepel a *követelmények. txt fájlban*.
- A postgres-konfiguráció környezeti változókat használ. Később megtudhatja, hogyan állíthatja be a környezeti változókat a App Serviceban.

a *azuresite/Production.* másolási funkció a tárházban található a kényelem érdekében, de az alkalmazás még nem használja. Annak érdekében, hogy a beállításai a App Serviceban legyenek használatban, két fájlt kell konfigurálnia, *Manage.py* és *azuresite/WSGI. file.*

- A *Manage.py*módosítsa a következő sort:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    A következő kódra:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    A környezeti változót `DJANGO_ENV` később a app Service alkalmazás konfigurálásakor fogja beállítani.

- A *azuresite/WSGI.* a-ben végezze el ugyanezt a módosítást.

    App Service a *Manage.py* használatával futtatja az adatbázis-áttelepítést, és a app Service a *azuresite/WSGI. a. a.* a Django-alkalmazást használja éles környezetben. Mindkét fájlban ez a változás biztosítja, hogy az éles beállítások mindkét esetben használatban legyenek.

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Az Azure CLI-t már telepítve kell lennie. Az [Azure CLI](/cli/azure/what-is-azure-cli) -vel a parancssori terminál használatával dolgozhat az Azure-erőforrásokkal. 

Az Azure-ba való bejelentkezéshez futtassa [`az login`](/cli/azure/reference-index#az-login) a következő parancsot:

```azurecli
az login
```

A terminál utasításait követve jelentkezzen be az Azure-fiókjába. Ha elkészült, az előfizetések JSON formátumban jelennek meg a terminál kimenetében.

## <a name="create-postgres-database-in-azure"></a>Postgres-adatbázis létrehozása az Azure-ban

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Ebben a szakaszban egy Azure Database for PostgreSQL-kiszolgálót és-adatbázist hoz létre. Az indításhoz telepítse a `db-up` bővítményt a következő paranccsal:

```azurecli
az extension add --name db-up
```

Hozza létre a postgres-adatbázist az Azure [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) -ban a paranccsal, az alábbi példában látható módon. Cserélje le * \<a PostgreSQL-Name>* *egyedi* névre (a kiszolgálói végpont *https://\<PostgreSQL-Name>. postgres.database.Azure.com*). A * \<admin-username>* és * \<a admin-password>* esetében határozza meg a hitelesítő adatokat, hogy rendszergazda felhasználót hozzon létre ehhez a postgres-kiszolgálóhoz.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Ez a parancs hosszabb időt is igénybe vehet, mert a következő műveleteket végzi el:

- Létrehoz egy [resource group](../../azure-resource-manager/management/overview.md#terminology) nevű `myResourceGroup`erőforráscsoportot, ha az nem létezik. Minden Azure-erőforrásnak ilyen típusúnak kell lennie. A(z) `--resource-group` nem kötelező.
- Létrehoz egy postgres-kiszolgálót a rendszergazda felhasználóval.
- Létrehoz egy `pollsdb` adatbázist.
- Engedélyezi a hozzáférést a helyi IP-címről.
- Engedélyezi az Azure-szolgáltatásokból való hozzáférést.
- Hozzon létre egy adatbázis-felhasználót, `pollsdb` amely hozzáfér az adatbázishoz.

A lépéseket külön is végrehajthatja más `az postgres` parancsokkal, `psql` `az postgres up` és az összes lépést megteheti egy lépésben.

A parancs befejeződése után keresse meg a által használt kimeneti sorokat `Ran Database Query:`. Az Ön számára létrehozott adatbázis-felhasználót, a felhasználónevet `root` és a jelszót `Pollsdb1`is megjeleníti. Ezeket később fogja használni az alkalmazás adatbázishoz való összekapcsolásához.

<!-- not all locations support az postgres up -->
> [!TIP]
> A postgres-kiszolgáló helyének megadásához adja meg az `--location <location-name>`argumentumot `<location_name>` , ahol az az egyik [Azure-régió](https://azure.microsoft.com/global-infrastructure/regions/). Az előfizetéshez elérhető régiókat a [`az account list-locations`](/cli/azure/account#az-account-list-locations) paranccsal érheti el.

## <a name="deploy-the-app-service-app"></a>A App Service alkalmazás üzembe helyezése

Ebben a szakaszban a App Service alkalmazást hozza létre. Ehhez az alkalmazáshoz a létrehozott postgres-adatbázishoz kell csatlakoznia, és telepítenie kell a kódot.

### <a name="create-the-app-service-app"></a>A App Service alkalmazás létrehozása

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Győződjön meg arról, hogy visszatért az adattár gyökerébe`djangoapp`(), mert az alkalmazás a címtárból lesz telepítve.

Hozzon létre egy App Service alkalmazást [`az webapp up`](/cli/azure/webapp#az-webapp-up) a paranccsal, ahogy az az alábbi példában is látható. Cserélje le * \<az App-Name>t* *egyedi* névre (a kiszolgálói végpont *https://\<az App-Name>. azurewebsites.net*). `A` - `Z`Az * \<alkalmazás neve>* `9`engedélyezett karakterek:, és `-` `0` -

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Ez a parancs hosszabb időt is igénybe vehet, mert a következő műveleteket végzi el:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Automatikusan létrehoz egy [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) .
- Létrehozza a [app Service terv](../overview-hosting-plans.md) *MyAppServicePlan* az alapszintű díjszabásban (B1), ha az nem létezik. `--plan`és `--sku` nem kötelező.
- Ha nem létezik, akkor a App Service alkalmazást hozza létre.
- Az alkalmazás alapértelmezett naplózásának engedélyezése, ha még nincs engedélyezve.
- Feltölti az adattárat a ZIP-telepítéssel, és lehetővé teszi a Build Automation használatát.

Az üzembe helyezés befejezése után a következőhöz hasonló JSON-kimenet jelenik meg:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Másolja az * \<app-Resource-Group>* értékét. Szüksége lesz rá az alkalmazás későbbi konfigurálásához. 

> [!TIP]
> A későbbiekben ugyanezzel a paranccsal telepítheti a módosításokat, és azonnal engedélyezheti a diagnosztikai naplók használatát a következővel:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

A mintakód már telepítve van, de az alkalmazás még nem csatlakozik a postgres-adatbázishoz az Azure-ban. Ezt a következő lépéssel teheti meg.

### <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Amikor helyileg futtatja az alkalmazást, beállíthatja a környezeti változókat a terminál-munkamenetben. App Service az *alkalmazás beállításaival*az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) paranccsal végezheti el.

Futtassa az alábbi parancsot az adatbázis-kapcsolat részleteinek megadásához az alkalmazás beállításaiként. Cserélje * \<le az alkalmazás neve>*, * \<az App-Resource-Group>* és * \<a PostgreSQL-Name>* a saját értékeire. Ne feledje, hogy a felhasználói `root` hitelesítő adatok és `Pollsdb1` az Ön `az postgres up`számára lettek létrehozva.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

További információ arról, hogy a kód hogyan fér hozzá ezekhez az alkalmazásokhoz, lásd: [hozzáférés környezeti változókhoz](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Adatbázis-áttelepítés futtatása

Ha App Service adatbázis-áttelepítést szeretne futtatni, nyisson meg egy SSH-munkamenetet a böngészőben *az\<https://App-name>. SCM.azurewebsites.net/webssh/Host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Az SSH-munkamenetben futtassa a következő parancsokat:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Tallózással keresse meg az üzembe helyezett alkalmazást URL-címmel *http:\//\<app-Name>. azurewebsites.net* egy böngészőben. Ekkor az üzenet **nem érhető**el. 

Tallózással keresse meg a *http:\//\<app-Name>. azurewebsites.net/admin* parancsot, és jelentkezzen be az utolsó lépésben létrehozott rendszergazda felhasználó használatával. Válassza a **Hozzáadás** a **kérdések**mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

Keresse meg az üzembe helyezett alkalmazást URL-címmel *http\//\<: app-Name>. azurewebsites.net/admin*, és hozzon létre néhány lekérdezési kérdést. A kérdéseket a *http:\//\<app-Name>. azurewebsites.net/* címen érheti el. 

![Python Django-alkalmazás futtatása App Services az Azure-ban](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Keresse meg az üzembe helyezett alkalmazást URL-címmel *http\//\<: app-Name>. azurewebsites.net* újra a lekérdezési kérdés megtekintéséhez és a kérdés megválaszolásához.

A App Service egy Django-projektet észlel a tárházban úgy, hogy egy *WSGI.py* -fájlt keres minden alkönyvtárban, amely `manage.py startproject` alapértelmezés szerint létrehoz. Amikor App Service megkeresi a fájlt, betölti a Django-webalkalmazást. További információ a App Service Python-alkalmazások betöltéséről: a [beépített Python-rendszerkép konfigurálása](how-to-configure-python.md).

**Gratulálok!** Python-(Django-) webalkalmazást futtat Azure App Service Linux rendszeren.

## <a name="develop-app-locally-and-redeploy"></a>Alkalmazás helyi fejlesztése és újbóli üzembe helyezése

Ebben a szakaszban a helyi környezetben fejleszti az alkalmazást, és újból üzembe helyezi a kódot a App Service.

### <a name="set-up-locally-and-run"></a>Helyi és Futtatás beállítása

A helyi fejlesztési környezet beállításához és a minta alkalmazás első futtatásához futtassa a következő parancsokat:

# <a name="bash"></a>[bash](#tab/bash)

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

# <a name="cmd"></a>[CMD](#tab/cmd)

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

Ha a Django-webalkalmazás teljesen be van töltve, az a következő üzenethez hasonló módon tér vissza:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Nyissa meg a *http:\//localhost: 8000* -et egy böngészőben. Ekkor az üzenet **nem érhető**el. 

Lépjen a *http:\//localhost: 8000/admin* webszolgáltatásra, és jelentkezzen be az utolsó lépésben létrehozott rendszergazdai felhasználóval. Válassza a **Hozzáadás** a **kérdések**mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

![Python Django-alkalmazás futtatása helyileg App Services](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Lépjen a *http:\//localhost: 8000* gombra a lekérdezési kérdés megtekintéséhez és a kérdés megválaszolásához. A helyi Django egy helyi Sqlite3-adatbázisba írja és tárolja a felhasználói adatait, így nem kell aggódnia az éles adatbázis felgyorsításával kapcsolatban. Ahhoz, hogy a fejlesztési környezet megfeleljen az Azure-környezetnek, érdemes inkább helyileg használni a postgres-adatbázist.

A Django-kiszolgáló leállításához írja be a CTRL + C billentyűkombinációt.

### <a name="update-the-app"></a>Az alkalmazás frissítése

Ha szeretné megtekinteni, hogyan működik az `polls/models.py`alkalmazások frissítései, végezze el a kis mértékű változást. Keresse meg a sort:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

És módosítsa a következőre:

```python
choice_text = models.CharField(max_length=100)
```

Az adatmodell módosításával létre kell hoznia egy új Django-áttelepítést. Ezt a következő paranccsal teheti meg:

```
python manage.py makemigrations
```

A módosításokat helyileg is tesztelheti, ha futtatja az áttelepítést, futtatja a fejlesztési kiszolgálót, és navigál a *http:\//localhost: 8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Kód újbóli üzembe helyezése az Azure-ban

A módosítások újbóli üzembe helyezéséhez futtassa a következő parancsot az adattár gyökeréből:

```azurecli
az webapp up --name <app-name>
```

App Service észleli, hogy az alkalmazás létezik, és csak telepíti a kódot.

### <a name="rerun-migrations-in-azure"></a>Migrálás újrafuttatása az Azure-ban

Mivel módosította az adatmodellt, újra kell futtatnia az adatbázis-áttelepítést App Serviceban. Nyisson meg egy SSH-munkamenetet a böngészőben, és navigáljon a *https://\<>. SCM.azurewebsites.net/webssh/Host nevű alkalmazáshoz*. Futtassa az alábbi parancsot:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Alkalmazás áttekintése éles környezetben

Tallózással keresse meg a *http:\//\<app-Name>. azurewebsites.net* , és tekintse meg az éles környezetben futó változásokat. 

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

Elérheti a tárolón belül létrehozott konzol naplóit.

> [!TIP]
> `az webapp up`az alapértelmezett naplózás bekapcsolása. A teljesítmény szempontjából ez a naplózás egy kis idő elteltével kikapcsolja magát, de minden alkalommal `az webapp up` újra visszavált. A manuális bekapcsolásához futtassa a következő parancsot:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Az alábbi Azure CLI-parancs futtatásával tekintheti meg a log streamet:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

> [!NOTE]
> A naplófájlokat a böngészőből is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a következőt `Ctrl` + `C`:.

## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése a Azure Portalban

A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a létrehozott alkalmazást.

![Navigáljon a Python Django alkalmazáshoz a Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** lapját jeleníti meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt olyan alapszintű felügyeleti feladatokat is elvégezhet, mint a Tallózás, Leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django-alkalmazás kezelése a Azure Portal áttekintés lapján](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem várható, hogy a jövőben szüksége lesz ezekre az erőforrásokra, törölje az erőforráscsoportot a következő parancsok futtatásával:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta a következőket:

> [!div class="checklist"]
> * Azure Database for PostgreSQL-adatbázis létrehozása
> * Kód üzembe helyezése Azure App Service és kapcsolódás a postgres-hez
> * A kód frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése a Azure Portalban

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhető le egyéni DNS-név az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Vagy tekintse meg a többi erőforrást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)
