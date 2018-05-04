---
title: Ruby- és MySQL-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben | Microsoft Docs Microsoft Docs
description: Megismerheti, hogyan tehet szert egy olyan, az Azure-ban működő Ruby-alkalmazásra, amely csatlakozik egy MySQL-adatbázishoz az Azure-ban.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a026eafeb71c67a2cb98c20c4fc5af16073be083
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Ruby- és MySQL-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Ruby-webalkalmazást, és hogyan csatlakoztathatja azt egy MySQL-adatbázishoz. Az oktatóanyag eredménye egy, a Linux App Service-ben futó [Ruby on Rails](http://rubyonrails.org/)-alkalmazás lesz.

![Az Azure App Service-ben futó Ruby on Rails-alkalmazás](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Ruby on Rails-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a Ruby 2.3-as verzióját](https://www.ruby-lang.org/documentation/installation/)
* [Telepítse a Ruby on Rails 5.1-es verzióját](http://guides.rubyonrails.org/v5.1/getting_started.html)
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

A kiszolgálókapcsolat bontásához írja be a `quit` parancsot.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Ruby on Rails-alkalmazás helyi létrehozása
Ebben a lépésben egy Ruby on Rails-mintaalkalmazásra tesz szert, konfigurálja annak adatbázis-kapcsolatát, és helyileg futtatja az alkalmazást. 

### <a name="clone-the-sample"></a>A minta klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.

Futtassa a következő parancsot a minta tárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

A `cd` paranccsal lépjen a klónozott könyvtárra. Telepítse a szükséges csomagokat.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>MySQL-kapcsolat konfigurálása

A tárházban nyissa meg a _config/database.yml_ fájlt, és adja meg a helyi MySQL rendszergazdai felhasználónevet és jelszót (13. sor). Ha a [Homebrew](https://brew.sh/) vagy egy hasonló eszköz használatával telepítette a MySQL-t, akkor a fájlban a hitelesítő adatok már be vannak állítva az alapértelmezett értékekre (`root` és egy üres jelszó).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>A minta futtatása helyben

Futtassa [a Rails migrálásokat](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) az alkalmazás számára szükséges táblák létrehozásához. Ha látni szeretné a migrálások során keletkező táblákat, tekintse meg a _db/migrate_ könyvtárat a Git-tárházban.

```bash
rake db:create
rake db:migrate
```

Futtassa az alkalmazást.

```bash
rails server
```

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. Vegyen fel néhány feladatot az oldalon.

![A Ruby on Rails sikeresen csatlakozik a MySQL-hez](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>MySQL létrehozása az Azure-ban

Ebben a lépésben egy MySQL-adatbázist hoz létre az [Azure Database for MySQL (előzetes verzió)](/azure/mysql) szolgáltatásban. Később konfigurálni fogja a Ruby on Rails-alkalmazást az adatbázishoz való csatlakozásra.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL-kiszolgáló létrehozása

Hozzon létre egy kiszolgálót az Azure Database for MySQL (előzetes verzió) szolgáltatásban az [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) paranccsal.

Az alábbi parancsban a _&lt;mysql_server_name>_ helyőrzőt cserélje le a MySQL-kiszolgáló nevére (érvényes karakterek: `a-z`, `0-9` és `-`). Ez a név része a MySQL-kiszolgáló állomásnevének (`<mysql_server_name>.mysql.database.azure.com`), és globálisan egyedinek kell lennie.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

A MySQL-kiszolgáló létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

Az [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) parancs használatával hozzon létre egy tűzfalszabályt a MySQL-kiszolgáló számára az ügyfélkapcsolatok engedélyezéséhez. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-mysql-server-locally"></a>Helyi csatlakozás éles MySQL-kiszolgálóhoz

A terminálablakban csatlakozzon az Azure-ban található MySQL-kiszolgálóhoz. A _&lt;mysql_server_name>_ helyőrzőnél használja a korábban megadott értéket.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Amikor a rendszer jelszót kér, használja a _My5up3r$tr0ngPa$w0rd!_ jelszót, amelyet az adatbázis-kiszolgáló létrehozásakor adott meg.

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `mysql` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _railsappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Bontsa a kiszolgálókapcsolat a `quit` parancs beírásával.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Az alkalmazás csatlakoztatása az Azure MySQL-hez

Ebben a lépésben csatlakoztatja a Ruby on Rails-alkalmazást a MySQL-adatbázishoz, amelyet az Azure Database for MySQL (előzetes verzió) szolgáltatásban hozott létre.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

A tárházban nyissa meg a _config/database.yml_ fájlt. A fájl végén cserélje le a termelési változókat az alábbi kódra. A _&lt;mysql_server_name>_ helyőrzőnél használja a létrehozott MySQL-kiszolgáló nevét.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

Mentse a módosításokat.

> [!NOTE]
> A rendszer hozzáadja a fájlhoz a `sslca` értéket, amely egy meglévő _.pem_ fájlra mutat a mintatárházban. Alapértelmezés szerint az Azure Database for MySQL kikényszeríti az SSL-kapcsolatokat az ügyfelektől. Ezzel a `.pem` tanúsítvánnyal alakíthat ki SSL-kapcsolatokat az Azure Database for MySQL-hez. További információkért lásd [Az SSL-kapcsolatok a MySQL-hez készült Azure Database-hez való kapcsolódásra az alkalmazásban való konfigurálását](../../mysql/howto-configure-ssl.md) bemutató cikket.
>

### <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

A helyi terminálban állítsa be a következő környezeti változókat:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Futtasson Rails-adatbázis migrálásokat az imént beállított termelési értékekkel a táblák létrehozásához az Azure Database for MySQL (előzetes verzió) szolgáltatásban MySQL-adatbázisban. 

```bash
rake db:migrate RAILS_ENV=production
```

Éles környezetben történő futtatásakor a Rails-alkalmazásnak előre lefordított objektumokra van szüksége. Hozza létre a szükséges objektumokat a következő paranccsal:

```bash
rake assets:precompile
```

A Rails éles környezet titkos kulcsokat is használ a biztonság kezeléséhez. Hozzon létre egy titkos kulcsot.

```bash
rails secret
```

Mentse a titkos kulcsot a Rails éles környezet által használt megfelelő változókba. Az egyszerűség kedvéért használja ugyanazt a kulcsot mindkét változóhoz.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Engedélyezze a Rails éles környezet számára a JavaScript- és CSS-fájlok kiszolgálását.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Futtassa a mintaalkalmazást az éles környezetben.

```bash
rails server -e production
```

Nyissa meg a `http://localhost:3000` címet. Ha az oldal hiba nélkül betölt, a Ruby on Rails-alkalmazás csatlakozik az Azure-ban található MySQL-adatbázishoz.

Vegyen fel néhány feladatot az oldalon.

![A Ruby on Rails sikeresen csatlakozik az Azure Database for MySQL (előzetes verzió) szolgáltatáshoz](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="commit-your-changes"></a>A módosítások véglegesítése

A módosítások véglegesítéséhez futtassa az alábbi Git-parancsokat:

```bash
git add .
git commit -m "database.yml updates"
```

Az alkalmazás készen áll az üzembe helyezésre.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a MySQL-hez csatlakoztatott Rails-alkalmazást az Azure App Service-ben.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ lehet beállítani az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) parancs Cloud Shellben való használatával.

Az alábbi Cloud Shell-parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállításokat konfigurálja. Cserélje le az _&lt;appname>_ és a _&lt;mysql_server_name>_ helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Rails környezeti változók konfigurálása

A helyi terminálban hozzon létre egy új titkos kulcsot az Azure-beli Rails éles környezet számára.

```bash
rails secret
```

Konfigurálja a Rails éles környezet számára szükséges változókat.

A következő Cloud Shell-parancsban cserélje le a két _&lt;output_of_rails_secret >_ helyőrzőt a helyi terminálban létrehozott új titkos kulcsra.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Az `ASSETS_PRECOMPILE="true"` arra utasítja az alapértelmezett Ruby tárolót, hogy minden Git-üzembehelyezésnél fordítson elő objektumokat.

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A helyi terminálban adjon hozzá egy távoli Azure-mappát a helyi Git-tárházhoz.

```bash
git remote add azure <paste_copied_url_here>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe a Ruby on Rails-alkalmazást. Az üzembe helyező felhasználó létrehozásának részeként a rendszer felkéri a korábban megadott jelszó megadására.

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

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése

Egy böngészőben keresse fel az `http://<app_name>.azurewebsites.net` címet, és vegyen fel néhány feladatot a listára.

![Az Azure App Service-ben futó Ruby on Rails-alkalmazás](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Gratulálunk, egy adatvezérelt Ruby on Rails-alkalmazást futtat az Azure App Service-ben.

## <a name="update-model-locally-and-redeploy"></a>A modell helyileg történő frissítése és ismételt üzembe helyezése

Ebben a lépésben egy egyszerű módosítást fog elvégezni a `task` adatmodellben és a webalkalmazásban, majd közzéteszi a frissítést az Azure-ban.

A feladatokkal kapcsolatos forgatókönyvben úgy módosítja az alkalmazást, hogy befejezettként jelölhessen meg feladatokat.

### <a name="add-a-column"></a>Oszlop hozzáadása

Lépjen a Git-adattár gyökérkönyvtárába a terminálon.

Hozzon létre egy új migrálást, amely egy `Done` nevű logikai oszlopot ad hozzá a `Tasks` táblához:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

Ez a parancs létrehoz egy új migrálási fájlt a _db/migrate_ könyvtárban.


A módosításnak a helyi adatbázisban való végrehajtásához futtassa a Rails adatbázis-migrálási parancsait a terminálon.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Az alkalmazáslogika frissítése

Nyissa meg az *app/controllers/tasks_controller.rb* fájlt. Keresse meg a következő sort a fájl végén:

```rb
params.require(:task).permit(:Description)
```

Módosítsa a sort, hogy tartalmazza az új `Done` paramétert.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>A nézetek frissítése

Nyissa meg az *app/views/tasks/_form.html.erb* fájlt (Szerkesztési űrlap).

Keresse meg a `<%=f.error_span(:Description) %>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Nyissa meg az *app/views/tasks/show.html.erb* fájlt (egyrekordos Nézet lap). 

Keresse meg a `<dd><%= @task.Description %></dd>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Nyissa meg az *app/views/tasks/index.html.erb* fájlt (az összes rekord Index lapja).

Keresse meg a `<th><%= model_class.human_attribute_name(:Description) %></th>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Ugyanabban a fájlban keresse meg a `<td><%= task.Description %></td>` sort, majd szúrja be az alábbi kódot közvetlenül alá:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>A módosítások helyi tesztelése

Futtassa a Rails-kiszolgálót a helyi terminálban.

```bash
rails server
```

A feladat állapotváltozásának megtekintéséhez lépjen a `http://localhost:3000` címre, és adjon hozzá vagy szerkesszen elemeket.

![A feladathoz hozzáadott jelölőnégyzet](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A módosítás Azure-adatbázisban való végrehajtásához futtasson Rails-adatbázis migrálásokat az éles környezetbe a terminálban.

```bash
rake db:migrate RAILS_ENV=production
```

Véglegesítse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Miután a `git push` befejeződött, lépjen az Azure-webalkalmazáshoz, és tesztelje az új funkciót.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Ha felvett feladatokat, azok megmaradnak az adatbázisban. Az adatséma frissítései érintetlenül hagyják a meglévő adatokat.

## <a name="manage-the-azure-web-app"></a>Az Azure webalkalmazás felügyelete

A létrehozott webalkalmazás felügyeletéhez ugorjon az [Azure Portalra](https://portal.azure.com).

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-php-mysql-app/access-portal.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a leállítás, elindítás, újraindítás, tallózás és törlés.

A bal oldali menü az alkalmazás konfigurálásához biztosít oldalakat.

![Az App Service lap az Azure Portalon](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Ruby on Rails-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazásokhoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)
