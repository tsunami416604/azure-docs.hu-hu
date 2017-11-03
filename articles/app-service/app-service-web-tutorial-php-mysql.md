---
title: "Az Azure-ban a PHP és a MySQL webes alkalmazás létrehozása |} Microsoft Docs"
description: "Ismerje meg, hogyan kérhet egy PHP-alkalmazásokban az Azure, az Azure-ban MySQL adatbázis-kapcsolat használata."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 08e9f58cc81122ae36db67d916cf2550490ec4ef
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>Az Azure-ban a PHP és a MySQL webalkalmazás létrehozása

Az [Azure Web Apps](app-service-web-overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan hozzon létre egy PHP webalkalmazást az Azure-ban, és csatlakoztassa a MySQL-adatbázis. Amikor végzett, konfigurálnia kell egy [Laravel](https://laravel.com/) Azure App Service Web Apps futó alkalmazáshoz.

![PHP-alkalmazás fusson az Azure App Service](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * A PHP-alkalmazás csatlakoztatása a MySQL
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a PHP 5.6.4 vagy újabb](http://php.net/downloads.php)
* [Szerkesztő telepítése](https://getcomposer.org/doc/00-intro.md)
* A következő PHP-bővítmények Laravel kell engedélyezni: OpenSSL, OEM-MySQL, Mbstring, jogkivonatokat létrehozó, XML
* [Telepítse és indítsa el a MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Készítse elő a helyi MySQL

Ebben a lépésben egy adatbázis létrehozása a helyi MySQL Server ebben az oktatóanyagban a használatra.

### <a name="connect-to-local-mysql-server"></a>Csatlakozás helyi MySQL-kiszolgálóhoz

Az egy terminálablakot a helyi MySQL-kiszolgálóhoz való csatlakozás. Ebben az oktatóanyagban a parancsok futtatásához használhatja a terminálablakot.

```bash
mysql -u root -p
```

Ha kéri a jelszót, írja be a jelszót a `root` fiók. Ha nem emlékszik a rendszergazdafiók jelszavával, lásd: [MySQL: a gyökér szintű jelszó alaphelyzetbe állításával](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Ha a parancs sikeresen lefutott, majd MySQL fut a kiszolgálón. Ha nem, győződjön meg arról, hogy a helyi MySQL-kiszolgáló elindult-e követve a [MySQL telepítés utáni lépéseket](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Helyi adatbázis létrehozása

: A `mysql` kérdés, hozzon létre egy adatbázist.

```sql 
CREATE DATABASE sampledb;
```

Lépjen ki a kiszolgálói kapcsolatot írja be a `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Helyileg egy PHP-alkalmazás létrehozása
Ebben a lépésben Laravel mintaalkalmazás beolvasása, az adatbázis-kapcsolat konfigurálása, és futtassa helyileg. 

### <a name="clone-the-sample"></a>A minta klónozása

A Terminálszolgáltatások ablakban `cd` egy működő könyvtárba.

Futtassa a következő parancsot a minta tárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd`a klónozott könyvtárba.
Telepítse a szükséges csomagokat.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL-kapcsolat beállítása

Az adattár gyökérkönyvtárában, hozzon létre egy szövegfájlt, nevű *.env*. Másolja be a következő változókat a *.env* fájlt. Cserélje le a  _&lt;root_password >_ helyőrzőt a MySQL gyökér szintű felhasználó jelszavát.

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

Információk arról, hogyan Laravel használja a _.env_ fájlba, lásd: [Laravel környezet konfigurációjának](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>A minta helyileg történő futtatása

Futtatás [Laravel adatbázis áttelepítések](https://laravel.com/docs/5.4/migrations) , az alkalmazás létrehozása a táblák kell. Tekintse meg a táblák létrejönnek az áttelepítést, tekintse meg a _adatbázis/áttelepítések_ könyvtárban lévő a Git-tárházba.

```bash
php artisan migrate
```

Hozzon létre egy új Laravel alkalmazás kulcsot.

```bash
php artisan key:generate
```

Futtassa az alkalmazást.

```bash
php artisan serve
```

Egy böngészőben nyissa meg a `http://localhost:8000` oldalt. A lapon vennie néhány feladatot.

![A PHP sikeresen csatlakozik a MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

Állítsa le a PHP-kiszolgálót, írja be a következőt `Ctrl + C` a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Hozzon létre MySQL az Azure-ban

Ebben a lépésben a MySQL-adatbázis létrehozása [MySQL (előzetes verzió) az Azure-adatbázis](/azure/mysql). Később konfigurálnia a PHP-alkalmazás az adatbázishoz való kapcsolódáshoz.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>A MySQL-kiszolgáló létrehozása

A felhő rendszerhéj a kiszolgáló létrehozása az Azure-adatbázisban a MySQL (előzetes verzió) a [az mysql kiszolgáló létrehozni](/cli/azure/mysql/server#create) parancsot.

Az alábbi parancs helyettesítse a MySQL-kiszolgáló nevét, ahol megjelenik a  _&lt;mysql_server_name >_ helyőrző (érvényes karakterek: `a-z`, `0-9`, és `-`). Ez a név része a MySQL-kiszolgáló állomásneve (`<mysql_server_name>.database.windows.net`), kell lennie a globálisan egyedi.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password MySQLAzure2017
```

> [!NOTE]
> Mivel több hitelesítő adatokat, így nem keverhetők össze, ebben az oktatóanyagban gondolniuk `--admin-user` és `--admin-password` üres érték van beállítva. Éles környezetben kövesse az ajánlott biztonsági eljárások kiválasztásakor helyes felhasználónevet és jelszót a MySQL-kiszolgáló az Azure-ban.
>
>

A MySQL-kiszolgáló létrehozása esetén, az Azure parancssori felület kapcsolatos adatokat jeleníti meg az alábbi példához hasonló:

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

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfal konfigurálása

A felhő rendszerhéj hozzon létre egy tűzfalszabályt az MySQL-kiszolgálót, hogy lehetővé tegyék az ügyfélkapcsolatokat használatával a [az mysql-tűzfalszabály létrehozása](/cli/azure/mysql/server/firewall-rule#create) parancsot.

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> A MySQL (előzetes verzió) Azure-adatbázis jelenleg nem korlátozza a kapcsolatokat csak az Azure-szolgáltatásokhoz való. Mivel a dinamikusan kiosztott IP-címek az Azure-ban, akkor az IP-címek engedélyezése. A szolgáltatás előzetes verzió van. Az adatbázis védelmét biztosító jobb módszerek tervbe van véve.
>
>

### <a name="connect-to-production-mysql-server-locally"></a>Csatlakozás helyi éles MySQL kiszolgálóhoz

A helyi terminálablakot csatlakozzon a MySQL-kiszolgálóhoz, az Azure-ban. A korábban megadott értéket  _&lt;mysql_server_name >_. Ha a rendszer kéri a jelszót, _MySQLAzure2017_, amely az Azure-ban az adatbázis létrehozásakor megadott.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

: A `mysql` kérdés, hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Hozzon létre egy felhasználói jogosultságokkal

Néven adatbázis-felhasználó létrehozása _phpappuser_ , és adjon neki összes jogosultságot a `sampledb` adatbázis. Ebben az esetben az oktatóanyag az egyszerűség kedvéért használja _MySQLAzure2017_ a jelszót.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Lépjen ki a kapcsolat beírásával `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Alkalmazás csatlakoztatása az Azure-beli MySQL

Ebben a lépésben csatlakoztatja a MySQL (előzetes verzió) Azure-adatbázisban létrehozott MySQL-adatbázis a PHP-alkalmazásokat.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

Az adattár gyökérkönyvtárában, hozzon létre egy _. env.production_ fájlt, és másolja azt a következő változókat. Cserélje le a helyőrző  _&lt;mysql_server_name >_ mindkét *DB_HOST* és *DB_USERNAME*.

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
> A MySQL-kapcsolati adatok védelme érdekében ez a fájl már ki van zárva a Git-tárházba való (lásd: _.gitignore_ az adattár gyökérkönyvtárában). Később megismerheti, hogyan konfigurálhatja a környezeti változókat az App Service-adatbázishoz való kapcsolódáshoz az Azure-adatbázisban a MySQL (előzetes verzió). A környezeti változókat, nem kell a *.env* fájl az App Service-ben.
>

### <a name="configure-ssl-certificate"></a>SSL-tanúsítvány konfigurálása

Alapértelmezés szerint a MySQL az Azure-adatbázis ügyfelektől érkező SSL-kapcsolatok érvénybe lépteti. A MySQL-adatbázis, az Azure-ban való kapcsolódáshoz használjon egy _.pem_ SSL-tanúsítvány.

Nyissa meg _config/database.php_ , és adja hozzá a _sslmode_ és _beállítások_ paraméterek `connections.mysql`, az alábbi kódban látható módon.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/certificate.pem', 
    ] : []
],
```

Megtudhatja, hogyan előállításához, ez _certificate.pem_, lásd: [konfigurálása az SSL-kapcsolatot az alkalmazásokhoz való biztonságos kapcsolódás Azure-adatbázis a MySQL](../mysql/howto-configure-ssl.md).

> [!TIP]
> Az elérési út _/ssl/certificate.pem_ mutat egy meglévő _certificate.pem_ fájl a Git-tárházban. Ez a fájl ebben az oktatóanyagban a kényelem valósul meg. A legjobb, meg kell nem hajtsa végre a _.pem_ tanúsítványok forrás vezérlőbe. 
>

### <a name="test-the-application-locally"></a>Az alkalmazás helyi teszteléséhez

Futtassa a Laravel adatbázis áttelepítése az _. env.production_ a táblák létrehozása az Azure-adatbázisban a MySQL-adatbázis (előzetes verzió) MySQL környezet fájlként. Ne feledje, hogy _. env.production_ a kapcsolódási adatokat, hogy a MySQL-adatbázis rendelkezik az Azure-ban.

```bash
php artisan migrate --env=production --force
```

_. env.production_ még nem rendelkezik érvényes kulcs. A Terminálszolgáltatások hozzá egy új létrehozni.

```bash
php artisan key:generate --env=production --force
```

Futtassa a mintaalkalmazást a _. env.production_ a környezet fájlként.

```bash
php artisan serve --env=production
```

Navigáljon a `http://localhost:8000`. Ha a lap nem jelenik meg hibaüzenet tölt be, a PHP-alkalmazások az Azure-ban a MySQL-adatbázis számára.

A lapon vennie néhány feladatot.

![A PHP sikeresen Azure adatbázishoz kapcsolódik a MySQL (előzetes verzió)](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

A PHP leállításához írja be a `Ctrl + C` a terminálon.

### <a name="commit-your-changes"></a>A változtatások véglegesítése a határidő

Futtassa a következő Git-parancsokat a változtatások véglegesítése a határidő:

```bash
git add .
git commit -m "database.php updates"
```

Az alkalmazás készen áll a telepíthető.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben az Azure App Service PHP MySQL-kompatibilis alkalmazást telepít központilag.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Szerint korábban, csatlakozhat az Azure-beli MySQL adatbázis App Service környezeti változók használatát.

A felhő rendszerhéj környezeti változókat, mint a beállított _Alkalmazásbeállítások_ használatával a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings#set) parancsot.

A következő parancsot konfigurálása az alkalmazás beállításaiban `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, és `DB_PASSWORD`. Cserélje le a helyőrzőket  _&lt;alkalmazásnév >_ és  _&lt;mysql_server_name >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Használhatja a PHP [getenv](http://www.php.net/manual/function.getenv.php) módszert a beállítások eléréséhez. az Laravel használ egy [env](https://laravel.com/docs/5.4/helpers#method-env) keresztül a PHP burkoló `getenv`. Például a MySQL konfigurációját a _config/database.php_ tűnik a következő kódot:

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

### <a name="configure-laravel-environment-variables"></a>Környezeti változók Laravel konfigurálása

Laravel kell egy alkalmazás-kulcsot az App Service-ben. Alkalmazás beállításokkal konfigurálható.

A helyi terminálablakot, használjon `php artisan` egy új alkalmazás-kulcs létrehozása úgy, hogy mentése nélkül _.env_.

```bash
php artisan key:generate --show
```

A felhő rendszerhéj kulcsát állítsa a alkalmazás az App Service a web app használatával a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings#set) parancsot. Cserélje le a helyőrzőket  _&lt;alkalmazásnév >_ és  _&lt;outputofphpartisankey: készítése >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"`be van állítva Laravel hibakeresési adatok vissza, ha a telepített webalkalmazás hibát észlel. Termelési alkalmazások futtatásakor állítsa az értékét `false`, amelyhez biztonságosabb.

### <a name="set-the-virtual-application-path"></a>A virtuális alkalmazás elérési útjának beállítása

Állítsa be a virtuális alkalmazás elérési útja a webalkalmazás számára. Ez a lépés akkor szükséges, mert a [Laravel alkalmazás életciklusa](https://laravel.com/docs/5.4/lifecycle) kezdődik a _nyilvános_ könyvtárhoz, az alkalmazás gyökérkönyvtárában helyett. Egyéb PHP keretrendszerek, amelynek életciklus indítsa el a gyökérkönyvtár használható az alkalmazás virtuális elérési út kézi konfigurálása nélkül.

A felhő rendszerhéj-beállításokat a virtuális alkalmazás elérési útja a [az erőforrás frissítési](/cli/azure/resource#update) parancsot. Cserélje le a  _&lt;alkalmazásnév >_ helyőrző.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Alapértelmezés szerint az Azure App Service a virtuális alkalmazás gyökérútvonalát mutat (_/_) üzembe helyezett alkalmazás fájlok gyökérkönyvtárára (_sites\wwwroot_).

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A helyi terminálablakban adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz. Cserélje le  _&lt;illessze be\_másolt\_URL-cím\_itt >_ a Git távoli feljegyzett URL-címét [webalkalmazás létrehozása](#create).

```bash
git remote add azure <paste_copied_url_here>
```

Küldje le az Azure távoli, a PHP-alkalmazások telepítéséhez. A központi felhasználói létrehozásának részeként korábban megadott jelszó megadását kéri.

```bash
git push azure master
```

A telepítés során az Azure App Service a telepítés előrehaladását a Git kommunikál.

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
> Azt tapasztalhatja, hogy telepítse-e a telepítési folyamat [szerkesztő](https://getcomposer.org/) csomagok végén. App Service nem futnak a ezek automatizálása alapértelmezett telepítés során, így ez a minta-tárház engedélyezheti a gyökérkönyvtárban három további fájlok:
>
> - `.deployment`– Ez a fájl közli az App Service futtatásához `bash deploy.sh` , az egyéni telepítési parancsfájl.
> - `deploy.sh`– Az egyéni telepítési parancsfájl. Tekintse át a fájlt, ha látni fogja, hogy fusson `php composer.phar install` után `npm install`.
> - `composer.phar`-A szerkesztő package manager.
>
> Bármely lépése hozzá szeretne adni a Git-alapú telepítéshez az App Service használhatja ezt a módszert használja. További információkért lásd: [egyéni telepítési parancsfájl](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

### <a name="browse-to-the-azure-web-app"></a>Keresse meg az Azure-webalkalmazásban

Keresse meg a `http://<app_name>.azurewebsites.net` és néhány feladatot hozzáadása a listához.

![PHP-alkalmazás fusson az Azure App Service](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

Gratulálunk, az Azure App Service-ben, amelyen az adatvezérelt PHP-alkalmazásokban.

## <a name="update-model-locally-and-redeploy"></a>Frissítési modell helyileg, és helyezze üzembe újra

Ebben a lépésben módosítja egyszerű való a `task` adatmodell, és a webalkalmazást, majd tegye közzé a frissítés az Azure-bA.

A feladatok esetben módosítani az alkalmazást, hogy a feladatoknak befejezettként.

### <a name="add-a-column"></a>Egy oszlop hozzáadása

A helyi terminálablakot navigáljon a Git-tárház gyökérkönyvtárában.

Áttelepítés az új adatbázis létrehozása a `tasks` tábla:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Ez a parancs megjeleníti a létrehozott áttelepítési fájl nevét. Ez a fájl a _adatbázis/áttelepítések_ , majd nyissa meg.

Cserélje le a `up` metódus a következő kóddal:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Az előzőekben látható kód a logikai oszlopot ad a `tasks` nevű táblát `complete`.

Cserélje le a `down` módszer a visszaállítási művelet a következő kóddal:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

A helyi terminálablakot futtassa Laravel adatbázis áttelepítése az módosítást a helyi adatbázisban.

```bash
php artisan migrate
```

Alapján a [Laravel elnevezési](https://laravel.com/docs/5.4/eloquent#defining-models), a modell `Task` (lásd: _app/Task.php_) van leképezve a `tasks` tábla alapértelmezés szerint.

### <a name="update-application-logic"></a>Frissítés az alkalmazáslogikát

Nyissa meg a *routes/web.php* fájlt. Az alkalmazás az útvonalak és üzleti logika Itt határozza meg.

A fájl végén adjon hozzá egy útvonalat, az alábbi kódra:

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

Az előzőekben látható kód esetén egy egyszerű frissítés az adatmodellbe való átváltással értékének `complete`.

### <a name="update-the-view"></a>A nézet frissítéséhez

Nyissa meg a *resources/views/tasks.blade.php* fájlt. Keresse meg a `<tr>` nyitó, és cserélje le:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Az előzőekben látható kód módosítja a sor szín attól függően, hogy a tevékenység befejeződött.

A következő sorban van a következő kódot:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Cserélje le a teljes sor a következő kódot:

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

Az előzőekben látható kód hozzáadása a Küldés gomb, amely az útvonalat, amelyet korábban megadott hivatkozik.

### <a name="test-the-changes-locally"></a>A módosításokat a helyi tesztelése

A helyi terminálablakot futtassa a fejlesztési kiszolgáló a Git-tárház gyökérkönyvtárában.

```bash
php artisan serve
```

Módosításához nyissa meg a feladat állapotának megtekintéséhez `http://localhost:8000` és jelölje be a jelölőnégyzetet.

![A hozzáadott melletti jelölőnégyzetet, hogy a feladat](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

A PHP leállításához írja be a `Ctrl + C` a terminálon.

### <a name="publish-changes-to-azure"></a>Változások közzétételére Azure

A helyi terminálablakot futtassa Laravel adatbázis áttelepítése az éles kapcsolati karakterlánccal végezheti el a módosítást az Azure-adatbázisban.

```bash
php artisan migrate --env=production --force
```

A Git a módosítások véglegesítéséhez, és ezután a kód változásainak leküldése Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Egyszer a `git push` befejezése, navigáljon az Azure web app és az új funkció tesztelése.

![Az Azure-bA közzétett modell és adatbázis módosítása](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

Ha olyan feladatokat, jelennek meg az adatbázisban. Az adatok séma frissítések érintetlenül meglévő adatokat.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók adatfolyam

Míg a PHP-alkalmazások az Azure App Service-ben, a terminálon az adatcsatornán konzolnaplófájlokban kaphat. Ily módon kaphat segítséget nyújtanak az alkalmazáshibák debug diagnosztikai ugyanazokat az üzeneteket.

Napló streaming indításához használja a [az webapp napló végéről](/cli/azure/webapp/log#tail) a felhő rendszerhéj parancsot.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Napló adatfolyam megkezdése, után frissítse az Azure-webalkalmazásban néhány webes forgalom eléréséhez a böngészőben. Most már megtekintheti a Terminálszolgáltatások az adatcsatornán konzolnaplófájlokban. Ha a konzol naplói nem jelenik meg azonnal, ellenőrzés 30 másodperc múlva.

Napló bármikor streaming leállításához írja be a `Ctrl` + `C`.

> [!TIP]
> A PHP-alkalmazások használhatják a normál [error_log()](http://php.net/manual/function.error-log.php) a konzol kimeneti. A mintaalkalmazás használja ezt a megközelítést _app/Http/routes.php_.
>
> Egy webes keretrendszer, [Laravel használ Monolog](https://laravel.com/docs/5.4/errors) a naplózás-szolgáltatóként. A konzol kimeneti üzenetek Monolog elérése, olvassa el [PHP: monolog használata bejelentkezni a konzolhoz (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-web-app"></a>Az Azure webalkalmazás felügyelete

A létrehozott webalkalmazás felügyeletéhez ugorjon az [Azure Portalra](https://portal.azure.com).

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-php-mysql/access-portal.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt például a stop, kezdő, indítsa újra, tallózással keresse meg és delete alapvető felügyeleti feladatokat hajthat végre.

A bal oldali menü lapokat biztosít az alkalmazás konfigurálását.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * A PHP-alkalmazás csatlakoztatása a MySQL
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
