---
title: "Az Azure App Service Ruby és MySQL webalkalmazás létrehozása Linux |} Microsoft Docs"
description: "Ismerje meg, hogyan kérhet egy Ruby alkalmazást az Azure, az Azure-ban MySQL adatbázis-kapcsolat használata."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 03b2b4e8f8827a08e1414512d848bd5bed48d674
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Az Azure App Service Ruby és MySQL webalkalmazás létrehozása Linux-kiszolgálón

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ez az oktatóanyag bemutatja, hogyan Ruby-webalkalmazás létrehozása, és csatlakoztassa a MySQL-adatbázis. Amikor végzett, konfigurálnia kell egy [Ruby sínen](http://rubyonrails.org/) az App Service Linux rendszeren futó alkalmazást.

![Az Azure App Service-ben futó sínek alkalmazást Ruby](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Csatlakozás egy Ruby sínek alkalmazásában MySQL
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Ruby 2.3 telepítése](https://www.ruby-lang.org/documentation/installation/)
* [5.1 sínen Ruby telepítése](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [Telepítse és indítsa el a MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>Készítse elő a helyi MySQL

Ebben a lépésben egy adatbázis létrehozása a helyi MySQL Server ebben az oktatóanyagban a használatra.

### <a name="connect-to-local-mysql-server"></a>Csatlakozás helyi MySQL-kiszolgálóhoz

Az egy terminálablakot a helyi MySQL-kiszolgálóhoz való csatlakozás. Ebben az oktatóanyagban a parancsok futtatásához használhatja a terminálablakot.

```bash
mysql -u root -p
```

Ha kéri a jelszót, írja be a jelszót a `root` fiók. Ha nem emlékszik a rendszergazdafiók jelszavával, lásd: [MySQL: a gyökér szintű jelszó alaphelyzetbe állításával](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Ha a parancs sikeresen lefutott, majd MySQL fut a kiszolgálón. Ha nem, győződjön meg arról, hogy a helyi MySQL-kiszolgáló elindult-e követve a [MySQL telepítés utáni lépéseket](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

Lépjen ki a kiszolgálói kapcsolatot írja be a `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Hozzon létre egy Ruby helyileg sínek alkalmazásában
Ebben a lépésben egy Ruby kérjen a sínek mintaalkalmazást, az adatbázis-kapcsolat konfigurálása, és futtassa helyileg. 

### <a name="clone-the-sample"></a>A minta klónozása

A Terminálszolgáltatások ablakban `cd` egy működő könyvtárba.

Futtassa a következő parancsot a minta tárház klónozásához.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd`a klónozott könyvtárba. Telepítse a szükséges csomagokat.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>MySQL-kapcsolat beállítása

Nyissa meg a tárház _config/database.yml_ és helyi MySQL legfelső szintű felhasználói nevet és jelszót (sor 13). Ha telepítette a MySQL hasonló eszköz használatával [Homebrew](https://brew.sh/), akkor a felhasználó hitelesítő adatait a fájl már be van állítva az alapértelmezett értékre (amely `root` és üres jelszóval).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>A minta helyileg történő futtatása

Futtatás [a sínek áttelepítések](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations) , az alkalmazás létrehozása a táblák kell. Tekintse meg a táblák létrejönnek az áttelepítést, tekintse meg a _db/áttelepítése_ könyvtárban lévő a Git-tárházba.

```bash
rake db:create
rake db:migrate
```

Futtassa az alkalmazást.

```bash
rails server
```

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. A lapon vennie néhány feladatot.

![Ruby sínen sikeresen csatlakozik a MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

A sínek kiszolgáló leállításához írja be a `Ctrl + C` a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Hozzon létre MySQL az Azure-ban

Ebben a lépésben a MySQL-adatbázis létrehozása [MySQL (előzetes verzió) az Azure-adatbázis](/azure/mysql). Később a Ruby konfigurálnia sínek alkalmazás ehhez az adatbázishoz való kapcsolódáshoz.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>A MySQL-kiszolgáló létrehozása

A kiszolgáló létrehozása az Azure-adatbázisban a MySQL (előzetes verzió) a [az mysql kiszolgáló létrehozni](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) parancsot.

Az alábbi parancs helyettesítse a MySQL-kiszolgáló nevét, ahol megjelenik a  _&lt;mysql_server_name >_ helyőrző (érvényes karakterek: `a-z`, `0-9`, és `-`). Ez a név része a MySQL-kiszolgáló állomásneve (`<mysql_server_name>.mysql.database.azure.com`), kell lennie a globálisan egyedi.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

A MySQL-kiszolgáló létrehozása esetén, az Azure parancssori felület kapcsolatos adatokat jeleníti meg az alábbi példához hasonló:

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

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfal konfigurálása

Hozzon létre egy tűzfalszabályt az MySQL-kiszolgálót, hogy lehetővé tegyék az ügyfélkapcsolatokat használatával a [az mysql-tűzfalszabály létrehozása](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) parancsot.

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> A MySQL (előzetes verzió) Azure-adatbázis jelenleg nem korlátozza a kapcsolatokat csak az Azure-szolgáltatásokhoz való. Mivel a dinamikusan kiosztott IP-címek az Azure-ban, akkor az IP-címek engedélyezése. A szolgáltatás előzetes verzió van. Az adatbázis védelmét biztosító jobb módszerek tervbe van véve.
>

### <a name="connect-to-production-mysql-server-locally"></a>Csatlakozás helyi éles MySQL kiszolgálóhoz

A terminálablakot csatlakozzon a MySQL-kiszolgálóhoz, az Azure-ban. A korábban megadott értéket  _&lt;mysql_server_name >_.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

Ha a rendszer kéri a jelszót, _My5up3r$ tr0ngPa$ w0rd!_, amely az adatbázis-kiszolgáló létrehozásakor megadott.

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

: A `mysql` kérdés, hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Hozzon létre egy felhasználói jogosultságokkal

Néven adatbázis-felhasználó létrehozása _railsappuser_ , és adjon neki összes jogosultságot a `sampledb` adatbázis.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

Lépjen ki a kapcsolat beírásával `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Alkalmazás csatlakoztatása az Azure-beli MySQL

Ebben a lépésben csatlakoztatja a Ruby sínek kérelem MySQL (előzetes verzió) Azure-adatbázisban létrehozott MySQL-adatbázis.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

Nyissa meg a tárház _config/database.yml_. A fájl alján cserélje le a következő kódot a termelési változók. Az a  _&lt;mysql_server_name >_ helyőrző, a létrehozott MySQL-kiszolgáló nevét használja.

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
> A `sslca` kerül, és mutat egy meglévő _.pem_ a minta-tárházban található fájl. Alapértelmezés szerint a MySQL az Azure-adatbázis ügyfelektől érkező SSL-kapcsolatok érvénybe lépteti. Ez `.pem` tanúsítvány ezzel SSL-kapcsolatok az Azure Database a MySQL. További információkért lásd [Az SSL-kapcsolatok a MySQL-hez készült Azure Database-hez való kapcsolódásra az alkalmazásban való konfigurálását](../../mysql/howto-configure-ssl.md) bemutató cikket.
>

### <a name="test-the-application-locally"></a>Az alkalmazás helyi teszteléséhez

A helyi terminálban állítsa be a következő környezeti változók:

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

Futtassa a sínek adatbázis áttelepítése az imént konfigurált a táblák létrehozása az Azure-adatbázisban a MySQL-adatbázis MySQL (előzetes verzió) éles értékekkel. 

```bash
rake db:migrate RAILS_ENV=production
```

Az éles környezetben történő futtatásakor az sínek alkalmazást kell előre lefordított eszközök. Létrehozni a szükséges eszközök, a következő paranccsal:

```bash
rake assets:precompile
```

A sínek éles környezetben is használ a titkos kulcsok biztonság kezeléséhez. Hozzon létre egy titkos kulcsot.

```bash
rails secret
```

Mentse a titkos kulcsot a megfelelő változók a sínek éles környezetben használják. Kényelmi célokat szolgál mindkét változók ugyanazt a kulcsot használja.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Engedélyezze a JavaScript és CSS fájlok sínek éles környezetben.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Futtassa a mintaalkalmazást az éles környezetben.

```bash
rails server -e production
```

Navigáljon a `http://localhost:3000`. Ha a lap nem jelenik meg hibaüzenet tölt be, a Ruby sínek alkalmazástól csatlakozik a MySQL-adatbázis, az Azure-ban.

A lapon vennie néhány feladatot.

![Ruby sínen sikeresen Azure adatbázishoz kapcsolódik a MySQL (előzetes verzió)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

A sínek kiszolgáló leállításához írja be a `Ctrl + C` a terminálon.

### <a name="commit-your-changes"></a>A változtatások véglegesítése a határidő

Futtassa a következő Git-parancsokat a változtatások véglegesítése a határidő:

```bash
git add .
git commit -m "database.yml updates"
```

Az alkalmazás készen áll a telepíthető.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben az Azure App Service sínek MySQL-kompatibilis alkalmazást telepít központilag.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal hozzon létre egy webalkalmazást a `myAppServicePlan` App Service-csomagban. 

A következő példában cserélje ki az `<app_name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). A futtatókörnyezet értéke `RUBY|2.3`, mely telepíti az [Ruby kép alapértelmezett](https://hub.docker.com/r/appsvc/ruby/). Az összes támogatott futtatókörnyezet megtekintéséhez futtassa az [az webapp list-runtimes](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes) parancsot. 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "RUBY|2.3" --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Ezzel létrehozott egy üres, új webalkalmazást, engedélyezett Git üzemelő példánnyal.

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Mentse el ezt az URL-t, később még szüksége lesz rá.
>

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben, a környezeti változók beállítása _Alkalmazásbeállítások_ használatával a [az webapp appsettings konfiguráció](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) a felhő rendszerhéj parancsot.

A következő felhő rendszerhéj parancsot konfigurálása az alkalmazás beállításaiban `DB_HOST`, `DB_DATABASE`, `DB_USERNAME`, és `DB_PASSWORD`. Cserélje le a helyőrzőket  _&lt;alkalmazásnév >_ és  _&lt;mysql_server_name >_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Környezeti változók sínek konfigurálása

A helyi terminálban hozzon létre egy új titkos kulcsot sínek éles környezetben az Azure-ban.

```bash
rails secret
```

A változók sínek éles környezetben szükséges konfigurálását.

A következő felhő rendszerhéj parancsban cserélje le a két  _&lt;output_of_rails_secret >_ helyőrzőt a helyi terminálban hozta létre az új titkos kulcsa.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`be van állítva az alapértelmezett, minden egyes Git-telepítésének eszközök előfordításához Ruby tároló.

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A helyi Git-tárház egy Azure távoli hozzáadása a helyi terminálban.

```bash
git remote add azure <paste_copied_url_here>
```

Küldje le az Azure távoli sínek alkalmazástól Ruby telepítéséhez. A központi felhasználói létrehozásának részeként korábban megadott jelszó megadását kéri.

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

### <a name="browse-to-the-azure-web-app"></a>Keresse meg az Azure-webalkalmazásban

Keresse meg a `http://<app_name>.azurewebsites.net` és néhány feladatot hozzáadása a listához.

![Az Azure App Service-ben futó sínek alkalmazást Ruby](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

Gratulálunk, sínek alkalmazást az Azure App Service egy adatvezérelt Ruby futtatja.

## <a name="update-model-locally-and-redeploy"></a>Frissítési modell helyileg, és helyezze üzembe újra

Ebben a lépésben módosítja egyszerű való a `task` adatmodell, és a webalkalmazást, majd tegye közzé a frissítés az Azure-bA.

A feladatok esetben módosítani az alkalmazást, hogy a feladatoknak befejezettként.

### <a name="add-a-column"></a>Oszlop hozzáadása

A terminálban keresse meg a Git-tárház gyökérkönyvtárában.

Létrehozása, amely egy logikai nevű oszlopot ad hozzá új áttelepítés `Done` számára a `Tasks` tábla:

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

A parancs létrehoz egy új áttelepítési fájlra a _db/áttelepítése_ könyvtár.


A Terminálszolgáltatások futnak sínek adatbázis áttelepítése az módosítást a helyi adatbázisban.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Frissítés az alkalmazáslogikát

Nyissa meg a *app/controllers/tasks_controller.rb* fájlt. A fájl végén található a következő sort:

```rb
params.require(:task).permit(:Description)
```

Módosítsa ezt a sort, hogy tartalmazzák az új `Done` paraméter.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>A nézetek frissítése

Nyissa meg a *app/views/tasks/_form.html.erb* fájlt, amely a Szerkesztés űrlap.

A sorban található `<%=f.error_span(:Description) %>` , majd szúrja be a következő kódot közvetlenül alá:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Nyissa meg a *app/views/tasks/show.html.erb* fájlt, amely az Egyrekordos nézet lap. 

A sorban található `<dd><%= @task.Description %></dd>` , majd szúrja be a következő kódot közvetlenül alá:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Nyissa meg a *app/views/tasks/index.html.erb* fájlt, amely az összes rekordokat az Index lap.

A sorban található `<th><%= model_class.human_attribute_name(:Description) %></th>` , majd szúrja be a következő kódot közvetlenül alá:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

Ugyanazt a fájlt, keresse meg a sor `<td><%= task.Description %></td>` , majd szúrja be a következő kódot közvetlenül alá:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>A módosításokat a helyi tesztelése

A helyi Terminálszolgáltatások futnak a sínek kiszolgáló.

```bash
rails server
```

Módosításához nyissa meg a feladat állapotának megtekintéséhez `http://localhost:3000` és hozzáadása vagy szerkesztése elemeket.

![A hozzáadott melletti jelölőnégyzetet, hogy a feladat](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

A sínek kiszolgáló leállításához írja be a `Ctrl + C` a terminálon.

### <a name="publish-changes-to-azure"></a>Változások közzétételére Azure

A Terminálszolgáltatások futnak sínek adatbázis áttelepítése az éles környezet végezheti el a módosítást az Azure-adatbázisban.

```bash
rake db:migrate RAILS_ENV=production
```

A Git a módosítások véglegesítéséhez, és ezután a kód változásainak leküldése Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Egyszer a `git push` befejezése, navigáljon az Azure web app és az új funkció tesztelése.

![Az Azure-bA közzétett modell és adatbázis módosítása](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

Ha olyan feladatokat, jelennek meg az adatbázisban. Az adatok séma frissítések érintetlenül meglévő adatokat.

## <a name="manage-the-azure-web-app"></a>Az Azure webalkalmazás felügyelete

A létrehozott webalkalmazás felügyeletéhez ugorjon az [Azure Portalra](https://portal.azure.com).

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webalkalmazás nevére.

![Navigálás a portálon az Azure-webapphoz](./media/tutorial-php-mysql-app/access-portal.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt például a stop, kezdő, indítsa újra, tallózással keresse meg és delete alapvető felügyeleti feladatokat hajthat végre.

A bal oldali menü lapokat biztosít az alkalmazás konfigurálását.

![Az App Service lap az Azure Portalon](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * MySQL-adatbázis létrehozása az Azure-ban
> * Csatlakozás egy Ruby sínek alkalmazásában MySQL
> * Az alkalmazás telepítése az Azure-bA
> * Az adatmodell frissítése, és telepítse újra az alkalmazást
> * Az adatfolyam diagnosztikai naplók az Azure-ból
> * Felügyelheti az alkalmazást az Azure-portálon

A következő oktatóanyag megtudhatja, hogyan képezheti egy egyéni DNS-nevet a webalkalmazás továbblépés.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](../app-service-web-tutorial-custom-domain.md)
