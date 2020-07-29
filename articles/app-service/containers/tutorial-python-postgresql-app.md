---
title: 'Oktatóanyag: Python Django-alkalmazás üzembe helyezése a postgres-mel'
description: Hozzon létre egy PostgreSQL-adatbázist tartalmazó Python-webalkalmazást, és telepítse azt az Azure-ba. Az oktatóanyag a Django keretrendszert használja, és az alkalmazás Azure App Service Linux rendszeren található.
ms.devlang: python
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- tracking-python
ms.openlocfilehash: 718c9a62cc867e5d65cc3c79e78ce3282f1037c7
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285849"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Oktatóanyag: Django-webalkalmazás üzembe helyezése a PostgreSQL-sel Azure App Service

Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy adatvezérelt Python [Django](https://www.djangoproject.com/) [Azure app Service](app-service-linux-intro.md) -webalkalmazást, és hogyan csatlakoztatható egy Azure Database for postgres-adatbázishoz. A App Service egy jól méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt.

Ebben az oktatóanyagban az Azure CLI használatával végezheti el a következő feladatokat:

> [!div class="checklist"]
> * A kezdeti környezet beállítása a Python és az Azure CLI használatával
> * Azure Database for PostgreSQL-adatbázis létrehozása
> * Kód üzembe helyezése Azure App Service és kapcsolódás a postgres-hez
> * A kód frissítése és újbóli üzembe helyezése
> * Diagnosztikai naplók megtekintése
> * A webalkalmazás kezelése a Azure Portalban

## <a name="set-up-your-initial-environment"></a>A kezdeti környezet beállítása

1. Rendelkeznie kell aktív előfizetéssel rendelkező Azure-fiókkal. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Telepítse a <a href="https://www.python.org/downloads/" target="_blank">Python 3,6-es vagy újabb</a>verzióját.
1. Telepítse az <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 vagy újabb verzióját, amellyel az Azure-erőforrások kiépítéséhez és konfigurálásához bármilyen rendszerhéjban parancsokat futtathat.

Nyisson meg egy terminált, és győződjön meg arról, hogy a Python verziója 3,6 vagy újabb:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Győződjön meg arról, hogy az Azure CLI verziója 2.0.80 vagy magasabb:

```azurecli
az --version
```

Ezután jelentkezzen be az Azure-ba a CLI használatával:

```azurecli
az login
```

Ez a parancs egy böngészőt nyit meg a hitelesítő adatok összegyűjtéséhez. A parancs befejeződése után az előfizetésekkel kapcsolatos információkat tartalmazó JSON-kimenet jelenik meg.

Miután bejelentkezett, futtathatja az Azure-parancsokat az Azure CLI-vel, hogy az előfizetésében lévő erőforrásokkal működjön.


## <a name="clone-or-download-the-sample-app"></a>A minta alkalmazás klónozása vagy letöltése

# <a name="git-clone"></a>[Git-klón](#tab/clone)

A minta tárház klónozása:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Ezután nyissa meg a mappát:

```terminal
cd djangoapp
```

# <a name="download"></a>[Letöltés](#tab/download)

Keresse fel [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) a **klónozott**elemet, majd válassza a **zip letöltése**lehetőséget. 

Csomagolja ki a ZIP-fájlt egy *djangoapp*nevű mappába. 

Ezután nyisson meg egy terminál ablakot a *djangoapp* mappában.

---

A djangoapp minta tartalmazza az adatvezérelt Django-lekérdezési alkalmazást, amelyet az [első Django-alkalmazásnak](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) a Django dokumentációjában történő megírásával talál. Az elkészült alkalmazást az Ön kényelme érdekében itt találja.

A minta úgy is módosul, hogy éles környezetben fusson, például App Service:

- Az üzemi beállítások a *azuresite/Production. file.* fájlba kerülnek. A fejlesztés részletei a *azuresite/Settings.* a. a.
- Az alkalmazás éles beállításokat használ, ha a `DJANGO_ENV` környezeti változó "éles" értékre van állítva. Ezt a környezeti változót később az oktatóanyagban fogja létrehozni, a PostgreSQL-adatbázis konfigurációjában használt többivel együtt.

Ezek a módosítások a Django konfigurálására szolgálnak az éles környezetben való futtatáshoz, és nem kifejezetten App Service. További információ: [Django Deployment ellenőrzőlista](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-postgres-database-in-azure"></a>Postgres-adatbázis létrehozása az Azure-ban

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Telepítse a `db-up` bővítményt az Azure CLI-hez:

```azurecli
az extension add --name db-up
```

Ha a `az` parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van [a kezdeti környezet beállítása](#set-up-your-initial-environment)című témakörben leírtak szerint.

Ezután hozza létre a postgres-adatbázist az Azure-ban a [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) paranccsal:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen4_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- Cserélje le az értékét az *\<postgres-server-name>* összes Azure-beli egyedi névre (a kiszolgálói végpont `https://\<postgres-server-name>.postgres.database.azure.com` ). A megfelelő minta a vállalat nevének és egy másik egyedi érték kombinációjának használata.
- És rendszer esetén a *\<admin-username>* *\<admin-password>* hitelesítő adatok megadásával hozzon létre egy rendszergazdai felhasználót ehhez a postgres-kiszolgálóhoz.
- Az itt használt B_Gen4_1 (alapszintű, Gen4, 1 Core) [árképzési szint](../../postgresql/concepts-pricing-tiers.md) a legkevésbé költséges. Éles adatbázisok esetében hagyja ki az `--sku-name` argumentumot, hogy ehelyett a GP_Gen5_2 (általános célú, Gen 5, 2 magok) szintet használja.

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

- Hozzon létre egy nevű [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) `DjangoPostgres-tutorial-rg` , ha még nem létezik.
- Hozzon létre egy postgres-kiszolgálót.
- Hozzon létre egy alapértelmezett rendszergazdai fiókot egyedi felhasználónévvel és jelszóval. (A saját hitelesítő adatainak megadásához használja `--admin-user` a `--admin-password` parancsot a és argumentumokkal `az postgres up` .)
- Hozzon létre egy `pollsdb` adatbázist.
- Engedélyezze a hozzáférést a helyi IP-címről.
- Az Azure-szolgáltatásokból való hozzáférés engedélyezése.
- Hozzon létre egy adatbázis-felhasználót, amely hozzáfér az `pollsdb` adatbázishoz.

Az összes lépést külön is végrehajthatja más `az postgres` és más `psql` parancsokkal, de `az postgres up` az összes lépést együtt hajtja végre.

A parancs befejeződése után egy olyan JSON-objektumot ad vissza, amely különböző kapcsolódási karakterláncokat tartalmaz az adatbázishoz a kiszolgáló URL-címével együtt, egy generált felhasználónevet (például " joyfulKoala@msdocs-djangodb-12345 ") és egy GUID-jelszót. Másolja a felhasználónevet és a jelszót egy ideiglenes szövegfájlba, ahogy az oktatóanyag későbbi részében szüksége lesz rájuk.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, az egyik [Azure-régióhoz](https://azure.microsoft.com/global-infrastructure/regions/)is beállítható. Az előfizetéshez elérhető régiókat a paranccsal érheti el [`az account list-locations`](/cli/azure/account#az-account-list-locations) . Éles alkalmazások esetében ugyanazon a helyen helyezze el az adatbázist és az alkalmazást.

## <a name="deploy-the-code-to-azure-app-service"></a>A kód üzembe helyezése Azure App Service

Ebben a szakaszban az App Host alkalmazást App Service alkalmazásban hozza létre, kapcsolja össze az alkalmazást a postgres-adatbázissal, majd telepítse a kódot a gazdagépre.

### <a name="create-the-app-service-app"></a>A App Service alkalmazás létrehozása

Győződjön meg arról, hogy a terminálon a tárház gyökerében ( `djangoapp` ) található, amely tartalmazza az alkalmazás kódját.

Hozzon létre egy App Service alkalmazást (a gazdagép folyamatát) a következő [`az webapp up`](/cli/azure/webapp#az-webapp-up) paranccsal:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Az `--location` argumentum esetében ugyanazt a helyet használja, mint az előző szakaszban található adatbázishoz.
- Cserélje le *\<app-name>* az értékét az összes Azure-beli egyedi névre (a kiszolgálói végpont `https://\<app-name>.azurewebsites.net` ). A következőhöz engedélyezett karakterek:, *\<app-name>* `A` - `Z` `0` - `9` és `-` . Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.

Ez a parancs a következő műveleteket hajtja végre, ami eltarthat néhány percig:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Ha még nem létezik, hozza létre az [erőforráscsoportot](../../azure-resource-manager/management/overview.md#terminology) . (Ebben a parancsban ugyanazt az erőforráscsoportot használja, amelyben korábban létrehozta az adatbázist.)
- Ha nem létezik, hozza létre [app Service](../overview-hosting-plans.md) az alapszintű díjszabási csomag (B1) *DjangoPostgres* . `--plan`és `--sku` nem kötelező.
- Ha nem létezik, hozza létre a App Service alkalmazást.
- Az alkalmazás alapértelmezett naplózásának engedélyezése, ha még nincs engedélyezve.
- Töltse fel az adattárat a ZIP-telepítéssel a Build Automation használatával.

Sikeres telepítés esetén a parancs a következő példához hasonló JSON-kimenetet hoz létre:

![Példa az WebApp up Command output](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

> [!TIP]
> Számos Azure CLI-parancs gyorsítótárazza az általános paramétereket, például az erőforráscsoport nevét és a App Service tervet a fájlba *. Azure/config*. Ennek eredményeképpen nem kell megadnia ugyanazt a paramétert a későbbi parancsokkal. Ha például a módosítások elvégzése után szeretné újból üzembe helyezni az alkalmazást, egyszerűen futtathatja `az webapp up` azokat paraméterek nélkül. A CLI-bővítményből származó parancsok, például a `az postgres up` (z) azonban nem a gyorsítótárban jelennek meg, ezért az erőforráscsoport és a hely megadására van szükség a következővel: `az webapp up` .

> [!NOTE]
> Ha ezen a ponton megpróbálja felkeresni az alkalmazás URL-címét, akkor a "DisallowedHost at/" hibaüzenet jelenik meg. Ez a hiba azért fordul elő, mert még nem konfigurálta az alkalmazást a korábban tárgyalt éles beállítások használatára, amelyet a következő szakaszban végez.

### <a name="configure-environment-variables-to-connect-the-database"></a>Környezeti változók konfigurálása az adatbázishoz való kapcsolódáshoz

Ha a kód már telepítve van a App Servicere, a következő lépés az alkalmazás összekötése a postgres-adatbázissal az Azure-ban.

Az alkalmazás kódja számos környezeti változóban várja az adatbázis-információk megkeresését. A környezeti változók App Serviceban történő beállításához az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) paranccsal hozhatja létre az "Alkalmazásbeállítások" értéket.

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Cserélje le a *\<postgres-server-name>* nevet a paranccsal korábban használt névre `az postgres up` .
- Cserélje le a *\<username>* és a *\<password>* kapcsolót arra a hitelesítő adatokra, amelyet a parancs is létrehoz.
- Az erőforráscsoport és az alkalmazás neve a *. Azure/config* fájl gyorsítótárazott értékeiből származik.
- A parancs a (z),,, `DJANGO_ENV` `DBHOST` `DBNAME` `DBUSER` és `DBPASS` az alkalmazás kódjának megfelelően hozza létre a beállításokat.
- A Python-kódban ezeket a beállításokat környezeti változókként, például a következő utasításokkal érheti el `os.environ.get('DJANGO_ENV')` . További információ: [hozzáférés környezeti változókhoz](how-to-configure-python.md#access-environment-variables).

### <a name="run-django-database-migrations"></a>Django adatbázis-áttelepítések futtatása

A Django-adatbázis áttelepítése biztosítja, hogy az Azure Database-ben található PostgreSQL-séma megfeleljen a kódban leírt sémának.

1. Nyisson meg egy SSH-munkamenetet a böngészőben, és lépjen a *https:// \<app-name> . SCM.azurewebsites.net/webssh/Host* webhelyre, és jelentkezzen be az Azure-fiókja hitelesítő adataival (nem az adatbázis-kiszolgáló hitelesítő adataival).

1. Az SSH-munkamenetben futtassa a következő parancsokat (a **CTRL** + **SHIFT**V használatával is beilleszthet parancsokat + **V**):

    ```bash
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. A `createsuperuser` parancs a rendszergazdai hitelesítő adatok megadását kéri. Ebben az oktatóanyagban használja az alapértelmezett felhasználónevet `root` , nyomja le az **ENTER** billentyűt az e-mail-címre, hogy üresen hagyja, és adja meg a `Pollsdb1` jelszót.
    
### <a name="create-a-poll-question-in-the-app"></a>Lekérdezési kérdés létrehozása az alkalmazásban

1. A böngészőben nyissa meg a *http: \/ / \<app-name> . azurewebsites.net*URL-címet. Az alkalmazásnak meg kell jelennie a "nincs elérhető lekérdezés" üzenetnek, mert az adatbázisban még nincsenek adott lekérdezések.

1. Keresse meg a *http: \/ / \<app-name> . azurewebsites.net/admin*parancsot. Jelentkezzen be a rendszergazdai hitelesítő adatokkal az előző szakaszban ( `root` és `Pollsdb1` ). A **lekérdezések**területen válassza a **Hozzáadás** a **kérdések** mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel.

1. Tallózással keresse meg újra a *http: \/ / \<app-name> . azurewebsites.net/* , és győződjön meg arról, hogy a kérdések most már jelen vannak a felhasználó számára. Válaszoljon a kérdésekre, azonban szeretne valamilyen adathalmazt előállítani az adatbázisban.

**Gratulálunk!** Egy Python Django-webalkalmazást futtat Azure App Service Linux rendszeren, aktív postgres-adatbázissal.

> [!NOTE]
> App Service észleli a Django-projektet úgy, hogy minden almappában megkeres egy *WSGI.py* -fájlt, amely `manage.py startproject` alapértelmezés szerint létrehoz. Amikor App Service megkeresi a fájlt, betölti a Django-webalkalmazást. További információ: a [beépített Python-rendszerkép konfigurálása](how-to-configure-python.md).


## <a name="make-code-changes-and-redeploy"></a>Kód módosításának és újbóli üzembe helyezésének tétele

Ebben a szakaszban helyi módosításokat hajt végre az alkalmazáson, és újból üzembe helyezi a kódot a App Service. A folyamat során beállíthat egy olyan Python virtuális környezetet, amely támogatja a folyamatos munkát.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa a következő parancsokat egy terminálablakban. Ügyeljen arra, hogy kövesse a következő utasításokat a rendszergazda létrehozásakor:

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

```cmd
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

A webalkalmazás teljes betöltését követően a Django fejlesztői kiszolgáló a helyi alkalmazás URL-címét biztosítja a "fejlesztési kiszolgáló indítása a következő helyen" című üzenetben: http://127.0.0.1:8000/ . Lépjen ki a kiszolgálóról a CTRL-BREAK billentyűkombinációval.

![Példa a Django fejlesztői kiszolgáló kimenetére](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Tesztelje az alkalmazást helyileg a következő lépésekkel:

1. Nyissa meg a *http: \/ /localhost: 8000* -et egy böngészőben, amely a "nincs elérhető lekérdezés" üzenetet jeleníti meg. 

1. Lépjen a *http: \/ /localhost: 8000/admin* webpontra, és jelentkezzen be a korábban létrehozott rendszergazda felhasználó használatával. A **lekérdezések**területen ismét válassza a **Hozzáadás** a **kérdések** mellett lehetőséget, és hozzon létre egy lekérdezési kérdést néhány lehetőséggel. 

1. Lépjen a *http: \/ /localhost: 8000* elemre, és válaszolja meg az alkalmazás tesztelésének kérdését. 

1. A **CTRL**C billentyűkombináció lenyomásával állítsa le a Django-kiszolgálót + **C**.

Helyileg futtatva az alkalmazás egy helyi Sqlite3-adatbázist használ, és nem zavarja az éles adatbázisát. Igény szerint helyi PostgreSQL-adatbázist is használhat az éles környezet jobb szimulálása érdekében.

### <a name="update-the-app"></a>Az alkalmazás frissítése

A-ben `polls/models.py` Keresse meg a kezdetű sort, `choice_text` és módosítsa a `max_length` paramétert 100-re:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Mivel módosította az adatmodellt, hozzon létre egy új Django-áttelepítést, és telepítse át az adatbázist:

```
python manage.py makemigrations
python manage.py migrate
```

Futtassa újra a fejlesztői kiszolgálót, `python manage.py runserver` és tesztelje az alkalmazást a *http: \/ /localhost: 8000/admin*címen:

Állítsa le újra a Django webkiszolgálót a **CTRL C billentyűkombinációval** + **C**.

### <a name="redeploy-the-code-to-azure"></a>A kód újbóli üzembe helyezése az Azure-ban

Futtassa a következő parancsot az adattár gyökerében:

```azurecli
az webapp up
```

Ez a parancs a *. Azure/config* fájlban gyorsítótárazott paramétereket használja. Mivel App Service észleli, hogy az alkalmazás már létezik, csak újra telepíti a kódot.

### <a name="rerun-migrations-in-azure"></a>Migrálás újrafuttatása az Azure-ban

Mivel módosította az adatmodellt, újra kell futtatnia az adatbázis-áttelepítést App Serviceban.

Nyisson meg egy SSH-munkamenetet a böngészőben a *https:// \<app-name> . SCM.azurewebsites.net/webssh/Host*. Ezután futtassa le a következő parancsokat:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Alkalmazás áttekintése éles környezetben

Keresse meg a *http: \/ / \<app-name> . azurewebsites.net* , és tesztelje újra az alkalmazást éles környezetben. (Mivel csak az adatbázis mező hosszát módosította, a módosítás csak akkor észlelhető, ha egy kérdés létrehozásakor megpróbál hosszabb választ adni.)

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

Elérheti az Azure-beli alkalmazást futtató tárolón belül létrehozott konzol naplóit.

Az alábbi Azure CLI-parancs futtatásával tekintheti meg a napló streamjét. Ez a parancs a *. Azure/config* fájlban gyorsítótárazott paramétereket használja.

```azurecli
az webapp log tail
```

Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

Ha bármikor le szeretné állítani a naplózási adatfolyamot, írja be a **CTRL C billentyűt** + **C**.

> [!NOTE]
> A naplófájlokat a böngészőből is ellenőrizheti `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
>
> `az webapp up`az alapértelmezett naplózás bekapcsolása. A teljesítmény szempontjából ez a naplózás egy kis idő elteltével kikapcsolja magát, de minden alkalommal újra visszavált `az webapp up` . A manuális bekapcsolásához futtassa a következő parancsot:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>Az alkalmazás kezelése a Azure Portalban

A [Azure Portal](https://portal.azure.com)keresse meg az alkalmazás nevét, és válassza ki az alkalmazást az eredmények között.

![Navigáljon a Python Django alkalmazáshoz a Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** oldalát jeleníti meg, amely általános teljesítményt nyújt. Itt olyan alapszintű felügyeleti feladatokat is elvégezhet, mint a Tallózás, Leállítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![A Python Django-alkalmazás kezelése a Azure Portal áttekintés lapján](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha meg szeretné tartani az alkalmazást, vagy folytassa a következő oktatóanyaggal, ugorjon a [következő lépésekre](#next-steps). Ellenkező esetben a folyamatos költségek elkerülése érdekében törölheti az ehhez az oktatóanyaghoz tartozó erőforráscsoport-létrehozási csoportot:

```azurecli
az group delete
```

A parancs az *. Azure/config* fájlban gyorsítótárazott erőforráscsoport-nevet használja. Az erőforráscsoport törlésével felszabadítja és törli az abban található összes erőforrást is.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan képezhető le egyéni DNS-név az alkalmazáshoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: egyéni DNS-név leképezése az alkalmazáshoz](../app-service-web-tutorial-custom-domain.md)

Ismerje meg, hogyan futtatja App Service egy Python-alkalmazást:

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](how-to-configure-python.md)
