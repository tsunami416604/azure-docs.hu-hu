---
title: PHP- és MySQL-webalkalmazás létrehozása az Azure-ban | Microsoft Docs
description: Megismerheti, hogyan tehet szert egy olyan, az Azure-ban működő PHP-alkalmazásra, amely csatlakozik egy MySQL-adatbázishoz az Azure-ban.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 28c50aea9aaad1b9b18fb6b3034617d10beea7ec
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-build-a-php-and-mysql-web-app-in-azure"></a>Oktatóanyag: PHP- és MySQL-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd: [PHP- és MySQL-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben](./containers/tutorial-php-mysql-app.md).
>

Az [Azure Web Apps](app-service-web-overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy PHP-webalkalmazást az Azure-ban, és hogyan csatlakoztathatja azt egy MySQL-adatbázishoz. Az oktatóanyag eredménye egy, a Linux App Service-ben futó [Laravel](https://laravel.com/)-alkalmazás lesz.

![Az Azure App Service-ben futó PHP-alkalmazás](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * PHP-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a PHP 5.6.4-es vagy újabb verzióját](http://php.net/downloads.php)
* [Telepítse a Composert](https://getcomposer.org/doc/00-intro.md)
* Engedélyezze a következő PHP-bővítményeket, amelyre a Laravelnek szüksége van: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [Telepítse és indítsa el a MySQL-t](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>A helyi MySQL előkészítése

Ebben a lépésben egy adatbázist hoz létre a helyi MySQL-kiszolgálón, hogy felhasználhassa azt az oktatóanyagban.

### <a name="connect-to-local-mysql-server"></a>Csatlakozás a helyi MySQL-kiszolgálóhoz

Csatlakozzon a helyi MySQL-kiszolgálóhoz egy terminálablakban. Ezzel a terminálablakkal futtathatja az oktatóanyagban szereplő összes parancsot.

```bash
mysql -u root -p
```

Ha a rendszer jelszót kér, adja meg a `root` fiókhoz tartozó jelszót. Ha nem emlékszik a rendszergazdafiók jelszavára, tekintse meg a [MySQL-ben a rendszergazdai jelszó alaphelyzetbe állítását](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html) ismertető cikket.

Ha a parancs futtatása sikeres, akkor a MySQL-kiszolgáló fut. Ha nem, mindenképp a [MySQL telepítése utáni lépéseket](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html) követve indítsa el a helyi MySQL-kiszolgálót.

### <a name="create-a-database-locally"></a>Adatbázis létrehozása helyben

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql 
CREATE DATABASE sampledb;
```

A kiszolgálókapcsolat bontásához írja be a `quit` parancsot.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>PHP-alkalmazás létrehozása helyben
Ebben a lépésben egy Laravel-mintaalkalmazásra tesz szert, konfigurálja annak adatbázis-kapcsolatát, és helyileg futtatja az alkalmazást. 

### <a name="clone-the-sample"></a>A minta klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa a következő parancsot a minta tárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

A `cd` paranccsal lépjen a klónozott könyvtárra.
Telepítse a szükséges csomagokat.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL-kapcsolat konfigurálása

Hozzon létre egy *.env* nevű szövegfájlt a tárház gyökérében. Másolja az alábbi változókat a *.env* fájlba. A _&lt;root_password>_ helyőrzőt cserélje le a MySQL rendszergazdai jelszavára.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

A [Laravel-környezet konfigurációjával](https://laravel.com/docs/5.4/configuration#environment-configuration) foglalkozó cikkben tekinthet meg információt arról, hogy a Laravel hogyan használja a _.env_ fájlt.

### <a name="run-the-sample-locally"></a>A minta futtatása helyben

Futtassa a [Laravel adatbázis-migrálási parancsait](https://laravel.com/docs/5.4/migrations) az alkalmazás számára szükséges táblák létrehozásához. Ha látni szeretné a migrálások során létrejövő táblákat, tekintse meg a _database/migrations_ könyvtárat a Git-adattárban.

```bash
php artisan migrate
```

Hozzon létre egy új Laravel-alkalmazáskulcsot.

```bash
php artisan key:generate
```

Futtassa az alkalmazást.

```bash
php artisan serve
```

Egy böngészőben nyissa meg a `http://localhost:8000` oldalt. Vegyen fel néhány feladatot az oldalon.

![A PHP sikeresen csatlakozik a MySQL-hez](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

A PHP-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>MySQL létrehozása az Azure-ban

Ebben a lépésben egy MySQL-adatbázist hoz létre az [Azure Database for MySQL-ben](/azure/mysql). Később konfigurálni fogja a PHP-alkalmazást az adatbázishoz való csatlakozásra.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL-kiszolgáló létrehozása

A Cloud Shellben hozzon létre egy kiszolgálót az Azure Database for MySQL szolgáltatásban az [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) paranccsal.

Az alábbi parancsban a _&lt;mysql_server_name>_ helyőrzőt cserélje le a MySQL-kiszolgáló nevére (érvényes karakterek: `a-z`, `0-9` és `-`). Ez a név része a MySQL-kiszolgáló állomásnevének (`<mysql_server_name>.database.windows.net`), és globálisan egyedinek kell lennie.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

> [!NOTE]
> Mivel az oktatóanyag több hitelesítő adattal dolgozik, a félreértések elkerülése érdekében az `--admin-user` és az `--admin-password` helyőrző értékekre van beállítva. Éles környezet esetén kövesse az ajánlott biztonsági eljárásokat, amikor megfelelő felhasználónevet és jelszót választ az Azure-ban található MySQL-kiszolgálóhoz.
>
>

A MySQL-kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

A Cloud Shellben az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) parancs használatával hozzon létre egy tűzfalszabályt a MySQL-kiszolgáló számára az ügyfélkapcsolatok engedélyezésére.

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Az Azure Database for MySQL jelenleg nem korlátozza a kapcsolatokat kizárólag Azure-szolgáltatásokra. Mivel Azure-ban az IP-címek kiosztása dinamikusan történik, érdemes az összes IP-címet engedélyezni. Folyik az adatbázis védelmét biztosító jobb módszerek tervezése.
>
>

### <a name="connect-to-production-mysql-server-locally"></a>Helyi csatlakozás éles MySQL-kiszolgálóhoz

A helyi terminálablakban csatlakozzon az Azure-ban található MySQL-kiszolgálóhoz. A _&lt;mysql_server_name>_ helyőrzőnél használja a korábban megadott értéket. Amikor a rendszer jelszót kér, használja a _My5up3r$tr0ngPa$w0rd!_ jelszót, amelyet az adatbázis-kiszolgáló létrehozásakor adott meg.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _phpappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban. Az oktatóanyag egyszerűsége kedvéért használja a _MySQLAzure2017_ jelszót.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Bontsa a kiszolgálókapcsolat a `quit` parancs beírásával.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Az alkalmazás csatlakoztatása az Azure MySQL-hez

Ebben a lépésben csatlakoztatja a PHP-alkalmazást a MySQL-adatbázishoz, amelyet az Azure Database for MySQL-ben hozott létre.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

Az adattár gyökérkönyvtárában, hozzon létre a _. env.production_ fájlt, és másolja bele a következő változókat. A _&lt;mysql_server_name >_ helyőrzőt cserélje le a *DB_HOST* és a *DB_USERNAME* elemekben.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Mentse a módosításokat.

> [!TIP]
> A MySQL-kapcsolati adatok védelme érdekében ez a fájl már ki van zárva a Git-adattárból (lásd: _.gitignore_ az adattár gyökérkönyvtárában). Később megtudhatja, hogyan konfigurálhat környezeti változókat az App Service-ben az Azure Database for MySQL-ben található adatbázishoz való csatlakozáshoz. A környezeti változók esetében nincs szüksége a *.env* fájlra az App Service-ben.
>

### <a name="configure-ssl-certificate"></a>SSL-tanúsítvány konfigurálása

Alapértelmezés szerint az Azure Database for MySQL kikényszeríti az SSL-kapcsolatokat az ügyfelektől. Az Azure-ban található MySQL-adatbázishoz való csatlakozáshoz használja a [_.pem_ tanúsítványt, amelyet az Azure Database for MySQL](../mysql/howto-configure-ssl.md) biztosít.

Nyissa meg a _config/database.php_ fájlt, majd adja hozzá az `sslmode` és az `options` paramétereket a `connections.mysql` szakaszhoz az alábbi kódban látható módon.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

Az oktatóanyagban az egyszerűség kedvéért a `BaltimoreCyberTrustRoot.crt.pem` tanúsítvány megtalálható az adattárban. 

### <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

Futtassa a Laravel adatbázis-migrálási parancsait az _.env.production_ fájllal környezeti fájlként az Azure Database for MySQL szolgáltatásban található MySQL-adatbázis tábláinak létrehozásához. Ne feledje, hogy a _.env.production_ fájl tartalmazza a kapcsolatadatokat az Azure-ban található MySQL-adatbázishoz való csatlakozáshoz.

```bash
php artisan migrate --env=production --force
```

A _. env.production_ még nem rendelkezik érvényes alkalmazáskulccsal. Hozzon létre számára egy újat a terminálon.

```bash
php artisan key:generate --env=production --force
```

Futtassa a mintaalkalmazást a _. env.production_ fájllal környezeti fájlként.

```bash
php artisan serve --env=production
```

Nyissa meg a `http://localhost:8000` címet. Ha az oldal hiba nélkül betölt, a PHP-alkalmazás csatlakozik az Azure-ban található MySQL-adatbázishoz.

Vegyen fel néhány feladatot az oldalon.

![A PHP sikeresen csatlakozik az Azure Database for MySQL-hez](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="commit-your-changes"></a>A módosítások véglegesítése

A módosítások véglegesítéséhez futtassa az alábbi Git-parancsokat:

```bash
git add .
git commit -m "database.php updates"
```

Az alkalmazás készen áll az üzembe helyezésre.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a MySQL-hez csatlakoztatott PHP-alkalmazást az Azure App Service-ben.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Ahogy azt az oktatóanyag korábban említette, az App Service-ben környezeti változókkal csatlakozhat az Azure MySQL-adatbázishoz.

A Cloud Shellben a környezeti változókat _alkalmazásbeállításként_ adhatja meg az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) paranccsal.

Az alábbi parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállítást konfigurálja. Cserélje le az _&lt;appname>_ és a _&lt;mysql_server_name>_ helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

A beállítások eléréséhez a PHP [getenv](http://www.php.net/manual/function.getenv.php) metódust használhatja. A Laravel-kód egy [env](https://laravel.com/docs/5.4/helpers#method-env) burkolót használ a PHP `getenv` helyett. A _config/database.php_ fájlban található MySQL-konfiguráció például az alábbi kódhoz hasonlít:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel környezeti változók konfigurálása

A Laravel egy alkalmazáskulcsot igényel az App Service-ben. Ezt alkalmazásbeállításokkal konfigurálhatja.

A helyi terminálablakban használja a `php artisan` parancsot egy új alkalmazáskulcs _.env_ fájlba való mentés nélküli létrehozásához.

```bash
php artisan key:generate --show
```

A Cloud Shellben állítsa be az App Service-webalkalmazás alkalmazáskulcsát az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) paranccsal. Cserélje le az _&lt;appname>_ és az _&lt;outputofphpartisankey:generate>_ helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

Az `APP_DEBUG="true"` arra utasítja a Laravelt, hogy küldje el a hibakeresési információkat, amikor az üzembe helyezett webalkalmazás hibába ütközik. Éles alkalmazás futtatásakor állítsa át a biztonságosabb `false` értékűre.

### <a name="set-the-virtual-application-path"></a>A virtuális alkalmazáselérési út beállítása

Állítsa be a webalkalmazás virtuális alkalmazáselérési útját. Erre a lépésre azért van szükség, mert a [Laravel-alkalmazás életciklusa](https://laravel.com/docs/5.4/lifecycle) az alkalmazás gyökérkönyvtára helyett a _nyilvános_ könyvtárban kezdődik. A többi PHP-keretrendszer, amelynek az életciklusa a gyökérkönyvtárban kezdődik, a virtuális alkalmazáselérési út manuális konfigurálása nélkül is működik.

A Cloud Shellben állítsa be a virtuális alkalmazáselérési utat az [`az resource update`](/cli/azure/resource#az_resource_update) paranccsal. Cserélje le az _&lt;appname>_ helyőrzőt.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Alapértelmezés szerint az Azure App Service virtuális alkalmazáselérési gyökérútvonala (_/_) a telepített alkalmazásfájlok gyökérkönyvtárára (_sites\wwwroot_) mutat.

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> Azt tapasztalhatja, hogy az üzembehelyezési folyamat [Composer](https://getcomposer.org/)-csomagokat telepít a folyamat végén. Az App Service nem futtatja ezeket az automatizálásokat az alapértelmezett üzembe helyezés során, ezért ez a mintaadattár három további fájllal rendelkezik a gyökérkönyvtárában ennek lehetővé tételéhez:
>
> - `.deployment` – Ez a fájl utasítja az App Service-t, hogy a `bash deploy.sh` fájlt futtassa egyéni üzembehelyezési szkriptként.
> - `deploy.sh` – Az egyéni üzembehelyezési szkript. Ha áttekinti a fájlt, láthatja, hogy a `php composer.phar install` és az `npm install` után fut.
> - `composer.phar` – A Composer csomagkezelője.
>
> Ezzel a módszerrel bármilyen lépést hozzáadhat a Git-alapú üzembe helyezéshez az App Service-ben. További információkért lásd: [Egyéni telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Egy böngészőben keresse fel az `http://<app_name>.azurewebsites.net` címet, és vegyen fel néhány feladatot a listára.

![Az Azure App Service-ben futó PHP-alkalmazás](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Gratulálunk, egy adatvezérelt PHP-alkalmazást futtat az Azure App Service-ben.

## <a name="update-model-locally-and-redeploy"></a>A modell helyileg történő frissítése és ismételt üzembe helyezése

Ebben a lépésben egy egyszerű módosítást fog elvégezni a `task` adatmodellben és a webalkalmazásban, majd közzéteszi a frissítést az Azure-ban.

A feladatokkal kapcsolatos forgatókönyvben úgy módosítja az alkalmazást, hogy befejezettként jelölhessen meg feladatokat.

### <a name="add-a-column"></a>Oszlop hozzáadása

A helyi terminálablakban keresse meg a Git-tárház gyökerét.

Hozzon létre egy új adatbázis-migrálást a `tasks` táblához:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Ez a parancs megjeleníti a létrehozott migrálási fájl nevét. Keresse meg ezt a fájlt a _database/migrations_ könyvtárban, majd nyissa meg.

Cserélje le az `up` metódust az alábbi kódra:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

A fenti kód egy `complete` nevű logikai oszlopot ad hozzá a `tasks` táblában.

Cserélje le a `down` metódust az alábbi kódra a visszaállítási művelethez:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

A módosítás helyi adatbázisban való végrehajtásához futtasson Laravel-adatbázis migrálásokat a helyi terminálablakban.

```bash
php artisan migrate
```

A [Laravel elnevezési szabály](https://laravel.com/docs/5.4/eloquent#defining-models) alapján a `Task` modell (lásd: _app/Task.php_) leképezése alapértelmezés szerint a `tasks` táblára történik.

### <a name="update-application-logic"></a>Az alkalmazáslogika frissítése

Nyissa meg a *routes/web.php* fájlt. Az alkalmazás ebben a fájlban definiálja az útvonalait és az üzleti logikáját.

A fájl végén adjon hozzá egy útvonalat az alábbi kóddal:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

A fenti kód egy egyszerű frissítést hajt végre az adatmodellen a `complete` értékének átállításával.

### <a name="update-the-view"></a>A nézet frissítése

Nyissa meg a *resources/views/tasks.blade.php* fájlt. Keresse meg a `<tr>` nyitó kódcímkét, és cserélje le a következőre:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

A fenti kód módosítja a sor színét, attól függően, hogy a feladat befejeződött-e.

A következő sorban az alábbi kódot láthatja:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Cserélje le a teljes sort az alábbi kódra:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

A fenti kód hozzáadja a korábban megadott útvonalra hivatkozó elküldés gombot.

### <a name="test-the-changes-locally"></a>A módosítások helyi tesztelése

A helyi terminálablakban futtassa a fejlesztési kiszolgálót a Git-tárház gyökérkönyvtárából.

```bash
php artisan serve
```

A feladat állapotváltozásának megtekintéséhez nyissa meg a `http://localhost:8000` címet, és jelölje be a jelölőnégyzetet.

![A feladathoz hozzáadott jelölőnégyzet](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A módosítás Azure-adatbázisban való végrehajtásához futtasson Laravel-adatbázis migrálásokat a termelési kapcsolati karakterlánccal a helyi terminálablakban.

```bash
php artisan migrate --env=production --force
```

Véglegesítse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Miután a `git push` befejeződött, lépjen az Azure-webalkalmazáshoz, és tesztelje az új funkciót.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Ha felvett feladatokat, azok megmaradnak az adatbázisban. Az adatséma frissítései érintetlenül hagyják a meglévő adatokat.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

Bár a PHP-alkalmazás az Azure App Service-ben fut, a konzolnaplófájlokat megkaphatja a terminálban is. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A naplóstreamelés indításához használja az [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) parancsot a Cloud Shellben.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

A naplóstreamelés megkezdése után frissítse az Azure-webalkalmazást a böngészőben, hogy legyen némi webes forgalom. Ekkor láthatja, hogy a rendszer átadja a konzolnaplófájlokat a terminálnak. Ha nem jelennek meg azonnal a konzolnaplófájlok, ellenőrizze ismét 30 másodperc múlva.

A naplóstreamelés leállításához írja be a `Ctrl`+`C` billentyűparancsot.

> [!TIP]
> A PHP-alkalmazások használhatják a normál [error_log()](http://php.net/manual/function.error-log.php) függvényt a kimenet konzolra küldéséhez. A mintaalkalmazás ezt a megközelítést alkalmazza az _app/Http/routes.php_ fájlban.
>
> Webes keretrendszerként a [Laravel a Monologot használja](https://laravel.com/docs/5.4/errors) naplózási szolgáltatóként. Ha szeretné megtudni, hogyan érheti el, hogy a Monolog-üzeneteket jelenítsen meg a konzolon, tekintse meg a [PHP: A Monolog használata a konzolba való naplózáshoz (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out) című cikket.
>
>

## <a name="manage-the-azure-web-app"></a>Az Azure webalkalmazás felügyelete

A létrehozott webalkalmazás felügyeletéhez ugorjon az [Azure Portalra](https://portal.azure.com).

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a leállítás, elindítás, újraindítás, tallózás és törlés.

A bal oldali menü az alkalmazás konfigurálásához biztosít oldalakat.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * PHP-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazásokhoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
