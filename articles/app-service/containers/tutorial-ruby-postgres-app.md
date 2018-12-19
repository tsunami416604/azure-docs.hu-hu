---
title: Postgres Ruby webalkalmazás összeállítása linuxon – az Azure App Service |} A Microsoft Docs
description: Megismerheti, hogyan tehet szert egy olyan, az Azure-ban működő Ruby-alkalmazásra, amely egy Azure-beli PostgreSQL-adatbázishoz csatlakozik.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 93cdf83797cde40d75d6f6d6220cf4e02cb56cef
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604219"
---
# <a name="build-a-ruby-and-postgres-web-app-in-azure-app-service-on-linux"></a>Ruby- és Postgres-webalkalmazás létrehozása a Linuxon futó Azure App Service-ben

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Ruby-webalkalmazást, és hogyan csatlakoztathatja azt egy PostgreSQL-adatbázishoz. Az oktatóanyag eredménye egy, a Linux App Service-ben futó [Ruby on Rails](https://rubyonrails.org/)-alkalmazás lesz.

![Az Azure App Service-ben futó Ruby on Rails-alkalmazás](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Ruby on Rails-alkalmazás csatlakoztatása a PostgreSQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* [A Git telepítése](https://git-scm.com/)
* [Telepítse a Ruby 2.3-as verzióját](https://www.ruby-lang.org/en/documentation/installation/)
* [Telepítse a Ruby on Rails 5.1-es verzióját](https://guides.rubyonrails.org/v5.1/getting_started.html)
* [A PostgreSQL telepítése és futtatása](https://www.postgresql.org/download/)

## <a name="prepare-local-postgres"></a>A helyi Postgres előkészítése

Ebben a lépésben egy adatbázist hoz létre a helyi Postgres-kiszolgálón, hogy felhasználhassa azt az oktatóanyagban.

### <a name="connect-to-local-postgres-server"></a>Csatlakozás a helyi Postgres-kiszolgálóhoz

A helyi Postgres-kiszolgálóhoz való csatlakozáshoz nyissa meg a terminálablakot, és futtassa a `psql` parancsot.

```bash
sudo -u postgres psql
```

Ha a kapcsolat létrejött, a Postgres-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadással](https://www.postgresql.org/download/) foglalkozó szakaszban ismertetett lépéseket követve indítsa el a helyi Postgres-adatbázist.

A Postgres-ügyfél bezárásához írja be a `\q` parancsot. 

A következő parancs futtatásával és bejelentkezett Linux-felhasználónevének használatával hozzon létre egy adatbázisok létrehozására képes Postgres-felhasználót.

```bash
sudo -u postgres createuser -d <signed_in_user>
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

### <a name="run-the-sample-locally"></a>A minta futtatása helyben

Futtassa [a Rails migrálásokat](https://guides.rubyonrails.org/active_record_migrations.html#running-migrations) az alkalmazás számára szükséges táblák létrehozásához. Ha látni szeretné a migrálások során keletkező táblákat, tekintse meg a _db/migrate_ könyvtárat a Git-tárházban.

```bash
rake db:create
rake db:migrate
```

Futtassa az alkalmazást.

```bash
rails server
```

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. Vegyen fel néhány feladatot az oldalon.

![A Ruby on Rails sikeresen csatlakozik a Postgreshez](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Postgres létrehozása az Azure-ban

Ebben a lépésben egy Postgres-adatbázist fog létrehozni az [Azure Database for PostgreSQL](/azure/postgresql/) rendszerében. Később konfigurálni fogja a Ruby on Rails-alkalmazást az adatbázishoz való csatlakozásra.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Postgres-kiszolgáló létrehozása

A PostgreSQL-kiszolgálót az [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) paranccsal hozhatja létre.

A Cloud Shellben futtassa a következő parancsot, és a *\<postgres_server_name>* helyőrzőt cserélje le egy egyedi kiszolgálónévre. A kiszolgálónév az összes Azure-kiszolgálón csak egyszer fordulhat elő. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres_server_name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

Az Azure Database for PostgreSQL-kiszolgáló létrehozását követően az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres_server_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres_server_name>",
  "location": "westeurope",
  "name": "<postgres_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

A Cloud Shellben az [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create) paranccsal hozzon létre egy tűzfalszabályt a Postgres-kiszolgáló számára az ügyfélkapcsolatok engedélyezésére. Ha a kezdő IP-cím és a záró IP-cím is 0.0.0.0 értékre van állítva, a tűzfal csak más Azure-erőforrások számára van nyitva. Cserélje le a *\<postgres_server_name>* helyőrzőt egy egyedi kiszolgálónévre.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres_server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> Még szigorúbb tűzfalszabályt is megadhat, ha [csak azokat a kimenő IP-címeket használja, amelyeket alkalmazása használ](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Helyi csatlakozás éles Postgres-kiszolgálóhoz

A Cloud Shellben csatlakozzon az Azure-beli Postgres-kiszolgálóhoz. A _&lt;postgres_server_name>_ helyőrzőknél használja a korábban megadott értéket.

```bash
psql -U adminuser@<postgres_server_name> -h <postgres_server_name>.postgres.database.azure.com postgres
```

Amikor a rendszer jelszót kér, használja a _My5up3r$tr0ngPa$w0rd!_ jelszót, amelyet az adatbázis-kiszolgáló létrehozásakor adott meg.

### <a name="create-a-production-database"></a>Éles adatbázis létrehozása

A `postgres` parancssorban hozzon létre egy adatbázist.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Engedélyekkel rendelkező felhasználó létrehozása

Hozzon létre egy _railsappuser_ nevű adatbázis-felhasználót, és adja meg neki az összes jogosultságot a `sampledb` adatbázisban.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017'; 
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Bontsa a kiszolgálókapcsolat a `\q` parancs beírásával.

## <a name="connect-app-to-azure-postgres"></a>Az alkalmazás csatlakoztatása az Azure Postgreshez

Ebben a lépésben csatlakoztatni fogja a Ruby on Rails-alkalmazást a Postgres-adatbázishoz, amelyet az Azure Database for PostgreSQL szolgáltatásban hozott létre.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Az adatbázis-kapcsolat konfigurálása

A tárházban nyissa meg a _config/database.yml_ fájlt. A fájl végén cserélje le a termelési változókat az alábbi kódra. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Mentse a módosításokat.

### <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

A helyi terminálban állítsa be a következő környezeti változókat:

```bash
export DB_HOST=<postgres_server_name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres_server_name>
export DB_PASSWORD=MyPostgresAzure2017
```

Futtasson Rails-adatbázis migrálásokat az imént beállított termelési értékekkel a táblák létrehozásához az Azure Database for PostgreSQL szolgáltatás Postgres-adatbázisában. 

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

Nyissa meg a `http://localhost:3000` címet. Ha az oldal hiba nélkül betölt, a Ruby on Rails-alkalmazás csatlakozik az Azure-ban található Postgres-adatbázishoz.

Vegyen fel néhány feladatot az oldalon.

![A Ruby on Rails sikeresen csatlakozik az Azure Database for PostgreSQL-hez](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

A Rails-kiszolgáló leállításához írja be a `Ctrl + C` billentyűparancsot a terminálon.

### <a name="commit-your-changes"></a>A módosítások véglegesítése

A módosítások véglegesítéséhez futtassa az alábbi Git-parancsokat:

```bash
git add .
git commit -m "database.yml updates"
```

Az alkalmazás készen áll az üzembe helyezésre.

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a Postgreshez csatlakoztatott Rails-alkalmazást az Azure App Service-ben.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az App Service-ben a környezeti változókat _alkalmazásbeállításként_ lehet beállítani az [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs Cloud Shellben való használatával.

Az alábbi Cloud Shell-parancs a `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` és `DB_PASSWORD` alkalmazásbeállításokat konfigurálja. Cserélje le az _&lt;appname>_ és a _&lt;postgres_server_name>_ helyőrzőt.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<postgres_server_name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres_server_name>" DB_PASSWORD="MyPostgresAzure2017"
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

![Az Azure App Service-ben futó Ruby on Rails-alkalmazás](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Gratulálunk, egy adatvezérelt Ruby on Rails-alkalmazást futtat az Azure App Service-ben.

## <a name="update-model-locally-and-redeploy"></a>A modell helyileg történő frissítése és ismételt üzembe helyezése

Ebben a lépésben egy egyszerű módosítást fog elvégezni a `task` adatmodellben és a webalkalmazásban, majd közzéteszi a frissítést az Azure-ban.

A feladatokkal kapcsolatos forgatókönyvben úgy módosítja az alkalmazást, hogy befejezettként jelölhessen meg feladatokat.

### <a name="add-a-column"></a>Oszlop hozzáadása

Lépjen a Git-adattár gyökérkönyvtárába a terminálon.

Hozzon létre egy új migrálást, amely egy `Done` nevű logikai oszlopot ad hozzá a `Tasks` táblához:

```bash
rails generate migration AddDoneToTasks Done:boolean
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

![A feladathoz hozzáadott jelölőnégyzet](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

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

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

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
> * Postgres-adatbázis létrehozása az Azure-ban
> * Ruby on Rails-alkalmazás csatlakoztatása a Postgreshez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazásokhoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése az Azure App Service-ben](../app-service-web-tutorial-custom-domain.md)
