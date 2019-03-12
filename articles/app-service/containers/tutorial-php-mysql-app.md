---
title: PHP-alkalmazás és MySQL létrehozása a linuxon – az Azure App Service |} A Microsoft Docs
description: Ismerje meg, hogyan tehet szert az Azure App Service Linux rendszeren dolgozik az Azure-ban MySQL-hez a PHP-alkalmazás.
services: app-service\web
author: cephalin
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: php
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0470c12f7965ec5d7e151bb6b03163d6946b83e6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548183"
---
# <a name="build-a-php-and-mysql-app-in-azure-app-service-on-linux"></a>A php-t és az Azure App Service MySQL-alkalmazás létrehozása linuxon

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Linuxon futó App Service-ben. Az App Service-ben üzembe _Windows_, lásd: [hozhat létre egy PHP- és MySQL-alkalmazást az Azure-ban](../app-service-web-tutorial-php-mysql.md).
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan hozhat létre PHP-alkalmazás, és azt egy MySQL-adatbázishoz csatlakozhat. Az oktatóanyag eredménye egy, a Linux App Service-ben futó [Laravel](https://laravel.com/)-alkalmazás lesz.

![Az Azure App Service-ben futó PHP-alkalmazás](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * PHP-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a PHP 5.6.4-es vagy újabb verzióját](https://php.net/downloads.php)
* [Telepítse a Composert](https://getcomposer.org/doc/00-intro.md)
* Engedélyezze a következő PHP-bővítményeket Laravel van szüksége: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [Telepítse és indítsa el a MySQL-t](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>A helyi MySQL előkészítése

Ebben a lépésben egy adatbázist hoz létre a helyi MySQL-kiszolgálón, hogy felhasználhassa azt az oktatóanyagban.

### <a name="connect-to-local-mysql-server"></a>Csatlakozás a helyi MySQL-kiszolgálóhoz

Csatlakozzon a helyi MySQL-kiszolgálóhoz egy terminálablakban. Ezzel a terminálablakkal futtathatja az oktatóanyagban szereplő összes parancsot.

```bash
mysql -u root -p
```

Ha a rendszer jelszót kér, adja meg a `root` fiókhoz tartozó jelszót. Ha nem emlékszik a rendszergazdafiók jelszavára, tekintse meg [MySQL: A gyökér szintű jelszó alaphelyzetbe állítása](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

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

Hozzon létre egy *.env* nevű fájlt az adattár gyökérkönyvtárjában. Másolja az alábbi változókat a *.env* fájlba. A _&lt;root_password>_ helyőrzőt cserélje le a MySQL rendszergazdai jelszavára.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

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

![A PHP sikeresen csatlakozik a MySQL-hez](./media/tutorial-php-mysql-app/mysql-connect-success.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>MySQL létrehozása az Azure-ban

Ebben a lépésben egy MySQL-adatbázist hoz létre az [Azure Database for MySQL-ben](/azure/mysql). Később konfigurálni fogja a PHP-alkalmazást az adatbázishoz való csatlakozásra.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL-kiszolgáló létrehozása

Hozzon létre egy kiszolgálót az Azure Database for MySQL szolgáltatásban az [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create) paranccsal.

Az alábbi parancsban írjon egy egyedi kiszolgálónevet a *\<mysql_server_name>*, egy felhasználónevet az *\<admin_user>*, valamint egy jelszót az *\<admin_password>* helyőrző helyére. A kiszolgálónév a MySQL-végpont (`https://<mysql_server_name>.mysql.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban. További részleteket az MySQL DB Termékváltozat kiválasztásához, [hozzon létre egy Azure Database for MySQL-kiszolgáló](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name> --location "West Europe" --admin-user <admin_user> --admin-password <admin_password> --sku-name B_Gen5_1
```

A MySQL-kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "westeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

Az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create) parancs használatával hozzon létre egy tűzfalszabályt a MySQL-kiszolgáló számára az ügyfélkapcsolatok engedélyezéséhez. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

Ahhoz, hogy engedélyezze a helyi számítógépről történő hozzáférést, futtassa újra a parancsot a Cloud Shellben, és a *\<your_ip_address>* helyére írja be a [helyi IPv4 IP-címet](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Helyi csatlakozás éles MySQL-kiszolgálóhoz

A terminálablakban csatlakozzon az Azure-ban található MySQL-kiszolgálóhoz. Használja a korábban megadott értéket  _&lt;admin_user >_ és  _&lt;mysql_server_name >_. Amikor a rendszer jelszót kér, használja azt a jelszót, amelyet az adatbázis létrehozásakor adott meg az Azure-ban.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _phpappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban.

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

Az adattár gyökérkönyvtárában, hozzon létre a _. env.production_ fájlt, és másolja bele a következő változókat. Cserélje le a _&lt;mysql_server_name>_ helyőrzőt.

```txt
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
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

Alapértelmezés szerint az Azure Database for MySQL kikényszeríti az SSL-kapcsolatokat az ügyfelektől. Az Azure-ban található MySQL-adatbázishoz való csatlakozáshoz használja a [_.pem_ tanúsítványt, amelyet az Azure Database for MySQL](../../mysql/howto-configure-ssl.md) biztosít.

Nyissa meg a _config/database.php_ fájlt, majd adja hozzá az _sslmode_ és az _options_ paramétert a `connections.mysql` szakaszhoz az alábbi kódban látható módon.

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

![A PHP sikeresen csatlakozik az Azure Database for MySQL-hez](./media/tutorial-php-mysql-app/mysql-connect-success.png)

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

A Laravel-alkalmazás a _/public_ könyvtárban indul el. Az App Service alapértelmezett PHP Docker-rendszerképe az Apache rendszert használja, és nem engedélyezi a `DocumentRoot` testreszabását a Laravel számára. A `.htaccess` használatával azonban újraírhatja az összes kérést úgy, hogy azok a _/public_ könyvtárra mutassanak a gyökérkönyvtár helyett. Az adattár gyökérkönyvtárában egy `.htaccess` már hozzá lett adva erre a célra. Ennek segítségével a Laravel-alkalmazás készen áll az üzembe helyezésre.

> [!NOTE] 
> Ha inkább nem használná a _.htaccess_ újraírást, üzembe helyezheti a Laravel-alkalmazását egy [egyéni Docker-rendszerképpel](quickstart-docker-go.md) is.
>
>

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ adhatja meg az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) paranccsal.

Az alábbi parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállítást konfigurálja. Cserélje le az _&lt;appname>_ és a _&lt;mysql_server_name>_ helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

A beállítások eléréséhez a PHP [getenv](https://php.net/manual/en/function.getenv.php) metódust használhatja. A Laravel-kód egy [env](https://laravel.com/docs/5.4/helpers#method-env) burkolót használ a PHP `getenv` helyett. A _config/database.php_ fájlban található MySQL-konfiguráció például az alábbi kódhoz hasonlít:

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

A `php artisan` használatával hozhat létre új alkalmazáskulcsot anélkül, hogy a _.env_ fájlba mentené azt.

```bash
php artisan key:generate --show
```

Állítsa be az alkalmazáskulcsot az App Service-alkalmazás használatával az [ `az webapp config appsettings set` ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot. Cserélje le az _&lt;appname>_ és az _&lt;outputofphpartisankey:generate>_ helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` arra utasítja a Laravel, hibakeresési információkat ad vissza, ha az üzembe helyezett alkalmazás hibába ütközik. Éles alkalmazás futtatásakor állítsa át a biztonságosabb `false` értékűre.

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

Adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz.

```bash
git remote add azure <paste_copied_url_here>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe a PHP-alkalmazást. Az üzembe helyező felhasználó létrehozásának részeként a rendszer felkéri a korábban megadott jelszó megadására.

```bash
git push azure master
```

Az üzembe helyezés során az Azure App Service közli előrehaladási állapotát a Gittel.

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

### <a name="browse-to-the-azure-app"></a>Az Azure alkalmazás megkeresése tallózással

Egy böngészőben keresse fel az `http://<app_name>.azurewebsites.net` címet, és vegyen fel néhány feladatot a listára.

![Az Azure App Service-ben futó PHP-alkalmazás](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

Gratulálunk, egy adatvezérelt PHP-alkalmazást futtat az Azure App Service-ben.

## <a name="update-model-locally-and-redeploy"></a>A modell helyileg történő frissítése és ismételt üzembe helyezése

Ebben a lépésben egy egyszerű módosítást fog elvégezni a `task` adatmodellben és a webalkalmazásban, majd közzéteszi a frissítést az Azure-ban.

A feladatokkal kapcsolatos forgatókönyvben úgy módosítja az alkalmazást, hogy befejezettként jelölhessen meg feladatokat.

### <a name="add-a-column"></a>Oszlop hozzáadása

Lépjen a Git-adattár gyökérkönyvtárába a terminálon.

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

A módosításnak a helyi adatbázisban való végrehajtásához futtassa a Laravel adatbázis-áttelepítési parancsait a terminálon.

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

Nyissa meg a *resources/views/tasks.blade.php* fájlt. Keresse meg a nyitó `<tr>` címkét, és cserélje le a következőre:

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

Futtassa a fejlesztési kiszolgálót a Git-adattár gyökérkönyvtárában.

```bash
php artisan serve
```

A feladat állapotváltozásának megtekintéséhez nyissa meg a `http://localhost:8000` címet, és jelölje be a jelölőnégyzetet.

![A feladathoz hozzáadott jelölőnégyzet](./media/tutorial-php-mysql-app/complete-checkbox.png)

A PHP leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A módosításnak az Azure-adatbázisban való végrehajtásához futtassa a Laravel adatbázis-áttelepítési parancsait a terminálon az éles kapcsolati sztringgel.

```bash
php artisan migrate --env=production --force
```

Véglegesítse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Miután a `git push` befejeződött, nyissa meg az Azure-alkalmazást, és tesztelje az új funkciót.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Ha felvett feladatokat, azok megmaradnak az adatbázisban. Az adatséma frissítései érintetlenül hagyják a meglévő adatokat.

## <a name="manage-the-azure-app"></a>Az Azure-alkalmazás kezelése

Nyissa meg a [az Azure portal](https://portal.azure.com) kezelheti a létrehozott alkalmazást.

A bal oldali menüben kattintson a **App Services**, majd kattintson az Azure-alkalmazás neve.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/tutorial-php-mysql-app/access-portal.png)

Az alkalmazás áttekintése oldal jelenik meg. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a leállítás, elindítás, újraindítás, tallózás és törlés.

A bal oldali menü az alkalmazás konfigurálásához biztosít oldalakat.

![Az App Service lap az Azure Portalon](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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

Folytassa a következő oktatóanyaggal, megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése az Azure App Service-ben](../app-service-web-tutorial-custom-domain.md)
